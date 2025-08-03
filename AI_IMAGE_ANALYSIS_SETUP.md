# 📸 AI Image Analysis: Setup and Architecture Guide

This document provides a comprehensive guide to the setup, architecture, and data flow of the AI-powered meal image analysis feature.

---

## 1. Feature Overview

The goal of this feature is to allow users to take a photo of their meal, upload it, and receive a detailed AI-generated analysis, including a description, estimated ingredients, a "rainbow" analysis, macronutrient estimates, and a mindful reflection prompt.

---

## 2. Architecture & Data Flow

The process involves four key components: the React Native client, Supabase Storage for hosting the image, a Vercel Serverless Function acting as a secure backend, and the OpenAI Vision API.

The data flows in the following sequence:

1.  **Client (Image Selection):** The user selects an image from their device's library within the `Step1_BasicInfoScreen`.
2.  **Client (Upload to Supabase):** The app uploads the selected image directly to a dedicated Supabase Storage bucket (`meal-images`).
3.  **Supabase (URL Generation):** Upon successful upload, Supabase generates a stable, public URL for the image.
4.  **Client (Navigation):** The app navigates to the `AIAnalysisScreen`, passing the public image URL as a navigation parameter.
5.  **Client (API Request):** The `AIAnalysisScreen` sends a `POST` request to our Vercel backend endpoint (`/api/analyze-image`), with the public image URL in the request body.
6.  **Vercel Backend (Proxy to OpenAI):** The serverless function receives the request, constructs a carefully formatted prompt, and sends it along with the image URL to the OpenAI GPT-4o Vision API. This is crucial as it keeps the `OPENAI_API_KEY` secure on the backend.
7.  **OpenAI (Analysis):** OpenAI fetches the image from the public URL, analyzes it based on the prompt, and returns a structured JSON response.
8.  **Vercel & Client (Response Handling):** The Vercel function forwards the JSON response back to the `AIAnalysisScreen`, where it is parsed and displayed to the user.

---

## 3. Component Breakdown

### a. Supabase Storage

-   **Bucket Name:** `meal-images`
-   **Purpose:** This bucket is used exclusively for storing user-uploaded meal images.
-   **Configuration:** The bucket must be configured with a public access policy to allow the OpenAI API to read the image files from their public URLs.

### b. Frontend: `src/screens/checkin/Step1_BasicInfoScreen.js`

-   This screen contains the `handleAiAnalysis` function.
-   It uses `expo-image-picker` to allow the user to select an image.
-   It handles the direct upload of the image from the client to the Supabase bucket and retrieves the public URL.
-   It then navigates to the `AIAnalysisScreen` with the URL.

### c. Frontend: `src/screens/checkin/AIAnalysisScreen.js`

-   Receives the public `imageUrl` via navigation props.
-   Sends the URL to the backend API endpoint for analysis.
-   Renders the JSON response returned from the backend.

### d. Backend: `app/api/analyze-image/route.js`

-   **Endpoint:** `/api/analyze-image`
-   **Purpose:** Acts as a secure proxy to the OpenAI API.
-   **Key Logic:** It receives the public `imageUrl` from the client and constructs a `messages` payload for the OpenAI API that is specifically formatted for vision requests, including both the text prompt and the `image_url` object.

---

## 4. Setup & Environment Variables

To ensure this feature functions correctly, the following environment variables must be set up in a `.env` file in the project root:

1.  **`OPENAI_API_KEY`**: Your secret API key from OpenAI. This is used by the Vercel backend.
    ```
    OPENAI_API_KEY="sk-..."
    ```

2.  **`API_URL`**: The base URL for your deployed Vercel backend. This is used by the React Native client to know where to send API requests.
    ```
    API_URL="https://your-vercel-project-url.vercel.app"
    ``` 