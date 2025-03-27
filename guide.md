#### 客观问题
- 云手机网络不稳定；
- APP 卡顿；
- APP 闪退；

#### 注意点
- 查找元素时，条件应尽量详细，使用多个属性组合，避免找到的元素非预期：
```javascript
const element = driver.findElement(
  ({ resourceId, className, text }) =>
    resourceId === "y" && className === "x" && text === "z",
);
```
- 同个页面可能有多个元素 ID 是相同的，不同页面也可能存在相同 ID 的元素；
