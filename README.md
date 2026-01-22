# Claude Code CLI - Complete iOS Development Setup Guide

> A comprehensive guide for setting up Claude Code CLI with PRD-driven workflows, extended thinking (ultrathink), and planning modes optimized for Swift/SwiftUI iOS development.

---

## Table of Contents

1. [Installation](#1-installation)
2. [Configuration Hierarchy](#2-configuration-hierarchy)
3. [CLAUDE.md Setup for iOS Projects](#3-claudemd-setup-for-ios-projects)
4. [PRD-Driven Development Workflow](#4-prd-driven-development-workflow)
5. [Extended Thinking & Ultrathink](#5-extended-thinking--ultrathink)
6. [Plan Mode Configuration](#6-plan-mode-configuration)
7. [XcodeBuildMCP Integration](#7-xcodebuildmcp-integration)
8. [Custom Slash Commands for iOS](#8-custom-slash-commands-for-ios)
9. [Agent Skills](#9-agent-skills)
10. [Subagents Configuration](#10-subagents-configuration)
11. [Output Styles](#11-output-styles)
12. [Plugins System](#12-plugins-system)
13. [Sandbox Mode & Safe Development](#13-sandbox-mode--safe-development)
14. [Settings & Permissions](#14-settings--permissions)
15. [Hooks for Swift Development](#15-hooks-for-swift-development)
16. [Complete Project Structure](#16-complete-project-structure)
17. [Best Practices & Tips](#17-best-practices--tips)

---

## 1. Installation

### Native Installation (Recommended)

```bash
# macOS/Linux - Native installer (no Node.js required)
curl -fsSL https://claude.ai/install.sh | bash

# Or install latest version
curl -fsSL https://claude.ai/install.sh | bash -s latest
```

### NPM Installation (Alternative)

```bash
# Global npm install (do NOT use sudo)
npm install -g @anthropic-ai/claude-code

# Migrate existing npm install to native
claude install
```

### Authentication

```bash
# Start Claude Code and authenticate via OAuth
claude

# Or set API key environment variable
export ANTHROPIC_API_KEY="your-key-here"
```

### Model Selection

```bash
# Use specific model at startup
claude --model claude-opus-4-5-20250929
claude --model claude-sonnet-4-5-20250929
claude --model claude-3-5-haiku-20241022

# Or set default model
export ANTHROPIC_MODEL="claude-sonnet-4-5-20250929"
```

---

## 2. Configuration Hierarchy

Claude Code uses a layered configuration system where each level can override the one below:

```
Priority (Highest to Lowest):
├── 1. Session flags (--model, --permission-mode)
├── 2. Environment variables
├── 3. .claude/settings.local.json (local - personal, gitignored)
├── 4. .claude/settings.json (project - shared with team)
└── 5. ~/.claude/settings.json (user - global)
```

### Configuration Scopes

| Scope | Description | Storage |
|-------|-------------|---------|
| **local** | Available only to you in current project (default for MCP) | `.claude/settings.local.json` |
| **project** | Shared with team via git | `.claude/settings.json`, `.mcp.json` |
| **user** | Available across all your projects | `~/.claude/settings.json` |

### Key Configuration Files

| File | Scope | Git Status | Purpose |
|------|-------|------------|---------|
| `~/.claude/settings.json` | User | N/A | Global preferences |
| `~/.claude/CLAUDE.md` | User | N/A | Global instructions |
| `~/.claude/skills/` | User | N/A | Personal Agent Skills |
| `~/.claude/commands/` | User | N/A | Personal slash commands |
| `.claude/settings.json` | Project | Committed | Team settings |
| `.claude/settings.local.json` | Local | Gitignored | Personal overrides |
| `.mcp.json` | Project | Committed | Shared MCP servers |
| `CLAUDE.md` | Project | Committed | Main project context |
| `.claude/skills/` | Project | Committed | Project Agent Skills |
| `.claude/commands/` | Project | Committed | Project slash commands |
| `.claude/agents/` | Project | Committed | Project subagents |

---

## 3. CLAUDE.md Setup for iOS Projects

The `CLAUDE.md` file is your primary context provider. Claude automatically loads it at session start.

### Root CLAUDE.md Template for iOS

Create `CLAUDE.md` in your project root:

````markdown
# Project: [Your App Name]

## Quick Reference
- **Platform**: iOS 17+ / macOS 14+
- **Language**: Swift 6.0
- **UI Framework**: SwiftUI
- **Architecture**: MVVM with @Observable
- **Minimum Deployment**: iOS 17.0
- **Package Manager**: Swift Package Manager

## XcodeBuildMCP Integration
**IMPORTANT**: This project uses XcodeBuildMCP for all Xcode operations.
- Build: `mcp__xcodebuildmcp__build_sim_name_proj`
- Test: `mcp__xcodebuildmcp__test_sim_name_proj`
- Clean: `mcp__xcodebuildmcp__clean`

## Project Structure
```
MyApp/
├── App/                    # App entry point, App delegate
├── Features/               # Feature modules
│   ├── [FeatureName]/
│   │   ├── Views/          # SwiftUI views
│   │   ├── ViewModels/     # @Observable classes
│   │   └── Models/         # Data models
├── Core/                   # Shared utilities
│   ├── Extensions/
│   ├── Services/
│   └── Networking/
├── Resources/              # Assets, Localizations
└── Tests/
```

## Coding Standards

### Swift Style
- Use Swift 6 strict concurrency
- Prefer `@Observable` over `ObservableObject`
- Use `async/await` for all async operations
- Follow Apple's Swift API Design Guidelines
- Use `guard` for early exits
- Prefer value types (structs) over reference types (classes)

### SwiftUI Patterns
- Extract views when they exceed 100 lines
- Use `@State` for local view state only
- Use `@Environment` for dependency injection
- Prefer `NavigationStack` over deprecated `NavigationView`
- Use `@Bindable` for bindings to @Observable objects

### Navigation Pattern
```swift
// Use NavigationStack with type-safe routing
enum Route: Hashable {
    case detail(Item)
    case settings
}

NavigationStack(path: $router.path) {
    ContentView()
        .navigationDestination(for: Route.self) { route in
            // Handle routing
        }
}
```

### Error Handling
```swift
// Always use typed errors
enum AppError: LocalizedError {
    case networkError(underlying: Error)
    case validationError(message: String)
    
    var errorDescription: String? {
        switch self {
        case .networkError(let error): return error.localizedDescription
        case .validationError(let msg): return msg
        }
    }
}
```

## Testing Requirements
- Unit tests for all ViewModels
- UI tests for critical user flows
- Use Swift Testing framework (`@Test`, `#expect`)
- Minimum 80% code coverage for business logic

## DO NOT
- Write UITests during scaffolding phase
- Use deprecated APIs (UIKit when SwiftUI suffices)
- Create massive monolithic views
- Use force unwrapping (`!`) without justification
- Ignore Swift 6 concurrency warnings

## Planning Workflow
When starting new features:
1. Read the PRD from `docs/PRD.md`
2. Create feature spec in `docs/specs/[feature-name].md`
3. Use `ultrathink` for architectural decisions
4. Use Plan Mode (`Shift+Tab`) for implementation strategy
5. Implement incrementally with tests

## Memory Imports
@import docs/PRD.md
@import docs/ARCHITECTURE.md
@import docs/ROADMAP.md
````

### Nested CLAUDE.md for Feature Directories

Create `.claude/CLAUDE.md` or `Features/[FeatureName]/CLAUDE.md`:

````markdown
# [Feature Name] Module

## Purpose
[Description of what this feature does]

## Architecture
- Uses MVVM with @Observable ViewModels
- Parent stores create child stores for modal presentations

## Navigation Pattern
### Sheet-Based Navigation
**Pattern**: Parent stores create optional child stores for modal presentations
**Rules**:
1. Parent ViewModel holds `@Published var childViewModel: ChildViewModel?`
2. View observes and presents sheet when non-nil
3. Dismissal sets childViewModel to nil

### Example
```swift
@Observable
final class ParentViewModel {
    var detailViewModel: DetailViewModel?
    
    func showDetail(for item: Item) {
        detailViewModel = DetailViewModel(item: item)
    }
}
```

## Testing
Run tests: `mcp__xcodebuildmcp__swift_package_test`
````

---

## 4. PRD-Driven Development Workflow

### Directory Structure for PRD Workflow

```
docs/
├── PRD.md                      # Main Product Requirements Document
├── ARCHITECTURE.md             # System architecture decisions
├── ROADMAP.md                  # Development roadmap & priorities
├── specs/                      # Feature specifications
│   ├── 000-project-setup.md
│   ├── 001-authentication.md
│   ├── 002-dashboard.md
│   └── template.md
└── tasks/                      # Task breakdowns
    ├── 000-sample.md
    └── [feature]-tasks.md
```

### PRD Template (`docs/PRD.md`)

```markdown
# Product Requirements Document: [App Name]

## Executive Summary
[Brief description of the product and its primary value proposition]

## Problem Statement
[What problem does this solve? Who experiences this problem?]

## Target Users
- **Primary**: [Description]
- **Secondary**: [Description]

## Success Metrics
| Metric | Target | Measurement |
|--------|--------|-------------|
| User Retention | 40% D7 | Analytics |
| App Rating | 4.5+ | App Store |
| Crash-Free Rate | 99.5% | Crashlytics |

## Core Features

### Feature 1: [Name]
**Priority**: P0 (Must Have)
**Description**: [Detailed description]
**User Stories**:
- As a [user type], I want [action] so that [benefit]

**Acceptance Criteria**:
- [ ] Criterion 1
- [ ] Criterion 2

**Technical Requirements**:
- iOS 17+ required
- Offline support needed
- Data persistence via SwiftData

### Feature 2: [Name]
[Continue pattern...]

## Non-Functional Requirements
- **Performance**: App launch < 2s, smooth 60fps scrolling
- **Accessibility**: WCAG 2.1 AA compliance
- **Localization**: English (primary), [other languages]
- **Security**: Keychain for credentials, certificate pinning

## Out of Scope (v1.0)
- [Feature explicitly not included]

## Technical Constraints
- Swift 6.0+ with strict concurrency
- SwiftUI-only (no UIKit unless necessary)
- SwiftData for persistence
- Minimum iOS 17.0

## Timeline
| Phase | Duration | Deliverables |
|-------|----------|--------------|
| Design | 2 weeks | Figma mockups |
| Development | 8 weeks | MVP features |
| Testing | 2 weeks | QA sign-off |
| Launch | 1 week | App Store submission |
```

### Feature Spec Template (`docs/specs/template.md`)

````markdown
# Feature Specification: [Feature Name]

**Status**: Draft | In Review | Approved | In Progress | Complete
**Priority**: P0 | P1 | P2
**PRD Reference**: Section [X]
**Author**: [Name]
**Last Updated**: [Date]

## Overview
[Brief description of the feature]

## User Stories
1. As a [user], I want [action] so that [benefit]
2. ...

## Acceptance Criteria
- [ ] AC1: [Specific, testable criterion]
- [ ] AC2: ...

## Technical Design

### Architecture
[How this feature fits into the overall architecture]

### Data Models
```swift
struct FeatureModel: Codable, Identifiable {
    let id: UUID
    // ...
}
```

### API Endpoints (if applicable)
```
GET /api/v1/feature
POST /api/v1/feature
```

### Dependencies
- [ ] Core networking module
- [ ] SwiftData setup

## UI/UX Design
- Figma Link: [URL]
- Key screens: [List]

## Edge Cases
1. [Edge case and how to handle]

## Testing Plan
- Unit tests for ViewModel logic
- UI tests for critical flows
- Performance tests for data loading

## Rollout Plan
- [ ] Feature flag: `feature_[name]_enabled`
- [ ] A/B test configuration

## Open Questions
- [ ] Question 1?
````

### Task File Template (`docs/tasks/feature-tasks.md`)

```markdown
# Tasks: [Feature Name]

**Feature Spec**: `docs/specs/[feature].md`
**Status**: Not Started | In Progress | Complete

## Progress Summary
- Total Steps: X
- Completed: Y
- Current: Step Z

## Steps

### Step 1: [Task Name]
- [ ] Subtask 1
- [ ] Subtask 2
**Notes**: [Implementation notes]

### Step 2: [Task Name]
- [ ] Subtask 1
**Notes**: 

## Changes Log
| Date | Step | Changes |
|------|------|---------|
| YYYY-MM-DD | 1 | Initial implementation |
```

### PRD-Driven Slash Commands

Create `.claude/commands/` with these files:

**`.claude/commands/create-prd.md`**
```markdown
---
description: Create a new PRD from requirements discussion
allowed-tools: Read, Write, Edit
---

# Create Product Requirements Document

Based on our discussion, create a comprehensive PRD in `docs/PRD.md`.

Follow this structure:
1. Executive Summary
2. Problem Statement
3. Target Users
4. Success Metrics
5. Core Features with user stories and acceptance criteria
6. Non-Functional Requirements
7. Technical Constraints
8. Timeline

Ask clarifying questions before writing. Use ultrathink for comprehensive planning.
```

**`.claude/commands/generate-spec.md`**
```markdown
---
description: Generate feature specification from PRD
argument-hint: <feature-name>
allowed-tools: Read, Write
---

# Generate Feature Specification

Read the PRD at `docs/PRD.md` and create a detailed specification for: $ARGUMENTS

1. Extract relevant user stories and requirements
2. Define acceptance criteria
3. Design technical architecture
4. Create the spec file at `docs/specs/$ARGUMENTS.md`

ultrathink about the technical design before writing.
```

**`.claude/commands/generate-tasks.md`**
```markdown
---
description: Break down feature spec into implementable tasks
argument-hint: <feature-name>
allowed-tools: Read, Write
---

# Generate Task Breakdown

Read the specification at `docs/specs/$ARGUMENTS.md` and create a detailed task breakdown.

1. Identify all implementation steps
2. Order by dependencies
3. Estimate complexity
4. Create `docs/tasks/$ARGUMENTS-tasks.md`

Each task should be completable in a single session.
```

---

## 5. Extended Thinking & Ultrathink

Extended thinking is **disabled by default** in Claude Code. You can enable it on-demand.

### Enabling Extended Thinking

| Method | Description |
|--------|-------------|
| **Tab key** | Press `Tab` to toggle Thinking on/off during session |
| **Prompt triggers** | Use phrases like "think", "think hard", "ultrathink" |
| **Environment variable** | Set `MAX_THINKING_TOKENS` for permanent enablement |

### Thinking Budget Hierarchy

Claude Code maps keywords to thinking token budgets:

| Keyword | Budget | Best For |
|---------|--------|----------|
| `think` | ~4K tokens | Simple planning, quick decisions |
| `think hard` / `megathink` / `think more` / `think a lot` | ~10K tokens | Medium complexity |
| `think harder` / `think longer` | ~16K tokens | Complex analysis |
| `ultrathink` | ~32K tokens (max) | Architecture, critical debugging |

### Usage Examples

```
# Simple planning
"Think about how to structure this view"

# Medium complexity  
"Think hard about the navigation architecture for this feature"

# Complex architectural decisions
"Ultrathink about how to design the offline sync system for this app"
```

### Viewing Thinking Process

- Press `Ctrl+O` to toggle verbose mode and see Claude's thinking process
- Thinking appears as *italic gray text* above the response

### When to Use Ultrathink

✅ **Use Ultrathink for:**
- System architecture decisions
- Complex debugging (e.g., race conditions, memory leaks)
- Large-scale refactoring planning
- Migration strategies (UIKit → SwiftUI, CoreData → SwiftData)
- Performance optimization analysis
- Unfamiliar codebase exploration

❌ **Avoid Ultrathink for:**
- Simple code changes
- Well-specified tasks with clear steps
- Rapid prototyping iterations
- Tasks where speed matters more than perfection

### Combining with Plan Mode

```bash
# Start session in Plan Mode with ultrathink
claude --permission-mode plan

# Then in session:
"Ultrathink about how to implement the authentication flow. 
Read the PRD first, then create a comprehensive plan."
```

### Cost Awareness

| Mode | Approx. Cost per Task |
|------|----------------------|
| Standard | ~$0.02-0.05 |
| think | ~$0.06 |
| think hard | ~$0.15 |
| ultrathink | ~$0.30-0.50 |

Use `/cost` command to monitor token usage during sessions.

---

## 6. Plan Mode Configuration

### What is Plan Mode?

Plan Mode instructs Claude to analyze codebases with **read-only operations** - perfect for:
- Multi-step implementation planning
- Code exploration before making changes
- Interactive development with iterative direction refinement

### Enabling Plan Mode

```bash
# Start new session in Plan Mode
claude --permission-mode plan

# Or use -p flag for headless Plan Mode query
claude --permission-mode plan -p "Analyze the authentication system and suggest improvements"
```

### Configure as Default

```json
// .claude/settings.json
{
  "permissions": {
    "defaultMode": "plan"
  }
}
```

### During Session

- Press `Shift+Tab` to cycle: Normal → Auto-Accept → Plan Mode
- Plan Mode indicator: `⏸ plan mode on`
- Auto-Accept indicator: `⏵⏵ accept edits on`
- Plan Mode restricts Claude to read-only operations

### Opus Plan Mode Strategy

Use Opus for planning, Sonnet for execution:

```bash
# Plan with Opus
claude --model claude-opus-4-5-20250929 --permission-mode plan

# Execute with Sonnet (more cost-effective)
claude --model claude-sonnet-4-5-20250929
```

### Plan Mode Slash Command

**`.claude/commands/plan-feature.md`**
```markdown
---
description: Create implementation plan for a feature
argument-hint: <feature-name>
allowed-tools: Read, Grep, Glob
model: claude-opus-4-5-20250929
---

# Plan Feature Implementation

ultrathink and create a comprehensive implementation plan for: $ARGUMENTS

## Steps
1. Read the PRD and feature spec
2. Analyze the existing codebase structure
3. Identify dependencies and integration points
4. Create step-by-step implementation plan
5. Identify potential challenges and solutions
6. Estimate effort for each step

Output the plan to `docs/tasks/$ARGUMENTS-plan.md`

DO NOT write any code. This is planning only.
```

---

## 7. XcodeBuildMCP Integration

XcodeBuildMCP is the essential MCP server for iOS development with Claude Code.

### MCP Transport Types

| Type | Description | Use Case |
|------|-------------|----------|
| **HTTP** | Recommended for remote servers | Cloud services (Notion, GitHub) |
| **SSE** | Server-Sent Events (deprecated) | Legacy remote servers |
| **stdio** | Local process | XcodeBuildMCP, local tools |

### Installation

```bash
# Add XcodeBuildMCP (stdio transport)
claude mcp add --transport stdio XcodeBuildMCP -- npx -y xcodebuildmcp@latest

# With environment variables
claude mcp add --transport stdio XcodeBuildMCP \
  --env INCREMENTAL_BUILDS_ENABLED=true \
  --env XCODEBUILDMCP_DYNAMIC_TOOLS=true \
  -- npx -y xcodebuildmcp@latest

# Specify scope
claude mcp add --transport stdio XcodeBuildMCP --scope project -- npx -y xcodebuildmcp@latest
```

> **Note**: The `--` separates Claude's flags from the server command. Everything after `--` is the command to run the MCP server.

### MCP Scopes

| Scope | Description | Storage |
|-------|-------------|---------|
| **local** (default) | Only you, current project | `~/.claude.json` under project path |
| **project** | Team-shared via git | `.mcp.json` |
| **user** | You, all projects | `~/.claude.json` |

### Manual Configuration (`.mcp.json`)

Create `.mcp.json` in project root for team-shared MCP servers:

```json
{
  "mcpServers": {
    "XcodeBuildMCP": {
      "command": "npx",
      "args": ["-y", "xcodebuildmcp@latest"],
      "env": {
        "INCREMENTAL_BUILDS_ENABLED": "true",
        "XCODEBUILDMCP_SENTRY_DISABLED": "true",
        "XCODEBUILDMCP_DYNAMIC_TOOLS": "true",
        "XCODEBUILDMCP_ENABLED_WORKFLOWS": "simulator,device,project-discovery,swift-package"
      }
    }
  }
}
```

### Environment Variable Expansion

`.mcp.json` supports environment variable expansion:

```json
{
  "mcpServers": {
    "custom-server": {
      "command": "${HOME}/tools/server",
      "env": {
        "API_KEY": "${MY_API_KEY}",
        "BASE_URL": "${API_URL:-https://default.example.com}"
      }
    }
  }
}
```

Syntax:
- `${VAR}` - Expands to variable value
- `${VAR:-default}` - Uses default if VAR not set

### Managing MCP Servers

```bash
# List all configured servers
claude mcp list

# Get details for a specific server
claude mcp get XcodeBuildMCP

# Remove a server
claude mcp remove XcodeBuildMCP

# Check server status (in session)
/mcp

# Authenticate with OAuth-enabled servers
/mcp  # Then select "Authenticate"
```

### MCP Output Limits

```bash
# Set higher limit for large outputs (default: 25,000 tokens)
export MAX_MCP_OUTPUT_TOKENS=50000
claude

# Set MCP server startup timeout (default: 60 seconds)
MCP_TIMEOUT=10000 claude
```

### MCP Resources with @ Mentions

Reference MCP resources using `@server:protocol://resource/path`:

```
> Analyze @github:issue://123 and suggest a fix
> Review the documentation at @docs:file://api/authentication
```

### Available XcodeBuildMCP Tools

| Tool | Description |
|------|-------------|
| `mcp__xcodebuildmcp__discover_projects` | Find Xcode projects/workspaces |
| `mcp__xcodebuildmcp__list_schemes` | List available build schemes |
| `mcp__xcodebuildmcp__build_sim_name_proj` | Build for iOS simulator |
| `mcp__xcodebuildmcp__build_device_proj` | Build for physical device |
| `mcp__xcodebuildmcp__test_sim_name_proj` | Run tests on simulator |
| `mcp__xcodebuildmcp__clean` | Clean build products |
| `mcp__xcodebuildmcp__list_simulators` | List available simulators |
| `mcp__xcodebuildmcp__boot_simulator` | Boot a simulator |
| `mcp__xcodebuildmcp__install_app` | Install app on simulator/device |
| `mcp__xcodebuildmcp__launch_app` | Launch installed app |
| `mcp__xcodebuildmcp__capture_logs` | Capture runtime logs |
| `mcp__xcodebuildmcp__screenshot` | Capture simulator screenshot |
| `mcp__xcodebuildmcp__swift_package_build` | Build Swift package |
| `mcp__xcodebuildmcp__swift_package_test` | Run Swift package tests |
| `mcp__xcodebuildmcp__create_project` | Scaffold new iOS/macOS project |

### Usage in CLAUDE.md

```markdown
## Build Commands
- **Build**: Use `mcp__xcodebuildmcp__build_sim_name_proj` for simulator builds
- **Test**: Use `mcp__xcodebuildmcp__test_sim_name_proj` for running tests
- **Clean**: Use `mcp__xcodebuildmcp__clean` before major rebuilds
- **Logs**: Use `mcp__xcodebuildmcp__capture_logs` to debug runtime issues
```

---

## 8. Custom Slash Commands for iOS

### Project Commands (`.claude/commands/`)

**build.md**
```markdown
---
description: Build the iOS project intelligently
allowed-tools: mcp__xcodebuildmcp__*
---

# Build Project

Detect the project type and build appropriately:
1. Check for .xcworkspace or .xcodeproj
2. Use XcodeBuildMCP to build for iOS Simulator
3. Report any build errors with suggested fixes
```

**test.md**
```markdown
---
description: Run all relevant tests
allowed-tools: mcp__xcodebuildmcp__*, Bash(swift *)
---

# Run Tests

Based on current context:
- For main app: `mcp__xcodebuildmcp__test_sim_name_proj`
- For Swift packages: `mcp__xcodebuildmcp__swift_package_test`
- Report test results and any failures
```

**run-app.md**
```markdown
---
description: Build and launch app on simulator
allowed-tools: mcp__xcodebuildmcp__*
---

# Build and Run

1. List available simulators
2. Build the app for the default simulator (iPhone 15)
3. Boot the simulator if needed
4. Install and launch the app
5. Start capturing logs
```

**create-view.md**
```markdown
---
description: Create a new SwiftUI view with ViewModel
argument-hint: <ViewName>
allowed-tools: Read, Write
---

# Create SwiftUI View: $ARGUMENTS

Create a new SwiftUI view following project patterns:

1. Read existing views for style reference
2. Create `$ARGUMENTS.swift` in appropriate Features directory
3. Create `${ARGUMENTS}ViewModel.swift` as @Observable class
4. Add preview provider
5. Follow project's navigation and styling patterns
```

**refactor-view.md**
```markdown
---
description: Extract and refactor a SwiftUI view
argument-hint: <source-file>
allowed-tools: Read, Write, Edit
---

# Refactor View: $ARGUMENTS

Analyze the view and:
1. Identify extractable subviews (>50 lines or reusable)
2. Extract to separate files
3. Create appropriate ViewModels if needed
4. Ensure proper data flow with bindings
5. Add documentation comments
```

**fix-build.md**
```markdown
---
description: Diagnose and fix build errors
allowed-tools: mcp__xcodebuildmcp__*, Read, Write, Edit, Bash(swift *)
---

# Fix Build Errors

ultrathink about the build errors:

1. Run a clean build to get fresh errors
2. Analyze each error
3. Propose fixes
4. Implement fixes one at a time
5. Verify build succeeds
```

**implement-feature.md**
```markdown
---
description: Implement a feature from spec
argument-hint: <feature-name>
allowed-tools: Read, Write, Edit, mcp__xcodebuildmcp__*
---

# Implement Feature: $ARGUMENTS

Follow the PRD workflow:

1. Read `docs/specs/$ARGUMENTS.md` for requirements
2. Read `docs/tasks/$ARGUMENTS-tasks.md` for task breakdown
3. Implement the current uncompleted task
4. Write tests for the implementation
5. Update task progress in the tasks file
6. Build and test

Stop after completing each task and wait for approval.
```

### Personal Commands (`~/.claude/commands/`)

**swift-style.md**
```markdown
---
description: Check Swift style and conventions
allowed-tools: Bash(swiftlint *), Bash(swift-format *)
---

# Swift Style Check

Run linting and formatting checks:
1. Run SwiftLint if configured
2. Run swift-format check
3. Report violations with suggested fixes
```

---

## 9. Agent Skills

Agent Skills are **model-invoked** capabilities - Claude autonomously decides when to use them based on your request and the Skill's description. Unlike slash commands (user-invoked), Skills activate automatically when relevant.

### Creating Agent Skills

Skills are stored as directories containing a `SKILL.md` file:

| Location | Scope | Use Case |
|----------|-------|----------|
| `~/.claude/skills/skill-name/` | Personal | Your individual workflows |
| `.claude/skills/skill-name/` | Project | Team-shared capabilities |

### Skill Structure

**`.claude/skills/ios-testing/SKILL.md`**
```markdown
---
name: ios-testing
description: iOS testing expert for unit tests, UI tests, and test-driven development. Use when working with XCTest, testing SwiftUI views, or implementing test strategies.
allowed-tools: Read, Grep, Glob, mcp__xcodebuildmcp__test_*
---

# iOS Testing Expert

## Instructions
1. Analyze existing test coverage
2. Identify untested code paths
3. Generate comprehensive test cases
4. Use XCTest and Swift Testing frameworks

## Best Practices
- Test behavior, not implementation
- Use dependency injection for testability
- Mock external dependencies
- Target 80%+ code coverage for critical paths
```

### Skill with Restricted Tools

Use `allowed-tools` to limit Claude's capabilities when the Skill is active:

**`.claude/skills/code-analyzer/SKILL.md`**
```markdown
---
name: code-analyzer
description: Read-only code analysis for architecture review and code quality assessment. Use when reviewing PRs or analyzing codebase structure.
allowed-tools: Read, Grep, Glob
---

# Code Analyzer (Read-Only)

## Review Checklist
1. Code organization and structure
2. Error handling patterns
3. Performance considerations
4. Security concerns
5. Test coverage gaps

Provide detailed feedback without modifying files.
```

### Multi-File Skill

Skills can include supporting files:

```
.claude/skills/swiftui-components/
├── SKILL.md
├── PATTERNS.md
├── REFERENCE.md
└── templates/
    ├── view-template.swift
    └── viewmodel-template.swift
```

**SKILL.md**
```markdown
---
name: swiftui-components
description: SwiftUI component expert for building reusable views, custom modifiers, and view compositions. Use when creating new SwiftUI views or refactoring UI code.
---

# SwiftUI Components

## Quick Start
For standard patterns, see [PATTERNS.md](PATTERNS.md).
For API reference, see [REFERENCE.md](REFERENCE.md).

## Instructions
1. Analyze the required component functionality
2. Check existing components for reuse
3. Apply templates from templates/ directory
4. Follow project styling conventions
```

### Managing Skills

```bash
# View available Skills
> "What Skills are available?"

# Skills activate automatically based on context
> "Help me write tests for the authentication module"
# → iOS testing Skill activates automatically

# Explicitly check Skill files
ls ~/.claude/skills/
ls .claude/skills/
```

### Skill Best Practices

| Practice | Example |
|----------|---------|
| **Focused scope** | "PDF form filling" not "Document processing" |
| **Clear triggers** | Include when to use in description |
| **Specific keywords** | "Use when working with XCTest, testing SwiftUI views" |

---

## 10. Subagents Configuration

Subagents are specialized AI assistants invoked to handle specific task types.

### Creating iOS-Specific Subagents

Subagents are defined in `.claude/agents/` or `~/.claude/agents/`

**`.claude/agents/ios-architect.md`**
```markdown
---
name: ios-architect
description: iOS architecture expert for system design and patterns
model: claude-opus-4-5-20250929
tools: Read, Grep, Glob
---

You are an expert iOS architect specializing in Swift and SwiftUI.

Your expertise includes:
- MVVM, MVC, VIPER, Clean Architecture
- Swift Concurrency (actors, async/await, Sendable)
- SwiftUI navigation patterns
- Dependency injection
- SwiftData and Core Data
- Modular app architecture

When consulted:
1. Analyze the existing codebase structure
2. Consider scalability and maintainability
3. Propose patterns that fit the team's expertise
4. Provide concrete Swift code examples
5. Consider testing implications

Focus on practical, production-ready advice.
```

**`.claude/agents/swift-reviewer.md`**
```markdown
---
name: swift-reviewer
description: Code reviewer for Swift/SwiftUI code quality
model: claude-sonnet-4-5-20250929
tools: Read, Grep
---

You are an expert Swift code reviewer.

Review code for:
- Swift 6 concurrency safety
- Memory management (retain cycles, weak references)
- SwiftUI best practices
- API design guidelines compliance
- Performance considerations
- Test coverage
- Documentation quality

Provide specific, actionable feedback with code examples.
```

**`.claude/agents/swiftui-specialist.md`**
```markdown
---
name: swiftui-specialist
description: SwiftUI expert for complex UI implementation
tools: Read, Write, Edit
---

You are a SwiftUI specialist with deep knowledge of:
- Custom layouts and GeometryReader
- Animations and transitions
- Custom view modifiers
- Preference keys
- Environment and EnvironmentObject
- NavigationStack and navigation patterns
- Performance optimization
- Accessibility

When building UI:
1. Start with the simplest approach
2. Extract reusable components
3. Use proper state management
4. Ensure accessibility
5. Consider all device sizes
```

### Using Subagents

```
# Explicit invocation
"Use the ios-architect to design the data layer"

# Automatic invocation (based on description matching)
"Review this authentication code for security issues"
# Claude may automatically invoke swift-reviewer
```

### Subagent for Research

**`.claude/agents/ios-researcher.md`**
```markdown
---
name: ios-researcher  
description: Research iOS APIs and best practices
tools: WebSearch, WebFetch, Read
---

You are an iOS research specialist.

When researching:
1. Search Apple Developer documentation
2. Find relevant WWDC sessions
3. Check Swift Evolution proposals if relevant
4. Look for community best practices
5. Summarize findings with code examples

Always cite sources and note iOS version requirements.
```

---

## 11. Output Styles

Output Styles adapt Claude Code for different use cases by modifying its system prompt.

### Built-in Output Styles

| Style | Description |
|-------|-------------|
| **Default** | Standard software engineering focus |
| **Explanatory** | Provides educational "Insights" while coding |
| **Learning** | Collaborative learn-by-doing with `TODO(human)` markers |

### Changing Output Style

```bash
# Open style menu
/output-style

# Or switch directly
/output-style explanatory
/output-style learning
```

### Creating Custom Output Styles

Create `.claude/output-styles/` or `~/.claude/output-styles/` files:

**`.claude/output-styles/ios-mentor.md`**
```markdown
---
name: iOS Mentor
description: Teaching-focused style for iOS development with explanations and learning opportunities
keep-coding-instructions: true
---

# iOS Mentor Style

You are an iOS development mentor helping developers learn Swift and SwiftUI.

## Behaviors
1. Explain **why** before showing **how**
2. Reference Apple documentation and WWDC sessions
3. Point out common pitfalls and Swift idioms
4. Suggest further learning resources
5. Add `// 💡 Learn: ...` comments explaining key concepts

## Teaching Approach
- Start with the concept, then show implementation
- Compare approaches when multiple valid options exist
- Highlight Swift 6 and iOS 18 modern patterns
```

### Output Style vs Other Features

| Feature | Purpose | Invocation |
|---------|---------|------------|
| **Output Style** | Changes system prompt behavior | Auto (session-wide) |
| **Slash Commands** | Stored prompts | User types `/command` |
| **Agent Skills** | Specialized capabilities | Auto (model decides) |
| **Subagents** | Task delegation | Auto or explicit |

---

## 12. Plugins System

Plugins extend Claude Code with custom functionality - commands, agents, Skills, hooks, and MCP servers bundled together.

### Installing Plugins

```bash
# Open plugin manager
/plugin

# Install from official marketplace (auto-available)
# Go to Discover tab, select plugin, choose scope

# Add custom marketplace
claude plugin marketplace add owner/repo

# Install directly
claude plugin install plugin-name@marketplace-name
```

### Plugin Scopes

| Scope | Description |
|-------|-------------|
| **user** | Available across all your projects |
| **local** | Only you, current project |
| **project** | Shared via git with team |

### Official Marketplace

The official Anthropic marketplace includes:
- **Code Intelligence** plugins (LSP for Swift, Python, etc.)
- **Workflow** plugins for common tasks
- **Integration** plugins for external services

### Creating a Plugin

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   └── my-command.md
├── agents/
│   └── my-agent.md
├── skills/
│   └── my-skill/
│       └── SKILL.md
├── hooks/
│   └── hooks.json
└── .mcp.json
```

**`.claude-plugin/plugin.json`**
```json
{
  "name": "ios-toolkit",
  "version": "1.0.0",
  "description": "iOS development toolkit with commands, agents, and MCP integration",
  "author": "Your Name"
}
```

### Plugin-Provided MCP Servers

Plugins can bundle MCP servers in `.mcp.json`:

```json
{
  "mcpServers": {
    "plugin-server": {
      "command": "${CLAUDE_PLUGIN_ROOT}/servers/my-server",
      "args": ["--config", "${CLAUDE_PLUGIN_ROOT}/config.json"],
      "env": {
        "DATA_PATH": "${CLAUDE_PLUGIN_ROOT}/data"
      }
    }
  }
}
```

Environment variables:
- `${CLAUDE_PLUGIN_ROOT}` - Plugin directory path
- `${CLAUDE_PROJECT_DIR}` - Project root directory

### Testing Plugins During Development

```bash
# Load plugin from local directory
claude --plugin-dir ./my-plugin

# Load multiple plugins
claude --plugin-dir ./plugin-one --plugin-dir ./plugin-two
```

---

## 13. Sandbox Mode & Safe Development

Claude Code can run in a restricted "sandbox" mode that prevents destructive operations while still allowing Xcode builds and simulator testing. This is ideal for:
- Learning Claude Code safely
- Reviewing AI-generated changes before applying
- Team environments where oversight is required

### Permission Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| **Plan Mode** | Read-only, no file modifications | Architecture planning, code review |
| **Normal Mode** | Asks permission for each action | Default, balanced safety |
| **Auto-Accept Mode** | Executes without confirmation | Trusted, repetitive tasks |

### Sandbox Configuration

Create a restrictive `.claude/settings.json` that allows builds but limits writes:

```json
{
  "model": "claude-sonnet-4-5-20250929",
  "permissions": {
    "allow": [
      "Read",
      "Glob",
      "Grep",
      "mcp__xcodebuildmcp__discover_projects",
      "mcp__xcodebuildmcp__list_schemes",
      "mcp__xcodebuildmcp__list_simulators",
      "mcp__xcodebuildmcp__build_sim_name_proj",
      "mcp__xcodebuildmcp__build_sim_name_ws",
      "mcp__xcodebuildmcp__test_sim_name_proj",
      "mcp__xcodebuildmcp__test_sim_name_ws",
      "mcp__xcodebuildmcp__clean",
      "mcp__xcodebuildmcp__boot_simulator",
      "mcp__xcodebuildmcp__install_app",
      "mcp__xcodebuildmcp__launch_app",
      "mcp__xcodebuildmcp__capture_logs",
      "mcp__xcodebuildmcp__screenshot",
      "mcp__xcodebuildmcp__swift_package_build",
      "mcp__xcodebuildmcp__swift_package_test",
      "Bash(git status)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "Bash(swift build *)",
      "Bash(swift test *)",
      "Bash(swiftlint *)",
      "Bash(xcrun simctl *)"
    ],
    "deny": [
      "Write",
      "Edit",
      "Bash(rm *)",
      "Bash(git push *)",
      "Bash(git commit *)",
      "Bash(git checkout *)",
      "Bash(git reset *)"
    ]
  }
}
```

### Sandbox Workflow

#### 1. Start in Plan Mode
```bash
# Start session in Plan Mode (read-only)
claude --permission-mode plan
```

#### 2. Let Claude Analyze and Plan
```
"Ultrathink about how to implement the login feature. 
Read the codebase, understand the architecture, and create a detailed plan.
DO NOT write any code yet."
```

#### 3. Review the Plan
Claude will analyze your codebase and propose changes without modifying anything.

#### 4. Switch to Normal Mode for Controlled Execution
```bash
# In session, press Shift+Tab twice to cycle to Normal Mode
# Or start a new session without --permission-mode plan
claude
```

#### 5. Apply Changes with Approval
In Normal Mode, Claude will ask for permission before each file modification.

### Graduated Permission Levels

#### Level 1: Full Sandbox (Read + Build Only)
```json
{
  "permissions": {
    "allow": [
      "Read", "Glob", "Grep",
      "mcp__xcodebuildmcp__build_*",
      "mcp__xcodebuildmcp__test_*",
      "mcp__xcodebuildmcp__list_*",
      "mcp__xcodebuildmcp__boot_simulator",
      "mcp__xcodebuildmcp__capture_logs"
    ],
    "deny": ["Write", "Edit"]
  }
}
```
- ✅ Read all files
- ✅ Build projects
- ✅ Run tests
- ✅ Boot simulators and capture logs
- ❌ Cannot create or modify files

#### Level 2: Sandbox + Docs Writing
```json
{
  "permissions": {
    "allow": [
      "Read", "Glob", "Grep",
      "Write(docs/*)",
      "Edit(docs/*)",
      "mcp__xcodebuildmcp__*"
    ],
    "deny": [
      "Write(*.swift)",
      "Edit(*.swift)",
      "Write(*.json)",
      "Write(*.plist)"
    ]
  }
}
```
- ✅ Everything in Level 1
- ✅ Can write PRDs, specs, and documentation
- ❌ Cannot modify Swift code or config files

#### Level 3: Sandbox + Test Files
```json
{
  "permissions": {
    "allow": [
      "Read", "Glob", "Grep",
      "Write(docs/*)",
      "Write(*Tests/*.swift)",
      "Write(*Tests/**/*.swift)",
      "Edit(*Tests/*.swift)",
      "Edit(*Tests/**/*.swift)",
      "mcp__xcodebuildmcp__*"
    ],
    "deny": [
      "Write(*/App/*)",
      "Write(*/Features/*)",
      "Write(*/Core/*)"
    ]
  }
}
```
- ✅ Everything in Level 2
- ✅ Can write and edit test files
- ❌ Cannot modify production code

#### Level 4: Full Development (Normal)
```json
{
  "permissions": {
    "allow": [
      "Read", "Write", "Edit",
      "mcp__xcodebuildmcp__*",
      "Bash(git *)",
      "Bash(swift *)"
    ],
    "deny": [
      "Write(.env*)",
      "Write(**/Secrets.swift)",
      "Bash(rm -rf *)"
    ]
  }
}
```
- ✅ Full development capabilities
- ❌ Cannot touch secrets or run destructive commands

### Sandbox Slash Commands

Create sandbox-specific commands:

**`.claude/commands/sandbox-review.md`**
```markdown
---
description: Review code changes in sandbox mode (read-only)
allowed-tools: Read, Grep, Glob, mcp__xcodebuildmcp__build_*, mcp__xcodebuildmcp__test_*
---

# Sandbox Code Review

Analyze the codebase in read-only mode:

1. Read and understand the current implementation
2. Build the project to verify it compiles
3. Run tests to check current state
4. Identify issues and improvements
5. Output findings as a report

DO NOT modify any files. This is analysis only.
```

**`.claude/commands/sandbox-build.md`**
```markdown
---
description: Build and test without modifying code
allowed-tools: mcp__xcodebuildmcp__*, Read
---

# Sandbox Build & Test

Execute build and test cycle:

1. Clean build products: `mcp__xcodebuildmcp__clean`
2. Build for simulator: `mcp__xcodebuildmcp__build_sim_name_proj`
3. Run all tests: `mcp__xcodebuildmcp__test_sim_name_proj`
4. Report results

No file modifications allowed.
```

**`.claude/commands/sandbox-run.md`**
```markdown
---
description: Build and run app on simulator (sandbox)
allowed-tools: mcp__xcodebuildmcp__*
---

# Sandbox Run App

Build and launch without code changes:

1. List simulators: `mcp__xcodebuildmcp__list_simulators`
2. Boot iPhone 15: `mcp__xcodebuildmcp__boot_simulator`
3. Build app: `mcp__xcodebuildmcp__build_sim_name_proj`
4. Install: `mcp__xcodebuildmcp__install_app`
5. Launch: `mcp__xcodebuildmcp__launch_app`
6. Capture logs: `mcp__xcodebuildmcp__capture_logs`

Report any build errors or runtime issues.
```

### Using Local Settings Override

For personal sandbox preferences without affecting team settings:

**`.claude/settings.local.json`** (gitignored)
```json
{
  "permissions": {
    "deny": ["Write", "Edit"]
  }
}
```

This overrides team settings to enforce sandbox mode for your local environment.

### Sandbox Session Workflow Example

```bash
# Terminal 1: Start Claude Code in sandbox
cd /path/to/ios-project
claude --permission-mode plan

# In Claude Code session:
> /sandbox-review
# Claude analyzes codebase, builds, tests, reports findings

> "What improvements would you suggest for the networking layer? ultrathink"
# Claude provides detailed analysis without modifying code

> "Create a detailed implementation plan for those improvements"
# Claude outputs plan to docs/

# When ready to implement, start new session:
# Terminal 2: Normal mode for implementation
claude
> "Implement step 1 from the networking improvement plan"
# Claude asks permission before each change
```

### Xcode Integration in Sandbox Mode

Even in full sandbox, you can:

| Action | Command | Allowed |
|--------|---------|---------|
| Build project | `mcp__xcodebuildmcp__build_sim_name_proj` | ✅ |
| Run tests | `mcp__xcodebuildmcp__test_sim_name_proj` | ✅ |
| Boot simulator | `mcp__xcodebuildmcp__boot_simulator` | ✅ |
| Install app | `mcp__xcodebuildmcp__install_app` | ✅ |
| Launch app | `mcp__xcodebuildmcp__launch_app` | ✅ |
| Take screenshot | `mcp__xcodebuildmcp__screenshot` | ✅ |
| Capture logs | `mcp__xcodebuildmcp__capture_logs` | ✅ |
| Create project | `mcp__xcodebuildmcp__create_project` | ❌ (if Write denied) |

---

## 14. Settings & Permissions

### Project Settings (`.claude/settings.json`)

```json
{
  "model": "claude-sonnet-4-5-20250929",
  "permissions": {
    "allow": [
      "mcp__xcodebuildmcp__*",
      "Read",
      "Write",
      "Edit",
      "Bash(git *)",
      "Bash(swift *)",
      "Bash(swiftlint *)",
      "Bash(swift-format *)",
      "Bash(xcodegen *)",
      "Bash(pod *)",
      "Bash(mint *)",
      "WebFetch"
    ],
    "deny": [
      "Read(.env*)",
      "Read(**/Secrets.swift)",
      "Write(.env*)",
      "Bash(rm -rf *)"
    ]
  },
  "env": {
    "PROJECT_NAME": "MyApp",
    "DEFAULT_SIMULATOR": "iPhone 15",
    "SWIFT_VERSION": "6.0",
    "IOS_DEPLOYMENT_TARGET": "17.0"
  }
}
```

### Local Settings (`.claude/settings.local.json`)

```json
{
  "model": "claude-opus-4-5-20250929",
  "env": {
    "DEVELOPMENT_TEAM": "YOUR_TEAM_ID",
    "CODE_SIGN_IDENTITY": "Apple Development"
  }
}
```

### Global User Settings (`~/.claude/settings.json`)

```json
{
  "model": "claude-sonnet-4-5-20250929",
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Read",
      "Bash(swift *)"
    ]
  },
  "theme": "dark",
  "preferredEditor": "xcode"
}
```

---

## 15. Hooks for Swift Development

Hooks are user-defined shell commands that execute at various points in Claude Code's lifecycle. They provide **deterministic control** - certain actions always happen rather than relying on the LLM.

### Hook Events

| Event | Trigger | Use Case |
|-------|---------|----------|
| **PreToolUse** | Before tool calls (can block) | Validate commands, protect files |
| **PostToolUse** | After tool calls complete | Format code, run linters |
| **PermissionRequest** | When permission dialog shown | Auto-approve/deny |
| **UserPromptSubmit** | When user submits prompt | Pre-process input |
| **Notification** | When Claude sends notifications | Custom alerts |
| **Stop** | When Claude finishes responding | Summary, cleanup |
| **SubagentStop** | When subagent tasks complete | Post-processing |
| **PreCompact** | Before compact operation | Save context |
| **SessionStart** | When session starts/resumes | Environment setup |
| **SessionEnd** | When session ends | Cleanup, logging |

### Configuration

Add to `.claude/settings.json` or use `/hooks` command:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/session-start.sh"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path' | { read f; [[ \"$f\" == *.swift ]] && swiftlint lint --path \"$f\" --quiet; }"
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "python3 -c \"import json,sys; d=json.load(sys.stdin); p=d.get('tool_input',{}).get('file_path',''); sys.exit(2 if '.env' in p or 'Secrets.swift' in p else 0)\""
          }
        ]
      }
    ]
  }
}
```

### Matcher Patterns

| Pattern | Matches |
|---------|---------|
| `Write` | Exactly "Write" tool |
| `Write\|Edit` | Write OR Edit tools |
| `mcp__xcodebuildmcp__*` | All XcodeBuildMCP tools |
| `*` or `""` | All tools |

### Hook Return Codes

For **PreToolUse** hooks:
- Exit `0`: Continue with tool call
- Exit `2`: Block tool call, provide feedback to Claude

For **PermissionRequest** hooks, output JSON:
```json
{
  "decision": "approve",
  "reason": "Documentation file auto-approved",
  "suppressOutput": true
}
```

### Environment Variables

| Variable | Description |
|----------|-------------|
| `$CLAUDE_PROJECT_DIR` | Project root directory |
| `${CLAUDE_PLUGIN_ROOT}` | Plugin directory (for plugin hooks) |

### Hook Scripts

**`.claude/hooks/session-start.sh`**
```bash
#!/bin/bash

PROJECT_NAME=$(basename "$(pwd)")
SWIFT_VERSION=$(swift --version 2>/dev/null | head -1 | grep -oE '[0-9]+\.[0-9]+' | head -1)
XCODE_VERSION=$(xcodebuild -version 2>/dev/null | head -1)

echo "🚀 Starting $PROJECT_NAME session" >&2
echo "📱 Swift $SWIFT_VERSION | $XCODE_VERSION" >&2

# Check for common issues
if ! command -v swiftlint &> /dev/null; then
    echo "⚠️  SwiftLint not installed" >&2
fi

# Check if simulator is booted
if ! xcrun simctl list devices booted | grep -q "Booted"; then
    echo "💡 No simulator running. Use /run-app to boot one." >&2
fi
```

**`.claude/hooks/post-swift-edit.sh`**
```bash
#!/bin/bash

# Read file path from stdin JSON
FILE=$(jq -r '.tool_input.file_path // empty')

if [[ "$FILE" != *.swift ]]; then
    exit 0
fi

# Run SwiftLint on the edited file
if command -v swiftlint &> /dev/null && [ -f ".swiftlint.yml" ]; then
    swiftlint lint --path "$FILE" --quiet 2>&1 | head -5 >&2
fi

# Run swift-format check
if command -v swift-format &> /dev/null; then
    swift-format lint "$FILE" 2>&1 | head -3 >&2
fi
```

**`.claude/hooks/file-protection.sh`** (PreToolUse)
```bash
#!/bin/bash

# Block edits to sensitive files
FILE=$(jq -r '.tool_input.file_path // empty' < /dev/stdin)

PROTECTED_PATTERNS=(
    ".env"
    "Secrets.swift"
    "GoogleService-Info.plist"
    ".git/"
    "Podfile.lock"
)

for pattern in "${PROTECTED_PATTERNS[@]}"; do
    if [[ "$FILE" == *"$pattern"* ]]; then
        echo "🛡️ Protected file: $FILE" >&2
        exit 2  # Block the operation
    fi
done

exit 0  # Allow the operation
```

### Setup via `/hooks` Command

```bash
# Interactive hook configuration
/hooks

# Select event type (e.g., PostToolUse)
# Add matcher (e.g., "Write|Edit")
# Add hook command
# Choose storage location (User/Project)
```

Make hooks executable:
```bash
chmod +x .claude/hooks/*.sh
```

---

## 16. Complete Project Structure

```
MyiOSApp/
├── .claude/
│   ├── commands/                    # Project slash commands
│   │   ├── build.md
│   │   ├── test.md
│   │   ├── run-app.md
│   │   ├── create-view.md
│   │   ├── implement-feature.md
│   │   ├── create-prd.md
│   │   ├── generate-spec.md
│   │   ├── sandbox-build.md
│   │   └── sandbox-review.md
│   ├── agents/                      # Project subagents
│   │   ├── ios-architect.md
│   │   ├── swift-reviewer.md
│   │   └── swiftui-specialist.md
│   ├── skills/                      # Agent Skills
│   │   ├── ios-testing/
│   │   │   └── SKILL.md
│   │   └── swiftui-components/
│   │       ├── SKILL.md
│   │       └── templates/
│   ├── output-styles/               # Custom output styles
│   │   └── ios-mentor.md
│   ├── hooks/                       # Automation hooks
│   │   ├── session-start.sh
│   │   ├── post-swift-edit.sh
│   │   └── file-protection.sh
│   ├── settings.json                # Team settings (committed)
│   ├── settings.local.json          # Personal settings (gitignored)
│   └── settings.sandbox.json        # Sandbox mode template
├── .mcp.json                        # MCP server configuration
├── CLAUDE.md                        # Main project context
├── docs/
│   ├── PRD.md                       # Product Requirements Document
│   ├── ARCHITECTURE.md              # Architecture decisions
│   ├── ROADMAP.md                   # Development roadmap
│   ├── specs/                       # Feature specifications
│   │   ├── template.md
│   │   ├── 001-authentication.md
│   │   └── 002-dashboard.md
│   └── tasks/                       # Task breakdowns
│       └── authentication-tasks.md
├── MyApp/
│   ├── App/
│   │   └── MyAppApp.swift
│   ├── Features/
│   │   ├── Authentication/
│   │   │   ├── CLAUDE.md            # Feature-specific context
│   │   │   ├── Views/
│   │   │   └── ViewModels/
│   │   └── Dashboard/
│   ├── Core/
│   │   ├── Extensions/
│   │   ├── Services/
│   │   └── Networking/
│   └── Resources/
├── MyAppTests/
├── MyAppUITests/
├── Package.swift                    # If using SPM
├── .swiftlint.yml                   # SwiftLint configuration
├── .swift-format                    # Swift-format configuration
└── .gitignore
```

### Recommended `.gitignore` additions

```gitignore
# Claude Code
.claude/settings.local.json
.claude/*.log

# Keep these committed
!.claude/commands/
!.claude/agents/
!.claude/hooks/
!.claude/settings.json
```

---

## 17. Best Practices & Tips

### Workflow Best Practices

1. **Start with Planning**
   - Use Plan Mode for new features
   - Write specs before code
   - Use `ultrathink` for architectural decisions

2. **Incremental Development**
   - Complete one task at a time
   - Test after each change
   - Commit frequently

3. **Context Management**
   - Use `/compact` when context grows large
   - Use `/clear` between unrelated tasks
   - Keep CLAUDE.md concise but comprehensive

4. **Subagent Usage**
   - Use `ios-architect` subagent early for design decisions
   - Use `swift-reviewer` subagent before PRs
   - Spin up research subagents for unfamiliar APIs

### Prompt Patterns for iOS Development

```
# Starting a new feature
"Read the PRD and spec for [feature]. Ultrathink about the implementation 
approach, then create a task breakdown in docs/tasks/"

# Code implementation
"Implement step 2 from docs/tasks/[feature]-tasks.md. Write tests first, 
then implementation. Build and run tests when complete."

# Debugging
"The app crashes when [action]. Use ultrathink to analyze the issue. 
Capture logs with XcodeBuildMCP and identify the root cause."

# Refactoring
"This view is too large. Think hard about how to extract it into 
smaller components while maintaining the same behavior."

# Architecture review
"Use the ios-architect to review the current data layer design 
and suggest improvements for offline support."
```

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Tab` | Toggle extended thinking on/off |
| `Shift+Tab` | Cycle permission modes (Normal → Auto-Accept → Plan) |
| `Ctrl+C` | Cancel current operation |
| `Ctrl+O` | Toggle verbose mode (see thinking process) |
| `/` | Open slash command menu |
| `@` | Reference files, directories, or MCP resources |

### Common Commands Reference

| Command | Description |
|---------|-------------|
| `/help` | Show all available commands |
| `/clear` | Clear conversation context |
| `/compact` | Summarize and compress context |
| `/cost` | Show token usage and cost |
| `/model` | Change model mid-session |
| `/config` | Open configuration interface |
| `/agents` | Manage subagents |
| `/hooks` | Configure hooks |
| `/mcp` | Manage MCP servers |
| `/plugin` | Plugin manager (discover, install, manage) |
| `/output-style` | Change output style |
| `/status` | Show session status and imports |

### Resuming Conversations

```bash
# Continue most recent conversation
claude --continue

# Show conversation picker
claude --resume

# Continue with a specific prompt
claude --continue --print "Continue with my task"
```

### @ References

```bash
# Reference a file
> Explain the logic in @src/Features/Auth/AuthViewModel.swift

# Reference a directory
> What's the structure of @MyApp/Features?

# Reference with line ranges (select in editor + Alt+K)
> Review @src/login.swift:45-67

# Reference MCP resources
> Show me @github:issue://123
```

### Performance Tips

1. **Use Incremental Builds**
   - Enable `INCREMENTAL_BUILDS_ENABLED=true` in XcodeBuildMCP
   - Significantly faster iteration cycles

2. **Minimize Context**
   - Don't load unnecessary files
   - Use specific imports in CLAUDE.md
   - Compact regularly on long sessions

3. **Choose the Right Model**
   - Opus for planning and architecture
   - Sonnet for most implementation work
   - Haiku for simple, repetitive tasks

4. **Batch Related Changes**
   - Group related file changes together
   - Reduces build-test cycles

### Troubleshooting

```bash
# Check Claude Code health
claude doctor

# Debug MCP connections
claude --mcp-debug

# View session logs
tail -f ~/.claude/logs/session.log

# Reset configuration
rm -rf ~/.claude && claude
```

---

## Quick Start Checklist

- [ ] Install Claude Code CLI (native method)
- [ ] Authenticate with Claude account
- [ ] Install XcodeBuildMCP
- [ ] Create project `.claude/` directory structure
- [ ] Create root `CLAUDE.md` with project context
- [ ] Create `.mcp.json` with XcodeBuildMCP configuration
- [ ] Create essential slash commands
- [ ] Set up hooks for Swift linting
- [ ] Create `docs/` structure for PRD workflow
- [ ] Write initial PRD
- [ ] Configure permissions in `settings.json`
- [ ] Test build command: `/build`

---

## Resources

- [Claude Code Official Documentation](https://code.claude.com/docs)
- [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)
- [XcodeBuildMCP GitHub](https://github.com/cameroncooke/XcodeBuildMCP)
- [Apple Swift API Design Guidelines](https://www.swift.org/documentation/api-design-guidelines/)
- [Swift Evolution Proposals](https://github.com/apple/swift-evolution)

---

*Last Updated: January 2026*
*Claude Code Version: 2.0.x*
