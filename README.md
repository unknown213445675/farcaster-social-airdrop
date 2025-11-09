# Farcaster Social Airdrop Finder

## Overview

A general-purpose tool to find Farcaster users who posted about a token but haven't received or bought it yet. Perfect for identifying potential airdrop recipients for any Base chain token.

## Prerequisites

1. **Neynar API Key**: You need a Neynar API key to access Farcaster data
   - Get one at: https://neynar.com/
   - Add it to your `.env` file: `NEYNAR_API_KEY=your_key_here`

2. **Token Contract Address**: Find your token's contract address on Base chain
   - **Base Explorer**: https://basescan.org/
   - **DexScreener**: https://dexscreener.com/base

## How to Use

### Basic Usage (elizaOS - Default)

```bash
# Uses default elizaOS settings
node src/index.js social-airdrop
```

### Search for Any Token

```bash
# Search for DEGEN token
node src/index.js social-airdrop \
  --ticker="DEGEN" \
  --tokenAddress="0xDEGENTokenAddressHere"

# Search for HIGHER token
node src/index.js social-airdrop \
  --ticker="HIGHER" \
  --tokenAddress="0xHIGHERTokenAddressHere"
```

### Advanced Usage

```bash
# Custom search text (instead of ticker-based)
node src/index.js social-airdrop \
  --ticker="MYTOKEN" \
  --searchText="\"My Token\" | token" \
  --tokenAddress="0x..." \
  --output="custom_results.csv"
```

### Parameters

- **`--ticker`** (default: "elizaOS"): Token ticker/symbol - automatically searches for "$ticker"
- **`--searchText`**: Custom search text (overrides automatic "$ticker" format)
- **`--tokenAddress`** (default: elizaOS address): Token contract address on Base chain
- **`--output`**: Output CSV filename (auto-generated as `{ticker}_airdrop_eligible.csv` if not specified)
- **`--noCache`**: Disable caching and fetch fresh data

## What the Script Does

1. **Searches Farcaster**: Uses global searchCasts API to find ALL casts with your search term
2. **Deduplicates**: Keeps only one cast per user (removes duplicate posts)
3. **Extracts Users**: Gets unique users and their verified Ethereum addresses
4. **Checks Balances**: Queries Base blockchain using multiple RPC providers for reliability
5. **Generates CSV**: Creates a CSV with users who DON'T have the token yet

## Features

- ✅ **Global Search**: Finds EVERY cast across all of Farcaster
- ✅ **Smart Caching**: 40x faster on subsequent runs
- ✅ **Resume Capability**: Can pause and restart anytime
- ✅ **Multi-RPC**: Rotates through 5 RPC providers with exponential backoff
- ✅ **User Deduplication**: One result per user (no duplicates)
- ✅ **General Purpose**: Works for any token, not just elizaOS

## Example Use Cases

```bash
# Find DEGEN supporters without the token
node src/index.js social-airdrop --ticker="DEGEN" --tokenAddress="0x..."

# Find HIGHER community members
node src/index.js social-airdrop --ticker="HIGHER" --tokenAddress="0x..."

# Find ai16z mentions
node src/index.js social-airdrop --ticker="ai16z" --tokenAddress="0x..."

# Custom search with multiple terms
node src/index.js social-airdrop \
  --searchText="(MYTOKEN | \$MYTOKEN | #MYTOKEN)" \
  --tokenAddress="0x..."
```

## Output CSV Format

The generated CSV will have these columns:

| Column | Description |
|--------|-------------|
| Username | Farcaster username |
| Display Name | User's display name |
| FID | Farcaster ID (unique identifier) |
| Wallet Address | Verified Ethereum address or "NO_VERIFIED_ADDRESS" |
| Reason | Why eligible: "NO_TOKEN" or "NO_ADDRESS" |
| Follower Count | User's follower count (for prioritization) |

## Example Output

```csv
Username,Display Name,FID,Wallet Address,Reason,Follower Count
alice,Alice Smith,12345,0x1234...,NO_TOKEN,523
bob,Bob Jones,67890,NO_VERIFIED_ADDRESS,NO_ADDRESS,142
charlie,Charlie Brown,11111,0x5678...,NO_TOKEN,891
```

## Performance & Reliability

