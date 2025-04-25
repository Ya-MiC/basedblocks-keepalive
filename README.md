# basedblocks-keepalive 🛡️

**持续挖矿，随时在线！** 🚀  
**Keep mining alive, uninterrupted!** 💎

本脚本为**纯前端注入**工具，专为 **basedblocks.xyz**（Base Network）设计。当挖矿过程检测到卡顿或有效哈希时，自动在“停止挖矿”🔴与“开始挖矿”🟢按钮之间切换，保障挖矿持续运行。⛏️  

> **仅用于教学公开，开发者不对任何使用行为负责。欢迎捐赠支持开发维护。** 🎁  
> - **SOL 捐赠地址：** 7CwDTxfQUmGcTmT2LCyKfuyPM7ZqfSPtaCcHG1zwfbMA 💰  
> - **BASE (ETH) 捐赠地址：** 0x38c1c29a4ee56531b6e5a688e4c147221afb7e41 🪙

---

## 🌟 Project Overview / 项目概述 🌍

**EN**: `basedblocks-keepalive` is a front-end script for **basedblocks.xyz** (Base Network). It automatically restarts mining when the process stalls or a valid hash is generated, by simulating clicks on the STOP/START MINING buttons. 🔄  
**CN**: `basedblocks-keepalive` 是专为 **basedblocks.xyz**（Base Network）开发的前端脚本，当挖矿卡顿或检测到有效哈希时，将自动在“停止挖矿”🔴和“开始挖矿”🟢按钮之间切换，确保挖矿不中断。⛏️

### 📝 Detailed Description / 详细说明 📖

- **🌐 Current Support / 当前支持**: 仅在 `basedblocks.xyz` 测试通过；🧪  
- **⚠️ Token Value Risk / 代币价值不确定**: 代币尚无稳定价值，一次 Claim 会产生额外 Gas 费用，**不建议频繁 Claim**。建议持续挂机，待价值明确后再逐笔 Claim；⏳  
- **💰 Mining Yield / 挖矿产出（截至04/25/2025）**: 默认每次可挖 **500 个代币**；平台不会回收您已挖代币，安全保留；🔐  
- **👥 Project Roles / 项目角色**: 本脚本开发者 与 `basedblocks.xyz` 平台方 **非同一主体**，仅提供自动化功能，不承担平台运营、资产管理或收益分配责任。

本项目采用 **MIT License** 📝，仅作教学演示，任何法律或道德风险请自行承担。⚖️

---

## 🛠️ 使用方法 / Usage 🖥️

### 🪟 Windows + Chrome 🎯

1. 在 Chrome 中访问 `https://basedblocks.xyz`。🌐  
2. 按 `F12` 打开 **开发者工具**，切换到 **Console** 标签页。🛠️ ( `CTRL+SHIFT+J` `inspect（检查)` 如果F12无效)
3. **点击面板空白处**（帮助定位输入行），切换到英文输入法，输入：  
   ```
   allow pasting
   ```  
   - （第一步定位输入行；第二步解除粘贴保护）🔓  
4. 按 **Enter**，然后粘贴脚本并再次按 **Enter** 执行。▶️

### 🍎 macOS + Safari 🎬

1. 打开 Safari，进入 `Safari > 设置 > 高级`，勾选 **在菜单栏中显示“开发”菜单**。⚙️  
2. 访问 `https://basedblocks.xyz`。🌐  
3. 在菜单栏选择 **开发 > 显示 JavaScript 控制台**，点击 **控制台空白处** 定位输入行。🔍  
4. 切换到英文输入法，输入：  
   ```
   allow pasting
   ```  
5. 按 **Enter**，然后粘贴脚本并再次按 **Enter** 执行。▶️

> **备注**：`allow pasting` 用于解除 DevTools 的自我 XSS 防护，在 Chrome 和 Safari 中通用。🛡️

---

## 🔧 脚本代码 / Script Code 💻

