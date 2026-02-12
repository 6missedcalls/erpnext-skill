# Frappe Framework Complete Reference

## What is Frappe Framework?

Frappe is a full stack, batteries-included, web framework written in Python and Javascript. It powers ERPNext and can be used to build any database-driven app.

### Key Concepts
- **DocType** = Model (database table + metadata + behavior)
- **Document** = Instance of DocType (single row)
- **Controller** = Python class for business logic
- **hooks.py** = App-level event handlers and configuration

### Tech Stack
- **Backend**: Python, MariaDB/PostgreSQL
- **Frontend**: JavaScript (jQuery), Jinja templates
- **Realtime**: Node.js + Socket.io
- **Background Jobs**: Python RQ + Redis

---

## Bench & Project Structure

### Create a Frappe Bench
```bash
bench init frappe-bench
cd frappe-bench
bench start
```

### Directory Structure
```
frappe-bench/
├── apps/                    # Frappe apps
│   └── frappe/             # Core framework
├── sites/                   # Sites directory
│   ├── assets/             # Static assets
│   ├── apps.txt            # Installed apps list
│   └── common_site_config.json
├── env/                     # Python virtualenv
├── config/                  # Redis, Nginx configs
├── logs/                    # Log files
└── Procfile                 # Dev server processes
```

### Create an App
```bash
bench new-app my_app
```

App structure:
```
my_app/
├── my_app/
│   ├── hooks.py            # App hooks
│   ├── modules.txt         # Module list
│   ├── patches.txt         # Migration patches
│   ├── my_app/             # Default module
│   │   └── doctype/        # DocTypes
│   ├── public/             # Static files (JS/CSS)
│   ├── templates/          # Jinja templates
│   └── www/                # Web pages
└── pyproject.toml
```

### Create a Site
```bash
bench new-site mysite.localhost
bench --site mysite.localhost install-app my_app
```

---

## DocTypes

### Creating a DocType
1. Enable developer mode: `bench set-config -g developer_mode true`
2. Go to Desk > DocType > New
3. Add fields, save

Creates:
- `{doctype}.json` - DocType definition
- `{doctype}.py` - Python controller
- `{doctype}.js` - Client-side controller
- `test_{doctype}.py` - Test file
- Database table `tab{DocType}`

### Field Types
| Type | Description |
|------|-------------|
| Data | Single line text |
| Text | Multi-line text |
| Text Editor | Rich text (HTML) |
| Int | Integer |
| Float | Decimal |
| Currency | Money |
| Date | Date picker |
| Datetime | Date + time |
| Time | Time picker |
| Select | Dropdown |
| Link | Foreign key to another DocType |
| Table | Child table (one-to-many) |
| Check | Boolean checkbox |
| Attach | File attachment |
| Attach Image | Image attachment |
| Password | Encrypted password |
| Read Only | Display only |
| HTML | Custom HTML |
| Section Break | Form section |
| Column Break | Form column |

### Naming Rules
```python
# In DocType settings or controller
autoname = "field:customer_name"           # Use field value
autoname = "naming_series:"                # Series like INV-0001
autoname = "hash"                          # Random hash
autoname = "format:PRJ-{####}"            # Custom format
autoname = "Prompt"                        # User enters name

# Or in controller:
def autoname(self):
    self.name = f"CUST-{self.customer_type}-{frappe.utils.now()}"
```

### DocType Types
1. **Regular** - Normal table with multiple records
2. **Single** - Only one record (settings)
3. **Child** - Used inside Table fields
4. **Virtual** - No database table, data from external source

---

## Document API

### Get/Create Documents
```python
# Get existing document
doc = frappe.get_doc("Customer", "CUST-001")

# Get with cache (for frequently accessed docs)
doc = frappe.get_cached_doc("Company", "My Company")

# Create new document
doc = frappe.get_doc({
    "doctype": "Customer",
    "customer_name": "John Doe",
    "customer_type": "Individual"
})
doc.insert()

# Alternative
doc = frappe.new_doc("Customer")
doc.customer_name = "John Doe"
doc.insert()
```

