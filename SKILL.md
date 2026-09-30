---
name: wavekol-live-monitor-extension
description: WaveKOL 小浪花直播中控实时监控 Chrome 插件的安装、验证、打包、发布和使用指导。Use when the user asks to install, update, debug, package, publish, distribute, explain, or use the “小浪花直播中控监控” / WaveKOL live monitor Chrome extension for 视频号 dashboardV4 real-time monitoring, red/yellow/green operations decisions, heat traffic share, live trend monitoring, or copying AI review prompts.
---

# 小浪花直播中控监控插件

用于维护和使用独立 Chrome MV3 插件：`~/Projects/wavekol-live-monitor-extension`。插件读取用户已打开、已登录的视频号 `dashboardV4` 数据大屏，侧边栏每 5 秒显示红黄绿运营建议。

## 快速流程

1. 先读 `references/live-monitor-plugin.md`，确认项目路径、安全边界、字段口径和验证命令。
2. 根据用户意图选择：
   - 安装 / 使用：给 Chrome 手动加载目录步骤，不自动改浏览器扩展状态。
   - 更新 / 修复：修改项目代码，跑 `npm test`、语法检查、`npm run zip`、`unzip -t`。
   - 发布 / 发同事：生成 zip；只有用户明确要求时才上传飞书云盘或设置公开权限。
   - 排错：先确认当前页是 `channels.weixin.qq.com/platform/statistic/dashboardV4`，再看侧边栏「读取状态」。
3. 输出时用中文，称呼用户，说清楚：结果、改动、验证、风险、下一步。

## 必守边界

- 只读当前已登录页面的结构化 runtime store。
- 不代登录、不绕过视频号后台权限。
- 不读取或输出 cookie、authorization、token、localStorage、sessionStorage、密码或浏览器配置。
- 不自动点击后台按钮，不自动投流，不自动发飞书。
- 不添加 `<all_urls>`、`debugger`、`webRequest`、`cookies` 权限，除非用户单独确认并说明风险。

## 常用命令

```bash
cd ~/Projects/wavekol-live-monitor-extension
npm test
node --check service_worker.js
node --check src/page-reader.js
node --check src/live-monitor-core.js
node --check src/sidepanel.js
npm run zip
unzip -t release/wavekol-live-monitor-extension.zip
```

安装给运营使用：

1. Chrome 打开 `chrome://extensions`。
2. 打开「开发者模式」。
3. 点「加载已解压的扩展程序」。
4. 选择 `~/Projects/wavekol-live-monitor-extension`。
5. 打开视频号数据大屏后点插件图标。

## 排错顺序

1. 页面不对：提示用户打开 dashboardV4 主页面，不要用截图估算。
2. 读不到 store：刷新主页面，等待 iframe 加载；不要读 cookie 或 storage。
3. 指标异常：用 `wechat-channels-data-reader` 的真实 raw/CSV 样例交叉验证字段口径。
4. 插件不打开：检查 `manifest.json`、service worker 控制台、Chrome 扩展错误页。
5. 打包失败：先跑语法检查，再重新 `npm run zip`。

## 版本发布

- 本地 release zip：`release/wavekol-live-monitor-extension.zip`。
- 发布给团队前必须确认：
  - `npm test` 通过。
  - `unzip -t` 通过。
  - `manifest.json` 只有 `https://channels.weixin.qq.com/*` 站点权限。
  - 扫描无敏感读取：`document.cookie`、`localStorage`、`sessionStorage`、`authorization`、`fetch(`。
- 上传飞书云盘或设为互联网可下载前，必须有用户明确要求。
