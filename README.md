<div align="center">

# 🎬 ssc

## 22 条硬规则，锁住分镜不漂移、台词不丢
## 22 hard rules keep storyboard blocking, dialogue and references locked

📮 **商务合作 / Business：wow.vip@foxmail.com**
🎬 **抖音 / Douyin：[v.douyin.com/07EjIDGVErY](https://v.douyin.com/07EjIDGVErY/)**

</div>

---

## 🇨🇳 中文

ssc 是影视前期总控链路技能：从剧本或制作计划出发，按固定顺序串联生成影视工业体系数据中心、故事板概要、场景调度表、空间真值、视觉资产、舞台定位图、黑白 3x3 分镜和视频提示词列表。它自己不生图也不生视频，只规定阶段顺序、输入真值、引用边界、画布交付物和 QC 判定。每个 10-15 秒视频任务都有唯一编号，并强制建立「任务编号 → 舞台定位图 → 黑白 3x3 分镜图 → 视频提示词段」的一一映射。链路一旦冻结就不允许压缩改写，坐标、人物数量、对白和参考图顺序都有独立校验，出错会给出 E01-E15 的具体错误码。用户说「全自动」时，它还能自动选择推荐项并连续推进。

### ✨ 能做什么

- 固定链路 A0→A1→A2→A3→A4→B1-B5→B6-B8，任何分镜任务必须先完成 STEP A
- 为每个 10-15 秒视频任务建立唯一编号与四段一一映射
- 舞台定位图用方形色块标人物、圆形标道具，颜色顺序全线冻结继承
- 坐标强制为带 X/Y/Z 键名的对象，禁止裸数字、无键数组和散文位置
- Panel 9 写入 continuity_handoff，成为下一任务 Panel 1 的 continuity_start
- 对白保真锁加引用有效性预检，派单前逐句核对源台词与参考图顺序

### 📦 包含的技能

- `ssc` —— 见 [`ssc/SKILL.md`](ssc/SKILL.md)

### 🚀 安装

```bash
git clone https://github.com/yzsp-studio/ssc.git
cp -r ssc/* "$DSH_HOME/skills/"
```

> 技能是**纯文本说明**（`SKILL.md` + `references/`），零依赖、零 API key，用你当前 agent 的会话额度。

---

## 🇬🇧 English

ssc is a pre-production control-chain skill for film and series work. Starting from a script or production plan, it chains the industrial production database, storyboard synopsis, scene scheduling table, spatial ground truth, visual assets, stage blocking maps, black-and-white 3x3 storyboards and the video prompt list in a fixed order. It never generates images or video itself — it only governs stage order, input ground truth, reference boundaries, canvas deliverables and QC decisions. Every 10-15 second video task gets a unique ID and a strict one-to-one mapping of task ID to blocking map to 3x3 storyboard to prompt segment. Once a stage is frozen it cannot be compressed or rewritten, and coordinates, character counts, dialogue and reference ordering are each independently validated, failing with a specific E01-E15 error code. Say the word for full-auto and it picks every recommended option and keeps going.

### ✨ What it does

- Fixed chain A0 to A1 to A2 to A3 to A4 to B1-B5 to B6-B8; any storyboard work must finish STEP A first
- Gives every 10-15 second video task a unique ID and a one-to-one four-stage mapping
- Stage blocking maps mark characters with squares and props with circles, with color order frozen chain-wide
- Coordinates must be keyed X/Y/Z objects — no bare numbers, keyless arrays or prose positions
- Panel 9 writes a continuity_handoff that becomes Panel 1's continuity_start in the next task
- Dialogue preservation lock plus reference-validity preflight verify every source line and image order before dispatch

### 📦 Skills included

- `ssc` — [`ssc/SKILL.md`](ssc/SKILL.md)

### 🚀 Install

```bash
git clone https://github.com/yzsp-studio/ssc.git
cp -r ssc/* "$DSH_HOME/skills/"
```

> Skills are **plain-text instructions** (`SKILL.md` + `references/`). Zero dependencies, no API keys — they run on your agent's existing session quota.

---

## 📮 联系 / Contact

| | |
|---|---|
| 🎬 **抖音 / Douyin** | [https://v.douyin.com/07EjIDGVErY/](https://v.douyin.com/07EjIDGVErY/) |
| 📮 **商务合作 / Business** | **wow.vip@foxmail.com** |
| 🐙 **GitHub** | [https://github.com/yzsp-ai](https://github.com/yzsp-ai) |
| 💖 **打赏 / Support** | [afdian.com/a/yzsp-ai](https://afdian.com/a/yzsp-ai) |

## 📄 License

[MIT](LICENSE) · © 2026 [yzsp-ai](https://github.com/yzsp-ai)
