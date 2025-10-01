Code IT
"Fostering Excellence, Tracking Growth, Building Community."

Overview
Code IT is a centralized web platform designed exclusively for the students of the KIET IT department. It aims to consolidate, track, and gamify the coding journey of every student by integrating with major competitive programming platforms. Built on a modern, serverless technology stack using Vercel and Supabase, it provides a unified leaderboard, personal growth analytics, a real-time contest feed, and a curated resource hub to create a vibrant and competitive coding culture.

Problem Statement
For students at KIET IT, coding progress is fragmented across various platforms like Codeforces and LeetCode. There's no unified way to measure or compare skills within their own peer group, leading to a lack of motivation and healthy competition. Students often miss out on important coding contests and struggle to find reliable, structured learning resources. This platform bridges that gap.

Solution
A unified web application that serves as the central hub for all coding-related activities. It automatically fetches and consolidates student data from Codeforces and LeetCode into a single, dynamic leaderboard. It provides personalized dashboards with visual growth trackers, an automated feed of upcoming contests, and a curated library of learning resources, making skill development streamlined and engaging.

Architecture Overview
graph TB
    subgraph "Platform Ecosystem"
        subgraph "Frontend & Edge (Vercel)"
            A[Next.js Frontend]
            B[Vercel Edge Network / CDN]
        end
        
        subgraph "Backend Services (Vercel & Supabase)"
            C[Next.js API Routes]
            D[Vercel Cron Jobs<br/>For Data Sync]
            E[Supabase Auth<br/>User Management]
            F[Supabase Database<br/>PostgreSQL]
        end
        
        subgraph "External Data Sources"
            G[Codeforces API]
            H[LeetCode API]
            I[Kontests API]
        end
    end
    
    B --> A
    A --> C
    D --> C
    C --> E
    C --> F
    C --> G
    C --> H
    C --> I

Data & Scoring System Architecture
graph LR
    subgraph "Data Processing Pipeline"
        A[API Scraper Agent<br/>Vercel Serverless Function]
        B[Data Store<br/>Supabase PostgreSQL]
        C[Scoring Engine<br/>Serverless Function]
        D[API Layer<br/>Next.js API Routes]
    end

    subgraph "External APIs"
        E[(Codeforces, LeetCode, Kontests)]
    end
    
    A --> E
    E --> A
    A --> B
    C --> B
    B --> C
    D --> C
    C --> D

User Journey Flow
flowchart TD
    A([User Visits Platform]) --> B{Authenticated?}
    B -->|No| C[Register/Login]
    C --> D[Link Codeforces &<br/>LeetCode Profiles]
    D --> E[Dashboard]
    B -->|Yes| E
    
    E --> F[View Leaderboard]
    E --> G[View Personal<br/>Growth Tracker]
    E --> H[Find Upcoming<br/>Contests]
    E --> I[Access Resources]
    
    subgraph "Periodic Sync (Automated)"
      J[Cron Job Triggers] --> K[Fetch Latest Stats<br/>from APIs]
      K --> L[Update Database<br/>& Recalculate Scores]
    end

    L --> F
    L --> G

Request Flow Architecture
sequenceDiagram
    participant UB as User Browser
    participant CDN as Vercel CDN
    participant App as Next.js App
    participant Auth as Supabase Auth
    participant Scraper as Data Scraper
    participant ExtAPI as External APIs
    participant DB as Supabase DB

    UB->>CDN: View Leaderboard Request
    CDN->>App: Forward Request
    App->>Auth: Validate User Token
    Auth-->>App: Token Valid

    alt Cron Job Trigger or Data is Stale
        App->>Scraper: Start Data Sync
        Scraper->>ExtAPI: Fetch Ratings
        ExtAPI-->>Scraper: Return Ratings Data
        Scraper->>DB: Store/Update User Stats
        DB-->>Scraper: Stored Successfully
        Scraper-->>App: Sync Complete
    end

    App->>DB: Fetch Leaderboard Data
    DB-->>App: Return Data
    App-->>UB: Return Rendered Page/Data

Technology Stack
Data Sources & APIs
Codeforces API: For contest ratings and user data.

LeetCode GraphQL API: For user stats and problems solved.

Kontests API: For an aggregated list of upcoming contests.

Frontend & Backend
Next.js 14: Full-stack React framework.

TypeScript: Type safety and development experience.

Tailwind CSS: Utility-first CSS framework for styling.

React Query: Server state management and caching.

Cloud Infrastructure
Vercel: Hosting, Serverless Functions, Cron Jobs, and CDN.

Supabase: PostgreSQL Database, Authentication, and Storage.

DevOps
GitHub Actions: For continuous integration and deployment (CI/CD).

ESLint & Prettier: For code quality and consistency.

Key Features
Multi-Platform Integration: Seamlessly connects with user profiles from Codeforces and LeetCode.

Dynamic Leaderboard: Real-time ranking of students, filterable by graduation year.

Personal Growth Dashboard: Visual charts tracking rating history and problem-solving trends.

Automated Contest Feed: Live, auto-updating list of upcoming contests from all major platforms.

Curated Resource Hub: An admin-managed section with handpicked links to the best learning materials.

Public User Profiles: Showcase individual stats, achievements, and linked coding profiles.

Getting Started
Prerequisites
Node.js 18+ and npm/yarn/pnpm

Supabase account & project setup

GitHub account for Vercel deployment

Installation
Clone the repository:

git clone [https://github.com/your-username/code-it.git](https://github.com/your-username/code-it.git)
cd code-it

Install dependencies:

npm install
# or
yarn install

Set up environment variables by copying the example file:

cp .env.example .env.local

Configure Supabase credentials in .env.local.

Run the development server:

npm run dev

Open http://localhost:3000 in your browser.

Environment Variables
# Supabase Configuration
NEXT_PUBLIC_SUPABASE_URL=your-supabase-project-url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-supabase-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-supabase-service-role-key

# Vercel Cron Job Secret
CRON_SECRET=a-secure-random-string-for-cron-job-auth

Project Structure
code-it/
├── src/
│   ├── app/                # Next.js app directory
│   │   ├── (main)/           # Main app pages (leaderboard, etc.)
│   │   ├── (auth)/           # Authentication pages
│   │   └── api/              # API routes (e.g., for cron jobs)
│   ├── components/         # Reusable React components
│   │   ├── ui/               # Generic UI components (buttons, cards)
│   │   └── features/         # Feature-specific components
│   ├── lib/                # Utility libraries
│   │   ├── supabase/         # Supabase client and helpers
│   │   ├── api-clients/      # Clients for Codeforces/LeetCode
│   │   └── utils.ts          # General utility functions
│   └── types/              # TypeScript type definitions
├── public/                 # Static assets
└── scripts/                # Utility scripts

Core Components
Leaderboard Engine
Calculates the custom "Code IT Score" based on a weighted formula of Codeforces rating, LeetCode rating, and problems solved. The leaderboard is filterable and updates automatically.

Growth Tracker
Stores historical snapshots of user data to generate time-series charts, allowing students to visualize their progress on each platform over time.

Data Scraper
A serverless function triggered by a cron job that fetches the latest data from external APIs, updates the Supabase database, and ensures the platform's data is always current.

Development Workflow
Local Development
Ensure your Supabase local development environment is running or you are connected to the cloud project.

Start the Next.js development server.

Use the Supabase CLI to manage database migrations.

Testing
# Run linting and type checking
npm run lint

# Run unit tests (if configured)
npm run test
