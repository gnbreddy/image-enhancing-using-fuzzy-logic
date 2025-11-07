# Qwen2-VL Troubleshooting Guide

## Issue: "AI enhancer didn't use the LLM"

### Quick Checks:

1. **Open Browser Console** (F12 or Right-click → Inspect → Console)
   - Look for error messages
   - Check what the console logs say

2. **Verify LM Studio Server**
   - Open LM Studio
   - Go to "Local Server" tab
   - Make sure it says "Server Running" with green indicator
   - Check the port number (should be 1234)

3. **Test the Connection**
   - In the AI Enhancer page, click "Test Connection"
   - Should show "✅ Connected successfully!"
   - If not, check the error message

---

## Common Issues & Solutions:

### 1. CORS Error
**Error:** `Access to fetch at 'http://127.0.0.1:1234' has been blocked by CORS policy`

**Solution:**
- In LM Studio, go to Settings
- Enable "CORS" or "Allow Cross-Origin Requests"
- Restart the server

### 2. Model Not Loaded
**Error:** `No model loaded` or `Model not found`

**Solution:**
- In LM Studio, make sure Qwen2-VL is loaded
- Look for the model name in the top bar
- If not loaded, click on your model to load it

### 3. Wrong Endpoint
**Error:** `404 Not Found` or `Cannot POST /v1/chat/completions`

**Solution:**
- Check LM Studio version (needs v0.2.0+)
- Update LM Studio if needed
- Verify the endpoint in LM Studio settings

### 4. Image Not Sent
**Error:** `Image data missing` or `Invalid image format`

**Solution:**
- Make sure you're using a vision-capable model (Qwen2-VL)
- Check that the model supports image inputs
- Try a smaller image (< 2MB)

---

## Step-by-Step Debugging:

### Step 1: Test LM Studio Directly

Open a new browser tab and go to:
```
http://127.0.0.1:1234/v1/models
```

**Expected:** JSON response with model list
**If error:** LM Studio server is not running

### Step 2: Check Browser Console

1. Open your AI Enhancer page
2. Press F12 to open Developer Tools
3. Go to "Console" tab
4. Click "Test Connection"
5. Look for logs:
   ```
   Testing connection to: http://127.0.0.1:1234
   Response status: 200
   Available models: {...}
   ```

### Step 3: Upload Image and Check Logs

1. Upload an image
2. Watch the console for:
   ```
   Starting Qwen analysis...
   Server URL: http://127.0.0.1:1234
   Sending request to Qwen...
   Response status: 200
   Qwen response: {...}
   ```

### Step 4: Check Network Tab

1. In Developer Tools, go to "Network" tab
2. Upload an image
3. Look for request to `/v1/chat/completions`
4. Click on it to see:
   - Request headers
   - Request payload (should include image)
   - Response

---

## LM Studio Configuration:

### Required Settings:

1. **Server Tab:**
   - ✅ Server Running (green)
   - ✅ Port: 1234
   - ✅ CORS: Enabled

2. **Model:**
   - ✅ Qwen2-VL 8B loaded
   - ✅ Vision capabilities enabled

3. **Advanced Settings:**
   - Max Tokens: 2000+
   - Temperature: 0.7
   - Context Length: 4096+

---

## Testing with cURL:

Test if LM Studio is working:

```bash
curl http://127.0.0.1:1234/v1/models
```

Should return:
```json
{
  "data": [
    {
      "id": "qwen2-vl-8b",
      ...
    }
  ]
}
```

---

## Alternative: Test with Simple Request

Add this to your browser console:

```javascript
fetch('http://127.0.0.1:1234/v1/chat/completions', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    messages: [{
      role: 'user',
      content: 'Hello, are you working?'
    }],
    max_tokens: 100
  })
})
.then(r => r.json())
.then(d => console.log('Response:', d))
.catch(e => console.error('Error:', e));
```

---

## If Nothing Works:

### Fallback Mode:
The AI Enhancer has a fallback mode that works without AI:
- It analyzes the image using basic algorithms
- Still provides good enhancement results
- Activates automatically if Qwen fails

### Check These:

1. **LM Studio Version:**
   - Minimum: v0.2.0
   - Recommended: Latest version
   - Update from: https://lmstudio.ai/

2. **Model Compatibility:**
   - Qwen2-VL supports vision ✅
   - Qwen2 (non-VL) does NOT support vision ❌
   - Make sure you have the VL (Vision-Language) version

3. **Browser:**
   - Chrome/Edge: ✅ Works
   - Firefox: ✅ Works
   - Safari: ⚠️ May have CORS issues

---

## Success Indicators:

When everything is working, you should see:

1. ✅ Green "Connected to Qwen2-VL" status
2. ✅ Console logs showing successful requests
3. ✅ AI analysis appears after 10-30 seconds
4. ✅ Detailed recommendations with percentages
5. ✅ Enhanced image appears after clicking "Apply"

---

## Still Having Issues?

### Share These Details:

1. LM Studio version
2. Qwen model exact name
3. Browser console errors (screenshot)
4. Network tab screenshot
5. LM Studio server logs

### Quick Test:

Run this in browser console on the AI Enhancer page:
```javascript
console.log('Server URL:', serverUrl);
console.log('Is Connected:', isConnected);
testConnection();
```

This will help diagnose the issue!
