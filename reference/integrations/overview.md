# ERPNext Integrations Module

The Integrations module provides connectivity with external services and platforms.

## Key Features

### Payment Gateways
- Stripe
- PayPal
- Razorpay
- Braintree
- GoCardless

### E-commerce Platforms
- Shopify
- WooCommerce
- Amazon
- Magento

### Communication
- Email (SMTP/IMAP)
- SMS providers
- Slack
- Google Calendar

### Accounting Services
- QuickBooks
- Tally
- Plaid

### Shipping
- Shiprocket
- Various couriers

### Other
- Google Maps
- OAuth 2.0
- LDAP authentication
- Webhooks

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `Payment Gateway Account` | Payment gateway setup |
| `Connected App` | OAuth applications |
| `Webhook` | Outbound webhooks |
| `Social Login Key` | Social auth config |
| `E Commerce Settings` | E-commerce integration |

## API Examples

### Configure Stripe
```python
doc = frappe.get_doc({
    "doctype": "Payment Gateway Account",
    "payment_gateway": "Stripe",
    "currency": "USD",
    "payment_account": "Bank Account - MC"
})
# Set API keys in payment gateway
```

### Create Webhook
```python
doc = frappe.get_doc({
    "doctype": "Webhook",
    "name": "Order Notification",
    "webhook_doctype": "Sales Order",
    "webhook_docevent": "on_submit",
    "request_url": "https://api.example.com/webhook",
    "request_method": "POST"
})
doc.insert()
```

### Configure OAuth
```python
doc = frappe.get_doc({
    "doctype": "Connected App",
    "provider_name": "Google",
    "client_id": frappe.conf.get("google_client_id"),  # from site_config.json
    "client_secret": frappe.conf.get("google_client_secret"),  # from site_config.json
    "authorization_uri": "https://accounts.google.com/o/oauth2/auth",
    "token_uri": "https://oauth2.googleapis.com/token"
})
doc.insert()
```

## Webhook Configuration

Trigger webhooks on document events:

```python
{
    "doctype": "Webhook",
    "webhook_doctype": "Customer",
    "webhook_docevent": "after_insert",
    "request_url": "https://api.example.com/customers",
    "request_method": "POST",
    "webhook_json": "{ \"name\": \"{{ doc.name }}\", \"email\": \"{{ doc.email_id }}\" }"
}
```

## Integration Methods

### REST API
Standard REST endpoints for all DocTypes

### Data Import/Export
- CSV/Excel import
- Data export tools
- Scheduled backups

### Custom API Endpoints
```python
@frappe.whitelist()
def custom_integration_endpoint(data):
    # Process external data
    return {"status": "success"}
```

## E-commerce Sync

### Shopify Integration
- Product sync
- Order import
- Inventory sync
- Customer sync

### WooCommerce
- Bidirectional sync
- Order processing
- Stock updates

## Payment Flow

1. Customer initiates payment
2. Redirect to payment gateway
3. Process payment
4. Callback to ERPNext
5. Update invoice status
6. Record payment entry

## Scheduled Sync

Use hooks for scheduled integration:
```python
# hooks.py
scheduler_events = {
    "hourly": [
        "myapp.integrations.sync_shopify_orders"
    ]
}
```

## Reports

- Payment Gateway Transaction Log
- Integration Sync Status
- Webhook Delivery Status
