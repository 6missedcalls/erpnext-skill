<div align="center">

# ERPNext / Frappe Developer Skill

**Comprehensive Frappe Framework and ERPNext v15 development reference for Claude Code.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/version-1.0.0-green.svg)](#)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Skill-7c3aed.svg)](#)
[![skills.sh](https://img.shields.io/badge/skills.sh-install-orange.svg)](https://skills.sh/6missedcalls/erpnext-skill)

Build full-featured ERPNext applications without leaving your editor.
Claude Code gains instant access to every Frappe API, DocType pattern,
controller hook, and module reference it needs to scaffold, extend, and
debug custom ERPNext apps.

</div>

---

## Features

### Framework API Coverage

| API Surface | What You Get |
|-------------|-------------|
| **Document API** | CRUD operations, naming, permissions, workflow transitions |
| **Database API** | Queries, transactions, bulk operations, direct SQL |
| **Controllers** | Lifecycle hooks, validation, submission, cancellation |
| **Hooks** | App configuration, scheduled jobs, doc events, overrides |
| **REST API** | Resource endpoints, custom methods, authentication |
| **Form Scripts** | Client-side JS, field events, dialogs, routing |

### ERPNext Module References

| Domain | Modules |
|--------|---------|
| **Finance** | Accounting, Assets |
| **Supply Chain** | Stock, Buying, Selling, Manufacturing |
| **People** | HR and Payroll |
| **Customer Facing** | CRM, Support, POS |
| **Operations** | Projects, Integrations, Setup, Customization |

> Every module reference includes DocType relationships, field schemas,
> common workflows, and ready-to-use code examples targeting ERPNext v15.

---

## Installation

### One-line install via [skills.sh](https://skills.sh)

```bash
npx skills add 6missedcalls/erpnext-skill
```

To install globally (available across all projects):

```bash
npx skills add 6missedcalls/erpnext-skill -g
```

To target a specific agent:

```bash
npx skills add 6missedcalls/erpnext-skill -a claude-code
```

### Manual install

Clone directly into your Claude Code skills directory:

```bash
# Project-level (current project only)
git clone https://github.com/6missedcalls/erpnext-skill.git .claude/skills/erpnext-skill

# Global (all projects)
git clone https://github.com/6missedcalls/erpnext-skill.git ~/.claude/skills/erpnext-skill
```

### Via Claude Code plugin marketplace

```
/plugin marketplace add 6missedcalls/erpnext-skill
```

Then select **Browse and install plugins** and follow the prompts.

---

## Quick Start

Once installed, Claude Code automatically loads the skill whenever you
work on a Frappe or ERPNext project. No manual invocation needed --
just ask Claude Code to build what you need:

```
"Create a custom app with an Invoice DocType that auto-calculates tax"
"Add a webhook hook that syncs new Sales Orders to an external API"
"Write a form script that validates line items before submission"
```

---

## Documentation

The `reference/` directory is organized for fast, targeted lookups:

```
reference/
|-- frappe-framework-complete.md   # Primary reference -- full API docs
|-- document-api.md                # Document CRUD operations
|-- database-api.md                # Database queries and transactions
|-- hooks-reference.md             # hooks.py configuration
|-- rest-api.md                    # REST API endpoints
|-- form-scripts.md                # Client-side JavaScript
|-- index.md                       # Module documentation index
|
|-- api/
|   |-- rest-api.md                # REST API deep-dive
|   +-- developer-api.md           # Python / JS developer APIs
|
|-- accounting/                    # Accounting and Finance
|-- stock/                         # Inventory and Stock Management
|-- manufacturing/                 # Production and Manufacturing
|-- crm/                           # Customer Relationship Management
|-- hr-payroll/                    # Human Resources and Payroll
|-- buying/                        # Procurement and Purchasing
|-- selling/                       # Sales
|-- projects/                      # Project Management
|-- support/                       # Helpdesk and Support
|-- assets/                        # Asset Management
|-- pos/                           # Point of Sale
|-- integrations/                  # Third-party Integrations
|-- setup/                         # System Setup and Configuration
+-- customization/                 # Customization and Extensions
```

**Start here:** `reference/frappe-framework-complete.md` -- the single
file that covers the entire Frappe API surface with working code examples.

---

## Usage

### How It Works

This skill is a **reference-only** package. It contains no executable code.
When Claude Code detects that you are working in a Frappe/ERPNext context,
it loads the relevant documentation into its context window so that it can:

- Scaffold new apps with `bench new-app`
- Generate DocTypes, controllers, and form scripts
- Wire up hooks, scheduled tasks, and doc events
- Write correct REST API integrations
- Follow Frappe best practices for permissions, workflows, and testing

### Example Workflow

```bash
# Create a new Frappe app
bench new-app my_custom_app
bench --site mysite install-app my_custom_app
```

Then ask Claude Code to build features inside your app. It will reference
the skill documentation to produce idiomatic Frappe code:

```python
# Controller with lifecycle hooks
class CustomInvoice(Document):
    def validate(self):
        self.calculate_totals()

    def on_submit(self):
        self.create_gl_entries()

    def calculate_totals(self):
        self.grand_total = sum(item.amount for item in self.items)
```

```javascript
// Form script with field-level events
frappe.ui.form.on('Custom Invoice', {
    refresh(frm) {
        if (frm.doc.docstatus === 0) {
            frm.add_custom_button(__('Fetch Items'), () => {
                frm.trigger('fetch_items');
            });
        }
    },
    fetch_items(frm) {
        // Custom logic here
    }
});
```

---

## Contributing

Contributions are welcome. Whether you want to expand module coverage, fix
an API example, or improve the documentation structure, feel free to open
an issue or submit a pull request.

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/add-xyz-reference`)
3. Commit your changes (`git commit -m "docs: add XYZ module reference"`)
4. Push to the branch (`git push origin feature/add-xyz-reference`)
5. Open a Pull Request

---

## License

This project is licensed under the [MIT License](https://opensource.org/licenses/MIT).

---

<div align="center">

Built by [openclaw](https://github.com/6missedcalls)

</div>
