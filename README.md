# MyBot

个人学习用途的 QQ 机器人项目。当前处于仓库与服务器基线整理阶段。

## 当前结构

```text
MyBot/
├── AGENTS.md          # 项目目标、安全边界和 Codex 工作规则
├── BeatBot/           # 现有 NoneBot 原型及本地虚拟环境
├── Napcat/            # NapCat/QQ 本地运行目录（不纳入 Git）
├── data/              # 本地运行数据库（不纳入 Git）
└── napcat.sh          # NapCat 本地管理脚本
```

`BeatBot/BeatBot/.env.prod`、数据库、日志、虚拟环境以及 NapCat 安装产物只保留在本机，不进入版本控制。

## Git 管理

仓库只跟踪源码、文档、示例配置和维护脚本。运行数据、凭据、日志、依赖目录及构建产物由 `.gitignore` 排除。

开始开发前请先阅读 [AGENTS.md](AGENTS.md)。

## 当前状态

- 已统一项目根目录为 `/home/admin/MyBot`
- 已保留现有 BeatBot 原型和 NapCat 运行文件
- 尚未配置生产部署或自动启动
- 尚未提交任何真实 Token、Cookie、密码或数据库

## 下一步
