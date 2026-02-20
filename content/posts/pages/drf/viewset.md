---
title: "DRF ViewSets & ModelViewSet - Complete Guide"
slug: "drf-viewset"
date: 2025-04-05
description: "Master DRF ViewSets — from basic concepts to ModelViewSet, actions, hooks, permissions, authentication, and custom actions."
showToc: true
weight: 7
series: ["Django REST Framework"]
categories: ["Django REST Framework"]
tags: ["Django", "DRF", "ViewSet", "ModelViewSet", "Router", "CRUD", "Permissions", "Authentication"]
summary: "Complete reference for ViewSets in Django REST Framework — ModelViewSet, action system, overridable hooks, permissions, authentication, and custom actions."
images: ["/images/drf.png"]
---


# DRF ViewSets — Complete Guide

---

## 1. Foundation of ViewSet

### 1.1 What is a ViewSet?

A **ViewSet** is a class that groups related views (`list`, `create`, `retrieve`, `update`, `delete`) into a **single class** instead of writing separate `APIView` classes for each endpoint.

- Focuses on **actions** rather than HTTP methods.
- Instead of writing `UserListAPIView` and `UserDetailAPIView`, a single `UserViewSet` handles all CRUD actions.

### 1.2 Why ViewSet Exists

- **Reduce boilerplate** — one class instead of many.
- **Standardize CRUD** operations across the project.
- **Seamless router integration** — automatic URL generation.

### 1.3 Problem It Solves (vs APIView)

With `APIView`, you write separate methods (`get`, `post`, `put`, `delete`) for each endpoint **manually**.

`ViewSet` abstracts this — you define **actions**, and DRF automatically maps HTTP methods to the correct function.

### 1.4 APIView vs ViewSet — Quick Comparison

| Feature         | APIView                     | ViewSet                     |
|-----------------|-----------------------------|-----------------------------|
| Method mapping  | HTTP methods (`get`, `post`) | Actions (`list`, `create`)  |
| URL handling    | Manual `path()` definition  | Automatic via routers       |
| Boilerplate     | High                        | Low                         |
| Best for        | Highly customized endpoints | CRUD-heavy resources        |

---

## 2. RESTful Actions & HTTP Method Mapping

ViewSets follow **REST conventions**. Here's the complete mapping:

| Action           | HTTP Method | URL Pattern         | Description                      |
|------------------|-------------|---------------------|----------------------------------|
| `list`           | `GET`       | `/books/`           | Returns a list of all objects    |
| `retrieve`       | `GET`       | `/books/<id>/`      | Returns a single object by PK   |
| `create`         | `POST`      | `/books/`           | Creates a new object             |
| `update`         | `PUT`       | `/books/<id>/`      | Full update of an object         |
| `partial_update` | `PATCH`     | `/books/<id>/`      | Partial update of an object      |
| `destroy`        | `DELETE`    | `/books/<id>/`      | Deletes an object                |

**Behind the scenes:** DRF uses the `as_view()` method + `action_map` to route HTTP methods to these actions.

---

## 3. ModelViewSet Deep Dive

`ModelViewSet` is the **most powerful** ViewSet — it provides **all 6 CRUD actions** automatically.

### 3.1 Required Attributes

You **must** define at least these two:

```python
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()              # Base queryset for the model
    serializer_class = BookSerializer          # Serializer for JSON conversion
```

**Optional attributes** (covered in detail below):

| Attribute               | Purpose                                           |
|-------------------------|---------------------------------------------------|
| `permission_classes`    | Who can access (→ `get_permissions()`)            |
| `authentication_classes`| How requests are authenticated (→ `get_authenticators()`) |
| `lookup_field`          | Field used for object lookup (default: `pk`)      |

### 3.2 How Router Connects to ModelViewSet

DRF **routers automatically generate URLs** from your ViewSet actions. No manual `path()` needed.

