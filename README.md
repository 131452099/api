# 安装说明 - 随机图片 API

## 一、Cloudflare Pages 部署（API 后端）

### 1.1 准备工作

- Cloudflare 账号
- 已上传图片到 Cloudflare R2（图片列表 `image_list.json` 可访问）
- 域名 DNS 托管在 Cloudflare（方便添加自定义域）

### 1.2 部署步骤

#### 方法一：控制台上传（推荐）

1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com/)
2. 左侧菜单 → **Pages**
3. 点击「创建项目」→「直接上传」
4. 项目名称填 `img-api`
5. 上传 `img-api-v6.zip`（包含 `_worker.js`）
6. 点击「部署」

#### 方法二：连接 Git 仓库

1. 在 Pages 页面点击「连接到 Git」
2. 选择仓库和分支
3. 构建设置：保留默认（因为是 Pages Functions，无需构建）
4. 点击「保存并部署」

### 1.3 添加自定义域名

部署完成后：

1. 进入项目 → 「自定义域」标签
2. 点击「设置自定义域」
3. 输入 `a.600060.xyz` → 点击「继续」
4. 输入 `api.600060.xyz` → 点击「继续」
5. 等待 SSL 证书签发（约 5-15 分钟）

**注意：** 如果之前有 Worker Routes（`*.600060.xyz/*`）干扰，需要先删除。

---

## 二、部署 API 说明主页

`api.600060.xyz` 需要显示 API 说明主页（即 `index.html`）。

### 2.1 部署到 Cloudflare Pages（静态托管）

如果要和 API 共用一个 Pages 项目，可以用**路由重写**或者**分开部署**。

#### 分开部署（推荐）

1. 新建一个 Pages 项目（名称如 `api-docs`）
2. 上传包含 `index.html` 的文件夹
3. 自定义域添加 `api.600060.xyz`

#### 共用一个项目（进阶）

修改 `_worker.js`，根据域名判断：

- `a.600060.xyz` → 返回随机图片
- `api.600060.xyz` → 返回 `index.html` 内容

（当前 `img-api-v6.zip` 已实现此逻辑）

### 2.2 部署到其他静态托管

也可以使用以下服务：

- **Vercel** — 直接拖拽上传
- **Netlify** — 直接拖拽上传
- **腾讯云 EdgeOne Pages** — 国内访问速度快
- **GitHub Pages** — 免费，但国内访问可能不稳定

---

## 三、验证部署

### 3.1 测试随机图片 API

在浏览器中访问：

```
https://a.600060.xyz/
```

应该会 302 跳转到一张随机图片，例如：

```
https://opg.dpdns.org/images/(pid-70004972)白のフランドール.webp
```

### 3.2 测试 API 说明主页

在浏览器中访问：

```
https://api.600060.xyz/
```

应该显示 API 说明页面，包含：
- API 地址（`https://a.600060.xyz/`）
- 「随机图片」按钮（可点击）
- 「复制 API」按钮
- 背景显示随机图片

### 3.3 测试 `<img>` 标签兼容性

创建一个测试 HTML：

```html
<img src="https://a.600060.xyz/" alt="随机图片">
```

在浏览器中打开，应该能正常显示图片。

---

## 四、故障排查

### 问题 1：自定义域名访问 522 / ERR_EMPTY_RESPONSE

**原因：** Worker Routes 冲突

**解决：**
1. 进入 Cloudflare Dashboard → **Workers & Pages** → **路由**
2. 删除所有指向 `*.600060.xyz/*` 的路由
3. 等待 1-2 分钟，再试

### 问题 2：自定义域名显示默认 Workers 页面

**原因：** Pages 部署未生效，或者路由优先级问题

**解决：**
1. 确认 Pages 项目已部署成功
2. 确认自定义域已添加且状态为「Active」
3. 尝试刷新 SSL/TLS 设置（SSL 模式改为「灵活」，再改回「完全」）

### 问题 3：背景图加载失败

**原因：** CORS 限制（`<img>` 不受 CORS 限制，但 `fetch` 会）

**解决：** 已修复，直接用 `<img src="a.600060.xyz/">` 加载背景图

---

## 五、文件清单

| 文件 | 用途 | 部署位置 |
|------|------|-----------|
| `img-api-v6.zip` | Cloudflare Pages Functions（API 后端） | Cloudflare Pages `img-api` 项目 |
| `api-4/index.html` | API 说明主页 | Cloudflare Pages `api-docs` 项目（或共用项目） |
| `README.md` | 项目说明文档 | 随代码一起上传 |

---

## 六、更新部署

### 6.1 更新 API 逻辑

1. 修改 `_worker.js`
2. 重新打包成 `img-api-v7.zip`
3. 在 Cloudflare Pages 控制台 → 「部署」→「上传文件」

### 6.2 更新说明主页

1. 修改 `index.html`
2. 重新上传到 Pages 项目（覆盖 `index.html`）

---

## 联系方式

如有问题，请联系：

- 邮箱：<btbbu@qq.com>
- GitHub：<https://github.com/131452099>
