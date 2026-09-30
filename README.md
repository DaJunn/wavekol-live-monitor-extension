# 小浪花直播中控监控插件 Skill

帮助 AI 安装、排查和维护视频号实时监控插件，查看趋势、加热占比与红黄绿运营提示。

**本仓库只有 skill 说明，不包含 Chrome 插件源码或安装包。** 使用前需另备插件项目，默认位置为 `~/Projects/wavekol-live-monitor-extension`；路径不同可直接告诉 AI。本机核对的 0.1.1 版本正常运行不依赖 Kimi WebBridge，其他版本以实际项目为准。

## 安装 Skill

```bash
git clone https://github.com/DaJunn/wavekol-live-monitor-extension.git \
  ~/.agents/skills/wavekol-live-monitor-extension
```

## 怎么用

向 AI 提供实际插件目录和需求，例如：

- 「帮我安装中控监控插件，源码在这个目录。」
- 「数据大屏已打开，侧边栏显示读取失败，帮我排查。」
- 「调整在线下滑提示并验证，给我本地安装包。」

安装还需要 Chrome 和已登录的视频号数据大屏；修改、测试与打包需要实际插件项目及其开发环境。AI 会区分本地检查通过、页面验证通过和安装包已交付。

## 产物与边界

按需求交付安装步骤、排错结果、修改后的源码或 zip。监控规则用于辅助判断，不自动投流或操作商品；上传、公开分享需要用户明确要求。

操作流程见 [SKILL.md](SKILL.md)，字段与检查命令见 [参考](references/live-monitor-plugin.md)。
