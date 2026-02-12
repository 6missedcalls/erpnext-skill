# ERPNext Customization

Extend and customize ERPNext without modifying core code.

## Key Features

### Custom Fields
- Add fields to existing DocTypes
- Field-level permissions
- Conditional visibility

### Custom Scripts
- Client-side JavaScript
- Form events
- Field triggers

### Server Scripts
- Python server-side logic
- API endpoints
- Scheduled scripts

### Custom DocTypes
- Create new document types
- Custom workflows
- Custom reports

### Custom Apps
- Frappe app development
- Module creation
- Hook-based extensions

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `Custom Field` | Additional fields |
| `Client Script` | JavaScript customizations |
| `Server Script` | Python customizations |
| `DocType` | Document definitions |
| `Custom DocPerm` | Custom permissions |
| `Property Setter` | Field property changes |

## API Examples

### Add Custom Field
```python
doc = frappe.get_doc({
    "doctype": "Custom Field",
    "dt": "Customer",
    "fieldname": "custom_priority",
    "label": "Customer Priority",
    "fieldtype": "Select",
    "options": "Low\nMedium\nHigh",
    "insert_after": "customer_name"
})
doc.insert()
```

### Create Client Script
```python
doc = frappe.get_doc({
    "doctype": "Client Script",
    "name": "Customer Custom Script",
    "dt": "Customer",
    "script": """
frappe.ui.form.on("Customer", {
    refresh: function(frm) {
        frm.add_custom_button("Special Action", function() {
            frappe.msgprint("Button clicked!");
        });
    }
});
"""
})
doc.insert()
```

### Create Server Script
```python
doc = frappe.get_doc({
    "doctype": "Server Script",
    "name": "Customer Validation",
    "script_type": "DocType Event",
    "reference_doctype": "Customer",
    "doctype_event": "Before Save",
    "script": """
if not doc.customer_name:
    frappe.throw("Customer name is required")
"""
})
doc.insert()
```

### Create API Endpoint
```python
doc = frappe.get_doc({
    "doctype": "Server Script",
    "name": "Get Customer Stats",
    "script_type": "API",
    "api_method": "get_customer_stats",
    "script": """
customer = frappe.form_dict.get('customer')
stats = frappe.db.sql('''
    SELECT COUNT(*) as orders, SUM(grand_total) as total
    FROM `tabSales Order`
    WHERE customer = %s
''', customer, as_dict=True)
frappe.response['message'] = stats[0]
"""
})
doc.insert()
```

## Custom App Development

### Create App
```bash
bench new-app myapp
bench --site mysite install-app myapp
```

### App Structure
```
myapp/
├── myapp/
│   ├── __init__.py
│   ├── hooks.py
│   ├── modules.txt
│   ├── patches.txt
│   └── mymodule/
│       ├── doctype/
│       └── report/
├── setup.py
└── requirements.txt
```

### Hooks Example
```python
# hooks.py
doc_events = {
    "Sales Invoice": {
        "on_submit": "myapp.events.on_invoice_submit"
    }
}

scheduler_events = {
    "daily": ["myapp.tasks.daily_task"]
}

override_whitelisted_methods = {
    "erpnext.selling.doctype.quotation.quotation.make_sales_order":
        "myapp.overrides.custom_make_sales_order"
}
```

## Property Setter

Modify existing field properties:
```python
doc = frappe.get_doc({
    "doctype": "Property Setter",
    "doctype_or_field": "DocField",
    "doc_type": "Customer",
    "field_name": "customer_name",
    "property": "reqd",
    "value": "1"
})
doc.insert()
```

## Custom Reports

### Script Report
```python
# myapp/mymodule/report/custom_report/custom_report.py
def execute(filters=None):
    columns = [
        {"fieldname": "name", "label": "Customer", "fieldtype": "Link", "options": "Customer"},
        {"fieldname": "total", "label": "Total Sales", "fieldtype": "Currency"}
    ]

    data = frappe.db.sql("""
        SELECT customer as name, SUM(grand_total) as total
        FROM `tabSales Invoice`
        WHERE docstatus = 1
        GROUP BY customer
    """, as_dict=True)

    return columns, data
```

## Best Practices

1. **Never modify core** - Use Custom Fields, Scripts, Apps
2. **Use hooks** - Extend behavior without overriding
3. **Version control** - Keep customizations in git
4. **Test upgrades** - Verify custom code after updates
5. **Document changes** - Maintain customization log
