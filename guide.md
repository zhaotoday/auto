#### 云手机存在问题
- 云手机网络连接不稳定
- APP 运行出现卡顿现象
- APP 存在闪退情况
- 不同机型存在差异

#### 优化建议
1. 元素定位策略
- 推荐使用多属性组合定位元素，提高定位准确性：
```javascript
const element = element.find(
  ({ resourceId, className, text }) =>
    resourceId === "y" && className === "x" && text === "z",
);
```
- 仅使用 ID 定位可能导致误匹配，尤其在多 APP 版本兼容场景下；
- 一些在所有步骤都可能出现的异常，可以在查找和点击元素失败时触发（或者通过事件触发器实现）：
```typescript
// shared/element.ts

export const find = (
  predicate: ElementPredicate,
  options: {
    tryTimes: number;
    wait: number;
    onError: () => void;
  },
) => {};

export const click = (
  predicate: ElementPredicate,
  options: {
    tryTimes: number;
    wait: number;
    onError: () => void;
  },
) => {};
```
- 处理异常场景时，应把识别场景的逻辑和处理的逻辑分开，减少误操作概率；
- 修复 Bug 时，应尽量复现 Bug 场景再修复，否则会有不可预知的副作用；

#### 问题排查指引
- 需提供以下信息：
  - 完整任务截图（包含运行 ID、任务时间信息）
  - 系统运行日志
- 日志查阅方法：
  - 根据任务时间筛选日志
  - 通过任务 ID 定位具体日志
- 问题复现处理：
  - 若 QA 测试发现已修复问题再次出现
  - 需提供新任务信息供开发排查
  - 同个问题可能出现在不同任务环节

#### 目录结构
#### 脚本规范
