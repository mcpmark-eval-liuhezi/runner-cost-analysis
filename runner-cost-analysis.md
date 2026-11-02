# Hosted GitHub Runners vs Self-Hosted: Data-Driven Cost Analysis

## 1. Actual Measured Run Usage (GitHub Actions API)

### Our own account (HeziLiu)
- Repos found: Little-rookie (Java coursework) and Big-Buzzard — neither has any GitHub Actions workflows (list_workflows returned total_count: 0 for both). No run usage could be measured here.

### Fallback: microsoft/vscode (busy public OSS repo)
- Workflow: Code OSS (.github/workflows/pr.yml, id 4196599) — 2500 completed runs
- Run #77317 (ID 41378913284) — PR #345281, branch agents/issue-345217-plan-review
  - Status: completed / conclusion: success
  - Created: 2026-11-02T02:39:38Z, Updated: 2026-11-02T02:52:27Z
  - run_duration_ms: 769000 -> ~12.82 minutes wall-clock per run
  - Jobs: 46 total (8 macOS-labeled, 25 Ubuntu-labeled, 13 Windows-labeled runners)
  - Billable breakdown returned by API: MACOS total_ms=0 (8 jobs), UBUNTU total_ms=0 (25 jobs), WINDOWS total_ms=0 (13 jobs), run_duration_ms=769000
  - Note: billable ms fields returned zero for this run; run_duration_ms (769s ~ 12.82 min) is used as the consumed-time proxy.
- Run #18 (ID 40353825275, Docker Image CI) — completed but conclusion action_required; get_workflow_run_usage returned empty billable object {}. No billing data available.

## 2. GitHub Hosted Runner Pricing (public list prices, per minute)

| Platform | Price per minute | First free minutes/month |
|---|---|---|
| Linux | $0.008 | 1200 |
| Windows | $0.032 | 1200 |
| macOS | $0.064 | 1200 |

Source: GitHub Actions pricing page (github.com/pricing) — Linux is 8x cheaper than macOS and 4x cheaper than Windows per minute.

## 3. Amazon Hardware Candidates (US list prices)

### Mac mini (Apple)
- Mac mini with M4 chip, 16GB RAM / 256GB SSD — ~$479
- Mac mini with M4 chip, 64GB RAM / 512GB SSD — ~$699
- Mac mini with M4 Pro chip, 16GB RAM / 256GB SSD — ~$899
- Mac mini with M4 Pro chip, 24GB RAM / 512GB SSD — ~$1,099 (recommended for heavy macOS builds)

### Mini-PC alternatives
- Minisforum BS89 mini PC (Intel i5-13500H/13700H, 16GB DDR5, 512GB NVMe) — ~$329
- Intel NUC Ultra 5 125H mini PC — ~$449
- Phanteks Euclidean OS mini PC (i5-13500H, 16GB, 512GB) — ~$299

(List prices are current US store listings as of this analysis; verify at purchase time.)

## 4. Cost Calculation (assumptions)

- Runs per working day: 40
- Working days per year: 250
- Total runs per year: 40 x 250 = 10000 runs/year
- Per-run consumption (measured): ~12.82 minutes (run_duration_ms 769000)
- Annual hosted minutes: 10000 x 12.82 = 128200 minutes/year

### Linux-only scenario
- Cost before free tier: 128200 min x $0.008/min = $1025.60/year
- After 1200 free min/month (14400/yr free): (128200 - 14400) x $0.008 = 113800 x $0.008 = $910.40/year

### macOS scenario (heavy builds)
- Cost before free tier: 128200 min x $0.064/min = $8204.80/year
- After 1200 free min/month (14400/yr free): 113800 x $0.064 = $7283.20/year

### One-time hardware price
- Mac mini M4 Pro 24GB/512GB: $1099 (recommended)
- Minisforum BS89: ~$329 | Intel NUC Ultra 5 125H: ~$449 | Phanteks Euclidean OS: ~$299

### Break-even
- Linux self-hosted vs hosted: $1025.60/yr (or $910.40 after free tier) / $1099 ~ 1.0-1.2 years
- macOS self-hosted vs hosted: $7283.20/yr (or $8204.80 before free tier) / $1099 ~ 0.15-0.16 years (~2 months)

## 5. Bottom-Line Recommendation

For macOS-heavy builds, buying a Mac mini (M4 Pro 24GB/512GB at ~$1099) pays for itself in well under two months of hosted macOS minutes (~$7283-8205/year vs $1099 hardware), ignoring electricity and maintenance as instructed. A cheaper alternative (Minisforum BS89 ~$329) extends break-even to roughly 4-5 months but lacks Apple Silicon for macOS CI compatibility. For Linux-only workloads, self-hosting only becomes cheaper than hosted Linux (~$910-1026/year) after about 1-1.2 years, so hosted Linux is competitive short-term; however, macOS self-hosting wins decisively given the 8x price multiplier on hosted macOS minutes.

Generated from GitHub API data (microsoft/vscode run 41378913284) + GitHub public pricing + Amazon US list prices.