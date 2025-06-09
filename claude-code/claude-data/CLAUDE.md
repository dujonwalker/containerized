# Claude Code Docker Environment Configuration

## Environment Overview

You are running inside a Docker/Podman container with **LIMITED SYSTEM ACCESS**. This is a **FILE-EDITING ENVIRONMENT ONLY** - you're helping create and modify files that will be executed elsewhere.

### File System Access

- **WORKSPACE ONLY**: You can only access files in `/workspace` (mapped to `~/claude-code/workspace` on host)
- **NO HOST ACCESS**: You cannot access files outside the workspace directory
- **PERSISTENT STORAGE**: Your Claude credentials and history are stored in `/root/.claude`

### System Limitations

**DO NOT ATTEMPT TO:**
- Install packages or dependencies (npm install, pip install, apt install, etc.)
- Run application servers or start services (npm run dev, python -m http.server, etc.)
- Access files outside `/workspace`
- Execute commands that require root privileges beyond the container
- Run long-running services, servers, or daemons
- Access host environment variables or system resources
- Install global packages or modify the container environment permanently
- Use GitHub CLI for authenticated operations (gh auth login, creating PRs, accessing private repos, etc.) - GitHub CLI is NOT authenticated and should only be used for public repository read operations
- **Use docker commands** (docker exec, docker run, docker ps, etc.) - You have no access to Docker daemon or other containers

**HOWEVER: Feel free to suggest Docker commands for the USER to run** - The user CAN execute Docker commands on the host system and report results back to help with troubleshooting. Examples:
- "Can you run `docker logs container_name` and share the output?"
- "Please try `docker exec container_name command` and let me know what happens"
- "Run `docker ps` to see if the container is running"

**YOU CAN:**
- Read, write, and modify files within `/workspace`
- Use all standard Unix tools for file inspection (cat, less, head, tail, grep, awk, sed)
- Navigate and analyze directory structures (ls, find, tree, du, wc)
- Use git commands for version control (git status, git add, git commit, etc.)
- Use Python 3 for file processing and analysis (python3 script.py for workspace analysis)
- Use Node.js tools for code analysis and file processing (node script.js for workspace tasks)
- Use curl for downloading files or checking URLs (if needed for project setup)
- Use build tools (make, gcc, etc.) for compilation tasks that don't require installation
- Create new files and directories in the workspace
- Use text processing tools to understand and analyze code
- Use all modern file analysis tools (rg, fd, bat, jq, yq, tree, awk, diff)
- Use JSON tools for safe manipulation (jq for parsing, jsonlint for validation, prettier for formatting)
- Use text processing tools for complex transformations (awk, sed, grep)
- Edit files using absolute paths like `/workspace/project/file.txt`
- Create documentation, code, and configuration files

### Development Workflow

1. **All work happens in `/workspace`**
2. **Focus on file creation and editing** - you're preparing code to run elsewhere
3. **Each command runs independently** - no shell state persistence
4. **Use absolute paths** when referencing workspace files: `/workspace/my-project/file.txt`
5. **Git operations** for version control are the main commands you'll use
6. **No execution or testing** - files will be run in their target environment

### Docker Compose Best Practices

**Modern Docker Compose Guidelines:**

```yaml
# ✅ CORRECT - Modern docker-compose.yml format
# NO version field needed (auto-detected)
services:
  app:
    build: .
    ports:
      - "3000:3000"

# ❌ OUTDATED - Don't include version field
version: "3.8"  # Remove this line
services:
  app:
    build: .
```

**Command Usage:**
- ✅ **Use**: `docker compose` (modern Docker CLI)
- ❌ **Avoid**: `docker-compose` (legacy standalone tool)

**Examples:**
```bash
# ✅ Modern commands
docker compose build
docker compose up -d
docker compose down
docker compose logs service_name

# ❌ Legacy commands (avoid)
docker-compose build
docker-compose up -d
```

