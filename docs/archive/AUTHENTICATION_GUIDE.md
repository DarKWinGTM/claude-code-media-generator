# Veo Video Generation - Authentication Guide

## Version: 1.0
## Date: 2026-01-18

---

## 1. Executive Summary

### Key Finding

**ทุกวิธี Authentication สุดท้ายติด Billing เหมือนกัน**

| Method | Authentication | Veo Access |
|--------|---------------|------------|
| API Key | ✅ ง่าย | ❌ ต้อง Billing |
| OAuth | ✅ ซับซ้อน | ❌ ต้อง Billing |
| Service Account | ✅ ซับซ้อน | ❌ ต้อง Billing |

**Veo ไม่มี Free Tier ใน API** - ไม่ว่าจะ authenticate ด้วยวิธีใด

---

## 2. Authentication Methods Comparison

### 2.1 Complexity Comparison

| เรื่อง | API Key | OAuth | Service Account |
|--------|---------|-------|-----------------|
| **Setup** | 1 นาที | 30+ นาที | 15 นาที |
| **Code Lines** | 3 บรรทัด | 50+ บรรทัด | 20 บรรทัด |
| **Token Management** | ไม่ต้อง | ต้อง refresh | ต้อง refresh |
| **Scope Registration** | ไม่ต้อง | ต้อง | ไม่ต้อง |
| **Browser Flow** | ไม่ต้อง | ต้อง | ไม่ต้อง |
| **Error Handling** | น้อย | มาก | ปานกลาง |

### 2.2 Recommendation

```
Simplicity Ranking:

1. API Key          ★★★★★  (ง่ายที่สุด)
2. Service Account  ★★★☆☆  (ปานกลาง)
3. OAuth            ★★☆☆☆  (ซับซ้อนที่สุด)
```

---

## 3. API Key Method (Recommended)

### 3.1 Why API Key is Best

- **No token refresh** - Key ไม่หมดอายุ (จนกว่าจะ revoke)
- **No browser flow** - ไม่ต้องเปิด browser
- **No scope management** - ไม่ต้องลงทะเบียน scopes
- **Simple code** - แค่ใส่ header

### 3.2 Setup Steps

```
Step 1: ไปที่ https://aistudio.google.com/apikey
Step 2: Create API Key
Step 3: Enable Billing (สำหรับ Veo)
Step 4: ใช้งานได้เลย
```

### 3.3 Usage

```bash
# Command line
python video_gen.py "prompt" --auth api-key --api-key YOUR_KEY

# Or set environment variable
export GOOGLE_API_KEY="YOUR_KEY"
python video_gen.py "prompt" --auth api-key
```

### 3.4 Code Example

```python
import requests

API_KEY = "your-api-key"
url = f"https://generativelanguage.googleapis.com/v1beta/models/veo-3.1-generate-preview:predictLongRunning?key={API_KEY}"

response = requests.post(url, json=payload)
```

---

## 4. OAuth Method (Complex)

### 4.1 Why OAuth is Complex

- **Browser flow required** - ต้องเปิด browser ทุกครั้งที่ token หมดอายุ
- **Token expires** - Access token หมดอายุใน 1 ชั่วโมง
- **Scope registration** - ต้องลงทะเบียน scopes ใน Google Cloud Console
- **Consent screen** - ต้อง configure OAuth consent screen
- **Multiple error types** - Token expired, scope mismatch, refresh failed

### 4.2 OAuth Flow

```
OAuth Flow (10+ steps):

1. Load client_secret.json
2. Check for existing token
3. If expired → refresh token
4. If no token → browser flow
5. Open browser
6. User consents
7. Receive callback
8. Exchange code for token
9. Store token
10. Finally make API request
```

### 4.3 Required Scopes for Gemini API

```python
SCOPES = [
    "https://www.googleapis.com/auth/generative-language.peruserquota",
    "https://www.googleapis.com/auth/generative-language.tuning",
    "https://www.googleapis.com/auth/generative-language.retriever",
]
```

### 4.4 Common OAuth Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `restricted_client` | Scope not registered | Register scope in Cloud Console |
| `invalid_grant` | Token expired/revoked | Re-authenticate |
| `access_denied` | User denied consent | User must approve |
| `scope_changed` | Missing openid scope | Add openid to scopes |

---

## 5. Billing Requirement (Critical)

### 5.1 Veo Has No Free Tier

```
Veo API Pricing (Paid Tier Only):

| Model     | Free Tier        | Paid Tier           |
|-----------|------------------|---------------------|
| Veo 3.1   | ❌ Not available | $0.15-$0.60/sec    |
| Veo 3.0   | ❌ Not available | $0.15-$0.40/sec    |
| Veo 2.0   | ❌ Not available | $0.35/sec          |
```

### 5.2 Subscription vs API Access

```
Google AI Pro/Family Subscription ($19.99/month)
│
├── App-based Access (UI) ✅ INCLUDED
│   ├── gemini.google.com
│   ├── aistudio.google.com
│   └── Veo quota: 3-5 videos/day
│
└── API Access ❌ NOT INCLUDED
    ├── Separate billing system
    ├── Pay-as-you-go pricing
    └── Veo: No free tier
```

### 5.3 How to Enable Billing

```
Option 1: Via AI Studio
1. Go to https://aistudio.google.com/apikey
2. Find your project
3. Click "Upgrade"
4. Link billing account

Option 2: Via Cloud Console
1. Go to https://console.cloud.google.com
2. Select project
3. Go to Billing
4. Enable billing
5. Link credit/debit card
```

---

## 6. Test Results Summary

### 6.1 OAuth Testing

```
OAuth with generative-language.peruserquota scope:

✅ Authentication: SUCCESS
✅ Scope validation: SUCCESS
✅ Token generation: SUCCESS
❌ Veo generation: FAILED (429 Quota Exceeded)

Reason: No billing enabled → quota = 0
```

### 6.2 API Key Testing

```
API Key testing:

✅ List models: SUCCESS
✅ Text generation (gemini-2.5-flash): SUCCESS
❌ Veo generation: FAILED (429 Quota Exceeded)

Reason: No billing enabled → quota = 0
```

### 6.3 Conclusion

**Both methods work for authentication, but Veo requires billing regardless of auth method.**

---

## 7. Recommendations

### 7.1 For Video Generation via API

```bash
# Simplest approach (requires billing)
python video_gen.py "prompt" --auth api-key --api-key YOUR_KEY
```

### 7.2 For Free Video Generation

```
Use Gemini App (UI):
1. Go to gemini.google.com
2. Use your Pro/Family subscription quota
3. Generate 3-5 videos/day
```

### 7.3 Decision Matrix

| Situation | Recommendation |
|-----------|----------------|
| Just testing | Use Gemini App (UI) |
| Need API automation | Enable billing + API Key |
| Multi-user app | Enable billing + OAuth |
| Enterprise/CI/CD | Enable billing + Service Account |

---

## 8. Quick Reference

### 8.1 API Key (Recommended)

```bash
# One-time setup
# 1. Create key at aistudio.google.com
# 2. Enable billing

# Usage
python video_gen.py "prompt" --auth api-key --api-key YOUR_KEY
```

### 8.2 OAuth (If needed)

```bash
# One-time setup
# 1. Create OAuth client in Cloud Console
# 2. Register scopes
# 3. Configure consent screen
# 4. Enable billing

# Usage
python video_gen.py --login
python video_gen.py "prompt" --auth oauth
```

---

## 9. Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-01-18 | Initial version with API Key vs OAuth comparison |
