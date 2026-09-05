# 🎓 VidyaMitra — Interview Preparation Guide

---

## 🔷 What is VidyaMitra? (1-line pitch)

> **VidyaMitra is an AI-powered mock interview and career readiness platform that helps students practice interviews and enables institutions/recruiters to manage and evaluate candidates.**

---

## 📌 Full Project Description (say this in the interview)

VidyaMitra is a full-stack web application built for two types of users:

**Students** can:
- Do AI-powered mock interviews with voice (using a bot called **FRIEDE**)
- Practice aptitude MCQs, coding problems, and HR-style interviews
- Upload their resume and get an **ATS (Applicant Tracking System) score**
- View a **Career Planner** with a personalized learning roadmap
- Analyze skill gaps and see which skills they need to learn for a target role
- Browse a **Job Board** powered by real-time job APIs
- Chat with an AI assistant (MitoiChat) for guidance anytime

**Admins / Institutions** can:
- Manage a dashboard with students, roles, and interview rounds
- Upload and **bulk-screen hundreds of resumes** with AI scoring
- Set custom question pools per role
- Send selection/rejection emails to candidates via **AWS SES**
- Track how many interviews and students have used the platform
- Subscribe to a **SaaS billing plan** (via Razorpay payment gateway)

---

## 🛠️ Tech Stack (simple table)

| Layer | Technology |
|---|---|
| **Frontend** | React 18 + TypeScript, Vite, Tailwind CSS, shadcn/ui |
| **Backend** | Node.js Express API server (apiServer.ts) |
| **Database** | Supabase (PostgreSQL) + SQLite (local fallback) |
| **AI - Interviews** | Google Gemini API (voice + text generation) |
| **AI - Resume/Career** | Groq (LLaMA), OpenAI GPT-4.1 (server-side proxy) |
| **Voice** | ElevenLabs (realistic AI voice), Web Speech API |
| **Code Execution** | Judge0 API (via RapidAPI or self-hosted on AWS EC2) |
| **File Storage** | AWS S3 (resume uploads, media) |
| **Email** | AWS SES (selection emails to candidates) |
| **Notifications** | AWS SNS (bulk marketing/notifications) |
| **Face Detection** | AWS Rekognition + TensorFlow.js (proctoring) |
| **Payments** | Razorpay (institution subscriptions) |
| **State Management** | React Context + TanStack React Query |

---

## 🧩 Key Features Explained Simply

### 1. FRIEDE Bot (AI Interviewer)
- An AI bot that **speaks questions** and **listens to your answers** using voice
- Powered by **Google Gemini + ElevenLabs** for realistic voice
- Gives instant AI-generated feedback and a performance score

### 2. Multi-Round Interview System
- **Round 1**: Aptitude MCQ test (timed)
- **Round 2**: Coding challenge (with live code editor using Monaco Editor)
- **Round 3**: AI mock interview (voice/text)
- Admin sees scores, can shortlist candidates and send Round 2 invitation emails

### 3. Smart Resume Builder + ATS Scorer
- Students build or upload resumes
- Gemini AI parses the resume and gives an **ATS compatibility score**
- Shows what keywords are missing for a target job role

### 4. Career Planner & Gap Analysis
- Student selects a target role (e.g., "Data Scientist")
- AI generates a **skill gap analysis** — what they know vs. what they need
- Creates a month-by-month **learning roadmap** (visualized as a Mermaid diagram)
- Recommends YouTube videos and learning resources

### 5. Proctoring System
- Detects **tab switches** (malpractice detection)
- Uses **webcam** to verify student presence
- Flags if AI-generated answers are detected
- AWS Rekognition used for face verification

