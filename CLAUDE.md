# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What is this?

A load balancer proxy for Claude and Claude Code that distributes requests across multiple OAuth accounts to avoid rate limiting.

## Important: After making code changes

Always run:
- `bun run lint` - Fix linting issues
- `bun run typecheck` - Check for type errors
- `bun run format` - Format code

## Commands

### Running the server
- `bun start` - Start the load balancer (port 8080)

### Managing accounts
- `ccproxy --add-account <name>` - Add a new account
- `ccproxy --list` - List all accounts
- `ccproxy --remove <name>` - Remove an account

### Maintenance
- `ccproxy --reset-stats` - Reset usage statistics
- `ccproxy --clear-history` - Clear request history