### Built-in Protections
- **Rate limiting**: 1 second between API calls
- **Exponential backoff**: Automatic retry with increasing delays
- **Multi-RPC rotation**: Switches between 5 Base RPC providers
- **Auto-save**: Progress saved every 10 pages (casts) and 25 users (balances)
- **Resume capability**: Interrupt and restart anytime

### Speed
- **First run**: 10-30 minutes (finds all users + checks balances)
- **Subsequent runs**: 15-30 seconds (uses cached data)
- **Speedup**: 40x faster with caching

## Real-World Examples

### Find DEGEN Community Members
```bash
node src/index.js social-airdrop \
  --ticker="DEGEN" \
  --tokenAddress="0x4ed4e862860bed51a9570b96d89af5e1b0efefed"
```

### Find HIGHER Supporters
```bash
node src/index.js social-airdrop \
  --ticker="HIGHER" \
  --tokenAddress="0x0578d8A44db98B23BF096A382e016e29a5Ce0ffe"
```

### Find ai16z Mentions
```bash
node src/index.js social-airdrop \
  --ticker="ai16z" \
  --tokenAddress="0xYourTokenAddress"
```

### Multiple Variations
```bash
# Search for any mention of the token
node src/index.js social-airdrop \
  --ticker="MYTOKEN" \
  --searchText="MYTOKEN | \$MYTOKEN | #MYTOKEN | mytoken" \
  --tokenAddress="0x..."
```

## Troubleshooting

### "No casts found"
- The ticker might not be used on Farcaster
- Try different search terms
- Verify the ticker is spelled correctly

