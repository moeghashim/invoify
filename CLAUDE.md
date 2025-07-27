# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Core Commands
- `npm run dev` - Start development server at http://localhost:3000
- `npm run build` - Build for production
- `npm run start` - Start production server
- `npm run lint` - Run ESLint
- `npm run analyze` - Analyze bundle size with webpack-bundle-analyzer

### Environment Setup
- Copy `.env.local` file for email functionality (NODEMAILER_EMAIL, NODEMAILER_PW)
- Run `npm install` to install dependencies including Puppeteer

## Architecture Overview

### Tech Stack
- **Framework**: Next.js 13 with App Router and TypeScript
- **Styling**: Tailwind CSS with Shadcn UI components
- **Forms**: React Hook Form with Zod validation 
- **PDF Generation**: Puppeteer with React server-side rendering
- **Internationalization**: next-intl with 14+ language support
- **State Management**: React Context providers with form state

### Application Structure

#### Core Contexts (contexts/)
The app uses multiple React contexts wrapped in `Providers.tsx`:
- `InvoiceContext` - Main invoice state management
- `ChargesContext` - Tax, discount, shipping calculations
- `ThemeProvider` - Dark/light theme switching
- `TranslationProvider` - i18n language switching
- `FormProvider` - React Hook Form state with Zod validation

#### Component Architecture (app/components/)
- **invoice/** - Main invoice form and preview components
  - `InvoiceForm.tsx` - Main form component
  - `InvoiceMain.tsx` - Layout wrapper
  - **form/sections/** - Individual form sections (BillFromSection, BillToSection, etc.)
  - **actions/** - PDF generation, live preview, and viewer components
- **modals/** - All modal dialogs (export, email, signature, alerts)
- **templates/** - Invoice PDF templates and email templates
- **reusables/** - Shared UI components and form fields

#### API Routes (app/api/invoice/)
- `generate/route.ts` - PDF generation endpoint
- `export/route.ts` - Export in various formats (JSON, CSV, XML, XLSX)  
- `send/route.ts` - Email PDF functionality

#### Services (services/invoice/)
- **server/** - Server-side PDF generation and email services
- **client/** - Client-side export utilities

### Key Features Architecture

#### PDF Generation Flow
1. Form data validated with Zod schemas (`lib/schemas.ts`)
2. Invoice template selected based on `pdfTemplate` field
3. React component rendered to HTML via `ReactDOMServer.renderToStaticMarkup`
4. Puppeteer generates PDF from HTML with Tailwind styles
5. Different Puppeteer configs for development vs production (uses @sparticuz/chromium in production)

#### Form Validation
- Central Zod schemas in `lib/schemas.ts` with reusable field validators
- TypeScript types auto-generated from schemas in `types.ts`
- Form state managed by React Hook Form with zodResolver

#### Internationalization
- Locale files in `i18n/locales/` (JSON format)
- Dynamic locale routing via `[locale]` folder structure  
- Middleware handles locale detection and routing
- Component translations accessed via next-intl hooks

#### Template System
- Multiple invoice PDF templates in `app/components/templates/invoice-pdf/`
- Template selection via `pdfTemplate` field in form
- Templates use shared layout and styling patterns

### Data Flow
1. Form inputs → React Hook Form state → Zod validation
2. Form state → Context providers → Components
3. PDF generation: Form data → API route → Puppeteer service → PDF blob
4. Export: Form data → Export service → Various formats (JSON/CSV/XML/XLSX)
5. Email: PDF generation + Nodemailer → Email with attachment

### Testing & Development
- No specific test framework configured - check with user if tests are needed
- Development uses standard Next.js dev server with hot reload
- Bundle analysis available via `npm run analyze`
- TypeScript strict mode enabled