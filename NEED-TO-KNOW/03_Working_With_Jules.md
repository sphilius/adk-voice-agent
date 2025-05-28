# Best Practices for Collaborating with Jules (The AI Coding Agent)

Jules is a powerful AI coding assistant designed to help you with software development tasks. To maximize productivity and ensure a smooth collaboration, especially considering the user's AuDHD profile and the nature of AI, follow these best practices.

## 1. The Importance of Clear, Specific Instructions

AI agents, including Jules, operate best with explicit and unambiguous instructions. This is also highly beneficial for users with AuDHD, as it reduces cognitive load and potential for misinterpretation.

*   **Why it's crucial:**
    *   **AI Interpretation:** Jules doesn't "understand" in a human sense; it processes language based on patterns. Vague instructions lead to ambiguous or incorrect outputs.
    *   **AuDHD Clarity:** Clear instructions reduce anxiety and the mental effort needed to decipher expectations.
    *   **Efficiency:** Specific prompts get you closer to the desired output faster, minimizing iterations.

*   **Examples:**

    *   **Less Effective:** "Jules, fix the calendar bug."
        *   *Problem:* Which bug? In which file? What is the expected behavior?
    *   **Good:** "Jules, there's a bug in `app/jarvis/tools/calendar_tools.py` in the `create_event` function. When an event is created without an end time, it defaults to the current time instead of one hour after the start time. Please modify the function to ensure a default duration of 1 hour if no end time is provided."

    *   **Less Effective:** "Jules, add some voice commands."
        *   *Problem:* What kind of commands? For what feature? How should they be structured?
    *   **Good:** "Jules, for the `adk-voice-agent`, I want to add voice commands for managing a to-do list. Create a new Python file `app/jarvis/tools/todo_list_tools.py`. Define three functions: `add_task(task_description: str)`, `view_tasks()`, and `complete_task(task_id: int)`. For now, these functions can just print to the console. Remember to add appropriate docstrings and type hints."

    *   **Less Effective:** "Jules, make the UI better."
    *   **Good:** "Jules, in `app/static/index.html`, I want to change the background color of the `div` with id `response_area` to light grey (`#f0f0f0`) and increase the font size of the text within it to 16px."

## 2. Breaking Down Large Tasks

Decomposing complex tasks into smaller, manageable sub-tasks is a cornerstone of effective project management and a key strategy for working with AI.

*   **Why it's crucial:**
    *   **AI Focus:** Jules can handle smaller, well-defined tasks more accurately.
    *   **AuDHD Management:** Reduces overwhelm, makes tasks feel less daunting, and provides more frequent senses of accomplishment.
    *   **Easier Review:** Smaller changes are easier to review and debug.
    *   **Step-by-Step Progress:** Aligns well with iterative development.

*   **Example:** Instead of "Jules, integrate a new weather API into the voice agent."

    1.  "Jules, research and identify 2-3 free weather APIs that provide current temperature and forecast by city name. List their pros and cons, and provide example API call URLs."
    2.  "Jules, based on the research, let's use the OpenWeatherMap API. Please draft a Python function in a new file `app/jarvis/tools/weather_tool.py` called `get_weather(city: str)` that fetches and returns the current temperature for a given city using this API. Include error handling for API request failures or invalid city names."
    3.  "Jules, now update `app/jarvis/agent.py` to include `get_weather` as a tool for the Gemini model. Ensure the agent's system prompt is updated to inform the model about this new capability."
    4.  "Jules, write a unit test for the `get_weather` function in `app/jarvis/tools/weather_tool.py`, mocking the API call."

## 3. Providing Context

Jules doesn't have memory of past interactions beyond the current session's context window (and even that can be limited). Providing relevant context is essential.

*   **Why it's crucial:**
    *   **AI Accuracy:** The more relevant information Jules has, the better its suggestions and code modifications will be.
    *   **Efficiency:** Avoids Jules asking for information you already know or making incorrect assumptions.

*   **What to provide:**
    *   **Relevant Files:** "Jules, I'm working on `app/main.py` and `app/jarvis/agent.py`." Use `read_files()` to show Jules the current state if needed.
    *   **Specific Functions/Classes:** "In the `JarvisAgent` class within `app/jarvis/agent.py`, I want to modify the `process_input` method."
    *   **Existing Behavior:** "Currently, the agent responds with 'Hello'. I want it to respond with 'Hello, how can I help?'"
    *   **Error Messages:** "Jules, I'm getting this error when running `app/main.py`: `TypeError: ...`. Here's the relevant code snippet from `calendar_tools.py`: ..."
    *   **Goal/Desired Outcome:** "The goal is to allow users to specify a color when creating a calendar event."

