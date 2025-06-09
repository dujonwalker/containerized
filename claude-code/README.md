# Claude Code in Docker/Podman

This repository provides a ready-to-use Docker and Podman setup for running the Claude Code CLI in a reproducible, persistent Linux environment with a comprehensive set of modern development tools.

## Features

- **Ubuntu 24.04** base image
- **Latest Claude Code CLI** automatically installed
- **Node.js 22.x** and npm from NodeSource for compatibility
- **Python and build tools** included for development
- **Comprehensive developer toolkit** with modern alternatives to standard Unix tools
- **Persistent storage** for Claude credentials and history
- **Works with both** Docker Compose and Podman Compose

## Included Tools

### Search & Navigation
- **ripgrep (rg)** - Lightning-fast recursive search, much better than grep
- **fd** - Fast alternative to find with intuitive syntax
- **fzf** - Interactive fuzzy finder for files and commands
- **tree** - Directory structure visualization

### File Viewing & Processing
- **bat** - Syntax-highlighted file viewer (better cat)
- **exa** - Modern ls replacement with git integration and colors
- **jq** - JSON parsing, filtering, and transformation
- **yq** - YAML/XML processor (like jq but for YAML)

### Development Tools
- **delta** - Enhanced git diff viewer with syntax highlighting
- **gh** - GitHub CLI for repository operations (read-only public repos)
- **prettier** - Code formatting
- **jsonlint** - JSON validation

### System & Monitoring
- **dust** - Disk usage analyzer with visual output
- **ncdu** - Interactive disk usage explorer
- **htop** - Better process viewer than top
- **entr** - Run commands when files change
- **watch** - Execute commands repeatedly and show output

### Text Processing
- **awk/gawk** - Advanced text processing
- **diffutils** - File comparison tools

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/dujonwalker/containerized.git
cd containerized/claude-code
```

### 2. Build the Container

```bash
# With Docker
docker compose build

# With Podman
podman compose build
```

### 3. Start the Service

```bash
# With Docker
docker compose up

# With Podman
podman compose up
```

### 4. Login to Claude Code

Open a shell in the running container:

```bash
# With Docker
docker compose exec claude-code bash

# With Podman
podman exec -it claude-code /bin/bash
```

Then, inside the container, log in:

```bash
claude login
```

Follow the CLI/browser prompts to authenticate.

## Environment Configuration

### CLAUDE.md File

This repository includes a pre-configured `CLAUDE.md` file that instructs Claude Code about the container environment and available tools. The file is automatically mounted and informs Claude about:

- Available command-line tools and their purposes
- Container limitations (no package installation, workspace-only access)
- GitHub CLI limitations (read-only public repositories)
- File editing workflow best practices

### Tool Capabilities

Claude Code can effectively use all included tools for:
- **Code analysis** with ripgrep, fd, and fzf
- **File inspection** with bat, exa, and tree
- **Data processing** with jq, yq, and awk
- **Git operations** with enhanced diff viewing via delta
- **Project exploration** with dust, ncdu, and interactive tools
- **File monitoring** with entr and watch

## Persistence

- **Credentials and history** are stored in a persistent Docker volume (`claude_data`), mapped to `/root/.claude` in the container
- **Project files** are mounted from your current directory to `/workspace` in the container
- **CLAUDE.md configuration** is mounted to provide consistent environment guidance

## Usage Examples

### Interactive File Navigation
```bash
# Use fzf for interactive file selection
fzf

# Better directory listing with git status
exa -la /workspace

# Interactive disk usage exploration
ncdu /workspace
```

### Enhanced Code Analysis
```bash
# Fast recursive search with syntax highlighting
rg "function.*export" /workspace/src --type js

# Find files with modern syntax
fd "package.json" /workspace

# View files with syntax highlighting
bat /workspace/src/app.js
```

### JSON/YAML Processing
```bash
# Parse and filter JSON
jq '.scripts' /workspace/package.json

# Process YAML files
yq '.services.web.ports' /workspace/docker-compose.yml

# Validate JSON syntax
jsonlint /workspace/config.json
```

### GitHub Operations (Public Repos)
```bash
# View public repository information
gh repo view owner/repo

# List public issues
gh issue list --repo owner/repo

# Check releases
gh release list --repo owner/repo
```

## Checking Login Status

To verify if you're logged in:

```bash
# With Docker
docker compose exec claude-code claude status

# With Podman
podman exec -it claude-code claude status
```

You can also inspect the credentials file:

```bash
# With Docker
docker compose exec claude-code cat /root/.claude/.credentials.json

# With Podman
podman exec -it claude-code cat /root/.claude/.credentials.json
```

## Customization

- **Custom host directory**: To use a specific host directory for credentials/history, edit the `volumes` section in `docker-compose.yml`
- **Additional tools**: To install additional tools, modify the `Dockerfile`
- **Environment configuration**: Modify `CLAUDE.md` to adjust Claude's behavior and tool usage

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `node: No such file or directory` | Ensure you've built the image with the provided Dockerfile |
| Login does not persist | Check that the `claude_data` volume is present and mounted correctly |
| Tool not found errors | Rebuild the container to ensure all tools are installed |
| GitHub CLI authentication errors | This is expected - GitHub CLI is configured for read-only public repository access only |

## Container Limitations

This container is designed as a **file-editing environment**:
- ✅ **Can**: Read, write, analyze files; use all included developer tools; perform git operations
- ❌ **Cannot**: Install packages, run application servers, access files outside `/workspace`, authenticate with GitHub

## License

MIT (or your preferred license)

## Credits

- [Anthropic Claude Code](https://www.anthropic.com)
- [Node.js](https://nodejs.org)
- [Docker](https://www.docker.com)
- [Podman](https://podman.io)
- All the amazing open-source tool creators: ripgrep, fd, fzf, bat, exa, delta, jq, yq, and many others

---

**Happy coding with Claude and a full modern developer toolkit!** 🚀