### Document Methods
```python
# Insert (new document)
doc.insert(
    ignore_permissions=True,    # Skip permission check
    ignore_links=True,          # Skip link validation
    ignore_if_duplicate=True,   # Don't error on duplicate
    ignore_mandatory=True       # Skip mandatory check
)

# Save (existing document)
doc.save(
    ignore_permissions=True,
    ignore_version=True         # Don't create version
)

# Delete
doc.delete()
frappe.delete_doc("Customer", "CUST-001")

# Rename
frappe.rename_doc("Customer", "OLD-001", "NEW-001")

# Reload from database
doc.reload()

# Direct database update (bypasses validation!)
doc.db_set("status", "Active")
doc.db_set("status", "Active", notify=True)      # Trigger realtime
doc.db_set("status", "Active", commit=True)      # Immediate commit
doc.db_set("status", "Active", update_modified=False)  # Keep timestamp

# Change detection
old_doc = doc.get_doc_before_save()
if doc.has_value_changed("status"):
    # status changed
    pass

# Child table operations
doc.append("items", {
    "item_code": "ITEM-001",
    "qty": 10
})
doc.set("items", [])  # Clear child table
```

---

## Database API

### Query Methods
```python
# Get list (respects permissions)
customers = frappe.db.get_list("Customer",
    filters={"status": "Active"},
    fields=["name", "customer_name", "email"],
    order_by="creation desc",
    start=0,
    page_length=20
)

# Get all (ignores permissions)
all_customers = frappe.db.get_all("Customer",
    filters={"status": "Active"},
    fields=["name", "customer_name"]
)

# Pluck single field
names = frappe.db.get_all("Customer", pluck="name")
# Returns: ["CUST-001", "CUST-002", ...]

# Get single value
email = frappe.db.get_value("Customer", "CUST-001", "email")

# Get multiple values
name, email = frappe.db.get_value("Customer", "CUST-001", ["customer_name", "email"])

# As dict
data = frappe.db.get_value("Customer", "CUST-001", ["customer_name", "email"], as_dict=True)

# Get from Single DocType
timezone = frappe.db.get_single_value("System Settings", "time_zone")

# Check existence
if frappe.db.exists("Customer", "CUST-001"):
    pass

# Count
count = frappe.db.count("Customer", {"status": "Active"})
```

### Filter Operators
```python
# Equals
{"status": "Active"}
[["status", "=", "Active"]]

# Not equals
[["status", "!=", "Inactive"]]

# Greater/Less than
[["amount", ">", 1000]]
[["amount", ">=", 1000]]
[["date", "<", "2024-01-01"]]

# Like
[["name", "like", "%search%"]]

# In list
[["status", "in", ["Open", "Pending"]]]
[["status", "not in", ["Closed"]]]

# Between
[["date", "between", ["2024-01-01", "2024-12-31"]]]

# Is null
[["email", "is", "set"]]
[["email", "is", "not set"]]

# OR filters
frappe.db.get_list("Customer",
    filters={"status": "Active"},
    or_filters=[
        ["region", "=", "East"],
        ["region", "=", "West"]
    ]
)
```

### Write Operations
```python
# Set single value
frappe.db.set_value("Customer", "CUST-001", "status", "Active")

# Set multiple values
frappe.db.set_value("Customer", "CUST-001", {
    "status": "Active",
    "modified_reason": "Reactivated"
})

# Bulk update
frappe.db.bulk_update("Customer", {
    "CUST-001": {"status": "Active"},
    "CUST-002": {"status": "Active"},
    "CUST-003": {"status": "Inactive"}
})

# Delete
frappe.db.delete("Error Log", {"creation": ["<", older_than]})

# Truncate (DDL - can't rollback!)
frappe.db.truncate("Error Log")
```

### Transactions
```python
# Auto-commit on POST/PUT requests
# Auto-rollback on exceptions

# Manual commit (rarely needed)
frappe.db.commit()

# Savepoint for partial rollback
frappe.db.savepoint("before_risky_op")
try:
    risky_operation()
except:
    frappe.db.rollback(save_point="before_risky_op")
    fallback_operation()

# Full rollback
frappe.db.rollback()
```

### Raw SQL (use sparingly)
```python
result = frappe.db.sql("""
    SELECT customer_name, SUM(grand_total) as total
    FROM `tabSales Order`
    WHERE company = %(company)s
    GROUP BY customer_name
    ORDER BY total DESC
""", {"company": "My Company"}, as_dict=True)
```

