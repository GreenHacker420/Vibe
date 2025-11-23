# Dating App MVP - Architecture & Development Plan

## Tinder-Like Dating Platform

---

## Table of Contents
- [Executive Summary](#executive-summary)
- [Prerequisites](#prerequisites)
- [Scaling to 10K User Base](#scaling-to-10k-user-base)
- [Execution Plan with Milestones](#execution-plan-with-milestones)
- [Core Features](#core-features)
- [User Flow](#user-flow)
- [Technical Architecture](#technical-architecture)
- [Database Design](#database-design)
- [API Endpoints](#api-endpoints)
- [Risk Management](#risk-management)
- [Success Metrics](#success-metrics)
- [Budget & Resources](#budget--resources)

---

## Executive Summary

**Objective**: Build a proven dating app following Tinder's successful model with essential features to validate market fit and scale to 10,000 active users.

**Approach**: Start with core features (swipe, match, chat), launch to initial users, iterate based on feedback, and scale infrastructure as user base grows.

**Timeline**: 4 weeks MVP + 8-12 weeks to reach 10K users

---

## Prerequisites

### Technical Requirements

#### Development Tools
- Code editor (VS Code)
- Git & GitHub for version control
- Postman for API testing
- MongoDB Compass (database management)
- Xcode (for iOS) / Android Studio (for Android)

#### Accounts & Services Setup
- AWS/DigitalOcean/Google Cloud account
- MongoDB Atlas account (or self-hosted MongoDB)
- Twilio account (SMS verification)
- Firebase account (push notifications)
- Stripe/Razorpay (payment processing for future premium features)
- Domain name & SSL certificate
- Apple Developer Account ($99/year)
- Google Play Developer Account ($25 one-time)

#### Development Environment
- Node.js 18+ installed
- MongoDB 6+ installed locally (or Atlas connection)
- Redis 7+ installed locally
- React Native development environment setup
- Docker (optional, for containerization)

### Team Requirements

**For MVP (Month 1)**:
- 1 React Native Developer (Frontend)
- 1 Backend Developer (Node.js + MongoDB)
- 1 UI/UX Designer (Part-time)
- 1 QA Tester (Part-time)

**For Scaling to 10K (Months 2-3)**:
- Add 1 DevOps Engineer
- Add 1 Backend Developer
- Add 1 Mobile Developer
- Content Moderator (Part-time)

### Infrastructure Prerequisites

**Initial Setup (0-1000 users)**:
- 1 Application server (2 vCPU, 4GB RAM)
- MongoDB instance (2 vCPU, 4GB RAM) or MongoDB Atlas M10
- 1 Redis instance (1GB)
- S3 bucket for images
- CDN for content delivery

**Scaling Setup (1000-10K users)** - See [Scaling Section](#scaling-to-10k-user-base)

### Legal & Compliance

- Privacy Policy & Terms of Service drafted
- GDPR compliance documentation (if targeting EU)
- Age verification mechanism (18+)
- Content moderation guidelines
- User data handling procedures
- Legal entity registration
- Business bank account

### Budget Prerequisites

**Development Phase (Month 1)**:
- Development: $10,000 - $15,000
- Infrastructure: $300 - $500
- Third-party services: $200 - $400
- Design: $1,500 - $2,500
- Legal: $1,000 - $2,000
- **Total**: $13,000 - $20,000

**Operating Costs (Months 2-3 for 10K users)**:
- Infrastructure: $1,500 - $3,000/month
- Team salaries: $15,000 - $25,000/month
- Marketing: $3,000 - $5,000/month
- Third-party services: $800 - $1,500/month
- **Total**: $20,000 - $35,000/month

---

## Scaling to 10K User Base

### Infrastructure Architecture for 10K Users

#### Growth Phases

**Phase 1: 0-1,000 Users (Week 1-4)**

![Phase 1 Architecture](./diagrams/architecture-phase1.png)

```
Single Server Architecture
┌─────────────────────────────────┐
│  Load Balancer (Basic)          │
└────────────┬────────────────────┘
             │
┌────────────▼────────────────────┐
│  App Server (t3.medium)         │
│  - API + WebSocket              │
│  - 2 vCPU, 4GB RAM              │
└────────────┬────────────────────┘
             │
    ┌────────┼────────┐
    │        │        │
    ▼        ▼        ▼
┌───────┐ ┌──────┐ ┌────────┐
│MongoDB│ │Redis │ │   S3   │
│(M10)  │ │(1GB) │ │(Images)│
└───────┘ └──────┘ └────────┘

Cost: $300-500/month
```

**Phase 2: 1,000-5,000 Users (Week 5-8)**

![Phase 2 Architecture](./diagrams/architecture-phase2.png)

```
Horizontal Scaling Architecture
┌─────────────────────────────────┐
│  Load Balancer (ALB)            │
└────────┬─────────┬──────────────┘
         │         │
    ┌────▼────┐ ┌──▼───────┐
    │  App 1  │ │  App 2   │
    │(t3.med) │ │(t3.med)  │
    └────┬────┘ └──┬───────┘
         │         │
         └────┬────┘
              │
    ┌─────────┼─────────┐
    │         │         │
    ▼         ▼         ▼
┌───────┐ ┌───────┐ ┌────────┐
│MongoDB│ │Redis  │ │   S3   │
│Primary│ │Cluster│ │+ CDN   │
│+ Read │ │(2GB)  │ │        │
│Replica│ │       │ │        │
└───────┘ └───────┘ └────────┘

Cost: $800-1,200/month
```

**Phase 3: 5,000-10,000 Users (Week 9-12)**

![Phase 3 Architecture](./diagrams/architecture-phase3.png)

```
Microservices Architecture
┌─────────────────────────────────┐
│  Load Balancer (ALB)            │
└────┬─────┬─────┬─────┬──────────┘
     │     │     │     │
     ▼     ▼     ▼     ▼
┌────────┐ ┌────────┐ ┌────────┐
│ App 1  │ │ App 2  │ │ App 3  │
│(t3.med)│ │(t3.med)│ │(t3.med)│
└────────┘ └────────┘ └────────┘
     │          │          │
     └──────────┼──────────┘
                │
    ┌───────────┼───────────┐
    │           │           │
    ▼           ▼           ▼
┌─────────┐ ┌─────────┐ ┌─────────┐
│Auth Svc │ │Match Svc│ │Chat Svc │
└─────────┘ └─────────┘ └─────────┘
    │           │           │
    └───────────┼───────────┘
                │
    ┌───────────┼───────────┐
    │           │           │
    ▼           ▼           ▼
┌─────────┐ ┌─────────┐ ┌─────────┐
│ MongoDB │ │  Redis  │ │   S3    │
│Primary +│ │ Cluster │ │ + CDN   │
│2 Replicas│ │ (4GB)   │ │         │
│(Sharded)│ │         │ │         │
└─────────┘ └─────────┘ └─────────┘

Cost: $1,500-3,000/month
```

### Detailed Infrastructure Specifications

#### For 10,000 Active Users

**Application Servers**:
- 3x EC2 t3.medium instances (2 vCPU, 4GB RAM each)
- Auto-scaling group (min: 2, max: 5)
- Application Load Balancer
- **Cost**: $150/server × 3 = $450/month

**Database (MongoDB)**:
- MongoDB Atlas M30 (8GB RAM) or self-hosted cluster
- 1 Primary + 2 Read Replicas (Replica Set)
- 500GB SSD storage
- Automated backups (7-day retention)
- **Cost**: Atlas M30 $280/month or self-hosted $400/month

**Caching Layer**:
- ElastiCache Redis cluster
- 2 nodes, 4GB RAM each
- Multi-AZ for high availability
- **Cost**: $150/month

**Storage & CDN**:
- S3 bucket: 500GB storage, 2TB transfer
- CloudFront CDN: 5TB/month data transfer
- Estimated 100,000 images (average 500KB each)
- **Cost**: S3 $25 + CDN $400 = $425/month

**Message Queue**:
- AWS SQS for background jobs
- 10 million requests/month
- **Cost**: $50/month

**Monitoring & Logging**:
- CloudWatch logs and metrics
- Error tracking (Sentry)
- **Cost**: $100/month

**Third-Party Services**:
- Twilio SMS: 10,000 verifications × $0.02 = $200
- Firebase Push: Free tier (unlimited)
- Image processing: $100/month
- **Cost**: $300/month

**Total Infrastructure Cost for 10K Users**: **$1,755/month**

With buffer and contingency: **$2,000-2,500/month**

### MongoDB Scaling Strategy

#### Database Architecture

**Collections Structure**:
```javascript
// users collection
{
  _id: ObjectId,
  phone_number: String (indexed, unique),
  email: String (indexed, unique),
  password_hash: String,
  is_verified: Boolean,
  created_at: Date,
  last_active: Date
}

// profiles collection
{
  _id: ObjectId,
  user_id: ObjectId (ref: users),
  name: String,
  age: Number,
  gender: String,
  bio: String,
  location: {
    type: "Point",
    coordinates: [lng, lat] // [longitude, latitude]
  },
  photos: [String], // Array of S3 URLs
  preferences: {
    age_min: Number,
    age_max: Number,
    distance: Number, // in km
    gender: String
  }
}

// swipes collection
{
  _id: ObjectId,
  swiper_id: ObjectId (ref: users),
  swiped_id: ObjectId (ref: users),
  action: String, // 'like' or 'pass'
  created_at: Date
}

// matches collection
{
  _id: ObjectId,
  user1_id: ObjectId (ref: users),
  user2_id: ObjectId (ref: users),
  matched_at: Date,
  is_active: Boolean
}

// messages collection
{
  _id: ObjectId,
  match_id: ObjectId (ref: matches),
  sender_id: ObjectId (ref: users),
  content: String,
  message_type: String, // 'text', 'image', 'voice'
  is_read: Boolean,
  sent_at: Date
}
```

#### Indexes for Performance

```javascript
// Geospatial index for location-based queries
db.profiles.createIndex({ location: "2dsphere" })

// Compound indexes for matching queries
db.swipes.createIndex({ swiper_id: 1, swiped_id: 1 }, { unique: true })
db.swipes.createIndex({ swiper_id: 1, created_at: -1 })

// Active users index
db.users.createIndex({ last_active: -1, is_verified: 1 })

// Match lookup index
db.matches.createIndex({ user1_id: 1, user2_id: 1 })
db.matches.createIndex({ user1_id: 1, is_active: 1 })

// Message history index
db.messages.createIndex({ match_id: 1, sent_at: -1 })
```

#### Caching Strategy

```javascript
// Redis cache keys and TTL
User Profile: `profile:${userId}` - TTL: 1 hour
Discovery Feed: `discovery:${userId}` - TTL: 5 minutes
Match List: `matches:${userId}` - TTL: 30 seconds
Active Users Count: `stats:active` - TTL: 1 minute
```

### Performance Targets for 10K Users

| Metric | Target | Strategy |
|--------|--------|----------|
| API Response Time | <200ms | Caching, MongoDB indexing |
| Profile Load Time | <1s | CDN, image optimization |
| Match Detection | <500ms | Redis queue, async processing |
| Message Delivery | <300ms | WebSocket, Redis pub/sub |
| Discovery Feed | <1s | Pre-computed matches, caching |
| Image Load Time | <500ms | CDN, progressive loading |
| Concurrent Users | 1,000+ | Horizontal scaling, load balancer |
| Database Queries/sec | 500+ | Read replicas, connection pooling |
| Uptime | 99.5%+ | Multi-AZ deployment, monitoring |

### Scalability Bottlenecks & Solutions

| Bottleneck | Impact at Scale | Solution |
|------------|----------------|----------|
| **Database Writes** | Matches, messages pile up | Write queue, batch inserts, MongoDB sharding |
| **Image Storage** | S3 GET requests expensive | CloudFront CDN caching |
| **Discovery Algorithm** | Slow profile queries | Pre-compute matches, Redis cache, geospatial indexes |
| **WebSocket Connections** | Memory exhaustion | Separate chat server, Socket.io Redis adapter |
| **Location Queries** | Slow geospatial searches | MongoDB 2dsphere indexes |
| **User Sessions** | Memory bloat | Redis session store |

### Monitoring & Alerts (for 10K users)

**Key Metrics to Track**:
- Server CPU/Memory usage (alert at 70%)
- MongoDB connections (alert at 80% pool)
- API response times (alert if p95 > 500ms)
- Error rates (alert if > 1%)
- Active WebSocket connections
- Cache hit rates (target: 80%+)
- Queue length (SQS message backlog)

**Tools**:
- CloudWatch for infrastructure
- MongoDB Atlas monitoring (if using Atlas)
- Sentry for error tracking
- Custom dashboard for business metrics
- PagerDuty for on-call alerts

### Cost Breakdown for 10K Users

| Category | Monthly Cost |
|----------|--------------|
| Application Servers (3x) | $450 |
| MongoDB (Atlas M30 or self-hosted) | $280-400 |
| Redis Cache | $150 |
| S3 Storage + CDN | $425 |
| Load Balancer | $50 |
| Message Queue | $50 |
| Monitoring | $100 |
| SMS Verification (Twilio) | $200 |
| Push Notifications | $0 (free) |
| Image Processing | $100 |
| Backup Storage | $75 |
| **Total Infrastructure** | **$1,880-2,000** |
| **With 20% buffer** | **$2,256-2,400** |

**Team Costs** (not infrastructure):
- 2 Backend Developers: $10,000
- 2 Mobile Developers: $10,000
- 1 DevOps Engineer: $6,000
- 1 QA Tester: $3,000
- 1 Content Moderator: $2,000
- **Total Team**: **$31,000/month**

**Grand Total Operating Cost**: **$33,256-33,400/month** for 10K users

---

## Execution Plan with Milestones

### Phase 1: MVP Development (Weeks 1-4)

**Goal**: Launch functional app with core features

#### Week 1: Foundation

**Day 1-2**: Project setup, architecture finalization
- Git repository setup
- MongoDB database design and setup
- API endpoint documentation
- UI/UX mockups approval

**Day 3-5**: Authentication system
- Phone/email signup
- OTP verification (Twilio integration)
- JWT token implementation
- Login/logout functionality

**Day 6-7**: Profile creation
- Photo upload to S3
- Profile form (bio, age, gender)
- Preference settings
- MongoDB profile schema implementation

**Milestone 1**: User can register and create profile ✅

#### Week 2: Core Features

**Day 8-10**: Discovery feed
- Swipe card UI
- Location-based filtering (MongoDB geospatial queries)
- Age/gender preference filtering
- Like/Pass action recording

**Day 11-12**: Matching system
- Match detection algorithm
- Match notification
- Match list view

**Day 13-14**: Testing & fixes
- Integration testing
- Bug fixes
- Performance optimization

**Milestone 2**: Complete swipe-match flow ✅

#### Week 3: Chat System

**Day 15-17**: Real-time chat backend
- WebSocket server setup (Socket.io)
- Message storage (MongoDB messages collection)
- Message delivery system
- Typing indicators

**Day 18-20**: Chat UI
- Chat interface
- Message bubbles
- Real-time updates
- Message history

**Day 21**: Unmatch & settings
- Unmatch functionality
- Basic settings page

**Milestone 3**: Working chat system ✅

#### Week 4: Testing & Launch

**Day 22-23**: Comprehensive testing
- End-to-end user flow testing
- Load testing (100 concurrent users)
- Security audit
- Bug fixes

**Day 24-25**: Deployment
- Production environment setup
- MongoDB Atlas setup or server deployment
- Server deployment
- App store builds

**Day 26-28**: Beta launch
- Internal testing (10 users)
- Beta invite to 50 users
- Gather feedback
- Critical fixes

**Day 29-30**: Documentation & handoff
- API documentation
- Admin panel basics
- Analytics setup

**Milestone 4**: MVP Live with 50-100 Beta Users ✅

---

### Phase 2: Growth to 1,000 Users (Weeks 5-8)

**Goal**: Validate product-market fit, gather user feedback

#### Week 5: Stabilization
- **User Target**: 100-300 users
- Fix critical bugs from beta feedback
- Add basic analytics tracking
- Implement photo moderation (manual review)
- Set up customer support email
- **Infrastructure**: Single server setup ($300/month)

#### Week 6: Feature Enhancement
- **User Target**: 300-600 users
- Add profile photo verification
- Implement basic report/block system
- Improve matching algorithm with activity scoring
- Push notification improvements
- **Marketing**: Soft launch on social media, college campuses

#### Week 7: Optimization
- **User Target**: 600-900 users
- MongoDB query optimization (add indexes)
- Add more profile fields (education, height, etc.)
- Implement daily swipe limits (50 for free users)
- Add "undo" last swipe feature (paid)
- **Infrastructure**: Monitor performance, prepare for scaling

#### Week 8: Scale Preparation
- **User Target**: 900-1,000 users
- Load testing for 2,000 concurrent users
- Set up CDN for images
- Implement Redis caching layer
- Plan premium features
- A/B test onboarding flow

**Milestone 5**: 1,000 Active Users, Positive Feedback ✅

---

### Phase 3: Scaling to 5,000 Users (Weeks 9-12)

**Goal**: Scale infrastructure, add revenue features

#### Week 9: Infrastructure Upgrade
- **User Target**: 1,000-2,000 users
- Deploy second application server
- Set up MongoDB replica set (Primary + Replica)
- Implement Redis cluster
- Configure load balancer
- **Infrastructure**: $800-1,200/month

#### Week 10: Premium Features
- **User Target**: 2,000-3,000 users
- Launch subscription system (Stripe integration)
- Unlimited likes for premium
- "Super like" feature (5 per day free, unlimited paid)
- "Boost" profile visibility
- See who liked you

#### Week 11: Marketing Push
- **User Target**: 3,000-4,000 users
- Launch referral program (invite friends, get premium trial)
- Influencer partnerships
- App store optimization
- Facebook/Instagram ads campaign
- **Marketing Budget**: $3,000-5,000

#### Week 12: Stabilization
- **User Target**: 4,000-5,000 users
- Monitor infrastructure performance
- A/B test premium features
- Improve conversion funnel
- Add analytics dashboard for team

**Milestone 6**: 5,000 Active Users, Revenue Generation ✅

---

### Phase 4: Reaching 10,000 Users (Weeks 13-16)

**Goal**: Achieve 10K users with stable infrastructure

#### Week 13: Infrastructure Scaling
- **User Target**: 5,000-7,000 users
- Deploy third application server
- Add second MongoDB read replica
- Upgrade Redis to 4GB cluster
- Implement MongoDB sharding preparation
- **Infrastructure**: $1,500-2,500/month

#### Week 14: Advanced Features
- **User Target**: 7,000-8,500 users
- Video profile feature (optional 15-sec video)
- Advanced filters (smoking, drinking, pets)
- Instagram integration
- Improved discovery algorithm (ML-based similarity)

#### Week 15: Aggressive Marketing
- **User Target**: 8,500-9,500 users
- Large-scale ad campaigns
- PR outreach and press releases
- Partnership with local businesses
- Event sponsorships
- **Marketing Budget**: $5,000-8,000

#### Week 16: Optimization & Monitoring
- **User Target**: 9,500-10,000+ users
- 24/7 monitoring setup
- Performance tuning
- MongoDB optimization (sharding if needed)
- Cost optimization
- Team expansion planning

**Milestone 7**: 10,000 Active Users Achievement ✅

---

### Execution Summary Timeline

![Timeline Gantt Chart](./diagrams/timeline-gantt.png)

```
Week 1-4:   MVP Development → 100 users
Week 5-8:   Growth Phase 1  → 1,000 users
Week 9-12:  Growth Phase 2  → 5,000 users
Week 13-16: Scale to Target → 10,000 users
```

**Total Timeline**: 16 weeks (4 months) from start to 10K users

---

## Core Features

### MVP Features (Week 1-4)

#### 1. Authentication
- Phone number or email sign-up
- SMS OTP verification
- Basic login/logout

#### 2. Profile Management
- Upload 3-6 photos
- Basic bio (300 characters)
- Essential details: Name, Age, Gender, Location
- Interest in: Gender preference
- Distance preference (5-100 km)
- Age range preference (18-60)

#### 3. Discovery/Swiping
- Card-based swipe interface
- Swipe right (Like) / Swipe left (Pass)
- Show profiles based on:
  - Location proximity (MongoDB geospatial queries)
  - Age preference
  - Gender preference
- Daily limit: 50 swipes for free users

#### 4. Matching
- Match occurs when both users like each other
- Match notification
- Match list view

#### 5. Chat
- Text messaging between matches only
- Real-time message delivery (WebSocket)
- Message history
- Basic typing indicator
- Unmatch option

#### 6. Settings
- Edit profile
- Update preferences
- Delete account
- Logout

---

## User Flow

![User Flow Diagram](./diagrams/user-flow.png)

### Complete User Journey

1. **Download App** → Sign up with phone/email
2. **OTP Verification** → Verify identity
3. **Create Profile** → Add photos, bio, and details
4. **Set Preferences** → Age range, distance, gender
5. **Discovery Feed** → Swipe on profiles
6. **Match** → When both users like each other
7. **Chat** → Send messages to matches
8. **Continue or Unmatch** → Keep chatting or remove match

---

## Technical Architecture

### Technology Stack

#### Frontend
- **Framework**: React Native
- **State Management**: Redux Toolkit or Zustand
- **Navigation**: React Navigation
- **UI Library**: React Native Paper or Native Base
- **Image Handling**: react-native-fast-image
- **Maps**: React Native Maps
- **HTTP Client**: Axios
- **WebSocket**: Socket.io-client

#### Backend
- **Runtime**: Node.js 18+
- **Framework**: Express.js
- **API Style**: RESTful + WebSocket
- **Authentication**: JWT tokens + bcrypt
- **Real-time**: Socket.io for chat
- **Validation**: Joi or express-validator

#### Database
- **Primary DB**: MongoDB 6+
  - Collections: users, profiles, matches, messages, swipes
  - Geospatial queries for location-based matching
  - Replica set for high availability
- **Cache**: Redis 7
  - Session storage
  - Active user cache
  - Match queue
  - Rate limiting

#### Storage
- **Images**: AWS S3 or Cloudinary
- **CDN**: CloudFront for fast image delivery

#### Infrastructure
- **Hosting**: AWS EC2 / DigitalOcean / Heroku
- **Container**: Docker (optional for dev)
- **Load Balancer**: AWS ALB or Nginx
- **Version Control**: Git + GitHub

#### Third-Party Services
- **SMS**: Twilio or Firebase Auth
- **Push Notifications**: Firebase Cloud Messaging (FCM)
- **Monitoring**: Basic logging (Winston) + Sentry
- **Analytics**: Mixpanel or Amplitude

---

### System Architecture Diagrams

#### Phase 1: Single Server (0-1K Users)
![Phase 1 Architecture](./diagrams/architecture-phase1.png)

#### Phase 2: Horizontal Scaling (1K-5K Users)
![Phase 2 Architecture](./diagrams/architecture-phase2.png)

#### Phase 3: Microservices (5K-10K Users)
![Phase 3 Architecture](./diagrams/architecture-phase3.png)

---

## Database Design

### MongoDB Schema

![Database Schema](./diagrams/database-schema.png)

#### Collections Overview

```javascript
// 1. users collection
{
  _id: ObjectId("507f1f77bcf86cd799439011"),
  phone_number: "+1234567890",
  email: "user@example.com",
  password_hash: "$2b$10$...",
  is_verified: true,
  created_at: ISODate("2025-01-15T10:30:00Z"),
  last_active: ISODate("2025-01-20T14:22:00Z")
}

// 2. profiles collection
{
  _id: ObjectId("507f1f77bcf86cd799439012"),
  user_id: ObjectId("507f1f77bcf86cd799439011"),
  name: "John Doe",
  age: 28,
  gender: "male",
  bio: "Love hiking and coffee",
  location: {
    type: "Point",
    coordinates: [-122.4194, 37.7749] // [lng, lat] San Francisco
  },
  photos: [
    "https://cdn.example.com/user123/photo1.jpg",
    "https://cdn.example.com/user123/photo2.jpg"
  ],
  preferences: {
    age_min: 24,
    age_max: 35,
    distance: 25, // km
    gender: "female"
  }
}

// 3. swipes collection
{
  _id: ObjectId("507f1f77bcf86cd799439013"),
  swiper_id: ObjectId("507f1f77bcf86cd799439011"),
  swiped_id: ObjectId("507f1f77bcf86cd799439022"),
  action: "like", // or "pass"
  created_at: ISODate("2025-01-20T14:25:00Z")
}

// 4. matches collection
{
  _id: ObjectId("507f1f77bcf86cd799439014"),
  user1_id: ObjectId("507f1f77bcf86cd799439011"),
  user2_id: ObjectId("507f1f77bcf86cd799439022"),
  matched_at: ISODate("2025-01-20T14:25:30Z"),
  is_active: true
}

// 5. messages collection
{
  _id: ObjectId("507f1f77bcf86cd799439015"),
  match_id: ObjectId("507f1f77bcf86cd799439014"),
  sender_id: ObjectId("507f1f77bcf86cd799439011"),
  content: "Hey! How are you?",
  message_type: "text", // or "image", "voice"
  is_read: false,
  sent_at: ISODate("2025-01-20T14:30:00Z")
}
```

### MongoDB Indexes

```javascript
// Essential indexes for performance

// Geospatial index for location queries
db.profiles.createIndex({ location: "2dsphere" });

// Swipes lookup
db.swipes.createIndex({ swiper_id: 1, swiped_id: 1 }, { unique: true });
db.swipes.createIndex({ swiper_id: 1, created_at: -1 });

// Active users
db.users.createIndex({ last_active: -1, is_verified: 1 });

// Match queries
db.matches.createIndex({ user1_id: 1, user2_id: 1 });
db.matches.createIndex({ user1_id: 1, is_active: 1 });
db.matches.createIndex({ user2_id: 1, is_active: 1 });

// Message history
db.messages.createIndex({ match_id: 1, sent_at: -1 });
db.messages.createIndex({ sender_id: 1, sent_at: -1 });

// Profile search
db.profiles.createIndex({ user_id: 1 }, { unique: true });
db.profiles.createIndex({ age: 1, gender: 1 });
```

---

## API Endpoints

### API Request Flow
![API Flow Diagram](./diagrams/api-flow.png)

### Core Endpoints

#### Authentication
```
POST   /api/auth/register        - Register with phone/email
POST   /api/auth/verify-otp      - Verify OTP code
POST   /api/auth/login           - Login
POST   /api/auth/logout          - Logout
POST   /api/auth/refresh-token   - Refresh JWT token
```

#### Profile
```
POST   /api/profile              - Create profile
GET    /api/profile/me           - Get own profile
PUT    /api/profile              - Update profile
POST   /api/profile/photos       - Upload photos
DELETE /api/profile/photos/:id   - Delete photo
PUT    /api/profile/preferences  - Update preferences
```

#### Discovery
```
GET    /api/discovery/profiles   - Get profiles to swipe (with filters)
POST   /api/swipe                -
