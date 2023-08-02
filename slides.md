---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: /bg.jpg
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: true

# persist drawings in exports and build
drawings:
  persist: false
# use UnoCSS (experimental)
css: unocss
---

# 组件库分享

---

# 目录

&nbsp;

- **如何优雅的组织样式**
- css-render，另一种 CSS-in-JS 的方式
- 树形数据使用场景
- 如何获取树形数据
- 涉及的特性
- 特性的常见实现
- 特性的统一实现
-  一点性能优化

---

|  | 样式和逻辑分离 | 样式和逻辑结合 | 样式和逻辑关联 |
| --- | --- | --- | --- |
| 开发打包流程 | 中等 | 简单 | 复杂 |
| 输出文件 | JS 文件和 CSS 文件 | JS 文件 | JS 文件和 CSS 文件 |
| 使用方法 | 分别引入 JS 和 CSS | 只引入 JS | 只引入 JS |
| 按需加载 | 需要额外支持 | 支持 | 支持 |
| 性能影响 | 无 | 带额外 runtime，可能有影响 | 无 |
| SSR | 支持 | 需要额外支持（部分方案不支持） | 支持（可能需要使用者调整配置） |
| 支持写法 | 常规 CSS / 零运行时 CSS in JS | 常规 CSS / CSS in JS | 常规 CSS / 零运行时 CSS in JS |
| 关键样式提取 | 自行处理 | 支持 | 自行处理 |

---

# 样式和逻辑分离

这种方案中,组件的CSS和JS在代码层面上是分离的,开发时写在不同的文件里。在打包时生成独立的逻辑文件和样式文件。

优点:
- 适用面广,可以支持不同的框架和技术栈。
- 支持SSR,样式处理留给使用者。
- 可以直接提供源码,便于主题定制。

缺点:  
- 使用时需要分别引入逻辑和样式,按需加载实现复杂，需要借助[`babel-plugin-import`](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fumijs%2Fbabel-plugin-import "https://github.com/umijs/babel-plugin-import")、[`unplugin-vue-components`](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fantfu%2Funplugin-vue-components "https://github.com/antfu/unplugin-vue-components")等。
- 样式文件打包可能存在冗余。

适合需要高适用性和灵活性的组件库。

---

# 样式和逻辑结合

这种方案将CSS和JS打包在一起,输出单一的JS文件。主要有两种实现形式:

1. CSS in JS:样式以对象或字符串形式存在在JS中。
2. 将CSS打包进JS:通过构建工具,将CSS文件内容注入到JS中。

优点:
- 使用简单,只需要引入JS即可。
- 天然支持按需加载。

缺点:
- 需要额外的runtime,可能影响性能。
- 难以利用浏览器缓存。
- SSR需要框架额外支持。


---

# 样式和逻辑关联

这种方案下,虽然CSS和JS在源码层分离,但组件内会直接引用样式,且输出文件中保留import语句。

优点:
- 使用简单,只引入JS即可。
- 支持按需加载。

缺点:  
- 对构建和SSR都有一定要求。
- 样式编译复杂。

---


# 目录

&nbsp;

- 如何优雅的组织样式
- **css-render，另一种 CSS-in-JS 的方式**
- 树形数据使用场景
- 如何获取树形数据
- 涉及的特性
- 特性的常见实现
- 特性的统一实现
-  一点性能优化

---

# 愿景

- 需要能在浏览器端（动态，可接受用户输入）生成 CSS 并挂载。
- 自身需要够小，不能节省的体积比引入的体积还要大。
- 尽量拥有较高的表达能力，能让一部分 Sass 代码以比较低的成本迁入。
- 能在 Node.js 端渲染

---

<img src="/ant.png" style="position: absolute; left: 0; right: 0; top: 0; bottom: 0;width:100%;height:100%;"/>

---


<img src="/naive.png" style="position: absolute; left: 0; right: 0; top: 0; bottom: 0;width:100%;height:100%;"/>


---

# styled-components

```jsx
import styled from "styled-components";

// 声明一个styled-components组件
const Button = styled.button<{ primary?: boolean }>`
  background: ${(props) => (props.primary ? "palevioletred" : "white")};
  color: ${(props) => (props.primary ? "white" : "palevioletred")};

  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

export default function App() {
  return (
    <>
      <Button>按钮</Button>
      <Button primary>按钮</Button>
    </>
  );
}
```

---

```js

// 最简单的例子
import CSSRender from 'css-render'

const {
  c
} = CSSRender()

