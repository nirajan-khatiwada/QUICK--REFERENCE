---
title: "Exit Animations & AnimatePresence in Framer Motion"
slug: "exit-animations-animate-presence"
date: 2025-10-16
description: "Learn how to animate components when they unmount from the DOM using AnimatePresence and exit props in Framer Motion."
showToc: true
weight: 5
series: ["Framer Motion"]
categories: ["Framer Motion", "React"]
tags: ["Framer Motion", "React", "Animation", "Exit Animations", "AnimatePresence", "Routing"]
summary: "Unlock the ability to animate components *out* of the DOM with `AnimatePresence`, essential for modals and page transitions."
images: ["/images/framer-motion.png"]
---

Upto now, we have only see the animation when a component is mounted or based on use interaction also we have seen how to animation based on user interaction.Now we will see how to animate a component when it is unmounted from the react tree.


## Exit Animation
Normally its is not possible to animate a component when it is unmounted ie removed from the dom tree because when a component is unmounted it is removed from the dom tree and hence we cannot animate it.But with framer motion we can easily achieve this by using the `AnimatePresence` component.

AnimatePresence is a component provided by framer motion which only unmounts components remove element from from dom when all exit animations are complete such that we can animate a component using the exit prop when it is removed from the react tree.

How it works?
When a component is removed from the react tree it is not immediately removed from the dom tree instead it is kept in the dom tree until all exit animations are complete and then it is removed from the dom tree.

> Note: exit pop is only used to define the properties to be animated when the component is unmounted and it works only when the component is wrapped inside the AnimatePresence component.



### Example

```jsx
import React from "react";
import { motion, AnimatePresence } from "framer-motion";
const App = () => {
  const [button, showButton] = React.useState(true);
  return (
    <div>
      <AnimatePresence>
        {button && (
          <motion.div
            className="w-2/5 mx-auto mt-20 p-10 border border-gray-300 rounded-lg shadow-lg"
            initial={{}}
            exit={{
              opacity: 0,

              y: 100,
            }}
          >
            <p>THis is a card</p>
          </motion.div>
        )}
      </AnimatePresence>

      <button
        className="bg-blue-500 text-white px-4 w-2/5 py-2 rounded-lg mt-10 mx-auto block"
        onClick={() => showButton(!button)}
      >
        Show Card
      </button>
    </div>
  );
};

export default App;

```


## Setup Entry Animation
To setup entry animation we can use the `initial` prop of the motion component to define the initial state of the component when it is mounted and then we can use the `animate` prop to define the final state of the component when it is mounted as we have seen in the previous examples.


for example :
```jsx
import React from "react";
import { motion, AnimatePresence } from "framer-motion";
const App = () => {
  const [button, showButton] = React.useState(true);
  return (
    <div>
      <AnimatePresence>
        {button && (
          <motion.div
            className="w-2/5 mx-auto mt-20 p-10 border border-gray-300 rounded-lg shadow-lg"
            initial={{
              opacity:0,
              scale:0.5,
              y:100
            }}

            animate = {
              {
                opacity:1,
                scale:1,
                y:0,
              }
            }
            exit={{
              opacity: 0,
              scale: 0.5,
              y: 100,
            }}
          >
            <p>THis is a card</p>
          </motion.div>
        )}
      </AnimatePresence>

      <button
        className="bg-blue-500 text-white px-4 w-2/5 py-2 rounded-lg mt-10 mx-auto block"
        onClick={() => showButton(!button)}
      >
        Show Card
      </button>
    </div>
  );
};

export default App;
```

In the above example we have defined the initial state of the component when it is mounted using the `initial` prop and then we have defined the final state of the component when it is mounted using the `animate` prop and then we have defined the exit animation using the `exit` prop.




## `initial={false}`
By default, when a page reloads, a component will animate from its initial state to its final state as defined by the initial and animate props.

However, if you want the animation to occur only when the component is mounted due to conditional rendering—and not when the page reloads—you can set the initial prop of the AnimatePresence component to false.




> Transtion will apply to both animate and exit animation and interactive elements like whileHover, whileTap etc.




## Some important thing to consider in Framer motion.
1. animate,initial and exit props dont work with inline components like span,strong etc. They work with block elements like div,section,article etc. so if you are working with inline elements then make sure to change their display property to block or inline-block.
2. Make sure initial -> animate occurs only when the component is mounted and use animation if you want to animate the component when it is already mounted.