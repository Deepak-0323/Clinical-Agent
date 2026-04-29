# Clinical Risk Monitoring & Decision Support Agent

Hospital-grade clinician decision support platform with agentic RAG for early deterioration detection.

## 🚀 Quick Start

### Prerequisites
- Python 3.10+
- Supabase account (free tier available)
- Node.js 18+ (for frontend)

### Step 1: Setup Supabase Database
```bash
# 1. Create a Supabase project at https://supabase.com
# 2. Go to Settings > Database
# 3. Copy your connection string (Connection Pooling mode)
# 4. Format: postgresql://postgres:[PASSWORD]@[PROJECT_REF].supabase.co:5432/postgres
```

### Step 2: Clone and Setup Backend
```bash
# Create virtual environment
python -m venv venv
venv\Scripts\activate  # Windows
# or
source venv/bin/activate  # Linux/Mac

# Install dependencies
cd backend
pip install -r requirements.txt

# Setup environment
copy .env.example .env  # Windows
# or
cp .env.example .env  # Linux/Mac

# Edit .env and add your Supabase DATABASE_URL
```

### Step 3: Setup Database Schema
```bash
# Run schema migrations on Supabase
python scripts/setup_database.py
```

### Step 3.5: Connect Supabase (API) and verify
Add these to `backend/.env`:

- `SUPABASE_URL` (your project URL)
- `SUPABASE_ANON_KEY` (your publishable/anon key)

Then run:

```bash
python scripts/check_supabase.py
```

### Step 3: Seed Guideline Data
```bash
# Index clinical guidelines into ChromaDB
python scripts/seed_guidelines.py
```

### Step 4: Start Backend Server
```bash
# From backend directory
uvicorn app.main:app --reload --port 8000
```

Backend will be available at: `http://localhost:8000`
API docs at: `http://localhost:8000/docs`

### Step 5: Test with Vitals Simulator
```bash
# Open new terminal
cd backend
python scripts/vitals_simulator.py

# Select option 2 for deteriorating patient simulation
```

## 📊 System Architecture

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  Patient Side   │────▶│  Backend (API)   │────▶│  Doctor Side    │
│                 │     │                  │     │                 │
│ • Vitals Stream │     │ • LangGraph      │     │ • Dashboard     │
│ • Lab PDFs      │     │ • Risk Predictor │     │ • Alerts        │
│ • EHR Notes     │     │ • RAG Retriever  │     │ • Reports       │
│ • Baseline      │     │ • Safety Layer   │     │ • Feedback      │
└─────────────────┘     │                  │     └─────────────────┘
                        │ Databases:       │
                        │ • PostgreSQL     │
                        │ • TimescaleDB    │
                        │ • ChromaDB       │
                        └──────────────────┘
```

## 🔐 Security & HIPAA Compliance

### Authentication
- JWT-based authentication
- Role-based access control (RBAC)
- Secure password hashing with bcrypt

### Data Protection
- All PHI encrypted in transit (TLS/SSL)
- Database encryption at rest
- Audit logging for all access
- Session timeout after 30 minutes

### Compliance Features
- Complete audit trail
- Data retention policies
- Access logs
- User activity monitoring

## 🧪 Testing

### Run Unit Tests
```bash
cd backend
pytest app/tests/ -v
```

### Test Coverage
```bash
pytest app/tests/ --cov=app --cov-report=html
```

### Integration Testing
```bash
# Test risk prediction
python -m pytest app/tests/test_risk_predictor.py

# Test RAG retrieval
python -m pytest app/tests/test_rag_retriever.py

# Test safety layer
python -m pytest app/tests/test_safety_layer.py
```

## 📱 Notification Setup

### Email (SMTP)

Update `.env`:
```
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASSWORD=your-app-password
MOCK_NOTIFICATIONS=False
```

### SMS (Twilio)

Update `.env`:
```
TWILIO_ACCOUNT_SID=your-sid
TWILIO_AUTH_TOKEN=your-token
TWILIO_PHONE_NUMBER=+1234567890
MOCK_NOTIFICATIONS=False
```

## 🎯 Key Features

### 1. Real-time Vitals Monitoring
- Continuous data ingestion
- 30-second update intervals
- Automatic baseline calculation

### 2. Multi-condition Risk Prediction
- Sepsis (6-12h horizon)
- Acute Kidney Injury
- Respiratory Failure
- NEWS2-based clinical scoring
- ML-enhanced probability

### 3. Explainable AI
- SHAP feature importance
- Top risk drivers identification
- Uncertainty quantification

### 4. Guideline-Grounded RAG
- NICE guidelines
- WHO protocols
- Hospital-specific policies
- Version-controlled retrieval

### 5. Safety Guardrails
- Data completeness checks
- Staleness detection
- Contradiction validation
- Minimum 60% vitals coverage required

### 6. Alert System
- WebSocket real-time alerts
- Email/SMS notifications
- Alert cooldown (2 min)
- Acknowledgment tracking

### 7. Audit Logging
- Full input/output capture
- Model version tracking
- Guideline version tracking
- Data provenance

## 🔧 Configuration

### Risk Thresholds

Edit `.env`:
```
HIGH_RISK_THRESHOLD=0.6
CRITICAL_RISK_THRESHOLD=0.8
ALERT_COOLDOWN_SECONDS=120
```

### Safety Layer
```
MIN_VITALS_COVERAGE=0.6
MAX_VITALS_STALENESS_MINUTES=30
```

## 📖 API Documentation

### Authentication
```bash
POST /api/auth/login
{
  "username": "doctor1",
  "password": "password"
}
```

### Submit Vitals
```bash
POST /api/vitals
{
  "encounter_id": 1,
  "timestamp": "2026-01-28T10:30:00Z",
  "heart_rate": 110,
  "respiratory_rate": 24,
  "spo2": 93,
  "temperature": 38.5,
  "mean_arterial_pressure": 75
}
```

### Get Alerts
```bash
GET /api/alerts?encounter_id=1&status=pending
```

### Generate Report
```bash
POST /api/reports/generate
{
  "alert_id": 123
}
```

## 🚨 Safety Notice

**CRITICAL: This system is advisory only**

- NO autonomous diagnosis
- NO medication prescribing
- NO treatment recommendations
- Clinician review ALWAYS required
- All outputs must be validated

## 🐛 Troubleshooting

### Database Connection Issues
```bash
# Check your Supabase connection string in .env file
# Format: postgresql://postgres:[PASSWORD]@[PROJECT_REF].supabase.co:5432/postgres

# Verify connection string is correct:
# 1. Go to Supabase Dashboard > Settings > Database
# 2. Use "Connection Pooling" mode (not Direct connection)
# 3. Copy the connection string and update .env

# Reset database schema
python scripts/setup_database.py
```

### ChromaDB Issues
```bash
# Clear and reseed
rm -rf chroma_db/
python scripts/seed_guidelines.py
```

### WebSocket Connection Failed

- Ensure backend is running on port 8000
- Check CORS settings in `main.py`
- Verify firewall allows WebSocket connections

## 📝 License

Proprietary - Hospital Use Only

## 👥 Support

For technical support: support@hospital.com
For clinical questions: clinical-it@hospital.com
