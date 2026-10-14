---
name: ssc
description: |
  从剧本或制作计划串联生成影视工业体系数据中心、故事板概要、场景调度表、空间真值、视觉资产、舞台定位图、黑白 3x3 分镜和视频提示词的前期总控技能。适用于定位分镜链、舞台定位到分镜、先资产再站位、先参考资产再舞台定位图、舞台定位图转分镜、先站位再分镜、多人场景定位、多人分镜位置漂移、blocking to storyboard、舞台定位图 黑白分镜图、故事板概要模板源、人物三庭五眼、人物参考图信息提取、Panel 视频提示词、视频提示词列表.md、视频任务切分计划、全自动制作等任务。不直接生图 / 生视频，而是规定阶段顺序、真值来源、引用映射、画布交付物和 QC。用户要求“全自动”时，允许本 Skill 在全程保持 ssc 制作链路的前提下自动选择推荐项、自动加载相关外部 Skill 能力并连续推进。
trigger-words: [定位分镜链, 舞台定位到分镜, 先资产再站位, 先参考资产再舞台定位图, 舞台定位图转分镜, 先站位再分镜, 先定站位再画分镜, 先角色站位图再故事板, 故事板概要模板源, 人物三庭五眼, 三庭五眼结构, 人物面部结构, 人物参考图信息提取, 多人场景定位, 多人分镜位置漂移, 先blocking再storyboard, blocking to storyboard, 舞台定位图 黑白分镜图, 视频任务切分计划, 视频提示词列表, 全自动]
allowed-tools: [hub_read, hub_write, hub_edit, hub_analyse_media, hub_canvas_get_node, hub_canvas_search_nodes, hub_canvas_write_text_node, hub_canvas_write_table_node, hub_canvas_write_media_node, question, skill]
author: yzsp-ai
repository: https://github.com/yzsp-ai
---


# ssc — 快慢机影视工业全链路制作

本 Skill 是影视前期总控链路，不直接生图 / 生视频。它只规定阶段顺序、输入真值、引用边界、画布交付物、视频任务切分和 QC。具体图片 / 视频仍由主流程按模型路由派单。

## 0. References 硬读取总则

`references/` 不是可选资料库，而是本 Skill 的执行体。任何阶段开始前，必须按下表真实调用 `hub_read` 读取对应 reference。未读取、读取失败、路径不存在、内容为空，均触发 `[E07: Required Reference Missing]`，必须暂停链路并通过 `question` 弹窗让用户选择修复、暂停或回退。

**全局第一步永远读取：**

```text
references/00-强制读取索引.md
references/01-全局硬规则.md
```

每次阶段派单必须在任务包里写入 `[Required_References_Read]`，记录已读取的 reference 文件名。不能用“之前读过”“上下文里有”“类似文件读过”替代。

## 1. 链路顺序

```text
STEP A：前置真值与资产
A0. 生成 / 更新：影视工业体系数据中心 — [集数][段落编号][场景名]
A1. 从 A0 抽取 / 更新：故事板概要 — [集数][段落编号][场景名]
A2. 从 A0/A1 抽取 / 更新：场景调度表 — [集数][段落编号][场景名] + 空间真值调用中心 — [集数][段落编号][场景名]
A3. 基于 A0/A1/A2 生成允许范围内视觉资产
A4. 基于 A0 + 双 MD + 空间真值 + 当前任务场景参考图生成舞台定位图
A5. STEP A 出口检查

STEP B：分镜与视频前期
B1. 重新读取输入并建立引用映射
B2-B5. 基于当前 10-15 秒视频任务的舞台定位图，逐任务生成对应的唯一黑白 3x3 分镜图并预审 QC
B6. 整理 / 更新 视频提示词列表.md，建立视频引用映射与 9 Panel 执行链
B7. 通过 question 弹窗确认增强选择、视频提示词列表.md 基础内容与同轮视频生成授权
B8. 视频派单前完成引用重索引、9 张上限处理、最终 image_paths 顺序锁定和视频生成
```

任何故事板 / 黑白宫格 / Panel 视频提示词任务，必须先完成 STEP A。不得跳过双 MD、视觉资产或当前制作动作所绑定 `video_task_id` 的舞台定位图直接进入 STEP B。