---

## Controllers

### Controller Hooks (Lifecycle)
```python
class Customer(Document):
    def autoname(self):
        """Set document name"""
        self.name = f"CUST-{self.customer_type[:3]}-{frappe.generate_hash(length=5)}"
    
    def before_insert(self):
        """Before first save"""
        self.set_defaults()
    
    def validate(self):
        """Validation - runs on insert AND save"""
        if not self.email:
            frappe.throw("Email is required")
        self.calculate_totals()
    
    def before_save(self):
        """After validate, before DB write"""
        self.set_status()
    
    def on_update(self):
        """After save to DB"""
        self.notify_changes()
    
    def after_insert(self):
        """After first save only"""
        self.send_welcome_email()
    
    def before_submit(self):
        """Before submit"""
        self.validate_for_submit()
    
    def on_submit(self):
        """After submit"""
        self.create_linked_records()
    
    def before_cancel(self):
        """Before cancel"""
        self.check_linked_records()
    
    def on_cancel(self):
        """After cancel"""
        self.reverse_linked_records()
    
    def on_trash(self):
        """Before delete"""
        self.cleanup()
    
    def after_delete(self):
        """After delete"""
        pass
```

### Lifecycle Order
```
Insert:  autoname → before_insert → validate → before_save → [DB INSERT] → on_update → after_insert
Save:    validate → before_save → [DB UPDATE] → on_update
Submit:  before_submit → [DB UPDATE docstatus=1] → on_submit
Cancel:  before_cancel → [DB UPDATE docstatus=2] → on_cancel
Delete:  on_trash → [DB DELETE] → after_delete
```

### Using Flags
```python
class Task(Document):
    def validate(self):
        self.flags.skip_notification = True
    
    def on_update(self):
        if not self.flags.get("skip_notification"):
            self.notify()

# From outside
doc = frappe.get_doc("Task", "TASK-001")
doc.flags.ignore_validate = True
doc.save()
```

---

## hooks.py

### Document Events
```python
doc_events = {
    "Sales Order": {
        "validate": "my_app.events.sales_order.validate",
        "on_submit": "my_app.events.sales_order.on_submit",
        "on_cancel": "my_app.events.sales_order.on_cancel"
    },
    "*": {
        # Runs for ALL DocTypes
        "after_insert": "my_app.events.audit.log_creation"
    }
}
```

Handler signature:
```python
def validate(doc, method):
    # doc = document instance
    # method = "validate"
    if doc.grand_total > 100000:
        doc.requires_approval = 1
```

### Scheduler Events
```python
scheduler_events = {
    "hourly": ["my_app.tasks.hourly_job"],
    "daily": ["my_app.tasks.daily_cleanup"],
    "weekly": ["my_app.tasks.weekly_report"],
    "monthly": ["my_app.tasks.monthly_archive"],
    "cron": {
        "0 9 * * 1": ["my_app.tasks.monday_reminder"],  # Mon 9am
        "*/5 * * * *": ["my_app.tasks.every_5_minutes"]
    }
}
```

### Other Hooks
```python
# App metadata
app_name = "my_app"
app_title = "My App"
app_publisher = "My Company"
app_version = "1.0.0"

# Assets
app_include_js = "/assets/my_app/js/app.js"
app_include_css = "/assets/my_app/css/app.css"
web_include_js = "/assets/my_app/js/web.js"

# Install/Migrate
before_install = "my_app.setup.before_install"
after_install = "my_app.setup.after_install"
after_migrate = "my_app.setup.after_migrate"

# Override methods
override_whitelisted_methods = {
    "frappe.client.get_list": "my_app.overrides.custom_get_list"
}

# Fixtures (sync to database)
fixtures = [
    "Custom Field",
    "Property Setter",
    {"dt": "Role", "filters": [["name", "like", "My%"]]}
]

# Extend bootinfo (global JS state)
extend_bootinfo = "my_app.boot.get_bootinfo"

# Session hooks
on_login = "my_app.auth.on_login"
on_logout = "my_app.auth.on_logout"
```

---

## REST API

