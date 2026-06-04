# LegalEase - Startup Guide

## Prerequisites
1. **Ollama must be running** with the `legalease:latest` model
2. **MongoDB must be running** (localhost:27017)
3. **Python virtual environment** (`venv`) must be activated for backend
4. **Tesseract OCR** (required for image OCR and scanned PDF processing)
   - **Windows:** Download from [UB Mannheim Tesseract](https://github.com/UB-Mannheim/tesseract/wiki)
   - Install to default location: `C:\Program Files\Tesseract-OCR\`
   - Add Tesseract to your `.env` file:
     ```
     TESSERACT_CMD=C:\Program Files\Tesseract-OCR\tesseract.exe
     ```
   - If installed elsewhere, update the path in `.env`

## Quick Start

### Option 1: Using Batch Files (Easiest)

**Terminal 1 - Backend:**
```bash
START_BACKEND.bat
```

**Terminal 2 - Frontend:**
```bash
START_FRONTEND.bat
```

### Option 2: Manual Commands

**Terminal 1 - Flask Backend (with venv):**
```bash
# Activate virtual environment
venv\Scripts\activate

# Navigate to Flask directory
cd LegalEase_Flask

# Run Flask server
python start_server.py
```

**Terminal 2 - Next.js Frontend (no venv needed):**
```bash
# Navigate to frontend directory
cd legalease-frontend

# Run Next.js dev server
npm run dev
```

## Services Overview

| Service | URL | Requires venv? |
|---------|-----|----------------|
| Flask Backend | http://127.0.0.1:5000 | ✅ Yes |
| Next.js Frontend | http://localhost:3000 | ❌ No |
| LegalEase API | http://localhost:11434 | N/A |

## Verify Everything is Running

1. **Check MongoDB:**
   ```bash
   # MongoDB should be running on localhost:27017
   # Check connection from Flask directory:
   python verify_cache_config.py
   ```

2. **Check Ollama:**
   ```bash
   ollama list
   # Should show: legalease:latest
   ```

3. **Check Backend:**
   ```bash
   curl http://127.0.0.1:5000/health
   # Should return: {"status":"ok"}
   
   # Check cache stats:
   curl http://127.0.0.1:5000/cache/stats
   ```

4. **Check Frontend:**
   - Open browser: http://localhost:3000

## Common Issues

**Ollama not running:**
```bash
# Start Ollama (will run in background)
ollama serve
# OR open interactive mode
ollama run legalease:latest
```

**MongoDB not running:**
- Start MongoDB service from Windows Services
- Or start MongoDB manually

**Port already in use:**
- Backend (5000): Check if another Flask app is running
- Frontend (3000): Next.js will auto-increment to 3001, 3002, etc.

## Recent Updates

### ✅ Cache System (400-Day Global Cache)
- **Cache TTL**: 400 days (13+ months) - Extended from 7 days
- **Global Caching**: Shared across ALL users (no session isolation)
- **Cached Services**: Text queries, PDF analysis, Image OCR, CNR lookups
- **Storage**: MongoDB (`law_gpt_db.query_cache` collection)
- **Verify Cache**: Run `python verify_cache_config.py` in Flask directory

### 📁 File Upload Improvements
- **Same File Re-upload**: Fixed - can now upload same image/PDF multiple times
- **Caching Enabled**: PDF and Image uploads now use global cache
- **Performance**: Second upload of identical file returns instantly from cache

## Stop Services

- Press `Ctrl + C` in each terminal window
- Or close the terminal windows

## Troubleshooting

**Cache not working?**
```bash
# Restart Flask to apply TTL index changes
Ctrl + C (in backend terminal)
python start_server.py

# Verify cache configuration
python verify_cache_config.py
```

**Can't upload same file twice?**
- Make sure you've restarted the Next.js frontend (`npm run dev`)
- Clear browser cache if issue persists
