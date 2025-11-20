# ccproxy CLI Documentation

The ccproxy CLI provides a command-line interface for managing OAuth accounts, monitoring usage statistics, and controlling the load balancer.

## Table of Contents

- [Installation and Setup](#installation-and-setup)
- [Global Options and Help](#global-options-and-help)
- [Command Reference](#command-reference)
  - [Account Management](#account-management)
  - [Statistics and History](#statistics-and-history)
  - [System Commands](#system-commands)
  - [Server and Monitoring](#server-and-monitoring)
- [Usage Examples](#usage-examples)
- [Configuration](#configuration)
- [Environment Variables](#environment-variables)
- [Troubleshooting](#troubleshooting)

## Installation and Setup

### Prerequisites

- Bun runtime (>= 1.2.8)
- Node.js compatible system

### Installation

1. Clone the repository:
```bash
git clone https://github.com/snipe-code/ccproxy.git
cd ccproxy
```

2. Install dependencies:
```bash
bun install
```

3. Build the CLI:
```bash
bun run build
```

4. Run the CLI:
```bash
ccproxy [options]
```

### First-time Setup

1. Add your first OAuth account:
```bash
ccproxy --add-account myaccount
```

2. Start the load balancer server:
```bash
ccproxy --serve
```

## Global Options and Help

### Getting Help

Display all available commands and options:

```bash
ccproxy --help
```

Or use the short form:

```bash
ccproxy -h
```

### Help Output Format

```
🎯 ccproxy - Load Balancer for Claude

Usage: ccproxy [options]

Options:
  --serve              Start API server with dashboard
  --port <number>      Server port (default: 8080, or PORT env var)
  --logs [N]           Stream latest N lines then follow
  --stats              Show statistics (JSON output)
  --add-account <name> Add a new account
    --mode <max|console>  Account mode (default: max)
    --tier <1|5|20>       Account tier (default: 1)
  --list               List all accounts
  --remove <name>      Remove an account
  --pause <name>       Pause an account
  --resume <name>      Resume an account
  --analyze            Analyze database performance
  --reset-stats        Reset usage statistics
  --clear-history      Clear request history
  --help, -h           Show this help message

Interactive Mode:
  ccproxy          Launch interactive TUI (default)
```

## Command Reference

### Account Management

#### `--add-account <name>`

Add a new OAuth account to the load balancer pool.

**Syntax:**
```bash
ccproxy --add-account <name> [--mode <max|console>] [--tier <1|5|20>]
```

**Options:**
- `--mode`: Account type (optional, defaults to "max")
  - `max`: Claude Max account
  - `console`: Console account
- `--tier`: Account tier (optional, defaults to 1, Max accounts only)
  - `1`: Tier 1 account
  - `5`: Tier 5 account
  - `20`: Tier 20 account

**Interactive Flow:**
1. If mode not provided, defaults to "max"
2. If tier not provided (Max accounts only), defaults to 1
3. Opens browser for OAuth authentication
4. Waits for OAuth callback on localhost:7856
5. Stores account credentials securely in the database

#### `--list`

Display all configured accounts with their current status.

**Syntax:**
```bash
ccproxy --list
```

**Output Format:**
```
Accounts:
  - account1 (max mode, tier 5)
  - account2 (console mode, tier 1)
```

#### `--remove <name>`

Remove an account from the configuration.

**Syntax:**
```bash
ccproxy --remove <name>
```

**Behavior:**
- Removes account from database immediately
- Cleans up associated session data
- For confirmation prompts, use the older `ccproxy-cli remove <name>` command

#### `--pause <name>`

Temporarily exclude an account from the load balancer rotation.

**Syntax:**
```bash
ccproxy --pause <name>
```

**Use Cases:**
- Account experiencing issues
- Manual rate limit management
- Maintenance or debugging

#### `--resume <name>`

Re-enable a paused account for load balancing.

**Syntax:**
```bash
ccproxy --resume <name>
```

### Statistics and History

#### `--stats`

Display current statistics in JSON format.

**Syntax:**
```bash
ccproxy --stats
```

**Output:**
Returns JSON-formatted statistics including account usage, request counts, and performance metrics.

#### `--reset-stats`

Reset request counters for all accounts.

**Syntax:**
```bash
ccproxy --reset-stats
```

**Effects:**
- Resets request counts to 0
- Preserves account configuration
- Does not affect rate limit timers

#### `--clear-history`

Remove all request history records.

**Syntax:**
```bash
ccproxy --clear-history
```

**Effects:**
- Deletes request log entries
- Preserves account data
- Reports number of records cleared

### System Commands

#### `--analyze`

Analyze database performance and index usage.

**Syntax:**
```bash
ccproxy --analyze
```

**Output:**
- Database performance metrics
- Index usage statistics
- Query optimization suggestions

#### Interactive Terminal UI (TUI)

Launch the interactive terminal interface (default mode):

```bash
ccproxy
```

**Features:**
- Real-time account monitoring
- Request logs viewing
- Performance analytics
- Interactive account management

### Server and Monitoring

#### `--serve`

Start the API server with dashboard.

**Syntax:**
```bash
ccproxy --serve [--port <number>]
```

**Options:**
- `--port`: Server port (default: 8080, or PORT env var)

**Access:**
- API endpoint: `http://localhost:8080`
- Dashboard: `http://localhost:8080/_dashboard`

#### `--logs [N]`

Stream request logs in real-time.

**Syntax:**
```bash
ccproxy --logs [N]
```

**Options:**
- `N`: Number of historical lines to display before streaming (optional)

**Examples:**
```bash
# Stream live logs only
ccproxy --logs

# Show last 50 lines then stream
ccproxy --logs 50
```

## Usage Examples

### Basic Account Setup

```bash
# Add a Claude Max account with tier 5
ccproxy --add-account work-account --mode max --tier 5

# Add a Console account
ccproxy --add-account personal-account --mode console

# List all accounts
ccproxy --list

# View statistics
ccproxy --stats
```

### Server Operations

```bash
# Start server on default port
ccproxy --serve

# Start server on custom port
ccproxy --serve --port 3000

# Stream logs
ccproxy --logs

# View last 100 lines then stream
ccproxy --logs 100
```

### Managing Rate Limits

```bash
# Pause account hitting rate limits
ccproxy --pause work-account

# Resume after cooldown
ccproxy --resume work-account

# Reset statistics for fresh start
ccproxy --reset-stats
```

### Maintenance Operations

```bash
# Remove account
ccproxy --remove old-account

# Clear old request logs
ccproxy --clear-history

# Analyze database performance
ccproxy --analyze
```

### Interactive Mode

```bash
# Launch interactive TUI (default)
ccproxy

# TUI launches with auto-started server
# Navigate with arrow keys, tab between sections
```

### Automation Examples

```bash
# Add multiple accounts via script
for i in {1..3}; do
  ccproxy --add-account "account-$i" --mode max --tier 5
done

# Monitor account status
watch -n 5 'ccproxy --list'

# Automated cleanup
ccproxy --clear-history && ccproxy --reset-stats

# Export statistics for monitoring
ccproxy --stats > stats.json
```

## Configuration

### Configuration File Location

ccproxy stores its configuration in platform-specific directories:

#### macOS/Linux
```
~/.config/ccproxy/ccproxy.json
```

Or if `XDG_CONFIG_HOME` is set:
```
$XDG_CONFIG_HOME/ccproxy/ccproxy.json
```

#### Windows
```
%LOCALAPPDATA%\ccproxy\ccproxy.json
```

Or fallback to:
```
%APPDATA%\ccproxy\ccproxy.json
```

### Configuration Structure

```json
{
  "lb_strategy": "session",
  "client_id": "optional-custom-client-id",
  "retry_attempts": 3,
  "retry_delay_ms": 1000,
  "retry_backoff": 2,
  "session_duration_ms": 18000000,
  "port": 8080
}
```

### Database Location

The SQLite database follows the same directory structure:
- **macOS/Linux**: `~/.config/ccproxy/ccproxy.db`
- **Windows**: `%LOCALAPPDATA%\ccproxy\ccproxy.db`

## Environment Variables

### Core Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `ccproxy_CONFIG_PATH` | Override config file location | Platform default |
| `ccproxy_DB_PATH` | Override database location | Platform default |
| `PORT` | Server port | 8080 |
| `CLIENT_ID` | OAuth client ID | 9d1c250a-e61b-44d9-88ed-5944d1962f5e |

### Load Balancing

| Variable | Description | Default |
|----------|-------------|---------|
| `LB_STRATEGY` | Load balancing strategy (only 'session' is supported) | session |

### Retry Configuration

| Variable | Description | Default |
|----------|-------------|---------|
| `RETRY_ATTEMPTS` | Number of retry attempts | 3 |
| `RETRY_DELAY_MS` | Initial retry delay (ms) | 1000 |
| `RETRY_BACKOFF` | Exponential backoff multiplier | 2 |

### Session Management

| Variable | Description | Default |
|----------|-------------|---------|
| `SESSION_DURATION_MS` | OAuth session duration (ms) | 18000000 (5 hours) |

### Logging and Debugging

| Variable | Description | Default |
|----------|-------------|---------|
| `LOG_LEVEL` | Log verbosity (DEBUG/INFO/WARN/ERROR) | INFO |
| `LOG_FORMAT` | Output format (pretty/json) | pretty |
| `ccproxy_DEBUG` | Enable debug mode (1/0) - enables console output | 0 |

### Pricing and Features

| Variable | Description | Default |
|----------|-------------|---------|
| `CF_PRICING_REFRESH_HOURS` | Pricing cache duration | 24 |
| `CF_PRICING_OFFLINE` | Offline mode flag (1/0) | 0 |

## Troubleshooting

### Common Issues

#### OAuth Authentication Fails

**Problem**: Browser doesn't open or OAuth callback fails

**Solutions**:
1. Ensure default browser is configured
2. Check firewall settings for localhost:7856
3. Manually copy OAuth URL from terminal
4. Verify network connectivity

#### Account Shows as "Expired"

**Problem**: Token status shows expired

**Solutions**:
1. Remove and re-add the account
2. Check system time synchronization
3. Verify OAuth session hasn't exceeded 5-hour limit

#### Rate Limit Errors

**Problem**: Accounts hitting rate limits frequently

**Solutions**:
1. Add more accounts to the pool
2. Increase session duration for less frequent switching
3. Implement request throttling in client code
4. Monitor usage with `bun cli list`

#### Database Errors

**Problem**: "Database is locked" or corruption errors

**Solutions**:
1. Stop all ccproxy processes
2. Check file permissions on database
3. Backup and recreate if corrupted:
   ```bash
   cp ~/.config/ccproxy/ccproxy.db ~/.config/ccproxy/ccproxy.db.backup
   rm ~/.config/ccproxy/ccproxy.db
   ```

### Debug Mode

Enable detailed logging for troubleshooting:

```bash
# Enable debug logging
export ccproxy_DEBUG=1
export LOG_LEVEL=DEBUG

# Run with verbose output
ccproxy --list

# Stream debug logs
ccproxy --logs
```

### Getting Support

1. Check existing documentation in `/docs`
2. Review debug logs for detailed error messages
3. Ensure all dependencies are up to date
4. File an issue with reproduction steps

### Best Practices

1. **Regular Maintenance**
   - Clear history periodically to manage database size
   - Reset stats monthly for accurate metrics
   - Monitor account health with regular `ccproxy --list` commands
   - Use `ccproxy --analyze` to optimize database performance

2. **Account Management**
   - Use descriptive account names
   - Distribute load across multiple accounts
   - Keep accounts of similar tiers for consistent performance
   - Pause accounts proactively when approaching rate limits

3. **Security**
   - Protect configuration directory permissions
   - Don't share OAuth tokens or session data
   - Rotate accounts periodically
   - Monitor logs with `ccproxy --logs` for suspicious activity

4. **Performance**
   - Use higher-tier accounts for heavy workloads
   - Implement client-side retry logic
   - Monitor rate limit patterns with `ccproxy --stats`
   - Run server with `ccproxy --serve` for production use