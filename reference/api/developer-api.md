# ERPNext Developer API Reference

The Frappe Framework provides comprehensive APIs for server-side and client-side development.

## Python APIs

All core functionality is exposed through the `frappe` namespace.

### Document Operations

```python
import frappe

# Get document
doc = frappe.get_doc("Customer", "CUST-00001")

# Create document
doc = frappe.get_doc({
    "doctype": "Customer",
    "customer_name": "New Customer",
    "customer_type": "Company"
})
doc.insert()

# Update document
doc = frappe.get_doc("Customer", "CUST-00001")
doc.customer_name = "Updated Name"
doc.save()

# Delete document
frappe.delete_doc("Customer", "CUST-00001")

# Check existence
if frappe.db.exists("Customer", "CUST-00001"):
    pass

# Get value
name = frappe.db.get_value("Customer", "CUST-00001", "customer_name")

# Get list
customers = frappe.get_list("Customer",
    filters={"territory": "United States"},
    fields=["name", "customer_name"],
    order_by="modified desc",
    limit=10
)
```

### Database API

```python
# SQL query
results = frappe.db.sql("""
    SELECT name, customer_name
    FROM `tabCustomer`
    WHERE territory = %s
""", ("United States",), as_dict=True)

# Get single value
count = frappe.db.count("Customer", {"territory": "United States"})

# Set value (without loading document)
frappe.db.set_value("Customer", "CUST-00001", "customer_name", "New Name")

# Commit transaction
frappe.db.commit()

# Get all values
values = frappe.db.get_all("Customer",
    filters={"territory": "United States"},
    fields=["name", "customer_name"]
)
```

### Whitelisted Methods

```python
@frappe.whitelist()
def my_custom_method(customer_name):
    """Callable via REST API"""
    doc = frappe.get_doc("Customer", customer_name)
    return doc.as_dict()

@frappe.whitelist(allow_guest=True)
def public_method():
    """Accessible without authentication"""
    return {"status": "ok"}
```

### Background Jobs

```python
# Enqueue background job
frappe.enqueue(
    "myapp.tasks.process_data",
    queue="long",
    timeout=600,
    customer_name="CUST-00001"
)

# Schedule job
frappe.enqueue(
    "myapp.tasks.daily_task",
    at_front=True,
    enqueue_after_commit=True
)
```

### Event Hooks

```python
# hooks.py
doc_events = {
    "Customer": {
        "validate": "myapp.customer.validate_customer",
        "on_update": "myapp.customer.on_customer_update",
        "before_save": "myapp.customer.before_save",
        "after_insert": "myapp.customer.after_insert"
    }
}

# Implementation
def validate_customer(doc, method):
    if not doc.customer_name:
        frappe.throw("Customer name is required")

def on_customer_update(doc, method):
    frappe.publish_realtime("customer_updated", {"name": doc.name})
```

### Utilities

```python
# Logging
frappe.log_error("Error message", "Error Title")
frappe.logger().info("Info message")

# Throw error
frappe.throw("Validation error message")
frappe.msgprint("Information message")

# Get current user
user = frappe.session.user

# Check permissions
if frappe.has_permission("Customer", "write", doc):
    pass

# Format currency/date
formatted = frappe.format_value(1000, {"fieldtype": "Currency"})
date_str = frappe.format_date(frappe.utils.today())

# Send email
frappe.sendmail(
    recipients=["user@example.com"],
    subject="Subject",
    message="Email body",
    attachments=[{"fname": "file.pdf", "fcontent": content}]
)
```

## JavaScript APIs

APIs attached to `window.frappe` namespace in the Desk.

### Form API

```javascript
frappe.ui.form.on("Customer", {
    refresh: function(frm) {
        // Add custom button
        frm.add_custom_button(__("Custom Action"), function() {
            frappe.call({
                method: "myapp.api.custom_method",
                args: { customer: frm.doc.name },
                callback: function(r) {
                    frappe.msgprint(r.message);
                }
            });
        });
    },

    validate: function(frm) {
        if (!frm.doc.customer_name) {
            frappe.throw(__("Customer name is required"));
        }
    },

    customer_name: function(frm) {
        // Field change handler
        frm.set_value("custom_field", frm.doc.customer_name.toUpperCase());
    }
});
```

### Server Calls (AJAX)

```javascript
// Call whitelisted method
frappe.call({
    method: "myapp.api.get_customer_data",
    args: {
        customer_name: "CUST-00001"
    },
    freeze: true,
    freeze_message: __("Loading..."),
    callback: function(r) {
        if (r.message) {
            console.log(r.message);
        }
    },
    error: function(r) {
        frappe.msgprint(__("Error occurred"));
    }
});

// Async/await style
const response = await frappe.call({
    method: "myapp.api.async_method",
    args: { param: "value" }
});
```

### Dialog API

```javascript
// Simple dialog
let d = new frappe.ui.Dialog({
    title: __("Enter Details"),
    fields: [
        {
            label: __("Customer Name"),
            fieldname: "customer_name",
            fieldtype: "Link",
            options: "Customer",
            reqd: 1
        },
        {
            label: __("Amount"),
            fieldname: "amount",
            fieldtype: "Currency"
        }
    ],
    primary_action_label: __("Submit"),
    primary_action: function(values) {
        console.log(values);
        d.hide();
    }
});
d.show();

// Confirmation dialog
frappe.confirm(
    __("Are you sure?"),
    function() { /* Yes */ },
    function() { /* No */ }
);

// Prompt
frappe.prompt(
    { fieldname: "reason", label: __("Reason"), fieldtype: "Text" },
    function(values) { console.log(values.reason); },
    __("Enter Reason")
);
```

### List & Page API

```javascript
// Refresh list
frappe.listview_settings["Customer"] = {
    add_fields: ["custom_field"],
    get_indicator: function(doc) {
        if (doc.disabled) {
            return [__("Disabled"), "grey", "disabled,=,1"];
        }
        return [__("Active"), "green", "disabled,=,0"];
    },
    onload: function(listview) {
        listview.page.add_action_item(__("Custom Action"), function() {
            // Bulk action
        });
    }
};
```

### Realtime Events

```javascript
// Subscribe to events
frappe.realtime.on("customer_updated", function(data) {
    console.log("Customer updated:", data.name);
    cur_frm.reload_doc();
});

// Publish from server
// frappe.publish_realtime("customer_updated", {"name": doc.name})
```

## Query Builder

```python
from frappe.query_builder import DocType

Customer = DocType("Customer")

# Select query
query = (
    frappe.qb.from_(Customer)
    .select(Customer.name, Customer.customer_name)
    .where(Customer.territory == "United States")
    .orderby(Customer.modified, order=frappe.qb.desc)
    .limit(10)
)
results = query.run(as_dict=True)

# Join query
SalesOrder = DocType("Sales Order")
query = (
    frappe.qb.from_(Customer)
    .join(SalesOrder).on(SalesOrder.customer == Customer.name)
    .select(Customer.customer_name, SalesOrder.grand_total)
)
```

## DocType Controller

```python
# customer.py
import frappe
from frappe.model.document import Document

class Customer(Document):
    def validate(self):
        self.validate_customer_name()

    def validate_customer_name(self):
        if not self.customer_name:
            frappe.throw("Customer name is required")

    def before_save(self):
        self.modified_by = frappe.session.user

    def after_insert(self):
        self.send_welcome_email()

    def on_update(self):
        self.clear_cache()

    def on_trash(self):
        self.validate_linked_records()
```
