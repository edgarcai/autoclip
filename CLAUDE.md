# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 🎯 Project Overview

AutoClip is an AI-powered intelligent video clipping system that downloads videos from YouTube/Bilibili, analyzes them using AI (Qwen LLM), and automatically generates exciting clips and collections. The system uses a modern full-stack architecture with React frontend and FastAPI backend, backed by Celery task queues.

## 📋 Common Development Commands

### Backend Development

```bash
# Activate virtual environment
source venv/bin/activate

# Set Python path
export PYTHONPATH="${PWD}:${PYTHONPATH}"

# Run backend directly
python -m uvicorn backend.main:app --reload --port 8000

# Install Python dependencies
pip install -r requirements.txt

# Run Celery worker
celery -A backend.core.celery_app worker --loglevel=info

# Run Celery Beat scheduler
celery -A backend.core.celery_app beat --loglevel=info

# Run Flower monitoring
celery -A backend.core.celery_app flower --port=5555

# Run tests
pytest backend/tests/ -v

# Run single test file
pytest backend/tests/test_specific.py -v
```

### Frontend Development

```bash
# Navigate to frontend directory
cd frontend

# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Lint code
npm run lint

# Preview production build
npm run preview
```

### Docker Development

```bash
# Start all services (production)
./docker-start.sh

# Start development environment
./docker-start.sh dev

# Stop all services
./docker-stop.sh

# Check service status
./docker-status.sh

# Using docker-compose directly
docker-compose up -d
docker-compose logs -f
```

### One-Click Local Startup

```bash
# Full startup with checks
./start_autoclip.sh

# Quick startup (development)
./quick_start.sh

# Check system status
./status_autoclip.sh

# Stop system
./stop_autoclip.sh
```

## 🏗️ System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    React Frontend                        │
│              (Port 3000 - Vite + TypeScript)            │
└────────────────────┬────────────────────────────────────┘
                     │ HTTP/WebSocket
┌────────────────────▼────────────────────────────────────┐
│               FastAPI Backend                           │
│                (Port 8000)                              │
│  ┌──────────────┬──────────────┬──────────────────────┐ │
│  │   Projects   │   Video      │   AI Pipeline        │ │
│  │   Management │   Processing │   (Qwen LLM)         │ │
│  └──────────────┴──────────────┴──────────────────────┘ │
└────────────────────┬────────────────────────────────────┘
                     │
        ┌────────────┼────────────┐
        │            │            │
        ▼            ▼            ▼
┌─────────────┐ ┌──────────┐ ┌──────────────┐
│   SQLite    │ │  Redis   │ │   Celery     │
│  Database   │ │ (Broker) │ │   Workers    │
└─────────────┘ └──────────┘ └──────────────┘
        │            │            │
        ▼            ▼            ▼
   data/         Queue        Processing
   autoclip.db   Management   Tasks
```

### Core Components

#### Backend Structure (`backend/`)

- **`api/v1/`** - API route handlers
  - `projects.py` - Project CRUD operations
  - `youtube.py` - YouTube video download
  - `bilibili.py` - Bilibili video download
  - `clips.py` - Video clip management
  - `collections.py` - Collection management
  - `processing.py` - Processing pipeline control
  - `files.py` - File upload/download
  - `progress.py` - Real-time progress tracking

- **`core/`** - Core configuration
  - `config.py` - Application settings (.env management)
  - `celery_app.py` - Celery task queue configuration
  - `database.py` - SQLAlchemy database setup
  - `llm_manager.py` - AI model management

- **`models/`** - SQLAlchemy data models
  - `project.py` - Project entity
  - `clip.py` - Video clip entity
  - `collection.py` - Collection entity
  - `task.py` - Task tracking entity
  - `bilibili.py` - BiliBili account entity

- **`tasks/`** - Celery async tasks
  - `processing.py` - Main AI processing pipeline
  - `video.py` - Video manipulation tasks
  - `upload.py` - BiliBili upload tasks
  - `maintenance.py` - System cleanup tasks

- **`services/`** - Business logic
  - `ai_service.py` - AI analysis service
  - `video_service.py` - Video processing service

#### Frontend Structure (`frontend/src/`)

- **`components/`** - Reusable React components
- **`pages/`** - Page components (HomePage, ProjectDetailPage, SettingsPage)
- **`services/`** - API client (`api.ts`)
- **`stores/`** - Zustand state management
- **`hooks/`** - Custom React hooks

#### Data Directory (`data/`)

- `projects/` - Project-specific data
- `uploads/` - Uploaded video files
- `temp/` - Temporary processing files
- `output/` - Generated clips and collections
- `autoclip.db` - SQLite database

## 🔧 Key Configuration

### Environment Variables (`.env`)

```bash
# Database
DATABASE_URL=sqlite:///./data/autoclip.db

