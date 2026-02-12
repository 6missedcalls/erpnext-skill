# ERPNext Selling/Sales Module

The Selling module manages the complete sales cycle.

## Key Features

### Customer Management
- Customer master records
- Customer groups
- Credit limits
- Customer portal access
- Multiple addresses/contacts

### Quotations
- Quote generation
- Multiple currency support
- Pricing rules and discounts
- Quote templates
- PDF generation

### Sales Orders
- Order management
- Delivery scheduling
- Blanket orders
- Partial delivery tracking

### Delivery
- Delivery notes
- Packing slips
- Shipping integration
- Partial deliveries

### Invoicing
- Sales invoices
- Credit notes
- Recurring invoices
- E-invoicing support

### Pricing
- Multiple price lists
- Customer-specific pricing
- Promotional schemes
- Discount rules

### Commissions
- Sales person hierarchy
- Commission calculation
- Partner management
- Target tracking

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `Customer` | Customer master |
| `Quotation` | Sales quotes |
| `Sales Order` | Customer orders |
| `Delivery Note` | Shipment records |
| `Sales Invoice` | Customer invoices |
| `Price List` | Pricing tiers |
| `Pricing Rule` | Discount rules |
| `Sales Person` | Sales team |

## API Examples

### Create Quotation
```python
doc = frappe.get_doc({
    "doctype": "Quotation",
    "quotation_to": "Customer",
    "party_name": "CUST-00001",
    "items": [{
        "item_code": "ITEM-001",
        "qty": 10,
        "rate": 100
    }]
})
doc.insert()
```

### Convert Quote to Order
```python
from erpnext.selling.doctype.quotation.quotation import make_sales_order

so = make_sales_order("QTN-00001")
so.insert()
so.submit()
```

### Create Delivery Note
```python
from erpnext.selling.doctype.sales_order.sales_order import make_delivery_note

dn = make_delivery_note("SO-00001")
dn.insert()
dn.submit()
```

### Create Sales Invoice
```python
from erpnext.selling.doctype.delivery_note.delivery_note import make_sales_invoice

si = make_sales_invoice("DN-00001")
si.insert()
si.submit()
```

## Sales Workflow

```
Quotation → Sales Order → Delivery Note → Sales Invoice → Payment
```

## Pricing Rules

Configure automatic discounts:
- Quantity-based discounts
- Customer group discounts
- Time-bound promotions
- Bundle pricing
- Buy X Get Y free

## Reports

- Sales Analytics
- Customer Acquisition
- Quotation Trends
- Sales Order Analysis
- Delivery Note Trends
- Gross Profit Analysis
- Sales Person Commission
- Territory-wise Sales
