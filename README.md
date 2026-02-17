# gogarvisexplained
Explanation
GoGarvis Explained — Engineering Tickets + GitHub Project Board Pack

Telauthorium:
	•	TID: TID-GOGARVIS-EXPLAINABILITY-DEMO-0002
	•	TAID: TAID-GOGARVIS-EXPLAINABILITY-CANVAS-GITHUB-BOARD-0001
	•	Version: v1.0.0
	•	Status: CANVAS · LOCKED
	•	Authority: Pearl & Pig — Founder & Architect

⸻

What you asked for
	•	A GitHub Projects “JSON import file”
	•	Pre-written issue markdown you can paste directly

Better way (recommended)

GitHub Projects (v2) does not support a universal “import JSON” button for boards/issues. The most reliable approach is:
	1.	Store a project-pack JSON in the repo
	2.	Run a GitHub CLI (gh) script that creates:
	•	labels
	•	milestone
	•	issues (from markdown)
	•	a GitHub Project board (kanban)
	•	adds issues to the board

This produces a deterministic, repeatable setup (and Josh will like it).

⸻

A) FILES TO ADD TO REPO

Create these files inside your repo (root is fine):

/.github/
  /project-pack/
    project-pack.json
    create_project_and_issues.sh
    /issues/
      01_repo_structure_validation.md
      02_deterministic_routing_verification.md
      03_threshold_sensitivity_validation.md
      04_canon_mutation_refusal_enforcement.md
      05_docker_environment_validation.md
      06_json_schema_enforcement_optional.md
      07_public_readme_audit.md
      08_release_tag_verification.md


⸻

B) project-pack.json (the “import file”)

— FILE: .github/project-pack/project-pack.json —

{
  "owner": "jonpearlandpig",
  "repo": "gogarvisexplained",
  "project": {
    "title": "GoGarvis Explained — v1.0.0-canvas",
    "description": "Hardening + validation board for the public explainability demo (deterministic routing + threshold gating + trace).",
    "visibility": "public"
  },
  "milestone": {
    "title": "v1.0.0-canvas",
    "description": "Ship the canvas + tag + release + validated demo.",
    "due_on": null
  },
  "labels": [
    {"name": "epic:explainability-demo", "color": "0E8A16", "description": "Epic for explainability demo hardening"},
    {"name": "infra", "color": "1D76DB", "description": "Infrastructure / tooling"},
    {"name": "repo", "color": "BFDADC", "description": "Repository structure"},
    {"name": "core", "color": "5319E7", "description": "Core runtime behavior"},
    {"name": "determinism", "color": "0052CC", "description": "Deterministic routing"},
    {"name": "threshold", "color": "FBCA04", "description": "Threshold gating"},
    {"name": "governance", "color": "D93F0B", "description": "Governance boundary / refusal"},
    {"name": "security", "color": "B60205", "description": "Security posture"},
    {"name": "docker", "color": "0E8A16", "description": "Docker runtime"},
    {"name": "enhancement", "color": "C2E0C6", "description": "Optional improvement"},
    {"name": "validation", "color": "006B75", "description": "Validation / schema"},
    {"name": "docs", "color": "D4C5F9", "description": "Documentation"},
    {"name": "public", "color": "F9D0C4", "description": "Public-facing polish"},
    {"name": "release", "color": "A2EEEF", "description": "Tagging / release"}
  ],
  "issues": [
    {
      "file": ".github/project-pack/issues/01_repo_structure_validation.md",
      "title": "Validate full repo structure + clean root",
      "labels": ["epic:explainability-demo", "infra", "repo"],
      "milestone": "v1.0.0-canvas"
    },
    {
      "file": ".github/project-pack/issues/02_deterministic_routing_verification.md",
      "title": "Verify routing determinism (identical input → identical output)",
      "labels": ["epic:explainability-demo", "core", "determinism"],
      "milestone": "v1.0.0-canvas"
    },
    {
      "file": ".github/project-pack/issues/03_threshold_sensitivity_validation.md",
      "title": "Validate threshold gating behavior",
      "labels": ["epic:explainability-demo", "core", "threshold"],
      "milestone": "v1.0.0-canvas"
    },
    {
      "file": ".github/project-pack/issues/04_canon_mutation_refusal_enforcement.md",
      "title": "Confirm canon mutation attempts are blocked",
      "labels": ["epic:explainability-demo", "governance", "security"],
      "milestone": "v1.0.0-canvas"
    },
    {
      "file": ".github/project-pack/issues/05_docker_environment_validation.md",
      "title": "Validate Docker build + runtime",
      "labels": ["epic:explainability-demo", "infra", "docker"],
      "milestone": "v1.0.0-canvas"
    },
    {
      "file": ".github/project-pack/issues/06_json_schema_enforcement_optional.md",
      "title": "Add runtime validation against explainability_trace.schema.json (Optional)",
      "labels": ["epic:explainability-demo", "enhancement", "validation"],
      "milestone": "v1.0.0-canvas"
    },
    {
      "file": ".github/project-pack/issues/07_public_readme_audit.md",
      "title": "Audit README clarity + remove ambiguity",
      "labels": ["epic:explainability-demo", "docs", "public"],
      "milestone": "v1.0.0-canvas"
    },
    {
      "file": ".github/project-pack/issues/08_release_tag_verification.md",
      "title": "Verify v1.0.0-canvas tag + GitHub Release integrity",
      "labels": ["epic:explainability-demo", "release", "infra"],
      "milestone": "v1.0.0-canvas"
    }
  ],
  "board": {
    "columns": ["Backlog", "In Progress", "Validation", "Done"],
    "initial_column_map": {
      "01_repo_structure_validation.md": "Backlog",
      "02_deterministic_routing_verification.md": "In Progress",
      "03_threshold_sensitivity_validation.md": "In Progress",
      "04_canon_mutation_refusal_enforcement.md": "Validation",
      "05_docker_environment_validation.md": "Validation",
      "06_json_schema_enforcement_optional.md": "Backlog",
      "07_public_readme_audit.md": "Backlog",
      "08_release_tag_verification.md": "Done"
    }
  }
}

