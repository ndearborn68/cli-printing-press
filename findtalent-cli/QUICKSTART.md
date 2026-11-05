# FindTalent CLI Quick Start

This CLI provides programmatic access to FindTalent's candidate sourcing platform.

## Prerequisites

- Go 1.21 or later
- A FindTalent account (https://www.findtalent.today/)
- Your FindTalent session cookie or API token

## Installation

```bash
# From the findtalent-cli directory
make build

# Or use go directly
go build -o findtalent-pp-cli ./cmd/findtalent-pp-cli
```

## Authentication Setup

### Method 1: Extract Session Cookie (Recommended for Testing)

1. **Sign in to FindTalent**
   - Visit https://www.findtalent.today/sign-in
   - Enter your credentials and sign in

2. **Open Browser Developer Tools**
   - Press F12 or Right-Click → Inspect
   - Go to the "Application" tab (Chrome) or "Storage" tab (Firefox)

3. **Find the Session Cookie**
   - Navigate to Cookies → https://www.findtalent.today
   - Look for a cookie named `findtalent_session` or similar
   - Copy the entire cookie value

4. **Set the Environment Variable**
   ```bash
   export FINDTALENT_BEARER_AUTH="YOUR_SESSION_COOKIE_VALUE"
   ```

### Method 2: Use API Token (For Production)

If FindTalent provides API tokens in account settings:

```bash
export FINDTALENT_BEARER_AUTH="your-api-token-here"
```

### Method 3: Use the Auth Command

Store credentials persistently:

```bash
echo "YOUR_TOKEN_OR_COOKIE" | ./findtalent-pp-cli auth set-token
```

## Verify Setup

```bash
./findtalent-pp-cli doctor
```

This checks your configuration and credentials.

## Common Commands

### Search for Candidates

Search by job title:
```bash
./findtalent-pp-cli candidates search --title "Software Engineer"
```

Search by company:
```bash
./findtalent-pp-cli candidates search --company "Google"
```

Search by location:
```bash
./findtalent-pp-cli candidates search --location "San Francisco, CA"
```

Combine multiple filters:
```bash
./findtalent-pp-cli candidates search \
  --title "Senior Software Engineer" \
  --location "San Francisco, CA" \
  --keywords "Python,AWS,React" \
  --limit 50
```

Get results as JSON:
```bash
./findtalent-pp-cli candidates search \
  --title "Software Engineer" \
  --json
```

### Get Candidate Profile

Retrieve full details for a specific candidate:
```bash
./findtalent-pp-cli candidates get --candidate-id cand_abc123xyz
```

With JSON output:
```bash
./findtalent-pp-cli candidates get --candidate-id cand_abc123xyz --json
```

### Export Candidates

Export multiple candidates to CSV:
```bash
./findtalent-pp-cli candidates export \
  --candidate-ids cand_abc123,cand_def456,cand_ghi789 \
  --format csv \
  --output candidates.csv
```

Export to JSON:
```bash
./findtalent-pp-cli candidates export \
  --candidate-ids cand_abc123,cand_def456 \
  --format json \
  --output candidates.json
```

Export specific fields only:
```bash
./findtalent-pp-cli candidates export \
  --candidate-ids cand_abc123,cand_def456 \
  --format csv \
  --fields "name,email,title,company,location" \
  --output candidates.csv
```

### View Search History

```bash
./findtalent-pp-cli findtalent-search history --limit 20
```

## Agent Integration

This CLI includes full MCP (Model Context Protocol) support for AI agents.

### Claude Desktop

1. Install the MCP binary
2. Add to your Claude Desktop config:
   ```json
   {
     "mcpServers": {
       "findtalent": {
         "command": "/path/to/findtalent-pp-mcp",
         "env": {
           "FINDTALENT_BEARER_AUTH": "your-token-here"
         }
       }
     }
   }
   ```

### Claude Code / Cursor

Use the `/pp-findtalent` skill:
```
/pp-findtalent search for senior engineers in Seattle
```

## Tips & Best Practices

1. **Session Cookie Expiration**: Session cookies expire after your browser session ends. For automation, use API tokens if available.

2. **Rate Limiting**: The CLI respects rate limits automatically. Use `--rate-limit` to adjust if needed.

3. **Pagination**: For large result sets, use `--limit` and `--offset`:
   ```bash
   ./findtalent-pp-cli candidates search --title "Engineer" --limit 100 --offset 0
   ./findtalent-pp-cli candidates search --title "Engineer" --limit 100 --offset 100
   ```

4. **Agent Mode**: Add `--agent` flag for JSON output optimized for AI agents:
   ```bash
   ./findtalent-pp-cli candidates search --title "Engineer" --agent
   ```

5. **Debugging**: Use `--dry-run` to see the request without sending it:
   ```bash
   ./findtalent-pp-cli candidates search --title "Engineer" --dry-run
   ```

## Troubleshooting

### "Unauthorized" Error

- Verify your token is set: `echo $FINDTALENT_BEARER_AUTH`
- Check if your session has expired - re-extract the cookie
- Run `./findtalent-pp-cli doctor` to diagnose

### "Not Found" Error

- Verify the candidate ID is correct
- The candidate may have been removed or made private

### "Rate Limited" Error

- Wait for the retry-after period (shown in the error)
- Reduce request frequency with `--rate-limit`

## More Information

- Full documentation: See [README.md](README.md)
- Agent skill guide: See [SKILL.md](SKILL.md)
- Report issues: Create an issue in the repository

## Important Notes

**This CLI is based on an inferred API specification.** FindTalent does not currently publish a public API. The endpoints and authentication mechanisms are best-effort assumptions based on standard web application patterns. If you encounter issues:

1. The actual FindTalent API may use different endpoints
2. Authentication requirements may differ
3. Response formats may vary
4. Some features may not be available via API

For the most accurate integration, contact FindTalent directly for official API documentation.
