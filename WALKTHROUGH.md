# Prototype Walkthrough: Celink Borrower Mobile App

## How to View

Open `index.html` in any browser. You'll see two device frames side by side — iPhone 15 Pro (left) and Pixel 8 (right) — showing the same app content. This demonstrates the cross-platform React Native experience Celink requested in their RFI.

On smaller screens, the devices stack vertically.

Navigate between screens using the bottom tab bar or in-app links. Every link works.

---

## Why This Design

### What Celink Asked For (RFI)

Celink's RFI requests a cross-platform mobile app (iOS + Android) that gives reverse mortgage borrowers **parity with the existing Borrower Portal** (reversedepartment.com), plus mobile-native capabilities like biometrics, camera upload, and push notifications.

Key constraints from the RFI:
- **Elderly user base** — accessibility and simplicity are critical
- **37 parity features** from the portal must be replicated
- **14 add-on features** including biometrics, push notifications, and white-labeling
- **OWASP MASVS compliance** for financial app security
- **WCAG 2.1 AA** accessibility baseline

### What We're Showing

This prototype demonstrates how 37 portal features + mobile-native additions translate into a clean, accessible mobile experience. Every screen maps directly to RFI scope items.

---

## Screen-by-Screen Walkthrough

### 1. Login (`index.html`)