const style = c('body', {
  margin: 0,
  backgroundColor: 'white'
}, [
  c('&.dark', {
    backgroundColor: 'black'
  }),
  c('.container', {
    width: '100%'
  })
])

/** 作为字符串使用 */
console.log(style.render())
/**
 * 或者挂载于 document.head，
 * 下面两行只在浏览器工作，不要在 node.js 中调用
 */
style.mount()
// ...
style.unmount()

```
---


```css
body {
  margin: 0;
  background-color: white;
}

body.dark {
  background-color: black;
}

body .container {
  width: 100%;
}
```


---


# 目录

&nbsp;

- 如何优雅的组织样式
- css-render，另一种 CSS-in-JS 的方式
- **树形数据使用场景**
- 如何获取树形数据
- 涉及的特性
- 特性的常见实现
- 特性的统一实现
-  一点性能优化

---

# 组件库中的树形结构使用场景

&nbsp;

**哪里会用到?**

<v-click>

- Tree 组件
- Menu 组件

</v-click>

<v-click>

**有没有其他的？**

</v-click>

<v-click>

- Table 组件
- Dropdown 组件
- Cascader 组件
- TreeSelect 组件

</v-click>

<v-click>

**还有么？**

</v-click>

<v-click>

- Select - 数组也是树

</v-click>

---

# 目录

&nbsp;

- 如何优雅的组织样式
- css-render，另一种 CSS-in-JS 的方式
- 树形数据使用场景
- **如何获取树形数据**
- 涉及的特性
- 特性的常见实现
- 特性的统一实现
-  一点性能优化

---

# 如何获取树形数据

### Props API vs Children API

<div class="grid grid-cols-2 gap-x-4 mb-4">

<div>

Props API

```jsx
<Tree
  data={[
    { label: 'A', key: 'A' },
    {
      label: 'B',
      key: 'B',
      children: [
        {
          label: 'C',
          key: 'C'
        }
      ]
    }
  ]}
/>
```

</div>

<div>

Children API

```jsx
<Tree>
  <TreeNode key="A">A</TreeNode>
  <TreeNode key="B">
    B<TreeNode key="C">C</TreeNode>
  </TreeNode>
