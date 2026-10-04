# OAutoPIN
ColorOS 开机自动解锁 SIM 卡 PIN

- 先在 app 内设定 PIN
- 仅在 ColorOS16 经过测试，其余自测
- 仅适配了单 SIM 卡或双卡单锁
- 仅作为自用，无 bug 不更新

## 来源与修改声明

本项目**并非原创**，而是从原作者仓库修改升级而来：

- 上游项目：[achyuki/OAutoPIN](https://github.com/achyuki/OAutoPIN)（作者：achyuki）
- 上游提交基线：`275367d`（上游 `main` 分支）
- 本项目地址：[iamhcfhsgl/OAutoPIN-Next](https://github.com/iamhcfhsgl/OAutoPIN-Next)

**修改原因是原作者并未继续更新**：上游模块仍使用 legacy Xposed API，并依赖
`XSharedPreferences` + `MODE_WORLD_READABLE` 这套跨进程读写方案。该方案在 LSPosed 中已被
标记为废弃、计划于 2.3.0 移除（模块列表页会显示废弃警告），且 `MODE_WORLD_READABLE` 本身
早已被 Android 废弃。因此在**不改变原有功能**的前提下，对模块做了适配性升级：

- 从 legacy Xposed API 迁移到 **libxposed 公共 API 102**
- PIN 存储改到框架自有的 **RemotePreferences**，不再产生 others 可读的 preference 文件
- 新增对**热重载**的支持，保存新 PIN 后无需重启 SystemUI

**本项目的修改工作由 AI 大肥鱼老师完成。**

除上述适配性修改外，模块的目标应用（ColorOS 的 SystemUI）、hook 逻辑与使用方式均沿用上游。

## 声明

- 本项目为**非官方修改版**，与原作者 achyuki **无隶属或授权关系**，也不代表原作者的立场。
  项目名称、包名沿用上游仅为保持兼容与可追溯，不代表任何官方背书。
- 本项目**仅供学习与技术研究使用，禁止用于任何商业用途**。请在下载后 24 小时内自行删除。
- 使用者需自行承担使用风险。修改系统行为存在不可预期的后果（包括但不限于锁屏无法解锁、
  SIM 卡被 PUK 锁定、系统异常），请务必自行备份并确认能够恢复。作者不对任何直接或间接损失负责。
- 请勿在未获授权的情况下将本项目用于侵犯他人合法权益的用途。
- **若本项目内容侵犯了任何人的合法权益，请通过下方渠道联系，我们将立即删除相关内容并停止分发。**
  - 侵权联系 / 删除请求：[提交 Issue](https://github.com/iamhcfhsgl/OAutoPIN-Next/issues)
- 若你是原作者 achyuki 并希望本项目停止分发或删除，请提交 Issue 说明，我们会立即配合处理。
- 上游项目及其原始代码、名称、图标等权利归原作者所有；本项目仅在其基础上做兼容性修改，
  并保留上游的 [MIT License](LICENSE)。

> **请联系删除：如有侵权，请联系删除。** 收到有效通知后我们会尽快处理。

## 模块形态

本版本要求框架支持 **libxposed API 102**：

- 目标作用域：`com.android.systemui`
- 声明方式：`META-INF/xposed/{java_init.list,module.prop,scope.list}`
- PIN 存放于框架自有的 **RemotePreferences**（不再使用 `XSharedPreferences` /
  `MODE_WORLD_READABLE`）
- 保存新 PIN 后可通过 LSPosed 的**热重载**生效，无需重启 SystemUI

改动细节、验证方式与构建注意点见 [MIGRATION.md](MIGRATION.md)。
云端构建与发行版流水线见 [.github/workflows](.github/workflows)。

> 升级提示：`16.2.0` 起包名由上游的 `io.github.achyuki.oautopin` 改为
> `io.github.iamhcfhsgl.oautopin`，因此**这是一次全新安装**（签名与包名都不同，
> 旧版无法覆盖升级，请先卸载旧版）。原应用数据目录下的
> `shared_prefs/pin.xml`（旧版遗留的 others 可读文件）会随卸载一并清除；
> 新版不再创建此类文件，请在设置页重新保存一次 PIN。
