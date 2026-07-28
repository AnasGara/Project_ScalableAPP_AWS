# Contributing to Scalable Web Application

Thank you for your interest in contributing to this project! This document provides guidelines and information for contributors.

## How to Contribute

### 1. Fork the Repository

1. Fork the repository on GitHub
2. Clone your fork locally
3. Create a new branch for your feature or fix

```bash
git clone https://github.com/YOUR_USERNAME/scalable-webapp-aws.git
cd scalable-webapp-aws
git checkout -b feature/your-feature-name
```

### 2. Make Changes

- Follow the existing code style and conventions
- Add comments to explain complex logic
- Update documentation if needed
- Test your changes before submitting

### 3. Commit Changes

Write clear, concise commit messages:

```bash
git commit -m "feat: Add new CloudWatch alarm for RDS connections"
git commit -m "fix: Resolve ALB health check timeout issue"
git commit -m "docs: Update README with deployment instructions"
```

### 4. Push and Create Pull Request

```bash
git push origin feature/your-feature-name
```

Then create a Pull Request on GitHub with:
- Clear description of changes
- Reference to related issues
- Screenshots (if applicable)

## Development Guidelines

### Code Style

- Use 4 spaces for indentation in YAML files
- Use 2 spaces for indentation in JavaScript/HTML/CSS files
- Follow AWS CloudFormation best practices
- Use meaningful variable and resource names

### Testing

- Test CloudFormation templates before submitting
- Verify all resources are properly configured
- Check for security best practices

### Documentation

- Update README.md if adding new features
- Add inline comments for complex configurations
- Keep architecture diagrams up to date

## Reporting Issues

When reporting issues, please include:
- Clear description of the problem
- Steps to reproduce
- Expected vs actual behavior
- Screenshots (if applicable)
- AWS region and configuration

## Security

If you discover a security vulnerability, please report it responsibly:
- Do NOT open a public issue
- Email the maintainer directly
- Provide details of the vulnerability

## Code of Conduct

- Be respectful and inclusive
- Focus on constructive feedback
- Help others learn and grow
- Follow AWS Well-Architected Framework principles

## Questions?

If you have questions about contributing, feel free to open an issue or reach out to the maintainer.
