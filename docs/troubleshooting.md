# Troubleshooting - AI Voice Agent

## 1. Widget does not load / conversation cuts off

**Symptom:** ElevenLabs widget does not render or conversation dies after 1 question.

**Error:** `Cannot read properties of null (reading 'FRAGMENT_SHADER')`

**Root cause:** Brave browser blocks WebGL (GPU not detected, VENDOR=0x0000).

**Solution:** Use Chromium or Chrome instead of Brave.

---

## 2. Webhook returns 403 / "Authorization data is wrong!"

**Symptom:** Agent cannot query products or create orders. Tools fail silently.

**Error:** `Authorization data is wrong!` when calling `/webhook/my-store/products`

**Root cause:** ngrok was pointing to the wrong port (5679 instead of 5678).

**Solution:**
1. Verify n8n is running on port 5678
2. ngrok must point to the same port: `ngrok http 5678`
3. Auth must go via header, NOT query param: `x-api-key: <your-key>`

**Quick test:**
```bash
# Should return 200
curl -H "x-api-key: YOUR_KEY" http://localhost:5678/webhook/my-store/products
```

---

## 3. Pre-flight checklist

- [ ] n8n running on port 5678
- [ ] json-server running on port 3000
- [ ] ngrok pointing to 5678
- [ ] Workflow activated in n8n
- [ ] Using Chromium (not Brave)
