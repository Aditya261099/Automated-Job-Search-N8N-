# 🤖 Automated Job Search & Application Tracker

An intelligent n8n workflow that automates job searching, scraping, AI-powered analysis, and notification system for streamlined job hunting on Indeed and other job platforms.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Use Cases](#use-cases)
- [Workflow Architecture](#workflow-architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Node Breakdown](#node-breakdown)
- [Usage](#usage)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This n8n workflow automates the tedious process of job searching by continuously monitoring job boards, extracting relevant job postings, analyzing them with AI, and organizing results in a spreadsheet. Built for developers and job seekers who want to optimize their job search process and never miss opportunities.

**Key Highlights:**
- 🔄 Automated scheduled job searches
- 🤖 AI-powered job description analysis
- 📊 Automatic data logging to Google Sheets
- 📱 Real-time Telegram notifications
- 📄 PDF resume parsing and extraction
- 🔍 Smart pagination handling for multiple search pages
- ⏱️ Configurable delays to avoid rate limiting

## ✨ Features

### Core Functionality
1. **Scheduled Execution**: Runs automatically at defined intervals to check for new job postings
2. **Dynamic Search URL Generation**: Creates search queries based on configurable parameters (location, keywords, experience level)
3. **Web Scraping**: Fetches and parses job listing pages from Indeed and similar platforms
4. **Job Link Extraction**: Identifies and extracts individual job posting URLs from search results
5. **Detailed Job Scraping**: Navigates to each job posting to extract complete details
6. **AI Analysis**: Uses OpenRouter Chat Model to analyze job descriptions and extract key information
7. **Data Storage**: Automatically logs job data to Google Sheets for tracking and analysis
8. **Telegram Alerts**: Sends instant notifications when relevant jobs are found
9. **PDF Resume Processing**: Extracts and analyzes resume content for job matching
10. **Pagination Management**: Automatically handles multi-page search results

### Advanced Features
- **Loop-based Processing**: Efficiently processes multiple jobs using the "Loop Over Items" node
- **Conditional Logic**: Filters jobs based on scoring criteria
- **Wait/Delay Mechanism**: Implements strategic delays to prevent rate limiting
- **Error Handling**: Built-in error management for robust operation

## 🎯 Use Cases

### 1. Active Job Seeker
**Scenario**: You're actively looking for full-stack developer positions in specific locations.

**How it helps**: 
- Set up search parameters once (keywords: "Full Stack Developer", location: "San Francisco", etc.)
- Workflow runs every 6 hours automatically
- New matching jobs are scraped, analyzed, and added to your tracking sheet
- Get Telegram notifications for high-priority matches
- Review organized data in Google Sheets with AI-extracted key details

### 2. Passive Job Monitoring
**Scenario**: You're employed but want to stay aware of market opportunities.

**How it helps**:
- Configure conservative search parameters for senior or highly-paid roles
- Run workflow once daily or weekly
- Only get notified about premium opportunities that match your criteria
- Build a database of market trends and salary ranges over time

### 3. Recruiter/Talent Sourcer
**Scenario**: You need to source candidates or understand job market trends.

**How it helps**:
- Monitor competitor job postings
- Track which companies are hiring for specific roles
- Analyze job description trends and required skills
- Build comprehensive market intelligence

### 4. Career Researcher
**Scenario**: You want to understand what skills and qualifications are in demand.

**How it helps**:
- Collect large datasets of job postings in your field
- Use AI to extract common requirements and skills
- Identify gaps in your resume or areas for skill development
- Track how job requirements evolve over time

### 5. Relocation Planning
**Scenario**: You're planning to move to a new city and want to gauge job availability.

**How it helps**:
- Run searches for multiple target cities simultaneously
- Compare job availability and requirements across locations
- Build a database before you relocate
- Understand local market conditions and salary expectations


## 📦 Prerequisites

### Required Accounts & Services
- **n8n Instance**: Self-hosted or n8n Cloud (v1.97.1 or higher)
- **Google Sheets**: For data storage and search parameter management
- **Telegram Bot**: For notifications (optional but recommended)
- **OpenRouter Account**: For AI-powered job analysis
- **File Storage**: For resume PDF processing (optional)

### Required n8n Nodes
All nodes used in this workflow are available in standard n8n installation:
- HTTP Request
- HTML Extract
- Code (JavaScript)
- Loop Over Items
- If/Switch
- Wait
- Google Sheets
- Telegram
- AI Agent (OpenRouter Chat Model)
- Schedule Trigger
- PDF Extract

## 🚀 Installation

### Step 1: Import Workflow

1. Export your workflow as JSON in n8n:
   - Open the workflow in n8n
   - Click the **⋯** (Options) menu → **Export** → **JSON**

2. Import into your n8n instance:
   - Go to **Workflows** → Click **Import from File**
   - Select the downloaded JSON file
   - Click **Import**

### Step 2: Set Up Google Sheets

Create a new Google Sheet with two tabs:

**Tab 1: "Search Params"**
| search_query | location | experience_level | max_pages |
|--------------|----------|------------------|-----------|
| Full Stack Developer | Remote | Mid-Level | 3 |

**Tab 2: "Job Results"**
| timestamp | job_title | company | location | url | salary | description | skills | ai_score |
|-----------|-----------|---------|----------|-----|--------|-------------|--------|----------|

### Step 3: Configure Credentials

Set up the following credentials in n8n:

1. **Google Sheets API**
   - Follow n8n's [Google Sheets authentication guide](https://docs.n8n.io/integrations/builtin/credentials/google/)
   - Grant necessary permissions

2. **Telegram Bot** (Optional)
   - Create a bot via [@BotFather](https://t.me/botfather)
   - Get your Chat ID
   - Add credentials in n8n

3. **OpenRouter API**
   - Sign up at [OpenRouter](https://openrouter.ai/)
   - Generate API key
   - Add to n8n credentials

## ⚙️ Configuration

### Schedule Trigger Node
- **Interval**: Default is every 6 hours
- **Recommendation**: For active job search, run every 4-6 hours. For passive monitoring, daily is sufficient.

```javascript
// Modify trigger schedule
Mode: "Interval"
Hours Between Triggers: 6
```
Search Parameters (Google Sheets)
Customize your search criteria in the "Search Params" sheet:

search_query: Job title or keywords (e.g., "Python Developer", "Data Scientist")

location: City, state, or "Remote"

experience_level: Entry, Mid-Level, Senior, Lead

max_pages: Number of search result pages to process (1-10 recommended)

AI Agent Configuration
The AI Agent uses a Chat Model to analyze job descriptions. Customize the prompt to extract specific information:
```
// Example prompt modification
"Analyze this job description and extract:
1. Required technical skills
2. Years of experience needed
3. Salary range (if mentioned)
4. Remote work policy
5. Company culture indicators
6. Red flags or concerns
Provide a match score from 1-10 based on [YOUR CRITERIA]"
```
Score Filter Node
Adjust the threshold score to control which jobs get logged:
```javascript
// Modify score threshold
if (score >= 7) {
  // Log to sheet and notify
} else {
  // Skip
}
```
🔧 Node Breakdown
1. Schedule Trigger
Purpose: Initiates workflow execution automatically
Configuration: Interval-based (default: every 6 hours)
Best Practice: Adjust based on job search urgency

2. Get Search Params
Purpose: Retrieves search configuration from Google Sheets
Type: Google Sheets Read operation
Output: Search query, location, experience level, max pages

3. Create Search URL
Purpose: Constructs Indeed search URL with query parameters
Type: Code (JavaScript)
Logic:
```javascript
const baseUrl = "https://www.indeed.com/jobs";
const query = $input.first().json.search_query;
const location = $input.first().json.location;
// URL encoding and parameter concatenation
```
4. Fetch Search Page
Purpose: Makes HTTP request to load job search results
Type: HTTP Request (GET)
Headers: User-Agent, Accept
Rate Limiting: Implement delays between requests

5. Extract Job Links
Purpose: Parses HTML to identify job posting URLs
Type: HTML Extract
Selector: CSS selectors for Indeed job cards
Output: Array of job URLs

6. Loop Over Items
Purpose: Iterates through each extracted job link
Type: Loop Over Items
Behavior: Processes one job at a time sequentially

7. Fetch Job Page
Purpose: Loads individual job posting page
Type: HTTP Request (GET)
Input: Job URL from loop
Output: Full HTML of job page

8. Parse Job HTML
Purpose: Extracts structured data from job page HTML
Type: HTML Extract
Extracted Fields:

Job title

Company name

Location

Salary (if available)

Job description

Apply URL

9. AI Agent
Purpose: Analyzes job description using LLM
Type: AI Agent (OpenRouter Chat Model)
Model: Recommended - GPT-4o, Claude 3.5 Sonnet, or similar
Output: Structured analysis with skills, requirements, match score

10. Parse AI Output
Purpose: Converts AI response to structured JSON
Type: Code (JavaScript)
Logic: JSON parsing and field extraction

11. Score Filter
Purpose: Determines if job meets quality threshold
Type: If/Switch node
Condition: score >= 7 (adjustable)

12. Wait
Purpose: Implements delay to avoid rate limiting
Type: Wait
Duration: 2-5 seconds between requests
Critical: Prevents IP blocking

13. Write to Sheet
Purpose: Logs job data to Google Sheets
Type: Google Sheets (Append)
Sheet: "Job Results" tab
Fields: All extracted and analyzed data

14. Telegram Alert
Purpose: Sends real-time notification for high-value jobs
Type: Telegram (Send Message)
Format: Markdown with job title, company, link, and score

15. Page Manager & Increment Page
Purpose: Handles pagination for multi-page results
Type: Code + Counter logic
Logic: Increments page number and loops back to fetch next page

16. Download File & Extract from PDF
Purpose: Processes resume PDFs for matching
Type: HTTP Request + PDF Extract
Use Case: Compare resume skills against job requirements

📖 Usage
Running the Workflow
Automatic Execution:
The workflow runs automatically based on the Schedule Trigger configuration. No manual intervention needed once set up [web:13].

Manual Execution:

Open workflow in n8n

Click Execute Workflow button

Monitor execution progress in real-time

Check Executions tab for logs and debugging

Monitoring Results
Google Sheets Dashboard:

Open your connected Google Sheet

"Job Results" tab contains all logged jobs

Sort by ai_score to prioritize applications

Filter by company, location, or salary

Track application status in additional columns

Telegram Notifications:

Real-time alerts for qualifying jobs

Click links directly from Telegram

Mark as read or save for later

Customization Tips
Multi-Search Setup: Duplicate "Get Search Params" rows for different job searches

Scoring Customization: Modify AI prompt to align with your priorities

Data Enrichment: Add nodes to fetch company info, Glassdoor ratings, etc.

Application Automation: Extend workflow to auto-fill applications (advanced)

🎓 Best Practices
Ethical Web Scraping [web:7]
Respect robots.txt: Check site policies before scraping

Rate Limiting: Use Wait nodes (2-5 second delays minimum)

User-Agent Headers: Identify your bot appropriately

Terms of Service: Ensure compliance with Indeed's terms

Data Management [web:7]
Regular Cleanup: Archive old job postings monthly

Deduplication: Check for duplicate URLs before logging

Privacy: Don't share credentials in exported workflows

Backup: Regularly export workflow JSON to GitHub

Performance Optimization
Pagination Limits: Don't scrape more than 5-10 pages per search

Conditional Execution: Use Score Filter to reduce unnecessary processing

Parallel Processing: Use Split in Batches for large datasets (advanced)

Error Handling: Add error workflows for failed requests

Security [web:13]
Credential Management: Use n8n's credential system, never hardcode

API Key Rotation: Regularly rotate OpenRouter and other API keys

Sheet Permissions: Limit Google Sheets access to necessary users only

Anonymize Exports: Remove credential IDs before sharing workflow JSON
```
🐛 Troubleshooting
Common Issues
1. "No job links extracted"

Cause: Indeed changed HTML structure

Solution: Update CSS selectors in "Extract Job Links" node

Debug: Use browser DevTools to inspect current HTML structure

2. "Rate limit exceeded" or "403 Forbidden"

Cause: Too many requests too quickly

Solution: Increase Wait node duration (try 5-10 seconds)

Prevention: Reduce max_pages parameter

3. "AI Agent timeout"

Cause: OpenRouter API slow or down

Solution: Increase timeout in HTTP Request settings

Alternative: Switch to faster model or local LLM

4. "Google Sheets authentication failed"

Cause: Expired credentials or insufficient permissions

Solution: Reauthorize Google Sheets OAuth in credentials

Check: Ensure Sheet ID is correct

5. "Telegram notifications not sending"

Cause: Incorrect Chat ID or bot token

Solution: Verify credentials, send test message via @BotFather

Debug: Check n8n execution logs for error details

Debugging Tips
Use Execute Node: Test individual nodes with sample data

Check Execution Logs: View detailed logs in Executions tab

Enable Error Workflows: Create error handler to log failures

Test with Small Dataset: Reduce max_pages to 1 during testing

Validate JSON: Use JSON validator for AI output parsing
```
🤝 Contributing
Contributions are welcome! Here's how you can help:

Feature Ideas
LinkedIn job scraping support

Salary prediction ML model

Application auto-submission

Company research automation

Skills gap analysis

Interview preparation automation

How to Contribute
Fork this repository (if hosted on GitHub)

Create a feature branch

Make your modifications to the workflow

Export updated JSON

Submit pull request with detailed description

Include use case examples

Reporting Issues
Provide n8n version, node versions, and error logs

Include anonymized workflow excerpt if possible

Describe expected vs actual behavior

📄 License
MIT License - feel free to modify and distribute this workflow.

Attribution appreciated but not required.

🙏 Acknowledgments
Built with n8n.io - Fair-code workflow automation

Powered by OpenRouter AI models

Inspired by the job-seeking developer community



⭐ If this workflow helped you land your dream job, consider starring the repo and sharing with other job seekers!

Version History
v1.0.0 (2026-01-13): Initial release with core job scraping functionality

v1.1.0 (Planned): Add LinkedIn and Glassdoor integration

v1.2.0 (Planned): AI-powered resume tailoring

Stats
Nodes: 21

Execution Time: ~3-5 minutes per search (depends on pagination)

API Costs: ~$0.10-0.50 per execution (OpenRouter credits)
