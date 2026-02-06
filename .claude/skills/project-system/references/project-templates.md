# Project Templates Reference

Starter configurations for different project types. Used by /plan to set up milestones and agent assignments.

---

## Available Templates

| Template | Description | Agents Required |
|----------|-------------|----------------|
| webapp | Standard web application | planner, ux-designer, frontend, tester, security, reviewer |
| saas | SaaS product with billing | planner, ux-designer, frontend, backend, tester, security, reviewer |
| marketing-site | Marketing/landing pages | planner, ux-designer, frontend, content-writer, reviewer |
| api | API/backend service | planner, backend, tester, security, technical-writer, reviewer |
| mobile-app | React Native / mobile | planner, ux-designer, frontend, backend, tester, security, reviewer |
| desktop-app | Electron / desktop | planner, ux-designer, frontend, backend, tester, security, reviewer |

---

## Standard Web App Phases

```
Phase 1: Foundation
- Project setup (framework, styling, components)
- Database setup
- Auth flow
- Deploy pipeline

Phase 2: Design
- Wireframes
- Component specs
- Design tokens

Phase 3: Core Feature
- The ONE thing the app must do
- Main user flow

Phase 4: Supporting Features
- Secondary features
- Settings, preferences

Phase 5: Polish
- Empty states
- Loading states
- Error handling
- Mobile responsive

Phase 6: Launch
- Testing (80%+ coverage)
- Security review
- SEO
- Analytics
- Documentation
```

---

## SaaS Product Phases

```
Phase 1: Foundation
- Tech stack decision
- Database schema
- Auth strategy
- Billing model

Phase 2: Design
- Marketing pages
- App wireframes
- Component system
- User flows

Phase 3: Auth and Core
- Authentication
- User management
- Core data models
- Basic dashboard

Phase 4: Features
- Main product features
- Settings pages
- Notifications

Phase 5: Billing
- Payment integration
- Subscription management
- Usage tracking
- Invoicing

Phase 6: Quality
- Tests (85%+ coverage)
- Security review
- Compliance check
- Documentation

Phase 7: Launch
- Deployment
- Monitoring
- Landing page
- SEO
```

### SaaS Considerations
- User onboarding flow
- Subscription tiers
- Team/organization support
- Data export
- GDPR compliance
- Audit logging

---

## Marketing Site Phases

```
Phase 1: Planning
- Content strategy
- Site structure
- SEO planning

Phase 2: Design
- Page wireframes
- Visual design
- Responsive layouts

Phase 3: Content
- Copy written
- Images sourced
- SEO metadata

Phase 4: Build
- All pages implemented
- Animations/interactions
- Forms integrated

Phase 5: Launch
- Performance optimized (LCP < 1.5s)
- SEO verified
- Cross-browser tested
```

### Marketing Focus
- Conversion optimization
- Page speed (stricter targets)
- SEO
- Visual impact
- Mobile experience

---

## API Service Phases

```
Phase 1: Design
- API design (endpoints, response format)
- Database schema
- Auth strategy
- Documentation setup (OpenAPI)

Phase 2: Core API
- Database setup
- Authentication
- Core CRUD endpoints
- Error handling

Phase 3: Features
- All endpoints
- Business logic
- Validation
- Rate limiting

Phase 4: Quality
- Unit tests (90%+ coverage)
- Integration tests
- Load testing
- Security testing

Phase 5: Launch
- Security review
- API documentation
- Deployment
- Monitoring
```

### API Considerations
- API versioning strategy
- Rate limiting rules
- Authentication method
- Documentation (OpenAPI)
- Error response format
- Pagination approach
- Webhooks
- SDK generation

---

## Mobile App Phases

```
Phase 1: Foundation
- Tech stack (React Native, Expo, etc.)
- Navigation structure
- Auth flow
- API integration setup

Phase 2: Design
- Screen flows
- Component library
- Platform-specific patterns (iOS vs Android)
- Gesture interactions

Phase 3: Core Screens
- Authentication screens
- Main tab/stack navigation
- Core feature screens
- Data sync

Phase 4: Features
- All screens implemented
- Push notifications
- Offline support
- Platform permissions

Phase 5: Polish
- Animations and transitions
- Loading states
- Error handling
- Platform-specific adjustments

Phase 6: Launch
- Testing on devices
- App store assets (screenshots, descriptions)
- App store submission
- Beta testing (TestFlight / Play Console)
```

### Mobile Considerations
- Platform guidelines (iOS HIG, Material Design)
- App store requirements
- Deep linking
- Push notifications
- Offline-first data
- Device testing matrix

---

## Desktop App Phases

```
Phase 1: Foundation
- Tech stack (Electron, Tauri, etc.)
- Main/renderer process architecture
- Auto-update setup
- Packaging pipeline

Phase 2: Design
- Window layout
- Menu structure
- System tray integration
- Keyboard shortcuts

Phase 3: Core Features
- Main functionality
- File system access
- IPC communication
- Native integrations

Phase 4: Features
- All features implemented
- Cross-platform testing (Mac/Win/Linux)
- Auto-update flow
- System integration

Phase 5: Polish
- Performance optimization
- Memory management
- Error handling
- Native feel (platform conventions)

Phase 6: Launch
- Code signing (Mac/Win)
- Installer/DMG generation
- Distribution (direct / app store)
- Auto-update server
```

### Desktop Considerations
- Code signing certificates
- Auto-update strategy
- Cross-platform UI differences
- Native menu integration
- File association handling
- Security (process isolation)

---

## Quality Defaults by Template

| Template | Test Coverage | A11y | LCP Target |
|----------|-------------|------|-----------|
| webapp | 80% | AA | 2.5s |
| saas | 85% | AA | 2.5s |
| marketing-site | 50% | AA | 1.5s |
| api | 90% | N/A | N/A |
| mobile-app | 80% | AA | N/A |
| desktop-app | 80% | AA | N/A |
