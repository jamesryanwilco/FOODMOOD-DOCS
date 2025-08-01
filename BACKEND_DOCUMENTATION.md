# 🏗️ Backend Architecture Documentation

This document provides a comprehensive overview of the backend systems that power the Awara application.

---

## 1. Core Philosophy

The backend is designed with a modern, hybrid approach, combining a custom Node.js server for specific business logic with a powerful Backend-as-a-Service (BaaS) platform for core functionalities.

-   **Custom Logic:** A dedicated Node.js server handles tasks that require secure, private credentials, such as proxying requests to the OpenAI API.
-   **Managed Services:** Supabase is used as a BaaS to handle user authentication, database management, and serverless functions, which dramatically accelerates development and provides a robust, scalable foundation.

---

## 2. Components

### 2.1. Node.js (Express) API Server

-   **Location:** `/backend`
-   **Purpose:** The primary role of this server is to act as a **secure proxy** for the OpenAI API. It exposes two endpoints that the mobile app communicates with:
    -   `/api/chat`: Handles requests for the "AwareAI" chatbot.
    -   `/api/insights`: Handles requests for the AI-powered analysis of user entries.
-   **Security:** This server securely stores the `OPENAI_API_KEY` as an environment variable. By acting as a middleman, it ensures that this secret key is **never** exposed on the client-side mobile application.
-   **Deployment:** The server is deployed as a serverless function on **Vercel**.
    -   **Configuration:** A `vercel.json` file within the `/backend` directory configures the build process and routing.
    -   **Deployment Command:** `vercel --prod` from the project root.

### 2.2. Supabase (Backend-as-a-Service)

Supabase provides the core infrastructure for all user-related data and services.

#### a. Authentication

-   **Service:** Supabase Auth
-   **Features:**
    -   Handles all user sign-up, sign-in, and session management.
    -   Provides secure authentication via email/password and Apple Sign In.
    -   Manages a full password reset flow, using deep links (`foodmood://`) to correctly redirect users back to the app.
-   **Custom Emails:** Supabase is configured with a custom SMTP provider (**SendGrid**) to send professional, branded authentication emails from a dedicated domain (`awaraworld.com`).

#### b. Database

-   **Service:** Supabase Postgres Database
-   **Schema:**
    -   **`profiles` table:** Stores user-specific data that is not part of the standard authentication schema. It is linked one-to-one with the `auth.users` table.
        -   `id` (primary key, foreign key to `auth.users.id`)
        -   `goals` (JSONB): Stores the user's selected and custom intentions.
        -   `is_premium` (BOOLEAN): Securely tracks the user's premium subscription status.
    -   **`entries` table:** Stores all user-generated meal check-in data. Each entry is linked to a user.
    -   **`insight_history` table:** Stores all AI-generated insight reports, linked to a user.
-   **Security:** All tables are protected by **Row Level Security (RLS) policies**. These policies are rules at the database level that ensure a user can **only** ever access, modify, or delete their own data.

#### c. Serverless Edge Function (`revenuecat-webhook`)

-   **Location:** `supabase/functions/revenuecat-webhook`
-   **Purpose:** This is a secure, server-side function that acts as a webhook receiver for **RevenueCat**.
-   **Workflow:**
    1.  A user makes a purchase in the app.
    2.  Apple/Google processes the payment and notifies RevenueCat.
    3.  RevenueCat sends a secure webhook (a POST request) to this Edge Function's unique URL.
    4.  The function verifies the request is genuinely from RevenueCat using a shared secret.
    5.  It then processes the event (e.g., `INITIAL_PURCHASE`, `RENEWAL`, `CANCELLATION`) and updates the `is_premium` flag in the user's `profiles` table accordingly.
-   **Security:** This server-to-server integration is the most secure and reliable way to manage subscription status, as it cannot be spoofed by the client.

---

## 3. Data Flow Summary

-   **User Data (Entries, Goals):** App -> Supabase Database
-   **Authentication:** App -> Supabase Auth
-   **AI Features:** App -> Vercel Backend -> OpenAI API
-   **Subscription Status:** App Store/Play Store -> RevenueCat -> Supabase Edge Function -> Supabase Database 