## 4. Iterative Development & Feedback

Treat working with Jules as a collaborative, iterative process. Start with small changes, review them, and provide specific feedback.

*   **Why it's crucial:**
    *   **Course Correction:** Catch misunderstandings or errors early.
    *   **Refinement:** AI-generated code is often a good starting point but may need refinement.
    *   **AuDHD Pacing:** Iteration allows for natural breaks and prevents marathon coding sessions that can lead to burnout.

*   **Process:**
    1.  **Assign a small task.**
    2.  **Jules provides a solution (e.g., code, explanation).**
    3.  **Review the solution carefully.** Does it work? Is it clean? Does it meet all requirements?
    4.  **Provide specific feedback:**
        *   "Jules, that's a good start. However, you forgot to handle the case where the `event_date` is in the past. Please add a check for that in `create_event`."
        *   "Jules, the function `get_user_profile` works, but can you refactor it to use a dictionary comprehension for better readability?"
        *   "Jules, this test for `add_task` is good, but please also add a test case for when the task description is empty."

## 5. Leveraging Jules's Strengths

Understand what Jules excels at to use it most effectively.

*   **Boilerplate Code:** Generating repetitive code structures, classes, functions based on a template.
    *   *"Jules, create a new FastAPI endpoint in `app/main.py` at `/api/v1/status` that returns `{'status': 'ok'}`."*
*   **Research & Information Gathering:** Finding information on APIs, libraries, algorithms (using `view_text_website` if available, or its internal knowledge).
    *   *"Jules, what are common Python libraries for parsing CSV files?"*
*   **Initial Drafts:** Creating a first pass of a function, class, or even a document.
    *   *"Jules, draft a Python function that takes a list of numbers and returns the average."*
*   **Debugging (with context):** Analyzing error messages and code snippets to suggest potential fixes.
    *   *"Jules, I'm getting a `KeyError: 'user_id'` with this Python code: `data = request.json; print(data['user_id'])`. What could be wrong?"*
*   **Refactoring (with specific instructions):** Improving code structure, readability, or performance based on clear rules.
    *   *"Jules, refactor this `for` loop in `process_data.py` to use a list comprehension: `results = []; for x in data: if x > 10: results.append(x*2)`."*
*   **Writing Tests:** Generating unit tests for existing functions, especially if the logic is clear.
    *   *"Jules, write a pytest unit test for the function `calculate_sum(a, b)` in `math_utils.py`."*

## 6. Understanding Jules's Limitations

It's equally important to understand what Jules *cannot* do or where it struggles.

*   **No True "Understanding":** Jules matches patterns, it doesn't comprehend code or concepts like a human. It can make logical leaps that seem nonsensical.
*   **Creativity & High-Level Design:** The user must drive the overall architecture, complex problem-solving, and novel ideas. Jules can help implement, but not invent.
*   **No Direct Environment Interaction (outside the repo):** Jules cannot directly run your code, access your local databases, or interact with external services unless explicitly done through tools like `run_in_bash_session`. Its actions are confined to the provided tools and the repository files.
*   **Context Window:** Jules can only "remember" a certain amount of information from the current interaction. For very long files or complex histories, you may need to re-provide context.
*   **Subtlety and Nuance:** Complex human requirements or implied meanings can be lost. Be explicit.
*   **Security Implications:** Always review code generated by Jules, especially if it involves sensitive data, authentication, or external libraries. Jules doesn't have a security expert's mindset.

## 7. Specific Advice for the `sphilius/adk-voice-agent` Repository

*   **Agent Instructions (`app/jarvis/agent.py` - system prompt):**
    *   When adding new tools or capabilities, clearly explain them in the agent's system prompt. Jules can help you draft this language.
    *   *"Jules, I've added a `get_weather(city: str)` tool. Help me update the system prompt in `app/jarvis/agent.py` to inform the Gemini model that it can now provide weather information and should ask for the city if not provided."*
*   **Tool Definitions (`app/jarvis/tools/`):**
    *   Ensure new tools have clear function signatures, type hints, and comprehensive docstrings. Jules needs these to understand how to use the tool and generate the necessary descriptions for the Gemini model.
    *   *"Jules, review the docstring for my new `create_calendar_event_with_color` function. Ensure it clearly explains the `color_id` parameter and its expected values according to Google Calendar API."*
