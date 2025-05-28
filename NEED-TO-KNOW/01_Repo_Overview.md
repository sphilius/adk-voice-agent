# `sphilius/adk-voice-agent` Repository Overview

## Overall Purpose

The `sphilius/adk-voice-agent` repository hosts a voice-activated assistant specifically designed for managing Google Calendar. Users can interact with the assistant using voice commands to perform various calendar-related tasks, such as creating events, querying existing events, and more.

## Architecture

The application follows a client-server architecture with distinct frontend and backend components, leveraging the Google ADK (Agent Development Kit) for core agent functionalities.

### Frontend

-   **Location:** `app/static/`
-   **Technologies:** HTML, JavaScript
-   **Responsibilities:**
    -   Capturing user voice input through the browser's microphone.
    -   Establishing and managing a WebSocket connection with the backend server for real-time communication.
    -   Sending captured audio data to the backend.
    -   Receiving responses (text and audio) from the backend.
    -   Playing back the audio response to the user.
    -   Displaying textual information or transcriptions received from the agent.

### Backend

-   **Location:** `app/main.py`
-   **Framework:** FastAPI
-   **Key Components:**
    -   **WebSocket Endpoint:** Provides a communication channel for the frontend to send audio data and receive responses.
    -   **ADK Integration:** Utilizes components from the `google.adk` library.
        -   `LiveRequestQueue`: Manages incoming user requests.
        -   `Runner`: Executes the agent logic.
        -   `InMemorySessionService`: Manages user session information.
    -   **Text-to-Speech (TTS):** Converts the agent's textual responses into audible speech.

### Jarvis Agent

-   **Location:** `app/jarvis/agent.py`
-   **Base Class:** `google.adk.agents.Agent`
-   **Core Logic:**
    -   **Gemini Model:** Leverages a Gemini language model for natural language understanding and response generation.
    -   **System Prompt/Instructions:** Pre-defined instructions guide the Gemini model's behavior and define the agent's persona and capabilities.
    -   **Calendar Tools Integration:**
        -   **Location:** `app/jarvis/tools/`
        -   Provides the agent with the necessary functions to interact with Google Calendar (e.g., create event, list events, find available slots). These tools are exposed to the Gemini model, allowing it to invoke them based on user requests.

## Request Flow

The typical flow of a user interaction is as follows:

1.  **User Input:** The user speaks a command into their microphone.
2.  **Frontend Captures Audio:** The JavaScript in `app/static/` captures the audio.
3.  **WebSocket Transmission:** The captured audio data is sent to the backend (`app/main.py`) via a WebSocket connection.
4.  **ADK Processing:**
    -   The `LiveRequestQueue` receives the incoming audio request.
    -   The `Runner` picks up the request and forwards it to the Jarvis agent.
5.  **Jarvis Agent Logic (`app/jarvis/agent.py`):**
    -   The audio is transcribed to text (details of STT not specified but implied).
    -   The transcribed text is processed by the Gemini model, guided by the system prompt and available calendar tools.
    -   If the user's intent requires a calendar operation, the agent invokes the appropriate tool from `app/jarvis/tools/`.
    -   The Gemini model generates a textual response based on the outcome of the tool execution or direct query.
6.  **TTS Conversion:** The agent's textual response is converted into audio using a Text-to-Speech service.
7.  **Response to Frontend:** Both the textual response and the generated audio are sent back to the frontend via the WebSocket connection.
8.  **Client Playback/Display:**
    -   The frontend JavaScript plays the received audio response for the user.
    -   The textual response or transcription may also be displayed on the web page.
