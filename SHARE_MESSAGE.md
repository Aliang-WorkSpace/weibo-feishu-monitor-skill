把这个目录里的 `weibo-feishu-monitor/` 放到你的 `~/.codex/skills/` 下面即可：

```bash
mkdir -p ~/.codex/skills
cp -R weibo-feishu-monitor ~/.codex/skills/
```

放好后重开 Codex 或开一个新会话。

这份 skill 负责的是“微博监控并发送飞书卡片”的工作流说明，不自带生产代码、微博登录态、飞书密钥和定时任务配置。你需要再按下面文档映射到你自己的环境：

- `weibo-feishu-monitor/SKILL.md`
- `weibo-feishu-monitor/references/distribution.md`
- `weibo-feishu-monitor/references/commands.md`
