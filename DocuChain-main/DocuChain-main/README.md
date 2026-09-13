DocuChain

A LAN-Based Blockchain Document Management and Integrity Verification System

DocuChain is a permissioned, blockchain-backed document management platform designed for enterprise and institutional use. It combines conventional document storage with cryptographic hash verification on a Hyperledger Fabric ledger, giving organizations a tamper-evident, auditable record of every document they manage — without relying on any public blockchain or external cloud service.

Table of Contents
Overview
Key Features
System Architecture
Tech Stack
Getting Started
Prerequisites
Installation
Running the System
Project Structure
Usage
Data Flow Summary
Security Notes
Roadmap
Team
License
Overview

Traditional document management systems provide storage and organization, but rarely offer any cryptographic guarantee that a file hasn't been altered after it was uploaded. DocuChain addresses this gap by pairing every document with three cryptographic hashes (MD5, SHA-1, SHA-256), anchoring the SHA-256 hash on an immutable Hyperledger Fabric ledger, and allowing any authorized user to verify a document's integrity on demand.

The entire system is designed to run on an organization's own physical servers within a Local Area Network (LAN), meaning no document content ever leaves the institution's controlled infrastructure.

Key Features
Document Upload & Organization — Upload documents with metadata such as type, department, owner, and timestamp.
Multi-Algorithm Hashing — Every document is fingerprinted using MD5, SHA-1, and SHA-256 at upload time.
Blockchain-Anchored Integrity — SHA-256 hashes are recorded on a permissioned Hyperledger Fabric ledger, making tampering detectable and the audit trail immutable.
Tamper Detection — On-demand verification compares a document's live hash against its blockchain record and flags any mismatch.
Full-Text Search — MeiliSearch indexes document content, enabling users to search by keywords found inside the files themselves, not just filenames.
Audit Trail Dashboard — Administrators can review a complete, timestamped history of uploads, verifications, and access events.
Role-Based Access — JWT-based authentication distinguishes between regular users and administrators.
LAN-Only Deployment — No internet dependency for core operations; all services run on-premises via Docker.
System Architecture

DocuChain follows a multi-layered architecture:

┌─────────────────────────────────────────────────────────┐
│                     Frontend (React)                     │
│              React + Vite + Tailwind CSS                 │
└───────────────────────────┬───────────────────────────────┘
                            │ HTTPS + JWT
┌───────────────────────────▼───────────────────────────────┐
│                  Backend API (Node.js)                    │
│                    Express REST API                       │
├───────────────┬───────────────┬───────────────────────────┤
│  Hashing       │  Blockchain   │   File Storage /          │
│  Service       │  Service      │   Search Indexing         │
│ (MD5/SHA1/256) │ (Fabric SDK)  │  (MeiliSearch + FS)        │
└───────┬────────┴───────┬───────┴───────────┬──────────────┘
        │                │                    │
┌───────▼───────┐ ┌──────▼───────────┐ ┌──────▼──────────┐
│  PostgreSQL   │ │ Hyperledger      │ │  MeiliSearch     │
│ (users, meta) │ │ Fabric Ledger    │ │  (search index)  │
│               │ │ (immutable hash  │ │                  │
│               │ │  records)        │ │                  │
└───────────────┘ └──────────────────┘ └──────────────────┘

When a document is uploaded:

The file is stored on the LAN server's filesystem.
The backend computes MD5, SHA-1, and SHA-256 hashes.
The SHA-256 hash is submitted to the Hyperledger Fabric chaincode and permanently recorded on the ledger.
Metadata is stored in PostgreSQL, and content is indexed in MeiliSearch for full-text search.
On verification, the current file hash is recomputed and compared against the ledger record; any mismatch is flagged as tampering.
Tech Stack
Layer	Technology
Frontend	React, Vite, Tailwind CSS
Backend	Node.js, Express
Database	PostgreSQL
Blockchain	Hyperledger Fabric 2.5
Search Engine	MeiliSearch
Hashing	Node.js crypto module (MD5, SHA-1, SHA-256)
Authentication	JSON Web Tokens (JWT)
Infrastructure	Docker, Docker Compose
Getting Started
Prerequisites

