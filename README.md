# Prompt Engineering Setup with Google Gemini API

A complete guide to set up a prompt engineering learning environment using Google Gemini API with Python, following [promptingguide.ai](https://www.promptingguide.ai/) tutorials.

## 📋 Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Security: Rotating Your API Key](#security-rotating-your-api-key)
- [Detailed Setup](#detailed-setup)
- [Usage Examples](#usage-examples)
- [Available Models](#available-models)
- [Troubleshooting](#troubleshooting)
- [Learning Resources](#learning-resources)
- [Project Files](#project-files)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This repository provides a complete setup for learning prompt engineering with:

- **Google Gemini API** (free student tier with unlimited tokens until June 2026)
- **Isolated Conda environment** (no dependency conflicts with existing projects)
- **Full parameter control** (temperature, top_p, max_tokens)
- **Production-ready templates** with error handling
- **40+ Gemini models** available for experimentation

Perfect for data engineers, developers, and anyone learning prompt engineering techniques.

## ✅ Prerequisites

- **macOS** or **Linux** (Windows with WSL2 works too)
- **Conda** or **Miniconda** installed
- **Python 3.11+**
- **Google account** (student account recommended for unlimited tokens)

## 🚀 Quick Start

```bash
# 1. Clone this repository
git clone https://github.com/yourusername/prompt-engineering-setup.git
cd prompt-engineering-setup

# 2. Create conda environment
conda create -n prompt-eng python=3.11 -y
conda activate prompt-eng

# 3. Install dependencies
pip install -r requirements.txt

# 4. Set up API key (choose one method)
# Method A: Shell environment variable
export GEMINI_API_KEY="your-api-key-here"

# Method B: .env file (recommended for projects)
cp .env.example .env
# Edit .env and add your API key

# 5. Test the setup
python test_gemini.py
# Or use secure test with .env file
python secure_test_gemini.py
```

> **💡 Tip:** For permanent setup, see [Step 3](#step-3-configure-api-key-permanent-setup). If you exposed your key, see [Security: Rotating Your API Key](#security-rotating-your-api-key).

## 🔒 Security: Rotating Your API Key

**Important:** If you accidentally exposed your API key (e.g., committed to GitHub, shared publicly), follow these steps immediately:

### 1. Generate a New API Key

1. Visit [Google AI Studio API Keys](https://aistudio.google.com/app/apikey)
2. Click on your existing key
3. Click **"Revoke"** or **"Delete"** to invalidate the old key
4. Click **"Create API Key"** to generate a new one
5. Copy the new key (starts with `AIzaSy...`)

### 2. Update Your Local Configuration

Open a terminal and run:

```bash
# Edit your .zshrc or appropriate shell config
nano ~/.zshrc
```

- Find the old line: `export GEMINI_API_KEY="old-key-value"`
- Replace with your new key: `export GEMINI_API_KEY="YOUR_NEW_KEY_HERE"`
- Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`)

```bash
# Reload your shell environment
source ~/.zshrc
```

To confirm:

```bash
echo $GEMINI_API_KEY
```

Should print only your new key.

### 3. (Optional) Remove Old Key from Shell Session

If you started Python anywhere before running `source ~/.zshrc`, restart that terminal or run:

```bash
unset GEMINI_API_KEY
source ~/.zshrc
```

### 4. Test Your Setup

```bash
python test_gemini.py
```

You should see the success message with your new key working properly.

### 5. Security Best Practices

- ✅ **Never commit API keys** to GitHub or version control
- ✅ **Use `.gitignore`** to exclude config files with keys
- ✅ **Use environment variables** instead of hardcoding keys
- ✅ **Rotate keys regularly** if used in production
- ✅ **Use `.env` files** for project-specific keys (add to `.gitignore`)

**Example `.gitignore`:**
```
.env
*.key
*_config.py
.zshrc_backup
```

> **📖 For comprehensive security guidelines, see [SECURITY.md](SECURITY.md)**

---

## 📚 Detailed Setup

### Step 1: Get Your Gemini API Key

1. Visit [Google AI Studio](https://aistudio.google.com/app/apikey)
2. Sign in with your Google account (use student account for unlimited tokens)
3. Click **"Create API Key"**
4. Copy the generated key (starts with `AIzaSy...`)

### Step 2: Create Isolated Environment

```bash
# Create new conda environment
conda create -n prompt-eng python=3.11 -y

# Activate environment
conda activate prompt-eng

# Install all dependencies from requirements.txt
pip install -r requirements.txt

# Or install individually
pip install google-generativeai python-dotenv
```

**Why isolated environment?**
- No conflicts with existing packages (dbt, airflow, etc.)
- Clean dependency management
- Easy to replicate across machines

### Step 3: Configure API Key (Permanent Setup)

> **⚠️ Security Warning:** Never commit your API key to version control. Keep it in shell config files that are not tracked by git.

#### For macOS/Linux (zsh shell):

```bash
# Open .zshrc
nano ~/.zshrc

# Add this line at the end (replace with your actual key)
export GEMINI_API_KEY="AIzaSyDf3N8xK2Qp9mL5vR7tW9jH4gB6cZ1aQ9E"

# Save (Ctrl+O, Enter, Ctrl+X) and reload
source ~/.zshrc

# Verify
echo $GEMINI_API_KEY
```

#### For bash shell:

Replace `~/.zshrc` with `~/.bashrc` in the above commands.

#### Alternative: Using .env File (Recommended for Projects)

For project-specific configuration, you can use a `.env` file:

```bash
# 1. Install python-dotenv
pip install python-dotenv

# 2. Copy the example file
cp .env.example .env

# 3. Edit .env and add your API key
nano .env

# 4. Use the secure test script
python secure_test_gemini.py
```

**Benefits of .env approach:**
- ✅ Project-specific keys (different keys for different projects)
- ✅ Easier to manage in teams (share .env.example, not .env)
- ✅ Automatically excluded by .gitignore
- ✅ No shell configuration needed

### Step 4: Create Test File

Create `test_gemini.py`:

```python
import os
import google.generativeai as genai
from google.generativeai.types import HarmCategory, HarmBlockThreshold

# Configure API key
api_key = os.environ.get("GEMINI_API_KEY")
if not api_key:
    print("ERROR: GEMINI_API_KEY not found.")
    exit(1)

genai.configure(api_key=api_key)

# Lenient safety settings for learning
safety_settings = {
    HarmCategory.HARM_CATEGORY_HATE_SPEECH: HarmBlockThreshold.BLOCK_NONE,
    HarmCategory.HARM_CATEGORY_HARASSMENT: HarmBlockThreshold.BLOCK_NONE,
    HarmCategory.HARM_CATEGORY_SEXUALLY_EXPLICIT: HarmBlockThreshold.BLOCK_NONE,
    HarmCategory.HARM_CATEGORY_DANGEROUS_CONTENT: HarmBlockThreshold.BLOCK_NONE,
}

# Create model
model = genai.GenerativeModel('models/gemini-2.5-flash')

# Test with simple prompt
response = model.generate_content(
    "Explain the concept of temperature in large language models in 3 sentences.",
    generation_config=genai.types.GenerationConfig(
        temperature=1.0,
        top_p=0.9,
        max_output_tokens=1024,
    ),
    safety_settings=safety_settings
)

print("✓ Connection successful!")
print(f"\nResponse:\n{response.text}")
print("\n✅ Setup complete! Ready for prompt engineering learning.")
```

### Step 5: Run Test

```bash
python test_gemini.py
```

**Expected output:**

```
✓ Connection successful!

Response:
Temperature is a hyperparameter used during text generation in large language models...

✅ Setup complete! Ready for prompt engineering learning.
```

## 💻 Usage Examples

### Basic Prompt

```python
import os
import google.generativeai as genai

genai.configure(api_key=os.environ.get("GEMINI_API_KEY"))
model = genai.GenerativeModel('models/gemini-2.5-flash')

response = model.generate_content("What is machine learning?")
print(response.text)
```

### With Parameters (Following promptingguide.ai)

```python
response = model.generate_content(
    "Explain neural networks",
    generation_config=genai.types.GenerationConfig(
        temperature=1.0,  # 0.0 = deterministic, 2.0 = creative
        top_p=0.9,        # Nucleus sampling threshold
        max_output_tokens=1024,
    )
)
```

### Zero-Shot Prompting

```python
prompt = """
Classify the text into neutral, negative or positive.

Text: I think the vacation is okay.
Sentiment:
"""
response = model.generate_content(prompt)
print(response.text)
```

### Few-Shot Prompting

```python
prompt = """
This is awesome! // Positive
This is bad! // Negative
Wow that movie was rad! // Positive
What a horrible show! //
"""
response = model.generate_content(prompt)
print(response.text)
```

### Chain-of-Thought (CoT)

```python
prompt = """
Let's solve step by step:

If 5 apples cost $10, how much do 8 apples cost?

Step 1: Find cost per apple
Step 2: Multiply by 8
Step 3: Final answer

Solution:
"""
response = model.generate_content(prompt)
print(response.text)
```

### Temperature Comparison

```python
creative_prompt = "Write a tagline for an ice cream shop."

# Deterministic (temperature=0.0)
response1 = model.generate_content(
    creative_prompt,
    generation_config=genai.types.GenerationConfig(temperature=0.0)
)

# Creative (temperature=1.5)
response2 = model.generate_content(
    creative_prompt,
    generation_config=genai.types.GenerationConfig(temperature=1.5)
)
```

## 🤖 Available Models

### Recommended for Learning

| Model | Speed | Quality | Context Window | Best For |
|-------|-------|---------|----------------|----------|
| `models/gemini-2.5-flash` | ⚡ Fast | ⭐⭐⭐⭐⭐ | 1M tokens | General learning (recommended) |
| `models/gemini-2.5-pro` | 🐢 Slower | ⭐⭐⭐⭐⭐ | 1M tokens | Complex reasoning |
| `models/gemini-2.0-flash` | ⚡ Fast | ⭐⭐⭐⭐ | 1M tokens | Quick experiments |
| `models/gemini-2.0-flash-lite` | ⚡⚡ Fastest | ⭐⭐⭐ | 128K tokens | Ultra-fast tests |

### List All Available Models

```python
import google.generativeai as genai

genai.configure(api_key=os.environ.get("GEMINI_API_KEY"))

for model in genai.list_models():
    if 'generateContent' in model.supported_generation_methods:
        print(f"✓ {model.name}")
        print(f"  Display Name: {model.display_name}")
```

## 🛠️ Troubleshooting

### Error: "Cannot import name 'genai'"

**Wrong:**
```python
from google import genai  # ❌ This doesn't work
```

**Correct:**
```python
import google.generativeai as genai  # ✅ Use this
```

### Error: "GEMINI_API_KEY not found"

```bash
# Check if key is set
echo $GEMINI_API_KEY

# If empty, add to shell config
echo 'export GEMINI_API_KEY="your-key-here"' >> ~/.zshrc
source ~/.zshrc
```

### Error: "429 Quota exceeded"

You hit the rate limit (5 requests per minute for free tier). Solutions:
- Wait 60 seconds before next request
- Verify student account for higher limits
- Monitor usage at https://ai.dev/usage

### Error: "404 Model not found"

Use full model path:

```python
# Wrong
model = genai.GenerativeModel('gemini-1.5-flash')

# Correct
model = genai.GenerativeModel('models/gemini-1.5-flash')
```

### Response Blocked by Safety Filters

```python
from google.generativeai.types import HarmCategory, HarmBlockThreshold

safety_settings = {
    HarmCategory.HARM_CATEGORY_HATE_SPEECH: HarmBlockThreshold.BLOCK_NONE,
    HarmCategory.HARM_CATEGORY_HARASSMENT: HarmBlockThreshold.BLOCK_NONE,
    HarmCategory.HARM_CATEGORY_SEXUALLY_EXPLICIT: HarmBlockThreshold.BLOCK_NONE,
    HarmCategory.HARM_CATEGORY_DANGEROUS_CONTENT: HarmBlockThreshold.BLOCK_NONE,
}

response = model.generate_content(prompt, safety_settings=safety_settings)
```

### Protobuf Conflicts with dbt/Airflow

If you see protobuf version conflicts, you're mixing environments. Solution:

```bash
# Always use the isolated environment
conda activate prompt-eng

# Verify you're in the right environment
echo $CONDA_DEFAULT_ENV  # Should show: prompt-eng
```

## 📖 Learning Resources

### Official Documentation
- [Google Gemini API Docs](https://ai.google.dev/gemini-api/docs)
- [Prompt Engineering Guide](https://www.promptingguide.ai/)
- [Google AI Studio](https://aistudio.google.com/)

### Recommended Learning Path

1. **Week 1**: Basic techniques (Zero-shot, Few-shot)
2. **Week 2**: Chain-of-Thought, Role-based prompting
3. **Week 3**: Advanced techniques (Self-consistency, Tree of Thoughts)
4. **Week 4**: Apply to your domain (data engineering, content creation)

### Key Concepts

#### Temperature (0.0 - 2.0)
- `0.0`: Deterministic, consistent responses
- `1.0`: Balanced (default, matches promptingguide.ai)
- `2.0`: Very creative, unpredictable

#### Top-p (0.0 - 1.0)
- Nucleus sampling threshold
- `0.9`: Use tokens that make up 90% of probability mass
- Lower = more focused, Higher = more diverse

#### Max Output Tokens
- Maximum length of generated response
- `1024`: Good default for learning
- Adjust based on your needs

## 🎓 Student Account Benefits

If you have a Google student account:

- ✅ **Unlimited tokens** until June 30, 2026
- ✅ **Access to all models** including latest experimental ones
- ✅ **No credit card required**
- ✅ **Higher rate limits**

**To verify student status:**
1. Visit https://aistudio.google.com/
2. Sign in with your .edu email or student-verified Google account
3. Check pricing page for "Student Free Tier" badge

## 📁 Project Files

This repository includes several helper files for secure setup:

| File | Purpose |
|------|---------|
| `test_gemini.py` | Basic API connection test |
| `secure_test_gemini.py` | Secure test using .env file configuration |
| `.env.example` | Template for environment variables (copy to `.env`) |
| `.gitignore` | Prevents committing sensitive files to git |

**Quick security setup:**

```bash
# Copy example files
cp .env.example .env

# Edit .env with your API key
nano .env

# Install dotenv for secure loading
pip install python-dotenv

# Test secure configuration
python secure_test_gemini.py
```

## 🤝 Contributing

Contributions welcome! Please:

1. Fork the repository
2. Create feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open Pull Request

## 📄 License

This project is licensed under the MIT License - see LICENSE file for details.

## 🙏 Acknowledgments

- [Prompt Engineering Guide](https://www.promptingguide.ai/) - Comprehensive learning resource
- [Google Gemini API](https://ai.google.dev/) - Powerful LLM access
- Data engineering community for inspiration

## 📧 Contact

For questions or discussions:
- Open an issue on GitHub
- Connect on [LinkedIn](https://www.linkedin.com/in/sumanth-malipeddi/)
- Follow on [Instagram](https://www.instagram.com/the.intlovert?igsh=M2J0NzYzaG9oMjlk&utm_source=qr) for data engineering content

---

**⭐ If this helped you, please star the repository!**

Made with ❤️ for the prompt engineering community
