**Deep Link（深度链接）** 是一种可以直接跳转到 App 内部特定页面的链接（类似网页的 URL），而不仅仅是打开 App 的首页。它允许用户绕过首页，直达 App 中的某个功能或内容（如商品页、文章、个人主页等）。  

---

## **1. Deep Link 的核心作用**
- **精准跳转**：比如从浏览器或短信点击链接，直接打开 App 的某个商品页，而不是首页。
- **跨平台引流**：从网页、其他 App、广告等场景引导用户进入目标 App 的指定页面。
- **提升用户体验**：减少用户手动查找内容的步骤，提高转化率（如电商、社交媒体常用）。

---

## **2. Deep Link 的常见形式**
### **（1）标准 Deep Link**
- **格式**：`scheme://path`  
  （例如：`myapp://product/123`）
- **特点**：  
  - 需要 App 提前声明支持该链接（在 `AndroidManifest.xml` 中配置）。  
  - 如果用户未安装 App，链接会失效（可能无反应或跳转错误页）。

### **（2）HTTP/HTTPS 链接（App Links）**
- **格式**：`https://example.com/product/123`  
- **特点**：  
  - 需要 App 和域名绑定（通过 [Digital Asset Links](https://developers.google.com/digital-asset-links) 验证）。  
  - 如果用户未安装 App，会默认打开网页版（无缝回退）。  
  - **Android 官方推荐**，安全性更高。

---

## **3. Deep Link 的典型应用场景**
| 场景                | 示例链接                          | 说明                          |
|---------------------|----------------------------------|-----------------------------|
| 电商商品页          | `taobao://item?id=123`          | 直接跳转到淘宝商品详情页       |
| 社交媒体用户主页    | `twitter://user?screen_name=xxx`| 跳转到 Twitter 用户主页       |
| 视频播放页          | `youtube://watch?v=123abc`      | 直接播放 YouTube 视频         |
| 地图导航            | `amap://navi?source=xx&dest=yy` | 高德地图启动导航到目标地点     |

---

## **4. Deep Link 在 Android 中的实现**
### **（1）在 AndroidManifest.xml 中声明**
```xml
<activity android:name=".ProductActivity">
    <intent-filter>
        <!-- 处理 https 链接 -->
        <data android:scheme="https" android:host="example.com" android:pathPrefix="/product"/>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
    </intent-filter>
    <intent-filter>
        <!-- 处理自定义 scheme 链接 -->
        <data android:scheme="myapp" android:host="product" />
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```
### **（2）在 Activity 中解析 Intent**
```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    val data = intent.data  // 获取 Deep Link 数据
    if (data != null) {
        val productId = data.getQueryParameter("id")  // 解析参数
        loadProduct(productId)  // 加载对应内容
    }
}
```

---

## **5. Deep Link vs App Link**
| 特性                | Deep Link                     | App Link（Android 官方方案）       |
|---------------------|-------------------------------|----------------------------------|
| **链接格式**         | `myapp://path`                | `https://example.com/path`       |
| **未安装 App 时**    | 无反应或报错                  | 跳转到网页版（fallback）          |
| **安全性**           | 较低（可能被劫持）            | 高（需域名验证）                  |
| **适用场景**         | 内部跳转、广告引流            | 官网、SEO、跨平台推广             |

---

## **6. 测试 Deep Link**
### **（1）使用 `adb` 测试**
```bash
adb shell am start -a android.intent.action.VIEW -d "myapp://product/123"
```
### **（2）在浏览器或短信中测试**
- 在手机浏览器输入 `myapp://product/123`，看是否跳转。
- 发送含 Deep Link 的短信，检查点击效果。

---

## **7. 常见问题**
### **Q1：Deep Link 不生效？**
- 检查 `AndroidManifest.xml` 是否正确声明。
- 确保链接格式和参数匹配。
- 使用 `adb logcat` 查看错误日志。

### **Q2：如何避免被恶意劫持？**
- 优先使用 **App Links**（HTTPS + 域名验证）。
- 对 Deep Link 参数做校验（如签名验证）。

---

### **总结**
Deep Link 是 App 跳转的核心技术，广泛用于电商、社交、广告等场景。正确使用可以大幅提升用户体验和转化率，但需注意安全性和兼容性问题。如果是面向普通用户的公开链接，建议使用 **App Links**（HTTPS 链接）。