Make sure the following are installed on your LAN server or local machine:

Node.js (v18 or later)
Docker and Docker Compose
Git
Installation
Clone the repository
bash
   git clone https://github.com/your-org/docuchain.git
   cd docuchain
Install backend dependencies
bash
   cd backend
   npm install
Install frontend dependencies
bash
   cd ../frontend
   npm install
Configure environment variables Create a .env file in the backend directory:
env
   PORT=5000
   DATABASE_URL=postgresql://user:password@localhost:5432/docuchain
   JWT_SECRET=your_jwt_secret_here
   MEILISEARCH_HOST=http://localhost:7700
   MEILISEARCH_API_KEY=your_meilisearch_key
   FABRIC_NETWORK_CONFIG=./config/connection-profile.json
Running the System
Start the infrastructure (PostgreSQL, MeiliSearch, Hyperledger Fabric nodes)
bash
   docker-compose up -d
Bring up the Hyperledger Fabric network and deploy chaincode
bash
   cd fabric-network
   ./network.sh up createChannel -c docuchainchannel
   ./network.sh deployCC -c docuchainchannel -ccn docuchain -ccp ../chaincode
Start the backend API
bash
   cd backend
   npm run dev
Start the frontend
bash
   cd frontend
   npm run dev
Access the application Open your browser to http://localhost:5173 (or your configured LAN address).
Project Structure
docuchain/
├── frontend/                # React + Vite + Tailwind CSS UI
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   └── services/
│   └── package.json
├── backend/                  # Node.js + Express API
│   ├── src/
│   │   ├── routes/
│   │   ├── services/
│   │   │   ├── hashingService.js
│   │   │   ├── blockchainService.js
│   │   │   └── storageService.js
│   │   ├── models/
│   │   └── middleware/
│   └── package.json
├── chaincode/                 # Hyperledger Fabric smart contract (Node.js)
│   └── lib/documentContract.js
├── fabric-network/            # Fabric network configuration & scripts
├── docker-compose.yml
└── README.md
Usage
Log in using your registered credentials (JWT-based authentication).
Upload a document — select a file and fill in metadata (department, document type, etc.).
The system automatically computes and records hashes on the blockchain ledger.
Use the search bar to find documents by content, filename, or metadata.
Click Verify Integrity on any document to confirm it hasn't been altered since upload.
Administrators can access the Audit Trail to review the complete history of system activity.
Data Flow Summary
Store	What It Holds	Why
PostgreSQL	User accounts, document metadata	Easy to query, update, and manage
Hyperledger Fabric	Hash values, version history, tamper flags	Immutable — cannot be altered
MeiliSearch	Document text content and metadata	Fast full-text search
Filesystem	Actual uploaded files	Binary files are too large for databases
Security Notes
Document content is never stored on the blockchain — only cryptographic hash values are recorded, preserving performance and storage efficiency.
The blockchain network is permissioned (Hyperledger Fabric), not a public chain like Ethereum or Bitcoin.
All core operations run within the LAN and do not require internet connectivity.
Passwords are hashed with bcrypt; sessions are managed via JWT.
Roadmap
 Digital signature workflows for signed documents
 Integration with external ERP/legal management systems
 Multi-organization blockchain consortium support
 Mobile-responsive interface improvements
Team
Name	Role
El Jane M. Bernal	Project Manager
Edrian M. Lim	System Developer
Jhastter R. Rollon	Systems Analyst & Documentation

Developed as a Capstone Project for the Institute of Computing, Davao del Norte State College.

License

This project is developed for academic purposes as part of a Bachelor of Science in Information Technology capstone requirement. Contact the project team for usage or licensing inquiries.
