# ERPNext v15 Documentation Reference

This directory contains documentation reference materials for ERPNext v15, organized by module.

## Documentation Structure

```
reference/
├── index.md                 # This file - documentation overview
├── api/                     # REST API and Developer APIs
│   ├── rest-api.md          # REST API documentation
│   └── developer-api.md     # Python/JS Developer APIs
├── accounting/              # Accounting & Finance module
│   └── overview.md
├── assets/                  # Asset Management module
│   └── overview.md
├── buying/                  # Procurement/Purchasing module
│   └── overview.md
├── selling/                 # Sales module
│   └── overview.md
├── stock/                   # Inventory/Stock Management
│   └── overview.md
├── manufacturing/           # Production/Manufacturing
│   └── overview.md
├── projects/                # Project Management
│   └── overview.md
├── crm/                     # Customer Relationship Management
│   └── overview.md
├── hr-payroll/              # Human Resources & Payroll
│   └── overview.md
├── support/                 # Helpdesk & Support
│   └── overview.md
├── pos/                     # Point of Sale
│   └── overview.md
├── integrations/            # Third-party Integrations
│   └── overview.md
├── setup/                   # System Setup & Configuration
│   └── overview.md
└── customization/           # Customization & Extensions
    └── overview.md
```

## ERPNext v15 Overview

ERPNext is a free and open-source enterprise resource planning (ERP) system built on the Frappe Framework.

- **Repository**: https://github.com/frappe/erpnext
- **Official Docs**: https://docs.frappe.io/erpnext/
- **Framework Docs**: https://docs.frappe.io/framework/
- **License**: GPL-3.0
- **Technology Stack**: Python (81%), JavaScript (16.8%), MariaDB

## Core Modules

| Module | Description |
|--------|-------------|
| **Accounting** | Chart of accounts, invoicing, payments, taxes, multi-currency, budgets |
| **Assets** | Fixed asset lifecycle, depreciation, maintenance |
| **Buying** | Procurement, RFQs, supplier management, purchase orders |
| **Selling** | Sales orders, quotations, pricing rules, commissions |
| **Stock** | Inventory, warehouses, serial/batch tracking, valuations |
| **Manufacturing** | BOMs, work orders, production planning, job cards |
| **Projects** | Task management, timesheets, Gantt charts, profitability |
| **CRM** | Leads, opportunities, campaigns, sales pipeline |
| **HR & Payroll** | Employees, attendance, leaves, salary, recruitment |
| **Support** | Tickets, SLAs, warranty, maintenance scheduling |
| **POS** | Point of sale, invoice consolidation |
| **Integrations** | Payment gateways, e-commerce, accounting services |

## API Access

ERPNext provides REST APIs for all DocTypes:
- **Base URL**: `https://{instance}/api`
- **Authentication**: Token-based, OAuth2, or Session-based
- **Endpoints**: CRUD operations on `/api/resource/{DocType}`

## External Resources

- [ERPNext v15 Features](https://frappe.io/erpnext/version-15)
- [Migration Guide v15](https://github.com/frappe/erpnext/wiki/Migration-Guide-to-ERPNext-version-15)
- [Frappe Forum](https://discuss.frappe.io/c/erpnext/6)
- [Frappe School](https://school.frappe.io)
