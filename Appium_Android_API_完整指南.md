# Appium Android 自动化 API 完整指南

## 目录
- [1. 会话管理 API](#1-会话管理-api)
- [2. 元素定位 API](#2-元素定位-api)
- [3. 元素操作 API](#3-元素操作-api)
- [4. 触摸和手势 API](#4-触摸和手势-api)
- [5. 设备控制 API](#5-设备控制-api)
- [6. 应用管理 API](#6-应用管理-api)
- [7. 键盘和按键 API](#7-键盘和按键-api)
- [8. 屏幕操作 API](#8-屏幕操作-api)
- [9. 文件和存储 API](#9-文件和存储-api)
- [10. 系统交互 API](#10-系统交互-api)
- [11. 调试和日志 API](#11-调试和日志-api)
- [12. WebView 相关 API](#12-webview-相关-api)
- [13. 高级功能 API](#13-高级功能-api)
- [14. 错误处理和重试 API](#14-错误处理和重试-api)

---

## 1. 会话管理 API

### 会话控制

```javascript
// 创建会话
const driver = await remote(capabilities);

// 获取会话信息
await driver.getSession();

// 删除会话
await driver.deleteSession();

// 获取会话 ID
const sessionId = driver.sessionId;

// 获取所有会话
await driver.getSessions();

// 设置超时
await driver.setTimeout({ 'implicit': 10000 });
await driver.setTimeout({ 'pageLoad': 30000 });
await driver.setTimeout({ 'script': 30000 });

// 获取超时设置
await driver.getTimeouts();
```

**参数说明：**
- `implicit`: 隐式等待时间（毫秒）
- `pageLoad`: 页面加载超时时间（毫秒）
- `script`: 脚本执行超时时间（毫秒）

---

## 2. 元素定位 API

### 查找元素

```javascript
// 单个元素查找
await driver.$('selector');                    // CSS 选择器风格
await driver.findElement('xpath', '//button'); // 原生方法

// 多个元素查找
await driver.$$('selector');
await driver.findElements('xpath', '//button');

// 按不同策略定位
await driver.findElement('id', 'button_id');
await driver.findElement('class name', 'android.widget.Button');
await driver.findElement('xpath', '//android.widget.Button[@text="登录"]');
await driver.findElement('accessibility id', 'login_button');
await driver.findElement('-android uiautomator', 'new UiSelector().text("登录")');
await driver.findElement('tag name', 'button');
await driver.findElement('name', 'button_name');

// WebDriverIO 风格定位
await driver.$('#button_id');                  // ID
await driver.$('.button_class');               // Class
await driver.$('~accessibility_id');           // Accessibility ID
await driver.$('android=new UiSelector().text("登录")'); // UiAutomator
await driver.$('//android.widget.Button');     // XPath

// 相对定位
const parent = await driver.$('#parent');
await parent.$('./child');                     // 子元素
await parent.$('../sibling');                  // 兄弟元素
```

### 元素等待

```javascript
// 等待元素出现
await driver.waitForDisplayed({ timeout: 10000 });
await driver.waitForExist({ timeout: 10000 });
await driver.waitForEnabled({ timeout: 10000 });
await driver.waitForClickable({ timeout: 10000 });

// 等待元素消失
await driver.waitForDisplayed({ timeout: 10000, reverse: true });
await driver.waitForExist({ timeout: 10000, reverse: true });

// 自定义等待条件
await driver.waitUntil(async () => {
    const element = await driver.$('#dynamic_element');
    return await element.isDisplayed();
}, { timeout: 10000, timeoutMsg: '元素未出现' });
```

**定位策略说明：**
- `id`: 通过 resource-id 定位
- `class name`: 通过类名定位
- `xpath`: 通过 XPath 表达式定位
- `accessibility id`: 通过 content-desc 定位
- `-android uiautomator`: 使用 UiAutomator 选择器
- `tag name`: 通过标签名定位

---

## 3. 元素操作 API

### 基础操作

```javascript
const element = await driver.$('#button');

// 点击操作
await element.click();
await element.doubleClick();

// 文本输入
await element.setValue('输入文本');
await element.addValue('追加文本');
await element.clearValue();

// 获取元素信息
const text = await element.getText();
const value = await element.getValue();
const tagName = await element.getTagName();
const attribute = await element.getAttribute('class');
const property = await element.getProperty('value');
const cssValue = await element.getCSSProperty('color');

// 元素状态检查
const isDisplayed = await element.isDisplayed();
const isEnabled = await element.isEnabled();
const isSelected = await element.isSelected();
const isExisting = await element.isExisting();
const isClickable = await element.isClickable();
const isFocused = await element.isFocused();

// 元素位置和大小
const location = await element.getLocation();
const size = await element.getSize();
const rect = await element.getRect();

// 滚动到元素
await element.scrollIntoView();
await element.scrollIntoView({ block: 'center' });
```

### 高级操作

```javascript
// 拖拽操作
await element.dragAndDrop(targetElement);
await element.dragAndDrop({ x: 100, y: 200 });

// 移动到元素
await element.moveTo();
await element.moveTo({ xOffset: 10, yOffset: 10 });

// 选择操作 (下拉框等)
await element.selectByIndex(1);
await element.selectByValue('option_value');
await element.selectByVisibleText('显示文本');

// 提交表单
await element.submit();
```

**返回值说明：**
- `getLocation()`: 返回 `{x: number, y: number}`
- `getSize()`: 返回 `{width: number, height: number}`
- `getRect()`: 返回 `{x: number, y: number, width: number, height: number}`

---

## 4. 触摸和手势 API

### 基础触摸

```javascript
// 单点触摸
await driver.touchAction({
    action: 'tap',
    x: 100,
    y: 200
});

// 长按
await driver.touchAction({
    action: 'longPress',
    x: 100,
    y: 200,
    duration: 2000
});

// 按压和释放
await driver.touchAction([
    { action: 'press', x: 100, y: 200 },
    { action: 'wait', ms: 1000 },
    { action: 'release' }
]);
```

### 滑动手势

```javascript
// 简单滑动
await driver.swipe(startX, startY, endX, endY, duration);

// 复杂滑动序列
await driver.touchAction([
    { action: 'press', x: 100, y: 500 },
    { action: 'wait', ms: 100 },
    { action: 'moveTo', x: 100, y: 100 },
    { action: 'release' }
]);

// 多点触控
await driver.multiTouchAction([
    [
        { action: 'press', x: 100, y: 100 },
        { action: 'moveTo', x: 50, y: 50 },
        { action: 'release' }
    ],
    [
        { action: 'press', x: 200, y: 200 },
        { action: 'moveTo', x: 250, y: 250 },
        { action: 'release' }
    ]
]);

// 缩放手势
await driver.zoom(centerX, centerY);
await driver.pinch(centerX, centerY);
```

### WebDriverIO 手势 API

```javascript
// 滑动方向
await driver.swipeUp();
await driver.swipeDown();
await driver.swipeLeft();
await driver.swipeRight();

// 滑动到元素
await element.swipeUp();
await element.swipeDown();
await element.swipeLeft();
await element.swipeRight();

// 拖拽
await driver.dragAndDrop(sourceElement, targetElement);
```

**TouchAction 动作类型：**
- `tap`: 点击
- `press`: 按下
- `release`: 释放
- `moveTo`: 移动到
- `wait`: 等待
- `longPress`: 长按

---

## 5. 设备控制 API

### 设备信息

```javascript
// 获取设备时间
const deviceTime = await driver.getDeviceTime();
const deviceTimeFormat = await driver.getDeviceTime('YYYY-MM-DD HH:mm:ss');

// 获取设备方向
const orientation = await driver.getOrientation();

// 设置设备方向
await driver.setOrientation('PORTRAIT');  // PORTRAIT, LANDSCAPE
await driver.setOrientation('LANDSCAPE');

// 获取窗口大小
const windowSize = await driver.getWindowSize();

// 设置窗口大小
await driver.setWindowSize(width, height);

// 获取窗口位置
const windowPosition = await driver.getWindowPosition();

// 设置窗口位置
await driver.setWindowPosition(x, y);

// 获取窗口矩形
const windowRect = await driver.getWindowRect();

// 设置窗口矩形
await driver.setWindowRect(x, y, width, height);
```

### 设备状态

```javascript
// 锁定设备
await driver.lock();
await driver.lock(5); // 锁定5秒

// 解锁设备
await driver.unlock();

// 检查设备是否锁定
const isLocked = await driver.isLocked();

// 获取电池信息
const batteryInfo = await driver.getBatteryInfo();

// 获取设备活动
const currentActivity = await driver.getCurrentActivity();

// 获取当前包名
const currentPackage = await driver.getCurrentPackage();
```

### 网络控制

```javascript
// 获取网络连接状态
const networkConnection = await driver.getNetworkConnection();

// 设置网络连接
await driver.setNetworkConnection(0); // 无网络
await driver.setNetworkConnection(1); // 飞行模式
await driver.setNetworkConnection(2); // WiFi
await driver.setNetworkConnection(4); // 数据网络
await driver.setNetworkConnection(6); // WiFi + 数据

// 切换 WiFi
await driver.toggleWiFi();

// 切换数据网络
await driver.toggleData();

// 切换定位服务
await driver.toggleLocationServices();
```

**网络连接类型：**
- `0`: 无网络连接
- `1`: 飞行模式
- `2`: WiFi 连接
- `4`: 数据网络连接
- `6`: WiFi + 数据网络

**电池信息返回值：**
```javascript
{
    level: 0.8,        // 电量百分比 (0-1)
    state: 2           // 充电状态 (1=未知, 2=充电中, 3=未充电, 4=未充电, 5=充满)
}
```

---

## 6. 应用管理 API

### 应用生命周期

```javascript
// 安装应用
await driver.installApp('/path/to/app.apk');
await driver.installApp('/path/to/app.apk', { replace: true });

// 检查应用是否已安装
const isInstalled = await driver.isAppInstalled('com.example.app');

// 启动应用
await driver.activateApp('com.example.app');

// 终止应用
await driver.terminateApp('com.example.app');

// 查询应用状态
const appState = await driver.queryAppState('com.example.app');
// 返回值: 0=未安装, 1=未运行, 2=后台运行, 3=后台挂起, 4=前台运行

// 移除应用
await driver.removeApp('com.example.app');
await driver.removeApp('com.example.app', { keepData: true });

// 重置应用
await driver.reset();

// 后台运行应用
await driver.background(-1);  // 永久后台
await driver.background(5);   // 后台5秒

// 关闭应用
await driver.closeApp();

// 启动应用
await driver.launchApp();
```

### 应用信息

```javascript
// 获取应用字符串
const appStrings = await driver.getAppStrings();
const appStringsLang = await driver.getAppStrings('zh');

// 获取应用源码
const pageSource = await driver.getPageSource();

// 获取上下文
const contexts = await driver.getContexts();
const currentContext = await driver.getContext();

// 切换上下文
await driver.switchContext('NATIVE_APP');
await driver.switchContext('WEBVIEW_com.example.app');
```

**应用状态说明：**
- `0`: 应用未安装
- `1`: 应用已安装但未运行
- `2`: 应用在后台运行
- `3`: 应用在后台挂起
- `4`: 应用在前台运行

---

## 7. 键盘和按键 API

### 物理按键

```javascript
// 按键码操作
await driver.pressKeyCode(4);    // 返回键
await driver.pressKeyCode(3);    // Home键
await driver.pressKeyCode(82);   // 菜单键
await driver.pressKeyCode(24);   // 音量+
await driver.pressKeyCode(25);   // 音量-
await driver.pressKeyCode(26);   // 电源键

// 长按键码
await driver.longPressKeyCode(4);
await driver.longPressKeyCode(4, 2000); // 长按2秒

// 组合按键
await driver.pressKeyCode(4, 1); // Shift + 返回键
await driver.pressKeyCode(29, 2); // Alt + A

// 返回键
await driver.back();

// 菜单键
await driver.menu();

// Home键
await driver.home();
```

### 虚拟键盘

```javascript
// 隐藏键盘
await driver.hideKeyboard();
await driver.hideKeyboard('pressKey', 'Done');
await driver.hideKeyboard('tapOutside');

// 检查键盘是否显示
const isKeyboardShown = await driver.isKeyboardShown();

// 发送按键
await driver.sendKeys(['H', 'e', 'l', 'l', 'o']);

// 输入文本到活动元素
await driver.keys('Hello World');
```

**常用按键码：**
- `3`: Home 键
- `4`: 返回键
- `24`: 音量增加
- `25`: 音量减少
- `26`: 电源键
- `82`: 菜单键
- `67`: 删除键
- `66`: 回车键

**修饰键：**
- `0`: 无修饰键
- `1`: Shift
- `2`: Alt
- `4`: Ctrl

---

## 8. 屏幕操作 API

### 截图和录制

```javascript
// 截图
const screenshot = await driver.takeScreenshot();
await driver.saveScreenshot('./screenshot.png');

// 开始录屏
await driver.startRecordingScreen();
await driver.startRecordingScreen({
    videoSize: '1280x720',
    timeLimit: 180,
    bitRate: 4000000
});

// 停止录屏
const video = await driver.stopRecordingScreen();
require('fs').writeFileSync('./recording.mp4', video, 'base64');

// 元素截图
const elementScreenshot = await element.takeScreenshot();
await element.saveScreenshot('./element.png');
```

### 屏幕控制

```javascript
// 旋转屏幕
await driver.rotateDevice(90, 0, 0);  // x, y, z 轴旋转

// 摇晃设备
await driver.shake();

// 打开通知栏
await driver.openNotifications();

// 获取状态栏高度
const statusBarHeight = await driver.getStatusBarHeight();

// 获取导航栏高度
const navigationBarHeight = await driver.getNavigationBarHeight();
```

**录屏参数说明：**
- `videoSize`: 视频分辨率 (如 '1280x720')
- `timeLimit`: 录制时间限制（秒）
- `bitRate`: 比特率
- `bugReport`: 是否包含 bug 报告

---

## 9. 文件和存储 API

### 文件操作

```javascript
// 推送文件到设备
await driver.pushFile('/data/local/tmp/test.txt', 'SGVsbG8gV29ybGQ='); // Base64

// 从设备拉取文件
const fileContent = await driver.pullFile('/sdcard/test.txt');

// 推送文件夹
await driver.pushFolder('/local/folder', '/data/local/tmp/folder');

// 拉取文件夹
await driver.pullFolder('/data/local/tmp/folder');
```

### 剪贴板操作

```javascript
// 设置剪贴板内容
await driver.setClipboard('Hello World', 'plaintext');

// 获取剪贴板内容
const clipboardContent = await driver.getClipboard();
```

**文件操作说明：**
- 推送文件时内容需要 Base64 编码
- 拉取文件返回 Base64 编码的内容
- 文件路径必须是设备上的绝对路径

---

## 10. 系统交互 API

### 系统设置

```javascript
// 获取系统设置
const setting = await driver.getSettings();

// 更新系统设置
await driver.updateSettings({
    waitForIdleTimeout: 100,
    waitForSelectorTimeout: 1000,
    normalizeTagNames: true,
    shouldUseCompactResponses: false,
    elementResponseAttributes: "name,text,className,resourceId"
});

// 获取系统属性
const property = await driver.getSystemProperty('ro.build.version.release');
```

### 权限管理

```javascript
// 获取权限
const permissions = await driver.getPermissions('com.example.app');

// 授予权限
await driver.grantPermissions('com.example.app', ['android.permission.CAMERA']);

// 撤销权限
await driver.revokePermissions('com.example.app', ['android.permission.CAMERA']);
```

**常用系统设置：**
- `waitForIdleTimeout`: 等待空闲超时时间
- `waitForSelectorTimeout`: 选择器等待超时时间
- `normalizeTagNames`: 是否标准化标签名
- `shouldUseCompactResponses`: 是否使用紧凑响应
- `elementResponseAttributes`: 元素响应属性

**常用权限：**
- `android.permission.CAMERA`: 相机权限
- `android.permission.RECORD_AUDIO`: 录音权限
- `android.permission.ACCESS_FINE_LOCATION`: 精确定位权限
- `android.permission.READ_EXTERNAL_STORAGE`: 读取外部存储权限
- `android.permission.WRITE_EXTERNAL_STORAGE`: 写入外部存储权限

---

## 11. 调试和日志 API

### 日志操作

```javascript
// 获取日志类型
const logTypes = await driver.getLogTypes();

// 获取日志
const logs = await driver.getLogs('logcat');
const driverLogs = await driver.getLogs('driver');

// 设置日志级别
await driver.setLogLevel('debug');
```

### 性能监控

```javascript
// 获取性能数据类型
const perfDataTypes = await driver.getSupportedPerformanceDataTypes();

// 获取性能数据
const perfData = await driver.getPerformanceData('com.example.app', 'cpuinfo', 5);

// 开始性能记录
await driver.startActivity('com.example.app', '.MainActivity', {
    waitForLaunch: true
});
```

**日志类型：**
- `logcat`: Android 系统日志
- `driver`: Appium 驱动日志
- `client`: 客户端日志
- `server`: 服务器日志

**性能数据类型：**
- `cpuinfo`: CPU 使用信息
- `memoryinfo`: 内存使用信息
- `batteryinfo`: 电池信息
- `networkinfo`: 网络信息

---

## 12. WebView 相关 API

### WebView 操作

```javascript
// 获取所有上下文
const contexts = await driver.getContexts();

// 切换到 WebView
await driver.switchContext('WEBVIEW_com.example.app');

// 切换回原生应用
await driver.switchContext('NATIVE_APP');

// 在 WebView 中执行 JavaScript
const result = await driver.execute('return document.title;');

// 异步执行 JavaScript
await driver.executeAsync((callback) => {
    setTimeout(() => callback('Hello'), 1000);
});
```

**上下文类型：**
- `NATIVE_APP`: 原生应用上下文
- `WEBVIEW_<package>`: WebView 上下文

---

## 13. 高级功能 API

### UiAutomator2 特有功能

```javascript
// UiAutomator2 选择器
await driver.$('android=new UiSelector().text("登录")');
await driver.$('android=new UiSelector().resourceId("com.example:id/button")');
await driver.$('android=new UiSelector().className("android.widget.Button")');
await driver.$('android=new UiSelector().description("登录按钮")');
await driver.$('android=new UiSelector().index(0)');
await driver.$('android=new UiSelector().instance(1)');

// 组合选择器
await driver.$('android=new UiSelector().className("android.widget.EditText").instance(0)');
await driver.$('android=new UiSelector().resourceIdMatches(".*username.*")');
await driver.$('android=new UiSelector().textContains("登录")');
await driver.$('android=new UiSelector().textMatches("登录|Login")');

// UiScrollable
await driver.$('android=new UiScrollable(new UiSelector().scrollable(true)).scrollIntoView(new UiSelector().text("目标文本"))');
```

### 自定义命令

```javascript
// 添加自定义命令
driver.addCommand('customSwipe', async function(direction) {
    const { width, height } = await this.getWindowSize();
    
    switch(direction) {
        case 'up':
            await this.swipe(width/2, height*0.8, width/2, height*0.2, 1000);
            break;
        case 'down':
            await this.swipe(width/2, height*0.2, width/2, height*0.8, 1000);
            break;
        // ... 其他方向
    }
});

// 使用自定义命令
await driver.customSwipe('up');
```

**UiSelector 方法：**
- `text(String text)`: 通过文本定位
- `textContains(String text)`: 通过包含文本定位
- `textMatches(String regex)`: 通过正则表达式匹配文本
- `resourceId(String id)`: 通过资源 ID 定位
- `resourceIdMatches(String regex)`: 通过正则表达式匹配资源 ID
- `className(String className)`: 通过类名定位
- `description(String desc)`: 通过内容描述定位
- `index(int index)`: 通过索引定位
- `instance(int instance)`: 通过实例定位

---

## 14. 错误处理和重试 API

### 错误处理

```javascript
// 捕获特定错误
try {
    await driver.$('#non-existent').click();
} catch (error) {
    if (error.name === 'NoSuchElementError') {
        console.log('元素不存在');
    }
}

// 重试机制
const retry = async (fn, maxAttempts = 3) => {
    for (let i = 0; i < maxAttempts; i++) {
        try {
            return await fn();
        } catch (error) {
            if (i === maxAttempts - 1) throw error;
            await new Promise(resolve => setTimeout(resolve, 1000));
        }
    }
};

await retry(async () => {
    await driver.$('#button').click();
});
```

**常见错误类型：**
- `NoSuchElementError`: 元素不存在
- `TimeoutError`: 操作超时
- `StaleElementReferenceError`: 元素引用过期
- `ElementNotInteractableError`: 元素不可交互
- `InvalidSelectorError`: 选择器无效

---

## 实用示例

### 完整的登录流程

```javascript
const { remote } = require('webdriverio');

async function loginExample() {
    const driver = await remote({
        hostname: 'localhost',
        port: 4723,
        path: '/wd/hub',
        capabilities: {
            platformName: 'Android',
            deviceName: 'Android Emulator',
            appPackage: 'com.example.app',
            appActivity: '.MainActivity',
            automationName: 'UiAutomator2'
        }
    });

    try {
        // 等待应用启动
        await driver.pause(3000);

        // 查找并点击登录按钮
        const loginBtn = await driver.$('~login_button');
        await loginBtn.waitForDisplayed({ timeout: 10000 });
        await loginBtn.click();

        // 输入用户名
        const usernameField = await driver.$('android=new UiSelector().resourceId("username")');
        await usernameField.setValue('testuser');

        // 输入密码
        const passwordField = await driver.$('android=new UiSelector().resourceId("password")');
        await passwordField.setValue('password123');

        // 提交登录
        const submitBtn = await driver.$('android=new UiSelector().text("登录")');
        await submitBtn.click();

        // 验证登录成功
        const welcomeMsg = await driver.$('android=new UiSelector().textContains("欢迎")');
        await welcomeMsg.waitForDisplayed({ timeout: 5000 });

        console.log('登录成功');

    } catch (error) {
        console.error('登录失败:', error.message);
        await driver.saveScreenshot('./login_error.png');
    } finally {
        await driver.deleteSession();
    }
}
```

### 滑动和手势示例

```javascript
async function gestureExample(driver) {
    // 获取屏幕尺寸
    const { width, height } = await driver.getWindowSize();

    // 向上滑动
    await driver.swipe(
        width / 2,      // 起始 x
        height * 0.8,   // 起始 y
        width / 2,      // 结束 x
        height * 0.2,   // 结束 y
        1000            // 持续时间
    );

    // 长按操作
    await driver.touchAction([
        { action: 'press', x: width / 2, y: height / 2 },
        { action: 'wait', ms: 2000 },
        { action: 'release' }
    ]);

    // 缩放手势
    await driver.multiTouchAction([
        [
            { action: 'press', x: width / 2 - 50, y: height / 2 - 50 },
            { action: 'moveTo', x: width / 2 - 100, y: height / 2 - 100 },
            { action: 'release' }
        ],
        [
            { action: 'press', x: width / 2 + 50, y: height / 2 + 50 },
            { action: 'moveTo', x: width / 2 + 100, y: height / 2 + 100 },
            { action: 'release' }
        ]
    ]);
}
```

---

## 最佳实践

### 1. 元素定位最佳实践

```javascript
// 优先级顺序：
// 1. Accessibility ID (推荐)
await driver.$('~login_button');

// 2. Resource ID
await driver.$('android=new UiSelector().resourceId("com.example:id/login")');

// 3. 文本定位 (谨慎使用，可能因语言变化)
await driver.$('android=new UiSelector().text("登录")');

// 4. XPath (最后选择，性能较差)
await driver.$('//android.widget.Button[@text="登录"]');
```

### 2. 等待策略最佳实践

```javascript
// 显式等待 (推荐)
const element = await driver.$('#button');
await element.waitForDisplayed({ timeout: 10000 });

// 避免使用固定延迟
// await driver.pause(5000); // 不推荐

// 使用条件等待
await driver.waitUntil(async () => {
    const elements = await driver.$$('.item');
    return elements.length > 0;
}, { timeout: 10000 });
```

### 3. 错误处理最佳实践

```javascript
async function robustElementInteraction(selector) {
    const maxRetries = 3;
    
    for (let i = 0; i < maxRetries; i++) {
        try {
            const element = await driver.$(selector);
            await element.waitForDisplayed({ timeout: 5000 });
            await element.click();
            return; // 成功则退出
        } catch (error) {
            if (i === maxRetries - 1) {
                // 最后一次重试失败，保存截图
                await driver.saveScreenshot(`error_${Date.now()}.png`);
                throw error;
            }
            // 等待后重试
            await driver.pause(1000);
        }
    }
}
```

---

## 总结

这份文档涵盖了 Appium Android 自动化的所有主要 API。在实际使用中，建议：

1. **优先使用稳定的定位策略**（Accessibility ID、Resource ID）
2. **合理使用等待机制**，避免固定延迟
3. **完善错误处理**，提高脚本健壮性
4. **适当使用截图和日志**，便于调试
5. **遵循 Page Object 模式**，提高代码可维护性

通过合理组合这些 API，您可以构建强大而稳定的 Android 自动化测试解决方案。