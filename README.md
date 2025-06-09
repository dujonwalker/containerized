# Containerized

A collection of Docker configurations for running mainstream Linux applications on NixOS (and other systems). This repository contains Dockerfiles and docker-compose configurations that make it easy to run applications in isolated, reproducible environments.

## 🎯 Purpose

As a NixOS user, I often encounter situations where:
- Applications have outdated versions in nixpkgs
- Upstream projects primarily target Ubuntu/mainstream distros
- I prefer the convenience and familiarity of containers for certain applications
- Containerization extends the Nix philosophy of isolated, reproducible environments

This repository serves as my personal collection of containerized applications with proven configurations that work reliably.

## 📦 What's Included

Each application directory contains:
- **Dockerfile**: Container definition optimized for the specific application
- **docker-compose.yml**: Complete deployment configuration with volumes, networks, and environment variables
- **README.md**: Application-specific setup instructions and configuration notes
- **Additional configs**: Any required configuration files or scripts

## 🚀 Usage

### Quick Start
```bash
# Clone the repository
git clone https://github.com/dujonwalker/containerized.git
cd containerized

# Navigate to the application you want to run
cd [application-name]

# Review and customize the configuration
nano docker-compose.yml

# Deploy
docker compose up -d
```

### General Workflow
1. **Browse available applications** in the repository
2. **Copy the relevant directory** to your preferred location
3. **Review configuration files** and adjust paths, ports, and environment variables
4. **Deploy using docker compose**
5. **Customize as needed** for your specific setup

## 🛠️ Applications

Applications are organized by category and include both self-hosted services and development tools that benefit from containerization on NixOS.

### Development Tools
- **[claude-code](claude-code/)** - Claude Code CLI with comprehensive modern developer toolkit (ripgrep, fd, fzf, bat, exa, delta, and more) in Ubuntu 24.04 container

*[Additional applications will be added]*

## 📋 Configuration Notes

### Volume Paths
- All configurations use relative paths or clearly marked placeholder paths
- Review volume mappings before deployment to ensure they match your system layout
- Consider using Docker volumes for data that doesn't need host access

### Networking
- Most applications use custom Docker networks for isolation
- Default ports are documented in each application's README
- Adjust port mappings to avoid conflicts with your existing services

### Environment Variables
- Sensitive values use placeholder environment variables
- Create `.env` files for production deployments
- Never commit real credentials to the repository

## 🔧 Customization Tips

### General Best Practices
- Always review security implications before exposing services
- Use Docker secrets or external secret management for production
- Regular container updates and security scanning
- Backup persistent volumes before major updates

## 🤝 Contributing

While this is primarily my personal collection, contributions are welcome:

1. **Fork the repository**
2. **Add your application** following the existing directory structure
3. **Include comprehensive documentation** in the application's README
4. **Test thoroughly** before submitting
5. **Submit a pull request** with a clear description

### Application Guidelines
- Include both Dockerfile and docker-compose.yml
- Provide clear setup instructions
- Document any special requirements or gotchas
- Use security best practices (non-root users, minimal attack surface)
- Include version pinning for reproducible builds

## 📝 Notes

- **Configurations are opinionated** and reflect my specific use cases
- **Security settings** are configured for home lab environments, review for production use
- **Version pinning** is used where possible for reproducibility
- **Documentation** assumes familiarity with Docker and basic system administration

## 🔗 Related Projects

- [Project NOVA MCP Server Dockerfiles](https://github.com/dujonwalker/project-nova/tree/main/mcp-server-dockerfiles) - If you like containerized applications, you might also be interested in my collection of containerized Model Context Protocol (MCP) servers for AI agent integrations

## 📄 License

MIT License - feel free to use, modify, and distribute these configurations as needed.

---

*These configurations have been tested on NixOS but should work on any system with Docker and docker-compose installed.*
