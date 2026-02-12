# ERPNext Asset Management Module

The Assets module manages fixed assets throughout their lifecycle.

## Key Features

### Asset Master
- Asset categorization
- Asset location tracking
- Custodian assignment
- Document attachments
- Insurance tracking

### Asset Lifecycle
- Acquisition (purchase/creation)
- Capitalization
- Depreciation
- Maintenance
- Transfer/movement
- Disposal/sale

### Depreciation
- Multiple depreciation methods
- Automatic depreciation posting
- Finance/tax book separation
- Depreciation schedules
- Accumulated depreciation

### Maintenance
- Preventive maintenance
- Maintenance teams
- Service history
- Warranty tracking

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `Asset` | Asset master records |
| `Asset Category` | Asset classifications |
| `Asset Movement` | Location/custodian changes |
| `Asset Depreciation Schedule` | Depreciation entries |
| `Asset Maintenance` | Maintenance records |
| `Asset Repair` | Repair tracking |
| `Asset Value Adjustment` | Value changes |

## API Examples

### Create Asset
```python
doc = frappe.get_doc({
    "doctype": "Asset",
    "asset_name": "Dell Laptop",
    "asset_category": "Electronic Equipment",
    "company": "My Company",
    "purchase_date": "2024-01-15",
    "gross_purchase_amount": 1500,
    "location": "Head Office"
})
doc.insert()
```

### Submit Asset (Capitalize)
```python
doc = frappe.get_doc("Asset", "AST-00001")
doc.submit()  # Capitalizes the asset
```

### Transfer Asset
```python
doc = frappe.get_doc({
    "doctype": "Asset Movement",
    "purpose": "Transfer",
    "assets": [{
        "asset": "AST-00001",
        "source_location": "Head Office",
        "target_location": "Branch Office"
    }]
})
doc.insert()
doc.submit()
```

### Dispose Asset
```python
from erpnext.assets.doctype.asset.asset import make_sales_invoice

si = make_sales_invoice("AST-00001")
si.items[0].rate = 500  # Sale price
si.insert()
si.submit()
```

### Get Depreciation Schedule
```python
schedules = frappe.get_all("Asset Depreciation Schedule",
    filters={"asset": "AST-00001"},
    fields=["schedule_date", "depreciation_amount", "accumulated_depreciation_amount"]
)
```

## Depreciation Methods

| Method | Description |
|--------|-------------|
| Straight Line | Equal depreciation each period |
| Double Declining | Accelerated depreciation |
| Written Down Value | Percentage of book value |
| Manual | Custom schedule |

## Asset Lifecycle

```
Purchase → Capitalize → Depreciate → Maintain → Dispose/Sell
```

## Asset Category Setup

```python
{
    "doctype": "Asset Category",
    "asset_category_name": "Vehicles",
    "finance_books": [{
        "depreciation_method": "Straight Line",
        "total_number_of_depreciations": 60,  # 5 years
        "frequency_of_depreciation": 1  # Monthly
    }]
}
```

## Reports

- Asset Depreciation Ledger
- Asset Movement Register
- Fixed Asset Register
- Asset Category Wise Value
- Maintenance Schedule Report
