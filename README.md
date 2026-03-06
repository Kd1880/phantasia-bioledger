# BioLedger — Verifiable AI Inference with Blockchain

> **2nd Place — AI + Blockchain Track, Phantasia 3.0 Hackathon**

BioLedger is a decentralized biometric verification pipeline that combines facial recognition AI with blockchain-based audit logging. Every biometric inference is cryptographically hashed and logged on-chain — making the entire pipeline transparent, tamper-proof, and accountable.

---

##  What It Does

Traditional biometric systems are opaque black boxes — you can't verify what data was used, which model ran, or whether outputs were tampered with.

BioLedger solves this by making every step verifiable:

- **Dataset contributions** → traceable via cryptographic hashing
- **Model inference** → logged with trust scores on-chain
- **Poisoned inputs** → detected and contributor reputation penalized
- **Audit trail** → immutable and publicly verifiable

---

##  System Architecture

```
Image Upload
     │
     ▼
┌─────────────────────────────┐
│     Flask REST API          │
│  • Keccak-256 image hash    │
│  • Routes to AI module      │
└────────────┬────────────────┘
             │
             ▼
┌─────────────────────────────┐
│       AI Pipeline           │
│  • ArcFace embeddings       │
│  • DBSCAN clustering        │
│  • Cosine similarity score  │
│  • Trust score (0–100)      │
└────────────┬────────────────┘
             │
             ▼
┌─────────────────────────────┐
│    Blockchain Layer         │
│  • Log inference on-chain   │
│  • Update contributor rep   │
│  • Dataset versioning       │
└─────────────────────────────┘
```

---

##  Team Roles

| Role | Responsibility |
|---|---|
| **AI Engineer** | Face embeddings, DBSCAN clustering, trust scoring, Flask API |
| **Blockchain Engineer** | Smart contracts, dataset versioning, inference logging |
| **Backend Engineer** | Integration layer, Web3 connection, hashing pipeline |
| **Frontend Engineer** | Upload UI, trust meter visualization, demo flow |

---

##  AI Pipeline (My Contribution)

### Face Embedding
- Used **ArcFace pretrained model** via DeepFace
- Generates **512-dimensional identity vectors** per face
- No model training required — pure inference on pretrained weights

### Anomaly Detection
- Built authorized identity cluster from **30 facial images across 3 identity classes**
- Applied **DBSCAN unsupervised clustering** to model legitimate user embeddings
- Computed **cosine similarity** between new input and cluster centroid

### Trust Scoring
- Returns a **trust score (0–100)** for every uploaded face
- Authorized faces: **83–85 trust score**
- Unknown/adversarial faces: **2–25 trust score**
- **100% detection accuracy** on test set

### Flask REST API
- `POST /analyze` — accepts image, returns trust score + image hash
- `GET /health` — server status check
- Applied **Keccak-256 hashing** (Ethereum-native) for tamper-proof fingerprinting
- API response feeds directly into blockchain team's smart contract calls

---

##  Project Structure

```
bioledger-ai/
│
├── dataset/
│   ├── authorized/        ← Clean authorized face images
│   ├── unknown/           ← Unknown face images for testing
│   └── poison/            ← Adversarial/poison face images
│
├── embedder.py            ← ArcFace embedding extraction
├── build_cluster.py       ← DBSCAN cluster builder
├── trust.py               ← Cosine similarity trust scoring
├── ai_module.py           ← Main AI inference interface
├── app.py                 ← Flask REST API
├── test_pipeline.py       ← End-to-end pipeline test
├── centroid.pkl           ← Saved cluster centroid
└── requirements.txt
```

---

##  Setup & Installation

### Prerequisites
- Python 3.8 – 3.10
- pip

### Install Dependencies

```bash
git clone https://github.com/Kd1880/phantasia-bioledger.git
cd phantasia-bioledger

python -m venv venv

# Windows
venv\Scripts\activate

# Mac/Linux
source venv/bin/activate

pip install -r requirements.txt
```

### Requirements

```
deepface
scikit-learn
scipy
numpy
flask
tensorflow
opencv-python
tf-keras
```

---

##  Running the Project

### Step 1 — Prepare Dataset

Place face images in the appropriate folders:
- `dataset/authorized/` — 15–20 images of the authorized person
- `dataset/unknown/` — images of unknown people for testing
- `dataset/poison/` — adversarial images for poison demo

### Step 2 — Build the Cluster

```bash
python build_cluster.py
```

This processes all authorized faces, runs DBSCAN, and saves `centroid.pkl`.

### Step 3 — Test the Pipeline

```bash
python test_pipeline.py
```

Expected output:
```
AUTHORIZED FACES → trust_score: 83–85, status: AUTHORIZED
UNKNOWN FACES    → trust_score: 2–25,  status: SUSPICIOUS
POISON FACES     → trust_score: 2–15,  status: SUSPICIOUS
```

### Step 4 — Start the API

```bash
python app.py
```

Server runs at `http://127.0.0.1:5000`

---

## 📡 API Reference

### `POST /analyze`

Upload a face image for biometric verification.

**Request:**
```
Content-Type: multipart/form-data
Body: image (File)
```

**Response:**
```json
{
  "trust_score": 85.41,
  "anomaly": false,
  "cosine_distance": 0.1459,
  "status": "AUTHORIZED",
  "image_hash": "4f19991979a6138e73fa0a75a34bcab30eaea46d...",
  "filename": "face.jpg"
}
```

| Field | Type | Description |
|---|---|---|
| `trust_score` | float (0–100) | Higher = more trustworthy |
| `anomaly` | bool | True if input is suspicious |
| `cosine_distance` | float | Distance from authorized centroid |
| `status` | string | `AUTHORIZED` or `SUSPICIOUS` |
| `image_hash` | string | Keccak-256 hash for blockchain logging |

### `GET /health`

```json
{ "status": "AI server running" }
```

---

##  Demo Scenarios

| Scenario | Input | Trust Score | Result |
|---|---|---|---|
| Clean upload | Authorized person | 83–85 | ✅ AUTHORIZED |
| Unknown face | Different person | 10–25 | 🔴 SUSPICIOUS |
| Poison attempt | Adversarial image | 2–15 | 🔴 SUSPICIOUS |

---

##  Blockchain Integration

The AI API output is consumed by the smart contract layer:

```
image_hash   → used as unique identifier in logInference()
trust_score  → stored on-chain per inference record
anomaly flag → triggers updateReputation() if True
```

Keccak-256 was chosen specifically because it is **Ethereum's native hashing algorithm** — ensuring the hash computed off-chain in Python matches exactly what the Solidity smart contract would compute, enabling end-to-end cross-verification.

##  Tech Stack

| Layer | Technology |
|---|---|
| Face Embeddings | ArcFace (via DeepFace) |
| Clustering | DBSCAN (scikit-learn) |
| Similarity | Cosine Distance (scipy) |
| API | Flask |
| Hashing | Keccak-256 |
| Blockchain | Polygon (smart contracts by blockchain team) |
| Testing | Postman |

---

## 📄 License

MIT License — feel free to use and build on this.

