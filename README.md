# lieshou-cloud · 猎手云开源版（演示项目）

> **开源版全栈演示项目**：组合开源组件（后端底座 + 前端共享 + 4 端应用）+ 部署编排，一键起全栈，可直接部署体验完整数字化平台。
> 组件独立演进，交付包只做组合与装配（与客户交付包同构的 superproject 模式）。
> 代码全部开源（Apache-2.0），可 fork 自部署、二次开发。

<p align="center">
  <img src="https://img.shields.io/badge/Java-21-orange" alt="Java 21"/>
  <img src="https://img.shields.io/badge/React-19-61dafb" alt="React 19"/>
  <img src="https://img.shields.io/badge/License-Apache--2.0-brightgreen" alt="Apache-2.0"/>
</p>

## 组合结构

```
lieshou-cloud（猎手云开源版 · 演示项目）
├── lieshou-cloud-{gateway,user,admin,auth,approval}-services/  后端服务(2026-09 framework 战略:submodule 挂载 lieshou-framework,根聚合 pom)
├── lieshou-contract-{api,config,types}/ · lieshou-ui/  前端共享层(契约层 contract-* + ui)
├── lieshou-admin-web/     B 端管理后台
├── lieshou-desktop/       桌面端
├── lieshou-mobile/        移动端
├── lieshou-mini-program/  微信小程序
└── deploy/                     一键全栈编排(infra + core 后端 + 前端 nginx)
```

## 快速开始

```bash
# 1. 初始化全部组件
git submodule update --init --recursive

# 2. 构建后端(5 服务仓,交付包统一构建入口;framework submodule 经根聚合 pom 一次构建)
for svc in gateway user admin auth approval; do
  (cd lieshou-cloud-$svc-services && JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64 mvn -B -ntp -DskipTests -f pom.xml package)
done

# 3. 构建前端(admin-web 演示入口)
cd lieshou-admin-web
pnpm install && pnpm build

# 4. 一键起全栈(infra + 后端 + 前端)
docker compose -f deploy/docker-compose.yml up -d
```

访问 `http://localhost:8080`(前端)→ gateway `:9000`(API)。

## 组件清单与各自 README

| 组件 | 定位 | 说明 |
| --- | --- | --- |
| [lieshou-framework](https://github.com/HUNTERCAT-DIGITAL/lieshou-framework) | 核心框架(业务唯一源) | 2026-09 起经各服务仓 `framework/` submodule 挂载,jwt/common/auth 模块 |
| [lieshou-contract-api](lieshou-contract-api/README.md) / [config](lieshou-contract-config/README.md) / [types](lieshou-contract-types/README.md) / [ui](lieshou-ui/README.md) | 前端共享 | 传输/配置/契约/UI(2026-09 契约层命名 contract-*) |
| lieshou-cloud-{gateway,user,admin,auth,approval}-services | 后端服务 | 每服务一仓(根 pom 聚合 framework + services) |
| [lieshou-admin-web](lieshou-admin-web/README.md) | B 端后台 | 演示/通用页面 |
| [lieshou-desktop](lieshou-desktop/README.md) | 桌面端 | Tauri 2 |
| [lieshou-mobile](lieshou-mobile/README.md) | 移动端 | Expo |
| [lieshou-mini-program](lieshou-mini-program/README.md) | 小程序 | Taro |

## 升级组件(pin 策略)

```bash
# 升级全部组件到各自 main
for s in lieshou-cloud-gateway-services lieshou-cloud-user-services lieshou-cloud-admin-services lieshou-cloud-auth-services lieshou-cloud-approval-services lieshou-contract-api lieshou-contract-config lieshou-contract-types lieshou-ui lieshou-admin-web lieshou-desktop lieshou-mobile lieshou-mini-program; do
  git -C $s fetch origin main && git -C $s checkout origin/main && git add $s
done
git commit -m "chore: bump components → latest"
```

组件改动 → 各组件仓独立 PR → 交付包 bump pin(与客户仓 bump-upstream 同构)。

## License

Apache-2.0。
