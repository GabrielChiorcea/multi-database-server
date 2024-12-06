# multi-database-server

# .github/workflows/trigger-server.yml
name: Trigger Server on Push

on:
  push:
    branches:
      - main

jobs:
  trigger-server:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Trigger production server
      run: curl -X POST -H "Content-Type: application/json" -d '{"ref": "${{ github.ref }}"}' https://your-production-server-endpoint/github-webhook


from flask import Flask, request
import os
import subprocess

app = Flask(__name__)

REPO_URL = "https://github.com/GabrielChiorcea/multi-database-server.git"
CLONE_DIR = "/path/to/clone/directory"

@app.route('/github-webhook', methods=['POST'])
def github_webhook():
    data = request.json
    # Check if the push event is from the main branch
    if data['ref'] == 'refs/heads/main':
        if not os.path.exists(CLONE_DIR):
            # Clone the repository if the directory does not exist
            subprocess.run(["git", "clone", REPO_URL, CLONE_DIR])
        else:
            # Pull the latest changes if the directory exists
            subprocess.run(["git", "-C", CLONE_DIR, "pull"])
    return '', 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
