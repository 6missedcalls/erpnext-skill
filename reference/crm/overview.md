# ERPNext CRM Module

The CRM module provides customer relationship management capabilities.

## Key Features

### Lead Management
- Lead capture from website/forms
- Lead source tracking
- Lead assignment rules
- Lead scoring
- Duplicate detection
- Lead to Customer conversion

### Opportunity Tracking
- Sales pipeline stages
- Expected revenue forecasting
- Probability tracking
- Lost reason analysis
- Competitor tracking

### Quotations
- Quote generation from opportunities
- Multiple pricing tiers
- Discount management
- Quote versioning
- Quote to Sales Order conversion

### Campaign Management
- Email campaigns
- Newsletter management
- Campaign ROI tracking
- Social media integration

### Sales Funnel
- Visual pipeline view
- Stage-wise analytics
- Conversion rates
- Sales forecasting

### Territory Management
- Territory hierarchy
- Territory-based assignment
- Regional sales tracking
- Territory targets

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `Lead` | Prospective customers |
| `Opportunity` | Sales opportunities |
| `Quotation` | Price quotes |
| `Customer` | Customer master |
| `Campaign` | Marketing campaigns |
| `Email Campaign` | Email sequences |
| `Sales Person` | Sales team members |
| `Territory` | Geographic regions |

## API Examples

### Create Lead
```python
doc = frappe.get_doc({
    "doctype": "Lead",
    "lead_name": "John Smith",
    "company_name": "Acme Corp",
    "email_id": "john@example.com",
    "mobile_no": "+1-555-0100",
    "source": "Website",
    "territory": "United States"
})
doc.insert()
```

### Convert Lead to Customer
```python
from erpnext.crm.doctype.lead.lead import make_customer

customer = make_customer("LEAD-00001")
customer.insert()
```

### Create Opportunity
```python
doc = frappe.get_doc({
    "doctype": "Opportunity",
    "opportunity_from": "Lead",
    "party_name": "LEAD-00001",
    "opportunity_type": "Sales",
    "status": "Open",
    "expected_closing": "2024-03-01",
    "opportunity_amount": 50000
})
doc.insert()
```

### Create Quotation from Opportunity
```python
from erpnext.crm.doctype.opportunity.opportunity import make_quotation

quotation = make_quotation("OPP-00001")
quotation.items = [{
    "item_code": "ITEM-001",
    "qty": 10,
    "rate": 100
}]
quotation.insert()
```

### Get Pipeline Analytics
```python
pipeline = frappe.get_all("Opportunity",
    filters={"status": ["not in", ["Lost", "Closed"]]},
    fields=["sales_stage", "sum(opportunity_amount) as total"],
    group_by="sales_stage"
)
```

## Lead Status Flow

```
New → Open → Replied → Interested → Converted
                    ↘ Do Not Contact
```

## Opportunity Stages

1. Prospecting
2. Qualification
3. Needs Analysis
4. Value Proposition
5. Decision Makers
6. Proposal/Quote
7. Negotiation
8. Closed Won / Closed Lost

## Auto-Assignment Rules

Configure automatic lead assignment based on:
- Territory
- Lead source
- Industry
- Round-robin among sales team
- Workload balancing

## Email Integration

- Email-to-Lead creation
- Auto-link emails to customers
- Email tracking (opens, clicks)
- Scheduled follow-ups
- Email templates

## Reports

- Lead Details
- Opportunity Summary
- Sales Funnel
- Campaign Effectiveness
- Territory-wise Sales
- Sales Person Performance
- Lost Opportunity Analysis
