---
title: Core Concepts
sidebar_position: 3
---

In the following part we will introduce you to the core concepts of React Flow and explain how to create an interactive flow. A flow consists of nodes and edges (or just nodes). You can pass arrays of `nodes` and `edges` as props to the ReactFlow component. Hereby all node and edge ids need to be unique. A node needs a position and a label (this could be different if you are using [custom nodes](/docs/guides/custom-nodes)) and an edge needs a source (node id) and a target (node id). You can read more about the options in the [Node options](/docs/api/nodes/node-options) and [Edge options](/docs/api/edges/edge-options) sections.

### Controlled or Uncontrolled

With React Flow you have two ways to setup a flow. You can either create a controlled or an [uncontrolled one](/docs/guides/uncontrolled-flow).
We recommend to use a controlled one but for simpler use cases you can also setup an uncontrolled flow. **In the following part we will setup a controlled flow.** Let's start by adding some nodes and edges to the ReactFlow component:

:::caution

The dimensions of your React Flow component depend on the parent dimensions. That means that the parent needs a width and height to render React Flow properly.

:::

import CodeViewer from '/src/components/CodeViewer';
const editorOptions = { editorHeight: 500, editorWidthPercentage: 45, wrapContent: true }

<CodeViewer codePath="api-flows/IntroductionFlow" applyStyles={false} options={editorOptions} />

## Basic Functionality

By default React Flow doesn't do any internal state updates besides handling the viewport when you setup a controlled flow. As with an `<input />` component you need to pass handlers to apply the changes that are triggered by React Flow to your nodes and egdes. In order to **select**, **drag** and **remove** nodes and edges you need to implement an `onNodesChange` and an `onEdgesChange` handler:

<CodeViewer codePath="api-flows/IntroductionFlow2" applyStyles={false} options={editorOptions} additionalFiles={['nodes.js', 'edges.js']} />

What is happening here? Whenever React Flow triggers a change (node drag, edge select, etc.), the `onNodesChange` handler gets called. We export an `applyNodeChanges` handler so that you don't need to handle the changes by yourself. The `applyNodeChanges` handler returns an updated array of nodes that is your new nodes array. You now have an interactive flow with the following kinds of interactions:

- selectable nodes and edges
- draggable nodes
- removable nodes and edges - (press Backspace to remove a selected node or edge, can be adjusted with the `deleteKeyCode` prop)
- multi-selection area by pressing Shift (that's the default `selectionKeyCode`)
- multi-selection by pressing command (that's the default `multiSelectionKeyCode`)

For convenience we export the helper hooks `useNodesState` and `useEdgesState` that you can use to create the nodes and edges state more easily:

```js
const [nodes, setNodes, onNodesChange] = useNodesState(initialNodes);
const [edges, setEdges, onEdgesChange] = useEdgesState(initialEdges);
```

## Connecting Nodes

The last piece that is missing to get the full interactivity is the `onConnect` handler. You need to implement it, in order to handle new connections.

<CodeViewer codePath="api-flows/IntroductionFlow3" applyStyles={false} options={editorOptions} additionalFiles={['nodes.js', 'edges.js']} />

In this example we are using the `addEdge` handler that returns an array of edges with the newly created one. If you want set a certain edge option whenever an edge gets created you pass your options like this:

```js
const onConnect = useCallback(
  (connection) => setEdges((eds) => addEdge({ ...connection, animated: true }, eds)),
  [setEdges]
);
```

or use the `defaultEdgeOptions` prop:

```jsx
const defaultEdgeOptions = { animated: true };
...
<ReactFlow
  nodes={nodes}
  edges={edges}
  onNodesChange={onNodesChange}
  onEdgesChange={onEdgesChange}
  onConnect={onConnect}
  defaultEdgeOptions={defaultEdgeOptions}
/>;
```
