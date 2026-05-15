# IPT - Angular 21 Auth Boilerplate

A comprehensive full-stack authentication system (Angular 21 + Node.js/MySQL) featuring user management and role-based access control.

## 🚀 Live Demo & Documentation

- **Live Frontend:** [https://your-frontend-name.onrender.com](https://your-frontend-name.onrender.com)
- **Live Backend API:** [https://your-backend-name.onrender.com](https://your-backend-name.onrender.com)
- **Swagger API Docs:** [https://your-backend-name.onrender.com/api-docs](https://your-backend-name.onrender.com/api-docs)

---

## Project Architecture

This project follows the traditional **NgModule-based** architecture. It uses:
- **NgModules** for organization and lazy loading.
- **HTTP Interceptors** for JWT handling and error management.
- **Fake Backend** for local development (Stage A) and **Live API Integration** for production (Stage B).

---

## Getting Started

### 1. Installation
```bash
npm install
```

### 2. Local Testing (Stage A - Fake Backend)
To test the UI logic without a real server:
- Ensure `fakeBackendProvider` is enabled in `src/app/app.module.ts`.
- Run `npm start`.

### 3. Integration Testing (Stage B - Live Backend)
To connect to your Node.js/MySQL API:
- Disable `fakeBackendProvider` in `src/app/app.module.ts`.
- Update `src/environments/environment.ts` with your backend URL.
- Run `npm start`.

### 4. Build for Production
```bash
npm run build
```
The output directory will be `dist/ipt-2026-frontend`.

---

## Deployment Configuration (Render)

### Frontend (Static Site)
- **Build Command:** `npm ci && npm run build`
- **Publish Directory:** `dist/ipt-2026-frontend`
- **SPA Rewrite Rule:** 
  - Source: `/*` 
  - Destination: `/index.html` 
  - Action: `Rewrite`

---

## Recent Fixes & Configuration Changes

The project was recently updated to resolve an issue where modules and components were not being recognized. Below are the details of the changes made.

### 1. Bootstrap Alignment (Standalone -> NgModule)

The application was mistakenly configured to bootstrap as a **Standalone** application, which caused it to ignore the `AppModule` and all its declarations.

**Before (Incorrect Standalone Bootstrap in `main.ts`):**
```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { appConfig } from './app/app.config';
import { App } from './app/app';

bootstrapApplication(App, appConfig)
  .catch((err) => console.error(err));
```

**After (Correct NgModule Bootstrap in `main.ts`):**
```typescript
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule } from './app/app.module';

platformBrowserDynamic().bootstrapModule(AppModule)
  .catch((err) => console.error(err));
```

### 2. Component Structure Correction

The root component was switched from the standalone `App` component to the proper `AppComponent` which includes the application's layout, navigation, and alert system.

- **Removed Standalone Files:** `src/app/app.ts`, `src/app/app.config.ts`, `src/app/app.routes.ts`, `src/app/app.html`, `src/app/app.css`.
- **Restored Root Component:** `src/app/app.component.ts` (and its associated template/styles).

### 3. Profile Module Fix

A build error was resolved in the Profile module where the component template filename did not match the class configuration.

- **Change:** Renamed `src/app/profile/detail.component.html` to `src/app/profile/details.component.html`.
- **Reason:** The `DetailsComponent` was looking for `details.component.html`, causing a compilation failure.

### 4. Persistent Alerts for Verification

The simulated "email" alerts for verification and password reset were disappearing too quickly for users to click the links.

- **Change:** Modified `src/app/_helpers/fake-backend.ts` to set `{ autoClose: false }` for these specific alerts.
- **Result:** Alerts containing verification and reset links will now stay on screen until manually closed or the link is clicked.

### 5. UI Hang Fix (Change Detection)

The application was experiencing an issue where the "Validating token..." state in the Reset Password and Verify Email components would hang indefinitely when using the Fake Backend.

- **Issue:** Asynchronous responses from the Fake Backend (which includes a 500ms delay) were not automatically triggering Angular's change detection in certain scenarios.
- **Change:** 
    - Imported `ChangeDetectorRef` into `ResetPasswordComponent` and `VerifyEmailComponent`.
    - Explicitly called `this.cdr.detectChanges()` after token validation and form submission state changes.
- **Result:** The UI now updates immediately as soon as the backend response is received, resolving the hanging state.

### 6. Final Project Deployment Prep

The project configuration was updated to meet the requirements for the final deployment to Render:

- **Environment Config:** Updated `src/environments/environment.prod.ts` with `production: true` and a placeholder `apiUrl` for the live backend.
- **Fake Backend Toggle:** Modified `src/app/app.module.ts` to conditionally load the `fakeBackendProvider` only when `environment.production` is false. This ensures the live build connects to the real API automatically.
- **Build Output Path:** Explicitly set the `outputPath` in `angular.json` to `dist/ipt-2026-frontend` to match the expected publish directory on Render.

---

## Features

- **Authentication:** Login, Registration, Email Verification.
- **Password Management:** Forgot Password, Reset Password.
- **User Profile:** View and update account details.
- **Admin Panel:** User management (List, Create, Edit, Delete).
- **Security:**
  - `AuthGuard`: Protects routes based on authentication status and roles.
  - `JwtInterceptor`: Automatically attaches JWT tokens to requests.
  - `ErrorInterceptor`: Handles 401/403 errors and redirects to login.

## Testing

Run unit tests via Karma:
```bash
npm test
```
