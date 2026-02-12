# ERPNext Stock/Inventory Module

The Stock module provides comprehensive inventory management capabilities.

## Key Features

### Warehouse Management
- Multi-warehouse support
- Warehouse hierarchy (tree structure)
- Warehouse-wise stock balance
- Inter-warehouse transfers

### Item Management
- Item master with variants
- Item groups and categories
- Multiple units of measure (UOM)
- Serial number tracking
- Batch tracking with expiry
- Quality inspection

### Stock Transactions
- **Stock Entry** - Material receipt, issue, transfer
- **Purchase Receipt** - Goods received from suppliers
- **Delivery Note** - Goods delivered to customers
- **Stock Reconciliation** - Inventory adjustments

### Valuation Methods
- FIFO (First In, First Out)
- Moving Average
- LIFO (Last In, First Out)
- Perpetual vs Periodic inventory

### Auto-Reorder
- Reorder levels by warehouse
- Safety stock configuration
- Auto-generate Material Requests
- Lead time tracking

### Quality Control
- Quality Inspection templates
- Inspection on receipt/delivery
- Accept/Reject workflows
- Quality parameters tracking

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `Item` | Product/service master |
| `Warehouse` | Storage locations |
| `Stock Entry` | Inventory movements |
| `Purchase Receipt` | Goods received |
| `Delivery Note` | Goods delivered |
| `Stock Ledger Entry` | Stock transaction log |
| `Bin` | Stock levels per item/warehouse |
| `Serial No` | Serial number tracking |
| `Batch` | Batch/lot tracking |
| `Quality Inspection` | QC records |

## API Examples

### Get Stock Balance
```python
from erpnext.stock.utils import get_stock_balance

qty = get_stock_balance("ITEM-001", "Main Warehouse")
```

### Create Stock Entry (Material Transfer)
```python
doc = frappe.get_doc({
    "doctype": "Stock Entry",
    "stock_entry_type": "Material Transfer",
    "items": [{
        "item_code": "ITEM-001",
        "qty": 10,
        "s_warehouse": "Stores",
        "t_warehouse": "Production"
    }]
})
doc.insert()
doc.submit()
```

### Get Stock Ledger Entries
```python
entries = frappe.get_all("Stock Ledger Entry",
    filters={
        "item_code": "ITEM-001",
        "warehouse": "Main Warehouse"
    },
    fields=["posting_date", "qty_after_transaction", "valuation_rate"],
    order_by="posting_date desc"
)
```

### Check Stock Availability
```python
from erpnext.stock.stock_balance import get_balance_qty_from_sle

qty = get_balance_qty_from_sle("ITEM-001", "Main Warehouse")
```

## Stock Entry Types

| Type | From Warehouse | To Warehouse | Use Case |
|------|----------------|--------------|----------|
| Material Receipt | - | Yes | Initial stock, adjustments |
| Material Issue | Yes | - | Consumption, write-offs |
| Material Transfer | Yes | Yes | Inter-warehouse moves |
| Manufacture | Yes | Yes | Production consumption |
| Repack | Yes | Yes | Bundle/unbundle items |
| Send to Subcontractor | Yes | - | Subcontracting |

## Inventory Dimensions

ERPNext supports unlimited inventory dimensions:
- Warehouse (default)
- Batch
- Serial No
- Custom dimensions (e.g., Rack, Shelf, Bin)

## Reports

- Stock Balance
- Stock Ledger
- Stock Projected Qty
- Stock Ageing
- Item-wise Price List Rate
- Ordered Items To Be Delivered
- Stock Analytics
