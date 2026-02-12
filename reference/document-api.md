# Frappe Document API Reference

A Document is an instance of a DocType. It is derived from the `frappe.model.Document` class and represents a single record in the database table.

## frappe.get_doc

```python
frappe.get_doc(doctype, name)
```

Returns a Document object of the record identified by doctype and name. If no document is found, a `DoesNotExistError` is raised. If doctype is a Single DocType, name is not required.

```python
# get an existing document
doc = frappe.get_doc('Task', 'TASK00002')
doc.title = 'Test'
doc.save()

# get a single doctype
doc = frappe.get_doc('System Settings')
doc.timezone  # Asia/Kolkata
```

```python
frappe.get_doc(dict)
```

Returns a new Document object in memory which does not exist yet in the database.

```python
# create a new document
doc = frappe.get_doc({
    'doctype': 'Task',
    'title': 'New Task'
})
doc.insert()
```

## frappe.get_last_doc

```python
frappe.get_last_doc(doctype, filters, order_by)
```

Returns the last Document object created under the mentioned doctype.

```python
# get the last Task created
task = frappe.get_last_doc('Task')

# get the last available Cancelled Task
task = frappe.get_last_doc('Task', filters={"status": "Cancelled"})
```

## frappe.get_cached_doc

Similar to `frappe.get_doc` but will look up the document in cache first before hitting the database.

## frappe.new_doc

```python
frappe.new_doc(doctype)
```

Alternative way to create a new Document.

```python
doc = frappe.new_doc('Task')
doc.title = 'New Task 2'
doc.insert()
```

## frappe.delete_doc

```python
frappe.delete_doc(doctype, name)
```

Deletes the record and its children from the database. Also deletes other documents like Communication, Comments, etc linked to it.

## frappe.rename_doc

```python
frappe.rename_doc(doctype, old_name, new_name, merge=False)
```

Rename a document's name (primary key) from old_name to new_name.

## Document Methods

### doc.insert

```python
doc.insert(
    ignore_permissions=True,  # ignore write permissions
    ignore_links=True,        # ignore Link validation
    ignore_if_duplicate=True, # don't insert if duplicate
    ignore_mandatory=True     # insert even if mandatory not set
)
```

### doc.save

```python
doc.save(
    ignore_permissions=True,  # ignore write permissions
    ignore_version=True       # do not create version record
)
```

### doc.delete

```python
doc.delete()
```

### doc.get_doc_before_save

Returns a version of the doc before the changes were made.

```python
old_doc = doc.get_doc_before_save()
if old_doc.price != doc.price:
    # price changed
    pass
```

### doc.has_value_changed

```python
if doc.has_value_changed("price"):
    pass
```

### doc.reload

Gets the latest values from the database and updates the doc state.

### doc.db_set

Set a field value directly in the database.

```python
# updates value in database
doc.db_set('price', 2300)

# with notify (triggers doc.notify_update())
doc.db_set('price', 2300, notify=True)

# with commit
doc.db_set('price', 2300, commit=True)

# without updating modified timestamp
doc.db_set('price', 2300, update_modified=False)
```

### doc.append

Append a new item to a child table.

```python
doc.append("childtable", {
    "child_table_field": "value",
    "child_table_int_field": 0,
})
```

### doc.get_url

Returns Desk URL for this document.

### doc.add_comment

```python
doc.add_comment('Comment', text='Test Comment')
doc.add_comment('Edit', 'Values changed')
```

### doc.run_method

Run method if defined in controller.

```python
doc.run_method('validate')
```

### doc.queue_action

Run a controller method in background.

```python
doc.queue_action('send_emails', emails=email_list, message='Howdy')
```
