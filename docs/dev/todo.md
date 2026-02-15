# Known Issues & TODOs

**Last Updated**: 2026-02-16

## Test Bugs (Needs Fix)

### test_persistence_across_restart_keeps_ready_dataset

- **Location**: `backend/tests/api_tests.rs`
- **Status**: `#[ignore]`
- **Root Cause**: 测试代码 bug - 创建 conn2 时 conn1 仍未关闭
- **Analysis**:
  - `app1` 和 `db1` 在整个测试函数作用域内都存活
  - 创建 `conn2` 时，`conn1` 仍持有数据库连接
  - DuckDB 在多连接场景下 WAL replay 出问题
- **Fix Options**:
  1. 显式 `drop(app1)` 和 `drop(db1)` 后再创建 conn2
  2. 在 conn1 上执行 `CHECKPOINT` 强制 flush WAL
- **Related DuckDB Issues**:
  - https://github.com/duckdb/duckdb/issues/20543
  - https://github.com/duckdb/duckdb/issues/19712
  - https://github.com/duckdb/duckdb/issues/18259
- **Priority**: Low (persistence 测试对当前功能影响较小)

## Performance Improvements

### Docker Build Time (docker_smoke)

- **Current**: ~15 minutes
- **Bottleneck**: `cargo build --release` recompiles everything from scratch
- **Potential Solutions**:
  1. Enable Docker layer cache (`cache-from`/`cache-to`)
  2. Reuse `backend_tests` compilation artifacts
  3. Use pre-built base image with dependencies
- **Priority**: Medium

## Future Enhancements

### MBTiles Connection Pool

- **Current**: New SQLite connection per request
- **Goal**: Connection pooling for high-traffic scenarios
- **Priority**: High (for production)

### Slug Race Condition

- **Current**: Manual uniqueness check before INSERT
- **Issue**: Small probability of race condition
- **Solution Options**:
  1. Database unique constraint (requires DuckDB support)
  2. Transaction with retry logic
- **Priority**: Low (acceptable for current phase)
