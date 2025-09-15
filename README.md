# Lead Processor Technical Documentation

## 📋 Overview
This documentation provides comprehensive technical information about the Lead Processor (CRM/LMS) system. The system is a full-stack lead management and CRM platform built to handle lead processing, opportunity tracking, and sales team coordination.

## 🏗️ System Architecture

| Component       | Name/Technology         | Version         | Hosted On                        | Software Type      |
|-----------------|------------------------|-----------------|----------------------------------|--------------------|
| **Backend**     | Node.js / Express.js   | Node.js: LTS<br>Express.js: 4.x | DigitalOcean App Platform        | Open Source        |
| **Backend Lang**| TypeScript             | 5.x             | -                                | Open Source        |
| **Frontend**    | Next.js                | 14+             | Vercel Cloud                     | Open Source        |
| **Frontend UI** | Material-UI            | ^6.1.3          | -                                | Open Source        |
| **Database**    | MongoDB                | 7.0.15          | DigitalOcean Managed Databases   | Open Source        |
| **ODM**         | Mongoose               | 7.x             | -                                | Open Source        |
| **Cache/Queue** | Valkey / (previously Redis) | 8.x         | DigitalOcean Managed Databases (2 GB RAM / 1vCPU / 30 GiB Disk / Primary only / BLR1) | Open Source        |
| **Auth**        | JWT                    | -               | -                                | Open Source        |
| **Monitoring**  | Elastic APM            | Saas          | Elastic Cloud Serverless Observability | Paid              |
| **WhatsApp**    | Doubletick | Cloud-based    | Saas | Paid              |



## 📁 Documentation Structure

```
/docs
├── README.md                    # This file - main overview
├── CHANGELOG.md                 # Version history and feature releases
├── architecture-overview.md     # High-level system architecture
├── tech-stack.md               # Technology stack justification
├── backend/
│   ├── api-documentation.md    # REST API documentation
│   ├── database-schema.md      # MongoDB schema design
│   ├── message-queues.md       # Redis queue implementation
│   └── background-jobs.md      # Cron jobs and scheduled tasks
├── frontend/
│   ├── architecture.md         # Frontend architecture
│   └── frontend-dev-onboarding.md # Frontend development guide
├── deployment/
│   └── deployment-infrastructure.md  # Deployment infrastructure setup
├── security/
│   └── security-assessment.md  # Security practices and assessment
├── monitoring-logging.md       # Observability setup
├── dev-onboarding.md           # Developer setup guide
└── known-tech-debt.md          # Technical debt inventory
```

## 🚀 Quick Start
1. Read [dev-onboarding.md](./dev-onboarding.md) to set up your development environment
2. Review [architecture-overview.md](./architecture-overview.md) for system understanding
3. Refer [database-schema.md](./backend/database-schema.md) For DB Schema
4. Check [backend/api-documentation.md](./backend/api-documentation.md) for API details

## 📞 Contact
For technical questions regarding this documentation, please contact the development team.
