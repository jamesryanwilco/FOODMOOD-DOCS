# 🛠️ Technical Documentation

This document provides a technical overview of the Food & Mood application's architecture and implementation.

---

## 1. Project Structure

The `src` directory is organized to separate concerns and improve maintainability:

-   **/src/screens**: Contains all main screen components, organized by feature (e.g., `checkin`, `onboarding`, `GoalsScreen.js`, `InsightsScreen.js`).
-   **/src/navigation**: Manages all React Navigation logic. `AppNavigator.js` is the main stack navigator, while `MainTabNavigator.js` handles the primary tabbed interface for "Check In," "Entries," "Insights," "Guide," and "Goals."
-   **/src/components**: Stores reusable UI components (e.g., `StyledSegmentedControl.js`).
-   **/src/context**: Manages global state (e.g., `CheckInContext.js`).
-   **/src/services**: Houses logic for external services (e.g., `supabase.js`, `OpenAIService.js`).
-   **/src/data**: Stores static data (e.g., `emotions.js`).
-   **/backend**: A new directory at the root of the project containing a Node.js (Express) server. This server acts as a secure proxy for all OpenAI API calls.

---

## 2. Architecture & Backend

-   **Monorepo Structure**: The project is a monorepo containing both the React Native mobile app (`/src`) and a Node.js backend server (`/backend`).
-   **Backend Server**: An Express server responsible for securely handling all communication with the OpenAI API. This prevents API keys from being exposed on the client-side. The server is deployed on Vercel.
-   **Supabase (Backend-as-a-Service)**: Supabase is used for all user authentication and will be used for future database needs. It handles user sign-up, sign-in, password resets, and social logins.
-   **RevenueCat**: The app uses RevenueCat for in-app subscription management.
    -   **Paywalls**: The `PremiumScreen.js` uses the `<RevenueCatUI.Paywall />` component from the `react-native-purchases-ui` library to display a remotely configurable paywall. This allows for rapid iteration and A/B testing from the RevenueCat dashboard without needing to ship a new app version.
    -   **Webhook**: A Supabase Edge Function (`/supabase/functions/revenuecat-webhook`) serves as a secure webhook to receive subscription status updates from RevenueCat, ensuring the user's `is_premium` status in the `profiles` table is always up-to-date.

---

## 3. State Management

-   **React Context (`CheckInContext.js`)**: The app uses React's Context API to manage the state of a meal entry during creation, avoiding prop drilling.
    -   The `CheckInProvider` wraps the main `AppNavigator`, making the `checkInData` object and its updater functions available to all screens in the check-in flow.
    -   State is reset via `resetCheckInData()` after a check-in is successfully saved.
    **Authentication State**: The root `App.js` component manages the user's session state. It listens for authentication changes from Supabase and conditionally renders either the `AuthNavigator` (for logged-out users) or the `AppNavigator` (for logged-in users).

---

## 4. Navigation

-   **React Navigation**: The app uses `@react-navigation/stack` and `@react-navigation/bottom-tabs`.
    -   **`AppNavigator.js`**: A primary `StackNavigator` that manages the transition between the initial `WelcomeScreen`, the `Onboarding` flow, and the `MainTabNavigator`. Check-in screens and the `ResetPasswordScreen` are part of this stack.
    -   **`MainTabNavigator.js`**: A `BottomTabNavigator` that provides access to the core features: "Check In," "Entries," "Insights," "Guide," and "Goals."
    -   **`AuthNavigator.js`**: A new `StackNavigator` that manages the unauthenticated user flow, including a landing screen, sign-in screen, sign-up screen, and forgot password screen.
    **Deep Linking**: The app is configured with a custom URL scheme (`foodmood://`) to handle password reset links. Logic in `App.js` listens for incoming links, extracts the auth tokens, and navigates the user to the appropriate screen.

---

## 5. User Data & Storage

-   **`@react-native-async-storage/async-storage`**: All user-generated content is currently stored locally on the device. User sessions from Supabase are also persisted here.
    -   Meal entries are saved as a single JSON array under the key `pending_entries`. An entry's `status` field is updated from `'pending'` to `'completed'` upon completion. The completed entry now also includes `emotionsAfter`, `fullness`, `mindfulness`, and `eatingSpeed` properties.
    -   User intentions are saved as a JSON object under the key `user_goals`, containing `selected` and `custom` goal properties.
    -   The `EntriesScreen` fetches and parses the `pending_entries` data for display. It also contains the logic to modify a completed entry via a modal and save the changes back to AsyncStorage.
    -   The `InsightsScreen` fetches and analyzes the `pending_entries` data to calculate and display all statistics and charts.
        -   It now correctly uses the user-provided `date` property for all date-sensitive calculations (streak, filtering) instead of the entry creation timestamp.
        -   It generates three interactive line plots: Energy, Hunger/Fullness, and How You Eat.
    -   The `GoalsScreen` fetches and updates the `user_goals` data.
    **Future Data Storage**: With the introduction of user accounts, all this data is planned to be migrated to the Supabase database to allow for cross-device syncing.

