# Lead Processor Technical Documentation

## 📋 Overview
This documentation provides comprehensive technical information about the Lead Processor (CRM/LMS) system. The system is a full-stack lead management and CRM platform built to handle lead processing, opportunity tracking, and sales team coordination.

## 🏗️ System Architecture
- **Backend**: Node.js/Express.js with TypeScript
- **Frontend**: Next.js 14+ with TypeScript, Material-UI
- **Database**: MongoDB (v7.0.15) with Mongoose ODM
- **Cache/Queue**: Redis (redis_version:7.2.4) for caching and message queuing
- **Authentication**: JWT-based authentication with session management
- **Monitoring**: Elastic APM, BetterStack

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
