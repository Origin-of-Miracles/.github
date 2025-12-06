# Origin of Miracles | Project Development Whitepaper

> **Note:** This document outlines the technical architecture and development standards for the "Origin of Miracles" project ecosystem.

- **Version:** 1.0.0-Draft
- **Codename:** Project Kivotos
- **Target Platform:** Minecraft Java Edition 1.20.1 (Forge)
  - *当前仅针对 Forge 1.20.1 进行开发，后续可能会根据社区呼声支持 NeoForge 版本。*
- **License:** [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)

---

## 0. Executive Summary | 执行摘要

**Origin of Miracles** 是一个非官方的粉丝项目 (Fan-made Project)，旨在将《蔚蓝档案》(Blue Archive) 的沉浸式体验带入 Minecraft 世界。

### 核心亮点

-  **革命性 UI 体验**：通过 Webview 技术，将原作精美的 UI 设计完整复刻到游戏中，告别传统方块 GUI。
-  **MomoTalk 社交系统**：与学生进行真实的对话互动，体验养成乐趣。
-  **沉浸式抽卡**：还原经典的"蓝天白云"招募动画，仪式感拉满。
-  **TacZ 枪械整合**：高度还原各学院学生的专属武器与战斗体验。

### 项目定位

本项目完全 **非商业化**，采用 **CC BY-NC-SA 4.0** 许可证，严禁任何形式的商业使用。我们是热爱《蔚蓝档案》的开发者社区，致力于为 Sensei 们创造独特的方块世界冒险体验。

> ** 免责声明：** 本项目与 NEXON Games 或 Yostar 无任何官方关联。所有《蔚蓝档案》相关素材的版权归原作者所有。

---

## 1. Project Vision | 项目愿景

本项目旨在打破 Minecraft 传统模组开发的限制，通过引入 **Modern Web Technologies (现代前端技术)** ，构建一个具有高沉浸感的《蔚蓝档案》(Blue Archive) 主题生态。

核心理念是 **“技术降维打击”**：利用 Webview 取代原版 GUI，利用 TacZ 取代原版战斗，将原作的“UI 美学”与“养成体验”在方块世界中完美复刻。

---

## 2. Technical Architecture | 技术架构

本项目采用 **前后端分离 (Decoupled Architecture)** 的设计模式，将游戏逻辑（Java）与界面表现（React/TS）完全剥离。

### 2.1 Component Topology | 核心组件拓扑

| Component | Codename | Tech Stack | Responsibility |
| :--- | :--- | :--- | :--- |
| **Miracle Bridge** | `Client` | **Java (Forge 1.20.1)** | **[容器层 / 底座]** <br> 1. 负责加载 Chromium 内核 (MCEF)。<br> 2. 监听 MC 游戏事件 (按键、击杀、交互)。<br> 3. 提供 JS <-> Java 的双向通信管道。 |
| **Shittim OS** | `Webview` | **React + ShadcnUI** | **[表现层 / 界面]** <br> 1. 运行于游戏内浏览器的单页应用 (SPA)。<br> 2. 负责渲染 MomoTalk、学生档案、抽卡动画。<br> 3. 处理复杂的 UI 交互逻辑。 |
| **Server Pack** | `World` | **TacZ + Scripts** | **[内容层 / 玩法]** <br> 1. 枪械数据平衡、总力战 Boss 机制（待定）。<br> 2. 经济系统数值策划。<br> 3. 任务脚本。 |

### 2.2 Data Flow | 通信数据流

* **Java to Web (下行 / Downstream):**
    * **Trigger:** 玩家按下 `M` 键 -> Mod 捕获事件 -> 调用 `browser.executeJavaScript("openShittimChest()")` -> Web 端渲染主界面。
    * **State Sync:** 玩家血量变化/获得物品 -> Mod 推送 JSON 数据 -> Web 端 React Store 更新 -> UI 实时重绘。
* **Web to Java (上行 / Upstream):**
    * **Action:** 玩家在 Web 界面点击“制造物品” -> JS 发起 `cefQuery` -> Mod 接收请求 -> 检查材料并执行 `/give` 指令。

---

## 3. Features & Effects | 功能模块与实现效果

### 3.1 Shittim Chest System (什亭之箱 OS)
* **Visual Effect:** 全屏、透明背景的高科技 HUD，非传统的方块 GUI。
* **Interactions:**
    * **MomoTalk:** 仿真的社交软件界面，玩家可浏览学生发来的消息，点击选项回复会增加好感度。
    * **Live2D / Motion:** 在界面左侧展示看板娘（L2D 或动态 Sprite），根据玩家点击触发不同的动作和语音。

