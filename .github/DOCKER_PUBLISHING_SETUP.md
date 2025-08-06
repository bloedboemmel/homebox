# Docker Publishing Setup for bloedboemmel/homebox

This document explains the Docker publishing configuration for this fork of the homebox project.

## What's Been Configured

The GitHub Actions workflows have been updated to publish Docker images to your registries:

### Docker Images Published
- **Regular Docker Image**: `bloedboemmel/homebox` (DockerHub) and `ghcr.io/bloedboemmel/homebox` (GitHub Container Registry)
- **Rootless Docker Image**: `bloedboemmel/homebox:*-rootless` and `ghcr.io/bloedboemmel/homebox:*-rootless`

### Supported Platforms
- linux/amd64
- linux/arm64
- linux/arm/v7

### Workflow Triggers
- **Pushes to main branch** (with changes to backend/, frontend/, Dockerfile, etc.)
- **Git tags** matching pattern `v*.*.*` (e.g., v1.2.3)
- **Pull requests** to main (for testing, doesn't push images)
- **Daily schedule** at midnight UTC (nightly builds)

## Required Setup Steps

### 1. DockerHub Repository
Create a repository named `homebox` in your DockerHub account at https://hub.docker.com/

### 2. GitHub Secrets Configuration
Add these secrets to your repository at https://github.com/bloedboemmel/homebox/settings/secrets/actions:

- `DOCKER_USERNAME`: Your DockerHub username (`bloedboemmel`)
- `DOCKER_PASSWORD`: Your DockerHub access token (recommended) or password
  - Create an access token at https://hub.docker.com/settings/security

### 3. GitHub Container Registry
No additional setup required - GHCR publishing is automatic with the `GITHUB_TOKEN`.

## Testing the Setup

### Manual Trigger
You can manually trigger the workflows from the Actions tab in your GitHub repository.

### Automatic Trigger
1. Make a change to the backend or frontend code
2. Push to the main branch
3. Check the "Actions" tab to see the workflow running

### Tag-based Release
1. Create and push a git tag: `git tag v1.0.0 && git push origin v1.0.0`
2. This will trigger both Docker publishing and binary release workflows

## Published Artifacts

After successful runs, you'll have:
- **Docker Images**: Available on DockerHub and GHCR
- **Release Binaries**: Published to GitHub Releases (via GoReleaser)
- **Multi-platform Support**: Images built for AMD64, ARM64, and ARM/v7

## Cache Strategy

The builds use Docker layer caching stored in `ghcr.io/bloedboemmel/devcache` to speed up subsequent builds.

## Release Information Sync

The workflows are configured to include release information from the original homebox repository through:
- Version information passed as build arguments
- Git commit SHA included in the build
- Proper tagging and metadata management