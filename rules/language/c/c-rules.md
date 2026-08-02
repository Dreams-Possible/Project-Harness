# C 语言编码规范

## 命名规范
本部分定义 C 代码必须遵守的命名和风格规范，遵循 Linux 内核编码风格及常见工业标准。

### 总览

| 类型 | 规范 | 示例 |
|------|------|------|
| 变量 | `snake_case` | `user_name`, `count` |
| 函数 | `snake_case` | `get_user_info()`, `calculate_total()` |
| 结构体 | `snake_case` 或 `PascalCase` | `user_data_t`, `UserData` |
| 常量 | `UPPER_SNAKE_CASE` | `MAX_RETRY_COUNT`, `DEFAULT_TIMEOUT` |
| 宏 | `UPPER_SNAKE_CASE` | `BUFFER_SIZE`, `IS_VALID(x)` |
| 枚举值 | `UPPER_SNAKE_CASE` | `STATE_IDLE`, `STATE_RUNNING` |
| 全局变量 | `g_snake_case` | `g_user_count`, `g_config` |
| 文件名 | `snake_case.c` / `snake_case.h` | `user_utils.c`, `data_processor.h` |

### 变量命名

- 使用小写字母和下划线
- 名称应清晰表达变量用途
- 布尔变量使用 `is_`、`has_`、`can_` 前缀
- 全局变量使用 `g_` 前缀

### 函数命名

- 使用小写字母和下划线
- 动词开头，清晰表达功能
- 静态函数（文件内）可使用 `_` 前缀

### 结构体命名

- 使用 `snake_case` 
- 类型定义时使用 `_t` 后缀
- 结构体标签使用 `snake_case`

### 宏和常量命名

- 全部大写，单词间用下划线分隔
- 宏参数也使用大写
- 所有的数字必须使用括号包围，避免展开时的优先级问题

### 枚举命名

- 枚举类型使用 `snake_case`
- 枚举值使用 `UPPER_SNAKE_CASE`
- 可以使用枚举类型名作为前缀

### 指针命名

- 指针变量使用普通 `snake_case`
