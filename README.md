#!/bin/bash

set -e  # Exit on error

# Check for .env with very conspicuous warning
if [ ! -f ".env" ]; then
    echo ""
    echo "⚠️  ========================= WARNING ========================= ⚠️"
    echo "⚠️                   .ENV FILE NOT FOUND!                      ⚠️"
    echo "⚠️  ========================================================== ⚠️"
    echo ""

    if [ -f "example.env" ]; then
        echo "📄 Found example.env, creating .env from it..."
        cp example.env .env
        echo "✅ .env file created."
    else
        echo "❌ ========================================================== ❌"
        echo "❌  CRITICAL ERROR: example.env not found.                    ❌"
        echo "❌  Cannot continue without .env file.                        ❌"
        echo "❌ ========================================================== ❌"
        exit 1
    fi

    echo ""
    echo "⚠️  Please review your .env file before continuing!           ⚠️"
    echo "⚠️  Press Enter to continue or Ctrl+C to abort...             ⚠️"
    read
fi

# Install pip if not installed
if ! command -v pip &> /dev/null; then
    echo "Installing pip..."
    sudo apt-get update
    sudo apt-get install -y python3-pip
fi

# Install pipenv if not installed
if ! command -v pipenv &> /dev/null; then
    echo "Installing pipenv..."
    pip install --user pipenv
    export PATH="$HOME/.local/bin:$PATH"
fi

# Add other dependencies from requirements.txt if it exists
if [ -f "requirements.txt" ]; then
    echo "Installing requirements.txt..."
    pipenv run pip install -r requirements.txt
fi

# Run the FastAPI server in the background
echo "🚀 Starting FastAPI server using 'fastapi run'..."
nohup pipenv run fastapi run --host 0.0.0.0 --port 8000 > fastapi.log 2>&1 &

# Wait for FastAPI to initialize
sleep 2

# Start PHP Server
echo "🌐 Starting PHP server on http://localhost:5000 ..."
cd frontend
php -S localhost:5000






now in your request.php change the base usl to local host 8000