</Tree>
```

<v-click>

😊 看起来更简单，数据和 JSX 结构一致，label 好写

</v-click>

<v-click>

😕 但是出来混总是要还的

- 最终 `JSX.Element` 还是要被转化成 data
- 不然怎么知道谁先谁后
- `children` => `data`，难写，性能差

</v-click>

</div>

</div>

---

# 如何获取树形数据

&nbsp;

😊 看起来更简单，数据和 JSX 结构一致，label 好写

- 支持这两个会给你带来很多开发上的麻烦
- 如果你在用户、老板的胁迫下，或者觉得好处更多增加了这类 API，做好花更多时间维护的准备

😕 但是出来混总是要还的

- 最终 `JSX.Element` 还是要被转化成 data（不然怎么知道谁先谁后，谁是父级谁是子级）
- `children` => `data`，难写，性能差
- 你的用户会来问你为啥 `TreeNode` 不能继续封装
- 性能不好优化
  - React：每次创建很多 ReactElement
  - Vue：收集过程避免触发依赖

---

# 目录

&nbsp;

- 如何优雅的组织样式
- css-render，另一种 CSS-in-JS 的方式
- 树形数据使用场景
- 如何获取树形数据
- **涉及的特性**
- 特性的常见实现
- 特性的统一实现
-  一点性能优化

---

# 树形数据涉及的特性

| **名称**   | **场景**                                             |
| ---------- | ---------------------------------------------------- |
| 查询节点   | 通过 key 查询                                        |
| 勾选       | 非级联选择；级联选择                                 |
| Group 节点 | 把一些选项合为一组                                   |
| 移动       | 父、子、兄弟；在成组节点中；循环；跳过 Disabled 节点 |
| 折叠       | 折叠展开                                             |
| 打平节点   | 虚拟列表                                             |
| 非数据节点 | divider                                              |
| 获取路径   | 高亮选中的 item                                      |

---

# 树形数据涉及的特性

| **组件**   | **勾选** | **移动** | **成组** | **折叠** | **非数据节点** | **打平节点** | **查询节点** | **获取路径** |
| ---------- | -------- | -------- | -------- | -------- | -------------- | ------------ | ------------ | ------------ |
| Tree       | ✅       | ✅       | ✅       | ✅       | ❌             | ✅           | ✅           | ❌           |
| Menu       | ❌       | ❌       | ✅       | ✅       | ❌             | ❌           | ✅           | ✅           |
| Table      | ✅       | ❌       | ❌       | ✅       | ❌             | ✅           | ✅           | ❌           |
| Dropdown   | ❌       | ✅       | ✅       | ❌       | ✅             | ❌           | ✅           | ✅           |
| Cascader   | ✅       | ✅       | ❌       | ❌       | ❌             | ❌           | ✅           | ✅           |
| TreeSelect | ✅       | ✅       | ✅       | ✅       | ❌             | ✅           | ✅           | ❌           |

---

# 目录

&nbsp;

- 如何优雅的组织样式
- css-render，另一种 CSS-in-JS 的方式
- 树形数据使用场景
- 如何获取树形数据
- 涉及的特性
- **特性的常见实现**
- 特性的统一实现
-  一点性能优化

---

# 实现特性的常见方法

针对于每种组件定制特定的数据结构

<div class="grid grid-cols-2 gap-x-4 mb-4">

<div>

### Tree

```ts
export interface DataNode {
  checkable?: boolean
  children?: DataNode[]
  // ...
}
```

### Cascader

```ts
export interface CascaderOption {
  value?: string | number
  label?: React.ReactNode
  // ...
}
```

各自为政，维护困难

</div>

<div>

### Select

```ts
export interface OptionCoreData {
  key?: Key
  disabled?: boolean
  // ...
}
```

### Menu

```ts
export interface MenuInfo {
  key: string
  keyPath: string[]
  // ...
}
```

</div>

</div>

---

# 目录

&nbsp;

- 如何优雅的组织样式
- css-render，另一种 CSS-in-JS 的方式
- 树形数据使用场景
- 如何获取树形数据
- 涉及的特性
- 特性的常见实现
- **特性的统一实现**
-  一点性能优化

---

# 使用同一种数据结构处理

&nbsp;

**这种数据结构需要满足：**

- 不修改原有数据
- 每个节点和原有数据一一映射
- 每个节点可以找回原有数据节点

**假设我们有一个很强大的方法 `createXTree`**

- 可以创造出一个 `xTree` 实例
- `xTree` 中的节点叫 `xNode`，和原有数据节点一一对应
- `xTree` 实现了所有之前提到的特性

---

# `xTree` 实现的特性

| **名称**       | **方法**                                              |
| -------------- | ----------------------------------------------------- |
| _可追溯原数据_ | `xNode.rawNode` 即对应原始节点                        |
| 查询节点       | `xTree.getNode(key)`                                  |
| Group 节点     | `{ type: 'group', label: '', children: [] }`          |
| 非数据节点     | `{ type: 'ignored' }`                                 |
| 获取路径       | `xNode.getPath()`                                     |
| 移动           | `xNode.getPrev()`, `xTree.getPrev(key)`, ...          |
| 折叠           | `xTree.getFlattenedNodes(collapsedKeys)`              |
| 打平节点       | `xTree.getFlattenedNodes(collapsedKeys)`              |
| 勾选           | `xTree.getCheckedKeys(checkedKeys, cascade: boolean)` |

---

# 特性实现：可追溯原数据

树的遍历

```js
function createXTree(data) {
  const xTree = {
    nodes: []
    // ...
  }
  function traverse(data) {
    // 向 xTree.nodes 添加 xNode
    // xNode.rawNode 设为原始 node
    xNode.rawNode = originalNode
  }
  traverse(data)
  return xTree
}
```

---

# 特性实现：查询节点

查询，建立使用 Map

```js
function createXTree(data) {
  const xTree = {
    nodes: [],
    map: new Map()
  }
  function traverse(data) {
    // 向 xTree.nodes 添加 xNode
    // xNode.rawNode 设为原始 node
    xNode.rawNode = originalNode
    // 遍历的过程将 xNode 添加到 xTree.map
    xTree.map.set(rawNode.key, xNode)
  }
  traverse(data)
  return xTree
}
```

---

# 特性实现：Group 节点和非数据节点

为 `xNode` 建立相关属性

```js
const xNode = {
  rawNode,
  isIgnored() {
    return this.rawNode.type === 'ingored'
  },
  isGroup() {
    return this.rawNode.type === 'group'
  }
}
```

之后会介绍为什么用 `getter`，无伤大雅

---

# 特性实现：移动

关联 `xNode`，在建立 `xTree` 的过程中为 `xNode` 添加属性

```js
function createXTree(data) {
  // ...
  function traverse(data) {
    // 向 xTree.nodes 添加 parent, children, siblings
    xNode.parent = traverseParent
    xNode.children = traverseChildren
    xNode.siblings = traverseSiblings
  }
  traverse(data)
  return xTree
}
```

```js
const xNode = {
  // return parent
  getParent() {},
  // loop xNode.children
  getFirstAvailableChild() {},
  // loop xNode.siblings
  getNext() {}
}
```

---

# 特性实现：获取路径

一个低级的移动

```js
const xNode = {
  getPath() {
    const path = [this]
    while (this.parent) {
      path.push(this.parent)
    }
    return path.reverse()
  }
}
```

---

# 特性实现：折叠 + 打平节点

用于虚拟列表的实现

依然使用树的遍历


```js
function flatten(nodes, collapsedKeys) {
  const flattenedNodes = []
  function traverse(nodes) {
    nodes.forEach((node) => {
      flattenedNodes.push(node)
      if ('children' in node && collapsedKeys.includes(node.key)) {
        traverse(node.children)
      }
    })
  }
  traverse(nodes)
  return flattenedNodes
}
```

<v-click>

<img src="/tree.png" style="position: absolute; left: 0; right: 0; top: 0; bottom: 0;width:100%;height:100%;"/>

</v-click>

---

# 特性实现：勾选

<div style="display: flex;">

<div>

**关于勾选**

- 重点在于 Cascade Check
- 如果你用过 antd，使用 `checkStrictly=false` 来开启这个 feature

**期望**

- 性能好，时间复杂度 `o(n)`
- 不影响原有数据结构（不直接在输入数据上修改）
- 支持不完整的异步数据（即使只有部分数据也可以正确的勾选）
- 处理 Disabled 节点
- 不重新生成整颗树
- 对于任意的输入，都能计算出正确的勾选节点

</div>

<div style="position: relative; display: flex; margin-right: 16px; width: 20%;">
  <img src="/cascade1.png" style="display: block; position: absolute; left: 0; right: 0; top: 0; bottom: 0;" />
  <img v-click src="/cascade2.png" style="display: block; position: absolute; left: 0; right: 0; top: 0; bottom: 0;" />
</div>

</div>

---

# 特性实现：勾选

&nbsp;

**直接计算一颗新的树？性能开销有点大**

<div class="grid grid-cols-2 gap-x-4 mb-4">

<div>

```js
const checkedKeys = [2]
const tree = [
  {
    key: 1,
    children: [
      {
        key: 2
      },
      {
        key: 3
      }
    ]
  },
  {
    key: 4
  }
]
```

</div>

<div>

```js
const newTree = [
  {
    key: 1,
    checked: 'half',
    children: [
      {
        checked: true,
        key: 2
      },
      {
        checked: false,
        key: 3
      }
    ]
  },
  {
    key: 4
  }
]
```

</div>

</div>

---

# 特性实现：勾选

&nbsp;

**希望直接拿到相关的 key**

<div class="grid grid-cols-2 gap-x-4 mb-4">

<div>

```js
const checkedKeys = [2]
const tree = [
  {
    key: 1,
    children: [
      {
        key: 2
      },
      {
        key: 3
      }
    ]
  },
  {
    key: 4
  }
]
```

</div>

<div>

```js
const newKeys = {
  checked: [2],
  halfChecked: [1]
}
```

<v-click>

🤔 能不能用常见的前序遍历计算？

</v-click>

<v-click>

- ❌ 自顶向下？不可，父级的状态依赖子级
- ❌ 子级还没计算，父级无法知道
- ❌ 父级依赖子级，递归套递归？性能差

</v-click>

<v-click>

🤔 自底向上呢？

</v-click>

<v-click>

- ❌ 也不够，子级的状态依赖祖先

</v-click>

</div>

</div>

---

# 特性实现：勾选

&nbsp;

<div style="display: flex; align-items: stretch; height: 54vh;">

<div>

<p style="margin-top: 0;">😎 真相只有一个，先自顶向下，再自底向上。</p>

<div v-click="2">

- 先将选中的 key 向下传播选中状态
  - 选中的 key 下面的节点全部选中
  - Disabled 节点不传播

</div>

<div v-click="4">

- 再将选中的 key 向上传播选中 + 半选中状态
  - 从最底层向上计算
  - 当前遍历的节点是否勾选（半勾选）通过下一层的勾选状态确定 - 因为自底向上传播，计算上层时下层已经计算完毕
  - Disabled 节点不传播

</div>

<div v-click="6">

- 🤔 那么每一层的节点哪里来的？
  - 在创建 `xTree` 的时候遍历储存的

</div>

</div>

<div style="position: relative; width: 35%; margin-left: 24px;">

<img style="position: absolute; left: 0; top: 0; height: 40vh;" src="/check1.png" />
<img v-click="1" style="position: absolute; left: 0; top: 0; height: 40vh;" src="/check2.png" />
<img v-click="3" style="position: absolute; left: 0; top: 0; height: 40vh;" src="/check3.png" />
<img v-click="5" style="position: absolute; left: 0; top: 0; height: 40vh;" src="/check4.png" />

</div>

</div>

---

# 目录

&nbsp;

- 如何优雅的组织样式
- css-render，另一种 CSS-in-JS 的方式
- 树形数据使用场景
- 如何获取树形数据
- 涉及的特性
- 特性的常见实现
- 特性的统一实现
- ** 一点性能优化**

---

# 性能优化 1：利用 Getter 减少 Vue 中不必要的渲染

```js
export default {
  name: 'Tree',
  setup(props) {
    return {
      xTree: computed(() => createXTree(props.treeData))
    }
  },
  render() {
    // 使用 xTree 渲染树
  }
}
```

假设有某一个节点的 `disabled` 更改，不想触发树的渲染。

`createXTree` 中不能访问原有数据节点的 `disabled`。

---

# 性能优化 1：利用 Getter 减少 Vue 中不必要的渲染

```js
function createXTree() {
  const xNode = {
    disabled: node.disabled // 不可
  }
}
```

**使用 getter**

哪里渲染哪里访问

```js
function createXTree() {
  const xNode = {
    get disabled() {
      return node.disabled
    }
  }
}
```

---

# 性能优化 2：利用 Object.create 优化节点创建性能

&nbsp;

**一个节点**

```js
const xNode = {
  key: '',
  getPrev() {},
  getNext() {}
}
```

在节点挂方法，每个节点实例都需要属性吗？答案是否定的。

---

# 性能优化 2：利用 Object.create 优化节点创建性能

&nbsp;

**降低内存占用**

```js
const node = {
  key: ''
}