### Authentication
```python
# Token auth (API Key + Secret)
headers = {"Authorization": "token api_key:api_secret"}

# OAuth Bearer
headers = {"Authorization": "Bearer access_token"}

# Session (cookie-based)
POST /api/method/login
{"usr": "email", "pwd": "password"}
```

### CRUD Endpoints
```
# List
GET /api/resource/{doctype}?fields=["name","status"]&filters=[["status","=","Open"]]

# Read
GET /api/resource/{doctype}/{name}

# Create
POST /api/resource/{doctype}
{"field1": "value1", "field2": "value2"}

# Update
PUT /api/resource/{doctype}/{name}
{"field1": "new_value"}

# Delete
DELETE /api/resource/{doctype}/{name}
```

### Custom Methods
```python
@frappe.whitelist()
def my_method(arg1, arg2):
    return {"result": arg1 + arg2}

# Call via:
# GET/POST /api/method/my_app.api.my_method?arg1=val1&arg2=val2
```

### Whitelist Decorators
```python
@frappe.whitelist()
def secure_method():
    """Requires login"""
    pass

@frappe.whitelist(allow_guest=True)
def public_method():
    """No login required"""
    pass

@frappe.whitelist(methods=["POST"])
def write_only():
    """Only POST requests"""
    pass
```

---

## Form Scripts (Client-Side JS)

### Event Handlers
```javascript
frappe.ui.form.on('Customer', {
    setup(frm) {
        // First time form created
        frm.set_query('territory', () => ({
            filters: {is_group: 0}
        }));
    },
    
    onload(frm) {
        // Form loaded
    },
    
    refresh(frm) {
        // Form rendered
        if (!frm.is_new()) {
            frm.add_custom_button('Do Something', () => {
                frm.trigger('custom_action');
            });
        }
    },
    
    validate(frm) {
        // Before save
        if (!frm.doc.email) {
            frappe.msgprint('Email required');
            frappe.validated = false;
        }
    },
    
    // Field change handler
    customer_type(frm) {
        if (frm.doc.customer_type === 'Company') {
            frm.set_value('is_corporate', 1);
        }
    },
    
    custom_action(frm) {
        frappe.call({
            method: 'my_app.api.process_customer',
            args: {customer: frm.doc.name},
            callback(r) {
                if (r.message) {
                    frappe.msgprint(r.message);
                }
            }
        });
    }
});
```

### Child Table Events
```javascript
frappe.ui.form.on('Sales Order Item', {
    items_add(frm, cdt, cdn) {
        // Row added
        let row = frappe.get_doc(cdt, cdn);
        row.qty = 1;
        frm.refresh_field('items');
    },
    
    item_code(frm, cdt, cdn) {
        // Field changed in row
        let row = frappe.get_doc(cdt, cdn);
        frappe.call({
            method: 'get_item_price',
            args: {item_code: row.item_code},
            callback(r) {
                frappe.model.set_value(cdt, cdn, 'rate', r.message);
            }
        });
    }
});
```

### Form API
```javascript
// Set value (returns promise)
frm.set_value('status', 'Active').then(() => {});

// Refresh
frm.refresh();
frm.reload_doc();  // Reload from server

// Save
frm.save();
frm.save('Submit');
frm.save('Cancel');

// Field properties
frm.set_df_property('field', 'hidden', 1);
frm.set_df_property('field', 'reqd', 1);
frm.set_df_property('field', 'read_only', 1);

// Toggle helpers
frm.toggle_display('field', condition);
frm.toggle_enable('field', condition);
frm.toggle_reqd('field', condition);

// Link field filters
frm.set_query('customer', () => ({
    filters: {disabled: 0}
}));

// Add child row
let row = frm.add_child('items', {item_code: 'ITEM-001'});
frm.refresh_field('items');

// Call server method
frm.call('get_pending_amount', {customer: frm.doc.customer})
    .then(r => console.log(r.message));
```

---

## Background Jobs

### Enqueue Jobs
```python
# Basic enqueue
frappe.enqueue(
    "my_app.tasks.long_running_task",
    arg1="value1",
    arg2="value2"
)

# With options
frappe.enqueue(
    "my_app.tasks.process_data",
    queue="long",           # short, default, long
    timeout=3600,           # seconds
    is_async=True,
    job_name="process_xyz",
    now=False,              # True = run immediately (sync)
    enqueue_after_commit=True,
    data=large_data
)

# Document method
doc.queue_action('send_emails', emails=email_list)
```

