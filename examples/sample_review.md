# Sample PR Review Output

## PR: feat: Add user authentication (#142)

**Verdict**: 🟡 REQUEST CHANGES

### Summary
The PR implements JWT-based authentication as specified in Asana task ASN-441. Core logic is correct but has security and test coverage gaps.

### Issues Found

#### 🔴 Critical
- **Missing token expiry validation** in `auth/middleware.py:47` — tokens are decoded but expiry (`exp` claim) is never checked. A stolen token never expires.

#### 🟡 High
- **No rate limiting** on `/api/auth/login` — vulnerable to brute force attacks
- **Test coverage**: `auth/` module at 23% — authentication code needs >80% coverage

#### 🟢 Low
- `user.password` logged in debug mode (`utils/logger.py:92`) — remove before merge
- Missing docstring on `generate_refresh_token()`

### Asana Task Alignment
✅ JWT implementation matches ASN-441 spec  
❌ 2FA requirement from ASN-441 not implemented — is this deferred?

### Recommendation
Fix critical token expiry issue + add login rate limiting before merging.
