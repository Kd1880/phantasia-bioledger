#  BioLedger — Verifiable AI Inference with Blockchain

> **2nd Place — AI + Blockchain Track, Phantasia 3.0 Hackathon**

BioLedger is a decentralized biometric verification pipeline combining facial recognition AI with Polygon blockchain-based audit logging. Every biometric inference is cryptographically hashed via Keccak-256 and logged on-chain — making the entire pipeline transparent, tamper-proof, and accountable.

> **AI-only module (standalone):** [bioledger-ai](https://github.com/Kd1880/bioledger-ai)

---

##  What It Does

Traditional biometric systems are opaque black boxes — you can't verify what data was used, which model ran, or whether outputs were tampered with.

BioLedger solves this by making every step verifiable:

- **Biometric inference** → trust scored by AI, logged on-chain
- **Poisoned inputs** → detected in real time, contributor reputation penalized
- **Image fingerprinting** → Keccak-256 hash links AI output to blockchain record
- **Audit trail** → immutable and publicly verifiable on Polygon

---

##  System Architecture

```
Image Upload
     │
     ▼
┌──────────────────────────────┐
│      Flask REST API          │
│   app.py + config.py         │
│   • Keccak-256 image hash    │
│   • Routes to AI + blockchain│
└──────────┬───────────────────┘
           │
     ┌─────┴──────┐
     ▼            ▼
┌─────────────┐  ┌──────────────────────┐
│ AI Pipeline │  │  Blockchain Layer     │
│ embedder.py │  │  blockchain.py        │
│ trust.py    │  │  contracts/           │
│ ai_module.py│  │  hardhat.config.js    │
│ build_      │  │  scripts/             │
│ cluster.py  │  │  deployments/         │
└─────────────┘  └──────────────────────┘
```

---

##  Team Roles

| Role | Responsibility |
|---|---|
| **AI Engineer** | ArcFace embeddings, DBSCAN clustering, trust scoring, Flask API |
| **Blockchain Engineer** | Solidity contracts, Hardhat setup, Polygon deployment, inference logging |
| **Backend Engineer** | Web3 integration, blockchain.py, hashing pipeline, register.py |
| **Frontend Engineer** | Upload UI, trust meter visualization, demo flow |

---

##  Project Structure

```
phantasia-bioledger/
│
├── contracts/               ← Solidity smart contracts
│   └── BioLedger.sol            (dataset versioning, inference logging,
│                                 contributor reputation)
│
├── scripts/                 ← Hardhat deployment scripts
├── deployments/             ← Deployed contract addresses & ABIs
├── test/                    ← Smart contract unit tests
├── utils/                   ← Shared utility functions
│
├── dataset/
│   ├── authorized/          ← Authorized face images
│   ├── unknown/             ← Unknown faces for testing
│   └── poison/              ← Adversarial faces for demo
│
├── embedder.py              ← ArcFace embedding extraction
├── build_cluster.py         ← DBSCAN cluster builder, saves centroid
├── trust.py                 ← Cosine similarity trust scoring
├── ai_module.py             ← AI inference interface
├── blockchain.py            ← Web3.py + Polygon contract interaction
├── register.py              ← Dataset/model registration on-chain
├── app.py                   ← Flask REST API (main entry point)
├── config.py                ← Configuration (RPC URL, contract address)
├── test_pipeline.py         ← AI pipeline end-to-end test
├── test.py                  ← Integration tests
├── hardhat.config.js        ← Hardhat + Polygon Amoy testnet config
├── package.json             ← Node dependencies for Hardhat
├── requirements.txt         ← Python dependencies
└── .gitignore

## 📡 API Reference

### `POST /analyze`

**Request** — `multipart/form-data`:

| Field | Type | Description |
|---|---|---|
| `image` | File | Face image (.jpg / .png) |

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
| `trust_score` | float 0–100 | Higher = more trustworthy |
| `anomaly` | bool | True if input is suspicious |
| `cosine_distance` | float | Distance from authorized centroid |
| `status` | string | `AUTHORIZED` or `SUSPICIOUS` |
| `image_hash` | string | Keccak-256 hash logged on-chain |

### `GET /health`

```json
{ "status": "AI server running" }
```

---

## 🧪 Demo Scenarios

| Scenario | Input | Trust Score | On-chain Action |
|---|---|---|---|
| Clean upload | Authorized person | 83–85 | ✅ Inference logged |
| Unknown face | Different person | 10–25 | 🔴 Flagged, logged |
| Poison attempt | Adversarial image | 2–15 | 🔴 Reputation penalized |

---

## 🤖 AI Pipeline Details

### Why ArcFace?
State-of-the-art face recognition model producing 512-dimensional embeddings where same-identity faces cluster tightly and different identities are clearly separated.

### Why DBSCAN?
Doesn't require a predefined number of clusters — naturally labels outliers (poisoned inputs) as noise without forcing them into a cluster.

### Why Cosine Similarity?
In 512-dimensional embedding space, cosine similarity measures angular distance between identity vectors — more meaningful than Euclidean distance for high-dimensional face embeddings.

### Why Keccak-256?
Ethereum's native hashing algorithm — the same one used by `keccak256()` in Solidity. Image hash computed in Python is directly verifiable on-chain without any conversion.

---

## 📊 Results

| Category | Cosine Distance | Trust Score | Status |
|---|---|---|---|
| Authorized | 0.14 – 0.18 | 83 – 85 | ✅ AUTHORIZED |
| Unknown | 0.75 – 0.98 | 2 – 25 | 🔴 SUSPICIOUS |
| Poison | 0.84 – 0.93 | 6 – 15 | 🔴 SUSPICIOUS |

**Detection accuracy: 100% on test set**

---

##  Tech Stack

| Layer | Technology |
|---|---|
| Face Embeddings | ArcFace via DeepFace |
| Clustering | DBSCAN — scikit-learn |
| Similarity | Cosine Distance — scipy |
| API | Flask |
| Hashing | Keccak-256 |
| Blockchain | Polygon Amoy Testnet |
| Smart Contracts | Solidity + Hardhat |
| Web3 Integration | Web3.py |
| Testing | Postman |


## 📄 License

MIT License
