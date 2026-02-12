# ERPNext Buying/Procurement Module

The Buying module manages the complete procurement lifecycle.

## Key Features

### Supplier Management
- Supplier master records
- Supplier groups
- Supplier scorecards
- Supplier portal access
- Multiple addresses/contacts

### Request for Quotation (RFQ)
- RFQ creation and distribution
- Supplier quotation comparison
- Best price selection
- Quote validity tracking

### Purchase Orders
- PO creation from RFQs
- Blanket orders (rate contracts)
- Subcontracting orders
- Approval workflows

### Goods Receipt
- Purchase receipts
- Quality inspection
- Partial receipts
- Over-receipt handling

### Invoicing & Payments
- Purchase invoice matching
- Landed cost allocation
- Debit notes
- Supplier payments

### Analytics
- Purchase history
- Supplier performance
- Category-wise spending
- Procurement tracking

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `Supplier` | Vendor master |
| `Material Request` | Internal requisitions |
| `Request for Quotation` | Supplier RFQs |
| `Supplier Quotation` | Supplier quotes |
| `Purchase Order` | Purchase orders |
| `Purchase Receipt` | Goods received |
| `Purchase Invoice` | Vendor invoices |
| `Buying Settings` | Module configuration |

## API Examples

### Create Material Request
```python
doc = frappe.get_doc({
    "doctype": "Material Request",
    "material_request_type": "Purchase",
    "items": [{
        "item_code": "ITEM-001",
        "qty": 100,
        "warehouse": "Stores"
    }]
})
doc.insert()
doc.submit()
```

### Create Purchase Order
```python
doc = frappe.get_doc({
    "doctype": "Purchase Order",
    "supplier": "SUP-00001",
    "items": [{
        "item_code": "ITEM-001",
        "qty": 100,
        "rate": 10,
        "warehouse": "Stores"
    }]
})
doc.insert()
doc.submit()
```

### Receive Goods
```python
from erpnext.buying.doctype.purchase_order.purchase_order import make_purchase_receipt

pr = make_purchase_receipt("PO-00001")
pr.insert()
pr.submit()
```

## Procurement Workflow

```
Material Request → RFQ → Supplier Quotation → Purchase Order → Purchase Receipt → Purchase Invoice → Payment
```

## Supplier Scorecard

Track supplier performance:
- Quality rating
- Delivery performance
- Price competitiveness
- Response time
- Issue resolution

## Reports

- Purchase Order Analysis
- Supplier-wise Sales Analytics
- Requested Items To Be Ordered
- Purchase Register
- Items To Be Requested
- Procurement Tracker
