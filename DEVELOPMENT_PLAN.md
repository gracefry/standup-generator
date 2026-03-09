# Development Plan - Standup Generator V1

## Phase 0: Spike (Understand the Problem)

**Goal:** Learn what Jira and Bitbucket APIs actually return 

### Tasks
1. Set up Gradle project + basic structure
2. Create script to call Jira API, inspect issue/PR data
   - Understand response structure for issues
   - Check how Jira links PRs (dev panel)
   - Note fields we care about: status, assignee, updated date, etc.
3. Create script to call Bitbucket API, inspect PR/commit data
   - Understand PR response structure
   - Understand commit response structure
   - Check how to query by author/date
4. Sketch domain model
   - What entities? (Issue, PullRequest, Commit, WorkItem?)

## Phase 1: Build API Clients

### Tasks - Jira Client
1. Create data classes for Jira API responses
2. Create Jira API client interface
3. Implement Jira client + integration tests

### Tasks - Bitbucket Client
Same as above roughly