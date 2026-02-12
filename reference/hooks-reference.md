# Frappe Hooks Reference

Hooks allow you to extend Frappe functionality without modifying core code. Defined in `hooks.py`.

## App Metadata

```python
app_name = "myapp"
app_title = "My App"
app_publisher = "My Company"
app_description = "Description"
app_version = "1.0.0"
```

## JavaScript / CSS Assets

### Desk

```python
app_include_js = "assets/js/app.min.js"
app_include_css = "assets/css/app.min.css"

# Multiple files
app_include_js = ["assets/js/app1.min.js", "assets/js/app2.min.js"]
```

### Portal

```python
web_include_js = "assets/js/app-web.min.js"
web_include_css = "assets/css/app-web.min.css"
```

### Web Form

```python
webform_include_js = {"ToDo": "public/js/custom_todo.js"}
webform_include_css = {"ToDo": "public/css/custom_todo.css"}
```

### Page

```python
page_js = {"background_jobs": "public/js/custom_background_jobs.js"}
```

## Install/Uninstall Hooks

```python
before_install = "myapp.setup.install.before_install"
after_install = "myapp.setup.install.after_install"
after_sync = "myapp.setup.install.after_sync"

before_uninstall = "myapp.setup.uninstall.before_uninstall"
after_uninstall = "myapp.setup.uninstall.after_uninstall"
```

## Migrate Hooks

```python
before_migrate = "myapp.migrate.before_migrate"
after_migrate = "myapp.migrate.after_migrate"
```

## Test Hooks

```python
before_tests = "myapp.tests.before_tests"
```

## Document Events

```python
doc_events = {
    "Sales Order": {
        "validate": "myapp.events.sales_order.validate",
        "on_submit": "myapp.events.sales_order.on_submit",
        "on_cancel": "myapp.events.sales_order.on_cancel"
    },
    "*": {
        "after_insert": "myapp.events.audit.log_creation"
    }
}
```

**Event handler signature:**
```python
def validate(doc, method):
    # doc is the document, method is the event name
    pass
```

## Scheduler Events

```python
scheduler_events = {
    "hourly": [
        "myapp.tasks.hourly.process_queue"
    ],
    "daily": [
        "myapp.tasks.daily.cleanup_old_logs"
    ],
    "weekly": [
        "myapp.tasks.weekly.send_summary_report"
    ],
    "monthly": [
        "myapp.tasks.monthly.generate_report"
    ],
    "cron": {
        "0 9 * * 1": [  # Every Monday at 9 AM
            "myapp.tasks.weekly.monday_reminder"
        ],
        "*/5 * * * *": [  # Every 5 minutes
            "myapp.tasks.frequent.check_integrations"
        ]
    }
}
```

## Override Whitelisted Methods

```python
override_whitelisted_methods = {
    "frappe.client.get_list": "myapp.overrides.custom_get_list"
}
```

## Fixtures

```python
fixtures = [
    "Custom Field",
    "Property Setter",
    {"dt": "Role", "filters": [["name", "like", "Custom%"]]},
    {"dt": "Workflow", "filters": [["document_type", "=", "Sales Order"]]}
]
```

## Extend Bootinfo

```python
extend_bootinfo = "myapp.boot.get_bootinfo"
```

```python
# myapp/boot.py
def get_bootinfo(bootinfo):
    bootinfo.my_key = "my_value"
```

## Session Hooks

```python
on_login = "myapp.overrides.successful_login"
on_session_creation = "myapp.overrides.allocate_free_credits"
on_logout = "myapp.overrides.clear_user_cache"
```

## Auth Hooks

```python
auth_hooks = ["myapp.overrides.validate_custom_jwt"]
```

## Website Hooks

### Context

```python
website_context = {
    "favicon": "/assets/myapp/image/favicon.png"
}
update_website_context = "myapp.overrides.website_context"
```

### Route Rules

```python
website_route_rules = [
    {"from_route": "/projects/<name>", "to_route": "myapp/projects/project"},
]
```

### Redirects

```python
website_redirects = [
    {"source": "/compare", "target": "/comparison"},
    {"source": "/docs(/.*)?", "target": "https://docs.example.com/\\1"},
]
```

### Homepage

```python
homepage = "homepage"

role_home_page = {
    "Customer": "orders",
    "Supplier": "bills"
}

get_website_user_home_page = "myapp.website.get_home_page"
```

### Portal Menu

```python
portal_menu_items = [
    {"title": "Dashboard", "route": "/dashboard", "role": "Customer"},
]

standard_portal_menu_items = [
    {"title": "Dashboard", "route": "/dashboard", "role": "Website Manager"},
]
```

### Clear Cache

```python
website_clear_cache = "myapp.overrides.clear_website_cache"
```

### 404

```python
website_catch_all = "not_found"
```

## Email Hooks

```python
override_email_send = "myapp.overrides.email.send"
get_sender_details = "myapp.overrides.email.get_sender_details"
```

## File Hooks

```python
before_write_file = "myapp.overrides.file.before_write"
write_file = "myapp.overrides.file.write_file"
delete_file_data_content = "myapp.overrides.file.delete_file"
```

## Default Mail Footer

```python
default_mail_footer = """
<div>
    Sent via <a href="https://example.com">My App</a>
</div>
"""
```

## Base Template

```python
base_template = "myapp/templates/my_custom_base.html"

base_template_map = {
    r"docs.*": "myapp/templates/doc_template.html"
}
```

## Calendars

```python
calendars = ["Appointment", "Task"]
```

## Clear Cache Hook

```python
clear_cache = "myapp.cache.clear_cache"
```

## Hook Resolution

Hooks are resolved using "last writer wins" strategy. Last installed app has highest priority. Change order in "Installed Applications" > "Update Hooks Resolution Order".