### Balance check errors
- The script automatically retries with 5 different RPCs
- If all fail, those users are marked as eligible (can't verify)
- Most checks should succeed

### Script seems stuck
- Check progress with: `tail -f FINAL_RUN.log`
- Checking 1000+ wallets takes 20-30 minutes (normal)
- Each wallet check: ~1 second with retries

## Tips for Best Results

1. **Use ticker parameter**: Easiest way - searches for "$ticker" automatically
2. **Custom search for comprehensive results**: Use OR operators for variations
3. **Verify token address**: Double-check on BaseScan before running
4. **Let it complete**: First run takes time but subsequent runs are instant
5. **Use follower count**: CSV includes followers - prioritize high-influence users

## Privacy & Ethics

⚠️ **Important Considerations:**

- Only wallet addresses that users have verified on Farcaster are collected
- This data is publicly available through the Farcaster protocol
- Use this tool responsibly and respect user privacy
- Don't spam or harass users identified through this tool
- Consider the ethical implications of airdrops and token distribution

## Bot/Spam Filtering

**Automatic filtering in both stages:**

### Criteria
Accounts are filtered if they meet **BOTH** conditions:
- FID (Farcaster ID) > 1,000,000 AND
- Follower count < 5

### Why?
- FIDs > 1M are very recent accounts (created 2024+)
- New accounts with almost no followers are likely:
  - Airdrop farmers
  - Spam bots
  - Inactive/fake accounts
  - Not genuine community members

### Impact (elizaOS Example)
- **Before filtering**: 1,390 users
- **After filtering**: 1,204 users  
- **Removed**: 186 likely bots/spam
- **Tokens saved**: 78,120 (186 × 420)

### Applied In
1. **Stage 2** of social-airdrop (during user extraction)
2. **Export-airdrop** command (when creating distribution list)

## Export for Airdrop Distribution

After finding eligible users, export to a simple holders/amounts format:

```bash
node src/index.js export-airdrop --input="elizaos_airdrop_eligible.csv"
```

**Output format:**
```csv
holders,amounts
0x86e36c9ba3c6a2542fd761bc2b4fd61a110ea6cd,420
0x869768642eb42ce6b500137036808164f1f28d07,420
0x128a14fbf6c1d3918c7953ebe17d554b44a78152,420
...
```

### Custom Amount
```bash
node src/index.js export-airdrop \
  --input="elizaos_airdrop_eligible.csv" \
  --amount=1000 \
  --output="distribution.csv"
```

### What It Does
- ✅ Extracts only valid wallet addresses (0x... format, 42 chars)
- ✅ Excludes users without verified addresses
- ✅ **Filters out bots/spam** (FID > 1M AND followers < 5)
- ✅ Assigns same amount to each wallet
- ✅ Shows total tokens needed for your budget
- ✅ Ready for merkle trees, multi-send, smart contracts

### Example Output
```
✓ Parsed 1132 valid wallet addresses from 1334 records
   Excluded: 15 (no address), 186 (bots/spam - FID>1M & followers<5)

✓ Airdrop distribution list created!
   File: airdrop_distribution.csv
   Total wallets: 1132
   Amount per wallet: 420
   Total tokens needed: 475,440
```

## Send Airdrop (Execute Distribution)

⚠️ **CAREFUL**: This command sends REAL tokens from your wallet!

### Prerequisites
1. Add `PRIVATE_KEY` to your `.env` file
2. Ensure wallet has enough tokens
3. Ensure wallet has ETH for gas (~0.0001 ETH per transaction)

### Step 1: Dry Run (Safe - Test First!)
```bash
node src/index.js send-airdrop --input="elizaos_distribution_filtered.csv"
```

**Default is dry-run mode** - shows what would happen without sending tokens:
- ✓ Your wallet address
- ✓ Token balance check
- ✓ Total tokens needed
- ✓ Gas estimate
- ✓ First 5 recipients preview
- ❌ **Does NOT send any tokens**

### Step 2: Live Send (Actually Sends Tokens)
```bash
node src/index.js send-airdrop \
  --input="elizaos_distribution_filtered.csv" \
  --no-dry-run
```

**What happens:**
1. Checks your token balance (must have enough)
2. Shows summary: recipients, total cost, gas estimate
3. **Asks for confirmation**: `Type 'yes' to confirm`
4. Sends tokens ONE BY ONE to each address
5. Shows transaction hash for each send
6. Saves progress every 10 transactions
7. Can resume if interrupted

### Safety Features
- ✅ **Dry-run by default** - Must use `--no-dry-run` to actually send
- ✅ **Balance verification** - Won't start if insufficient funds
- ✅ **Confirmation prompt** - Asks "yes" before sending
- ✅ **Progress saving** - Can resume if interrupted
- ✅ **Transaction receipts** - Verifies each send
- ✅ **Skip duplicates** - Won't send to same address twice

### Parameters
- `--input` (required): Distribution CSV file
- `--tokenAddress` (default: elizaOS): Token contract on Base
- `--dryRun` (default: true): Safe mode - use `--no-dry-run` to send

### Example Output
```
💼 Sender Wallet: 0x1c8D40d6E81289B5a1B6e4E3a2E34eA23d1c2A0E
🪙  Token: elizaOS
   Your balance: 500000.0 elizaOS

📊 Airdrop Summary:
   Recipients: 1132
   Amount each: 420 elizaOS
   Total needed: 475440.0 elizaOS
   ✓ Sufficient balance

⚠️  ⚠️  ⚠️  LIVE MODE - REAL TOKENS WILL BE SENT! ⚠️  ⚠️  ⚠️

Type 'yes' to confirm and send tokens: yes

🚀 Starting airdrop...

[1/1132] Sending to 0x86e36c9ba3c6a2542fd761bc2b4fd61a110ea6cd...
  ⏳ Transaction sent: 0xabc123...
  ✓ Confirmed in block 12345678

[2/1132] Sending to 0x5b347cbd34e8195e8b7b0fc3eccfa05cb2e44b76...
  ⏳ Transaction sent: 0xdef456...
  ✓ Confirmed in block 12345679

  💾 Progress saved (10 sent)
...

╔═══════════════════════════════════════════════════════════╗
║                  AIRDROP COMPLETE                        ║
╚═══════════════════════════════════════════════════════════╝

📊 Results:
   ✓ Successful: 1132
   ✗ Failed: 0
   Total sent: 475440.0 elizaOS
```

### Performance
- **Speed**: ~3-4 seconds per transaction (send + confirm + delay)
- **Time for 1,132**: ~60-75 minutes
- **Gas cost**: ~0.0001 ETH per transaction (~0.11 ETH total)
- **Resume**: Automatic if interrupted

## Complete End-to-End Workflow

### Full Example: DEGEN Token Airdrop

```bash
# STEP 1: Find all users who posted about DEGEN (10-30 min)
node src/index.js social-airdrop \
  --ticker="DEGEN" \
  --tokenAddress="0x4ed4e862860bed51a9570b96d89af5e1b0efefed"

# Output: degen_airdrop_eligible.csv
# - All users who posted "$DEGEN"
# - Only those who DON'T have the token
# - Includes: username, wallet, FID, followers
# - Bot filtering applied

# STEP 2: Export to distribution format (instant)
node src/index.js export-airdrop \
  --input="degen_airdrop_eligible.csv" \
  --amount=500

# Output: airdrop_distribution.csv
# - Simple holders,amounts format
# - Bot filtering applied again
# - Shows total tokens needed

# STEP 3: Test with dry run (instant, safe)
node src/index.js send-airdrop \
  --input="airdrop_distribution.csv" \
  --tokenAddress="0x4ed4e862860bed51a9570b96d89af5e1b0efefed"

# Shows:
# - Your wallet address
# - Current token balance
# - Total tokens needed
# - First 5 recipients
# - Does NOT send anything (safe!)

# STEP 4: Verify you have enough funds
# - Check wallet on BaseScan
# - Ensure enough tokens
# - Ensure enough ETH for gas

# STEP 5: Send for real (60+ min, SENDS REAL TOKENS!)
node src/index.js send-airdrop \
  --input="airdrop_distribution.csv" \
  --tokenAddress="0x4ed4e862860bed51a9570b96d89af5e1b0efefed" \
  --no-dry-run

# Will:
# - Ask for confirmation
# - Send tokens one by one
# - Show transaction hashes
# - Save progress (resumable)
# - Complete the airdrop!
```

### Bot Filtering Impact
At each stage, accounts with **FID > 1,000,000 AND followers < 5** are excluded:

**Example (elizaOS):**
- Found: 1,390 users
- After filtering: 1,204 users (Stage 2)
- Valid wallets: 1,132 (export-airdrop)
- **Total filtered: 258 bots/spam/no-address**
- **Tokens saved: ~78,000** (186 bots × 420)

## Available Commands

```bash
# 1. Find eligible users
node src/index.js social-airdrop --help

# 2. Export for distribution
node src/index.js export-airdrop --help

# 3. Send tokens (actual airdrop)
node src/index.js send-airdrop --help

# Show all commands
node src/index.js --help
```

### Command Summary

| Command | Purpose | Output |
|---------|---------|--------|
| `social-airdrop` | Find users who posted about token | `{ticker}_airdrop_eligible.csv` |
| `export-airdrop` | Convert to distribution format | `airdrop_distribution.csv` |
| `send-airdrop` | Send tokens to addresses (LIVE) | Blockchain transactions |

## Requirements for Sending

### .env File Setup
```bash
NEYNAR_API_KEY=your_neynar_key_here
PRIVATE_KEY=0xyour_private_key_here
```

**⚠️ NEVER commit .env file to git!**

### Wallet Requirements (For Send Command)
1. **Tokens**: Enough for all recipients (shown in dry-run)
2. **ETH**: For gas fees (~0.0001 ETH per transaction)
   - Example: 1,000 recipients = ~0.10 ETH for gas

### Safety Tips
1. **Always test with dry-run first** (default behavior)
2. **Verify token contract address** on BaseScan
3. **Start with small test** - Send to 5-10 addresses first
4. **Keep your private key safe** - Never share it
5. **Monitor transactions** on BaseScan during send

## Troubleshooting Send Command

### "Insufficient balance"
```
❌ Insufficient balance!
   Need: 475440.0 elizaOS
   Have: 0.0 elizaOS
```
**Solution**: Buy/transfer enough tokens to your wallet

### "Insufficient funds for gas"
**Solution**: Add more ETH to your wallet (0.15 ETH recommended)

### Transaction failed
- Check you have ETH for gas
- Network might be congested - wait and retry
- Script saves progress - just run again

### Want to resume interrupted send
```bash
# Just run the command again - it skips already-sent addresses
node src/index.js send-airdrop --input="distribution.csv" --no-dry-run
```

## Need Help?

**Commands:**
```bash
node src/index.js --help                # All commands
node src/index.js social-airdrop --help # Find users
node src/index.js export-airdrop --help # Export distribution
node src/index.js send-airdrop --help   # Send tokens
```

**Documentation files in this repo - check them out!**

Happy airdropping! 🎯

