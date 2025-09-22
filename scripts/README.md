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

## Backend Synchronization

### Cloudflare ↔ SQLite-vec Sync Tools

**`sync_memory_backends.py`** - Bidirectional synchronization between Cloudflare and SQLite-vec backends

```bash
# Check sync status
python scripts/sync_memory_backends.py --status

# Dry run - preview what would be synced
python scripts/sync_memory_backends.py --dry-run

# Sync from Cloudflare to SQLite-vec (backup)
python scripts/sync_memory_backends.py --direction cf-to-sqlite

# Sync from SQLite-vec to Cloudflare (restore)
python scripts/sync_memory_backends.py --direction sqlite-to-cf

# Bidirectional sync
python scripts/sync_memory_backends.py --direction bidirectional

# Verbose mode with detailed logging
python scripts/sync_memory_backends.py --verbose --dry-run
```

**Features:**
- ✅ Bidirectional sync with intelligent deduplication
- ✅ Content-based hashing to prevent duplicates
- ✅ Dry-run mode for safe testing
- ✅ Comprehensive status reporting
- ✅ Preserves all metadata and timestamps
- ✅ Handles large datasets efficiently

**`claude_sync_commands.py`** - User-friendly wrapper for sync operations

```bash
# Simple command interface
python scripts/claude_sync_commands.py status    # Check sync status
python scripts/claude_sync_commands.py backup    # Cloudflare → SQLite
python scripts/claude_sync_commands.py restore   # SQLite → Cloudflare
python scripts/claude_sync_commands.py sync      # Bidirectional
python scripts/claude_sync_commands.py dry-run   # Preview changes
```

**Use Cases:**
- Hybrid cloud/local deployment strategies
- Disaster recovery and backup
- Development/production synchronization
- Multi-machine memory sharing
- Migration between storage backends

## Service Management

**`memory_service_manager.sh`** - Linux service management for dual-backend deployments

```bash
# Start with Cloudflare backend
./scripts/memory_service_manager.sh start-cloudflare

# Start with SQLite-vec backend
./scripts/memory_service_manager.sh start-sqlite

# Check service status and sync status
./scripts/memory_service_manager.sh status

# Sync operations
./scripts/memory_service_manager.sh sync-backup   # Cloudflare → SQLite
./scripts/memory_service_manager.sh sync-restore  # SQLite → Cloudflare
./scripts/memory_service_manager.sh sync-both     # Bidirectional

# Stop service
./scripts/memory_service_manager.sh stop
```

**Features:**
- ✅ Manages dual-backend configurations
- ✅ Environment file management (.env, .env.sqlite)
- ✅ Service health monitoring
- ✅ Integrated sync operations
- ✅ Log management and troubleshooting

## Configuration Management

**`validate_config.py`** - Configuration validation for MCP Memory Service

```bash
# Validate current configuration
python scripts/validate_config.py

# Future: Auto-fix common issues
python scripts/validate_config.py --fix
```

**Features:**
- ✅ Validates Claude Code global configuration (~/.claude.json)
- ✅ Checks for conflicting .mcp.json files
- ✅ Validates Cloudflare credentials
- ✅ Detects environment configuration conflicts
- ✅ Comprehensive error reporting with solutions
- ✅ Color-coded output for clarity

**Use Cases:**
- Troubleshooting setup issues
- Pre-deployment validation
- Configuration consistency checks
- Debugging MCP connection problems

## Adding New Scripts

When adding new maintenance scripts:
1. Create appropriate subdirectories for organization
2. Include clear documentation
3. Always implement backup/safety mechanisms for data-modifying scripts
4. Add verification scripts where appropriate