### 3.2 Immersive Gacha (沉浸式抽卡)
* **Visual Effect:** 解决 MC 原版抽奖箱毫无仪式感的问题。Webview 全屏播放经典的“蓝天白云”或“彩虹光”签收动画。
* **Logic:** 动画结束后，通过回调函数在 MC 玩家背包中生成对应的“学生专武”或“角色召唤卡”。
* **Pity System:** 前端记录抽卡次数，视觉上展示“招募点数”进度条。

### 3.3 Tactics & Total Assault (战术与总力战)
* **TacZ Integration:**
    * 还原各学院学生的专属武器，通过 Mod 修改其弹道、后坐力和特效。
    * 使用 Webview 绘制动态准星或弹药计数器（可选），替代原版 HUD。
* **Boss Visualization:**
    * 在屏幕上方渲染多阶段 Boss 血条（Webview 实现），带有精美的边框和阶段转换动画。
    * 战斗结算时，弹出类似原作的“通关评价”结算板，显示 MVP 和伤害数据。

### 3.4 Economy & Progression (经济与养成)
* **Dual Currency:**
    * **Credits (信用点):** 游戏内通用货币（可交易）。
    * **Pyroxene (青辉石):** 只能通过 Web 任务面板领取，用于抽卡。
* **Cafe System:**
    * 玩家拥有独立空间，通过 Webview 界面管理家具摆放。

---

## 4. Implementation Strategy | 关键技术实现

### 4.1 Rendering Core (渲染核心)
* **Dependency:** **MCEF (ds58 Fork)**，专为 Minecraft 1.20.1+ Forge 优化。
* **Mode:** 开启 **OSR (Off-Screen Rendering)** 模式，配合 CSS `background: transparent`，实现非矩形、不遮挡视野的 UI 叠加层。

### 4.2 Optimization (性能优化)
* **Loading:** Web 资源采用**本地渲染**方案。前端项目随 Mod 一同分发，通过本地开发服务器（类似 `pnpm dev`）启动，由 Webview 加载 `localhost` 地址。
    * **优点：** 无需联网即可运行，加载速度快，隐私性更好。
    * **构建方式：** 玩家安装 Mod 后，前端资源会被自动解压到本地目录，Mod 启动时自动拉起本地服务。
* **Memory:** 仅在 GUI 打开时活跃渲染，关闭 GUI 时暂停 JS 执行或卸载 Webview 进程，防止内存泄漏。

---

## 5. Licensing & EULA | 许可证与法律声明

本项目采取极其严格的**非商业化策略**，以保护开源成果不被滥用。

### 5.1 License Selection
* **Protocol:** **[CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)** (Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International)
* **Constraints:**
    1.  **Attribution (署名):** 任何使用必须保留 "Origin of Miracles" 标识，并注明原作者。
    2.  **Non-Commercial (非商用):** **严禁**用于任何营利性用途，包括但不限于：
        - 拥有付费白名单的服务器
        - 售卖数值道具、皮肤、特权的服务器
        - 任何形式的 VIP / 会员制度
        - 将本项目或其衍生品用于商业推广
        - **仅允许纯公益服务器（完全无偿、无任何变相收费）使用**
    3.  **Share-Alike (相同方式共享):** 任何基于本项目的 Fork、修改版或衍生作品，**必须**沿用 CC BY-NC-SA 4.0 协议。

### 5.2 Defense Mechanisms (防御机制)
* **Frontend Lock:** Webview 前端代码中植入版权标识，若检测到非授权域名的请求，显示“盗版警告”。
* **Startup Notice:** Mod 首次加载时弹出 EULA 确认框，明确告知玩家该 Mod 禁止商用。

---

## 6. Roadmap | 开发路线图

- [ ] **Phase 1: Bridge Construction (基建)**
    - [ ] 搭建 Forge 1.20.1 开发环境
    - [ ] 引入 MCEF 依赖，实现 `Hello World` 浏览器渲染
    - [ ] 建立 JS <-> Java 基础通信管道
- [ ] **Phase 2: OS Implementation (系统)**
    - [ ] 使用 React + ShadcnUI 搭建 "Shittim OS" 基础框架
    - [ ] 实现 MomoTalk 静态界面与列表滚动
- [ ] **Phase 3: Content Injection (内容)**
    - [ ] 对接 TacZ 资产，制作第一把“专武”
    - [ ] 实现抽卡动画与物品发放的逻辑闭环
- [ ] **Phase 4: Optimization (优化)**
    - [ ] 处理窗口缩放适配 (Responsive Design)
    - [ ] 完善 EULA 弹窗和防商用标识