# Redis
REDIS_URL=redis://localhost:6379/0

# AI API
API_DASHSCOPE_API_KEY=your_dashscope_api_key
API_MODEL_NAME=qwen-plus
API_MAX_TOKENS=4096
API_TIMEOUT=30

# Processing
PROCESSING_CHUNK_SIZE=5000
PROCESSING_MIN_SCORE_THRESHOLD=0.7
PROCESSING_MAX_CLIPS_PER_COLLECTION=5
PROCESSING_MAX_RETRIES=3

# Logging
LOG_LEVEL=INFO
LOG_FILE=backend.log

# Environment
ENVIRONMENT=development
DEBUG=true
```

### Service Ports

- **Frontend**: 3000 (React dev server)
- **Backend**: 8000 (FastAPI)
- **Redis**: 6379 (Message broker)
- **Flower**: 5555 (Celery monitoring)

## 🚀 Development Workflow

### 1. Initial Setup

```bash
# Clone repository
git clone <repo-url>
cd autoclip

# Create Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install backend dependencies
pip install -r requirements.txt

# Install frontend dependencies
cd frontend && npm install && cd ..

# Setup environment
cp env.example .env
# Edit .env with your API keys

# Initialize database
python -c "from backend.core.database import engine, Base; Base.metadata.create_all(bind=engine)"
```

### 2. Starting Development

```bash
# Option 1: Quick start (recommended for development)
./quick_start.sh

# Option 2: Manual start
# Terminal 1 - Backend
source venv/bin/activate
export PYTHONPATH="${PWD}:${PYTHONPATH}"
python -m uvicorn backend.main:app --reload --port 8000

# Terminal 2 - Celery Worker
source venv/bin/activate
celery -A backend.core.celery_app worker --loglevel=info

# Terminal 3 - Frontend
cd frontend && npm run dev

# Access: http://localhost:3000
# API Docs: http://localhost:8000/docs
```

### 3. Access Points

- **Frontend UI**: http://localhost:3000
- **Backend API**: http://localhost:8000
- **API Documentation**: http://localhost:8000/docs
- **Health Check**: http://localhost:8000/api/v1/health/
- **Flower Monitoring**: http://localhost:5555

## 🔍 API Endpoints

### Core Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/v1/projects` | GET, POST | List/create projects |
| `/api/v1/projects/{id}` | GET | Get project details |
| `/api/v1/projects/{id}/process` | POST | Start processing |
| `/api/v1/projects/{id}/status` | GET | Get processing status |
| `/api/v1/youtube/parse` | POST | Parse YouTube URL |
| `/api/v1/youtube/download` | POST | Download YouTube video |
| `/api/v1/bilibili/download` | POST | Download Bilibili video |
| `/api/v1/clips` | GET | List clips |
| `/api/v1/collections` | GET, POST | Manage collections |
| `/api/v1/progress/{task_id}` | GET | Get task progress |

## 🛠️ Processing Pipeline

The AI processing pipeline (`backend/tasks/processing.py`) follows these steps:

1. **Video Download/Upload** - Retrieve video and subtitle files
2. **Subtitle Extraction** - Extract subtitles (using pysrt or bcut-asr)
3. **Content Analysis** - AI analyzes video outline using Qwen LLM
4. **Timeline Extraction** - Identify topic time segments
5. **Clip Scoring** - Score each segment for excitement/value
6. **Clip Generation** - Generate video clips using FFmpeg
7. **Collection Creation** - Group clips into collections

### Pipeline Files

