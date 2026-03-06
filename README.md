# Celink Borrower Mobile App — Interactive Prototype

Cross-platform mobile app prototype for reverse mortgage borrowers. Displays side-by-side iPhone 15 Pro and Pixel 8 device frames showing the same React Native app experience.

## View Live

**[Open Prototype](https://arturomv199.github.io/EMPA-Celink-Prototype/)**

## Screens

| Screen | File | Description |
|--------|------|-------------|
| Login | `index.html` | Auth0 login with biometrics, MFA |
| Dashboard | `dashboard.html` | Loan summary, quick actions, recent activity |
| My Loan | `loan.html` | Balances, rates, transaction history |
| Documents | `documents.html` | Statements, forms, upload with camera |
| Actions | `actions.html` | LOC draw, payoff quote, occupancy cert |
| Notifications | `notifications.html` | Push notification center (11 event types) |
| Profile | `profile.html` | Settings, preferences, support, legal |

## Design Details

See [WALKTHROUGH.md](WALKTHROUGH.md) for screen-by-screen design decisions traced back to RFI requirements.

## Technical

- Pure HTML + CSS (no JS frameworks, no build tools)
- Inline SVG icons (zero external dependencies)
- CSS custom properties for white-label theming
- Responsive: side-by-side on desktop, stacked on mobile

---

*Prototype by Simpat Tech | 2026*
