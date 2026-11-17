# rustroot-lzcapp

[RustRoot](https://github.com/jimichenx/rustroot) 的懒猫微服打包：面向有经验开发者的 Rust 开源指南（Astro 静态站点，中英双语，离线内置）。

- 打包方式：**源码构建**（静态站点，无后端）。CI 内嵌上游源码快照，`npm run build` 产出 zh/en 双构建，nginx 托管（`/` → 中文，`/en/` → 英文）
- 许可：正文 CC BY-NC-ND 4.0（未修改、署名、非商业分发），`examples/` 示例代码 MIT
- 版本策略：跟随上游源码版本（git version source），打 tag `v*` 触发构建发布
- 只发布到喵喵商店（`stores.private`），不走官方商店

## 与上游部署的差异

| 项 | 上游（astro dev/preview） | 本打包 |
|----|--------------------------|--------|
| 服务 | `astro dev`（Node 进程） | nginx 静态托管构建产物 |
| 语言 | 双域名双站（rustroot.cn / rustroot.dev） | 单应用双路径：`/` 中文，`/en/` 英文 |
| 源码 | 本地 clone | build context 内嵌 `source.tar.gz`（SHA 记录于 `build/SOURCE_SHA`） |

语言切换按钮仍指向上游官网域名（构建时写死），本地浏览内容不受影响。

## 自动发布

`.github/workflows/lazycat.yml` 使用 [ca-x/lazycat-github-action](https://github.com/ca-x/lazycat-github-action) `v1`：手动或定时触发，`toolchains: docker` 从内嵌 Dockerfile 构建、打包 LPK、发 GitHub Release 并发布到喵喵商店。Secrets：`APPSTORE_URL`、`APPSTORE_TOKEN`（组织级）。

## 更新上游版本

```bash
# 重新抓取上游 main 快照（记录新 SHA）
curl -sL https://api.github.com/repos/jimichenx/rustroot/tarball/main -o /tmp/rustroot-src.tgz
tar tzf /tmp/rustroot-src.tgz | head -1   # 读取快照 SHA
tar xzf /tmp/rustroot-src.tgz -C /tmp/rustroot-extract
tar -czf build/source.tar.gz -C /tmp/rustroot-extract <快照目录名>
echo <SHA> > build/SOURCE_SHA
# 打 tag 触发构建
git tag v1.x.y && git push origin v1.x.y
```
