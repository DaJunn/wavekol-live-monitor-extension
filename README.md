# wavekol-live-monitor-extension

**直播中控实时监控插件** —— 视频号 dashboardV4 实时监控，红黄绿决策、热度流量占比、AI 复盘提示词

让 AI agent（DSH / Codex / Claude Code 等）使用。

## 解决什么问题

之前中控盯盘全靠人眼盯数据大屏，流量掉了、热度被抢了反应不过来。

现在用这个 Chrome 插件实时监控 dashboardV4：红黄绿三色决策提示、热度流量占比、直播趋势，还能一键复制 AI 复盘提示词。

## 前置依赖

1. **kimi-webbridge daemon** 在跑：
   ```bash
   ~/.kimi-webbridge/bin/kimi-webbridge status   # 要 running:true + extension_connected:true
   ```
   没装：`curl -fsSL https://cdn.kimi.com/webbridge/install.sh | bash`
2. **Chrome 已登录对应后台**。本系列只复用你自己已打开的标签页，**不代登录**。

## 安装

```bash
git clone https://github.com/DaJunn/wavekol-live-monitor-extension.git \
  ~/.agents/skills/wavekol-live-monitor-extension
```

## 触发方式

对 agent 说：「装下中控监控插件」「打包发布中控监控」「中控监控怎么用」「改一下监控阈值」。

插件为**独立 Chrome MV3 扩展**。**开发者**注意：源码在 `~/Projects/wavekol-live-monitor-extension`（各人路径不同，按自己实际路径调整）。


## 说明

- 插件权限最小化：不申请 `<all_urls>`、`debugger`、`webRequest`、`cookies`
- 加权限属高风险动作，需单独确认

## 相关

- 完整技能合集见飞书文档《AI减负视频号运营技能合集》
- 更多 skill：https://github.com/DaJunn
