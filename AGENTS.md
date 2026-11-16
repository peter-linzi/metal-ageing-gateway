# 项目级开发规则

## 提交前验证

提交纯文档变更时，仅检查文档内容和格式，不执行 build 和 test（包括 `colcon build`、`colcon test` 和 `colcon test-result`）。

提交任何代码或接口变更前，必须先检查受影响文件的代码风格，再在当前工作区完成编译和测试，并确认全部通过。

变更 C++ 文件后，使用仓库 `.clang-format` 执行格式化，并处理相关 `.clang-tidy` 发现。先运行受影响包或测试的窄范围检查，再运行完整验证；如果工具或配置不可用，必须报告并视为验证未完成。

完整验证命令：

```bash
colcon build --symlink-install --event-handlers console_direct+
colcon test --event-handlers console_direct+
colcon test-result --verbose
```

以下任一情况都视为验证失败，不得提交：

- 受影响的 C++ 文件未按 `.clang-format` 检查或格式化；
- 相关 `.clang-tidy` 发现未处理；

- `colcon build` 出现 `Failed`、`Aborted` 或未完成的 package；
- `colcon test` 失败、跳过了应执行的测试，或测试阶段中止；
- `colcon test-result --verbose` 报告失败测试；
- 构建环境缺少依赖，导致编译或测试未实际执行；
- 仅完成静态检查，未完成上述编译和测试。

验证失败时，应先修复问题并重新执行完整验证。不得用旧的 `build/`、`install/` 或 `log/` 产物替代本次变更的编译和测试结果。
