# ERPNext Point of Sale (POS) Module

The POS module provides retail point-of-sale functionality.

## Key Features

### POS Interface
- Touch-friendly design
- Item search and selection
- Barcode scanning
- Quick item buttons
- Cart management

### Payment Processing
- Multiple payment methods
- Split payments
- Cash management
- Change calculation
- Payment receipts

### Inventory Integration
- Real-time stock checking
- Multi-warehouse support
- Serial/batch selection
- Stock reservation

### Customer Management
- Customer lookup
- New customer creation
- Customer-specific pricing
- Loyalty programs

### Invoice Management
- Immediate invoicing
- Draft invoices
- Returns handling
- Invoice consolidation

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `POS Profile` | POS terminal configuration |
| `POS Opening Entry` | Shift opening |
| `POS Closing Entry` | Shift closing and reconciliation |
| `POS Invoice` | Point of sale invoices |
| `Sales Invoice` | Invoice records (from POS) |

## API Examples

### Create POS Profile
```python
doc = frappe.get_doc({
    "doctype": "POS Profile",
    "name": "Retail Counter 1",
    "company": "My Company",
    "warehouse": "Retail Store",
    "write_off_account": "Write Off - MC",
    "payments": [{
        "mode_of_payment": "Cash",
        "default": 1
    }, {
        "mode_of_payment": "Credit Card"
    }]
})
doc.insert()
```

### Open POS Session
```python
doc = frappe.get_doc({
    "doctype": "POS Opening Entry",
    "pos_profile": "Retail Counter 1",
    "user": frappe.session.user,
    "opening_balance": [{
        "mode_of_payment": "Cash",
        "opening_amount": 500
    }]
})
doc.insert()
doc.submit()
```

### Create POS Invoice
```python
doc = frappe.get_doc({
    "doctype": "POS Invoice",
    "pos_profile": "Retail Counter 1",
    "customer": "Walk-in Customer",
    "items": [{
        "item_code": "ITEM-001",
        "qty": 2,
        "rate": 100
    }],
    "payments": [{
        "mode_of_payment": "Cash",
        "amount": 200
    }]
})
doc.insert()
doc.submit()
```

### Close POS Session
```python
doc = frappe.get_doc({
    "doctype": "POS Closing Entry",
    "pos_profile": "Retail Counter 1",
    "user": frappe.session.user,
    "pos_opening_entry": "POS-OPE-00001",
    "payment_reconciliation": [{
        "mode_of_payment": "Cash",
        "closing_amount": 2500,
        "expected_amount": 2500
    }]
})
doc.insert()
doc.submit()
```

## POS Profile Configuration

- Warehouse for stock
- Write-off account and limit
- Price list
- Customer group
- Payment methods
- Item groups to display
- Customer-specific settings

## Invoice Consolidation

For high-volume retail:
1. Create individual POS Invoices
2. Consolidate at shift close
3. Single Sales Invoice per day
4. Reduce accounting entries

## Offline POS

ERPNext supports offline POS:
- Browser-based caching
- Sync when online
- Local data storage

## Reports

- POS Sales Analytics
- Daily Sales Summary
- Cashier Collection
- Payment Method-wise Summary
- Item-wise Sales
