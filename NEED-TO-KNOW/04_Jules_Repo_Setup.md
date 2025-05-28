# Setting Up the `sphilius/adk-voice-agent` Repository in Jules

This guide explains the setup process for the `sphilius/adk-voice-agent` repository within the jules.google.com environment, how to interpret the initial setup messages you saw, and the recommended steps for a robust and secure development environment using a custom setup script.

## 1. The jules.google.com Setup Process

When you first add a repository to Jules, it performs several actions to prepare the environment:

1.  **Repository Cloning:** Jules needs a local copy of your repository to work with. It uses Git to clone the repository into a dedicated workspace.
2.  **Initial Validation/Setup:** Jules may perform some basic checks or setup steps. The output you saw is part of this initial provisioning of the environment for your repository.

    *   `sudo mkdir /app`: This command creates a directory named `app` at the root of the filesystem within Jules's sandboxed environment. This is a common practice to provide a standard location for applications.
    *   `sudo chown 1001 /app`: This changes the ownership of the `/app` directory. The user ID `1001` likely corresponds to the non-root user account under which Jules and your code will run. This ensures the user has the necessary permissions to read, write, and execute files within `/app`.
    *   `git config --global user.email "you@example.com"` and `git config --global user.name "Your Name"`: These commands set up global Git configuration for your user within the Jules environment. This is important if Jules needs to perform any Git operations (like commits, though this is less common for automated agents and more for interactive environments).
    *   `git clone https://github.com/sphilius/adk-voice-agent /app/adk-voice-agent --progress`: This is the core command that copies your repository from GitHub into the `/app/adk-voice-agent` directory within Jules's environment. The `--progress` flag shows cloning progress.

## 2. What Your `echo do set up` Command Did (and Didn't Do)

When you entered `echo do set up` into the "Setup commands" field in Jules, you essentially told Jules to execute that command *after* it completed its own initial environment preparation (the `mkdir`, `chown`, `git clone` etc., as described above).

*   **What it did:** The `echo do set up` command simply printed the string "do set up" to the standard output. You would have seen this in the setup logs.
*   **What it didn't do:** It did *not* perform any actual setup tasks for your Python application (like creating a virtual environment, installing dependencies, or setting up environment variables). The command `echo` only outputs text.

This is why a more comprehensive setup script is necessary.

## 3. Recommended `jules_setup.sh` Script

To properly prepare the environment for the `adk-voice-agent`, you should use a setup script. Create a file named `jules_setup.sh` in the root of your `adk-voice-agent` repository with the following content:

```bash
#!/bin/bash
set -e # Exit immediately if a command exits with a non-zero status.

# 1. Navigate to the repository directory (if not already there)
#    Jules usually runs setup commands from the repo root, but cd is good practice.
echo "Navigating to the application directory: /app/adk-voice-agent"
cd /app/adk-voice-agent

# 2. Create and activate a Python virtual environment
echo "Creating Python virtual environment..."
python3 -m venv .venv
echo "Activating Python virtual environment..."
source .venv/bin/activate

# 3. Upgrade pip and install dependencies
echo "Upgrading pip..."
pip install --upgrade pip
echo "Installing dependencies from requirements.txt..."
pip install -r requirements.txt

# 4. Environment Variables & .env File
echo "Setting up environment variables..."
if [ -f ".env.example" ]; then
    echo "Found .env.example, copying to .env..."
    cp .env.example .env
    echo "IMPORTANT: .env file created from .env.example."
    echo "           You MUST configure necessary environment variables (like API keys)"
    echo "           using Jules's secret management features."
    echo "           Do NOT commit actual secrets to your .env file in the repository."
else
    echo "Warning: .env.example not found. You may need to create .env manually."
    echo "         Ensure you configure necessary environment variables using Jules's secret management."
fi

# 5. Google Calendar OAuth Setup (credentials.json & token.json)
echo ""
echo "-----------------------------------------------------------------------"
echo "Google Calendar OAuth Setup (credentials.json & token.json)"
echo "-----------------------------------------------------------------------"
echo "The 'setup_calendar_auth.py' script handles Google Calendar OAuth2."
echo "This script typically requires browser interaction for the initial authorization flow,"
echo "which is CHALLENGING in a headless environment like Jules."
echo ""
echo "Recommended Strategies:"
echo "  1. Local Machine First: Run 'setup_calendar_auth.py' on your local machine."
echo "     This will generate 'credentials.json' (if you don't have it from Google Cloud Console)"
echo "     and 'token.json' (containing your refresh token)."
echo "  2. Secure Storage of 'token.json':"
echo "     - OPTION A (Preferred for Jules): Upload the *contents* of 'token.json' as a secret in Jules."
echo "       Your application code will need to be adapted to read this secret from Jules's environment"
echo "       and reconstruct the 'token.json' file at runtime or use the credentials directly."
echo "     - OPTION B (If direct secret access is complex): Upload the 'token.json' file itself"
echo "       to a secure, private location accessible by Jules (e.g., a secure bucket, or using Jules's file upload features if available)."
echo "       Ensure this location is NOT your Git repository."
echo "  3. 'credentials.json':"
echo "     - This file contains your client ID and secret from Google Cloud Console."
echo "     - It can also be stored as a Jules secret (upload its content)."
echo "     - Alternatively, if your application is adapted, these values can be set as individual environment variables."
echo ""
echo "IMPORTANT: Add 'token.json' and potentially 'credentials.json' (if not managed solely by secrets)"
echo "           to your .gitignore file to prevent accidental commits."
echo "           The .venv directory and .env file (with actual secrets) should also be in .gitignore."
echo ""
echo "The application will look for 'credentials.json' and 'token.json' (or expect their contents via environment variables/secrets)"
echo "to interact with the Google Calendar API."
echo "-----------------------------------------------------------------------"

echo ""
echo "Setup script completed."
echo "Virtual environment '.venv' is ready and dependencies are installed."
echo "Remember to configure secrets for .env values and OAuth credentials."
```

## 4. Making `jules_setup.sh` Executable and Using It

1.  **Add the script to your repository:**
    *   Create the `jules_setup.sh` file in the root of your `adk-voice-agent` repository.
    *   Copy the content above into the file.
    *   Commit and push this script to your GitHub repository.
        ```bash
        git add jules_setup.sh
        git commit -m "Add jules_setup.sh for environment preparation"
        git push
        ```

2.  **Make it executable (locally, for Git to track permissions):**
    While Jules will execute it with `bash jules_setup.sh` (which doesn't strictly require the executable bit on the file itself), it's good practice for shell scripts.
    ```bash
    git update-index --chmod=+x jules_setup.sh
    ```
    Or, if your local system is Unix-based:
    ```bash
    chmod +x jules_setup.sh
    git add jules_setup.sh # If already added, Git will pick up the mode change
    git commit -m "Make jules_setup.sh executable"
    git push
    ```

3.  **Configure Jules to use the script:**
    *   Go to your repository settings in jules.google.com.
    *   Find the "Setup commands" or "Build commands" section.
    *   Replace `echo do set up` with:
        ```bash
        bash jules_setup.sh
        ```
    *   Save the changes. Jules will use this command for subsequent setups or when you trigger a re-build/re-setup.

## 5. Importance of `.gitignore`

To prevent sensitive information from being accidentally committed to your repository, ensure your `.gitignore` file (in the root of your repository) includes:

```gitignore
# Python virtual environment
.venv/
venv/
*/.venv/
*/venv/

# Environment variables file
.env
*.env

# Google OAuth files
token.json
credentials.json # If you choose to keep a copy locally outside of secrets, though not ideal.

# Python cache
__pycache__/
*.pyc
*.pyo
*.pyd

# IDE / Editor specific files
.vscode/
.idea/
*.swp
```

*   If `credentials.json` is absolutely necessary to be present as a file and you are not managing it solely via secrets, ensure you have a very robust reason for it. Generally, its contents (client ID, client secret) should be managed as individual secrets.
*   `token.json` is generated after a successful OAuth flow and contains sensitive refresh tokens. **It should never be committed to the repository.**

By following this guide, you will have a properly configured and secure environment for running the `sphilius/adk-voice-agent` in Jules. Remember to manage all secrets (API keys, OAuth tokens/credentials) through Jules's secret management system.
```
