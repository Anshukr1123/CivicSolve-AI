# CivicSolve AI

> **Problem Statement 43:** *A Digital Platform to Crowdsource Societal Challenges and Facilitate Collaborative Problem Solving through University and Industry Partnerships.*

CivicSolve AI is an open-source, full-stack collaborative platform designed to bridge the gap between grassroots societal challenges and institutional problem-solvers. The platform enables citizens, grassroots NGOs, and community advocates to submit real-world ground realities (such as groundwater contamination, agricultural cold-chain deficiencies, or rural healthcare accessibility), while leveraging Google Gemini AI to analyze systemic root causes, detect semantic duplicates, match multidisciplinary university research laboratories, and mobilize corporate CSR sponsorship.

---

## 📑 Table of Contents

- [Core Features](#-core-features)
- [Architecture & Tech Stack](#-architecture--tech-stack)
- [Agentic Threat Model & Security Architecture](#-agentic-threat-model--security-architecture)
- [Quickstart: Local Development](#-quickstart-local-development)
- [Git & GitHub Upload Guide](#-git--github-upload-guide)
- [Google Cloud Run Deployment](#-google-cloud-run-deployment)
  - [1. Prerequisites & GCP APIs](#1-prerequisites--gcp-apis)
  - [2. Secret Manager Configuration](#2-secret-manager-configuration)
  - [3. Firestore Database & Security Rules](#3-firestore-database--security-rules)
  - [4. Build and Deploy to Cloud Run](#4-build-and-deploy-to-cloud-run)
  - [5. Mandatory Challenge Verification Label](#5-mandatory-challenge-verification-label)
- [Interactive Functional Walkthrough & Test Suite](#-interactive-functional-walkthrough--test-suite)
- [License](#-license)

---

## 🌟 Core Features

1. **Crowdsourced Societal Problem Intake:**
   - Multistep guided submission workflow for grassroots advocates, rural citizens, and NGOs.
   - Structured ingestion capturing current situation, structural root causes, existing attempts, and constraints.
2. **Gemini AI Root-Cause & Severity Analysis:**
   - Automated 0–100 Severity Index calculation.
   - UN Sustainable Development Goals (SDG) classification.
   - Systematic root-cause extraction and recommended intervention areas.
3. **Semantic Duplicate Detection:**
   - Vector and semantic similarity matching to alert creators of existing overlapping challenges and prevent duplicated effort.
4. **Multilateral Stakeholder Matching:**
   - Automated matchmaking with universities (IITs, NITs, state colleges), specialized research institutes (e.g., NIH Roorkee), and industry CSR sponsors.
5. **Multi-Criteria Solution Evaluation:**
   - 7-dimension AI scoring (Feasibility, Scalability, Community Impact, Cost Efficiency, Technical Rigor, Sustainability, Implementation Timeline).
   - Identification of key strengths, vulnerabilities, and recommended pilot milestones.
6. **Collaborative Project Workspaces:**
   - Interactive Kanban boards (To Do, In Progress, Under Review, Completed).
   - Deliverables and pilot milestone tracking.
   - Integrated **Gemini AI Co-Design Assistant** with direct "Add to Kanban" functionality.
7. **Interactive Geospatial Map & Telemetry:**
   - Geographic distribution of challenges across Indian states and districts with severity filters.
   - Real-time impact telemetry reporting beneficiaries, mobilized funds, and participating institutions.
8. **Stakeholder Persona Simulator:**
   - Role switching between Citizen Reporter, Student Researcher, University Faculty, Industry CSR Lead, and Council Moderator.

---

## 🏗 Architecture & Tech Stack

- **Frontend:** React 19, TypeScript, Tailwind CSS v4, Motion, Lucide Icons.
- **Backend Service:** Node.js & Express API proxy with JSON body deserialization and null-safe payload sanitization.
- **AI Service Layer:** Google `@google/genai` TypeScript SDK with structured schema parsing and a 4-tier resilient model fallback ladder (`gemini-3.6-flash` ➔ `gemini-3.1-flash-lite` ➔ `gemini-flash-latest` ➔ `gemini-3.7-flash`).
- **Database & Identity:** Google Cloud Firestore (Native Mode) with owner-bound security rules and Firebase Authentication.
- **Hosting & Infrastructure:** Google Cloud Run (containerized) with Google Cloud Secret Manager.

---

## 🛡 Agentic Threat Model & Security Architecture

In accordance with enterprise secure development directives and the **OWASP Top 10 for LLM Applications**, CivicSolve AI implements defensive controls across all 5 threat zones:

| Threat Zone | Identified Vector | Severity | Countermeasure & Defensive Architecture |
|---|---|---|---|
| **1. Input Surfaces** | Malicious injection payloads, script tags, and oversized file payloads. | High | Strict input schema validation, defensive null-safe destructuring, text sanitization, and stripping of `undefined` values before persistence (`cleanPayload`). |
| **2. Planning & Reasoning** | Indirect prompt injection via user-submitted community problems attempting to hijack system instructions. | Critical | Untrusted inputs treated exclusively as plain data wrapped in deterministic delimiters, never as instructions. Rigid JSON schema extraction via `@google/genai` Type Schema definitions. |
| **3. Tool Execution & AI** | Denial of Service (DoS) from token exhaustion, upstream API throttling, or quota exhaustion (`429`/`503`). | Medium | Resilient 4-tier model fallback ladder (`gemini-3.6-flash` ➔ `gemini-3.1-flash-lite` ➔ `gemini-flash-latest` ➔ `gemini-3.7-flash`) with automatic backoff and retry. |
| **4. Memory & State** | Cross-tenant data leakage, session hijacking, or unauthorized modification of project tasks. | High | Strict owner-bound document authorization paths (`request.auth.uid == userId`), role-based access checks, and isolated user interaction subcollections. |
| **5. Inter-System Communication** | Accidental API key exposure in client bundles or network logs. | Critical | Backend-for-Frontend (BFF) proxy pattern: Zero Gemini or Firebase secret keys exposed to the client. All LLM orchestration executed server-side in Express. |

---

## 💻 Quickstart: Local Development

### Prerequisites
- Node.js 20+ and npm 10+
- A Google Gemini API Key ([Get one from Google AI Studio](https://aistudio.google.com/))
- (Optional) Firebase project credentials for cloud sync

### 1. Clone the repository
```bash
git clone https://github.com/<your-username>/civicsolve-ai.git
cd civicsolve-ai
```

### 2. Install dependencies
```bash
npm install
```

### 3. Configure environment variables
Copy the example environment configuration:
```bash
cp .env.example .env
```

Edit `.env` with your credentials:
```env
# Required for Gemini AI features
GEMINI_API_KEY="AIzaSy..."

# Application URL
APP_URL="http://localhost:3000"

# (Optional) Firebase Client Configuration
VITE_FIREBASE_API_KEY=""
VITE_FIREBASE_AUTH_DOMAIN=""
VITE_FIREBASE_PROJECT_ID=""
VITE_FIREBASE_STORAGE_BUCKET=""
VITE_FIREBASE_MESSAGING_SENDER_ID=""
VITE_FIREBASE_APP_ID=""
VITE_FIREBASE_DATABASE_ID=""
```

### 4. Run development server
```bash
npm run dev
```
The server will boot on `http://localhost:3000`.

### 5. Build for production
```bash
npm run build
npm run start
```

---

## 📤 Git & GitHub Upload Guide

Follow these steps to initialize and push this codebase to a new GitHub repository:

```bash
# 1. Initialize git repository (if not already initialized)
git init

# 2. Add all project files
git add .

# 3. Create initial commit
git commit -m "feat: initial commit of CivicSolve AI platform"

# 4. Set the default branch to main
git branch -M main

# 5. Link to your GitHub remote repository
# Replace <YOUR_USERNAME> and <YOUR_REPO> with your GitHub details:
git remote add origin https://github.com/<YOUR_USERNAME>/<YOUR_REPO>.git

# 6. Push to GitHub
git push -u origin main
```

> **Note:** The `.gitignore` file is preconfigured to ignore `node_modules/`, `dist/`, `.env`, build artifacts, and secret keys. Verify that `.env` is never committed.

---

## 🚀 Google Cloud Run Deployment

Follow these steps to deploy CivicSolve AI directly to Google Cloud Run with automated secret management and security bindings.

### 1. Prerequisites & GCP APIs

Authenticate with the Google Cloud SDK and set your GCP project:

```bash
# Set your Google Cloud Project ID and Region
export PROJECT_ID="your-gcp-project-id"
export REGION="us-central1"
gcloud config set project $PROJECT_ID

# Enable required Google Cloud APIs
gcloud services enable \
  run.googleapis.com \
  secretmanager.googleapis.com \
  firestore.googleapis.com \
  artifactregistry.googleapis.com
```

### 2. Secret Manager Configuration

Store the Gemini API Key in Secret Manager and grant the Cloud Run runtime service account permission to access it:

```bash
# 1. Create and populate the secret
gcloud secrets create GEMINI_API_KEY --replication-policy="automatic"
echo -n "YOUR_API_KEY" | gcloud secrets versions add GEMINI_API_KEY --data-file=-

# 2. Grant the default Cloud Run service account access to read the secret
export PROJECT_NUMBER=$(gcloud projects describe $PROJECT_ID --format='value(projectNumber)')

gcloud secrets add-iam-policy-binding GEMINI_API_KEY \
  --member="serviceAccount:${PROJECT_NUMBER}-compute@developer.gserviceaccount.com" \
  --role="roles/secretmanager.secretAccessor"
```

### 3. Firestore Database & Security Rules

Ensure Cloud Firestore is provisioned in Native Mode. Deploy the following security rules to enforce user data isolation:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/interactions/{interactionId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

Deploy rules using the Firebase CLI:
```bash
firebase deploy --only firestore:rules
```

### 4. Build and Deploy to Cloud Run

Deploy the containerized full-stack application directly from source:

```bash
gcloud run deploy civicsolve-ai \
  --source . \
  --platform managed \
  --region $REGION \
  --allow-unauthenticated \
  --set-secrets GEMINI_API_KEY=GEMINI_API_KEY:latest \
  --set-env-vars NODE_ENV=production,PORT=3000
```

### 5. Mandatory Challenge Verification Label

Apply the official campaign resource label to register the service for automated challenge verification:

```bash
gcloud run services update civicsolve-ai \
  --update-labels=dev-tutorial=cloud-run-ai-challenge \
  --region=$REGION
```

---

## 🧪 Interactive Functional Walkthrough & Test Suite

This test suite details manual and automated verification procedures across every user interaction:

### Test Case 1: Landing Page & Geospatial Exploration
1. Open the application homepage (`http://localhost:3000`).
2. Verify the editorial broadsheet header and problem statement banner render.
3. Scroll to the **Interactive Geospatial Map of India**.
4. Click on any regional map marker (e.g., *Maharashtra* or *Tamil Nadu*).
5. Verify that the detailed card expands showing the local challenge summary, severity index, and primary SDG goal.
6. Toggle category filter pills (*Water & Sanitation*, *Clean Energy*). Confirm the challenge list filters dynamically.

### Test Case 2: Challenge Submission & Gemini AI Structuring
1. Click **Submit Challenge** in the navigation bar.
2. **Step 1 (Basic Info)**: Enter a title (e.g., *"Groundwater Salinity and Drinking Water Scarcity"*), select category *"Water & Sanitation"*, urgency *"High"*, and region *"Maharashtra"*.
3. Click **Next: Root Causes & Context**. Enter community details and observed symptoms.
4. Click **Next: Evidence & Attachments**. Add a reference URL or document.
5. Click **Run AI Analysis & Review**.
6. Verify that the Gemini AI synthesis generates:
   - Calculated Severity Index (0–100).
   - Mapped UN Sustainable Development Goals (SDGs).
   - Systemic Root Causes.
   - Target Partner Types.
   - Semantic Duplicate Check (with similarity score).
7. Click **Confirm & Publish Challenge**. Confirm the confirmation notification toast appears and the challenge is added to the live registry.

### Test Case 3: Solution Proposal & 7-Dimension AI Evaluation
1. Navigate to **Explore Challenges** and select any challenge dossier (e.g., *"Fluoride Contamination in Shevgaon Drinking Water"*).
2. Click **Propose Solution**.
3. Enter title, technical approach, budget, and timeline.
4. Click **Evaluate with Gemini AI**.
5. Verify the AI evaluation generates:
   - 7-Dimension Radar Scores (Feasibility, Scalability, Social Impact, Cost Effectiveness, Technical Rigor, Sustainability, Implementation Timeline).
   - Identified key strengths and potential vulnerabilities.
   - Recommended phased pilot milestones.
6. Click **Submit Evaluated Solution**. Confirm the solution appears in the dossier with upvote capabilities.

### Test Case 4: Collaborative Workspace & AI Assistant
1. Navigate to **Workspaces** and open an active project (e.g., *"Solar Cold Storage for Perishable Produce"*).
2. In the **Kanban Board**, drag or transition tasks between *To Do*, *In Progress*, *Under Review*, and *Completed*.
3. Click **Add Task**, fill in the title, and assign it to a team member. Verify immediate persistence.
4. In the **AI Co-Design Assistant** drawer, submit a query (e.g., *"Suggest a 2-week testing protocol for field solar panels"*).
5. Review the structured AI recommendations. Click **+ Add to Kanban** on any suggested action item to add it directly to the active task backlog.

### Test Case 5: Stakeholder Persona Switcher & Moderation Deck
1. Locate the **Role** selector in the top-right header.
2. Select **Council Moderator (Admin)**.
3. Navigate to **Dashboard**.
4. Confirm the **Council Moderation Queue** displays reported items.
5. Click **Dismiss** or **Take Down & Resolve** on a report item. Verify state updates with a confirmation toast.
6. Switch back to **Student Researcher** or **Grassroots Citizen** to verify permission boundaries.

---

## 📄 License

Distributed under the Apache 2.0 License. See `LICENSE` for more information.
