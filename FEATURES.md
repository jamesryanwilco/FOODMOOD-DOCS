# 🚧 Food & Mood Features

This document tracks the development status of the app's core features.

## ✅ Completed

-   **Onboarding Flow:** A multi-page introduction to mindful eating and how the app works, with swipe and button navigation.
-   **Basic UI Structure:** Welcome screen, onboarding, and main tab navigation are in place.
-   **Main App UI Shell:** Placeholder screens for Check-in, Analyze, and Guide are set up with a tab bar.
-   **Full Check-In Flow (Phase 1 & 2):**
    -   [✅] **Before-Eating Check-In:** Log meal info, emotions, physical state, and motivations (including "Energy" and "Performance").
    -   [✅] **After-Eating Check-In:** Log post-meal emotions, experience, and goal fulfillment.
-   **Entry Management & Display:**
    -   [✅] View pending entries.
    -   [✅] View completed entries grouped by week and sorted from latest to oldest.
    -   [✅] Display meal-type icons, eating motivations, and before/after comparisons for mood and energy.
    -   [✅] Delete entries (pending or completed) with a confirmation prompt.
    -   [✅] Modify completed entries (date, meal type, description) via an in-place modal.
-   **Reminders & Timers:**
    -   [✅] **Notification Reminders:** Users are prompted to set a reminder (in minutes) after completing a "before eating" entry. A local notification is scheduled to remind them to complete the second part.
    -   [✅] **Daily Reminders:** Users can now enable a daily, repeating notification and set a specific time for it in the settings screen.
    -   [✅] **Smart Notification Handling:** If a user taps a notification for an entry that has already been completed, they are gracefully redirected to the completed entries list instead of the check-in flow.
    -   [✅] **Live Countdown:** The "Pending" entries list displays a live countdown for each entry, showing the time remaining until the reminder is due.
-   **Emotion Matrix:** A guided tool for users to identify and log their emotional state (used for both pre-meal and post-meal).
-   **Goals & Intentions Screen ("Goals" Tab):**
    -   [✅] **Streamlined Flow:** Users are taken directly to their list of current intentions.
    -   [✅] **Interactive Goal Management:**
        -   Drag and drop to reorder selected intentions.
        -   Swipe right to delete an intention.
    -   [✅] **Multiple Custom Intentions:** Users can now add and manage a list of their own custom-written intentions.
-   **Insights Dashboard ("Insights" Tab):**
    -   [✅] **Interactive Analytics:**
        -   Display total entries, average energy boost, and top 3 energy-boosting meals.
        -   Display an accurate daily streak counter based on the user-assigned date of the meal.
        -   Filter all insights by "This Week" (default), "This Month," or "All Time."
    -   [✅] **Secure, Backend-Powered AI Analysis:**
        -   Users can generate personalized insights based on their filtered data.
        -   API calls to OpenAI are now securely proxied through a dedicated backend server, protecting API keys.
        -   A confirmation is required if an analysis for the selected period already exists.
    -   [✅] **AI Insight History:**
        -   All generated insights are saved for future reference.
        -   A dedicated history screen allows users to review past insights.
        -   Includes an interactive calendar to filter history by date.
    -   [✅] **Eating Motivation Chart:** A pie chart visualizing the breakdown of eating motivations.
    -   [✅] **Interactive Mood Shift List:**
        -   Displays the top 3 most common mood transitions (e.g., "Stressed → Calm").
        -   Tapping a shift opens a modal showing all associated foods, categorized by meal type (Breakfast, Lunch, etc.).
    -   [✅] **Interactive Data Plots:**
        -   All plots feature a semi-transparent fill under the primary line for better readability.
        -   **Energy Plot:** Dual-line chart for "Energy Before" vs. "Energy After."
        -   **Hunger & Fullness Plot:** Dual-line chart for "Hunger Before" vs. "Fullness After."
        -   **How You Eat Plot:** Dual-line chart for "Mindfulness" vs. "Eating Speed."
    -   [✅] **AI Meal Analysis (Premium):**
        -   Users can analyze a meal by taking a photo or selecting one from their library.
        -   The feature is restricted to premium users and has a daily usage limit to manage costs.
        -   Provides a detailed breakdown: description, ingredients, rainbow analysis, and macronutrient estimates.
        -   This AI-generated data is now integrated into the main AI Insights analysis for richer, more detailed patterns.
-   **UI & Animations:**
    -   [✅] Added a continuous, smooth spinning animation to the main Check-In wheel.
    -   [✅] Refined UI layouts for better alignment and visual appeal across multiple screens.
    -   [✅] Implemented a reusable, styled segmented control for consistent UI across the app.

## 🏃‍♀️ In Progress

-   **User Authentication:**
    -   [✅] **Email & Password:** Users can create an account and sign in.
    -   [✅] **Apple Sign In:** Users can sign in with their Apple ID (iOS only).
    -   [✅] **Password Reset:** A full deep-linking password reset flow has been implemented.
    -   [✅] **Logout:** Users can log out from the settings screen.
-   **Premium Subscriptions:**
    -   [ ] Integration with RevenueCat to handle in-app purchases.
    -   [ ] A dedicated "Premium" screen to display feature benefits and purchase options.
-   **AI Assistant ("AwareAI"):**
    -   [✅] Integration of a conversational AI to answer questions about mindful eating and app features.
    -   [✅] The AI is now accessible from both the main Check-In screen and the Guide page.

## 📝 Planned

### Core Functionality
-   **Meal History:** A searchable/filterable log of all past check-ins.

### Content & Support ("Guide" Tab)
-   **Educational Articles:**
    -   [ ] "What is Mindful Eating?"
    -   [ ] "Understanding Your Hunger Cues"
    -   [ ] "Tips for Slowing Down"
-   **Guided Meditations:** Short audio guides for mindful eating.

### Settings & Personalization
-   **User Profile & Intentions:** Allow users to set or update their primary intention/goal.
-   **Data Export:** Option for users to export their data.
- **Settings Screen**: A dedicated screen to manage app preferences.
- **Notification Controls**: Users can enable or disable all notifications from the app.
- **General Reminders**: An option to receive a daily reminder at noon to encourage regular check-ins. The reminder message includes the user's current streak to foster engagement.
-   **AI Assistant ("AwareAI"):**
    -   [ ] A conversational AI, now powered by the secure backend, to answer questions about mindful eating and app features.

### Enhanced Mood Selection
- **Interactive Mood Meter**: The mood meter is now fully interactive.
- **Pinch-to-Zoom**: Users can pinch to zoom into the mood grid for a closer look.
- **Tap to Select**: Tapping on any emotion in the grid instantly updates the sliders to the corresponding values.
- **Assisted Zoom**: The zoom functionality is centered on the currently selected emotion, and the grid automatically pans to keep the selection in view as the user moves the sliders.
- **Improved Selection UI**: The selected emotion is highlighted with a clean, crisp border and larger text, avoiding any blurriness or distortion.
## How to Use

The app is designed to be intuitive. Upon first opening, you'll be guided through a brief onboarding process to set up your initial goals. After that, you can start checking in with your meals immediately. 