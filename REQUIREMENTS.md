# 📋 DharmaCodex — Requirements Specification

**Version**: 1.0  
**Last Updated**: February 11, 2026  
**Document Type**: Functional & Non-Functional Requirements

---

## Table of Contents

1. [Introduction](#introduction)
2. [Stakeholders](#stakeholders)
3. [Functional Requirements](#functional-requirements)
4. [Non-Functional Requirements](#non-functional-requirements)
5. [User Stories](#user-stories)
6. [System Requirements](#system-requirements)
7. [API Requirements](#api-requirements)
8. [Security Requirements](#security-requirements)
9. [Performance Requirements](#performance-requirements)
10. [Compliance Requirements](#compliance-requirements)
11. [Acceptance Criteria](#acceptance-criteria)

---

## 1. Introduction

### 1.1 Purpose

This document specifies the complete requirements for DharmaCodex, a Gurukul-inspired adaptive programming education platform. It serves as the authoritative reference for development, testing, and validation of the system.

### 1.2 Scope

DharmaCodex encompasses:
- Student learning interface with adaptive AI
- Parent governance dashboard
- Teacher cohort management
- Admin feature-flag console
- Secure code execution sandbox
- Analytics and progress tracking
- Mythological-themed UI system

### 1.3 Definitions

- **Mastery**: Achievement of 70%+ correctness with minimal hints
- **Retry**: Submission attempt without penalty
- **Hint Level**: Progressive guidance (1=conceptual, 2=structural, 3=pseudo-code)
- **Feature Flag**: Dynamic toggle controlling feature availability
- **Sandbox**: Isolated Docker container for code execution
- **Gurukul**: Traditional Indian education system emphasizing mentorship

---

## 2. Stakeholders

### 2.1 Primary Users

| Role | Description | Key Needs |
|------|-------------|-----------|
| Student | Learner (ages 8-18) | Engaging lessons, clear feedback, no pressure |
| Parent | Guardian/supervisor | Progress visibility, screen time control, safety |
| Teacher | Educator managing cohorts | Class analytics, intervention tools, assignments |
| Admin | System administrator | Feature control, system health, user management |

### 2.2 Secondary Stakeholders

- Educational institutions
- Content creators
- Platform developers
- Regulatory bodies (COPPA, GDPR compliance)

---

## 3. Functional Requirements

### 3.1 Authentication & Authorization (FR-AUTH)

#### FR-AUTH-001: User Registration
**Priority**: Critical  
**Description**: System shall allow users to register with email and password  
**Acceptance Criteria**:
- Email validation (RFC 5322 compliant)
- Password minimum 8 characters with 1 uppercase, 1 number
- Role selection (Student/Parent/Teacher)
- Age verification for students
- Email confirmation sent
- Account created in Firestore

#### FR-AUTH-002: User Login
**Priority**: Critical  
**Description**: System shall authenticate users and issue JWT tokens  
**Acceptance Criteria**:
- Email and password verification
- JWT token generated with 24-hour expiration
- Token includes user_id, role, email
- Failed login attempts logged
- Account lockout after 5 failed attempts (15-minute cooldown)

#### FR-AUTH-003: Role-Based Access Control
**Priority**: Critical  
**Description**: System shall restrict access based on user roles  
**Acceptance Criteria**:
- Students access only learning interface
- Parents access child dashboards only
- Teachers access assigned cohorts only
- Admins access all features
- Unauthorized access returns 403 error

#### FR-AUTH-004: Password Reset
**Priority**: High  
**Description**: System shall allow password reset via email  
**Acceptance Criteria**:
- Reset link sent to registered email
- Link expires after 1 hour
- New password meets complexity requirements
- Old password invalidated immediately

### 3.2 Learning Engine (FR-LEARN)

#### FR-LEARN-001: Lesson Structure
**Priority**: Critical  
**Description**: Each lesson shall follow Concept → Practice → Reflection → Mastery flow  
**Acceptance Criteria**:
- Concept explanation with examples
- Practice problem with starter code
- Reflection prompt before completion
- Mastery score calculated (0.0-1.0)
- Progress saved after each stage

#### FR-LEARN-002: Mastery Gating
**Priority**: Critical  
**Description**: System shall lock lessons until prerequisites are mastered  
**Acceptance Criteria**:
- Lessons marked as LOCKED, UNLOCKED, IN_PROGRESS, COMPLETED
- Unlock when all prerequisites have mastery ≥ 0.7
- Dependency graph enforced
- Visual indication of locked status
- Unlock notification sent

#### FR-LEARN-003: Retry-Based Progression
**Priority**: Critical  
**Description**: Students shall have unlimited attempts without penalties  
**Acceptance Criteria**:
- No score reduction for retries
- Attempt count tracked
- Each attempt saved with timestamp
- Retry heatmap generated
- Encouragement messages on failures

#### FR-LEARN-004: Progress Tracking
**Priority**: Critical  
**Description**: System shall track and persist student progress  
**Acceptance Criteria**:
- Progress saved after each submission
- Includes: attempts, hints_used, time_spent, mastery_level
- Real-time sync to Firestore
- Progress retrievable across sessions
- Historical data retained for analytics



### 3.3 Coding Sandbox (FR-SANDBOX)

#### FR-SANDBOX-001: Code Execution
**Priority**: Critical  
**Description**: System shall execute user code in isolated Docker containers  
**Acceptance Criteria**:
- Each execution in fresh container
- Python 3.11+ support
- Stdout and stderr captured
- Exit code returned
- Container destroyed after execution
- Execution time measured

#### FR-SANDBOX-002: Security Isolation
**Priority**: Critical  
**Description**: Sandbox shall prevent malicious code execution  
**Acceptance Criteria**:
- Network disabled (--network none)
- Read-only filesystem
- No privileged operations
- Blacklisted imports blocked (os, sys, subprocess, socket)
- AST validation before execution
- All Linux capabilities dropped

#### FR-SANDBOX-003: Resource Limits
**Priority**: Critical  
**Description**: Sandbox shall enforce resource constraints  
**Acceptance Criteria**:
- Memory limit: 128MB
- CPU limit: 0.5 core
- Timeout: 2 seconds
- Disk I/O: Read-only
- Process limit: 1
- Limits enforced by Docker API

#### FR-SANDBOX-004: Error Handling
**Priority**: High  
**Description**: System shall provide human-readable error messages  
**Acceptance Criteria**:
- Syntax errors explained
- Runtime errors mapped to common causes
- Timeout errors indicate infinite loops
- Memory errors suggest optimization
- Line numbers provided
- Suggestions for fixes

#### FR-SANDBOX-005: Test Case Validation
**Priority**: Critical  
**Description**: System shall validate code against test cases  
**Acceptance Criteria**:
- Multiple test cases per problem
- Input provided to code
- Output compared to expected
- Partial credit not given (all-or-nothing)
- Test results detailed per case
- Hidden test cases supported

### 3.4 AI Adaptation Engine (FR-AI)

#### FR-AI-001: Adaptive Difficulty
**Priority**: High  
**Description**: System shall adjust lesson difficulty based on performance  
**Acceptance Criteria**:
- Track success rate per concept
- Increase difficulty after 3 consecutive successes
- Decrease difficulty after 3 consecutive failures
- Difficulty levels: 1 (Easy) to 5 (Hard)
- Smooth transitions (no sudden jumps)
- User notified of difficulty changes

#### FR-AI-002: Weakness Detection
**Priority**: High  
**Description**: System shall identify concepts student struggles with  
**Acceptance Criteria**:
- Analyze attempts per concept
- Flag concepts with >5 average attempts
- Flag concepts with <0.6 average mastery
- Weakness list updated daily
- Revision suggestions generated
- Dashboard displays weaknesses

#### FR-AI-003: Layered Hint System
**Priority**: Critical  
**Description**: AI shall provide progressive hints without revealing solutions  
**Acceptance Criteria**:
- Level 1: Conceptual reminder (e.g., "Remember loops repeat actions")
- Level 2: Structural guidance (e.g., "You need a for loop here")
- Level 3: Pseudo-code (e.g., "Try: for i in range(n):")
- Never provide complete solution code
- Hint level increases with attempts
- Hints logged for transparency

#### FR-AI-004: AI Silence Mode
**Priority**: High  
**Description**: AI shall remain silent until thresholds are met  
**Acceptance Criteria**:
- Silent for first 2 attempts
- Silent if time spent < 5 minutes
- Activate on 3rd failed attempt OR 5+ minutes
- User can manually request hint
- Silence mode configurable by parent
- Activation reason logged

#### FR-AI-005: Frustration Detection
**Priority**: Medium  
**Description**: System shall detect and respond to student frustration  
**Acceptance Criteria**:
- Detect rapid successive failures (<30 seconds apart)
- Detect repeated identical submissions
- Trigger calming message
- Offer break suggestion
- Notify parent if persistent
- Log frustration events

#### FR-AI-006: AI Transparency
**Priority**: High  
**Description**: System shall log and display all AI decisions  
**Acceptance Criteria**:
- Every hint logged with timestamp
- Trigger reason recorded
- Hint level and content saved
- Parent/teacher can view full log
- Student can see "Why this hint?" explanation
- Logs retained for 90 days

### 3.5 Feature Flag System (FR-FLAGS)

#### FR-FLAGS-001: Dynamic Feature Toggling
**Priority**: Critical  
**Description**: System shall enable/disable features without deployment  
**Acceptance Criteria**:
- Flags stored in Firestore
- Real-time updates via listeners
- Frontend re-evaluates on flag change
- No app restart required
- Flag changes logged with admin ID
- Rollback history maintained

#### FR-FLAGS-002: Targeted Rollouts
**Priority**: High  
**Description**: Flags shall support gradual rollouts to user segments  
**Acceptance Criteria**:
- Rollout percentage (0-100%)
- Target by role (Student/Parent/Teacher/Admin)
- Target by age range
- Target by learning mode (Lite/Guided/Full)
- Deterministic (same user always gets same result)
- A/B testing support

#### FR-FLAGS-003: Dependency Management
**Priority**: Medium  
**Description**: Flags shall enforce feature dependencies  
**Acceptance Criteria**:
- Flags can list required dependencies
- Dependent features disabled if dependency off
- Circular dependencies prevented
- Dependency graph visualized in admin console
- Warning before disabling flag with dependents

#### FR-FLAGS-004: Instant Rollback
**Priority**: Critical  
**Description**: Admin shall be able to disable features instantly  
**Acceptance Criteria**:
- One-click disable in admin console
- Takes effect within 5 seconds
- All users affected immediately
- Rollback reason required
- Previous state saved for re-enable
- Notification sent to dev team

### 3.6 Analytics & Reporting (FR-ANALYTICS)

#### FR-ANALYTICS-001: Student Dashboard
**Priority**: High  
**Description**: Students shall view their progress and statistics  
**Acceptance Criteria**:
- Mastery heatmap (color-coded by concept)
- Current streak (consecutive days)
- Lessons completed count
- Time spent today/week/month
- Weaknesses highlighted
- Next recommended lesson

#### FR-ANALYTICS-002: Parent Dashboard
**Priority**: High  
**Description**: Parents shall monitor child's learning activity  
**Acceptance Criteria**:
- Child progress overview
- Screen time today/week
- Lessons completed
- Mastery heatmap
- AI interaction log
- Downloadable PDF report

#### FR-ANALYTICS-003: Teacher Dashboard
**Priority**: High  
**Description**: Teachers shall view cohort analytics  
**Acceptance Criteria**:
- Class-wide mastery heatmap
- Individual student progress
- Struggling students identified
- Average time per lesson
- Completion rates
- Intervention recommendations

#### FR-ANALYTICS-004: Admin Dashboard
**Priority**: Medium  
**Description**: Admins shall monitor system health and usage  
**Acceptance Criteria**:
- Total users by role
- Active users (daily/weekly/monthly)
- Feature flag status
- API error rates
- Sandbox execution metrics
- Database query performance

#### FR-ANALYTICS-005: Progress Export
**Priority**: Medium  
**Description**: System shall generate downloadable progress reports  
**Acceptance Criteria**:
- PDF format
- Includes: student info, mastery heatmap, lesson history
- Date range selectable
- Branded with DharmaCodex logo
- Generated within 10 seconds
- Emailed to parent/teacher

### 3.7 User Interface (FR-UI)

#### FR-UI-001: Legendary Mythological Theme
**Priority**: High  
**Description**: UI shall feature mythological-inspired design  
**Acceptance Criteria**:
- Color palette: Gold, Saffron, Purple, Blue
- Gradient backgrounds
- Animated transitions (300-500ms)
- Mythological iconography
- Cinzel font for headings
- Responsive across devices

#### FR-UI-002: Learning Modes
**Priority**: High  
**Description**: System shall support three UI complexity levels  
**Acceptance Criteria**:
- Lite: Minimal UI, essential features only
- Guided: Hints enabled, moderate features
- Full: All features accessible
- Mode selectable in settings
- Mode affects feature flag evaluation
- Visual differences clear

#### FR-UI-003: Deep Focus Mode
**Priority**: Medium  
**Description**: System shall provide distraction-free coding environment  
**Acceptance Criteria**:
- Hides sidebar, notifications, animations
- Full-screen code editor
- Minimal color scheme
- Timer visible
- Exit button always accessible
- Auto-save enabled

#### FR-UI-004: Calm UI Mode
**Priority**: Medium  
**Description**: System shall offer reduced-animation experience  
**Acceptance Criteria**:
- Disables particle effects
- Reduces transition durations (100ms)
- Static backgrounds
- No auto-playing animations
- Maintains functionality
- Toggle in settings

#### FR-UI-005: Accessibility
**Priority**: High  
**Description**: UI shall be accessible to users with disabilities  
**Acceptance Criteria**:
- WCAG 2.1 Level AA compliant
- Keyboard navigation support
- Screen reader compatible
- Color contrast ratio ≥ 4.5:1
- Text scalable to 200%
- Alt text for all images

### 3.8 Governance & Safety (FR-GOVERN)

#### FR-GOVERN-001: Screen Time Control
**Priority**: High  
**Description**: Parents shall set daily screen time limits  
**Acceptance Criteria**:
- Limit configurable (15-180 minutes)
- Warning at 80% of limit
- Session locked at 100%
- Time resets at midnight (user timezone)
- Override option for parent
- Time tracking accurate to 1 minute

#### FR-GOVERN-002: Parent-Child Linking
**Priority**: High  
**Description**: Parents shall link to and monitor child accounts  
**Acceptance Criteria**:
- Parent sends link request via email
- Child accepts link (or auto-accept if <13)
- Parent views all child data
- Parent can modify child settings
- Multiple children per parent supported
- Child can request unlink (if >13)

#### FR-GOVERN-003: Teacher-Student Assignment
**Priority**: High  
**Description**: Teachers shall manage student cohorts  
**Acceptance Criteria**:
- Teacher creates class/cohort
- Students join via class code
- Teacher views all cohort members
- Teacher assigns lessons
- Teacher cannot modify student code
- Student can leave cohort

#### FR-GOVERN-004: Content Safety
**Priority**: Critical  
**Description**: System shall ensure child-safe content and interactions  
**Acceptance Criteria**:
- No user-generated content sharing (Phase 1)
- No chat or messaging features
- All lesson content reviewed
- Profanity filter on text inputs
- Age-appropriate examples
- COPPA compliant