— END FILE —

⸻

C) create_project_and_issues.sh (GitHub CLI automation)

Prereqs:
	•	Install GitHub CLI: gh
	•	Authenticate: gh auth login
	•	From repo root: run script

— FILE: .github/project-pack/create_project_and_issues.sh —

#!/usr/bin/env bash
set -euo pipefail

PACK_FILE=".github/project-pack/project-pack.json"

if ! command -v gh >/dev/null 2>&1; then
  echo "ERROR: gh (GitHub CLI) not installed. Install from https://cli.github.com" >&2
  exit 1
fi

OWNER=$(jq -r '.owner' "$PACK_FILE")
REPO=$(jq -r '.repo' "$PACK_FILE")
MILESTONE_TITLE=$(jq -r '.milestone.title' "$PACK_FILE")

echo "[1/5] Ensuring labels exist..."
LABEL_COUNT=$(jq '.labels | length' "$PACK_FILE")
for i in $(seq 0 $((LABEL_COUNT-1))); do
  NAME=$(jq -r ".labels[$i].name" "$PACK_FILE")
  COLOR=$(jq -r ".labels[$i].color" "$PACK_FILE")
  DESC=$(jq -r ".labels[$i].description" "$PACK_FILE")

  # create label if missing
  if gh label list -R "$OWNER/$REPO" --limit 500 | awk '{print $1}' | grep -qx "$NAME"; then
    echo "  - label exists: $NAME"
  else
    gh label create "$NAME" -R "$OWNER/$REPO" --color "$COLOR" --description "$DESC"
    echo "  + label created: $NAME"
  fi
done

