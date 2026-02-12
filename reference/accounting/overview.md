# ERPNext Accounting Module

The Accounting module provides comprehensive financial management capabilities.

## Key Features

### Chart of Accounts
- Hierarchical account structure
- Standard chart templates by region
- Custom account creation
- Multiple account types (Asset, Liability, Equity, Income, Expense)

### Invoicing
- **Sales Invoice** - Customer billing with tax calculation
- **Purchase Invoice** - Vendor bills and expense tracking
- **Debit Note** - Sales returns and adjustments
- **Credit Note** - Purchase returns and adjustments

### Payments
- **Payment Entry** - Record payments received/made
- **Bank Reconciliation** - Match transactions with bank statements
- **Payment Request** - Generate payment links
- **Payment Gateway Integration** - Stripe, PayPal, Razorpay

### Multi-Currency
- Multiple currency transactions
- Exchange rate management
- Realized/unrealized gains tracking
- Currency-specific price lists

### Taxation
- Tax templates and rules
- GST/VAT support
- Tax withholding (TDS)
- Regional compliance (India, UAE, Europe)

### Budgeting
- Budget allocation by cost center
- Budget variance tracking
- Monthly/quarterly/annual budgets
- Budget approval workflows

### Reporting
- General Ledger
- Trial Balance
- Profit & Loss Statement
- Balance Sheet
- Cash Flow Statement
- Accounts Receivable/Payable Aging
- Budget vs Actuals

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `Account` | Chart of accounts |
| `Journal Entry` | Manual accounting entries |
| `Sales Invoice` | Customer invoices |
| `Purchase Invoice` | Vendor bills |
| `Payment Entry` | Payment transactions |
| `GL Entry` | General ledger entries |
| `Cost Center` | Cost tracking centers |
| `Budget` | Budget allocations |
| `Tax Rule` | Tax calculation rules |
| `Mode of Payment` | Payment methods |

## API Examples

### Create Sales Invoice
```python
doc = frappe.get_doc({
    "doctype": "Sales Invoice",
    "customer": "CUST-00001",
    "items": [{
        "item_code": "ITEM-001",
        "qty": 10,
        "rate": 100
    }]
})
doc.insert()
doc.submit()
```

### Get Outstanding Invoices
```python
invoices = frappe.get_all("Sales Invoice",
    filters={
        "docstatus": 1,
        "status": ["in", ["Unpaid", "Overdue"]]
    },
    fields=["name", "customer", "grand_total", "outstanding_amount"]
)
```

### Create Payment Entry
```python
from erpnext.accounts.doctype.payment_entry.payment_entry import get_payment_entry

pe = get_payment_entry("Sales Invoice", "SINV-00001")
pe.paid_amount = 1000
pe.received_amount = 1000
pe.insert()
pe.submit()
```

## Workflows

### Invoice to Payment Flow
1. Create Sales Order (optional)
2. Create Sales Invoice
3. Submit Invoice
4. Create Payment Entry
5. Reconcile with Bank Statement

### Period Closing
1. Create Period Closing Voucher
2. Close accounting period
3. Generate closing entries
4. Lock period for edits
