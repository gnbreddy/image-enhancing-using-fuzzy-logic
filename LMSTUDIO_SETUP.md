# LM Studio Integration Guide

## 🖥️ How to Integrate Your Local LLM with the Project

### Prerequisites
- ✅ LM Studio installed
- ✅ A vision-capable model downloaded (LLaVA, BakLLaVA, etc.)

---

## Step 1: Setup LM Studio

### Download Vision Model
1. Open LM Studio
2. Go to "Search" tab
3. Search for vision models:
   - **LLaVA 1.5** (Recommended) - 7B or 13B
   - **BakLLaVA** - Good for image analysis
   - **LLaVA 1.6** - Latest version
4. Download your preferred model

### Start Local Server
1. Go to "Local Server" tab in LM Studio
2. Select your vision model
3. Click "Start Server"
4. Default URL: `http://localhost:1234`
5. Keep LM Studio running!

---

## Step 2: Update Your HTML File

Add this JavaScript code to connect to LM Studio:

```javascript
const LMSTUDIO_URL = 'http://localhost:1234';

async function analyzeWithLMStudio(imageDataUrl) {
    const base64Image = imageDataUrl.split(',')[1];
    
    const response = await fetch(`${LMSTUDIO_URL}/v1/chat/completions`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
            model: 'local-model', // LM Studio auto-detects
            messages: [
                {
                    role: 'user',
                    content: [
                        { 
                            type: 'text', 
                            text: 'Analyze this image quality. What enhancements does it need?' 
                        },
                        { 
                            type: 'image_url', 
                            image_url: { url: imageDataUrl } 
                        }
                    ]
                }
            ],
            max_tokens: 500,
            temperature: 0.7
        })
    });

    const data = await response.json();
    return data.choices[0].message.content;
}
```

---

## Step 3: Integration Code

### Complete Integration Example:

```html
<script>
// Test connection to LM Studio
async function testLMStudioConnection() {
    try {
        const response = await fetch('http://localhost:1234/v1/models');
        if (response.ok) {
            console.log('✅ Connected to LM Studio');
            return true;
        }
    } catch (error) {
        console.error('❌ LM Studio not running');
        return false;
    }
}

// Analyze image with local LLM
async function analyzeImage(imageDataUrl) {
    const prompt = `Analyze this image and provide recommendations in JSON format:
{
  "contrast": 0-100,
  "noise": 0-100,
  "sharpness": 0-100,
  "brightness": -50 to 50,
  "recommendations": ["recommendation1", "recommendation2"]
}`;

    const response = await fetch('http://localhost:1234/v1/chat/completions', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
            messages: [{
                role: 'user',
                content: [
                    { type: 'text', text: prompt },
                    { type: 'image_url', image_url: { url: imageDataUrl } }
                ]
            }],
            max_tokens: 1000
        })
    });

    const data = await response.json();
    const aiResponse = data.choices[0].message.content;
    
    // Parse JSON from response
    const jsonMatch = aiResponse.match(/\{[\s\S]*\}/);
    return jsonMatch ? JSON.parse(jsonMatch[0]) : null;
}

// Use in your image upload handler
document.getElementById('image-upload').addEventListener('change', async (e) => {
    const file = e.target.files[0];
    const reader = new FileReader();
    
    reader.onload = async (event) => {
        const imageDataUrl = event.target.result;
        
        // Check LM Studio connection
        const isConnected = await testLMStudioConnection();
        if (!isConnected) {
            alert('Please start LM Studio server first!');
            return;
        }
        
        // Analyze with local AI
        const analysis = await analyzeImage(imageDataUrl);
        console.log('AI Analysis:', analysis);
        
        // Apply enhancements based on AI recommendations
        applyEnhancements(analysis);
    };
    
    reader.readAsDataURL(file);
});
</script>
```

---

## Step 4: Update index.html

Change the AI Auto Enhancer link to use LM Studio version:

```html
<div onclick="window.location.href='ai_enhancer_lmstudio.html'">
    <h2>AI Auto Enhancer</h2>
    <p>Uses your local LM Studio model</p>
</div>
```

---

## Advantages of LM Studio

✅ **Completely FREE** - No API costs
✅ **Privacy** - Everything runs locally
✅ **No Internet Required** - Works offline
✅ **No Rate Limits** - Use as much as you want
✅ **Fast** - Depends on your hardware

---

## Recommended Models for Image Analysis

### Best Options:
1. **LLaVA 1.5 7B** - Fast, good accuracy
2. **LLaVA 1.5 13B** - Better accuracy, slower
3. **BakLLaVA** - Optimized for image tasks

### Download Links in LM Studio:
- Search: "llava"
- Filter: "Vision" models
- Sort by: Downloads

---

## Troubleshooting

### "Connection Failed"
- ✅ Make sure LM Studio is running
- ✅ Check server is started (green indicator)
- ✅ Verify URL is `http://localhost:1234`
- ✅ Try restarting LM Studio

### "Model Not Responding"
- ✅ Load a vision-capable model
- ✅ Wait for model to fully load
- ✅ Check LM Studio console for errors

### "Slow Response"
- ✅ Use smaller model (7B instead of 13B)
- ✅ Reduce max_tokens to 500
- ✅ Close other applications
- ✅ Consider GPU acceleration

---

## Performance Tips

### For Faster Processing:
1. Use GPU if available (CUDA/Metal)
2. Use 7B models instead of 13B
3. Reduce image resolution before sending
4. Set max_tokens to 500-1000
5. Use temperature 0.7 for consistent results

### Hardware Requirements:
- **Minimum:** 8GB RAM, CPU only
- **Recommended:** 16GB RAM, GPU with 6GB+ VRAM
- **Optimal:** 32GB RAM, GPU with 12GB+ VRAM

---

## Example Prompt for Best Results

```javascript
const prompt = `Analyze this image for quality issues. Provide specific recommendations.

Evaluate:
1. Contrast levels (too dark/bright/balanced?)
2. Noise levels (visible grain/artifacts?)
3. Edge quality (sharp/blurry?)
4. Overall brightness

Respond in JSON:
{
  "analysis": "brief assessment",
  "issues": ["issue1", "issue2"],
  "recommendations": {
    "noise_reduction": {"apply": true/false, "strength": 0-100},
    "contrast": {"apply": true/false, "strength": 0-100},
    "edges": {"apply": true/false, "strength": 0-100},
    "brightness": {"apply": true/false, "adjustment": -50 to 50}
  }
}`;
```

---

## Next Steps

1. ✅ Install LM Studio
2. ✅ Download a vision model (LLaVA recommended)
3. ✅ Start the local server
4. ✅ Update your HTML file with the integration code
5. ✅ Test with an image!

Your local AI is now ready to analyze images and provide intelligent enhancement recommendations - completely free and private!
