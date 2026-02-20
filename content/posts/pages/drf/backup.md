# ViewSet in Django REST Framework

---

## 1. Foundation of ViewSet

### 1.1 What is a ViewSet

A ViewSet groups related views (list, create, retrieve, update, delete) into a **single class** instead of separate APIView classes.

- Focuses on **actions** rather than HTTP methods.
- Example: Instead of `UserListAPIView` and `UserDetailAPIView`, a single `UserViewSet` handles all CRUD actions.

### 1.2 Why ViewSet Exists

- Reduce boilerplate code.
- Standardize CRUD operations.
- Work seamlessly with DRF **routers** for automatic URL generation.

### 1.3 Problem It Solves Compared to APIView

| Feature        | APIView                        | ViewSet                       |
|----------------|--------------------------------|-------------------------------|
| Method mapping | HTTP methods (`get`, `post`)   | Actions (`list`, `create`)    |
| URL handling   | Manual URL definition          | Automatic via routers         |
| Boilerplate    | High                           | Low                           |
| Best for       | Highly customized endpoints    | CRUD-heavy resources          |

### 1.4 RESTful Action Mapping

| Action           | HTTP Method | URL Pattern         |
|------------------|-------------|---------------------|
| `list`           | GET         | `/objects/`          |
| `create`         | POST        | `/objects/`          |
| `retrieve`       | GET         | `/objects/{id}/`     |
| `update`         | PUT         | `/objects/{id}/`     |
| `partial_update` | PATCH       | `/objects/{id}/`     |
| `destroy`        | DELETE      | `/objects/{id}/`     |

Behind the scenes: DRF uses `as_view()` + `action_map` to route HTTP methods to these actions.

---

## 2. ModelViewSet Deep Dive

ModelViewSet is the most powerful ViewSet — provides **full CRUD** automatically.

### 2.1 Required Attributes

```python
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()              # Required
    serializer_class = BookSerializer          # Required
```

Optional: `permission_classes`, `authentication_classes`, `lookup_field`

### 2.2 Router Connection

```python
from rest_framework.routers import DefaultRouter
from .views import BookModelViewSet
from django.urls import path, include

router = DefaultRouter()
router.register(r'books', BookModelViewSet)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api-auth/', include('rest_framework.urls')),
]

urlpatterns += router.urls
```

Automatically generates all CRUD URLs — no manual `path()` needed.

---

## 3. Action System

### 3.1 Standard Actions

```python
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

- `GET /books/` → `list`
- `POST /books/` → `create`
- `GET /books/1/` → `retrieve`
- `PUT /books/1/` → `update`
- `PATCH /books/1/` → `partial_update`
- `DELETE /books/1/` → `destroy`

### 3.2 The `self.action` Attribute

DRF sets `self.action` to the current action name. Useful for conditional logic:

```python
def get_serializer_class(self):
    if self.action == 'list':
        return BookListSerializer
    return BookDetailSerializer
```

### 3.3 Overriding Standard Actions

```python
def create(self, request, *args, **kwargs):
    print("Creating a new book!")
    return super().create(request, *args, **kwargs)
```

> Always call `super()` if you want DRF to handle default behavior.

### 3.4 Custom Actions with `@action`

For operations beyond standard CRUD:

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

| Parameter       | Meaning                          | Example URL                |
|-----------------|----------------------------------|----------------------------|
| `detail=True`   | Action on a **single object**    | `/books/1/publish/`        |
| `detail=False`  | Action on the **whole collection** | `/books/recent-books/`   |

#### `methods` Argument

```python
@action(detail=True, methods=['post'])
def mark_as_favorite(self, request, pk=None):
    book = self.get_object()  # Only available when detail=True
    book.favorite = True
    book.save()
    return Response({"status": "marked as favorite"})
```

#### Custom URL Path

```python
@action(detail=False, url_path='recent-books')
def recent_books(self, request):
    ...
```

URL becomes: `/books/recent-books/`  
URL name is auto-generated as `<basename>-<action>`.

---

## 4. Important Overridable Hooks

Hooks let you customize behavior **without rewriting entire actions**.

### 4.1 `get_queryset()`

Filter objects dynamically based on request/user:

```python
def get_queryset(self):
    return Book.objects.filter(owner=self.request.user)
```

### 4.2 `get_serializer_class()`

Use different serializers per action:

```python
def get_serializer_class(self):
    if self.action == 'list':
        return BookListSerializer
    return BookDetailSerializer
```

### 4.3 `get_object()`

Custom lookup logic for detail views (`retrieve`, `update`, `destroy`):

```python
def get_object(self):
    obj = super().get_object()
    if obj.owner != self.request.user:
        raise PermissionDenied("You cannot access this object")
    return obj
```

### 4.4 `perform_create(serializer)`

Inject extra data when saving:

```python
def perform_create(self, serializer):
    serializer.save(owner=self.request.user)
```

---

## 5. Authentication, Permissions & Lookup Field

### 5.1 `authentication_classes`

Define **how** requests are authenticated:

```python
from rest_framework.authentication import TokenAuthentication

class BookViewSet(viewsets.ModelViewSet):
    authentication_classes = [TokenAuthentication]
```

### 5.2 `permission_classes`

Define **who** can access the ViewSet:

```python
from rest_framework.permissions import IsAuthenticated

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    permission_classes = [IsAuthenticated]
```

Common classes: `AllowAny`, `IsAuthenticated`, `IsAdminUser`, `IsAuthenticatedOrReadOnly`

### 5.3 `get_permissions()` — Dynamic Permissions Per Action

```python
def get_permissions(self):
    if self.action in ['destroy', 'update', 'partial_update']:
        permission_classes = [IsAdminUser]
    else:
        permission_classes = [IsAuthenticated]
    return [p() for p in permission_classes]
```

### 5.4 Object-Level Permissions

Check access to **specific objects** with a custom permission class:

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

### 5.5 `lookup_field`

Change the default lookup from `pk` to another field:

```python
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    lookup_field = 'slug'  # URL: /books/<slug>/
```

---

## 6. When NOT to Use ViewSet

| Scenario                          | Why Not                                              | Use Instead        |
|-----------------------------------|------------------------------------------------------|--------------------|
| Highly customized endpoints       | You'll override most actions, defeating the purpose  | `APIView`          |
| Non-CRUD APIs (`/send-email/`)    | A single APIView is simpler and clearer              | `APIView` / FBV    |
| Performance-critical APIs         | Extra abstraction layers add overhead                | `APIView` / FBV    |