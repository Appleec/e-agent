---
name: jishi-mcp
description: 通过「即时MCP」插件读取与操作即时设计(JiShi Design)画布。当用户要查看/读取设计稿节点、获取选中元素、按 ID 或名称查子节点、导出 PNG/JPG/SVG/PDF 图片、批量下载图标 SVG、在画布中创建或修改图层、或用 jsDesign API 执行自定义脚本时使用。涉及即时设计、JiShi、jsDesign、画板/Frame、节点导出、设计稿读取等场景即触发。
---

# 即时设计 MCP 操作指南

通过 `jishi-design` MCP 服务器操作即时设计画布。工具均以 `mcp__jishi-design__` 为前缀。

## 前置检查

操作前确认插件已连接。若任何工具返回「插件未连接 / JiShi Design Plugin not connected」，提示用户在即时设计中打开 `九匠即时MCP` 插件并确认面板状态为绿色「已连接」。

## 标准工作流

读取设计稿遵循「发现 → 下钻 → 操作」三步，避免一次性拉取整棵树：

1. **发现** — `get_page_nodes`：列出当前页所有顶层画板（id、name、childCount）。这是入口，几乎所有任务都从这里开始拿到 `id`。
2. **下钻** — `get_node_children`（按 `id` 或 `name`）或 `get_selection`（用户已选中时）：获取目标节点的层级与属性。
3. **操作** — `save_image` / `download_icons` 导出，或 `execute_script` 做自定义创建/修改。

## 工具速查

| 工具 | 用途 | 关键参数 |
|------|------|---------|
| `get_page_nodes` | 列出顶层画板 | （无） |
| `get_selection` | 读取当前选中节点 | `includeChildren`、`maxDepth`、`maxNodes` |
| `get_node_children` | 读取指定节点子树 | `id` 或 `name`（二选一）、`maxDepth`、`maxNodes` |
| `save_image` | 导出单个节点为图片 | `nodeId`、`format`、`scale`、`savePath` |
| `download_icons` | 批量导出 SVG | `nodeIds`、`svgOutlineText` 等 |
| `execute_script` | 执行 jsDesign 脚本 | `code` |
| `list_plugin_clients` | 列出已连接客户端 | （无） |

所有工具均接受可选 `clientId`（string，多客户端寻址，见下文）与 `timeoutMs`（number，单次请求超时，默认 30000）。

## 完整参数

### get_page_nodes
无参数。返回 `{ pageName, pageId, nodeCount, nodes: [{ id, name, type, x, y, width, height, childCount }] }`。

### get_selection
| 参数 | 类型 | 默认 | 说明 |
|------|------|------|------|
| `includeChildren` | boolean | `true` | 是否包含子节点 |
| `maxDepth` | number | `12` | 最大递归深度（≥0） |
| `maxNodes` | number | `2000` | 最大节点数（硬上限 10000） |
| `clientId` | string | 首个在线 | 目标客户端 |

返回 `{ nodes, summary }`。`nodes` 含 id/name/type、位置尺寸、fills/strokes/effects、文本属性（characters/fontSize/fontName 等）、layout 及子节点。未选中时 `nodes` 为空、`summary.totalSerialized` 为 0。

### get_node_children
| 参数 | 类型 | 默认 | 说明 |
|------|------|------|------|
| `id` | string | — | 节点 ID（与 `name` 至少一个） |
| `name` | string | — | 节点名称，匹配首个同名 |
| `includeChildren` | boolean | `true` | 是否递归子节点的子节点 |
| `maxDepth` | number | `12` | 最大递归深度 |
| `maxNodes` | number | `2000` | 最大节点数（硬上限 10000） |
| `clientId` | string | 首个在线 | 目标客户端 |

返回结构同 `get_selection`：`{ nodes, summary }`。

### save_image
| 参数 | 类型 | 默认 | 说明 |
|------|------|------|------|
| `nodeId` | string | 当前选中 | 要导出的节点 ID |
| `format` | string | `PNG` | `PNG`/`JPG`/`SVG`/`PDF` |
| `scale` | number | `1` | 缩放倍数（仅 PNG/JPG） |
| `filename` | string | 节点名 | 文件名 |
| `savePath` | string | — | 绝对路径；提供则存盘（目录自动创建），否则触发浏览器下载 |
| `clientId` | string | 首个在线 | 目标客户端 |

### download_icons
| 参数 | 类型 | 默认 | 说明 |
|------|------|------|------|
| `nodeIds` | string[] | 当前选中 | 要导出的节点 ID 数组 |
| `contentsOnly` | boolean | `true` | 仅导出内容 |
| `useAbsoluteBounds` | boolean | `false` | 使用绝对边界 |
| `svgOutlineText` | boolean | `true` | 文本转轮廓 |
| `svgIdAttribute` | boolean | `false` | 添加 ID 属性 |
| `svgSimplifyStroke` | boolean | `true` | 简化描边 |
| `clientId` | string | 首个在线 | 目标客户端 |

返回 `{ success: [{ id, name, svg }], failed: [{ id, name, error }] }`。

### execute_script
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `code` | string | 是 | JavaScript 代码，作用域含 `jsDesign`/`console`，被包进 async 函数，需 `return` 结果 |
| `clientId` | string | 否 | 目标客户端 |

### list_plugin_clients
无参数。返回 `[{ clientId, connectedAt, info: { port, docName, userAgent } }]`。

## 数据完整性（重要）

`get_selection` 和 `get_node_children` 返回 `{ nodes, summary }`。**每次都要检查 `summary.truncated`**：

- 为 `true` 时数据被截断（超过 `summary.limits` 的 maxDepth/maxNodes）。
- 处理办法：调高 `maxDepth`/`maxNodes` 重试，或对子节点 `id` 分批调用 `get_node_children` 逐块读取，而不是把结果当成完整数据使用。

不要默默忽略截断——否则会基于不完整的设计稿给出错误结论。

## 多客户端寻址

用户可能同时打开多个设计文档（各为独立客户端）。当结果可能来自错误文档、或用户明确指定文档时：

1. 先调用 `list_plugin_clients` 拿到客户端列表（含 `clientId`、`info.docName`）。
2. 在后续工具调用中传入对应的 `clientId` 参数锁定目标文档。

不传 `clientId` 时默认操作首个在线客户端；单文档场景无需关心。

## 导出建议

- 位图导出用 `scale: 2`（或更高）避免模糊。
- `save_image` 传 `savePath`（绝对路径）直接存盘，目录不存在会自动创建；不传则触发浏览器下载。
- 多个图标/矢量批量导出优先 `download_icons`（一次返回 `success`/`failed` 两组）。

## execute_script 用法

代码被包裹进 async 函数，可用 `await`，必须 `return` 结果；作用域内有 `jsDesign` 和 `console`。

```js
// 读取当前页名称
return jsDesign.currentPage.name;

// 创建红色矩形并返回 id
const rect = jsDesign.createRectangle();
rect.resize(100, 100);
rect.fills = [{ type: 'SOLID', color: { r: 1, g: 0, b: 0 } }];
jsDesign.currentPage.appendChild(rect);
return rect.id;
```

当内置工具无法满足（如批量改属性、复杂查询、创建图层）时再用 `execute_script`；能用专用工具就别用脚本。修改型脚本先向用户说明将产生的画布变更。

## 注意事项

- 节点 `id` 形如 `"1:2"`，由发现/下钻步骤获得，不要臆造。
- 读取大画板先用较小的 `maxDepth` 探查结构，再针对性深入，控制返回体积。
- `get_node_children` 的 `id` 与 `name` 至少提供一个；`name` 匹配首个同名节点。