echo "[2/5] Ensuring milestone exists..."
if gh api -H "Accept: application/vnd.github+json" "/repos/$OWNER/$REPO/milestones" --paginate | jq -r '.[].title' | grep -qx "$MILESTONE_TITLE"; then
  echo "  - milestone exists: $MILESTONE_TITLE"
else
  MS_DESC=$(jq -r '.milestone.description' "$PACK_FILE")
  gh api -X POST -H "Accept: application/vnd.github+json" "/repos/$OWNER/$REPO/milestones" \
    -f title="$MILESTONE_TITLE" \
    -f description="$MS_DESC" >/dev/null
  echo "  + milestone created: $MILESTONE_TITLE"
fi

# resolve milestone number
MS_NUM=$(gh api -H "Accept: application/vnd.github+json" "/repos/$OWNER/$REPO/milestones" --paginate \
  | jq -r ".[] | select(.title==\"$MILESTONE_TITLE\") | .number" | head -n 1)

echo "[3/5] Creating issues from markdown..."
ISSUE_COUNT=$(jq '.issues | length' "$PACK_FILE")
CREATED_ISSUES_JSON=$(mktemp)

echo "[]" > "$CREATED_ISSUES_JSON"

for i in $(seq 0 $((ISSUE_COUNT-1))); do
  FILE=$(jq -r ".issues[$i].file" "$PACK_FILE")
  TITLE=$(jq -r ".issues[$i].title" "$PACK_FILE")
  LABELS=$(jq -r ".issues[$i].labels | join(\",\")" "$PACK_FILE")

  # skip if issue already exists with same title
  if gh issue list -R "$OWNER/$REPO" --search "$TITLE" --json title --limit 100 | jq -r '.[].title' | grep -Fxq "$TITLE"; then
    echo "  - issue exists: $TITLE"
    continue
  fi

  URL=$(gh issue create -R "$OWNER/$REPO" --title "$TITLE" --body-file "$FILE" --label "$LABELS" --milestone "$MS_NUM" --json url -q '.url')
  echo "  + created: $TITLE"
  jq --arg title "$TITLE" --arg url "$URL" '. + [{title:$title,url:$url}]' "$CREATED_ISSUES_JSON" > "$CREATED_ISSUES_JSON.tmp" && mv "$CREATED_ISSUES_JSON.tmp" "$CREATED_ISSUES_JSON"
done

echo "[4/5] Project board creation (manual step by default)..."
cat <<'NOTE'
GitHub Projects (v2) board creation via API is possible, but requires org/user Project permissions + GraphQL.
Best practice: create the Project in the GitHub UI (owner: jonpearlandpig), then run an optional GraphQL script to add items.

Manual steps (fast):
1) GitHub → Projects → New Project
2) Name: GoGarvis Explained — v1.0.0-canvas
3) Template: Board
4) Columns: Backlog / In Progress / Validation / Done
5) Add the created issues to the project

If you want full automation, add a PAT and I can provide a GraphQL add-items script.
NOTE

echo "[5/5] Done. Issues created (or verified)."
echo "Created issues (this run):"
cat "$CREATED_ISSUES_JSON" | jq -r '.[] | "- \(.title): \(.url)"'

rm -f "$CREATED_ISSUES_JSON"

— END FILE —

Note: this script uses jq. If not installed:

	•	macOS: brew install jq
	•	Ubuntu: sudo apt-get install -y jq

⸻

D) Pre-written Issue Markdown (copy/paste ready)

ISSUE 01

— FILE: .github/project-pack/issues/01_repo_structure_validation.md —

### Objective
Ensure repository structure matches the intended architecture and is clean for public distribution.

### Required Structure
- `00_README_GOVERNANCE/`
- `01_CANON/`
- `04_BUILD/`
- `demo/`
- `PEARL_PIG_OS/gogarvisexplained_canvas/` (canvas artifact)

### Tasks
- [ ] Confirm no nested repos or stray build artifacts
- [ ] Confirm no duplicate folder roots
- [ ] Confirm `.env.example` only (no real `.env`)
- [ ] Confirm no secrets committed
- [ ] Confirm `.gitignore` excludes node_modules and local OS files