**RFI scope:** Parity — Login landing page, MFA, Copyright display *(PDF Appendix 1, Parity Features #1-#4)* | Add-on — Biometrics *(Excel Add-ons #1)* | Add-on — Social auth *(Excel Add-ons #2)*

**Design decisions:**
- Celink brand front and center (logo with orange "e" accent) — *(PDF 4.4: "client logo + refinance phone number" is a parity requirement)*
- Large input fields with 16px font — prevents iOS auto-zoom, critical for elderly borrower demographic *(PDF 2.1: "reverse mortgage borrowers" — elderly users confirmed)*
- Orange pill-shaped buttons matching Celink's existing web CTA style (celink.com)
- **Face ID / biometric option** prominently displayed — *(Excel Add-ons #1: "Biometrics Face ID / Touch ID")* . Designed as parallel to password per **Q10 answer: "biometric authentication should exist in parallel with password-based one, giving those users who register for it an extra option"**
- **Auth0 Universal Login** powers this screen — *(Q08: "We are on Enterprise plan")* confirmed native mobile SDK support. Verified: `react-native-auth0` SDK v5.x supports iOS + Android natively with biometric Credentials Manager
- "Register" link visible but registration is NOT in parity scope — *(Q14: "In-app registration is not part of parity features")* . Link is there for completeness; flow deferred.
- "Forgot password" maps to Auth0 password recovery — *(PDF Appendix 1, Parity #3-#4: "Password reset/recovery, Password change")*
- **No guest access** — *(PDF 4.2: "The mobile app will not allow anonymous or 'guest' access" + Assumption A03)*

### 2. Dashboard (`dashboard.html`)

**RFI scope:** Parity — My-loan page, Loan details, Online tools cards, Transaction history *(PDF Appendix 1, Parity #8-#12)* | Parity — Send usage data to Segment *(Parity #37)* | Add-on — Push notifications *(PDF 4.4, Excel Add-ons #4)*

**Design decisions:**
- **Dark loan summary card** at the top — loan summary (name, co-borrower, account#, loan type, address) is a parity requirement *(PDF Appendix 1, Parity #10: "My-loan page")* . Available LOC is the #1 thing HECM borrowers check.
- Greeting with borrower name — personalization from Auth0 user profile. *(Q08: Auth0 Enterprise provides user metadata)*
- Key metrics at a glance: loan type, rate, account tenure — maps to *(Parity #11: "Loan details — balances, rate")*
- **Quick Actions grid** — maps to the 4 most frequent portal actions: LOC Draw *(Parity #15: "LOC draw request submission")*, Payoff Quote *(Parity #16: "Payoff quote request submission")*, Upload *(Parity #14: "Upload documents — camera capture + file picker")*, Statements *(Parity #9: "View statements up to 2 years")*
- **Recent Activity** — *(Parity #12: "Transaction history")* . Color-coded amounts because financial data must be unambiguous for elderly users.
- **Info banner for occupancy cert** — *(Parity #17: "Annual occupancy certification + eSignature")* . This appears on dashboard because missing it can trigger loan default — high-stakes for reverse mortgage borrowers.
- **Notification bell with badge** — *(PDF 4.4: "Push notifications represent a net-new capability")* . Bell links to notification center. Badge count driven by unread push events. *(Q06: "Systems do not emit triggering events" — all 11 events instrumented from scratch)*
- **Segment tracking fires on every screen** — *(Parity #37 + Q23: "Segment tracking for borrower portal exists. Mobile will likely require different setup")* . New mobile tracking plan.

### 3. My Loan (`loan.html`)

**RFI scope:** Parity — Loan details (balances, rate), Transaction history *(PDF Appendix 1, Parity #10-#12)* | Parity — Profile/account settings *(Parity #13)*

**Design decisions:**
- **Tabbed interface** (Details / Transactions / History) keeps a dense information set organized without overwhelming the user — *(PDF 2.1: elderly borrower demographic requires reduced cognitive load)*
- Loan Overview card shows all key loan data in a scannable format — borrower, co-borrower, property address, origination date, servicer — *(Parity #10: "My-loan page")*
- **Balances card** is the financial heart of the app — *(Parity #11: "Loan details — balances, rate")*:
  - Principal Limit, Current Balance, and Available LOC with a visual progress bar showing utilization
  - Interest rate clearly labeled as variable with the index formula
  - MIP rate displayed separately (reverse mortgage-specific — borrowers often ask about this)
- Transaction list with 5 recent items — *(Parity #12: "Transaction history")*. Amounts are color-coded and right-aligned for quick scanning.
- **API response times of 1.4s avg / 2.5s p90** *(Q22)* mean the real app will need skeleton loading states here — this screen is data-heavy

### 4. Documents (`documents.html`)

**RFI scope:** Parity — View statements up to 2 years *(PDF Appendix 1, Parity #9)* | Parity — Download statements as PDF *(Parity #18)* | Parity — Download forms *(Parity #19)* | Parity — Upload documents with camera capture *(Parity #14)* | Parity — Annual privacy notice *(Parity #20)*

**Design decisions:**
- **Three tabs** map to the three document workflows: viewing statements, downloading forms, and uploading documents
- Statements grouped by year with clear dates and PDF indicators — *(Parity #9: "View statements up to 2 years")*
- Each statement row has a download icon — one tap to get the PDF — *(Parity #18: "Download statements")*
- **Annual Privacy Notice** highlighted in an info banner — *(Parity #20: regulatory requirement for reverse mortgages)*
- **Upload area** at the bottom with camera + file picker prompt — *(Parity #14: "Upload documents — camera capture + file picker")*. The RFI specifies "camera capture" for uploads — this design supports both camera and file selection
- Upload is "straight upload" per Celink's answer — *(Q16: "In scope is straight upload. Out of scope, but considered for the future is AI processing, e.g. to confirm the correct form was used")*. We propose AI document validation (Textract + Claude via Bedrock) as a Phase 2 add-on in our ROM.

### 5. Actions (`actions.html`)

**RFI scope:** Parity — LOC draw request submission *(PDF Appendix 1, Parity #15)* | Parity — Payoff quote request submission *(Parity #16)* | Parity — Annual occupancy certification + eSignature *(Parity #17)* | Parity — Switch to/from paperless *(Parity #21)*

**Design decisions:**
- **Warning banner** for the occupancy certification deadline — this is the most time-critical action and appears at the top. Missing occupancy certification can trigger loan default — high-stakes for reverse mortgage borrowers
- Three action cards with distinct color-coded icons:
  - **LOC Draw** (orange) — *(Parity #15: "LOC draw request submission")*. Shows current available balance inline so borrowers don't have to navigate away to check
  - **Payoff Quote** (green) — *(Parity #16: "Payoff quote request submission")*. Straightforward request flow
  - **Occupancy Certification** (blue, with "Due Soon" badge) — *(Parity #17: "Annual occupancy certification + eSignature")*. Celink uses a custom-built eSignature solution *(Q07: "Custom-built solution is used")*, so this will be implemented as a WebView wrapper in the actual app (~50-80 hrs per ROM)
- **Paperless toggle** at the bottom — *(Parity #21: "Switch to/from paperless")*. Simple on/off with confirmation text.
- **LOC/Payoff module modifications** are Celink-internal — *(Q04: "No scope estimate is needed... making changes to a corresponding module to account for the presence of mobile app" + Q12: "Internal teams will make those changes")*

### 6. Notifications (`notifications.html`)

**RFI scope:** Add-on — Push notifications, 11 event types *(PDF 4.4: "Push notifications represent a net-new capability", Excel Add-ons #4)* | Add-on — Opt-in/out management *(Excel Add-ons #5)*

**Design decisions:**
- This is a **net-new capability** — the portal doesn't have push notifications today. *(Q06: "Systems do not emit triggering events")* — all 11 events must be instrumented from scratch using EventBridge → SQS → Lambda → SNS → FCM/APNs
- Back arrow navigation (this is a sub-page, not a main tab)
- **Unread notifications** are visually distinct with orange dot and light orange background
- Read notifications fade to neutral styling
- "Mark all read" link for quick management
- Each notification shows: title, description, and relative timestamp
- The 7 sample notifications demonstrate several of the 11 RFI event types *(Excel Add-ons #4 event list)*:
  - Occupancy certification reminder
  - LOC draw processed
  - Statement available
  - Interest rate update
  - Tax document ready
  - Login detection (security)
  - Payment confirmation
- **Push infrastructure** leverages Celink's existing SNS/SQS/EventBridge — *(Q05: "Celink uses, for various purposes, SNS, SQS and Eventbridge")* — but triggering logic is new

### 7. Profile & Settings (`profile.html`)

**RFI scope:** Parity — Profile/account settings *(PDF Appendix 1, Parity #13)* | Parity — Security settings, password change *(Parity #3-#4)* | Parity — Notification preferences *(Parity #22)* | Parity — Paperless enrollment *(Parity #21)* | Parity — FAQs, forms, contact info *(Parity #23-#26)* | Parity — Privacy policy, terms, accessibility *(Parity #27-#30)* | Add-on — Biometrics toggle *(Excel Add-ons #1)*

**Design decisions:**
- Avatar with initials — personal touch without requiring a photo upload
- **Four grouped sections** organize a lot of content cleanly:
  - **Account:** Personal info *(Parity #13)*, security *(Parity #3-#4)*, notification preferences *(Parity #22)*, paperless status *(Parity #21)*
  - **Support:** FAQs *(Parity #23)*, common forms *(Parity #24)*, contact *(Parity #25)*, My Home Toolkit *(Parity #26)* — all RFI parity items
  - **Legal:** Privacy policy *(Parity #27)*, terms *(Parity #28)*, licenses *(Parity #29)*, accessibility statement *(Parity #30)* — all RFI reference pages
  - **App:** Biometric toggle and version number
- **Biometric Login toggle** — *(Excel Add-ons #1: "Biometrics Face ID / Touch ID" + Q10: "biometric authentication should exist in parallel with password-based one")*. Active by default to show the enrolled state.
- **Sign Out** button clearly visible but separated from other actions to prevent accidental logout
- Celink branding in footer — *(PDF 4.4: "client logo + refinance phone number" is a parity requirement)*
- **White-labeling ready** — all colors in CSS `:root` variables, logo is config-driven — *(Q15: "Expect white-labeling to be limited to only 3 clients")*

---

## Design Principles

| Principle | Why | How |
|-----------|-----|-----|
| **Large touch targets** | Elderly users, potential motor difficulties | Minimum 44px tap areas, pill buttons, generous padding |
| **High contrast** | Aging vision, WCAG 2.1 AA compliance | Dark text on white, orange on dark for CTAs |
| **Minimal navigation depth** | Reduce cognitive load | Everything within 2 taps from dashboard |
| **Familiar patterns** | Borrowers already use the web portal | Similar information architecture to reversedepartment.com |
| **Clear financial data** | Money is sensitive — no ambiguity | Color-coded amounts, explicit labels, formatted currencies |
| **Brand consistency** | Celink's existing identity | Orange accent, dark text, clean white backgrounds |

---

## What's Not in This Prototype (but in Scope)

- **White-labeling for 3 clients** — the CSS variables (`:root`) make theming trivial. Logo, colors, and content are config-driven.
- **Registration flow** — excluded from parity per Celink's answer (Q14), but can be added as an add-on screen
- **AI Document Processing** — proposed as Phase 2 add-on (Textract + Claude via Bedrock)
- **eSignature flow** — the occupancy cert action links to it, but the actual WebView wrapper for Celink's custom eSign system is a development task
- **Offline mode** — will be recommended in the Excel response
- **Deep linking from push notifications** — implemented in code, not visible in static prototype

---

## Technical Notes

- **Pure HTML + CSS** — no JavaScript frameworks, no build tools, no dependencies
- **Inline SVG icons** — zero external icon libraries, full control over styling
- **CSS custom properties** — all brand colors in `:root`, ready for white-label theming
- **Responsive** — side-by-side devices on desktop, stacked on mobile
- **Realistic data** — fictional borrower (Margaret Richardson) with plausible HECM loan details, not "Lorem ipsum" or "John Doe"
- **"Conceptual Only" disclaimer banner** — every screen displays a Simpat-blue banner at top clarifying this is an early-stage RFI prototype, not a final design
- **Watermark** — background watermark reads "CONCEPTUAL ONLY — RFI PROTOTYPE" at 3% opacity to reinforce the prototype nature on every page
- **Flex-based device frames** — device screens use CSS flex column layout ensuring bottom navigation is always visible regardless of content height

---

*Prototype by Simpat Tech | 2026*
