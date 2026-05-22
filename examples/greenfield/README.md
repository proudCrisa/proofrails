# Greenfield Service Example

This example walks through starting a new service from scratch using ProofRails.

## Scenario

Build a `user-notification-service` that sends email and push notifications when specific events occur. The service will be a REST API consumed by other internal services.

## Step 1: Invoke ProofRails

```
/proofrails in /path/to/user-notification-service start a user notification service.
No existing code. Tech stack preference: Go, PostgreSQL, Redis.
Success criteria: proposal, design, tasks, first vertical slice plan.
Do not write implementation yet.
```

## Step 2: Structured interview

ProofRails drives the intake interview:

**Product:**
- Users: other internal services (order-service, payment-service, auth-service)
- Problem: no centralized notification delivery
- Target workflow: service calls `POST /notifications` with user ID, channel, template, and variables; notification is delivered and status is tracked
- Acceptance: notification delivered within 30 seconds, status queryable via `GET /notifications/{id}`

**Design/UX:**
- API-first: REST with JSON
- No UI — consumed by backend services
- API contract: OpenAPI 3.0 spec

**Engineering:**
- Stack: Go 1.22, chi router, pgx, Redis for rate limiting
- Deployment: Docker, docker-compose for local dev
- Data model: notifications table, templates table, delivery_attempts table
- Integrations: SendGrid for email, Firebase for push
- Constraints: idempotency key to prevent duplicate sends

**Operations:**
- Observability: structured logging, Prometheus metrics
- Rollout: canary deploy, feature flag for new templates
- Rollback: revert deploy, no data loss (notifications are idempotent)
- Security: service-to-service auth via shared secret
- Ownership: platform team

## Step 3: Spec draft

ProofRails creates `.proofrails/changes/new-notification-service/`:

**proposal.md:**
```markdown
# Proposal: new-notification-service

## Why
No centralized notification delivery. Each service builds its own email/push integration.
This leads to duplicated code, inconsistent delivery, and no unified tracking.

## What changes
- New `user-notification-service` repository
- REST API for sending and querying notifications
- Email provider integration (SendGrid)
- Push provider integration (Firebase)
- Idempotency support
- Delivery status tracking

## Non-goals
- User preference management (separate service)
- Template management UI (future)
- SMS/in-app channels (future)

## Impact
- order-service, payment-service, auth-service will migrate to this service
- Existing per-service notification code will be removed after migration

## Risks
- New service adds latency to notification delivery
- Provider outages affect all services simultaneously
- Mitigation: async processing, provider fallback, circuit breakers

## Rollback
- Callers keep existing notification code during migration period
- Cut over one caller at a time with feature flags
```

**design.md, tasks.md, specs/notification-api/spec.md** follow the templates.

## Step 4: Challenge gate

ProofRails challenges:
1. **Business:** Is 30-second delivery too generous? -> Confirmed: 30s is max, typical < 5s.
2. **Risk:** What if both SendGrid and Firebase are down? -> Queue and retry; circuit breaker prevents cascade.
3. **Implementation:** Why Go over TypeScript? -> Team expertise, lower operational overhead.
4. **History:** Any existing notification infrastructure? -> No, greenfield.

## Step 5: First vertical slice

ProofRails identifies the smallest complete slice:

**"Send an email notification and query its status."**

This slice includes:
- Project skeleton (Go module, directories, Makefile)
- Database migration (notifications table)
- `POST /api/v1/notifications` endpoint
- `GET /api/v1/notifications/{id}` endpoint
- SendGrid integration (mock in tests)
- Health check endpoint
- Docker setup

Everything else (push notifications, templates, rate limiting, metrics) is deferred to slice 2+.

## Step 6: Approval

User reviews and approves the plan. Implementation proceeds slice by slice.

## Step 7: Implementation (slice 1)

1. Project skeleton created.
2. Database migration written.
3. Tests written for both endpoints.
4. Implementation makes tests pass.
5. Build, lint, and test all green.
6. Evidence recorded.
7. Slice 1 archived.

## Step 8: Subsequent slices

Each subsequent slice is a new change with its own change-id:
- `add-push-notifications`
- `add-notification-templates`
- `add-rate-limiting`
- `add-delivery-metrics`

## Key takeaways

1. The structured interview caught a missing idempotency requirement that would have caused duplicate notifications.
2. The first vertical slice was genuinely complete — a working service, not a skeleton.
3. The challenge gate identified the single-provider risk and prompted circuit breaker planning before any code was written.
4. Each subsequent feature is a separate archived change, keeping the evidence chain clean.
