# 🔄 Request Load Flow: ถูกต้องแล้ว!

## 🎯 หลักการสำคัญ

**Request Load = Client → CLIProxyAPI (Direct)**
**CCS = เฉพาะตอนดึง OAuth Token เท่านั้น**

---

## 📊 Flow ที่ถูกต้อง (Request Path)

```
┌─────────────────────────────────────────────────────────────┐
│  1. Client (image_gen.py)                                  │
│     python3 image_gen.py "prompt"                          │
└────────────────┬────────────────────────────────────────────┘
                 │
                 │ HTTP POST http://127.0.0.1:8317/v1/chat/completions
                 │ Headers: Authorization: Bearer ccs-internal-managed
                 │ Body: {model, messages, modalities, image_config}
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  2. CLIProxyAPI (Main Request Handler) ✅                    │
│     localhost:8317                                          │
│                                                             │
│  - รับ request โดยตรงจาก client                          │
│  - แปลง format (OpenRouter → Google Imagen)              │
│  - Manage authentication                                    │
└────────────────┬────────────────────────────────────────────┘
                 │
                 │ CLIProxyAPI ต้องการ OAuth token
                 │ เห็น "ccs-internal-managed"
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  3. CCS Token Query (เฉพาะตอนดึง token เท่านั้น) 🔑        │
│     CLIProxyAPI ←→ CCS (internal)                          │
│     Location: ~/.ccs/cliproxy/auth/gemini-*.json          │
│                                                             │
│  - CCS ส่ง OAuth token ให้ CLIProxyAPI                     │
│  - CCS ไม่ได้รับ request load จาก client                  │
└────────────────┬────────────────────────────────────────────┘
                 │
                 │ CLIProxyAPI ได้ OAuth token แล้ว
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  4. CLIProxyAPI → Google Imagen API 🌐                     │
│     POST https://generativelanguage.googleapis.com/...     │
│     Headers: Authorization: Bearer ya29.a0Aa...            │
│     Body: {instances, parameters}                          │
│                                                             │
│  - CLIProxyAPI ส่ง request โดยตรงไป Google               │
│  - ไม่ผ่าน CCS ในขั้นตอนนี้                               │
└────────────────┬────────────────────────────────────────────┘
                 │
                 │ Google ส่ง image กลับมา
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  5. CLIProxyAPI (Response Processing)                      │
│     - แปลง format กลับ (Google → OpenRouter)            │
│     - ส่ง response กลับไป client                          │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  6. Client (image_gen.py)                                  │
│     - รับ base64 image data                               │
│     - บันทึกเป็น PNG file                                 │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎯 สรุปความสำคัญ

| ขั้นตอน | Request ผ่านใคร | ตัวจัดการหลัก |
|---------|-----------------|---------------|
| **1. Client → CLIProxyAPI** | ✅ โดยตรง | **CLIProxyAPI** |
| **2. CLIProxyAPI → CCS** | ❌ Internal query เท่านั้น | CCS (เฉพาะ token) |
| **3. CLIProxyAPI → Google** | ✅ โดยตรง | **CLIProxyAPI** |
| **4. Google → CLIProxyAPI** | ✅ โดยตรง | **CLIProxyAPI** |
| **5. CLIProxyAPI → Client** | ✅ โดยตรง | **CLIProxyAPI** |

---

## 🔑 จุดสำคัญที่ต้องเข้าใจ

### 1. Request Load = CLIProxyAPI
```
Client Request → CLIProxyAPI (Direct)
                    ↓
              CLIProxyAPI จัดการทุกอย่าง
                    ↓
              CLIProxyAPI Response → Client (Direct)
```

### 2. CCS = Token Provider Only
```
CLIProxyAPI ต้องการ token
         ↓
    Query CCS (Internal)
         ↓
CCS ส่ง OAuth token กลับมา
```

### 3. CCS ไม่ได้รับ Request Load
- ❌ CCS ไม่ได้รับ request จาก client โดยตรง
- ❌ CCS ไม่ได้ process request
- ❌ CCS ไม่ได้ translate format
- ✅ CCS จัดการ OAuth tokens เท่านั้น

---

## 📐 สถาปัตยกรรมที่ถูกต้อง

```
┌──────────────────┐
│  image_gen.py    │
│   (Client)       │
└────────┬─────────┘
         │ Request with "ccs-internal-managed"
         ↓
┌──────────────────────────────────────┐
│    CLIProxyAPI (Main Handler)        │
│                                      │
│  1. Receive request                  │
│  2. Translate format                  │
│  3. Query CCS for token ◄───────┐   │
│  4. Call Google API               │   │
│  5. Translate response             │   │
│  6. Return to client               │   │
└──────────────────────────────────┼───┘
                                   │
                    ┌──────────────┴──────────────┐
                    │                             │
         ┌──────────▼──────────┐        ┌────────▼────────┐
         │   CCS (Extension)     │        │  Google Imagen   │
         │                        │        │      API         │
         │  - Token Storage      │        │                  │
         │  - Auto-refresh       │        │  - Generate      │
         │  - OAuth management   │        │    image         │
         └───────────────────────┘        └───────────────────┘
```

---

## ✅ ความเข้าใจที่ถูกต้อง (Final)

1. **CLIProxyAPI** = Main Request Handler + Format Translator
2. **CCS** = Token Provider Only (เฉพาะตอนดึง OAuth token)
3. **Request Load** = Client → CLIProxyAPI → Google API (ไม่ผ่าน CCS)
4. **CCS Role** = Internal token query โดย CLIProxyAPI เท่านั้น

---

## 🚀 Flow สั้นๆ ที่จำ

```
User → image_gen.py → CLIProxyAPI → Google API
                         ↑
                         └→ CCS (เฉพาะตอนดึง token)
```

**คำสำคัญ**: Request **ไม่ผ่าน CCS** แต่ CLIProxyAPI **ดึง token จาก CCS** เองครับ! 🎯