```python
from rest_framework.routers import DefaultRouter
from .views import BookViewSet
from django.urls import path, include

router = DefaultRouter()
router.register(r'books', BookViewSet)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api-auth/', include('rest_framework.urls')),
]

# Append all auto-generated CRUD URLs
urlpatterns += router.urls
```

This **automatically creates** all 6 routes for `BookViewSet` — no manual URL wiring.

---

## 4. Action System

### 4.1 `self.action` Attribute

Inside any ViewSet method, `self.action` tells you **which action is currently executing** (`'list'`, `'create'`, `'retrieve'`, etc.).

Use it for **conditional logic** inside hooks:

```python
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()

    def get_serializer_class(self):
        if self.action == 'list':
            return BookListSerializer       # Lightweight serializer for lists
        return BookDetailSerializer          # Full serializer for detail views
```

### 4.2 Overriding Standard Actions

Override any action to inject **custom behavior**:

```python
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def create(self, request, *args, **kwargs):
        print("Creating a new book!")          # Custom logic before save
        return super().create(request, *args, **kwargs)  # Let DRF handle the rest
```

> **Tip:** Always call `super()` if you want DRF's default behavior to still run.

### 4.3 Custom Actions — `@action` Decorator

When standard CRUD isn't enough, create **custom endpoints** with `@action`:

```python
from rest_framework.decorators import action
from rest_framework.response import Response

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    @action(detail=False, methods=['get'])
    def recent_books(self, request):
        recent = Book.objects.order_by('-created_at')[:5]
        serializer = self.get_serializer(recent, many=True)
        return Response(serializer.data)
```

#### `detail=True` vs `detail=False`

| Parameter        | Meaning                          | Example URL                 |
|------------------|----------------------------------|-----------------------------|
| `detail=True`    | Action on a **single object**    | `/books/1/publish/`         |
| `detail=False`   | Action on the **whole collection** | `/books/recent_books/`    |

#### `methods` Argument

Specify which HTTP methods are allowed (default: `GET`):

```python
@action(detail=True, methods=['post'])
def mark_as_favorite(self, request, pk=None):
    book = self.get_object()              # Only available when detail=True
    book.favorite = True
    book.save()
    return Response({"status": "marked as favorite"})
```

#### Customizing the URL Path

By default, the URL uses the method name. Override with `url_path`:

```python
@action(detail=False, url_path='recent-books')
def recent_books(self, request):
    ...
# URL becomes: /books/recent-books/
```

**URL naming convention:** `<basename>-<action_name>` (e.g., `book-recent_books`).

---

## 5. Overridable Hooks

Hooks are methods called **automatically** during request handling. Override them to customize behavior **without rewriting entire actions**.

### 5.1 `queryset` → `get_queryset()`

**Static:** Set `queryset` as a class attribute.
**Dynamic:** Override `get_queryset()` to filter based on the current request.

```python
# Static (same queryset for every request)
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

```python
# Dynamic (filter per user)
class BookViewSet(viewsets.ModelViewSet):
    serializer_class = BookSerializer

    def get_queryset(self):
        return Book.objects.filter(owner=self.request.user)
```

**Flow:** DRF calls `get_queryset()` internally → if not overridden, it returns `self.queryset`.

### 5.2 `serializer_class` → `get_serializer_class()`

**Static:** Set `serializer_class` for all actions.
**Dynamic:** Override `get_serializer_class()` to return different serializers per action.

```python
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()

    def get_serializer_class(self):
        if self.action == 'list':
            return BookListSerializer       # Lightweight for listing
        if self.action == 'create':
            return BookCreateSerializer     # Different fields for creation
        return BookDetailSerializer          # Full detail for retrieve/update
```

**Flow:** DRF calls `get_serializer_class()` → if not overridden, it returns `self.serializer_class`.

### 5.3 `lookup_field` → `get_object()`

**Static:** Set `lookup_field` to change the default lookup from `pk` to another field.

```python
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    lookup_field = 'slug'       # Now uses /books/<slug>/ instead of /books/<pk>/
```

**Dynamic:** Override `get_object()` for custom lookup logic or extra checks.

```python
def get_object(self):
    obj = super().get_object()
    if obj.owner != self.request.user:
        raise PermissionDenied("You cannot access this object")
    return obj
