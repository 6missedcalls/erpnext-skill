# ERPNext Setup & Configuration

System setup and initial configuration for ERPNext.

## Initial Setup

### Company Setup
- Company creation
- Fiscal year
- Currency settings
- Chart of accounts
- Cost centers

### User Management
- User creation
- Role assignment
- Permission configuration
- Department structure

### Master Data
- Items
- Customers
- Suppliers
- Warehouses
- Price lists

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `Company` | Company master |
| `User` | User accounts |
| `Role` | Permission roles |
| `Fiscal Year` | Accounting periods |
| `Currency` | Currency definitions |
| `Global Defaults` | System defaults |
| `System Settings` | Core settings |

## API Examples

### Create Company
```python
doc = frappe.get_doc({
    "doctype": "Company",
    "company_name": "My Company",
    "abbr": "MC",
    "default_currency": "USD",
    "country": "United States",
    "chart_of_accounts": "Standard"
})
doc.insert()
```

### Create User
```python
doc = frappe.get_doc({
    "doctype": "User",
    "email": "user@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "roles": [
        {"role": "Accounts User"},
        {"role": "Sales User"}
    ]
})
doc.insert()
# Send welcome email
doc.send_welcome_mail_to_user()
```

### Configure Defaults
```python
frappe.db.set_default("company", "My Company")
frappe.db.set_default("currency", "USD")
```

## Module Settings

Each module has dedicated settings:
- `Selling Settings`
- `Buying Settings`
- `Stock Settings`
- `Manufacturing Settings`
- `HR Settings`
- `Accounts Settings`

## Naming Series

Configure document numbering:
```python
doc = frappe.get_doc({
    "doctype": "Document Naming Rule",
    "document_type": "Sales Invoice",
    "prefix": "SINV-.YYYY.-",
    "prefix_digits": 5
})
```

## Workflow Setup

Create approval workflows:
```python
doc = frappe.get_doc({
    "doctype": "Workflow",
    "workflow_name": "Purchase Order Approval",
    "document_type": "Purchase Order",
    "states": [
        {"state": "Draft", "doc_status": 0},
        {"state": "Pending Approval", "doc_status": 0},
        {"state": "Approved", "doc_status": 1}
    ],
    "transitions": [
        {
            "state": "Draft",
            "action": "Submit for Approval",
            "next_state": "Pending Approval",
            "allowed": "Purchase User"
        }
    ]
})
doc.insert()
```

## Email Configuration

```python
doc = frappe.get_doc({
    "doctype": "Email Account",
    "email_account_name": "Notifications",
    "email_id": "notifications@example.com",
    "password": frappe.conf.get("email_password"),  # from site_config.json
    "smtp_server": "smtp.gmail.com",
    "smtp_port": 587,
    "use_tls": 1
})
doc.insert()
```

## Print Format

Customize document templates:
- Standard print formats
- Custom HTML templates
- Letter head configuration
- PDF generation

## Data Import

Bulk import via:
- Data Import Tool
- CSV uploads
- API bulk operations

## Reports

- User Permission Report
- Role Permission Report
- Setup Completion
- Module Status