## 2. 阶段必读表

| 阶段 | 必须读取 |
|---|---|
| 全局启动 / 恢复 / 重做 | `references/00-强制读取索引.md`、`references/01-全局硬规则.md` |
| A0 影视工业体系数据中心 | `references/00-强制读取索引.md`、`references/01-全局硬规则.md`、`references/02-A0影视工业体系数据中心.md` |
| A1 故事板概要 | `references/00-强制读取索引.md`、`references/01-全局硬规则.md`、`references/02-A0影视工业体系数据中心.md`、`references/03-A1-A2双MD与空间真值.md` |
| A2 场景调度表 / 空间真值 | `references/00-强制读取索引.md`、`references/01-全局硬规则.md`、`references/02-A0影视工业体系数据中心.md`、`references/03-A1-A2双MD与空间真值.md` |
| A3 视觉资产 | `references/00-强制读取索引.md`、`references/01-全局硬规则.md`、`references/02-A0影视工业体系数据中心.md`、`references/03-A1-A2双MD与空间真值.md`、`references/04-A3视觉资产.md` |
| A4 舞台定位图 | `references/00-强制读取索引.md`、`references/01-全局硬规则.md`、`references/02-A0影视工业体系数据中心.md`、`references/03-A1-A2双MD与空间真值.md`、`references/04-A3视觉资产.md`、`references/05-A4舞台定位图.md` |
| A5 STEP A 出口检查 | `references/00-强制读取索引.md`、`references/01-全局硬规则.md`、`references/02-A0影视工业体系数据中心.md`、`references/03-A1-A2双MD与空间真值.md`、`references/04-A3视觉资产.md`、`references/05-A4舞台定位图.md`、`references/08-QC与输出验收.md` |
| B1-B5 黑白 3x3 分镜 | `references/00-强制读取索引.md`、`references/01-全局硬规则.md`、`references/02-A0影视工业体系数据中心.md`、`references/03-A1-A2双MD与空间真值.md`、`references/04-A3视觉资产.md`、`references/05-A4舞台定位图.md`、`references/06-B1-B5黑白分镜.md`、`references/08-QC与输出验收.md` |
| B6-B8 视频提示词 / 视频生成前 | `references/00-强制读取索引.md`、`references/01-全局硬规则.md`、`references/02-A0影视工业体系数据中心.md`、`references/03-A1-A2双MD与空间真值.md`、`references/04-A3视觉资产.md`、`references/05-A4舞台定位图.md`、`references/06-B1-B5黑白分镜.md`、`references/07-B6-B8视频提示词与引用.md`、`references/08-QC与输出验收.md` |
| 任一 QC / 输出验收 | `references/00-强制读取索引.md`、`references/01-全局硬规则.md`、`references/08-QC与输出验收.md` |

## 3. 不可降级全局硬规则

这些规则即使拆入 references，也必须在主 Skill 中保留为不可降级约束：