### Definition of Done
- Repo tree is clean, intentional, and public-safe
- No sensitive artifacts present

— END FILE —

ISSUE 02

— FILE: .github/project-pack/issues/02_deterministic_routing_verification.md —

### Objective
Verify routing determinism: identical input and threshold produce identical routing outcomes.

### Test
Run the same input 5x:
- same `input_text`
- same `THRESHOLD`

Confirm these are identical across runs:
- `domain_detected`
- `operator_scores` values (order + composite scores)
- `qualified_operator_ids`
- `orchestration_mode`
- `provider_used`

Allowed to differ:
- `invocation_id`
- `latency_ms` (if timestamp-based additive exists)

### Definition of Done
- Routing outputs (excluding invocation_id) are stable across repeated runs
- No randomness in selection/scoring logic

— END FILE —

ISSUE 03

— FILE: .github/project-pack/issues/03_threshold_sensitivity_validation.md —

### Objective
Validate threshold gating affects qualification and orchestration behavior.

### Steps
- [ ] Run: `THRESHOLD=0.65 npm run demo`
- [ ] Run: `THRESHOLD=0.80 npm run demo`

### Expected
- Higher threshold ⇒ fewer qualified operators
- Orchestration mode may shift (parallel → single)
- UI displays current threshold value

### Definition of Done
- Threshold visibly changes qualified set and/or mode
- No UI mismatch between applied threshold and displayed threshold

— END FILE —

ISSUE 04

— FILE: .github/project-pack/issues/04_canon_mutation_refusal_enforcement.md —

### Objective
Ensure canon/governance mutation attempts are refused with a clear TWO-KEY REQUIRED response.

### Test Inputs
- "Modify router kernel confidence thresholds."
- "Update approval gates in canon."
- "Change the canon operator schema."

### Expected
- HTTP 403
- `refusal: true`
- headline: `TWO-KEY REQUIRED`
- approver requirement listed

### Definition of Done
- Refusal triggers reliably (case/wording variations)
- No bypass via simple phrasing changes

— END FILE —

ISSUE 05

— FILE: .github/project-pack/issues/05_docker_environment_validation.md —

### Objective
Validate Docker build and runtime are reproducible on a clean environment.

### Steps
- [ ] From `demo/`: `docker compose up --build`
- [ ] Confirm UI loads at `http://localhost:8787`
- [ ] Confirm `/invoke` returns trace payload

### Definition of Done
- Docker build succeeds with no manual steps
- Runtime works consistently

— END FILE —

ISSUE 06 (Optional)

— FILE: .github/project-pack/issues/06_json_schema_enforcement_optional.md —

### Objective (Optional)
Add runtime validation against `04_BUILD/SCHEMAS/explainability_trace.schema.json`.

### Suggested Approach
- Add `ajv` to demo dependencies
- Validate the response object before returning it from `/invoke`
- If invalid, return 500 and log which schema fields failed

### Definition of Done
- Response always validates to schema
- Any schema drift causes a loud failure (prevents silent breakage)

— END FILE —

ISSUE 07

— FILE: .github/project-pack/issues/07_public_readme_audit.md —

### Objective
Ensure public README is investor-clear and contains the required proof elements.

### Checklist
- [ ] Positioning statement at top
- [ ] "Why this exists" section
- [ ] Determinism guarantee statement
- [ ] Routing flow diagram
- [ ] Black box comparison
- [ ] Trace snapshot (redacted)
- [ ] Threshold sensitivity instructions
- [ ] "What this demo does NOT do" section
- [ ] Canvas reference link correct

### Definition of Done
- README communicates the proof in < 60 seconds
- No internal-only language

— END FILE —

ISSUE 08

— FILE: .github/project-pack/issues/08_release_tag_verification.md —

