# MCP Memory Service Scripts

This directory contains utility scripts for maintaining and managing the MCP Memory Service.

## Directory Structure

- **`migrations/`** - Database migration scripts for schema changes and data cleanup
  - `cleanup_mcp_timestamps.py` - Fixes timestamp field proliferation issue
  - `verify_mcp_timestamps.py` - Verifies database timestamp consistency
  - `TIMESTAMP_CLEANUP_README.md` - Documentation for timestamp cleanup

## Maintenance Scripts

### Database Cleanup and Maintenance

**`cleanup_corrupted_encoding.py`** - Removes memories with corrupted emoji encoding

```bash
# Dry run - preview what would be deleted
python scripts/cleanup_corrupted_encoding.py

# Execute actual cleanup
python scripts/cleanup_corrupted_encoding.py --execute
```

**`find_duplicates.py`** - Comprehensive tool for identifying and removing duplicate memories from the database

```bash
# Dry run - preview what would be deleted
python scripts/find_duplicates.py

# Execute actual removal of duplicates  
python scripts/find_duplicates.py --execute

# Custom database path
python scripts/find_duplicates.py --db-path /path/to/sqlite_vec.db --execute
```

**Features:**
- ✅ Detects exact duplicates using content hash comparison
- ✅ Identifies similar content using normalized text analysis
- ✅ Prioritizes UTF8-fixed versions over corrupted ones
- ✅ Keeps newest versions when no other criteria apply
- ✅ Supports dry-run mode to preview deletions
- ✅ Detailed reporting of duplicate groups and reasons for retention
- ✅ Safe deletion with comprehensive error handling

**Use Cases:**
- Database maintenance after encoding fixes
- Cleanup after re-ingestion of documents
- Regular database optimization
- Resolving duplicate entries from multiple sources

### Documentation Link Checker

**`check_documentation_links.py`** - Comprehensive tool for validating internal documentation links

```bash
# Basic usage - check all links
python scripts/check_documentation_links.py

# Verbose mode - show all links (working and broken)
python scripts/check_documentation_links.py --verbose

# With fix suggestions for broken links
python scripts/check_documentation_links.py --fix-suggestions

# Different output formats
python scripts/check_documentation_links.py --format json
```

**Features:**
- ✅ Scans all markdown files in the repository
- ✅ Validates internal relative links only (skips external URLs)
- ✅ Provides detailed error reporting with target paths
- ✅ Suggests fixes for broken links based on similar filenames
- ✅ Multiple output formats (text, markdown, json)
- ✅ Exit codes for CI/CD integration (0 = success, 1 = broken links found)

**Use Cases:**
- Pre-commit validation
- Documentation maintenance
- CI/CD pipeline integration
- After repository restructuring

### Timestamp Diagnostic Tools

**`simple_timestamp_check.py`** - Production-ready timestamp health analyzer for SQLite databases

```bash
# Quick health check using default database
python scripts/simple_timestamp_check.py

# Analyze specific database file
python scripts/simple_timestamp_check.py /path/to/database.db

# JSON output for programmatic use
python scripts/simple_timestamp_check.py --format json

# Save results to file
python scripts/simple_timestamp_check.py --format json --output results.json

# Verbose analysis with detailed examples
python scripts/simple_timestamp_check.py --verbose

# Quick summary only
python scripts/simple_timestamp_check.py --format summary
```

**Features:**
- ✅ Direct SQLite database analysis (no service dependencies)
- ✅ Comprehensive timestamp field validation
- ✅ Health status assessment with exit codes
- ✅ Multiple output formats (text, JSON, summary)
- ✅ Detailed reporting of missing and inconsistent timestamps
- ✅ Cross-platform database path detection (macOS default)
- ✅ Production-ready error handling and logging

**Use Cases:**
- Database health monitoring
- Timestamp migration verification
- Troubleshooting search inconsistencies
- CI/CD database validation
- Production database maintenance

**Exit Codes:**
- `0` - Excellent/Good health
- `1` - Warning status
- `2` - Critical issues found
- `3` - Analysis failed (database errors)

## Usage

All scripts should be run from the repository root or with appropriate path adjustments.

## Adding New Scripts

When adding new maintenance scripts:
1. Create appropriate subdirectories for organization
2. Include clear documentation
3. Always implement backup/safety mechanisms for data-modifying scripts
4. Add verification scripts where appropriate