1. **question 授权 / 全自动模式**：故事板概要、场景调度表、视觉资产、舞台定位图、黑白分镜、Panel 视频提示词、视频生成、修复、重做、追加、跳步恢复，都是独立制作动作。每次动作开始前必须调用 `question` 工具；一次弹窗只授权当前动作，动作完成即失效。B7 例外：视频前单次事务弹窗可同时完成增强选择、确认 `视频提示词列表.md` 基础内容并授权同轮视频生成。用户原话包含“全自动”时，视为已授权全程自动模式：后续常规制作动作不再逐次弹窗，所有选择项按本 Skill 推荐项自动选择，并连续推进；但 `[E06: Reference Resolve Failure]`、`[E07: Required Reference Missing]`、模型不可用 / 需换模型、连续 3 次 FAIL、缺少剧本 / 关键资产、法律或安全风险仍必须通过 `question` 高危弹窗请求人工决策。
2. **核心文档命名原子化**：核心文档必须使用 `影视工业体系数据中心 — [集数][段落编号][场景名]`、`故事板概要 — [集数][段落编号][场景名]`、`场景调度表 — [集数][段落编号][场景名]`、`空间真值调用中心 — [集数][段落编号][场景名]`。禁止读取或创建旧通用名，如 `故事板概要`、`场景调度表`、`空间真值调用中心.md`。
3. **A0 优先**：A0 是所有下游母体数据中心。A1/A2/A3/A4/B1-B8 之前必须先创建或更新 A0。缺失 A0，触发 `[E01: Missing Script/Assets]`。
4. **视频任务切分计划**：A0 必须输出 `视频任务切分计划`。每个 10-15 秒任务必须有唯一任务编号，并建立 `任务编号 -> 舞台定位图 -> 黑白 3x3 分镜图 -> 视频提示词段` 一一映射。
5. **9 Panel 语义**：黑白 3x3 的 9 个 Panel 只是单个 10-15 秒任务内部的 9 个子帧，不等于一集或一个段落只能生成 9 个视频任务。若切出 10 个任务，必须生成 10 套舞台定位图、10 张黑白 3x3 分镜图、10 个视频提示词段。
6. **Panel 9 衔接桥**：同一集内 `EPxx-VTnn Panel 9` 必须写入结构化 `continuity_handoff`，并成为 `EPxx-VT(n+1) Panel 1 continuity_start`；跨集边界时，上一集最后一个 VT 的 Panel 9 必须成为下一集 `EPxx+1-VT01 Panel 1 continuity_start`。继承内容包括角色位置、动作余势、视线方向、道具状态、情绪余韵、声音尾音、屏幕方位、空间方位、朝向方位、镜头方位、运动方位和摄影机状态。但下一任务仍必须生成新的舞台定位图、新的黑白 3x3 分镜图和新的提示词段。Panel 9 不能被下一任务复用为 `@STORYBOARD_GRID`，也不能替代下一任务 Panel 1 图像。
7. **引用有效性与链路连续性预检**：A4、B1-B5、B6-B8 进入前，以及图片 / 视频派单前，必须执行 `Reference Validity & Lineage Continuity Preflight`。预检范围必须按阶段裁剪：A4 只检查 A0/A1/A2/A3 与空间真值；B1-B5 检查 A0/A1/A2/A3/A4；B6-B8 检查 A0/A1/A2/A3/A4/B1-B5。禁止让 A4 等待 B1-B5，或让 B1-B5 等待 B6/B8 这类尚未生成的下游产物；否则视为预检配置错误并立即修正，不得循环等待。除检查占位符、`Reference image paths`、`image_paths`、`@STAGE_MAP_VISUAL`、`@IMAGE_ASSET_N`、`@STORYBOARD_GRID` 是否全部解析到真实 ACTIVE 节点或路径外，还必须验证当前 `video_task_id` 的阶段范围内 `lineage_audit_chain` 无断点、无孤儿路径、无旧节点、无 `[DEPRECATED_OLD_VERSION]`、无版本错配；`source_coordinate_camera_baseline_section` 必须指向 `坐标与摄影机基准`；所有二维 / 三维坐标必须为带 X/Y/Z 键名对象，禁止裸数字、无键数组或散文坐标。失败触发 `[E06: Reference Resolve Failure]` 或 `[E05: Spatial Data Corruption]`。
7A. **链路接口契约 / Link Interface Contract**：每个 `video_task_id` 必须维护 `upstream_producer_map`、`downstream_consumer_map` 与 `link_interface_audit_status`。任一下游字段必须能追溯到明确 producer；任一 producer 必须有 consumer 或标注 `archive_only`；`pending`、`unknown`、`none`、空数组或空对象不得被当成 PASS。consumer 发现空数据时必须回到对应 producer 阶段，不得自行猜测补写或循环重试。违反时触发 `[E13: Link Interface Contract Failure]` 或 `[E14: Empty Upstream Data / Pending-as-PASS Failure]`。
7B. **人物数量锁 / Character Count Lock**：每个 `video_task_id` 必须从 A2 角色站位表、A4 table_node 和 A0 `reference_requirements` 建立 `allowed_visible_characters`、`allowed_visible_character_count`、`forbidden_extra_people` 与 `character_count_lock_status`。A4 舞台定位图、B1-B5 黑白分镜和 B6-B8 最终视频提示词都必须显式继承该锁；允许出现的人物全集只能来自 `allowed_visible_characters`，不得由场景参考图、评审棚语义、合并人物参考图或模型自行扩展。若任一舞台定位图、黑白分镜图或最终视频画面出现未列名人物、人形剪影、群众、额外评委、额外工作人员、路人、游客、背景旁观者，或把合并人物参考图误解为可扩展人群，均判定 FAIL，并触发 `[E15: Character Count Lock Failure]`。合并人物参考图必须拆解为已声明角色写入 `allowed_visible_characters`；它只用于识别这些已列角色，不代表允许生成更多人物。
7C. **舞台定位图形状颜色编码锁 / Stage Map Shape-Color Encoding Lock**：A4 舞台定位图中，全部人物位置必须用方形 / 矩形色块标记，全部道具位置必须用圆形色块标记，禁止人物与道具混用形状。人物颜色按 `allowed_visible_characters` 顺序固定为红、橙、黄、绿、青、蓝、紫、粉、白；道具颜色按当前任务 `required_prop_tokens` / A4 table_node 道具行顺序固定为红、橙、黄、绿、青、蓝、紫、粉、白。白色方块 / 圆形必须带深色描边。A4 table_node 必须冻结 `character_shape_color_map` 与 `prop_shape_color_map`；B1-B5 黑白分镜、B6-B8 视频提示词必须继承这些映射，不得重新配色、重排或丢失形状/颜色编号。黑白分镜最终画面可为纯黑白线稿，但结构化载荷必须保留 A4 的形状、颜色顺序、坐标和 token 映射。
7D. **XYZ 坐标完整性锁 / XYZ Coordinate Integrity Lock**：全链路中角色、道具、摄影机、朝向目标和运动路径不得只写裸数字、无键数组或散文位置。二维字段必须为 `{"x": number, "y": number}`；三维字段必须为 `{"x": number, "y": number, "z": number}`。A2/A4/B1-B5 中每个角色和道具必须同时保留 `stage_position_xy` 与 `stage_position_xyz`，平面舞台对象默认 `z: 0`，多层空间写真实 Z 层级；movement path 若用于下游，必须同步提供 `movement_path_xyz`。若发现只有二维无三维、数组、逗号字符串或缺少 X/Y/Z 键名，触发 `[E05: Spatial Data Corruption]`，必须回到 producer 阶段标准化并回写后才能继续。
8. **视频引用顺序与反向覆盖**：视频阶段 `@STORYBOARD_GRID` 必须是当前任务自己的黑白 3x3 分镜图；其位置不得硬编码为 `image_paths[-2]`，必须由 `final_reference_order_policy` 决定：用户明确指定参考图优先顺序时按用户顺序，典型为主角人物图 → 另一主角人物图 → 当前黑白 3x3 分镜图 → 其他人物 / 合并人物 → 场景 → 道具 / 合并道具 → 光影最后；用户未指定时按默认 SSC 顺序。可选光影资产若存在，必须位于 `image_paths[-1]`；舞台定位图禁止传给视频模型。B8 必须从 A0 `active_asset_pool`、当前 `video_task_id.reference_requirements`、A2 当前任务角色 / 场景 / 道具清单、B1-B5 被剪枝资产清单和当前 ACTIVE 黑白分镜反向建立 `expected_video_reference_assets`；B1-B5 的参考图剪枝只在 `image2` 16 张上限被超过时触发，若总数小于或等于 16 张必须全量传入当前任务必要参考图，且不得作为 B8 视频参考图删减依据。正式派发给 video agent 的 `actual_video_agent_image_paths` 必须与冻结的 `final_reference_image_paths` 数量、顺序、路径逐项完全一致；B8 `final_video_model_prompt_text` / `[FINAL_DISPATCH_PROMPT]` / Verbatim 包装层中“参考图使用规则”的 `@文件名` 出现顺序也必须与实际 `image_paths` 顺序逐项一致，禁止按资产类型重排；否则触发 `[E12: Video Reference Coverage Failure]`。
9. **参考图范围**：只允许人物、场景、关键道具、可选光影、舞台定位图、黑白分镜图六类视觉资产。不得新增服装图、妆造图、表情包、姿势库、材质拆解或气氛小样。
10. **模型边界**：人物 / 场景 / 道具 / 光影参考图使用 `image2 / 1k / 16:9 / high`；舞台定位图使用 `image2 / 1k / 16:9 / medium`（画质：中）；黑白分镜使用 `image2 / 1k / 16:9 / medium`（画质：中）。A4 舞台定位图与 B1-B5 黑白分镜的派单载荷、冻结记录、QC 记录与实际生成 metadata 必须显式写入 `model: gpt-image-2`、`model_alias: image2`、`resolution: 1k`、`quality: medium`、`aspect_ratio: 16:9`；不得省略 `quality: medium` / `画质：中`，不得写入或继承 `resolution: 4k`、`quality: low`、`quality: high`、`画质：低`、`画质：高`、`low quality` 或 `high quality`，否则触发 `[E09: Storyboard Model Parameter Drift]` 或 A4 参数漂移 FAIL。模型变更必须走 `question`。
11. **全链路冻结保护 / Chain Freeze Lock**：保护对象不只限于视频提示词，而是覆盖 A0/A1/A2/A3/A4/B1-B5/B6-B8 全链路所有核心文档、结构化真值、派单载荷、外部 Skill 优化结果、引用映射和最终提示词。每个阶段通过 QC 或完成用户授权后，必须写入 A0 `[SSC_RUNTIME_LEDGER].chain_freeze_manifest`，记录冻结对象、所属阶段、冻结时间、`content_hash_or_length_snapshot`、允许后续变更范围和上游来源。冻结后任何 Agent、任何阶段、任何模型路由都不得修改、压缩、精简、提炼、缩减、概括、重写、截断、删减、合并同义句或用“同上 / 已继承”替代冻结内容；只能原样继承或在允许机器引用层内更新路径、节点 id、版本号、重索引编号。缺少 `chain_freeze_manifest`、`core_doc_freeze_records`、`dispatch_payload_freeze_records`，缺少 `freeze_id` / `content_hash_or_length_snapshot` / `allowed_mutation_scope`，或下游读取未冻结 / 已废弃版本，均触发 `[E08: Chain Freeze Violation]`，禁止继续下游。B8 唯一例外是最终视频模型派发前的“格式编译”：必须在不改变冻结剧情、镜头、台词、表演、声音、时长、参考图语义、Panel 顺序和空间真值的前提下，把工作文档块编译成用户指定的自然语言纯提示词模板。提示词文本长度本身不是限制条件，不得因为提示词长而拆分或改写。只有当单个 10-15 秒视频无法承载上级链路剧本要求表达的剧情、镜头、台词、动作、情绪、声音或转场时，才触发视频承载超限处理：回到 `视频任务切分计划` 拆成更多 10-15 秒视频任务，并为新增任务分别生成舞台定位图、黑白 3x3 分镜图和独立提示词段。
11A. **禁止借冻结偷懒 / No Freeze-Induced Compression Backdoor**：冻结保护不是下游省略载荷的理由。任何阶段读取了冻结对象后，必须把该冻结对象中被下游消费的强制字段完整展开到当前阶段的 `[MANDATORY DISPATCH PAYLOAD]`、task_description、`视频提示词列表.md` 工作块或最终 `final_video_model_prompt_text` 的对应自然语言位置；不得以“已冻结 / 已继承 / 见上游 / 按舞台定位图 / 参考黑白分镜 / 原样继承”等短句替代。尤其 A4 → B1-B5 → B6-B8 必须逐级展开：100x100 坐标系、人物/道具形状颜色映射、`stage_position_xy`、`stage_position_xyz`、`facing_target_xy/xyz`、紫白色朝向箭头语义、`screen_direction_text`、`spatial_direction_text`、`facing_direction_text`、`camera_position_xyz`、`camera_target_xyz`、`camera_direction_text`、`camera_screen_position_text`、`focal_length_mm`、`movement_path_xyz`、`movement_direction_text`、Eyeline、相对距离、Panel 1-9 行列位置、可见/离画/遮挡人物锁。若任一阶段只写概括句而没有展开这些消费字段，即使上游已冻结，也判定 `[E08: Chain Freeze Violation]` + `[E13: Link Interface Contract Failure]`，必须隔离污染产物并从最近 producer 阶段重做。
12. **视频提示词主文档**：只能有一个 `视频提示词列表.md`，其中每个 10-15 秒视频任务有独立提示词段。
13. **QC 基准**：任何 QC 必须先读取当前阶段 reference 与 `references/08-QC与输出验收.md`，以 reference 原文为唯一判定标准。
14. **最终视频模型纯提示词模板最高铁律**：B8 正式派发给视频模型的最终提示词本体必须编译成用户指定的纯提示词形态，并写入 `final_video_model_prompt_text`：以 `model: ...`、`ratio: ...`、`duration: ...`、`generate_audio: ...` 开头，其中 `model / ratio / duration / generate_audio` 只能来自 A0 `video_output_format_lock` 或用户明确覆盖记录，默认 `ratio: 9:16`，不得从 A3/A4/B1-B5 的 16:9 参考资产或分镜画布继承；不一致触发 `[E10: Video Output Format Drift]`。接 `生成N段连贯的剧情视频:`，再写 `参考图使用规则`，其中所有 `@文件名` 必须严格按最终 `Reference image paths` / 正式 `image_paths` 的真实顺序逐项出现，并在各自真实位置说明用途：场景图只作环境与摄影参数参考，人物图只作身份参考，合并人物 / 合并道具图只作识别参考且声明不是分镜图 / 首帧 / 视频帧，道具图只作道具一致性参考，黑白分镜图只作当前任务自己的 Panel 1-9 顺序、blocking、取景、动作链、机位推进和连续性参考。禁止把参考图说明按资产类别重排。随后按 `镜头1（Xs）：... 镜头2（Ys）：... 镜头N（Xs）：...` 连续写完整剧情、动作、微表情、台词和最后静止时长；所有未获 `user | script_explicit` 省略授权的源对白必须以逐字引号形式写入对应镜头，禁止用“指定中文台词 / 原台词 / 按剧本台词 / 他说出台词”等占位概括。镜头数量由当前 `video_task_id` 的视频任务切分计划、Panel Execution Chain、剧情承载预算和用户指定镜头数决定，不固定为 3 个。末尾固定写 `整体风格为超写实电影质感，动态范围：Dynamic Range 15 stops`，并继续写照明方式、氛围、台词交付和音乐策略。B8 最终提示词本体禁止保留 `[Visual Block]`、`[Panel Execution Chain]`、YAML/JSON 结构块、QC 块、冻结记录、Required_References_Read 或任何 SSC 内部字段名；这些只能留在 `视频提示词列表.md` 工作文档和账本中。若上游或外部 Skill 产出“真人实拍质感竖屏微短剧”等近似风格词，B8 编译最终提示词本体时必须统一覆盖为 `超写实电影质感`；凡涉及动态范围，必须统一写为 `Dynamic Range 15 stops`。
15. **Video Agent Verbatim 透传包装层**：`[FINAL_DISPATCH_PROMPT]` 是 B8 最终派发提示词的最高优先级命名冻结物，必须与 `video_task_mapping[video_task_id].final_video_model_prompt_text` 逐字一致。主 Agent 正式派发给 video agent 的 `task_description` 不是最终提示词本体，而是跨 Agent 透传包装层。每个视频任务必须按 `Verbatim prompt N (use as-is):` 包裹该任务已冻结并编译完成的 `[FINAL_DISPATCH_PROMPT]` 全文，N 为同批任务序号，从 1 开始，按同批视频任务顺序对应 `1/2/3/4...`，不得跳号、乱序或复用序号。包装块内部文本必须与 `video_task_mapping[video_task_id].final_video_model_prompt_text` / `[FINAL_DISPATCH_PROMPT]` 逐字一致，且内部第一行仍必须是 `model: ...`。禁止以普通说明段落、摘要段落或“请根据以下内容生成”等形式传递最终派发提示词。`Verbatim prompt N (use as-is)` 只用于防止 video agent 二次改写、翻译、压缩、重排或删减，不属于最终视频模型提示词本体；QC 检查提示词模板时必须检查包装块内部文本。
16. **对白保真锁 / Dialogue Preservation Lock**：A0 必须从原始剧本逐句抽取 `dialogue_source_lock.required_dialogues`，包含 speaker、text、source_line、assigned_video_task_id、required；同时必须建立 `task_dialogue_inventory` 覆盖当前 `video_task_id` 剧情范围内全部源对白。任何后续阶段不得把 required dialogue 或未获省略授权的当前任务源对白删除、概括、换人；未获 `user | script_explicit` 授权时，也不得改写为沉默 / 吸气 / 表情反应 / performance_only。`A0_analysis` 只能提出替代候选或拆分建议，不能作为最终省略 / 沉默化 / performance_only 授权来源。“台词少而准”只允许处理未锁定的重复废话，不得覆盖剧本锁定对白。B8 派发前必须真实读取 A0 当前 `video_task_id` 的完整对白全集，并生成 `dialogue_verbatim_diff_table`；`rows.length` 必须等于 A0 `task_dialogue_inventory.all_source_dialogues_in_task` 中当前任务源对白数量，逐句确认 A0 源对白、B6 `[Audio Hook]`、B8 `final_video_model_prompt_text` 和 video agent Verbatim 包装层 exact match；失败触发 `[E11: Dialogue Source Missing Before Dispatch]` 或 `[E11: Dialogue Verbatim Diff Failure]`。若 10-15 秒承载不了对白、剧情、动作、表演或声音，必须回到 A0 拆分视频任务，不能压缩对白；若矩阵已由 `user | script_explicit` 授权 interrupted / swallowed / performance_only，则必须保留授权来源和反证检查记录。
17. **角色 / 道具合并参考图协议**：A0 判定为“建议出 / 可合并”的多人角色，可按角色合并参考图协议生成彩色框选宫格参考图；A0 判定为“必出 / 建议出 / 可合并”的道具，可按道具合并参考图协议生成彩色框选宫格参考图。颜色顺序为红、橙、黄、绿、青、蓝、紫、粉、白，阅读顺序为从左到右、从上到下；2=1×2，3=1×3，4=2×2，5/6=2×3，7/8/9=3×3，其中 7/8 只占用对应格数，其余为空位且不得生成额外角色 / 道具。白色框必须带深色描边。
18. **合并参考图视频解释锁**：角色 / 道具合并参考图进入视频 `image_paths` 时，B6/B8 必须在 `参考图使用规则` 中明确说明：该图是多宫格身份 / 道具识别参考，不是分镜图，不是首帧，不是视频中的某一帧；彩色框和中文标注只用于识别，最终画面不得出现框线、文字、色块、宫格或参考图排版。
19. **场景摄影参数继承门**：每次生成场景参考图前必须提醒或在全自动模式下自动加载光影、色彩、氛围、质感、摄影机参数、光圈、景深、镜头参数等相关能力；A3 必须记录 `scene_cinematography_profile`，B6/B8 必须把场景参考图的光影、色彩、氛围、质感、摄影机系统、光圈、景深、焦段 / 镜头参数继承到最终视频提示词，确保场景参考图与视频摄影参数一致。
20. **对白意图与交付方式判定矩阵**：A0 必须为每句对白建立 `dialogue_intent_delivery_matrix`，判断剧情功能、观众依赖、关系依赖、主题依赖、是否可被表演替代、交付方式和决策归属。只有 `user | script_explicit` 可作为最终省略 / 片段化 / 沉默化 / performance_only 授权来源；`A0_analysis` 只能记录建议，不能授权删改。B6/B8 只能继承矩阵，不得以“台词少而准”“留给演技”“镜头更有张力”为由临时删台词、沉默化或 performance_only。凡无法确认是否可替代，默认保留 spoken，必要时回 A0 拆分任务或弹窗裁决。
21. **视频模型能力边界预判**：A0 必须建立 `video_model_capability_risk_register` 和 `performance_substitution_plan`，提前识别精细书写、准确画符号、可读 UI、复杂手部小物互动、连续物理变化、多人遮挡动作等高风险动作。下游必须保留动作的剧情功能，但可用结果先行、动作遮挡、声音替代、反应替代、静态道具替代或镜头切分替换字面实时动作；B6/B8 不得重新要求视频模型执行已判定高风险且需替代的字面动作。
22. **A0 新增字段全链路消费锁**：`dialogue_intent_delivery_matrix`、`task_dialogue_inventory`、`video_output_format_lock`、`video_model_capability_risk_register`、`performance_substitution_plan`、`reference_requirements` 不得停留在 A0 私有字段。A1 必须摘要继承，A2 必须转成 Panel / Temporal / 道具动作 / 参考需求载荷，A3/A4 必须消费静态替代道具、空间状态和参考需求接口，B1-B5 必须进入黑白分镜结构化文本负载与剪枝清单，B6-B8 必须转成 `dialogue_verbatim_diff_table`、`dialogue_delivery_mode_check`、`video_output_format_check`、`expected_video_reference_assets` 与 `model_capability_risk_execution_check`，QC 必须逐段验收；任一阶段缺消费记录视为空链路，触发 `[E01: Missing Script/Assets]`、`[E13: Link Interface Contract Failure]` 或 `[E14: Empty Upstream Data / Pending-as-PASS Failure]`。

