# 💾 Data Storage Guide

This document provides a complete reference for all data stored in the application, including the Supabase cloud database and the user's local device via **AsyncStorage**.

---

## 1. Supabase Cloud Storage (`entries` Table)

With the introduction of user accounts, all user-generated check-in data is now stored in a central `entries` table in the Supabase database. This allows for cross-device syncing. The `DatabaseService.js` file handles the mapping between the database's `snake_case` columns and the app's `camelCase` state.

### The Entry Object Structure

| Property | Type | Set In | Description | Used In |
| :--- | :--- | :--- | :--- | :--- |
| **`id`** | `String` | `Step5_ReminderScreen.js` | A unique UUIDv4 generated when the first phase of the check-in is saved as "pending". | **Entries:** Key for lists & updates.<br>**Phase 2 Screens:** Identifies the entry to update. |
| **`status`** | `String` | `Step5_ReminderScreen.js` ('pending'), `CheckInContext.js` ('completed') | The current state of the entry. | **Entries:** Filters between Pending/Completed lists.<br>**Insights:** Filters for completed entries only. |
| | | | | |
| **Phase 1 Data** | | | *(Collected before the meal)* | |
| **`date`** | `String` | `Step1_BasicInfoScreen.js` | The ISO string of the date and time the user is logging. | **Entries:** Display and editing.<br>**Insights:** Used for streak calculation and date range filtering. |
| **`mealType`** | `String` | `Step1_BasicInfoScreen.js` | e.g., "Breakfast", "Lunch", "Dinner", "Snack". | **Entries:** Display and editing.<br>**Insights:** Displayed in modals. |
| **`foodDescription`** | `String` | `Step1_BasicInfoScreen.js` | The user's description of the meal. | **Entries:** Display and editing.<br>**Insights:** Used to identify "Top Energy Boost" meals. |
| **`emotions`** | `Array<String>` | `Step2_EmotionScreen.js` / `Step2a_MoodMeterScreen.js` | The user's selected emotions before eating. | **Entries:** "Before" mood display.<br>**Insights:** "Before" value for "Common Mood Shifts" analysis. |
| **`energyLevel`** | `Number` | `Step3_PhysicalStateScreen.js` | The user's energy level (0-10) before eating. | **Entries:** "Before" energy display.<br>**Insights:** "Before" value for "Avg. Energy Boost" & energy line chart. |
| **`hungerLevel`** | `Number` | `Step3_PhysicalStateScreen.js` | The user's hunger level (0-10) before eating. | **Insights:** "Before" value for the hunger/fullness line chart. |
| **`motivations`** | `Array<String>` | `Step4_MotivationsScreen.js` | The user's selected reasons for eating. | **Entries:** Displays as tags.<br>**Insights:** Powers the "Eating Motivations" pie chart. |
| **`notes`** | `String` | `Step4_MotivationsScreen.js` | The user's free-text notes before eating. | *(Not currently used in UI)* |
| **`phase1_completed_at`**| `String` | `Step5_ReminderScreen.js` | The ISO string of when the user finished Phase 1. | **Entries:** Sorting pending entries. |
| **`reminder_minutes`**| `Number` | `Step5_ReminderScreen.js` | The number of minutes for the reminder notification. | **Entries:** Countdown timer for pending entries. |
| | | | | |
| **Phase 2 Data** | | | *(Collected after the meal)* | |
| **`mindfulness`** | `Number` | `Phase2_Step1_ExperienceScreen.js` | User's mindfulness level (0-10) during the meal. | **Insights:** Used in the "How You Eat" line chart. |
| **`eatingSpeed`** | `Number` | `Phase2_Step1_ExperienceScreen.js` | User's eating speed (0-10) during the meal. | **Insights:** Used in the "How You Eat" line chart. |
| **`energy`** | `Number` | `Phase2_Step1_ExperienceScreen.js` | User's energy level (0-10) **after** the meal. | **Entries:** "After" energy display.<br>**Insights:** "After" value for "Avg. Energy Boost" & energy line chart. |
| **`fullness`** | `Number` | `Phase2_Step1_ExperienceScreen.js` | User's fullness level (0-10) after the meal. | **Insights:** "After" value for the hunger/fullness line chart. |
| **`emotionsAfter`** | `Array<String>` | `Phase2_Step1a_EmotionScreen.js` / `Phase2_Step1ba_MoodMeterScreen.js` | The user's selected emotions **after** eating. | **Entries:** "After" mood display.<br>**Insights:** "After" value for "Common Mood Shifts" analysis. |
| **`goalAligned`** | `Boolean` | `Phase2_Step2_GoalScreen.js` | Whether the user felt the meal aligned with their goals. | *(Not currently used in UI)* |
| **`phase2_completed_at`**| `String` | `CheckInContext.js` (in `completeCheckIn`) | The ISO string of when the user finished the entire entry. | **Entries:** Sorting and grouping completed entries by week. |
| | | | | |
| **AI Analysis Data** | | | *(Collected from the AI Image Analysis feature)* | |
| **`aiAnalysis`** | `Object` | `AIAnalysisScreen.js` | An object containing the full analysis from the AI. This entire object is `null` if the user did not perform an analysis. | **Insights:** The nested properties are sent to the AI Insights engine for deeper pattern recognition. |

---

## 2. AsyncStorage Keys

AsyncStorage is still used for non-critical, client-side data.

| Key | Set In | Description |
| :--- | :--- | :--- |
| **`hasOnboarded`** | `OnboardingScreen.js` | A simple boolean flag (`'true'`) set after the user completes the initial onboarding flow. Used in `App.js` to determine the initial route. |
| **`image_analysis_usage`**| `useImageAnalysisUsage.js` | A stringified JSON object (e.g., `{ "date": "2023-08-03", "count": 1 }`) that tracks the number of times the user has used the AI Image Analysis feature per day. It is used to enforce the daily usage limit. | 