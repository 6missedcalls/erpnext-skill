# ERPNext REST API Reference

Frappe/ERPNext automatically generates REST APIs for all DocTypes.

## Base URL

```
https://{YOUR_ERPNEXT_INSTANCE}/api
```

## Authentication Methods

### 1. Token-Based Authentication

Generate API credentials through User settings:

1. Navigate to User list → open user record
2. Access "Settings" tab → expand "API Access"
3. Generate API Key and Secret
4. Use header: `Authorization: token api_key:api_secret`

```bash
curl -X GET "https://instance.erpnext.com/api/resource/Customer" \
  -H "Authorization: token your_api_key:your_api_secret"
```

### 2. OAuth2 Bearer Token

```bash
curl -X GET "https://instance.erpnext.com/api/resource/Customer" \
  -H "Authorization: Bearer access_token"
```

**OAuth2 Flow:**
1. `POST /method/frappe.integrations.oauth2.authorize` - Get authorization code
2. `POST /method/frappe.integrations.oauth2.get_token` - Exchange for access token
3. `POST /method/frappe.integrations.oauth2.revoke_token` - Revoke tokens

### 3. Session-Based Authentication

```bash
# Login
curl -X POST "https://instance.erpnext.com/api/method/login" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "usr=username&pwd=password" \
  -c cookies.txt

# Subsequent requests use the cookie
curl -X GET "https://instance.erpnext.com/api/resource/Customer" \
  -b cookies.txt
```

## Document CRUD Operations

### List Documents

```
GET /api/resource/{DocType}
```

**Query Parameters:**
- `fields` - JSON array of fields to return: `["name", "customer_name"]`
- `filters` - Filter conditions: `[["field", "operator", "value"]]`
- `order_by` - Sorting: `"modified desc"`
- `limit_start` - Pagination offset (default: 0)
- `limit_page_length` - Page size (default: 20)
- `expand` - Expand link field values

**Example:**
```bash
curl -X GET "https://instance.erpnext.com/api/resource/Customer?fields=[\"name\",\"customer_name\"]&filters=[[\"territory\",\"=\",\"United States\"]]&limit_page_length=10"
```

### Get Single Document

```
GET /api/resource/{DocType}/{name}
```

**Query Parameters:**
- `expand_links=True` - Expand linked records

```bash
curl -X GET "https://instance.erpnext.com/api/resource/Customer/CUST-00001"
```

### Create Document

```
POST /api/resource/{DocType}
```

```bash
curl -X POST "https://instance.erpnext.com/api/resource/Customer" \
  -H "Content-Type: application/json" \
  -d '{
    "customer_name": "New Customer",
    "customer_type": "Company",
    "territory": "All Territories"
  }'
```

### Update Document

```
PUT /api/resource/{DocType}/{name}
```

Note: Acts like PATCH - only send fields to update.

```bash
curl -X PUT "https://instance.erpnext.com/api/resource/Customer/CUST-00001" \
  -H "Content-Type: application/json" \
  -d '{
    "customer_name": "Updated Name"
  }'
```

### Delete Document

```
DELETE /api/resource/{DocType}/{name}
```

```bash
curl -X DELETE "https://instance.erpnext.com/api/resource/Customer/CUST-00001"
```

## Remote Method Calls

Call whitelisted Python methods:

```
GET/POST /api/method/{dotted.path.to.method}
```

- **GET** - Read-only operations
- **POST** - Database changes (auto-commits)

```bash
# Get logged in user
curl -X GET "https://instance.erpnext.com/api/method/frappe.auth.get_logged_user"

# Run report
curl -X POST "https://instance.erpnext.com/api/method/frappe.desk.query_report.run" \
  -H "Content-Type: application/json" \
  -d '{"report_name": "Accounts Receivable"}'
```

## File Uploads

```
POST /api/method/upload_file
```

```bash
curl -X POST "https://instance.erpnext.com/api/method/upload_file" \
  -H "Authorization: token api_key:api_secret" \
  -F "file=@/path/to/file.pdf" \
  -F "doctype=Customer" \
  -F "docname=CUST-00001"
```

## Response Format

**Success Response:**
```json
{
  "data": { ... }
}
```

**Method Call Success:**
```json
{
  "message": { ... }
}
```

**Error Response:**
```json
{
  "exc_type": "ValidationError",
  "exc": "Traceback...",
  "_server_messages": "[\"Error message\"]"
}
```

## Common DocTypes

| DocType | Description |
|---------|-------------|
| `Customer` | Customer records |
| `Supplier` | Vendor/Supplier records |
| `Item` | Products/Services |
| `Sales Order` | Sales orders |
| `Sales Invoice` | Sales invoices |
| `Purchase Order` | Purchase orders |
| `Purchase Invoice` | Purchase invoices |
| `Stock Entry` | Inventory movements |
| `Journal Entry` | Accounting entries |
| `Employee` | Employee records |
| `Lead` | CRM leads |
| `Opportunity` | CRM opportunities |

## Rate Limiting

ERPNext may implement rate limiting. Best practices:
- Cache responses where possible
- Use webhooks for real-time updates
- Batch operations when possible
- Implement exponential backoff on errors