## 4. 外部 Skill 加载协议

每个独立制作动作进入 Entry Guard 前，必须通过 `question` 弹窗询问是否加载自建 Skill 辅助当前步骤，并把本次授权写入 A0 `[SSC_RUNTIME_LEDGER].question_authorization_log`。可参考能力：award-winning-performance-prompt / video-prompt-craft、character-body-language、cinematic-camera-engine、epic-cinematic-lighting、voice-performance-layer、cinematic-grammar-glossary、ARRI ALEXA 65 摄影机参数决策、FACS/AU、身体物理碰撞与步态、镜头语言增强、音频/SFX 设计、剧本分析/影视化转译、空间布局分析/建筑结构解析、分镜构图、QC 校验/视觉一致性检查。

用户回复“无 / 跳过”则记录为跳过；用户指定能力则写入 `[EXTERNAL_SKILL_LOAD_LIST]`，并同步写入 A0 `[SSC_RUNTIME_LEDGER].external_skill_load_log` 后在后续 task_description 中注入。用户原话包含“全自动”时，跳过外部 Skill 加载询问，视为授权加载当前阶段可用的全部相关外部 Skill 能力；必须写入 `[EXTERNAL_SKILL_LOAD_LIST: AUTO_ALL_RELEVANT]` 与 A0 `[SSC_RUNTIME_LEDGER].external_skill_load_log`，并把已加载能力注入后续 task_description 做优化。链路异常自愈时，只能复用账本里已记录的外部 Skill 列表，不能凭对话记忆复用。

