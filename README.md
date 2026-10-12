# AI Hardware Dev Manager

新硬件固件开发项目的方法论 Skill：把「从零到可用」的嵌入式 / 单片机固件开发标准化为可执行的**阶段流程、验收门禁、经验红线与项目模板**。经验沉淀自真实嵌入式项目复盘，不绑定特定板子或机器，人人可用。

## 核心思想

**硬件事实先于代码、环境先于业务、逐层验证、每层闭环**（编译 → 烧录 → 实测 → 提交 → 沉淀）。不猜不编、先征同意、证据分级。

六阶段：**P0 需求决策 → P1 硬件摸底 → P2 环境基线 → P3 骨架与第 0 层 → P4 逐层功能 → P5 验证交付 → P6 复盘沉淀**。每阶段有硬性产出与门禁，满足才进下一阶段。

## 目录结构

```
SKILL.md                                  # 主入口：六阶段总览、11 条红线、执行流程、资源路由
references/
  phases.md                               # 六阶段详细操作手册（含 P1 分步访谈 8 阶段）
  lessons.md                              # 经验与踩坑库（现象/根因/规则）
  tools-and-skills.md                     # 工具使用清单与专用 Skill 协作边界
  esp32-s3-constraints.md                 # ESP32-S3 芯片约束快速反查（strapping/仅输入/存储占用/复用）
assets/
  hardware-archive.template.md            # 硬件档案模板（GPIO 映射/供电使能链/BOOT/危险区/待验证项）
  bring-up-checklist.template.md          # 逐级点亮清单（串口→心跳→每外设，可观察验收标准）
  project-log.template.md                 # 项目日志模板（里程碑/决策表/踩坑表/复盘要点）
```

## 使用方式

1. 新硬件项目开工：按 P0 → P1 → … → P6 推进，每阶段对照 `phases.md` 的动作序列与验收清单。
2. P1 硬件摸底采用**分步访谈**（每阶段 3–6 问、随问随存 `intake-notes.md`），引脚表 100% 冻结且来源可溯。
3. 每层新增经验按分类回填 `references/lessons.md`，模板改进回填 `assets/` 模板——Skill 自身随项目迭代。
4. 执行阶段按需路由到已安装的专用 Skill（环境/编译/烧录类、开发板知识类等），无对应 Skill 时使用官方工具承担。

## 许可

保留所有权利（All rights reserved）。
