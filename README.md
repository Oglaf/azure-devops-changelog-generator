# Azure DevOps Release Changelog Generator

An AI-powered skill that generates structured release documentation from Azure DevOps TFVC changesets or Git commits by retrieving associated work items and analyzing their full context.

## Features

- **Organization-agnostic**: Works with any Azure DevOps organization
- **Supports TFVC and Git**: Works with both version control systems
- **Project-based grouping**: Groups changes by Azure DevOps project name
- **Rich context analysis**: Reads work item titles, descriptions, and discussion comments to generate accurate summaries
- **AI-generated summaries**: Produces concise, meaningful descriptions of what changed

## Usage

This skill is triggered when you mention:
- TFVC changesets
- Git commits
- Azure DevOps work items
- "generate changelog", "cards.md", or "release notes"

The skill will ask for:
1. Your Azure DevOps organization name
2. Whether you're using TFVC or Git

## Requirements

- Azure DevOps Personal Access Token (PAT) stored in `$env:DEVOPS_PAT`
- PowerShell 5.1+

## Install

Install the skill locally using `npx skills` (requires Node.js / npm):

```powershell
# install from local path
npx skills install ./skills/azure-devops-changelog-generator
```

You can also install directly from GitHub.

```powershell
# GitHub shorthand (if supported by the CLI)
npx skills install github:Oglaf/azure-devops-changelog-generator

# HTTPS git URL
npx skills install https://github.com/Oglaf/azure-devops-changelog-generator.git
```

## Project Structure

```
├── skills/
│   └── azure-devops-changelog-generator/
│       ├── SKILL.md              # Skill definition
│       ├── scripts/              # PowerShell helper scripts
│       │   ├── fetch-workitems.ps1
│       │   ├── generate-cards.ps1
│       │   └── generate-changelog.ps1
│       └── evals/               # Evaluation data
└── README.md
```

## Quick Start

1. Open PowerShell and set your Azure DevOps Personal Access Token (PAT):

```powershell
$env:DEVOPS_PAT = 'your-personal-access-token'
$Organization = 'your-org-name'
```

2. Fetch work items (example):

```powershell
& .\skills\azure-devops-changelog-generator\scripts\fetch-workitems.ps1 -WorkItemIds "10828,11075" -Organization $Organization -OutputDir "C:\Temp"
```

3. Generate `cards.md` and `changelog.md` from the fetched `workitems.json`:

```powershell
& .\skills\azure-devops-changelog-generator\scripts\generate-cards.ps1 -InputFile "C:\Temp\workitems.json" -Organization $Organization -OutputFile "C:\Temp\cards.md"
& .\skills\azure-devops-changelog-generator\scripts\generate-changelog.ps1 -InputFile "C:\Temp\workitems.json" -Organization $Organization -OutputFile "C:\Temp\changelog.md"
```

Outputs are written to the directory specified by `-OutputDir` / `-OutputFile` (examples above use `C:\Temp`).


## Scripts

### fetch-workitems.ps1
Fetches work items from Azure DevOps API.

```powershell
& ./scripts/fetch-workitems.ps1 -WorkItemIds "10828, 11075" -Organization "myorg" -OutputDir "C:\Temp"
```

### generate-cards.ps1
Generates cards.md from workitems.json.

```powershell
& ./scripts/generate-cards.ps1 -InputFile "C:\Temp\workitems.json" -Organization "myorg" -OutputFile "C:\Temp\cards.md"
```

### generate-changelog.ps1
Generates changelog.md from workitems.json, grouped by project.

```powershell
& ./scripts/generate-changelog.ps1 -InputFile "C:\Temp\workitems.json" -Organization "myorg" -OutputFile "C:\Temp\changelog.md"
```

## Output Format

### cards.md
Contains detailed card information including:
- Work item ID, title, and type
- State and assignee
- Project name
- Link to work item
- AI-generated summary from title + description + comments

### changelog.md
Release-style changelog grouped by Azure DevOps project:
- Lists projects involved in the release
- Groups items by project
- Categorizes as New Features, Bug Fixes, or Improvements

## Example

Input:
```
4488	Developer Name	1/24/2026 4:45:26 AM	CARDS 12345, 12346, 12347, 12348, 12349 - main ***NO_CI***
```

Output (changelog.md):
```markdown
# Release Changelog

This release includes changes from the following Azure DevOps projects:

- ProjectName1
- ProjectName2

---

## ProjectName1

### Bug Fixes

- **12348** – Bug title here
  Bug fix description here...

---

## ProjectName2

### New Features

- **12345** – Feature title here
  Feature description here...
```

## License

MIT
