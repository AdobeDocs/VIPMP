# Quick start guide

This guide shows you how to implement VIP Marketplace features using AI-assisted code generation. You can start with a visual overview of the user journey, explore a sample implementation in Adobe's reference application, or follow the complete workflow to generate code for your project. For a deeper understanding of the toolkit's architecture and workflow, review the [overview](./index.md) before you start.

## Before you begin

The Visualize track requires only an AI agent, so no additional setup is necessary. The prerequisites in this section apply only to the hands-on Try and Build tracks.

You will need the following:

- Node.js and npm
- Git
- An AI coding agent that supports slash-command skills (for example, Claude Code, ChatGPT, Gemini, or GitHub Copilot)
- VIP Marketplace credentials for your `.env` file. See [configuration](https://github.com/adobe/adobe-commerce-partnerships-ref-app#configuration) for where to obtain these.
- Both repositories cloned side by side:
  - [adobe-commerce-partnerships-ai-kit](https://github.com/adobe/adobe-commerce-partnerships-ai-kit)
  - [adobe-commerce-partnerships-ref-app](https://github.com/adobe/adobe-commerce-partnerships-ref-app) (`develop` branch)

**Directory layout:**

Use the following directory structure:

```text
~/Documents/
  adobe-commerce-partnerships-ai-kit/      ← this repo
  adobe-commerce-partnerships-ref-app/     ← develop branch
```

## Visualize the experience before implementing

To preview the user experience before writing code, provide an Experience Card to your AI agent and ask it to generate a mock reference application.

**For example:**

Using the attached Experience Card for the `<featureName>` feature, build a working reference app that simulates the end-to-end user journey. Follow its workflows, business rules, and interactions, making reasonable assumptions where details are missing.

**Example Experience Card:** `feature-specs/flexible-discounts/experience-card/flexible-discounts.md`

Most AI agents, including Claude, ChatGPT, Gemini, and GitHub Copilot, can generate this experience. No modifications to your project are required.

## Try it out on adobe-commerce-partnerships-ref-app

Use this hands-on exercise to explore what the toolkit can do. This workflow uses Adobe's reference LLD and service cards to generate a feature implementation in `adobe-commerce-partnerships-ref-app`, Adobe's reference VIP Marketplace partner app, using a single skill command. See [Before you begin](#before-you-begin) for prerequisites and directory layout.

### Step 1: Clone and install

```bash
git clone git@github.com:adobe/adobe-commerce-partnerships-ai-kit.git
git clone -b develop git@github.com:adobe/adobe-commerce-partnerships-ref-app.git
cd adobe-commerce-partnerships-ref-app && npm install && cp .env.sample .env
```

Fill in your VIP Marketplace credentials in `.env`. See [configuration](https://github.com/adobe/adobe-commerce-partnerships-ref-app#configuration) for details.

### Step 2: Open your AI coding agent in the kit directory

```bash
cd ../adobe-commerce-partnerships-ai-kit
```

Open your AI coding agent from this directory. Skills are invoked as slash commands (for example, `/implement-feature`).

**Using Claude Code?** See [Setting Up Claude Code](https://github.com/adobe/adobe-commerce-partnerships-ai-kit/blob/main/manual/setup/claude-code.md) to wire the kit before running skills.

### Step 3: Run the feature skill

```bash
/implement-feature <featureName> ../adobe-commerce-partnerships-ref-app --use-reference-files
```

Example:

```bash
/implement-feature flexible-discounts ../adobe-commerce-partnerships-ref-app --use-reference-files
```

The `--use-reference-files` flag tells the skill to load the pre-approved LLD and service cards from `feature-specs/<featureName>/reference-files/` in this repo instead of generating them.

### Step 4: Verify the feature

```bash
/verify-feature <featureName> ../adobe-commerce-partnerships-ref-app --use-reference-files
```

Example:

```bash
/verify-feature flexible-discounts ../adobe-commerce-partnerships-ref-app --use-reference-files
```

The skill audits every generated file against the LLD specification, checks application wiring, and runs the project's type check. When static checks pass, the skill starts the dev server automatically at [http://localhost:9000](http://localhost:9000) and presents a browser checklist. Review each item and report any failures.

## Run the full workflow

This section describes the complete, multi-step workflow for integrating a VIP Marketplace feature into a codebase of your choice, whether Adobe's reference app, adobe-commerce-partnerships-ref-app, or your project. Follow these steps to generate service cards, produce LLDs from Adobe's feature specs, and implement the integration code. See [Before you begin](#before-you-begin) for prerequisites and directory layout.

### Step 1: Open your AI coding agent in the kit directory

Run all AI Kit skills from the `adobe-commerce-partnerships-ai-kit/` working directory. Open your AI coding agent here.

**Using Claude Code?** See [Setting Up Claude Code](https://github.com/adobe/adobe-commerce-partnerships-ai-kit/blob/main/manual/setup/claude-code.md).

### Step 2: Generate backend service card

Run once per project. Re-run after a major refactor.

```bash
/generate-backend-service-card <targetRepoPath>
```

Example:

```bash
/generate-backend-service-card ../adobe-commerce-partnerships-ref-app
```

Scans your backend codebase and generates a set of service cards that capture its patterns, contracts, and conventions. The output is written to `<targetRepo>/docs/ai-kit/service-cards/backend/`.

### Step 3: Generate UI service card

Run once per project. Re-run after a major refactor.

```bash
/generate-ui-service-card <targetRepoPath>
```

Example:

```bash
/generate-ui-service-card ../adobe-commerce-partnerships-ref-app
```

Scans your frontend codebase and generates a set of service cards covering component patterns, state management, the data layer, and routing conventions. Written to `<targetRepo>/docs/ai-kit/service-cards/ui/`.

### Step 4: Apply API spec → backend LLD

```bash
/apply-api-spec <targetRepoPath> <featureSpecPath>
```

Example:

```bash
/apply-api-spec ../adobe-commerce-partnerships-ref-app feature-specs/flexible-discounts/APISpec/flexible-discounts-apispec.md
```

Reads the API spec and your backend service cards, then produces a Low-Level Design for all backend changes needed. Written to `<targetRepo>/docs/ai-kit/LLD/backend/`.

Review the LLD before continuing. This is the most efficient stage at which to identify and address design issues.

### Step 5: Apply experience card → UI LLD

```bash
/apply-experience-card <featureSpecPath> <targetRepoPath> <backendLLDPath>
```

| Argument | Required | Description |
| --- | --- | --- |
| `featureSpecPath` | Yes | Path to the experience card file |
| `targetRepoPath` | Yes | Absolute path to the target UI repo |
| `backendLLDPath` | No | Path to the backend LLD. Pass `none` if the feature makes no new backend calls. |

Example:

```bash
/apply-experience-card \
  feature-specs/flexible-discounts/experience-card/flexible-discounts.md \
  ../adobe-commerce-partnerships-ref-app \
  ../adobe-commerce-partnerships-ref-app/docs/ai-kit/LLD/backend/flexible-discounts-lld.md
```

Reads the experience card, the approved backend LLD, and your UI service cards, then generates a UI Low-Level Design covering components, data-fetching units, state management, and navigation. Written to `<targetRepo>/docs/ai-kit/LLD/ui/`.

**Review the LLD before continuing.**

### Step 6: Implement the feature

```bash
/implement-feature <featureName> <targetRepoPath> [--mode=both|backend|ui] [--use-reference-files]
```

| Argument | Required | Description |
| --- | --- | --- |
| `featureName` | Yes | Kebab-case feature name (e.g. `flexible-discounts`) |
| `targetRepoPath` | Yes | Absolute path to the target repo |
| `--mode` | No | `--mode=both` (default): backend + UI. `--mode=backend`: backend only. `--mode=ui`: UI only. |
| `--use-reference-files` | No | Load pre-approved LLD and service cards from this kit repo instead of the target repo. |

Example:

```bash
/implement-feature flexible-discounts ../adobe-commerce-partnerships-ref-app
```

Reads both approved LLDs and your service cards, then generates the feature implementation for the backend, including models, controllers, routes, and constants, and for the UI, including hooks, components, pages, and CSS modules. Runs a type-check at the end and auto-fixes any errors in the generated files before reporting done.

### Step 7: Verify the feature

```bash
/verify-feature <featureName> <targetRepoPath> [--mode=both|backend|ui] [--use-reference-files]
```

Example:

```bash
/verify-feature flexible-discounts ../adobe-commerce-partnerships-ref-app
```

Audits every generated file against the LLD specification, checks integration between the UI and backend, runs the project's type-check command, and verifies that the implementation covers of the experience card acceptance criteria.

When the static checks are complete, the skill starts the dev server, if it is not already running, and presents a structured browser checklist derived from the experience card, with one checklist item for each user-facing interaction. Go through each item in the browser and reply with any that fail. The skill produces a final pass/partial/fail report with specific fix guidance for any gaps found.
