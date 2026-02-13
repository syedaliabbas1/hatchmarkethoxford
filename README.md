<p align="center">
  <img src="assets/logo.svg" alt="Hatchmark Logo" width="80" height="80" />
</p>

<h1 align="center">Hatchmark</h1>

<p align="center">
  <strong>Blockchain-powered content authenticity for the provenance economy</strong>
</p>

<p align="center">
  <video src="https://github.com/syedaliabbas1/hatchmarkethoxford/assets/demo.mp4" width="100%" controls>
    Your browser does not support the video tag.
  </video>
</p>

<p align="center">
  <em>📹 <a href="https://youtu.be/Gok6uf3wdY0">Watch full demo on YouTube</a></em>
</p>

<p align="center">
  <a href="https://ethoxford.onrender.com/">Live Demo</a> •
  <a href="https://youtu.be/Gok6uf3wdY0">Video Demo</a> •
  <a href="#quick-start">Quick Start</a> •
  <a href="#architecture">Architecture</a> •
  <a href="#smart-contracts">Smart Contracts</a>
</p>

---

## Overview

**Hatchmark** is a decentralized content authenticity platform built on the [Sui blockchain](https://sui.io). It enables creators to register image ownership on-chain using perceptual hashing, providing trustless, timestamped copyright protection in the age of AI-generated content.

### The Problem

- AI can generate photorealistic images in seconds
- Artists and photographers have no trustless way to prove they created something first
- Copyright disputes rely on centralized platforms making arbitrary decisions

### The Solution

Hatchmark creates a new **consumer primitive for the Provenance Economy**:

1. **Register** — Upload your image → compute perceptual hash → mint an on-chain certificate
2. **Verify** — Check any suspect image against the indexed registry
3. **Flag** — Create transparent on-chain disputes with SUI staking for accountability

## Features

- **On-Chain Registration** — Immutable timestamps on Sui blockchain
- **Perceptual Hashing** — Detect similar images even after cropping, resizing, or compression
- **Dispute System** — Stake-based flagging with transparent resolution
- **AI Detection** — Identify AI-generated content during registration
- **Browser Extension** — Right-click any image to verify authenticity
- **Downloadable Certificates** — Visual proof of registration with QR codes
- **Dark Mode** — Full theme support

## Live Demo

**Frontend:** [ethoxford.onrender.com](https://ethoxford.onrender.com/)

**Video Demo:** [Watch on YouTube](https://youtu.be/Gok6uf3wdY0)

**Network:** Sui Testnet

**Package ID:** `0x65c282c2a27cd8e3ed94fef0275635ce5e2e569ef83adec8421069625c62d4fe`

## Tech Stack

| Layer | Technology |
|-------|------------|
| **Frontend** | Next.js 15, React 19, TypeScript, Tailwind CSS |
| **Blockchain** | Sui Network (Move language) |
| **Wallet** | @mysten/dapp-kit (Sui Wallet Adapter) |
| **Database** | Supabase (PostgreSQL + Real-time) |
| **Hashing** | Client-side perceptual hashing (phash) |
| **Extension** | Chrome Manifest V3 |
| **Deployment** | Vercel (Frontend), Supabase (Indexer) |

## Quick Start

### Prerequisites

- Node.js 18+
- npm or pnpm
- Sui Wallet (e.g., [Sui Wallet](https://chrome.google.com/webstore/detail/sui-wallet/opcgpfmipidbgpenhmajoajpbobppdil))

### 1. Clone & Install

```bash
git clone https://github.com/syedaliabbas1/hatchmarkethoxford.git
cd hatchmarkethoxford/hatchmark-frontend
npm install
```

### 2. Environment Setup

Create `.env.local` in `hatchmark-frontend/`:

```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_KEY=your-service-key

# Sui
NEXT_PUBLIC_PACKAGE_ID=0x65c282c2a27cd8e3ed94fef0275635ce5e2e569ef83adec8421069625c62d4fe
```

### 3. Run Development Server

```bash
npm run dev
```

Visit [http://localhost:3000](http://localhost:3000)

## Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                           USER INTERFACE                             │
├─────────────────┬─────────────────┬─────────────────────────────────┤
│   Web Frontend  │  Browser Ext.   │         Sui Wallet              │
│   (Next.js)     │  (Chrome)       │         (dApp Kit)              │
└────────┬────────┴────────┬────────┴──────────────┬──────────────────┘
         │                 │                       │
         ▼                 ▼                       │
┌─────────────────────────────────────────┐       │
│              API Layer                   │       │
│  /api/verify  /api/register  /api/flag  │       │
└────────┬────────────────────────────────┘       │
         │                                         │
         ▼                                         ▼
┌─────────────────────┐               ┌───────────────────────────────┐
│      Supabase       │◄──── sync ────│         Sui Blockchain        │
│    (PostgreSQL)     │               │  ┌─────────────────────────┐  │
│  - registrations    │               │  │  RegistrationCertificate│  │
│  - disputes         │               │  │  Dispute                │  │
│  - indexer          │               │  │  Events                 │  │
└─────────────────────┘               │  └─────────────────────────┘  │
                                      └───────────────────────────────┘
```

## Project Structure

```
hatchmark/
├── hatchmark-frontend/         # Next.js web application
│   ├── app/
│   │   ├── register/           # Image registration flow
│   │   ├── verify/             # Image verification
│   │   ├── dashboard/          # User certificates & disputes
│   │   └── api/                # Backend API routes
│   ├── components/             # React components
│   └── lib/                    # Utilities (phash, sui, supabase)
│
├── hatchmark-extension/        # Chrome browser extension
│   ├── background.js           # Context menu handler
│   ├── content.js              # In-page verification UI
│   ├── popup.html              # Extension popup
│   └── phash.js                # Client-side hashing
│
├── sui-contracts/              # Sui Move smart contracts
│   └── hatchmark/
│       ├── sources/
│       │   └── registry.move   # Core contract logic
│       └── tests/              # Move unit tests
│
└── SUPABASE_SETUP.md           # Database setup guide
```

## Smart Contracts

The Hatchmark registry is implemented in [Sui Move](https://docs.sui.io/concepts/sui-move-concepts):

### Objects

| Object | Type | Description |
|--------|------|-------------|
| `RegistrationCertificate` | Owned | Proof of image registration (hash, timestamp, creator) |
| `Dispute` | Shared | Flagged content dispute with staked SUI |

### Entry Functions

```move
// Register a new image hash on-chain
entry fun register(
    image_hash: vector<u8>,
    title: String,
    description: String,
    clock: &Clock,
    ctx: &mut TxContext
)

// Flag content as potentially infringing (requires 0.1 SUI stake)
entry fun flag_content(
    cert: &RegistrationCertificate,
    flagged_hash: vector<u8>,
    similarity_score: u8,
    stake: Coin<SUI>,
    clock: &Clock,
    ctx: &mut TxContext
)

// Resolve dispute (only certificate creator)
entry fun resolve_dispute(
    dispute: &mut Dispute,
    cert: &RegistrationCertificate,
    resolution: u8,  // 1 = valid, 2 = invalid
    ctx: &mut TxContext
)
```

### Staking Mechanism

- **Minimum stake:** 0.1 SUI to file a dispute
- **Valid dispute:** Stake returned to flagger
- **Invalid dispute:** Stake forfeited to content creator

## Browser Extension

The Chrome extension allows instant verification of any image on the web:

1. Right-click any image
2. Select "Check with Hatchmark"
3. View verification results in an overlay

### Installation (Development)

1. Open `chrome://extensions/`
2. Enable "Developer mode"
3. Click "Load unpacked"
4. Select `hatchmark-extension/` folder

## API Reference

### POST `/api/verify`

Check if an image hash exists in the registry.

```json
// Request
{ "hash": "a1b2c3d4e5f6..." }

// Response
{
  "exactMatch": { "cert_id": "...", "creator": "0x...", "title": "..." },
  "matches": [{ "similarity": 95, "cert_id": "...", ... }]
}
```

### POST `/api/register`

Store registration in the database after on-chain transaction.

```json
// Request
{
  "cert_id": "0x...",
  "image_hash": "...",
  "creator": "0x...",
  "title": "My Artwork",
  "description": "...",
  "tx_digest": "..."
}
```

### POST `/api/flag`

Record a dispute after on-chain flagging.

### POST `/api/ai-detect`

Detect if an image is AI-generated.

```json
// Request
{ "imageBase64": "..." }

// Response
{ "isAIGenerated": true, "confidence": 87 }
```

## Deployment

### Frontend (Vercel)

```bash
cd hatchmark-frontend
npm run build
vercel --prod
```

### Supabase Database

See [SUPABASE_SETUP.md](SUPABASE_SETUP.md) for complete setup instructions.

### Smart Contracts

```bash
cd sui-contracts/hatchmark
sui client publish --gas-budget 100000000
```

## Development Notes

### Sui Development Experience

**What worked well:**
- Object-centric model maps naturally to ownership concepts
- Move's linear type system prevents common security bugs
- Fast testnet finality (~500ms)
- dApp Kit provides excellent React integration

**Challenges:**
- Learning curve for Move's ownership semantics
- Limited tooling compared to EVM ecosystem
- Event indexing requires custom infrastructure

## Roadmap

- [x] **V1:** Perceptual hash registration + verification + disputes
- [ ] **V2:** Zero-knowledge ownership proofs (prove ownership without revealing original)
- [ ] **V2:** IPFS integration for full image storage
- [ ] **V3:** Multi-chain support (Ethereum, Polygon)
- [ ] **V3:** DAO-based dispute resolution

## Contributing

Contributions are welcome! Please read our contributing guidelines and submit PRs.

## License

MIT License — see [LICENSE](LICENSE) for details.

---

<p align="center">
  Built at ETHOxford 2026
</p>
