# Medhas Academy - Technical Architecture & Technology Stack

## Purpose

This document defines the initial technology stack and architectural decisions for Medhas Academy.

The platform is expected to support approximately 50-200 active learners during its first phase and is designed as a modular monolithic application. Future migration to microservices can be considered if organizational requirements and platform scale increase significantly.

---

# Architecture Strategy

## Current Approach

Modular Monolith

The platform will be developed as a single application with clear domain separation.

Example Domains:

* Authentication
* User Management
* Learning Management
* Assessments
* Projects
* Progress Tracking
* Certifications
* Admin Management

### Benefits

* Faster development
* Easier deployment
* Simpler debugging
* Lower infrastructure cost
* Easier team collaboration

---

# Frontend

## Framework

Next.js

### Reason

* Production-ready React framework
* Excellent routing system
* Server Components
* Server Actions
* Good SEO support
* Scalable application structure

---

## Language

TypeScript

### Reason

* Type safety
* Better maintainability
* Improved developer experience
* Reduced runtime bugs

---

## Styling

Tailwind CSS

### Reason

* Fast UI development
* Consistent design system
* Easy maintenance

---

## Component Library

Shadcn UI

### Reason

* Modern design system
* Highly customizable
* Excellent dashboard components
* Form and table support

---

## Animations

Framer Motion

### Usage

* Dashboard transitions
* Progress indicators
* Statistics animations
* Interactive learning experiences

---

# Backend

## Runtime

Bun

### Reason

* Fast startup times
* High performance
* Native TypeScript support
* Simplified tooling

---

## API Layer

Hono

### Reason

* Lightweight
* Type-safe
* Bun optimized
* High performance

---

# Database

## Primary Database

PostgreSQL

### Reason

The platform contains highly relational data:

* Users
* Roles
* Courses
* Phases
* Modules
* Lessons
* Assessments
* Projects
* Reviews
* Certifications
* Progress Tracking

PostgreSQL provides strong consistency and excellent support for complex relationships.

---

## ORM

Prisma

### Reason

* Type-safe database access
* Migration support
* Excellent developer experience
* Strong TypeScript integration

---

# Authentication & Authorization

## Authentication

Auth.js

### Features

* Email Authentication
* Company Domain Verification
* Session Management
* Role-based Access

---

## Roles

### Admin

Platform management.

### Mentor

Project reviews and learner evaluation.

### Learner

Course consumption and project participation.

---

# File Storage

## Cloud Provider

Amazon Web Services (AWS)

---

## Storage Service

Amazon S3

### Assets Stored

* Course Videos
* PDF Resources
* Research Documents
* Assignment Files
* Project Deliverables
* Certificates
* Images
* Learning Resources

### Reason

* Organization already uses AWS
* Highly scalable
* Reliable
* Cost effective
* Secure access management

---

# Media Delivery

## CDN

Amazon CloudFront

### Purpose

* Faster content delivery
* Global asset caching
* Reduced S3 load
* Improved learner experience

---

# Email Service

## AWS SES

### Usage

* Account Verification
* Password Reset
* Program Notifications
* Assessment Results
* Certificate Delivery

### Reason

Native AWS integration.

---

# Caching Layer

## Redis

### Future Usage

* Session Caching
* Rate Limiting
* Leaderboards
* Frequently Accessed Data
* Assessment Statistics

### Initial Phase

Optional

Can be introduced after MVP launch.

---

# Certificate Generation

## PDF Generation

PDF-Lib

### Usage

* FDE Certificates
* Assessment Reports
* Completion Reports

---

# Analytics

## PostHog

### Track

* Lesson Completion
* User Progress
* Assessment Performance
* Platform Usage
* Learning Drop-off Points

### Purpose

Measure learning effectiveness and platform engagement.

---

# AI Integrations

## Supported Providers

* OpenAI
* Anthropic
* Google Gemini

---

## SDK Layer

Vercel AI SDK

### Purpose

Provide a unified interface for:

* AI Mentor Features
* Quiz Generation
* Project Evaluation
* Content Assistance
* Future AI Capabilities

---

# Infrastructure

## Containerization

Docker

### Purpose

* Consistent deployments
* Simplified environment management
* Easy scalability

---

## Reverse Proxy

Nginx

### Purpose

* Request routing
* SSL termination
* Static asset optimization

---

# Deployment Strategy

## Initial Deployment

Single VPS Instance

### Services

* Next.js Application
* Hono API
* PostgreSQL
* Nginx
* Docker

### Expected Capacity

50 - 200 Active Learners

---

# Future Scaling Strategy

When platform usage grows significantly:

### Phase 1

* Dedicated PostgreSQL Server
* Dedicated Redis Server

### Phase 2

* Separate API Services
* Separate Worker Services

### Phase 3

* Microservice Architecture
* Kubernetes Deployment

Migration to microservices will occur only when operational requirements justify the added complexity.

---

# Final Technology Stack

Frontend

* Next.js
* TypeScript
* Tailwind CSS
* Shadcn UI
* Framer Motion

Backend

* Bun
* Hono
* TypeScript

Database

* PostgreSQL
* Prisma

Authentication

* Auth.js

Storage

* AWS S3
* CloudFront

Email

* AWS SES

Caching

* Redis (Future Phase)

Analytics

* PostHog

AI

* OpenAI
* Anthropic
* Gemini
* Vercel AI SDK

Infrastructure

* Docker
* Nginx

Deployment

* VPS
* Modular Monolith Architecture
