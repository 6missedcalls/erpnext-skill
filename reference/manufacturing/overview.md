# ERPNext Manufacturing Module

The Manufacturing module provides production planning and execution capabilities.

## Key Features

### Bill of Materials (BOM)
- Multi-level BOMs (sub-assemblies)
- Multiple BOM versions
- BOM costing (material + operation)
- By-products and scrap items
- Process loss tracking
- BOM comparison

### Production Planning
- Production Plan from Sales Orders
- Make-to-Order vs Make-to-Stock
- Material requirement planning (MRP)
- Capacity planning
- Demand forecasting (exponential smoothing)

### Work Orders
- Production scheduling
- Operation-wise tracking
- Material consumption
- Finished goods receipt
- Gantt chart visualization

### Job Cards
- Operation-level tracking
- Time logging
- Employee assignment
- Quality inspection
- Workstation management

### Workstations
- Capacity configuration
- Operating costs (hourly rates)
- Working hours setup
- Downtime tracking

### Subcontracting
- Send raw materials to subcontractor
- Track subcontracted items
- Receive finished goods

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `BOM` | Bill of Materials |
| `Work Order` | Production orders |
| `Job Card` | Operation tracking |
| `Production Plan` | Production planning |
| `Workstation` | Machine/station config |
| `Operation` | Manufacturing operations |
| `Routing` | Operation sequences |
| `BOM Update Tool` | Bulk BOM updates |

## API Examples

### Create BOM
```python
doc = frappe.get_doc({
    "doctype": "BOM",
    "item": "FG-001",
    "quantity": 1,
    "items": [
        {"item_code": "RM-001", "qty": 2},
        {"item_code": "RM-002", "qty": 1}
    ],
    "operations": [
        {
            "operation": "Assembly",
            "workstation": "WS-001",
            "time_in_mins": 30
        }
    ]
})
doc.insert()
doc.submit()
```

### Create Work Order
```python
from erpnext.manufacturing.doctype.work_order.work_order import make_work_order

wo = frappe.get_doc({
    "doctype": "Work Order",
    "production_item": "FG-001",
    "bom_no": "BOM-001",
    "qty": 100,
    "fg_warehouse": "Finished Goods",
    "wip_warehouse": "Work In Progress"
})
wo.insert()
wo.submit()
```

### Start Production
```python
# Create Stock Entry for Material Transfer
from erpnext.manufacturing.doctype.work_order.work_order import make_stock_entry

se = make_stock_entry(wo.name, "Material Transfer for Manufacture", qty=100)
se.insert()
se.submit()
```

### Complete Production
```python
# Create Stock Entry for Manufacture
se = make_stock_entry(wo.name, "Manufacture", qty=100)
se.insert()
se.submit()
```

### Get Production Status
```python
wo = frappe.get_doc("Work Order", "WO-00001")
print(f"Produced: {wo.produced_qty}/{wo.qty}")
print(f"Status: {wo.status}")
```

## Production Workflow

1. **Plan** - Create Production Plan from demand
2. **Material Request** - Generate MRs for raw materials
3. **Work Order** - Create work orders
4. **Material Issue** - Transfer materials to WIP
5. **Job Cards** - Track operations
6. **Manufacture** - Complete and receive finished goods

## BOM Types

| Type | Description |
|------|-------------|
| Standard | Regular production BOM |
| Template | Base for product variants |
| Subcontract | Subcontracted manufacturing |

## Costing

BOM costs include:
- Raw material cost (based on valuation)
- Operation cost (workstation hourly rate × time)
- Scrap and process loss
- Overhead allocation

## Plant Floor App

ERPNext v15 includes Plant Floor mobile app:
- Real-time job card updates
- Barcode scanning
- Quality inspection entry
- Downtime recording
