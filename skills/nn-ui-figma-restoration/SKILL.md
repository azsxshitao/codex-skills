---
name: nn-ui-figma-restoration
description: Use when implementing Vue UI from Figma URLs, logged-in browser access, Chrome screenshot comparison, current-module style matching, annotated motion/effects, layered image assets, fonts, or pixel-perfect parity requests involving NN component library and @nn/ui.
---

# NN UI Figma 还原

## 核心原则

UI 还原先还原设计系统语义，再还原像素。只要 Figma 节点来自 NN 基础组件库，就先匹配 `@nn/ui` 组件和 props；只有确认组件库缺口后，才用业务组件或局部 CSS 补齐。

**必用子技能**：遇到 Figma 地址时，先使用 `figma-implement-design` 拉取设计上下文、截图、结构、尺寸、资源与组件实例信息。

## 适用场景

- 用户输入 Figma 地址，要求在当前模块内实现页面、弹窗、卡片、表单或组件。
- 用户同时提供设计截图或效果截图，要求一比一还原。
- 用户没有提供截图，但给了 Figma 节点地址，需要自行获取 Figma 截图并对比本地实现。
- Figma 文件、Figma 组件库或本地页面对比依赖用户已登录浏览器状态。
- 设计图包含注释提示、动画、特效、复杂图层叠加、图片蒙版或特殊字体。
- 页面包含按钮、输入框、Tabs、Select、Switch、Checkbox、Radio、Badge、Empty 等可能对应 `@nn/ui` 的元素。

## 登录浏览器选择