### Task Function
```python
def long_running_task(arg1, arg2):
    """Background job function"""
    items = get_items()
    total = len(items)
    
    for i, item in enumerate(items):
        process_item(item)
        
        # Update progress
        frappe.publish_progress(
            i * 100 / total,
            title="Processing",
            description=f"Item {i+1} of {total}"
        )
        
        # Commit periodically
        if i % 100 == 0:
            frappe.db.commit()
    
    # Final commit
    frappe.db.commit()
```

---

## Utilities

### Common Functions
```python
from frappe.utils import (
    now, today, getdate, add_to_date, date_diff,
    cint, flt, cstr,
    random_string, get_url,
    validate_email_address, validate_phone_number
)

# Date/Time
now()                           # '2024-01-15 10:30:00'
today()                         # '2024-01-15'
getdate('2024-01-15')          # datetime.date object
add_to_date(today(), days=10)  # 10 days later
date_diff(date2, date1)        # days between

# Type conversion
cint("123")     # 123
flt("123.45")   # 123.45
cstr(123)       # "123"

# Misc
random_string(10)               # Random alphanumeric
get_url()                       # Site URL
frappe.generate_hash(length=8)  # Random hash
```

### Email
```python
frappe.sendmail(
    recipients=["user@example.com"],
    subject="Hello",
    message="Email body",
    template="my_template",
    args={"name": "John"},
    attachments=[{"file_url": "/files/doc.pdf"}]
)
```

### Cache
```python
cache = frappe.cache()

# Simple key-value
cache.set_value("key", "value")
value = cache.get_value("key")

# With expiry
cache.set_value("key", "value", expires_in_sec=3600)

# Hash-based
cache.hset("hash_name", "field", "value")
value = cache.hget("hash_name", "field")
```

### Realtime
```python
# Server
frappe.publish_realtime(
    "event_name",
    {"data": "value"},
    user="user@example.com"  # or room="room_name"
)

# Client
frappe.realtime.on("event_name", (data) => {
    console.log(data);
});
```

---

## Testing

### Test File
```python
# test_customer.py
import frappe
from frappe.tests import IntegrationTestCase

class TestCustomer(IntegrationTestCase):
    def setUp(self):
        self.customer = frappe.get_doc({
            "doctype": "Customer",
            "customer_name": "Test Customer"
        }).insert()
    
    def tearDown(self):
        frappe.delete_doc("Customer", self.customer.name, force=True)
    
    def test_customer_creation(self):
        self.assertEqual(self.customer.customer_name, "Test Customer")
    
    def test_validation_error(self):
        with self.assertRaises(frappe.ValidationError):
            frappe.get_doc({
                "doctype": "Customer"
                # Missing required field
            }).insert()
```

### Run Tests
```bash
# All tests
bench --site mysite run-tests

# Specific app
bench --site mysite run-tests --app my_app

# Specific DocType
bench --site mysite run-tests --doctype Customer

# Specific module
bench --site mysite run-tests --module my_app.tests.test_api
```

---

## Common Patterns

### Upsert
```python
def upsert_customer(data):
    if frappe.db.exists("Customer", data["name"]):
        doc = frappe.get_doc("Customer", data["name"])
        doc.update(data)
        doc.save()
    else:
        doc = frappe.get_doc({"doctype": "Customer", **data})
        doc.insert()
    return doc
```

### Bulk Import
```python
def bulk_import(items, batch_size=100):
    for i in range(0, len(items), batch_size):
        batch = items[i:i+batch_size]
        for item in batch:
            try:
                doc = frappe.get_doc({"doctype": "Item", **item})
                doc.insert(ignore_permissions=True)
            except frappe.DuplicateEntryError:
                pass
        frappe.db.commit()
        frappe.publish_progress(i, len(items), "Importing")
```

### Permission Check
```python
@frappe.whitelist()
def sensitive_action(doctype, name):
    if not frappe.has_permission(doctype, "write", name):
        frappe.throw("Not permitted", frappe.PermissionError)
    
    # proceed with action
```
