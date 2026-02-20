# AcuAPI 🛡️

A powerful command-line interface for [Acunetix](https://www.acunetix.com/) vulnerability scanner. Manage targets, scans, reports, and vulnerabilities directly from your terminal.

## Features

- 🎯 **Target Management** - Add, list, delete, and bulk import targets
- 🔍 **Scan Control** - Start, stop, pause, resume, and watch scans live
- 📊 **Reports** - Generate and download vulnerability reports
- 🐛 **Vulnerabilities** - View details with HTTP request/response, filter by severity
- 👥 **Groups** - Organize targets, bulk import, scan entire groups, get group vulns
- ⚡ **Quick Scan** - One-command target addition and scan initiation
- 📤 **Export** - JSON, CSV, and Markdown output formats
- 🎨 **Beautiful Output** - Color-coded, formatted terminal output

## Prerequisites

- **bash** (v4.0+)
- **curl** - For API requests
- **jq** - For JSON parsing

```bash
# macOS
brew install jq

# Ubuntu/Debian
sudo apt-get install jq

# CentOS/RHEL
sudo yum install jq
```

## Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/AcuAPI.git
cd AcuAPI

# Make executable
chmod +x acunetix

# (Optional) Add to PATH for global access
sudo ln -s $(pwd)/acunetix /usr/local/bin/acunetix
```

## Configuration

Edit `config.json` with your Acunetix credentials:

```json
{
    "url": "https://your-acunetix-server:3443",
    "api_key": "your-api-key-here"
}
```

To get your API key:
1. Log into Acunetix web interface
2. Go to **Profile** → **API Key**
3. Generate or copy your API key

## Usage

```bash
./acunetix <command> <subcommand> [options]
```

### Quick Start

```bash
# Run a quick scan (adds target + starts scan)
./acunetix quick https://example.com

# Check scan status
./acunetix scans status <scan_id>
```

### Targets

```bash
# List all targets
./acunetix targets list

# Add a new target
./acunetix targets add https://example.com "Description" 10

# Get target info
./acunetix targets info <target_id>

# Delete a target
./acunetix targets delete <target_id>
```

### Scans

```bash
# List all scans
./acunetix scans list

# Start a scan
./acunetix scans start <target_id> [profile]

# Check scan status
./acunetix scans status <scan_id>

# Stop a scan
./acunetix scans stop <scan_id>

# Pause/Resume a scan
./acunetix scans pause <scan_id>
./acunetix scans resume <scan_id>

# Delete a scan
./acunetix scans delete <scan_id>
```

### Reports

```bash
# List all reports
./acunetix reports list

# Generate a report
./acunetix reports generate <scan_id> [template]

# Download a report
./acunetix reports download <report_id> output.pdf

# Delete a report
./acunetix reports delete <report_id>
```

### Vulnerabilities

```bash
# List vulnerabilities for a scan
./acunetix vulns list <scan_id>

# Filter by severity
./acunetix vulns list <scan_id> critical
./acunetix vulns list <scan_id> high

# Full details for ALL vulnerabilities (with HTTP req/res)
./acunetix vulns all <scan_id>
./acunetix vulns all <scan_id> high

# Detailed view of single vulnerability
./acunetix vulns show <scan_id> <vuln_id>

# Get raw JSON details
./acunetix vulns info <scan_id> <vuln_id>

# Export vulnerabilities
./acunetix vulns export <scan_id> json findings.json
./acunetix vulns export <scan_id> csv findings.csv
./acunetix vulns export <scan_id> markdown report.md
```

### Groups

Manage target groups for bulk operations:

```bash
# List all groups
./acunetix groups list

# Create a new group
./acunetix groups create "BugBounty" "Bug bounty targets"

# Get group info
./acunetix groups info <group_id>

# List targets in group
./acunetix groups targets <group_id>

# Import URLs from file into group
./acunetix groups import <group_id> urls.txt

# Scan ALL targets in a group
./acunetix groups scan <group_id>
./acunetix groups scan <group_id> high  # with profile

# Get vulnerabilities for entire group
./acunetix groups vulns <group_id>
./acunetix groups vulns <group_id> critical  # filter by severity

# Full vulnerability details for group
./acunetix groups vulns-all <group_id>
./acunetix groups vulns-all <group_id> high

# Delete a group
./acunetix groups delete <group_id>
```

**Alternative syntax** - you can also put the group_id first:
```bash
./acunetix groups <group_id> info
./acunetix groups <group_id> targets
./acunetix groups <group_id> scan
./acunetix groups <group_id> import urls.txt
./acunetix groups <group_id> vulns high
```

### Other Commands

```bash
# List scan profiles
./acunetix profiles

# List report templates
./acunetix templates

# API info
./acunetix info

# License info
./acunetix license

# Dashboard stats
./acunetix stats
```

### Output Formats

```bash
# JSON output
./acunetix -j scans list
./acunetix --json vulns list <scan_id>

# CSV output
./acunetix -c targets list

# Save to file
./acunetix -j -o results.json scans list
```

## Scan Profiles

| Profile | Alias | Description |
|---------|-------|-------------|
| Full Scan | `full` | Complete vulnerability scan |
| High Risk Vulnerabilities | `high` | Focus on critical issues |
| Cross-site Scripting | `xss` | XSS vulnerability detection |
| SQL Injection | `sqli` | SQL injection testing |
| Weak Passwords | `passwords` | Password strength testing |
| Crawl Only | `crawl` | Site crawling without active testing |

**Example:**
```bash
./acunetix scans start <target_id> sqli
```

## Report Templates

| Template | Alias | Description |
|----------|-------|-------------|
| Developer | `developer` | Technical details for developers |
| Executive Summary | `executive` | High-level overview for management |
| Quick | `quick` | Brief summary |
| Affected Items | `affected` | List of affected resources |
| HIPAA | `hipaa` | HIPAA compliance report |
| OWASP Top 10 2017 | `owasp2017` | OWASP 2017 compliance |
| OWASP Top 10 2021 | `owasp2021` | OWASP 2021 compliance |
| PCI DSS 3.2 | `pci` | PCI DSS compliance |
| SANS Top 25 | `sans` | SANS Top 25 compliance |

**Example:**
```bash
./acunetix reports generate <scan_id> owasp2021
```

## Examples

### Complete Workflow

```bash
# 1. Add a target
./acunetix targets add https://testsite.com "Production Site" 10
# Output: Target ID: abc-123-def

# 2. Start a full scan
./acunetix scans start abc-123-def full
# Output: Scan ID: xyz-456-ghi

# 3. Monitor progress
./acunetix scans status xyz-456-ghi
./acunetix scans watch xyz-456-ghi  # Live progress

# 4. View vulnerabilities (after scan completes)
./acunetix vulns list xyz-456-ghi
./acunetix vulns all xyz-456-ghi critical  # Full details

# 5. Generate report
./acunetix reports generate xyz-456-ghi executive
# Output: Report ID: rep-789-jkl

# 6. Download report
./acunetix reports download rep-789-jkl security-report.pdf
```

### Group-Based Workflow

```bash
# 1. Create a group
./acunetix groups create "BugBounty" "Q1 targets"
# Output: Group ID: d9ef3687-42a0-...

# 2. Import targets from file
./acunetix groups import d9ef3687-... urls.txt

# 3. Scan all targets at once
./acunetix groups scan d9ef3687-...

# 4. Get all vulnerabilities for the group
./acunetix groups vulns d9ef3687-... high
./acunetix -j groups vulns d9ef3687-... > report.json
```

### Quick Scan

```bash
# One command to add target and start scanning
./acunetix quick https://testsite.com high
```

## Project Structure

```
AcuAPI/
├── acunetix        # Main CLI script
├── config.json     # Configuration file
└── README.md       # Documentation
```

## Troubleshooting

### Certificate Errors
The CLI uses `-k` flag to allow self-signed certificates. This is common for Acunetix installations.

### Connection Issues
Verify your Acunetix server is accessible:
```bash
curl -k https://your-server:3443/api/v1/info -H "X-Auth: your-api-key"
```

### Permission Denied
```bash
chmod +x acunetix
```

## Security Notes

⚠️ **Important:**
- Keep your `config.json` secure and never commit it with real credentials
- Add `config.json` to `.gitignore` in production
- Use environment variables for CI/CD pipelines

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Acknowledgments

- [Acunetix](https://www.acunetix.com/) for their comprehensive API
- Built with ❤️ for the security community

---

**Disclaimer:** This tool is for authorized security testing only. Always obtain proper authorization before scanning any target.