```

**Flow:** DRF calls `get_object()` → uses `lookup_field` to filter queryset → returns single instance.

### 5.4 `perform_create()`, `perform_update()`, `perform_destroy()`

These hooks run **right when the object is saved/deleted**. Override them to inject extra data or side effects.

```python
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def perform_create(self, serializer):
        serializer.save(owner=self.request.user)   # Auto-set owner on create

    def perform_update(self, serializer):
        serializer.save(updated_by=self.request.user)

    def perform_destroy(self, instance):
        instance.is_active = False                  # Soft delete instead
        instance.save()
```

**Flow:** `create()` action → validates serializer → calls `perform_create(serializer)` → `serializer.save()`.

---

## 6. Permissions & Authentication

### 6.1 `permission_classes` → `get_permissions()`

**Static:** Set `permission_classes` as a class attribute — **same permissions for all actions**.

```python
from rest_framework.permissions import IsAuthenticated, IsAdminUser

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [IsAuthenticated]      # All actions require login
```

**Common built-in permission classes:**

| Class                       | Who can access                        |
|-----------------------------|---------------------------------------|
| `AllowAny`                  | Everyone (no restriction)             |
| `IsAuthenticated`           | Only logged-in users                  |
| `IsAdminUser`               | Only staff/admin users                |
| `IsAuthenticatedOrReadOnly` | Logged-in for writes, anyone for reads|

**Dynamic:** Override `get_permissions()` to apply **different permissions per action**.

```python
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get_permissions(self):
        if self.action in ['destroy', 'update', 'partial_update']:
            return [IsAdminUser()]              # Only admin can modify/delete
        return [IsAuthenticated()]               # Others just need login
```

**Flow:** DRF calls `get_permissions()` → if not overridden, it instantiates `self.permission_classes`.

### 6.2 Object-Level Permissions

For checking access to a **specific object** (not just the endpoint), create a custom permission class:

```python
from rest_framework.permissions import BasePermission

class IsOwner(BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.owner == request.user

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [IsOwner]
```

**Flow:** DRF calls `has_permission()` first (endpoint-level) → then `has_object_permission()` on `get_object()` calls (object-level).

### 6.3 `authentication_classes` → `get_authenticators()`

**Static:** Set `authentication_classes` as a class attribute.

```python
from rest_framework.authentication import TokenAuthentication, SessionAuthentication

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    authentication_classes = [TokenAuthentication]     # Token-based auth
```

**Dynamic:** Override `get_authenticators()` to change authentication per action.

```python
def get_authenticators(self):
    if self.action == 'list':
        return []                                # No auth needed for listing
    return [TokenAuthentication()]
```

**Flow:** DRF calls `get_authenticators()` → if not overridden, it instantiates `self.authentication_classes`.

**Common authentication classes:**

| Class                     | How it works                              |
|---------------------------|-------------------------------------------|
| `SessionAuthentication`   | Uses Django session cookies               |
| `TokenAuthentication`     | Uses `Authorization: Token <key>` header  |
| `BasicAuthentication`     | Uses HTTP Basic Auth (username:password)  |
| `JWTAuthentication`       | Uses JWT tokens (via `djangorestframework-simplejwt`) |

---

## 7. When NOT to Use ViewSet

### 7.1 Highly Customized Endpoints

If your endpoints don't follow standard CRUD (e.g., reporting, multi-model aggregation, multi-step workflows), you'll end up overriding most actions — defeating the purpose.

**Use `APIView` instead.**

### 7.2 Non-CRUD APIs

Single-purpose endpoints like `/send-email/` or `/generate-report/` that don't manipulate models.

**A single `APIView` is simpler and clearer.**

### 7.3 Performance-Critical APIs

ViewSets add abstraction layers (routers, action mapping, hooks). For APIs where every millisecond counts, lightweight `APIView` or function-based views reduce overhead.