- **THIS IS A FILE-EDITING ENVIRONMENT ONLY** - code will be executed elsewhere
- **Use standard Unix tools freely** for file inspection and analysis
- **NEVER use Edit tool directly on JSON files** - always use jq for safe JSON manipulation
- **Always backup JSON files before modification** - JSON corruption is easy and dangerous
- **Use the JSON safety workflow** - extract, edit text separately, merge with jq, validate
- **MANDATORY: Always clean up temp files automatically** - Never ask user to remind you, never leave temp files behind
- **Feel free to use curl/ping for network debugging** - Test APIs, check connectivity, diagnose protocol issues
- **You cannot run Docker commands, but the USER can** - Suggest Docker commands for the user to run and report back when troubleshooting
- When suggesting commands, focus on file manipulation, analysis, and git operations
- Do not attempt to install dependencies, run application code, or test applications
- If asked about running/testing code, explain it should be done in the target environment
- Always work within the `/workspace` directory context
- Focus on creating, editing, analyzing, and organizing files

### JSON File Safety Guidelines

**CRITICAL: When editing JSON files, ALWAYS use jq for safe manipulation:**

```bash
# ❌ DANGEROUS - Direct editing of JSON files with Edit tool
# This can corrupt JSON structure, especially with newlines and quotes

# ✅ SAFE - Use jq for JSON modifications
# Extract field, edit separately, then use jq to safely insert back
jq '.nodes[0].parameters.options.systemMessage' file.json > temp_message.txt
# Edit temp_message.txt with Edit tool
# Then safely replace using jq:
jq --rawfile newmsg temp_message.txt '.nodes[0].parameters.options.systemMessage = $newmsg' file.json > file_new.json

# ✅ SAFE - Always validate JSON after any changes
jsonlint filename.json
# or
python3 -m json.tool filename.json > /dev/null && echo "Valid JSON" || echo "INVALID JSON"

# ✅ SAFE - Use jq for simple field updates
jq '.field = "new value"' file.json > file_new.json

# ✅ SAFE - Create backups before JSON edits
cp original.json original.json.backup
```

**JSON Editing Workflow:**
1. **Always backup first**: `cp file.json file.json.backup`
2. **Extract complex text fields** with jq before editing
3. **Use Edit tool only on extracted text**, not raw JSON
4. **Use jq to safely merge changes back**
5. **Validate immediately**: `jsonlint file.json`
6. **AUTOMATICALLY clean up temp files**: `rm temp_*.txt *.backup` (NEVER ask user to remind you)
7. **If validation fails**: Restore backup and try again

### Cleanup Requirements

**MANDATORY: Always automatically clean up temporary files without being asked:**

```bash
# ✅ REQUIRED - Automatic cleanup after ANY workflow that creates temp files
rm temp_message.txt original.json.backup temp_*.txt *.backup

# ✅ REQUIRED - Clean up after JSON workflows
rm /workspace/temp_*.txt /workspace/*.backup

# ✅ REQUIRED - No exceptions - NEVER leave temp files behind
# This should happen automatically at the end of every task that creates temporary files
```

**You MUST clean up temporary files immediately after completing any task - do not wait for the user to remind you.**

### Example Valid Commands