## 5. 异常自愈

任一阶段因工具超时、网络波动或模型中断导致链路失联时，总控 Agent 必须优先读取 `references/00-强制读取索引.md`、`references/08-QC与输出验收.md` 与 A0 `[SSC_RUNTIME_LEDGER]`，再根据 `latest_success_checkpoint`、`stage_qc_log`、`question_authorization_log`、`external_skill_load_log` 和 `active_asset_pool` 恢复。必须恢复至上一个通过 QC 的环节，只补做缺失或失败的下一动作；恢复前先隔离失败产物到 `deprecated_asset_pool`，不得从头重跑污染 ACTIVE 资产池。

## 6. 输出清单

完整链路结束后至少应有：A0、A1、A2、空间真值调用中心、允许范围内视觉资产、按 `视频任务切分计划` 逐任务生成并一一绑定的舞台定位图、黑白 3x3 分镜图和 `视频提示词列表.md` 独立提示词段，以及通过 `question` 弹窗确认或在“全自动”模式下按全程授权生成的视频片段。

## 7. 触发测试

应触发：

- “帮我先做舞台定位图再做黑白宫格分镜”
- “这个故事短片需要先稳定站位，然后从零做分镜”
- “用舞台定位图的坐标继续黑白分镜图”
- “根据剧本生成视频提示词列表.md”
- “检查视频任务切分计划和分镜引用是否对齐”
