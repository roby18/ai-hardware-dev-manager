# 工具与技能使用清单（tools-and-skills.md）

> 执行硬件固件开发时的工具用法与 Skill 路由。先读本文件确定"这一步用哪个工具 / 技能"，再动手。
> 示例基线（路径随机器不同）：Windows + ESP-IDF + 对应 Python 虚拟环境 + host 测试工具链。

## 工具路由总表

| 动作 | 用谁 | 说明 |
|---|---|---|
| 环境安装 / 修复 / 编译 / 烧录 / 串口验证 / 设备验身 | 环境类专用 Skill（如 esp-idf-cy） | 环境与设备操作的第一入口；遵循其烧录安全规约 |
| 板级事实（目标板引脚 / BOOT / 电源域） | 开发板知识类 Skill（如 easyinput-board-cy） | 该板硬件事实的权威源，读取其 references |
| 新板子通用硬件档案 | 本 Skill `assets/hardware-archive.template.md` | 复制改，权威分级填来源 |
| 纯逻辑单元测试（无板） | host 测试（纯 C + Unity） | 编译 + 运行一条命令 |
| 版本管理 | git | 每里程碑 commit+push |
| 文档配图 / 机制图 | 可视化工具 | 架构 / 流程 / 机制类配图 |
| 项目多 Agent 协作骨架 | 协作流程 Skill（如 project-flow-cy） | 用户要求接协作流程时 |

## 环境激活脚本模式（一键激活脚本范式）

每次编译 / 烧录前必须处于完整工具链环境。脚本要点：

```
# 关键环境变量（固定，不依赖会话状态）
$env:IDF_PATH = "<SDK 安装路径>"
$env:IDF_TOOLS_PATH = "<工具链根路径>"
$env:IDF_PYTHON_ENV_PATH = "<Python 虚拟环境路径>"
# 官方 export
. "$env:IDF_PATH\export.ps1"
# export 之后二次强制（官方 export 会覆盖前置设置！）
$env:IDF_CCACHE_ENABLE = "0"
$env:CMAKE_BUILD_PARALLEL_LEVEL = "2"
```

- 无交互终端时不要裸跑需要 TTY 的命令（如 monitor / menuconfig，会卡死会话）；配置改 `sdkconfig.defaults` 后重新生成，不直接编辑生成的 sdkconfig。
- 若 `python` 不在 PATH，用虚拟环境内的解释器显式调用构建命令。

## 构建命令要点

```
idf.py set-target <芯片型号>   # 首次或换芯片
idf.py build                   # 编译（限并行度见激活脚本）
idf.py -p <串口号> flash       # 烧录（先验端口+MAC+用户确认）
# 日志验证：真终端用 monitor；agent 用有界串口采集（定超时 + 特征正则）
```

- 首次 build 编译几百文件，几分钟正常，后台跑。
- 烧录前清理残留 monitor 进程（编辑器插件会残留占用串口）。
- 手动进过下载模式的板子：烧完**断电重开**才跑新固件（勿重复按 BOOT）。

## host 测试（无板单元测试）

- 前提：把被测逻辑抽成零框架依赖纯 C（见 phases P4）。
- 工具链：本机 C 工具链（如 w64devkit，gcc + 基础环境），测试框架 Unity。
- 运行：host 构建脚本（编译 + 运行一条命令，秒级）；结果格式 `N/M tests (X Failures)`。
- 纪律：修改被抽取源码后，先跑 host 再回归固件构建——单一来源，杜绝两份代码漂移。
- 踩坑备忘：工具链子进程（as/ld）需 bin 入 PATH；严格 C11 下 `M_PI` 需 `#ifndef` 兜底；Unity 要求全局 setUp/tearDown；固件侧 NULL 需显式 `#include <stddef.h>`。

## 串口工具

- 串口助手 / 编辑器 Monitor / 脚本读取：**同一时间仅一个进程可用**，用完即关。
- 验证周期信号（如心跳日志）用有界采集：定超时、匹配特征正则，超时未匹配判失败——不用无界 monitor。

## Git / GitHub

- 每里程碑 `git add -A && git commit -m "Mxx: 描述" && git push`；回复中告知提交内容。
- `.gitignore` 排除：`build/`、`managed_components/`、`sdkconfig.old`、`.pyc`、日志等生成物。
- 敏感信息（token / 密钥 / 大文件）绝不入库；网络受限时按本机已有方式处理（代理/镜像等），该经验属个人环境，不回填本 Skill。

## 与专用 Skill 的协作边界

- 环境安装 / 修复 / 烧录 / 设备验身这类**确定性高风险操作**，交给环境类专用 Skill 按其安全规约执行（验身份、用户确认、烧后闭环）。
- 本 Skill 负责：项目级流程编排、硬件档案 / 需求 / 日志文档、分层规划与门禁检查、经验复盘。两者不冲突：先有流程，再有执行。
