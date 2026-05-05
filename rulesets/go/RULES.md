# Go 全局规则

> 适用范围：x.go 项目所有 Go 代码
> 互补规则：[testing.md](./testing.md)、[domain-invariants.md](./domain-invariants.md)、[testing-real-environment.md](./testing-real-environment.md)

---

## 精度约束（DI-001）

- **禁止 `float64` 用于价格、数量、费率等金额字段**，必须使用 `decimal.Decimal`
- 统计计算（均值、标准差、Z-Score）可用 `float64`，但输入输出必须经过 `decimal.Decimal` 转换
- 例外记录见 [domain-invariants.md](./domain-invariants.md)

## 错误处理

- 库代码使用 `fmt.Errorf("...: %w", err)` 包装错误，保留错误链
- 禁止吞错（至少 `log` 记录）
- 禁止 `panic`，除非是程序初始化阶段的 fail-fast
- 错误消息必须包含上下文：`fmt.Errorf("fetch %s kline: %w", symbol, err)`
- 使用 `errors.Is` / `errors.As` 判断错误类型，禁止字符串比较

## 代码风格

- `gofmt` / `goimports` 必须通过（PostToolUse hook 自动执行）
- `go vet ./...` 零警告
- 公共导出类型和函数必须有文档注释
- 注释使用中文
- 禁止 `fmt.Println` / `fmt.Printf`（使用 `log/slog` 或 `zap`）

## 命名约定

| 元素 | 风格 | 示例 |
|------|------|------|
| 类型/接口 | `PascalCase` | `OrderBook`, `Provider` |
| 函数/方法 | `camelCase` | `fetchKline`, `GetTicker` |
| 常量 | `PascalCase` 或 `SCREAMING_SNAKE_CASE` | `MaxRetry`, `DEFAULT_TIMEOUT` |
| 包名 | `lowercase` 单数 | `provider`（非 `providers`） |
| 文件名 | `snake_case` | `order_book.go` |
| 接口 | 动词/名词 + `er` | `Fetcher`, `Publisher` |

## 并发安全

- 共享状态必须用 `sync.Mutex` / `sync.RWMutex` 保护，或使用 channel
- 禁止裸读写 map（并发场景必须加锁或使用 `sync.Map`）
- goroutine 必须有退出机制，禁止泄漏（用 `context.Context` 控制生命周期）
- `go test -race` 必须通过

## 依赖层级

- 禁止跨层调用：`provider` 层不得引用 `analysis` / `strategy` 层
- 禁止循环依赖
- `internal/` 下的包只能被本项目引用

## 时间戳

- 所有时间戳统一使用 **UTC 毫秒**（`int64`）
- 禁止使用本地时区（`time.Local`），必须使用 `time.UTC`
- 详见 SPEC-29

## 数据库操作

- SQL 必须使用参数化查询，禁止字符串拼接
- TDengine 禁止将 `interval` 作为 TAG（详见 SPEC-27）
- 事务操作必须显式 `Commit` / `Rollback`

## 日志规范

- 使用结构化日志（`slog` 或 `zap`），禁止裸 `fmt.Print*`
- 错误日志必须带 `err` 字段：`slog.Error("fetch failed", "err", err, "symbol", symbol)`
- 禁止在日志中输出敏感信息（API Key、密码）

## 验证命令

```bash
go build ./...
go test -count=1 ./...
go test -race ./...
golangci-lint run ./...
```
