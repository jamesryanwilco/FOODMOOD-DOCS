# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## v1.4.0 (Unreleased)

### ✨ New Features

-   **Professional Paywall:**
    -   Replaced the custom-built premium screen with **RevenueCat Paywalls**.
    -   The paywall is now fully configurable from the RevenueCat dashboard, allowing for A/B testing and remote updates without requiring an app release.
-   **Daily Reminders:**
    -   Added a new "Daily Reminder" feature to the settings screen.
    -   Users can enable or disable a daily, repeating notification and select a specific time for it to be delivered.
    -   The user's preference is saved to their profile in the Supabase database.
-   **Chat on Guide Page:**
    -   Added a new button on the "Guide" screen to provide users with direct access to the Awara Chat assistant.

### 🐛 Bug Fixes & Refinements

-   **Paywall Navigation:**
    -   Fixed a critical bug where the app would crash if the user dismissed the paywall modal without making a purchase. The navigation logic is now more robust.
    -   Resolved a crash that occurred when a user without an active subscription attempted to restore purchases.
-   **Notification Scheduling:**
    -   Fixed a bug where daily reminders would fire immediately if the selected time had already passed for the current day. The logic now correctly schedules the notification for the next upcoming occurrence.
-   **Chart Styling:**
    -   Corrected a styling inconsistency between the line charts on the Insights screen. All charts now use solid, opaque dots for data points.
-   **Improved Development Workflow:**
    -   Transitioned the recommended testing workflow from creating slow production builds to using fast, iterable **Development Builds** via `eas build --profile development`.

## v1.4.0 - 2025-08-03

### ✨ New Features

-   **AI Meal Analysis (Premium Feature):**
    -   Users can now get a detailed AI analysis of their meal by taking a photo or selecting one from their library.
    -   The feature is restricted to premium subscribers and includes a daily usage limit of 10 analyses to manage costs.
    -   The analysis provides an AI-generated description, a list of estimated ingredients, a "rainbow analysis" of food colors, macronutrient estimates, and a mindful reflection prompt.
-   **Integration with AI Insights:**
    -   The data from the new AI Meal Analysis is now seamlessly integrated into the main AI Insights feature on the Insights page.
    -   The insights engine has been upgraded to find correlations between the AI-identified ingredients/macronutrients and the user's reported energy and mood shifts, providing richer, more holistic feedback.

### 🐛 Bug Fixes & Refinements

-   **Keyboard Handling:** Implemented a `KeyboardAvoidingView` on the main check-in screen to prevent the keyboard from obscuring the meal description text input.
-   **UI/UX Polish:**
    -   Completely redesigned the AI Analysis screen with a modern, card-based UI, improved typography, and a warmer color scheme.
    -   Added a professional skeleton loading state to the analysis screen to provide better user feedback while the AI is working.
    -   The "Analyze Meal with AI" button is now disabled after one use per check-in to create a clearer user flow.
    -   The "Done" button on the analysis screen is now disabled while the analysis is in progress.

## v1.3.0 - 2025-07-30

This is a major architectural update that transforms the app into a secure, cloud-native service with a full authentication and subscription system.

### ✨ New Features

-   **User Authentication:**
    -   Implemented a complete user authentication system powered by Supabase.
    -   Users can now sign up and sign in with email and password.
    -   Added "Sign in with Apple" for a seamless login experience on iOS.
    -   Built a full password reset flow using deep linking to correctly open the app from an email.
    -   Added a "Log Out" feature in the settings screen.
-   **Premium Subscription System:**
    -   Integrated RevenueCat to handle in-app purchases for a new "Premium" subscription.
    -   Created a dedicated `PremiumScreen` to display offerings and handle the purchase flow.
    -   Added a "Restore Purchases" button to the settings screen.
-   **Custom Authentication Emails:**
    -   Configured a custom SMTP provider (SendGrid) to send professional, branded authentication emails from the app's own domain.

### 🏗️ Architectural Changes

-   **Secure Backend Server:**
    -   Introduced a new Node.js (Express) backend server to act as a secure proxy for all OpenAI API calls, protecting secret keys.
    -   The backend is deployed to and served from Vercel.
-   **Cloud Data Migration:**
    -   Migrated all user-specific data from local device storage (`AsyncStorage`) to a secure, cloud-based Supabase database. This includes:
        -   Meal Entries
        -   User Goals/Intentions
        -   AI Insight History
    -   The app is now fully cloud-synced, allowing users to access their data from any device.
-   **Serverless Webhook for Subscriptions:**
    -   Deployed a Supabase Edge Function to securely handle webhooks from RevenueCat.
    -   This function automatically updates a user's `is_premium` status in the database in real-time when their subscription status changes.

### 🐛 Bug Fixes & Refinements

-   Refactored all screens (`Entries`, `Insights`, `Goals`, and the entire check-in flow) to use the new cloud database, removing all dependencies on local storage for user data.
-   Resolved numerous complex bugs related to deep linking, native build configurations, and the data migration process.
-   Updated all technical documentation to reflect the new architecture.

## v1.2.0 - 2025-07-27

### Added
- **AI-Powered Chatbot:** Introduced "AwarAI," an intelligent assistant to help users with mindful eating questions and app features.
- **Markdown Support:** The chatbot can now send formatted responses, including lists and bold text, for better readability.
- **Secure API Key Handling:** Integrated `react-native-dotenv` to securely manage the OpenAI API key.
- **Interactive Goal Management:** Implemented drag-and-drop reordering for the goals list on the "View Goals" screen.
- **Swipe-to-Delete:** Added swipe-to-delete functionality for goals on the "View Goals" screen, with an improved delete button that includes a trash icon.
- **Multiple Custom Goals:** Users can now add, view, and delete multiple custom-written intentions on the "Select Goals" screen.

### Changed
- **Chat UI:** The initial chat message now includes the app icon, and the chatbot's responses are full-width for improved readability.
- **Streamlined Goal Navigation:** Removed the intermediate "Goals" landing page. The "Goals" tab now navigates directly to the user's list of current intentions (`ViewGoalsScreen`).
- **Improved "Select Goals" UI:** The "Select Goals" page has been redesigned with a fixed header and no longer uses collapsible sections, making all goal options immediately visible.
- **Fixed Layout on "View Goals" Screen:** The "View Goals" screen now has a fixed header and footer, with only the list of goals being scrollable.

### Fixed
- **API Key Security:** Removed a hardcoded OpenAI API key from the source code to resolve a security vulnerability and prevent secrets from being committed to the repository.
- **Chat Layout:** Corrected a layout issue where message bubbles were incorrectly sized after adding the initial message icon.
- **Keyboard Handling:** The text input on the "Select Goals" screen now moves into view when the keyboard is open, preventing it from being obscured.
- **Data Compatibility:** Fixed a crash on the goals pages caused by an older data format. The app now gracefully handles both old and new data structures for custom goals. 