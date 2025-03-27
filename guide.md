#### 客观问题
- 云手机网络不稳定；
- APP 卡顿；
- APP 闪退；

#### 注意点
- 查找元素时，条件应尽量详细，使用多个属性组合，才能更精准找到预期的元素：
```javascript
const element = driver.findElement(
  ({ resourceId, className, text }) =>
    resourceId === "y" && className === "x" && text === "z",
);
```
- 使用 ID 查找元素，也会造成碰撞（误查找），特别是当兼容多个 APP 版本时；

#### 排查问题
- 提供任务截图（含：运行 ID、任务开始时间/结束时间、任务截图）、日志；
