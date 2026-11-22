# card_workbench · 雾濑夜行前端

这是角色卡《雾濑夜行》的前端静态资源，供 jsdelivr CDN 托管。

## 目录结构

```
card_workbench/
  雾濑夜行/
    状态栏.html      ← 编译后的「海雾监控终端」状态栏（jsdelivr 加载这个）
    开局表单.html    ← 编译后的自建巫女表单
    源码/           ← 前端源码（供以后修改重编译）
      schema.ts
      界面/
        状态栏/    (App.vue / index.ts / store.ts / global.css / index.html)
        开局表单/  (App.vue / index.ts / index.html)
```

## 推送到 GitHub

```bash
cd card_workbench          # 仓库根目录（已 clone 你的 card_workbench）
# 把 card_workbench_staging/雾濑夜行/ 整个目录放进来
git add 雾濑夜行
git commit -m "雾濑夜行前端"
git push
```

推送后，jsdelivr 地址（卡里已经写好了）：

- 状态栏：`https://testingcf.jsdelivr.net/gh/lyc2371606223/card_workbench/雾濑夜行/状态栏.html`
- 开局表单：`https://testingcf.jsdelivr.net/gh/lyc2371606223/card_workbench/雾濑夜行/开局表单.html`

## 注意事项

1. **jsdelivr 缓存**：推完不会立刻生效，主服务器约 12h 才刷新。想立刻生效，去 `https://www.jsdelivr.com/tools/purge` 输入链接（把 `testingcf` 换成 `cdn.jsdelivr`）手动 purge；或临时换 `fastly.jsdelivr.net` 镜像域名。
2. **路径必须一致**：如果改了目录名或文件名，要同步改卡内两张正则（`正则/状态栏界面.html`、`正则/开局表单界面.html`）里的地址，再重新 `pack`。
3. **改前端后重新编译**：改了 `源码/` 里的 Vue 源码后，需要回 `tavern_helper_template` 里 `pnpm build`，把新的 `dist/雾濑夜行/界面/状态栏/index.html` 覆盖到 `状态栏.html`，再推。
