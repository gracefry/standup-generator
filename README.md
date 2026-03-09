# standup-generator

A CLI tool that synthesizes recent work activity (commits, PRs, Jira tickets) into a daily standup summary. Built to explore event-driven architecture, API integration patterns, and team automation tooling.

## The Problem

Standup prep is tedious:
- Scrolling through Jira to remember what you did
- Context-switching between Bitbucket and Jira
- Forgetting to mention PRs you opened

This tool automates that by pulling your activity from Jira and Bitbucket, grouping related work, and generating a markdown summary in seconds.

## Current Approach (V1)

**Focus:** Core activity synthesis with simple grouping.

### How It Works

1. **Fetch from Jira API** → Get issues updated/commented/transitioned by you yesterday
2. **Enrich with Bitbucket** → For each Jira issue, find linked PRs and commits
3. **Group Related Activity** → Link commits → PR → ticket updates into logical units
4. **Generate Markdown** → Output sections for "Yesterday's Work" (closed) and "Today's Work" (open)

### Architecture

```
CLI Input (--date, --jira-url, --bitbucket-token)
    ↓
[Activity Fetcher] → Jira API, Bitbucket API
    ↓
[Event Normalizer] → Standardized event objects
    ↓
[Activity Grouper] → Link commits/PRs/tickets
    ↓
[Standup Summarizer] → Format into sections
    ↓
Markdown Output
```

### Tech Stack

**Key Dependencies:**
- `kotlinx.serialization` - JSON parsing with type safety
- `Fuel` - Lightweight HTTP client
- `Clikt` - CLI argument parsing
- `Gradle` + shadow plugin - Single fat JAR 

## Getting Started

### Prerequisites
- Java 17+
- Jira API token
- Bitbucket App Password (with `repository:read` and `pullrequest:read` scopes)

### Setup

```bash
# Clone and build
./gradlew build

# Run
java -jar build/libs/standup-generator-all.jar \
  --jira-url https://your-domain.atlassian.net \
  --jira-token <token> \
  --bitbucket-workspace <workspace> \
  --bitbucket-username <username> \
  --bitbucket-token <app-password> \
  --date yesterday
```

### Output

Generates `standup-<date>.md`:

```markdown
# Standup Summary - 2026-03-08

## Yesterday's Work
- **[PROJ-123] Fix OAuth bug** (Closed)
  - Commits: `oauth: handle 401 edge case`, `oauth: add token refresh`
  - PR: #456 (Merged)

## Today's Work
- **[PROJ-124] Add rate limiting** (In Progress)
  - PR: #457 (Under Review - 2 approvals)
```

## Considerations / Ideas

### V1 Challenges

1. **Grouping Heuristics Are Simple**
   - Currently links by: Jira ticket → PR → commits
   - **Problem:** What if commits exist without a PR? Or a ticket with no linked PR?
   - **Consideration:** Need better heuristics (branch naming, commit message patterns, time proximity)

2. **No Intelligent Filtering of "Your Work"**
   - Takes a naive date range (yesterday)
   - **Problem:** What about timezone differences? Commits at 11:55 PM? Weekend work?
   - **Consideration:** Move to "since last standup" with better timestamp handling

### V2: Better Work Recognition (Next)
- **Add commit-level activity:** Fetch commits directly from Bitbucket by author + date
- **Smarter grouping:** Use branch names, commit message patterns, time clustering
- **Include code reviews:** Track pull requests you reviewed (not just authored)
- **Configurable date ranges:** "Since last standup" instead of fixed "yesterday"

**Learning Goal:** Understand API pagination, caching strategies, and heuristic-based classification.

### ShipIt ???

These are rough ideas for potential team/company integration:

1. **Slack Integration**
   - Post daily standup to #standup channel
   - Slash command: `/standup` to fetch your summary
   - **Consideration:** Needs to run as a service
   - **Learning:** Slack API, scheduled tasks, environment secrets