1. 打开用户提供的 Figma 地址和 [NN 规范 基础组件库](https://www.figma.com/design/gYGjpqolatxHetP7IzOkOa/NN-%E8%A7%84%E8%8C%83-%E5%9F%BA%E7%A1%80%E7%BB%84%E4%BB%B6%E5%BA%93?node-id=387-24655&t=hfwjuDPDSLSLyyRZ-0) 时，必须使用有 Figma 登录态的浏览器。
2. 若当前任务没有指定浏览器，先询问用户要使用哪个已登录浏览器；默认选项是谷歌浏览器 Chrome。
3. 用户选择 Chrome 时，优先使用 `chrome:control-chrome` 操作用户的 Chrome 会话，不用无登录态的内置浏览器代替。
4. 若所选浏览器没有登录态或无法访问 Figma 文件，先让用户完成登录或重新选择浏览器，再继续拉取设计和组件库信息。

## 设计资源拉取

1. 先用 `figma-implement-design` 获取 Figma 节点的设计上下文、截图、层级结构、尺寸、自动布局、颜色、字体、圆角、阴影、图片资源与交互状态。
2. 检查每个关键节点是否为组件实例，记录组件名、variant、component set、实例 props、slot 文本和覆盖样式。
3. 特别检查设计图中的注释、批注、说明文字、prototype 交互、动画提示、hover/click 状态、转场、阴影、模糊、滤镜、混合模式和遮罩效果。
4. 获取所有文本节点的 font 信息：font family、font weight、font size、line height、letter spacing、font style、颜色和文本截断规则。
5. 对来自组件库的实例，优先确认是否来自 [NN 规范 基础组件库](https://www.figma.com/design/gYGjpqolatxHetP7IzOkOa/NN-%E8%A7%84%E8%8C%83-%E5%9F%BA%E7%A1%80%E7%BB%84%E4%BB%B6%E5%BA%93?node-id=387-24655&t=hfwjuDPDSLSLyyRZ-0)。
6. 用户提供截图时，以用户截图为视觉验收基准；未提供截图时，从 Figma 节点导出或截取基准图。

## 注释、动画和特效

1. Figma 中的注释提示不是装饰信息；必须逐条读取并转成实现要求或验收项。
2. 对动画和特效，记录触发方式、持续时间、延迟、缓动、起止状态、透明度、位移、缩放、旋转、模糊、阴影、渐变、滤镜和层级变化。
3. 如果设计只在批注里说明 hover、点击、展开、加载、骨架屏、粒子、光效或转场，仍然必须实现或在交付中说明阻塞原因。
4. 动效实现要跟随当前模块已有技术栈和样式风格；已有 transition、animation、motion helper、组件状态或 CSS 变量时优先复用。

## 复杂图层和图片溯源

1. 遇到复杂叠层、光影、纹理、蒙版、玻璃态、渐变叠加、背景装饰或多次截图对比仍无法还原的区域，不要继续盲目调 CSS。
2. 必须在已登录浏览器中模拟点击或选择对应 Figma 节点，逐层查看 fills、images、effects、masks、blend mode、opacity、export settings 和隐藏图层。
3. 如果发现设计效果来自图片、切图、贴图、位图纹理、图片叠加或导出的合成资源，必须导出并使用该图片资源；不要再用 CSS 近似还原。
4. 使用图片资源时，保留设计中的裁切、尺寸、位置、透明度、圆角、混合模式和响应式规则，并按当前模块资源引用方式落地。

## 字体检查

1. 实现前必须获取设计图中的 font 信息，并核对当前模块是否已加载对应字体或等价字体变量。
2. 字体存在于项目时，沿用当前模块的 `font-family`、token、变量或公共 class；字体缺失时，不得静默 fallback，必须说明缺口并选择导入字体资源或向用户确认替代字体。
3. 字体还原不仅是 family：同时核对字号、字重、行高、字距、颜色、抗锯齿差异、数字字体、截断和换行规则。

## 组件匹配

| Figma 线索                      | 仓库核对点                | 实现要求                                                                         |
| ------------------------------- | ------------------------- | -------------------------------------------------------------------------------- |
| Button / 按钮实例               | `@nn/ui` 按钮导出与 props | 优先使用 `@nn/ui` 按钮组件，按 type、size、disabled、loading、icon 等 props 映射 |
| Input / Textarea                | `@nn/ui` 输入组件 props   | 优先使用输入组件 props，还原 placeholder、状态、清除按钮、前后缀                 |
| Select / 下拉                   | `@nn/ui` 下拉组件 props   | 核对 options、multiple、filterable、clearable、placement 等 props                |
| Tabs                            | `@nn/ui` Tabs 组件 props  | 核对 items、active、type、size、滚动状态和内容插槽                               |
| Checkbox / Radio / Switch       | `@nn/ui` 表单组件 props   | 使用 modelValue、value、disabled、size、effect、active/inactive 文案或颜色       |
| Badge / Label / Empty / Tooltip | `@nn/ui` 展示或反馈类组件 | 优先 props 表达颜色、尺寸、状态和内容                                            |

匹配流程：

1. 先按当前仓库的依赖、别名和导出入口定位 `@nn/ui` 的源码、类型声明或组件文档。
2. 用 `rg` 搜索目标组件的 `Props`、`defineProps`、`buildProps`、`withDefaults` 和当前模块里的实际用法。
3. 先用组件 props 表达 Figma variant；props 无法表达的视觉差异，再补 wrapper class。
4. 若 Figma 组件没有 `@nn/ui` 对应物，必须在实现或交付说明中写清原因，并保持局部实现可替换。

## 当前模块风格检查

写代码前必须先检查目标模块，不允许直接套用其它模块的结构或样式：

1. 确认目标模块根目录、入口文件、路由或页面注册方式，以及模块内是否有独立的规则文档。
2. 阅读目标模块已有页面、组件、hooks、store、API、mock、类型文件，提炼命名、目录分层、状态管理、错误处理和数据流写法。
3. 阅读目标模块已有样式：Tailwind、SCSS、CSS Module、BEM、变量、主题 token、移动端适配、响应式断点和公共 class。
4. 新增 UI 的文件组织、组件拆分、props 命名、事件命名、样式写法、资源引用和中文文案风格必须跟随当前模块。
5. 若 Figma 设计与当前模块样式体系冲突，先保留设计视觉目标，再用当前模块已有 token、mixin、class 或组件封装表达；只有缺口明确时才新增局部样式。

## 实现顺序

1. 建立 Figma 节点清单：页面尺寸、栅格或布局方式、组件实例、图片资源、文本样式、颜色 token、状态。
2. 建立 `@nn/ui` 映射表：Figma 组件名、仓库组件名、props、仍需 CSS 补齐的差异。
3. 建立注释、动画、特效、复杂图层、图片资源和字体清单，确认哪些必须实现、哪些需要资源导出。
4. 完成当前模块风格检查，明确本次实现要沿用的代码结构、组件写法和样式体系。
5. 在目标模块内按现有模式实现，使用该模块已经采用的 Vue、TypeScript、状态管理和样式组织方式。
6. 所有尺寸、间距、字体、圆角、边框、阴影、颜色、图片裁切都必须来自 Figma 数据、当前模块 token 或截图量测，不凭感觉估算。
7. 页面实现后启动对应本地页面，打开谷歌浏览器 Chrome，自动操作界面、截图并与基准图逐项对比。

## Chrome 自动对比循环

每轮实现后必须执行：

1. 用谷歌浏览器 Chrome 打开本地实现页面，视口尺寸与用户截图或 Figma 基准图保持一致。
2. 通过浏览器自动化操作页面：等待接口和资源加载、滚动到目标区域、点击或展开交互控件、触发 hover、selected、disabled、loading、empty 等关键状态。
3. 截取本地页面同尺寸截图；必要时分别截取首屏、滚动区域、弹层、下拉框和交互状态。
4. 与用户截图或 Figma 基准图对比首屏、关键组件、间距、字体、字号、颜色、圆角、图片、特效、动效状态和滚动区域。
5. 记录差异，回到 Figma 结构或 `@nn/ui` props 再核对，不直接用随机 CSS 试错。
6. 若某个复杂区域多次无法还原，回到 Figma 模拟点击对应节点并检查是否有图片叠加、隐藏图层或导出资源；发现是图片就改用图片资源。
7. 修正后再次用 Chrome 自动操作并截图，直到视觉差异只剩浏览器渲染、字体抗锯齿或数据内容差异。

没有截图输入时，不得跳过视觉验收；必须自行从 Figma 获取基准截图。

## 常见错误

| 错误                            | 正确做法                                                      |
| ------------------------------- | ------------------------------------------------------------- |
| 只看截图，不拉取 Figma 结构     | 先用 `figma-implement-design` 获取结构、尺寸、资源和组件实例  |
| 用未登录浏览器打开 Figma 文件   | 询问用户选择已登录浏览器，默认使用 Chrome                     |
| 发现 Figma 组件实例后仍手写 div | 先核对 NN 基础组件库和 `@nn/ui` 组件 props                    |
| 忽略设计注释里的动效和特效      | 把注释、动画、特效逐条转成实现要求和验收项                    |
| 复杂叠层多次调 CSS 仍不像       | 模拟点击 Figma 节点查图片、遮罩、混合模式和隐藏图层           |
| 发现效果是图片还继续 CSS 还原   | 导出并使用图片资源，按当前模块资源方式引用                    |
| 没获取字体就写默认字体          | 提取 font family、weight、size、line-height、letter-spacing   |
| 不看当前模块风格就开始写代码    | 先检查目标模块的目录、组件、样式、token 和已有页面写法        |
| 只还原静态默认态                | 同时检查 hover、disabled、selected、loading、empty 等设计状态 |
| props 能表达却写死 CSS          | 优先 props，CSS 只补组件库未暴露的局部差异                    |
| 画完页面不操作、不截图          | 必须用 Chrome 自动操作页面并截图对比，差异闭环后才能说明完成  |

## 完成交付

最终回复必须包含：

- 使用到的 Figma 节点或截图来源。
- Figma 文件和 NN 基础组件库使用的已登录浏览器；未指定时说明是否使用默认 Chrome。
- 已检查的设计注释、动画、特效、复杂图层、图片资源和字体信息。
- 当前模块代码风格和样式风格的检查结论，以及本次实现沿用的模式。
- 匹配到的 `@nn/ui` 组件和未匹配原因。
- 已执行的 Chrome 自动操作、本地截图对比方式、验证命令和剩余可接受差异。
- 若受登录态、接口数据或 Figma 权限阻塞，说明阻塞点和已完成的替代验证。
