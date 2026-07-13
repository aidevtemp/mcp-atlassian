# Fix `main.py` auth method/function mismatch

**Current broken state**
- `_process_authentication_headers` was dedented to module level, but still called as `self._process_authentication_headers(scope_copy)` from `UserTokenMiddleware.__call__`.
- The standalone function still calls `self._parse_auth_header(...)`, but `_parse_auth_header` was removed from the file.
- This causes `AttributeError` / `NameError` at runtime.

**Proposed fix**
1. Restore `_process_authentication_headers` as an instance method of `UserTokenMiddleware` (move it back under the class, same indentation as other methods).
2. Restore `_parse_auth_header` as an instance method of `UserTokenMiddleware` (move it back under the class, same indentation as other methods).
3. Keep the new `x-api-key` fallback logic intact inside `_process_authentication_headers`.
4. Keep `entrypoint.py` unchanged.
5. Run lint/typecheck/tests to confirm.

**Validation**
```bash
uv run pytest -xvs
pre-commit run --all-files
```
