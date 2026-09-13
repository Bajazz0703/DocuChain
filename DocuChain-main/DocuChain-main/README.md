A LAN-Based Blockchain Document Management and Integrity Verification System

DocuChain pairs conventional document storage with blockchain-backed integrity verification. Every uploaded document gets three cryptographic hashes (MD5, SHA-1, SHA-256), with the SHA-256 hash permanently recorded on a Hyperledger Fabric ledger giving organizations a tamper-evident, auditable record without relying on any public blockchain or external cloud.

Key Features:

Document upload with metadata (type, department, owner, timestamp)
Multi-algorithm hashing (MD5, SHA-1, SHA-256) at upload
Blockchain-anchored integrity via Hyperledger Fabric
On-demand tamper detection (live hash vs. ledger record)
Full-text search powered by MeiliSearch
Complete audit trail dashboard for admins
JWT-based role access (user vs. admin)
Fully LAN-deployed — no internet dependency for core functions

Tech Stack:
React/Vite/Tailwind (frontend) · Node.js/Express (backend) · PostgreSQL (metadata) · Hyperledger Fabric 2.5 (blockchain) · MeiliSearch (search) · Docker/Docker Compose (infrastructure)

How it works: On upload → file stored on LAN server → hashes computed → SHA-256 submitted to Fabric ledger → metadata saved to PostgreSQL → content indexed in MeiliSearch. On verification → current hash recomputed and compared to the ledger record → mismatch flags tampering.

Storage responsibilities:

Store	Holds	Why
PostgreSQL	User accounts, metadata	Easy to query/update
Hyperledger Fabric	Hashes, version history, tamper flags	Immutable
MeiliSearch	Document text + metadata	Fast full-text search
Filesystem	Actual files	Too large for databases

Security notes: Only hashes go on-chain (never document content); permissioned blockchain, not public; passwords hashed with bcrypt; sessions via JWT.

Team: El Jane M. Bernal (Project Manager), Edrian M. Lim (System Developer), Jhastter R. Rollon (Systems Analyst & Documentation) — Institute of Computing, Davao del Norte State College.
