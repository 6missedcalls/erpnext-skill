# ERPNext Support/Helpdesk Module

The Support module provides customer service and issue tracking capabilities.

## Key Features

### Issue Management
- Issue/ticket creation
- Multi-channel intake (email, web, phone)
- Priority and status tracking
- Assignment and escalation
- Resolution tracking

### Service Level Agreements (SLA)
- SLA definitions
- Response time targets
- Resolution time targets
- Priority-based SLAs
- SLA pause/resume

### Warranty & Maintenance
- Warranty claims
- Maintenance schedules
- Asset service history
- Preventive maintenance

### Knowledge Base
- Help articles
- FAQ management
- Self-service portal

### Customer Portal
- Ticket submission
- Status tracking
- Communication history

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `Issue` | Support tickets |
| `Service Level Agreement` | SLA definitions |
| `Warranty Claim` | Warranty issues |
| `Maintenance Schedule` | Preventive maintenance |
| `Maintenance Visit` | Service visits |
| `Issue Type` | Ticket categories |
| `Issue Priority` | Priority levels |

## API Examples

### Create Issue
```python
doc = frappe.get_doc({
    "doctype": "Issue",
    "subject": "Product not working",
    "customer": "CUST-00001",
    "issue_type": "Technical",
    "priority": "High",
    "description": "Detailed description of the issue"
})
doc.insert()
```

### Update Issue Status
```python
doc = frappe.get_doc("Issue", "ISS-00001")
doc.status = "Closed"
doc.resolution_details = "Issue resolved by replacing component"
doc.save()
```

### Create Maintenance Schedule
```python
doc = frappe.get_doc({
    "doctype": "Maintenance Schedule",
    "customer": "CUST-00001",
    "items": [{
        "item_code": "ITEM-001",
        "start_date": "2024-01-01",
        "end_date": "2024-12-31",
        "periodicity": "Monthly"
    }]
})
doc.insert()
doc.submit()
```

### Log Maintenance Visit
```python
doc = frappe.get_doc({
    "doctype": "Maintenance Visit",
    "customer": "CUST-00001",
    "mntc_date": "2024-02-05",
    "purposes": [{
        "item_code": "ITEM-001",
        "work_done": "Routine maintenance completed",
        "service_person": "user@example.com"
    }]
})
doc.insert()
doc.submit()
```

## Issue Workflow

```
Open → Replied → Resolved → Closed
         ↓
    On Hold / Escalated
```

## SLA Configuration

Define SLA based on:
- Issue priority
- Customer group
- Issue type
- Service hours

```python
{
    "doctype": "Service Level Agreement",
    "service_level": "Premium Support",
    "priorities": [{
        "priority": "High",
        "response_time": 2,  # hours
        "resolution_time": 8  # hours
    }]
}
```

## Email Integration

- Email-to-Issue conversion
- Auto-reply on ticket creation
- Agent notifications
- Customer updates
- Email threading

## Reports

- Issue Analytics
- First Response Time
- Average Resolution Time
- SLA Fulfilled/Breached
- Support Hours Summary
- Issue Summary by Type
