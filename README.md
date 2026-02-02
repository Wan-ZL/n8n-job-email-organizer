# AI Job Email Organizer

An n8n workflow that automatically categorizes and organizes job-related emails using GPT-4o (or GPT-5.2) with structured outputs.

## What it does

- **Monitors Gmail** for new emails
- **AI Classification** - Uses GPT to classify emails as JOB or OTHER
- **Smart Routing** - Routes job emails by status (New Post, Applied, Interview, Rejected, Offer)
- **Google Sheets Logging** - Saves organized data to separate tabs
- **Auto Mark Read** - Marks processed emails as read

## Output Tabs

| Tab | Description |
|-----|-------------|
| **New Jobs** | Job alerts and new postings to review |
| **Applied Jobs** | Application confirmations |
| **Rejected Jobs** | Rejection emails |
| **Action Jobs** | Interviews, offers, follow-ups requiring action |
| **Other** | Non-job emails (optional - delete if not needed) |

## Fields Extracted

For each job email, the AI extracts:
- **Company** - Company name
- **Role** - Job title
- **Status** - Current stage (New Post/Applied/Interview/Rejected/Offer/Follow Up)
- **Next Step** - Recommended action (Apply/Prep/Archive/Follow Up/Review)
- **Score** - Match score 1-10 for your target role
- **Summary** - One sentence summary
- **Reason** - Why this score/recommendation

## Setup

### Prerequisites
- [n8n](https://n8n.io/) installed (self-hosted or cloud)
- Gmail account
- OpenAI API key
- Google Sheets

### Installation

1. **Import the workflow**
   - Copy `workflow.json`
   - In n8n: Settings → Import from File

2. **Configure credentials**
   - Gmail OAuth2
   - OpenAI API
   - Google Sheets OAuth2

3. **Create Google Sheet with tabs:**
   - `New Jobs`
   - `Applied Jobs`
   - `Rejected Jobs`
   - `Action Jobs`
   - `Other` (optional)

4. **Update Sheet ID**
   - Replace `YOUR_GOOGLE_SHEET_ID` in all Google Sheets nodes

5. **Activate the workflow**

## Customization

### Change AI Model
Edit the `model` field in `Prep Classify` and `Prep Job` nodes:
- `gpt-4o` - Default, good balance
- `gpt-4o-mini` - Faster, cheaper
- `gpt-5.2` - Latest (if available)

### Remove "Other" Category
If you only want job emails:
1. Delete `Format Other` node
2. Delete `Save Other` node
3. Non-job emails will just be marked as read

### Customize Classification
Edit the system prompt in `Prep Classify` to adjust what counts as a job email.

### Customize Job Analysis
Edit the system prompt in `Prep Job` to change:
- Target role (default: ML/AI Engineer)
- Scoring criteria
- Status categories

## Architecture

```
Gmail Trigger
     ↓
Prep Classify (build GPT request)
     ↓
Classify Email (GPT API call)
     ↓
Parse Category (extract result)
     ↓
Route Category ─────────────────┐
     ↓ (JOB)                    ↓ (OTHER)
Prep Job                    Format Other
     ↓                          ↓
Analyze Job                 Save Other
     ↓                          ↓
Format Job                      │
     ↓                          │
Route Job Status                │
     ↓↓↓↓                       │
Save New/Applied/Rejected/Action│
     ↓                          ↓
     └──────── Mark Read ───────┘
```

## License

MIT

## Contributing

Pull requests welcome! Please open an issue first to discuss changes.