---

## 6. Notifications & Timers

-   **`expo-notifications`**: The app uses this library to schedule and handle local notifications for meal reminders.
    -   **Permissions**: On app startup, `App.js` requests notification permissions from the user. This is handled in the root component to ensure permissions are ready when needed.
    -   **Scheduling**: In `Step5_ReminderScreen.js`, a notification is scheduled locally after the user completes Phase 1 of a check-in. The trigger time is based on the number of minutes the user inputs. The `entryId` is passed in the notification's `data` payload.
    -   **Daily Reminders**: In `SettingsScreen.js`, the user can enable and set a time for a daily, repeating reminder. The logic in `NotificationService.js` correctly calculates the next trigger date to prevent the notification from firing immediately if the time has already passed for the current day. The user's preferences are saved to their Supabase profile.
    -   **Handling**: `App.js` contains a listener (`addNotificationResponseReceivedListener`) that waits for a user to tap on a notification. When an interaction is detected, it first checks the status of the corresponding entry in `AsyncStorage`. 
        - If the entry has already been marked 'completed', it navigates the user to the "Completed" tab on the `EntriesScreen` and displays an alert.
        - Otherwise, it uses the `entryId` from the payload to navigate the user directly to the "Complete Meal" flow (`CompleteStep1`).
-   **Live Timers**: In `EntriesScreen.js`, a `useEffect` hook runs an interval timer to provide a live countdown for pending entries.

---

## 7. Key Dependencies

-   **Authentication**: `@supabase/supabase-js`, `expo-apple-authentication`, `@react-native-async-storage/async-storage`, `expo-linking`.
-   **Navigation**: `@react-navigation/native`, `@react-navigation/stack`, `@react-navigation/bottom-tabs`.
-   **Subscriptions & Paywall**: `react-native-purchases`, `react-native-purchases-ui`.
-   **Local Storage**: `@react-native-async-storage/async-storage`.
-   `expo-notifications`, `expo-device`: For scheduling and handling local reminders.
-   `@react-native-community/datetimepicker`: For the native time picker used in the settings screen.
-   `date-fns`: For robust date manipulation, used for grouping entries by week and for filtering insights by date ranges.
-   `react-native-chart-kit`: For rendering the pie chart and all line plots on the Insights screen. Custom styling is used for line fills.
-   `react-native-heroicons`, `react-native-svg`: For high-quality, consistent iconography.
-   `@react-native-community/slider`: For the sliders used in the check-in process.
-   `@react-native-segmented-control/segmented-control`: For the "Pending/Completed" tabs on the Entries screen and the date filter on the Insights screen.
-   `uuid`, `react-native-get-random-values`: For generating unique IDs for each entry. 
-   **Goals Management**: `react-native-draggable-flatlist`, `react-native-gesture-handler`.
- `SelectGoalsScreen.js`: Allows users to select from a list of predefined goals or create their own. The screen now supports adding multiple custom goals.
- `ViewGoalsScreen.js`: Displays the user's selected and custom goals. This screen has been enhanced with `react-native-draggable-flatlist` to allow users to reorder their selected goals via drag-and-drop. It also uses the `Swipeable` component from `react-native-gesture-handler` to enable swipe-to-delete functionality.
-
-### Settings & Notifications
- `SettingsScreen.js`: A new screen providing users with options to manage their preferences. It features a dark mode theme and includes toggles for notification permissions and general reminders.
- `NotificationService.js`: Enhanced to support daily, repeating general reminders. It includes logic to schedule a notification at noon and includes the user's current streak in the message.
- `streakUtils.js`: A new utility to centralize the streak calculation logic, which is now used by both the `InsightsScreen` and the `NotificationService`.
- `DatabaseService.js`: Includes a generic `updateProfile` function to save user settings, such as reminder preferences, to the Supabase database.
-
-### Interactive Mood Meter
-The `MoodMeterGrid.js` component has been significantly refactored to create a more interactive and intuitive user experience.
-
-- **Gesture Handling**: The component now uses the `GestureDetector` API from `react-native-gesture-handler` to manage user interactions. It combines a `PinchGesture` for zooming and a `TapGesture` (implemented via `Pressable`) for selection.
-- **Assisted Zoom**: The zoom is now "assisted," meaning it automatically centers on the currently selected emotion.
-  - The `useEffect` hook tracks changes to the `selectedValue` prop.
-  - When the selection changes, it calculates the target coordinates and uses `withTiming` to smoothly animate the grid's position.
-- **Conditional Panning**: The centering translation is only applied when the grid is zoomed in (`scale > 1`). An `interpolate` function is used to create a smooth transition as the user starts and ends their zoom.
-- **Cell Selection**: The grid cells are now `Pressable` components. The `onCellPress` callback is passed up to the parent screen, which then updates the slider values.
-- **Optimized Selection Style**: The `transform` property on the selected cell has been replaced with a thicker border and a larger font size to provide a clean, crisp selection effect without any rendering glitches. 