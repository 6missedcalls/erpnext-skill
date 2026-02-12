# Frappe Database API Reference

## frappe.db.get_list

```python
frappe.db.get_list(doctype, filters, or_filters, fields, order_by, group_by, start, page_length)
```

Returns a list of records from a doctype table. Also aliased to `frappe.get_list`. Applies user permissions.

```python
frappe.db.get_list('Employee')
# [{'name': 'HR-EMP-00008'}, {'name': 'HR-EMP-00006'}, ...]

# with pluck
frappe.db.get_list('Employee', pluck='name')
# ['HR-EMP-00008', 'HR-EMP-00006', ...]

# with filters and options
frappe.db.get_list('Task',
    filters={'status': 'Open'},
    fields=['subject', 'date'],
    order_by='date desc',
    start=10,
    page_length=20,
    as_list=True
)
```

### Filter Operators

```python
# Greater than
filters={'date': ['>', '2019-09-08']}

# Between (inclusive)
filters=[['date', 'between', ['2020-04-01', '2021-03-31']]]

# Like
filters={'subject': ['like', '%test%']}

# In
filters={'status': ['in', ['Open', 'Pending']]}
```

### Group By

```python
frappe.db.get_list('Task',
    fields=['count(name) as count', 'status'],
    group_by='status'
)
```

## frappe.db.get_all

Same as `frappe.db.get_list` but fetches all records WITHOUT applying permissions.

## frappe.db.get_value

```python
frappe.db.get_value(doctype, name, fieldname)
frappe.db.get_value(doctype, filters, fieldname)
```

```python
# single value
subject = frappe.db.get_value('Task', 'TASK00002', 'subject')

# multiple values
subject, description = frappe.db.get_value('Task', 'TASK00002', ['subject', 'description'])

# as dict
task_dict = frappe.db.get_value('Task', 'TASK00002', ['subject', 'description'], as_dict=1)

# with filters
subject, description = frappe.db.get_value('Task', {'status': 'Open'}, ['subject', 'description'])
```

## frappe.db.get_single_value

```python
timezone = frappe.db.get_single_value('System Settings', 'timezone')
```

## frappe.db.set_value

```python
frappe.db.set_value(doctype, name, fieldname, value)
```

Sets a field's value directly in the database. Does NOT call ORM triggers.

```python
# update a field
frappe.db.set_value('Task', 'TASK00002', 'subject', 'New Subject')

# update multiple values
frappe.db.set_value('Task', 'TASK00002', {
    'subject': 'New Subject',
    'description': 'New Description'
})

# without updating modified
frappe.db.set_value('Task', 'TASK00002', 'subject', 'New Subject', update_modified=False)
```

## frappe.db.exists

```python
frappe.db.exists("User", "jane@example.org")
frappe.db.exists({"doctype": "User", "full_name": "Jane Doe"})
frappe.db.exists("User", {"full_name": "Jane Doe"})
```

## frappe.db.count

```python
frappe.db.count('Task')
frappe.db.count('Task', {'status': 'Open'})
```

## frappe.db.delete

```python
frappe.db.delete("Route History", {
    "modified": ("<=", last_record_to_keep[0].modified),
    "user": user
})
```

## frappe.db.truncate

```python
frappe.db.truncate("Error Log")
```

**Warning:** This is a DDL command (TRUNCATE TABLE), cannot be rolled back.

## frappe.db.commit

```python
frappe.db.commit()
```

Commits current transaction. In most cases you don't need this manually.

## frappe.db.savepoint

```python
frappe.db.savepoint("save_point_name")
```

## frappe.db.rollback

```python
frappe.db.rollback()
frappe.db.rollback(save_point="save_point_name")
```

## frappe.db.sql

```python
frappe.db.sql(query, values, as_dict)
```

Execute arbitrary SQL query.

```python
values = {'company': 'My Company'}
data = frappe.db.sql("""
    SELECT acc.account_number, gl.debit, gl.credit
    FROM `tabGL Entry` gl
    LEFT JOIN `tabAccount` acc ON gl.account = acc.name
    WHERE gl.company = %(company)s
""", values=values, as_dict=True)
```

## frappe.db.bulk_update

```python
frappe.db.bulk_update(doctype, doc_updates, chunk_size=100, modified=None, modified_by=None, update_modified=True)
```

Bulk update multiple documents efficiently.

```python
frappe.db.bulk_update(
    "Task",
    {
        "TASK-0001": {"status": "Closed", "description": "Completed"},
        "TASK-0002": {"status": "Open", "description": "Pending"},
    },
    chunk_size=200
)
```

## Transaction Hooks (v15+)

```python
frappe.db.before_commit.add(func)
frappe.db.after_commit.add(func)
frappe.db.before_rollback.add(func)
frappe.db.after_rollback.add(func)
```

## Transaction Model

### Web Requests
- POST/PUT: auto-commit on success
- GET: no auto-commit
- Exceptions: auto-rollback

### Background Jobs
- Auto-commit on success
- Auto-rollback on exception

### Patches
- Auto-commit on success
- Auto-rollback on exception
