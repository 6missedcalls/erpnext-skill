# Frappe REST API Reference

Frappe auto-generates REST APIs for all DocTypes.

## Authentication

### Token Based

```python
# Generate API keys in User > Settings > API Access
headers = {
    "Authorization": "token api_key:api_secret"
}
```

### OAuth

```python
headers = {
    "Authorization": "Bearer access_token"
}
```

### Password Based

```javascript
fetch('/api/method/login', {
    method: 'POST',
    headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json',
    },
    body: JSON.stringify({
        usr: 'username',
        pwd: 'password'
    })
})
```

## CRUD Operations

### Create

```
POST /api/resource/:doctype

Body: {"field1": "value1", "field2": "value2"}
```

### Read

```
GET /api/resource/:doctype/:name
GET /api/resource/:doctype/:name?expand_links=True
```

### Update

```
PUT /api/resource/:doctype/:name

Body: {"field1": "new_value"}
```

### Delete

```
DELETE /api/resource/:doctype/:name
```

## Listing Documents

```
GET /api/resource/:doctype
```

### Fields

```
GET /api/resource/:doctype?fields=["field1", "field2"]
```

### Filters

```
GET /api/resource/:doctype?filters=[["field", "operator", "value"]]
```

**Operators:**
- `=`, `!=`, `<`, `>`, `<=`, `>=`
- `like`, `not like`
- `in`, `not in`
- `between`
- `is`, `is not` (for null)

### OR Filters

```
GET /api/resource/:doctype?or_filters=[["status", "=", "Open"], ["status", "=", "Pending"]]
```

### Sorting

```
GET /api/resource/:doctype?order_by=title%20desc
```

### Pagination

```
GET /api/resource/:doctype?limit_start=0&limit_page_length=20
```

### Expand Links

```
GET /api/resource/:doctype?expand=["priority"]
```

### Debug

```
GET /api/resource/:doctype?debug=True
```

Returns executed SQL query in response.

## Remote Method Calls

```
GET/POST /api/method/dotted.path.to.method
```

```python
@frappe.whitelist()
def my_method(arg1, arg2):
    return {"result": arg1 + arg2}
```

```
POST /api/method/myapp.api.my_method
Body: {"arg1": "value1", "arg2": "value2"}
```

## File Uploads

```bash
curl -X POST \
    http://site/api/method/upload_file \
    -H 'Authorization: token xxx:yyy' \
    -F file=@/path/to/file.png
```

## Response Format

### Success

```json
{
    "data": {...}
}
```

Or for methods:

```json
{
    "message": "return_value"
}
```

### Error

```json
{
    "exc": "stack_trace",
    "exc_type": "ExceptionClass",
    "_server_messages": "[\"Error message\"]"
}
```

## Headers

Always include:

```
Accept: application/json
Content-Type: application/json
```