```javascript
// —— 全局状态
let lastAttempt = null;
let lastValidHashTime = 0;

// —— 拦截并扩展 console.log
(function() {
  const origLog = console.log;
  console.log = function(...args) {
    const msg = args[0];
    if (typeof msg === 'string' &&
       (msg.includes("Valid hash found:") || msg.includes("找到有效的哈希值："))) {
      console.warn("⛏️ 有效哈希检测到，触发重启...");
      lastValidHashTime = Date.now();
      restartMining();
    }
    origLog.apply(console, args);
  };
})();

// —— 获取尝试次数元素
function getAttemptDiv() {
  return Array.from(document.querySelectorAll('div.pixel-text')).find(el =>
    /attempt:/.test(el.textContent) || /尝试：/.test(el.textContent)
  );
}

// —— 获取停止/开始按钮
function getStopButton() {
  return Array.from(document.querySelectorAll('button')).find(btn =>
    btn.textContent.includes('STOP MINING') || btn.textContent.includes('停止挖矿')
  );
}
function getStartButton() {
  return Array.from(document.querySelectorAll('button')).find(btn =>
    btn.textContent.includes('START MINING') || btn.textContent.includes('开始挖矿')
  );
}

// —— 重启挖矿流程
function restartMining() {
  const stopBtn = getStopButton();
  const startBtn = getStartButton();
  if (stopBtn) stopBtn.click();
  setTimeout(() => {
    if (startBtn) startBtn.click();
  }, 1000);
}

// —— 定时检查尝试次数卡顿
setInterval(() => {
  const el = getAttemptDiv();
  if (!el) return;
  const m = el.textContent.match(/(?:attempt:|尝试：)\s*(\d+)/);
  if (!m) return;
  const currentAttempt = parseInt(m[1], 10);
  if (lastAttempt !== null && currentAttempt === lastAttempt &&
      Date.now() - lastValidHashTime > 5000) {
    console.warn("📷 尝试卡住，重启挖矿...");
    restartMining();
  }
  lastAttempt = currentAttempt;
}, 1000);
```  

---

## 📄 License & Disclaimer / 许可证与免责声明 📜

- 本项目遵循 **MIT License**。✅  
- **仅用于教学公开**，开发者不对任何使用行为负责，任何法律或道德风险请自行承担。⚖️  
- **欢迎捐赠**，支持脚本维护与更新。🙏

---

## 🇬🇧 English Version  📝 🇬🇧

**Project Name**: basedblocks-keepalive  
**Slogan**: Keep mining alive, uninterrupted!✨

**Overview**: basedblocks-keepalive is a front-end script for basedblocks.xyz (Base Network). It monitors console logs for valid hashes and page elements for attempt counts. When mining stalls or a valid hash is found, it automatically clicks STOP and START buttons to maintain continuous mining. 🔄

**Details**:

- **Support**: Tested on basedblocks.xyz only.🧪
- **Token Value Risk**: The token’s future value is uncertain; a claim incurs gas fees. Frequent claims are **not recommended**.⚠️
- **Yield (04/25/2025)**: Up to 500 tokens per session; tokens remain on-chain safely; platform does not reclaim.💰
- **Roles**: Script developer is independent from the basedblocks.xyz platform and bears no responsibility for platform operations or asset management.👥

**Usage**:

- **Chrome (Windows)**: F12 → Console → click blank → switch to EN input → `allow pasting` → Enter → paste script → Enter.🖱️
- **Safari (macOS)**: Enable Develop menu → Develop → Show JavaScript Console → click blank → EN input → `allow pasting` → Enter → paste script → Enter.🖱️

**License & Disclaimer**:

- MIT License.📜
- For educational use only; no liability for any legal or ethical risks.⚖️
- Donations welcome:💝
  - SOL: 7CwDTxfQUmGcTmT2LCyKfuyPM7ZqfSPtaCcHG1zwfbMA
  - BASE(ETH): 0x38c1c29a4ee56531b6e5a688e4c147221afb7e41

---
