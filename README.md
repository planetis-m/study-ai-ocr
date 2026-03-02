# Study-AI Qwen Code Extension

## Overview
The extension helps students prepare for exams by processing lecture PDF slides. It uses the `pdfocr` vision model tool to natively read slides and generate structured study notes, quizzes, and essay questions.

## Setup Instructions

### 1. Install Required Packages

```bash
sudo apt-get update
sudo apt-get install -y npm nodejs uv git
```

### 2. Install an AI CLI (Choose One)

#### Option A: Qwen Code

```bash
npm install -g @qwen-code/qwen-code@latest
qwen --version
```

**Optional: Use OpenRouter**

Edit `~/.qwen/.env`:
```bash
OPENAI_API_KEY=your_openrouter_api_key
OPENAI_BASE_URL=https://openrouter.ai/api/v1
OPENAI_MODEL=x-ai/grok-4-fast
```

Edit `~/.qwen/settings.json`:
```json
{
  "selectedAuthType": "openai"
}
```

Get your API key: https://openrouter.ai/keys
Browse models: https://openrouter.ai/models

#### Option B: Gemini CLI

```bash
npm install -g @google/gemini-cli@latest
gemini --version
```

### 3. Set Up the Study-AI Extension

**For Qwen:**
```bash
qwen extensions install https://github.com/planetis-m/study-ai-ocr.git
```

**For Gemini:**
```bash
gemini extensions install https://github.com/planetis-m/study-ai-ocr.git
```

### 4. Enable PDF Processing (pdfocr)

PDF processing relies on `@planetis-m/pdfocr`, which uses DeepInfra's olmOCR model to read slides.

1. **Install Runtime Dependencies**:
   - **Linux**: `sudo apt install -y libcurl4 libwebp-dev`
   - **macOS**: `brew install curl webp`

2. **Download pdfocr**:
   - Download the latest release asset for your platform from: https://github.com/planetis-m/pdfocr/releases/latest
   - Extract the archive and place the `pdfocr` executable (along with the bundled `libpdfium` library) in your workspace or system PATH.

3. **Set DeepInfra API Key**:
   Since `pdfocr` utilizes the olmOCR model via API, you must export your key:
   ```bash
   export DEEPINFRA_API_KEY=your_deepinfra_api_key
   ```
   *(Alternatively, you can place a `config.json` next to the `pdfocr` executable).*

### 5. Run Your Chosen CLI

#### If using Qwen Code:
```bash
qwen
```

#### If using Gemini CLI:
```bash
gemini
```

Then run `/help` to confirm that the `study-ai` commands are available.

## Usage Examples

Your `<workspace>` is the folder where you store your lecture slides.

```bash
cd <workspace>

# Extract text from a single PDF
/study-ai:extract lecture1.pdf

# Extract specific page ranges
/study-ai:extract lecture1.pdf --pages "8-20,22-27" > lecture1.md

# Combine multiple sources into one study file (use >> to append)
/study-ai:extract lecture2_part1.pdf --pages "1-20" > study_guide.md
/study-ai:extract lecture2_part2.pdf --pages "5-10" >> study_guide.md

# Analyze already-extracted text content
/study-ai:analyze "Paste your extracted slide content here..."

# Generate essay questions
/study-ai:essay "Paste content here..."

# Complete pipeline (extract -> analyze)
/study-ai:study-notes lecture1.pdf

# Generate practice quizzes
/study-ai:quiz "Paste your slide content here..."

# Analyze extracted text
/study-ai:analyze lecture1.md
```

## Extension Directory Structure

```
study-ai/
├── AGENTS.md              # Context file with assistant behavior
├── gemini-extension.json  # Gemini extension config
├── qwen-extension.json    # Qwen extension config
└── commands/
    └── study-ai/
        ├── extract.toml      # Basic preprocessing command using pdfocr
        ├── analyze.toml      # Analysis command
        ├── study-notes.toml  # Complete pipeline command
        ├── quiz.toml         # Quiz command
        └── essay.toml        # Essay questions command
```

## Key Design Decisions

1. **Separation of Concerns**: Commands are separated into discrete stages (extract, analyze) while also providing a convenient combined command (study-notes).
2. **Flexible Input**: Users can either pass PDF files directly or paste pre-extracted text for analysis.
3. **Context-Driven Behavior**: The `AGENTS.md` file establishes the assistant's role and behavior patterns, which commands reinforce through their specific prompts.
4. **Integration with pdfocr**: Commands leverage the `@planetis-m/pdfocr` CLI tool for highly accurate, vision-based text extraction and layout preservation, streamlining the extraction process.
5. **Exam-Focused**: All prompts emphasize exam preparation, key topics, and deep understanding rather than general summarization.

## License
This project is licensed under **Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)**.