- `backend/pipeline/step1_outline.py` - Extract video outline
- `backend/pipeline/step2_timeline.py` - Generate timeline
- `backend/pipeline/step3_scoring.py` - Score segments
- `backend/pipeline/step6_video.py` - Generate video clips

## 📊 Database Schema

### Key Models

- **Project**: Tracks video processing jobs
  - id, name, description, status, project_type
  - video_url, video_path, subtitle_path
  - created_at, updated_at

- **Task**: Celery task tracking
  - id, project_id, task_type, status
  - result, error, created_at, completed_at

- **Clip**: Generated video segments
  - id, project_id, title, description
  - start_time, end_time, score
  - video_path, thumbnail_path

- **Collection**: Grouped clips
  - id, project_id, name, description
  - clip_ids (JSON array)

## 🎨 Frontend Tech Stack

- **React 18** - UI framework with Hooks
- **TypeScript** - Type safety
- **Ant Design** - UI component library
- **Vite** - Build tool with hot reload
- **Zustand** - Lightweight state management
- **React Router** - Client-side routing
- **Axios** - HTTP client
- **React Player** - Video playback

## 🔐 Key Features

### Implemented
- ✅ YouTube/Bilibili video download
- ✅ Local file upload
- ✅ AI-powered content analysis (Qwen LLM)
- ✅ Automatic video clipping
- ✅ Collection management
- ✅ Progress tracking
- ✅ WebSocket real-time updates
- ✅ Celery async task processing

### In Development
- 🔄 BiliBili upload functionality
- 🔄 Visual subtitle editor
- 🔄 Multi-account management
- 🔄 Mobile responsive design

## 🐛 Debugging Tips

### View Logs

```bash
# Backend logs
tail -f logs/backend.log

# Frontend logs
tail -f logs/frontend.log

# Celery logs
tail -f logs/celery.log

# Docker logs
docker-compose logs -f autoclip
```

### Check Service Health

```bash
# Backend health
curl http://localhost:8000/api/v1/health/

# Redis
redis-cli ping

# Check if ports are in use
lsof -i :8000  # Backend
lsof -i :3000  # Frontend
lsof -i :6379  # Redis
```

### Common Issues

1. **Port already in use**: Stop existing processes or use different ports
2. **Redis connection failed**: Ensure Redis is running (`brew services start redis`)
3. **YouTube download fails**: Update yt-dlp (`pip install --upgrade yt-dlp`)
4. **AI processing slow**: Check API key and network connection

## 📦 Dependencies

### Backend
- **FastAPI** - Web framework
- **Celery** - Task queue
- **Redis** - Message broker & cache
- **SQLAlchemy** - ORM
- **Pydantic** - Data validation
- **yt-dlp** - YouTube downloader
- **FFmpeg** - Video processing
- **requests/aiohttp** - HTTP clients

### Frontend
- **React 18** - UI framework
- **TypeScript 5** - Language
- **Ant Design 5** - UI library
- **Vite 5** - Build tool
- **Zustand** - State management

## 🚢 Deployment

### Docker (Recommended)
```bash
./docker-start.sh
```

### Local
```bash
./start_autoclip.sh
```

### Production Considerations
- Use PostgreSQL instead of SQLite
- Configure proper CORS origins
- Set up SSL/HTTPS
- Configure resource limits
- Set up log rotation
- Enable Redis persistence

## 📖 Additional Resources

- **Full Documentation**: See `README.md` and `README-EN.md`
- **Docker Guide**: See `DOCKER.md`
- **Technical Architecture**: See `.trae/documents/autoclip_technical_architecture.md`
- **API Docs**: http://localhost:8000/docs (when running)

## 💡 Development Tips

1. **Python Path**: Always set `PYTHONPATH="${PWD}:${PYTHONPATH}"`
2. **Virtual Environment**: Activate before running any Python commands
3. **Hot Reload**: Backend auto-reloads on code changes when using `--reload`
4. **Database**: Tables auto-create on backend startup
5. **File Structure**: Keep video files in `data/projects/{project_id}/raw/`
6. **Environment**: Use `.env` file for all configuration
7. **Logging**: Check `logs/` directory for service logs
8. **Testing**: Run tests before committing changes
