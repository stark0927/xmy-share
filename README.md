# 微信分享 H5 页面部署指南

## 📁 文件说明

- `activity_share.html` - 活动分享的 H5 页面

## 🚀 部署步骤

### 方案 1：使用免费静态托管服务（推荐新手）

#### Vercel 部署
1. 注册 [Vercel](https://vercel.com) 账号
2. 安装 Vercel CLI：`npm install -g vercel`
3. 在 `h5_share` 目录下运行：`vercel`
4. 按提示完成部署，获得域名如：`https://your-project.vercel.app`

#### Netlify 部署
1. 注册 [Netlify](https://netlify.com) 账号
2. 拖拽 `h5_share` 文件夹到 Netlify 网站
3. 获得域名如：`https://your-project.netlify.app`

#### GitHub Pages 部署
1. 创建 GitHub 仓库
2. 上传 `h5_share` 文件夹内容
3. 在仓库设置中启用 GitHub Pages
4. 获得域名如：`https://yourusername.github.io/repo-name`

### 方案 2：使用云服务器

#### 阿里云 OSS / 腾讯云 COS
1. 开通对象存储服务
2. 创建 Bucket，设置为公共读
3. 上传 `activity_share.html`
4. 绑定自定义域名（需备案）
5. 配置 HTTPS 证书

## ⚙️ 配置 Android 代码

### 1. 修改分享链接

在 [DetailContentActivity.kt:206](../app/src/main/java/com/xmy/xmy_client/component/activitydetail/DetailContentActivity.kt#L206) 中：

```kotlin
// 将 "https://yourdomain.com" 替换为你的实际域名
val shareUrl = "https://yourdomain.com/activity_share.html?id=${detail.activityId}"
```

### 2. Deep Link 配置

已在 [AndroidManifest.xml:191](../app/src/main/AndroidManifest.xml#L191) 中配置：

```xml
<data
    android:scheme="xmyapp"
    android:host="activity"
    android:pathPrefix="/detail" />
```

Deep Link 格式：`xmyapp://activity/detail?id=123`

## 🔧 H5 页面配置

### 后端 API 已配置

H5 页面已经配置好了后端 API 接口：

**接口地址：** `https://xmyreq.xmyyl.cn/sps/activityHall/ssai?storeActivityId={activityId}`

**请求方式：** POST

**响应格式：**
```json
{
  "success": true,
  "data": {
    "activityId": 123,
    "title": "活动标题",
    "greetings": "欢迎参加",
    "realActivityName": "主题活动",
    "description": "活动描述",
    "activityTimeStr": "2026-04-10 14:00",
    "activityEndTimeStr": "2026-04-10 16:00",
    "area": "活动地点",
    "activityKind": 1,
    "isCharge": 0,
    "sponsor": "主办方",
    "ask": "活动要求"
  }
}
```

### 如果需要修改 API 地址

如果后端 API 地址发生变化，修改 `activity_share.html` 中的 `apiUrl`：

```javascript
const apiUrl = `https://xmyreq.xmyyl.cn/sps/activityHall/ssai?storeActivityId=${activityId}`;
```

### 修改 Deep Link Scheme（可选）

如果需要修改 Deep Link 的 scheme，需要同步修改：

1. H5 页面中的 `deepLink` 变量
2. AndroidManifest.xml 中的 `android:scheme`

## 📱 测试流程

### 1. 本地测试 H5 页面
```bash
# 在 h5_share 目录下启动本地服务器
python -m http.server 8000
# 或使用 Node.js
npx http-server
```

访问：`http://localhost:8000/activity_share.html?id=123`

### 2. 测试 Deep Link
```bash
# 使用 adb 测试 Deep Link
adb shell am start -W -a android.intent.action.VIEW -d "xmyapp://activity/detail?id=123"
```

### 3. 完整流程测试
1. 部署 H5 页面到服务器
2. 修改 Android 代码中的域名
3. 编译安装 App
4. 在 App 中点击分享按钮
5. 分享到微信
6. 在微信中点击分享内容
7. 点击"打开 App"按钮
8. 验证是否正确跳转到活动详情页

## 🎯 工作原理

```
用户分享 → 微信 → H5 页面 → 点击按钮 → Deep Link → 打开 App
    ↓
分享链接: https://yourdomain.com/activity_share.html?id=123
    ↓
H5 页面显示活动详情 + "打开 App" 按钮
    ↓
按钮链接: xmyapp://activity/detail?id=123
    ↓
Android 系统识别 Deep Link
    ↓
启动 DetailContentActivity，传入 activityId=123
```

## ⚠️ 注意事项

1. **HTTPS 必须**：微信要求分享链接必须是 HTTPS
2. **域名备案**：如果使用国内服务器，域名需要备案
3. **跨域问题**：如果 H5 需要调用 API，注意配置 CORS
4. **微信缓存**：微信会缓存分享内容，测试时可能需要清除缓存
5. **Deep Link 测试**：确保 App 已安装，否则 Deep Link 无法唤起

## 🔗 相关文档

- [微信开放平台](https://open.weixin.qq.com/)
- [Android Deep Links](https://developer.android.com/training/app-links)
- [Vercel 文档](https://vercel.com/docs)
