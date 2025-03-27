#### 客观问题
- 云手机网络不稳定；
- APP 卡顿；
- APP 闪退；

#### 注意点
- 查找元素时，条件应尽量详细，使用多个属性组合，避免找到的元素非预期：
  ```js
const element = driver.findElement(
  ({ resourceId, className, text }) =>
    resourceId === "y" && className === "x" && text === "z",
);
  ```
- 使用 ID 查找元素时应尽量配合其他属性；
