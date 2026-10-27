# Omni CNC DEB 发布仓库

此仓库用于管理 Omni CNC 的 DEB 发布包及发布说明，作为 `keniutech_cnc`
源码仓库的 `omni-cnc/` Git 子模块使用。

## 发布内容

- 按版本目录保存发布包，例如 `releases/v0.8.0/`。
- 每次发布附带发布说明、对应源码提交和包的 SHA-256 校验值。
- DEB 在源码仓库通过 `./scripts/package_deb.sh` 构建，输出位于
  `build/production/packages/`；经验证后再复制到本仓库并提交。

## 更新子模块

在源码仓库首次检出后执行：

```bash
git submodule update --init omni-cnc
```

发布时先在本仓库提交并推送发布内容，再在源码仓库提交 `omni-cnc`
子模块引用，确保其他开发者能够获取对应提交。