```bash
# ✅ Good - modern file operations and analysis
rg "function.*export" /workspace/src --type js
fd "package.json" /workspace
fzf  # interactive file finder
bat /workspace/src/app.js
exa -la /workspace  # better ls with git status
tree /workspace -I "node_modules|.git"
dust /workspace  # disk usage analysis
ncdu /workspace  # interactive disk usage

# ✅ Good - SAFE JSON manipulation and validation
# ALWAYS backup JSON files before editing
cp /workspace/config.json /workspace/config.json.backup

# Extract complex fields for editing
jq '.nodes[0].parameters.options.systemMessage' /workspace/workflow.json > /workspace/temp_message.txt
# Edit temp_message.txt with Edit tool, then safely merge back:
jq --rawfile newmsg /workspace/temp_message.txt '.nodes[0].parameters.options.systemMessage = $newmsg' /workspace/workflow.json > /workspace/workflow_new.json

# Simple JSON field updates
jq '.version = "2.0"' /workspace/package.json > /workspace/package_new.json

# ALWAYS validate after JSON changes
jsonlint /workspace/config.json
python3 -m json.tool /workspace/config.json > /dev/null && echo "Valid JSON" || echo "INVALID JSON"

# Pretty-print and format JSON
jq '.' /workspace/config.json > /workspace/config_formatted.json
prettier --write /workspace/data.json

# ✅ Good - text processing and comparison
awk '/pattern/ {print $2}' /workspace/logfile.txt
diff /workspace/old.txt /workspace/new.txt
delta /workspace/old.txt /workspace/new.txt  # enhanced diff
sed 's/old/new/g' /workspace/file.txt

# ✅ Good - git operations with enhancements
git status
git diff  # or use delta for better output
git add .
git commit -m "Update files"

# ✅ Good - GitHub CLI (public repos only)
gh repo view owner/repo
gh issue list --repo owner/repo
gh release list --repo owner/repo

# ✅ Good - file monitoring and system info
watch -n 2 'ls -la /workspace'
entr -s 'echo File changed' <<< /workspace/file.txt
htop  # process viewer

# ✅ Good - YAML processing
yq '.services.web.ports' /workspace/docker-compose.yml

# ✅ Good - network testing and debugging
curl http://192.168.50.196:1111/api/v1/notes
curl https://192.168.50.196:1111/api/v1/notes
curl -v http://api.example.com/status  # verbose output for debugging
ping google.com
curl -I https://github.com  # check headers only
# ✅ Good - standard operations
cat /workspace/project/package.json
find /workspace -name "*.js" -type f
grep -r "function" /workspace/src
wc -l /workspace/**/*.py
head -20 /workspace/README.md
touch /workspace/new-file.txt
mkdir /workspace/new-directory

# ✅ Good - using installed tools for workspace analysis
python3 -c "import json; print(json.load(open('/workspace/package.json'))['name'])"
node -e "console.log(require('/workspace/package.json').version)"
curl -s https://api.github.com/repos/user/repo > /workspace/repo-info.json
```

**Note**: Each command runs independently - you cannot chain commands or maintain shell state between executions.

### Example Invalid Commands

```bash
# ❌ EXTREMELY DANGEROUS - Direct JSON file editing with Edit tool
# This corrupts JSON files, especially with newlines, quotes, and large text blocks
# Edit tool should NEVER be used directly on JSON files

# ❌ Bad - Docker operations (no access to Docker daemon)
docker exec container_name command
docker ps
docker run image_name
docker logs container_name

# ❌ Bad - installing packages
npm install express
pip install requests
apt install tree

# ❌ Bad - running application servers
npm run dev
python -m http.server 8000
node server.js

# ❌ Bad - GitHub CLI authentication operations
gh auth login
gh pr create
gh repo create
gh issue create

# ❌ Bad - commands requiring shell state persistence
cd /workspace/my-project && ls  # cd doesn't persist
source venv/bin/activate  # activation doesn't persist
export MY_VAR=value  # environment variables don't persist
```

## Quick Reference

- **Working Directory**: `/workspace`
- **Available Tools**: curl, git, python3, pip (for analysis only), node, npm (for analysis only), build-essential tools
- **Search Tools**: rg (ripgrep), fd, fzf (fuzzy finder), grep, find
- **File Viewers**: bat (syntax highlighting), exa (better ls), cat, less, head, tail
- **Data Tools**: jq (JSON), yq (YAML), tree (directory structure), dust (disk usage), ncdu (interactive disk usage)
- **Text Processing**: awk, sed, grep, diff, delta (better git diff)
- **Code Tools**: prettier (formatting), jsonlint (JSON validation)
- **Git Tools**: git, gh (GitHub CLI - read-only public repos), delta (enhanced diffs)
- **System Tools**: htop (processes), watch (repeat commands), entr (file watching)
- **File Access**: Workspace files only
- **Purpose**: File editing and analysis - not application execution
- **System Commands**: Use freely for file operations and analysis