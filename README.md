# N8N Workflows

A centralized repository for managing, versioning, and documenting all my **N8N** automation workflows.

## 📌 Project Overview

The goal of this repository is to maintain a single source of truth for my automations. By storing workflows as JSON files, I can leverage Git for version control, track changes over time, and ensure I have a reliable backup of my logic outside of the N8N instance.

## 📂 Repository Structure

The project follows a modular structure where each workflow or automation group has its own dedicated directory:

```text
.
├── LICENSE              # Project license
├── README.md            # Main documentation (this file)
├── wow.json             # WOW (Workflow-of-Workflows) - The main loader
├── project_name_1/      # Directory for a specific workflow
│   ├── README.md        # Specific details, triggers, and requirements
│   └── workflow.json    # The exported n8n workflow file
└── project_name_2/
    ├── README.md
    └── workflow.json
```

## 🚀 Usage Guide

### 🤖 The WOW (Workflow-of-Workflows) Way (Recommended)
The `wow.json` file is a special workflow designed to automate the setup of this entire repository in your n8n instance.

1. Import `wow.json` into your n8n instance.
2. Configure your **GitHub** and **n8n API** credentials in the respective nodes.
3. Execute the workflow.
4. It will automatically scan this repository, find all `workflow.json` files, and create them in your instance.

### Manual Import
1. Create a new workflow in your n8n instance.
2. Drag and drop the `workflow.json` file onto the canvas, or use the **"Import from File"** option in the menu.
3. **Note:** You will need to re-link your local **Credentials**, as they are not included in the JSON export for security reasons.

## 🔒 Security & Best Practices

* **Sensitive Data:** n8n does NOT export credentials (API keys, passwords) within the JSON. However, please double-check that no sensitive hardcoded strings (like specific IDs or personal emails) are present before pushing to a public repository.
* **Environment Variables:** Use n8n expressions and environment variables to keep workflows generic and portable.

---
*Generated for my n8n Home Lab setup.*
