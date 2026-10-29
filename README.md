# PERSONA//OS

AI 人设生成终端：单页前端（`index.html`）+ 一组角色 CRUD 接口，两种运行方式共用同一份前端。

## 本地运行

```bash
npm start            # Node 版：http://127.0.0.1:8000，数据存 data/characters.json
npm run dev:worker   # Workers 版：http://127.0.0.1:8787，本地模拟 KV 绑定
```

首次跑 Workers 版前先 `npm install`。

## 部署

- **Cloudflare Workers（私有仓库，Dashboard 手动导入）**：见 [DEPLOY-CLOUDFLARE.md](DEPLOY-CLOUDFLARE.md)
- `render.yaml` 是早期的 Render 配置，Cloudflare 不会读取；不再需要时可以删除。

## 目录

| 路径 | 说明 |
| --- | --- |
| `index.html` | 整个前端（内联样式与脚本，hash 路由） |
| `server.js` | Node 版服务端，静态文件 + `/api/characters*`，数据落 `data/characters.json` |
| `src/worker.js` | Workers 版入口，接口行为与 `server.js` 一致；数据走 KV（`CHARACTERS` 绑定），未绑定时退化为内存 |
| `wrangler.jsonc` | Workers 部署配置：名称、入口、兼容性日期、HTML 文本模块规则、KV 绑定（注释状态） |

## 接口

| 方法 | 路径 | 说明 |
| --- | --- | --- |
| GET | `/api/characters` | 角色列表（摘要，带评论） |
| POST | `/api/characters` | 新建角色，返回 `{ id }` |
| GET | `/api/characters/:id` | 角色详情（含 `state`） |
| PUT | `/api/characters/:id` | 覆盖保存 |
| DELETE | `/api/characters/:id` | 删除 |
| POST | `/api/characters/:id/comments` | 追加一条评论 |
| GET | `/api/health` | Workers 版专用：返回当前存储模式 `kv` / `memory` |

请求与响应都是 JSON；`index.html` 里只依赖这些字段，改动接口时注意同步。
