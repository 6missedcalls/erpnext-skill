# Frappe Form Scripts Reference

Client-side JavaScript for form customization.

## Syntax

```javascript
frappe.ui.form.on('DocType', {
    event_name(frm) {
        // handle event
    }
})
```

## Form Events

| Event | Description |
|-------|-------------|
| `setup` | Triggered once when form is created |
| `before_load` | Before form is about to load |
| `onload` | When form is loaded |
| `refresh` | When form is loaded and rendered |
| `onload_post_render` | After form is fully rendered |
| `validate` | Before save |
| `before_save` | After validate, before DB write |
| `after_save` | After form is saved |
| `before_submit` | Before submit |
| `on_submit` | After form is submitted |
| `before_cancel` | Before cancel |
| `after_cancel` | After form is cancelled |
| `timeline_refresh` | After timeline is rendered |
| `{fieldname}` | When field value changes |

## Child Table Events

```javascript
frappe.ui.form.on('Child DocType', {
    items_add(frm, cdt, cdn) {
        let row = frappe.get_doc(cdt, cdn);
    },
    items_remove(frm, cdt, cdn) {},
    field_name(frm, cdt, cdn) {
        let row = frappe.get_doc(cdt, cdn);
    }
})
```

| Event | Description |
|-------|-------------|
| `{fieldname}_add` | Row added |
| `{fieldname}_remove` | Row removed |
| `{fieldname}_move` | Row reordered |
| `before_{fieldname}_remove` | Before row removal |
| `form_render` | Row opened as form |

## Form API

### frm.set_value

```javascript
frm.set_value('field', 'value')
frm.set_value({field1: 'value1', field2: 'value2'})

// Returns promise
frm.set_value('field', 'value').then(() => {
    // value is set
})
```

### frm.refresh / frm.reload_doc

```javascript
frm.refresh()      // Refresh with local values
frm.reload_doc()   // Reload from server
```

### frm.save

```javascript
frm.save()           // Save
frm.save('Submit')   // Submit
frm.save('Cancel')   // Cancel
frm.save('Update')   // Update after submit
```

### frm.enable_save / frm.disable_save

```javascript
frm.enable_save()
frm.disable_save()
```

### frm.is_dirty / frm.dirty

```javascript
if (frm.is_dirty()) {
    // Form has unsaved changes
}

frm.dirty()  // Mark form as dirty
```

### frm.is_new

```javascript
if (frm.is_new()) {
    // Form is new (not saved yet)
}
```

### frm.set_intro

```javascript
frm.set_intro('Message', 'blue')  // blue, red, orange, green, yellow
```

### frm.add_custom_button

```javascript
frm.add_custom_button('Button Label', () => {
    // action
})

// With group
frm.add_custom_button('Action', () => {}, 'Group Name')
```

### frm.change_custom_button_type

```javascript
frm.change_custom_button_type('Button Label', null, 'primary')
frm.change_custom_button_type('Action', 'Group', 'danger')
```

### frm.remove_custom_button

```javascript
frm.remove_custom_button('Button Label')
frm.remove_custom_button('Action', 'Group')
```

### frm.clear_custom_buttons

```javascript
frm.clear_custom_buttons()
```

### frm.set_df_property

```javascript
frm.set_df_property('field', 'fieldtype', 'Text')
frm.set_df_property('field', 'options', ['Option 1', 'Option 2'])
frm.set_df_property('field', 'reqd', 1)
frm.set_df_property('field', 'read_only', 1)
frm.set_df_property('field', 'hidden', 1)
```

### frm.toggle_enable

```javascript
frm.toggle_enable(['field1', 'field2'], condition)
```

### frm.toggle_reqd

```javascript
frm.toggle_reqd('field', condition)
```

### frm.toggle_display

```javascript
frm.toggle_display(['field1', 'field2'], condition)
```

### frm.set_query

```javascript
// Basic filter
frm.set_query('customer', () => {
    return {
        filters: {
            disabled: 0
        }
    }
})

// For child table
frm.set_query('item_code', 'items', () => {
    return {
        filters: {
            is_sales_item: 1
        }
    }
})

// Custom query
frm.set_query('field', () => {
    return {
        query: 'myapp.api.custom_query',
        filters: {field1: 'value1'}
    }
})
```

### frm.add_child

```javascript
let row = frm.add_child('items', {
    item_code: 'ITEM-001',
    qty: 1
})
frm.refresh_field('items')
```

### frm.call

```javascript
// Call controller method
frm.call('method_name', {arg1: 'value'}).then(r => {
    if (r.message) {
        // handle response
    }
})
```

### frm.trigger

```javascript
frm.trigger('custom_event')
```

### frm.get_selected

```javascript
let selected = frm.get_selected()
// {items: ["row1", "row2"], taxes: ["row3"]}
```

## frappe.call

```javascript
frappe.call({
    method: 'myapp.api.method',
    args: {arg1: 'value'},
    freeze: true,
    freeze_message: 'Processing...',
    callback(r) {
        if (r.message) {
            // handle response
        }
    }
})
```

## frappe.model.set_value

```javascript
// Set value in child table row
frappe.model.set_value(cdt, cdn, 'field', 'value')
```

## frappe.get_doc

```javascript
// Get child table row
let row = frappe.get_doc(cdt, cdn)
```
