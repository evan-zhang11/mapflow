# Known Issues & TODOs

**Last Updated**: 2026-02-16

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
