# Binary Tree Visualizer

A small React + Vite app for building a binary tree by hand and watching the four standard traversals run on it one node at a time. You start with a root node, add children with "+" buttons, optionally change a node's value, then pick a traversal. The app highlights each node in visit order and builds up the resulting sequence underneath the tree.

Live demo: https://binary-tree-visualizer-azure.vercel.app

## Features

- Start from a single root node with value `0`.
- Add a left or right child to any node that has an empty slot, using the "+" buttons. New nodes get auto-incrementing values (1, 2, 3, ...).
- Click a node to select it and edit its value in a small popup (Set Value / Cancel).
- Four traversals: inorder, preorder, postorder and level order (breadth-first).
- Step-by-step animation: one node is highlighted per second, and its value is added to the output array shown below the tree.
- Pause/Resume and Restart controls while a traversal is running.

This is a general binary tree, not a binary search tree. You choose where each node goes, and there is no ordering rule.

## Tech stack

- React 18
- Vite 5
- Plain CSS (`BinaryTree.css`, `App.css`, `index.css`)
- ESLint 9

## How it works

- `src/BinaryTree.js` holds the data structure: a `TreeNode` class (`value`, `left`, `right`, `selected`) and a `BinaryTree` class with `insertLeft`, `insertRight`, recursive `inorderTraversal` / `preorderTraversal` / `postorderTraversal`, a queue-based `levelOrderTraversal`, and `findNodeByValue`, a depth-first search by value.
- `src/BinaryTreeComponent.jsx` is the controller. It keeps one `BinaryTree` instance in state along with the UI state (selected node, traversal type, run/pause flags, current step).
  - On "Start Traversal" it calls the chosen traversal method and stores the resulting array of values.
  - A `useEffect` moves through that array. At each step it finds the node with `findNodeByValue`, highlights it, adds the value to the output array, and schedules the next step with a 1000 ms `setTimeout`. Pausing stops the effect from scheduling the next step.
- `src/TreeNodeComponent.jsx` draws the tree recursively. Each node renders its value and then either a child `TreeNodeComponent` or a "+" button for each side. It applies the `selected` and `highlighted` CSS classes.
- `src/App.jsx` and `src/main.jsx` are the standard Vite entry points.

## Running locally

Requires Node.js and npm.

```bash
npm install
npm run dev       # start the Vite dev server
npm run build     # production build to dist/
npm run preview   # serve the production build locally
npm run lint      # run ESLint
```

No environment variables are needed.

## Known limitations / next steps

- There are no automated tests. The traversal methods in `BinaryTree.js` would be easy to unit test.
- You cannot delete nodes or reset the tree without reloading the page.
- The tree is changed in place (`node.value = ...`, `node.left = ...`), and re-renders happen only because an unrelated piece of state changes. Moving to immutable updates, or a reducer, would make this more reliable.
- Highlighting looks nodes up by value (`findNodeByValue`). If two nodes share a value, the first match is highlighted instead of the node actually being visited. Traversals should return node references instead.
- Edited values are stored as strings from the text input, while generated values are numbers. Values are not validated.
- There is no speed control. The step delay is fixed at 1 second.
- The layout is plain nested flexbox with no connecting lines between nodes, so wide or deep trees get cramped.
- `src/ArrayDisplay.jsx` and `ArrayDisplay.css` are not used anywhere.
