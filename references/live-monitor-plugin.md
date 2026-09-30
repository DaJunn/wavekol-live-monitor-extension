# 小浪花直播中控监控插件参考

## 项目

- 外部项目目录：`~/Projects/wavekol-live-monitor-extension`（不包含在本 skill 仓库；使用前核对实际路径）
- 插件类型：Chrome Manifest V3 side panel
- 安装包：`release/wavekol-live-monitor-extension.zip`
- 主要文件：
  - `manifest.json`
  - `service_worker.js`
  - `sidepanel.html`
  - `src/page-reader.js`
  - `src/live-monitor-core.js`
  - `src/sidepanel.js`
  - `src/styles.css`
  - `tests/live-monitor-core.test.mjs`

## 数据口径

读取路径：当前 dashboardV4 页面中的同源 `iframe[name="statistic"]`，取 `iframe.contentWindow._store.statisticStore.liveDataDashboardV4`。

分钟趋势：

- 曝光：`ecConversionDashboardData.trendingTraffic.impressionUv`
- 进房：`ecConversionDashboardData.trendingTraffic.newWatchUv`
- 在线：`ecConversionDashboardData.trendingTraffic.onlineWatchUv`
- 优先取 `step === "60"` 且无维度的序列；当前实现会回退到其他 60 秒序列，交付时需核对实际口径。

成交：

- 成交金额：`ecConversionDashboardEcData.ecTread.gmvTread["60"]`，页面原值是分，展示时转元。
- 订单数：`payPvTread["60"]`
- 买家数：`payUvTread["60"]`

加热：

- 加热看播人次：`ecConversionDashboardData.overview.promotionCumulativeWatchPv`
- 总看播人次：`ecConversionDashboardData.overview.cumulativeWatchPv`
- 加热占比：`promotionCumulativeWatchPv / cumulativeWatchPv`
- 分母必须用 PV，不和 `cumulativeWatchUv` 混用。

## 决策规则的核对位置

以下对应本机外部项目的 0.1.1 版本，不保证其他安装版本一致。完整条件和优先级以实际 `src/live-monitor-core.js` 的 `buildPacing`、`buildDecisionCards`、`evaluateDecision` 为准，调整前阅读对应实现。

- 绿：近 5 分钟进房率 ≥ 25%，且在线持平或上升。
- 黄：近 5 分钟平均每分钟曝光 ≥ 200，进房率 ≥ 15% 且 < 25%，或进入转化提醒窗口。
- 红：
  - 连续 30 秒读不到 dashboardV4 store。
  - 最近 3 个分钟点平均曝光 ≥ 200 且进房率 < 15%。
  - 当前在线较近 10 分钟峰值下滑 ≥ 25%。
  - `buildPacing` 判定转化窗口为红色且成交仍为 0；综合开播时长、当前在线与近期峰值，不使用单一的“15 分钟 / 50 在线”条件。
  - 加热占比 ≥ 20% 且近 5 分钟进房率 < 15%。

## 验证

先读取实际项目 `package.json`。以下命令适用于含对应文件与 scripts 的项目；`npm run zip` 会替换同名 release zip，需保留旧包时先另存副本。

```bash
cd ~/Projects/wavekol-live-monitor-extension
npm test
node --check service_worker.js
node --check src/page-reader.js
node --check src/live-monitor-core.js
node --check src/sidepanel.js
python3 -m json.tool manifest.json >/dev/null
npm run zip
unzip -t release/wavekol-live-monitor-extension.zip
```

安全扫描：

```bash
rg -n "<all_urls>|chrome\\.cookies|localStorage|sessionStorage|document\\.cookie|authorization|fetch\\(|XMLHttpRequest|chrome\\.downloads|chrome\\.webRequest" .
```

README 中出现安全说明词不算失败；源码或 manifest 命中后需判断实际用途。源码测试、语法检查和 zip 检查不能替代在真实页面验证。
