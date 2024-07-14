# CMAKE

## cmake with gtest

- https://google.github.io/googletest/quickstart-cmake.html
- 如果想要使用 ctest, 必须在项目目录中的 `CMakeLists.txt` 中 `enable_testing()`

# BAZEL

# GTEST

## Death Test

gtest 的死亡测试能做到在一个安全的环境下执行崩溃的测试案例，同时又对崩溃结果进行验证。

| Fatal                                       | assertion Nonfatal                          | assertion Verifies                                                       |
| ------------------------------------------- | ------------------------------------------- | ------------------------------------------------------------------------ |
| `ASSERT_DEATH(statement, regex);`           | `EXPECT_DEATH(statement, regex);`           | statement crashes with the given error                                   |
| `ASSERT_EXIT(statement, predicate, regex);` | `EXPECT_EXIT(statement, predicate, regex);` | statement exits with the given error and its exit code matches predicate |

- 编写死亡测试案例时，TEST 的第一个参数，即 testcase_name，请使用 DeathTest 后缀。原因是 gtest 会优先运行死亡测试案例，应该是为线程安全考虑。
- regex 是一个正则表达式，用来匹配异常时在 stderr 中输出的内容
- predicate 在这里必须是一个委托，接收 int 型参数，并返回 bool。只有当返回值为 true 时，死亡测试案例才算通过。gtest 提供了一些常用的 predicate：
  - `testing::ExitedWithCode(exit_code)`
  - `testing::KilledBySignal(signal_number) // Windows 下不支持`