### Objective
Verify `v1.0.0-canvas` tag and GitHub Release are correctly published.

### Checklist
- [ ] Tag exists: `v1.0.0-canvas`
- [ ] Release title matches
- [ ] Release description includes: determinism, threshold gating, routing trace
- [ ] Canvas file included and linked
- [ ] (Optional) screenshot attached showing scoring + threshold + trace

### Definition of Done
- Public release page is correct and complete

— END FILE —

⸻

E) GitHub Issue Checklist Board (Manual setup, fastest)

Create a GitHub Project board (Board template):
	•	Columns: Backlog / In Progress / Validation / Done

Then add issues and set initial column placement:
	•	Backlog: 01, 06, 07
	•	In Progress: 02, 03
	•	Validation: 04, 05
	•	Done: 08

⸻

F) If you want full board automation

GitHub Projects v2 automation is possible via GraphQL, but it requires:
	•	A Personal Access Token (PAT) with Projects scope
	•	The project owner context (user vs org)

If you provide a PAT (or Josh runs it locally), I can supply a GraphQL script that:
	•	creates the project
	•	creates columns
	•	adds each created issue as an item
	•	sets item status (Backlog/In Progress/etc.)

⸻

Operator: Marty Hillsdale
Weight: 4
Output Type: Task
Next Step: Add these files to the repo and have Josh run .github/project-pack/create_project_and_issues.sh after gh auth login.

⸻

Upload to GitHub (Option A — Terminal Recommended)

Telauthorium:
	•	TID: TID-GOGARVIS-EXPLAINABILITY-DEMO-0002
	•	TAID: TAID-GOGARVIS-EXPLAINABILITY-CANVAS-GITHUB-UPLOAD-0001
	•	Version: v1.0.0
	•	Status: LOCKED

Purpose

Deterministically upload the GitHub Project Pack to the existing repository using terminal commands.

⸻

Step 1 — Confirm Repo Root

From your local machine, navigate to the repository root:

PEARL_PIG_OS/gogarvisexplained

Confirm expected structure:

00_README_GOVERNANCE
01_CANON
04_BUILD
demo
PEARL_PIG_OS/gogarvisexplained_canvas


⸻

Step 2 — Create Project Pack Folders

Run:

mkdir -p .github/project-pack/issues

Place the following files exactly into:

.github/project-pack/

Required files:
	•	project-pack.json
	•	create_project_and_issues.sh
	•	issues/01_repo_structure_validation.md
	•	issues/02_deterministic_routing_verification.md
	•	issues/03_threshold_sensitivity_validation.md
	•	issues/04_canon_mutation_refusal_enforcement.md
	•	issues/05_docker_environment_validation.md
	•	issues/06_json_schema_enforcement_optional.md
	•	issues/07_public_readme_audit.md
	•	issues/08_release_tag_verification.md

⸻

Step 3 — Commit + Push

git add .
git commit -m "Add GitHub project board pack (automation + issue set)"
git push

Repository URL:

https://github.com/jonpearlandpig/gogarvisexplained


⸻

Step 4 — Run Automation (Optional but Recommended)

Prerequisites:
	•	GitHub CLI installed (gh)
	•	jq installed

Authenticate:

gh auth login

Install jq if needed:

macOS:

brew install jq

Ubuntu:

sudo apt-get install -y jq

Make script executable and run:

chmod +x .github/project-pack/create_project_and_issues.sh
.github/project-pack/create_project_and_issues.sh

This will:
	•	Create labels
	•	Create milestone v1.0.0-canvas
	•	Create all 8 issues

⸻

Validation Check

Visit:

https://github.com/jonpearlandpig/gogarvisexplained/tree/main/.github/project-pack

Confirm files exist.

Then confirm Issues tab shows created tickets.

⸻

Guardrails

Do NOT commit:
	•	.env
	•	node_modules/
	•	Real API keys
	•	Private prompts

Only the project-pack files.

⸻

This completes the deterministic GitHub board provisioning flow.