*   **Testing:**
    *   The ADK and agent logic can be complex. Request tests for both individual tools and the agent's ability to correctly select and use tools based on prompts.
    *   *"Jules, write a test for the Jarvis agent that checks if it correctly calls the `create_event` tool when I say 'Create a meeting for tomorrow at 2 PM'."* (This might be more conceptual, guiding you to write such a test).
*   **Frontend-Backend Interaction (`app/main.py`, `app/static/`):**
    *   Be specific about whether changes affect the frontend (JavaScript, HTML) or backend (Python, FastAPI).

## 8. Tips for Aligning with User's AuDHD Preferences

These tips are designed to make the collaboration with Jules more comfortable and effective for a user with AuDHD:

*   **Request Step-by-Step Plans:** Before Jules generates a large chunk of code, ask it to outline its approach.
    *   *"Jules, before you write the code to add color-coding to calendar events, please provide a step-by-step plan of which files you'll modify and what changes you'll make in each."*
*   **Ask for Explanations of Jargon or Complex Code:** If Jules uses a term or generates code that isn't immediately clear, ask for clarification.
    *   *"Jules, you mentioned using a 'decorator' here. Can you explain what that means in this context and why it's useful?"*
    *   *"Jules, can you explain this line of Python code you wrote: `return {**base_event, **color_params}`?"*
*   **Request Concrete Examples:** If a concept is abstract, ask for an example.
    *   *"Jules, you suggested refactoring using a 'strategy pattern'. Can you show me a small, concrete example of how that would look in Python for, say, different ways of calculating a price?"*
*   **Use Jules for Organization:**
    *   *"Jules, I have these ideas for features: X, Y, Z. Can you help me structure them into a list with brief descriptions for a markdown file?"*
*   **Set Clear Boundaries for Tasks:** Define the scope of what Jules should do in a given turn to keep interactions focused.

## 9. Example Workflow: Adding Color-Coding to Calendar Events

Let's illustrate a collaborative task, incorporating many of the above principles. The user wants to add a feature where they can specify a color when creating a calendar event via voice.

**User:** "Jules, I want to add a feature to the voice agent to allow specifying a color when creating a Google Calendar event. Can you outline a plan for this?"

**Jules (should provide a plan):**
"Okay! Here's a possible plan:
1.  **Research:** Identify how to specify event colors using the Google Calendar API Python client library. Specifically, what parameter and format are needed.
2.  **Modify Tool:** Update the `create_event` function in `app/jarvis/tools/calendar_tools.py`.
    *   Add a new optional parameter (e.g., `color_id: str | None = None`) to the function signature.
    *   If a `color_id` is provided, include it in the `body` of the event passed to the Google Calendar API service.
    *   Update the docstring to reflect the new parameter and its usage.
3.  **Update Agent:**
    *   Modify the system prompt in `app/jarvis/agent.py` to inform the Gemini model about the new color capability (e.g., "You can now specify a color when creating an event. Google Calendar supports specific color IDs. If the user mentions a color, try to map it to a valid ID or ask for clarification.").
    *   Ensure the function signature for `create_event` available to the Gemini model is updated (this might be automatic if using certain ADK features or require manual updates in the agent's tool registration).
4.  **Testing (Optional but Recommended):**
    *   Add a unit test for `create_event` in `calendar_tools.py` to verify that the `color_id` is correctly passed to the API call mock.
    *   Manually test the voice agent with commands like 'Create a dentist appointment for tomorrow at 3 PM, make it red.'"

**User:** "That plan looks good. Let's start with step 1. Jules, can you research how to specify event colors with the Google Calendar API Python client and tell me the parameter name and format?"

**Jules (after potential internal search or using `view_text_website` if it had access to a relevant doc page):**
"Okay, I've looked into it. When creating or updating an event using the Google Calendar API Python client, you can specify the color using the `colorId` field within the event resource body. The `colorId` is a string representing a number from '1' to '11', each corresponding to a predefined color in Google Calendar.

For example, to set an event to a specific color (e.g., 'Tomato', which is ID '11'), the relevant part of the event body would look like this:
```python
event = {
    'summary': 'My Event',
    'start': {'dateTime': '2024-08-15T10:00:00', 'timeZone': 'America/Los_Angeles'},
    'end': {'dateTime': '2024-08-15T11:00:00', 'timeZone': 'America/Los_Angeles'},
    'colorId': '11'
}
```
You can find the list of available `colorId` values and their corresponding colors in the Google Calendar API documentation."

**User:** "Great. Now for step 2. Please show me the `create_event` function from `app/jarvis/tools/calendar_tools.py`."

**Jules (using `read_files`):**
