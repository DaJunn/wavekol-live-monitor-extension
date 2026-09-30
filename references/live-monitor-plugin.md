# 小浪花直播中控监控插件参考

## 项目

- 项目目录：`~/Projects/wavekol-live-monitor-extension`
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
- 只取 `step === "60"` 且无维度序列。

成交：

- 成交金额：`ecConversionDashboardEcData.ecTread.gmvTread["60"]`，页面原值是分，展示时转元。
- 订单数：`payPvTread["60"]`
- 买家数：`payUvTread["60"]`

加热：

- 加热看播人次：`ecConversionDashboardData.overview.promotionCumulativeWatchPv`
- 总看播人次：`ecConversionDashboardData.overview.cumulativeWatchPv`
- 加热占比：`promotionCumulativeWatchPv / cumulativeWatchPv`
- 分母必须用 PV，不和 `cumulativeWatchUv` 混用。

## 决策规则

- 绿：近 5 分钟进房率 ≥ 25%，且在线持平或上升。
- 黄：平台给量但承接一般，近 5 分钟进房率 15%-25%。
- 红：
  - 连续 30 秒读不到 dashboardV4 store。
  - 高曝光窗口进房率 < 15% 持续 3 分钟。
  - 当前在线较近 10 分钟峰值下滑 ≥ 25%。
  - 开播 15 分钟后在线 ≥ 50 且成交仍为 0。
  - 加热占比 ≥ 20% 且近 5 分钟进房率 < 15%。

## 验证

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

README 中出现安全说明词不算失败；源码或 manifest 出现敏感权限/读取才需要处理。