### 6. Subscription System (SaaS)
- Institutions pay a monthly/annual fee
- **4 plans**: Starter (₹1,999) → Professional (₹4,999) → Enterprise (₹14,999) → Mega (₹49,999)
- Payments via **Razorpay** (India's leading payment gateway)
- Usage tracking: how many students and interviews per month

---

## 🏗️ Architecture Diagram (draw this on whiteboard)

```
┌──────────────────────────────────────────────────────┐
│                    USER (Browser)                     │
│           React + TypeScript + Vite SPA               │
│    [Student UI]  [Admin UI]  [Institution UI]         │
└───────────────────────┬──────────────────────────────┘
                        │ REST API calls (JWT Auth)
                        │
┌───────────────────────▼──────────────────────────────┐
│             Node.js Express API Server                 │
│                  (apiServer.ts)                       │
│                                                       │
│  /api/auth     /api/interviews    /api/resumes         │
│  /api/gemini   /api/career-plan   /api/subscription   │
│  /api/s3       /api/sns           /api/rekognition     │
└──────┬─────────────┬──────────────────┬───────────────┘
       │             │                  │
       ▼             ▼                  ▼
┌──────────┐  ┌─────────────┐   ┌────────────────┐
│ Supabase │  │  AWS Cloud  │   │  External APIs │
│PostgreSQL│  │  S3  SES    │   │                │
│          │  │  SNS Rekog  │   │  Gemini AI     │
│ (main DB)│  │  Lambda EC2 │   │  Groq / OpenAI │
└──────────┘  └─────────────┘   │  Judge0 (code) │
                                │  ElevenLabs    │
                                │  Razorpay      │
                                └────────────────┘
```

**How to explain this diagram:**
- **Top**: The browser runs a React SPA (Single Page App). Different UI dashboards for students, admins, institutions.
- **Middle**: All requests go through our Express backend. It handles auth (JWT tokens), business logic, and proxies API calls so secret keys are never exposed to the frontend.
- **Bottom left**: Supabase PostgreSQL is the main database — stores users, interviews, resumes, subscriptions.
- **Bottom middle**: AWS services handle file storage (S3), emails (SES), notifications (SNS), face detection (Rekognition), and serverless functions (Lambda).
- **Bottom right**: External AI and service APIs — Gemini for interviews, Judge0 for code execution, ElevenLabs for voice, Razorpay for payments.

---

## 🔄 How an Interview Works (flow to explain)

```
Student logs in
     ↓
Selects role (e.g., "Software Engineer")
     ↓
Round 1: Aptitude MCQ (timed, proctored)
     ↓
Admin reviews score → shortlists → sends email (AWS SES)
     ↓
Round 2: Coding Challenge (Judge0 runs the code)
     ↓
Round 3: AI Mock Interview (Gemini asks questions via ElevenLabs voice)
     ↓
AI generates score + feedback report
     ↓
Student sees history + downloadable PDF report
```

---

## 🗃️ Database Tables (key ones to know)

| Table | Purpose |
|---|---|
| `users` | Student accounts |
| `institutions` | College/company accounts |
| `interviews` | All interview sessions + scores |
| `resumes` | Uploaded resumes + ATS scores |
| `round1_aptitude` | MCQ test results |
| `practice_coding` | Coding practice sessions |
| `career_plans` | Generated learning roadmaps |
| `gap_analysis` | Skill gap reports |
| `subscriptions` | Institution billing plans |
| `subscription_transactions` | Payment history |

---

## ❓ Common Interview Questions & Crisp Answers

**Q: What problem does VidyaMitra solve?**
> Students lack access to quality mock interviews. Institutions waste time manually screening candidates. VidyaMitra automates both — AI interviews for students, AI resume screening for institutions.

**Q: Why did you choose React + TypeScript?**
> React gives component-based reusability and fast rendering. TypeScript catches errors at compile time, which is critical when working with complex data from multiple APIs.

**Q: How does the AI interview work technically?**
> The backend proxies a request to Google Gemini with the student's role and resume context. Gemini generates questions. ElevenLabs converts text to speech. Student answers via mic (Web Speech API). Answer text goes back to Gemini for scoring and feedback.

**Q: How did you handle security?**
> All API keys are stored server-side only — never in the frontend code. JWT tokens authenticate every request. OpenAI and Gemini calls are proxied through our backend so the browser never touches secret keys.

**Q: What is Judge0?**
> Judge0 is a code execution API. When a student submits code, we send it to Judge0, which compiles and runs it in an isolated sandbox and returns the output. We either use RapidAPI's hosted Judge0 or a self-hosted instance on AWS EC2.

**Q: How does the ATS scoring work?**
> We extract text from the uploaded PDF using pdf.js. Then we send the resume text + target job role to Gemini AI, which analyzes keyword match, formatting, and relevance — and returns a score out of 100 with specific improvement suggestions.

**Q: What is Razorpay used for?**
> Institutions pay for platform access via subscription plans. Razorpay handles the payment flow (UPI, card, net banking). After payment, a webhook from Razorpay tells our server to activate the subscription in the database.

**Q: How does proctoring work?**
> We use three layers: (1) Tab-switch detection via browser focus events, (2) Webcam monitoring using TensorFlow.js BlazeFace to verify face presence, (3) AWS Rekognition for face matching. If violations are detected, the interview is flagged or aborted.

**Q: What is AWS SNS used for?**
> SNS (Simple Notification Service) is used for bulk email/SMS notifications — like sending marketing messages or alerts to subscribed institutions and students.

---

## 🔑 Key Numbers to Remember

| Metric | Value |
|---|---|
| Total pages/routes | 25+ |
| Total React components | 40+ |
| API endpoints | 50+ |
| Database tables | 20+ |
| AI APIs integrated | 4 (Gemini, Groq, OpenAI, ElevenLabs) |
| AWS services used | 6 (S3, SES, SNS, Rekognition, Lambda, EC2) |
| Subscription plans | 4 (Starter → Mega) |
| Code execution | Judge0 API |

---

## ✅ One-Liner Summary (to close any answer)

> "VidyaMitra is a full-stack AI-powered platform built with React, Node.js, Supabase, and multiple AI/cloud services — it automates mock interviews for students and candidate screening for institutions, with a SaaS subscription model on top."
