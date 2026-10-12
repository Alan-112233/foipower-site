# 斐石科技 Faith Technology · 储能产品官网

便携式储能电源产品站。单页应用（纯静态），无框架、无构建步骤、无外部依赖。

**English**: Static single-page site for Faith Technology's portable power station lineup. No framework, no build step, no external dependencies.

---

## 目录结构 / Structure

```
.
├── index.html      全站结构 / 样式 / 交互（含全部文案与产品数据）
├── assets/         产品图、系列图、场景图、新闻配图（28 张 webp）
├── specs/          产品规格书 PDF（10 份）
├── favicon.ico     ┐
├── favicon-32.png  │ 站点图标（4 种尺寸 / 格式）
├── favicon-192.png │
├── apple-touch-icon.png ┘
├── .github/workflows/keepalive.yml   定时保活（见下方「表单与线索」）
├── .nojekyll       关闭 GitHub Pages 的 Jekyll 处理
├── .gitattributes  二进制资源不做行尾转换
└── README.md
```

- 路由基于 URL hash，例如 `#/products`、`#/product/ecandle-pro`、`#/news/xxx`
- 所有页面由 `index.html` 内的数据表（`PRODUCTS` / `SERIES` / `NEWS` / `FAQ` …）驱动生成
- 图片走相对路径 `assets/…`，规格书走 `specs/…`，**无任何外链资源**

## 本地预览 / Local preview

双击 `index.html` 即可。若浏览器限制 `file://` 下的行为，可用任意静态服务器：

```bash
python -m http.server 8000
# 打开 http://localhost:8000
```

## 部署到 GitHub Pages

1. 在 GitHub 新建一个仓库（例如 `foipower-site`），**不要**勾选初始化 README
2. 在本目录执行：

```bash
git init
git add .
git commit -m "feat: 官网首版"
git branch -M main
git remote add origin https://github.com/<你的用户名>/<仓库名>.git
git push -u origin main
```

3. 仓库页面 → **Settings → Pages**
4. **Source** 选 `Deploy from a branch`，**Branch** 选 `main`、目录选 `/ (root)`，保存
5. 等 1–2 分钟，访问 `https://<你的用户名>.github.io/<仓库名>/`

> 自定义域名：在 **Settings → Pages → Custom domain** 填入域名，并在域名服务商处按提示配置 CNAME。

## 内容维护 / Maintenance

| 要改什么 | 怎么改 |
|---|---|
| 全站文案、产品参数、新闻、FAQ | 编辑 `index.html` 内对应的数据表 |
| 替换产品图 / 场景图 / 新闻图 | 用同名文件覆盖 `assets/` 下对应文件（保持 `.webp` 格式） |
| 替换规格书 | 用同名文件覆盖 `specs/` 下对应 PDF |

改完直接 commit + push，GitHub Pages 会自动重新发布。

**注意**：图片请保持原有文件名与格式。文件名即页面上的引用键，改名会导致图片显示不出来。

## 说明 / Notes

- 站内提供**中文 / English** 切换（导航右侧），语言偏好记录在浏览器本地
- 「新闻与动态」页设有**管理员入口**（筛选行最右侧），密码解锁后可在线新增 / 编辑 / 删除动态
  - 新增的动态保存在**访问者自己的浏览器**中，不会同步到服务器，也不会出现在其他访客页面上
  - 该入口为前端密码，仅用于防止误操作，不构成访问控制

## 表单与线索 / Contact form & leads

「联系我们」表单提交后写入**云端数据库**（Supabase），站长在站内后台查看与导出。

| 事项 | 说明 |
|---|---|
| 后台入口 | `你的网址/#/leads` —— **不在导航里**，需手输地址；未登录时显示登录表单 |
| 账号 | 由 Supabase Auth 校验，**密码不进本仓库**；数据库侧策略写死管理员邮箱 |
| 权限 | 匿名访客只能**提交**，读不到任何一条；只有管理员登录后可读、可删、可导出 |
| 导出 | 列表页「导出 CSV」（Excel 直开，带 BOM）/「导出 JSON」（完整备份） |
| 配置在哪 | `index.html` 内搜 `SUPABASE_CFG` —— 留空即自动禁用该通道 |

- `SUPABASE_CFG` 里的 **`url` 与 `anonKey` 是公开信息**（设计如此），安全性由数据库的 RLS 策略保证。**切勿**把 `service_role` / `secret` key 填进来，它会绕过全部 RLS。
- 表单为**多通道并发**：Supabase → 自建中转（企业微信智能表格）→ 邮件 → 群机器人，**任一成功即算成功**；全部失败时给出「邮件发送 / 复制内容」的降级出口，不会假装提交成功。
- **`keepalive.yml` 为什么存在**：Supabase 免费项目连续 7 天无数据库活动会被自动暂停，暂停期间客户提交会**静默失败**。该工作流每 2 天发一次只读查询保活，失败时 GitHub 会发邮件告警。
  ⚠️ GitHub 会停掉「60 天无任何提交」的仓库的定时任务 —— 长期不动代码时，请每两个月提交一次，或到 Actions 页面点 **Enable workflow**。
- **备份**：Supabase 免费版不提供自动备份。请定期到后台「导出 CSV / JSON」自行存档。
- **隐私政策**：`#/privacy`。数据存储在新加坡节点（跨境传输），保留期限为自最后一次联系起 12 个月。条款为通用模板措辞，上线前建议经法务复核。

## 许可 / License

© Faith Technology. 保留所有权利。站点内容、图片与文档归斐石科技所有，未经授权不得转载或商用。