Object.setPrototypeOf(node, {
  getPrev() {},
  getNext() {}
})

// 或

node.__proto__ = {
  getPrev() {},
  getNext() {}
}
```

---

# 性能优化 2：利用 Object.create 优化节点创建性能

[benchMark](https://perf.link/#eyJpZCI6IjdmNzFnZGpydHN6IiwidGl0bGUiOiJGaW5kaW5nIG51bWJlcnMgaW4gYW4gYXJyYXkgb2YgMTAwMCIsImJlZm9yZSI6ImZ1bmN0aW9uIGNyZWF0ZUZyb21PYmooKXtcbiAgY29uc3QgYiA9IHsgYjogJ2InIH1cbiAgZm9yIChsZXQgaSA9IDA7IGkgPCAxMDAwOyArK2kpIHtcbiAgIGNvbnN0IGEgPSBPYmplY3QuY3JlYXRlKGIpXG4gICBhLmEgPSAnYSdcbiB9XG59XG5cblxuZnVuY3Rpb24gY3JlYXRlRnJvbVByb3RvKCl7XG4gY29uc3QgYiA9IHsgYjogJ2InIH1cbiBmb3IgKGxldCBpID0gMDsgaSA8IDEwMDA7ICsraSkge1xuICAgICBjb25zdCBhID0geyBhOiAnYScgfVxuICAgICBPYmplY3Quc2V0UHJvdG90eXBlT2YoYSwgYilcbiAgIH1cbn0iLCJ0ZXN0cyI6W3sibmFtZSI6IlRlc3QgQ2FzZSIsImNvZGUiOiJjcmVhdGVGcm9tUHJvdG8oKSIsInJ1bnMiOls2NTAwLDc1MDAsNjUwMCw1NTAwLDkwMDAsODAwMCw1MDAwLDgwMDAsOTUwMCw4NTAwLDkwMDAsODUwMCwxMDAwMCw5NTAwLDY1MDAsNTAwMCw0NTAwLDQ1MDAsNTUwMCw4MDAwLDg1MDAsNDUwMCw1MDAwLDUwMDAsNjAwMCw1NTAwLDU1MDAsOTUwMCw4NTAwLDc1MDAsMTAwMDAsNTAwMCw1NTAwLDUwMDAsODUwMCw1MDAwLDY1MDAsOTAwMCw2MDAwLDY1MDAsNTUwMCw5NTAwLDk1MDAsNjAwMCw1MDAwLDY1MDAsNzAwMCw5MDAwLDkwMDAsOTUwMCw1NTAwLDU1MDAsNTUwMCw3MDAwLDYwMDAsNTUwMCw5NTAwLDUwMDAsNzAwMCw1NTAwLDUwMDAsNTUwMCw3MDAwLDU1MDAsNDUwMCw1NTAwLDU1MDAsNDUwMCw1MDAwLDU1MDAsNTUwMCw1MDAwLDUwMDAsNTUwMCw2NTAwLDY1MDAsNzAwMCw1NTAwLDUwMDAsOTUwMCw1MDAwLDYwMDAsNTUwMCw1NTAwLDUwMDAsNDUwMCw2MDAwLDY1MDAsNzUwMCw1MDAwLDYwMDAsODUwMCw1MDAwLDY1MDAsOTAwMCw2MDAwLDc1MDAsOTUwMCw1MDAwLDU1MDBdLCJvcHMiOjY1NTV9LHsibmFtZSI6IlRlc3QgQ2FzZSIsImNvZGUiOiJjcmVhdGVGcm9tT2JqKCkiLCJydW5zIjpbOTUwMCwxMzAwMCw5MDAwLDgwMDAsMTUwMDAsMTQwMDAsOTAwMCwxMzUwMCwxNDUwMCwxMDAwMCwxNDAwMCwxMTAwMCwxNDUwMCwxNzAwMCw3MDAwLDc1MDAsNjUwMCw5MDAwLDY1MDAsMTM1MDAsMTUwMDAsMTU1MDAsMTY1MDAsMTQ1MDAsMTE1MDAsMTYwMDAsMTcwMDAsMTMwMDAsMTI1MDAsMTA1MDAsMTUwMDAsMTUwMDAsMTcwMDAsMTcwMDAsMTI1MDAsMTUwMDAsMTcwMDAsMTQwMDAsMTA1MDAsODAwMCwxNzAwMCwxNzAwMCwxMDUwMCwxNzAwMCwxNjAwMCwxNzAwMCw4MDAwLDE0NTAwLDExMDAwLDE1NTAwLDEwNTAwLDE3MDAwLDE3MDAwLDk1MDAsMTcwMDAsNzAwMCwxNzAwMCwxNzAwMCwxMDAwMCwxNzAwMCwxNTAwMCwxMjUwMCwxMDAwMCwxNzAwMCwxNjUwMCwxNzAwMCwxNzAwMCwxMjAwMCwxNzAwMCw5MDAwLDE3MDAwLDE3MDAwLDE3MDAwLDE2MDAwLDE3MDAwLDE3MDAwLDEzMDAwLDE3MDAwLDE3MDAwLDEzNTAwLDE2NTAwLDcwMDAsMTcwMDAsMTcwMDAsMTcwMDAsMTY1MDAsMTcwMDAsMTcwMDAsOTUwMCwxNjAwMCwxNzAwMCwxMzUwMCwxNjUwMCwxNzAwMCwxNTAwMCwxNzAwMCwxMTUwMCwxNTUwMCwxNjUwMCwxNzAwMF0sIm9wcyI6MTM5OTB9XSwidXBkYXRlZCI6IjIwMjMtMDctMzFUMDk6MzA6NTMuODQ3WiJ9)

&nbsp;

**提升性能**

```js
const node = Object.create({
  getPrev() {},
  getNext() {}
})

node.key = ''
```

<div class="grid grid-cols-2 gap-x-4 mb-4">

<div>

### Object.setPrototypeOf

```js
const b = { b: 'b' }
for (let i = 0; i < 1000; ++i) {
  const a = { a: 'a' }
  Object.setPrototypeOf(a, b)
}
```

`1710.3 ops/s 慢 95.77%`

</div>

<div>

### Object.create

```js
const b = { b: 'b' }
for (let i = 0; i < 1000; ++i) {
  const a = Object.create(b)
  a.a = 'a'
}
```

`40397.54 ops/s`

</div>

</div>

---

# TreeMate地址

TreeMate 实现了所有特性

一个组件库只需要一种树形数据结构。

https://github.com/07akioni/treemate

```js
import { createTreeMate } from 'treemate'

const treeMate = createTreeMate(treeData)
```
