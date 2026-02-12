# ERPNext HR & Payroll Module

The HR module provides comprehensive human resource management including payroll processing.

## Key Features

### Recruitment
- Job openings and postings
- Applicant tracking
- Interview scheduling
- Offer letter generation
- Onboarding checklists

### Employee Management
- Employee master records
- Organization hierarchy
- Department/designation management
- Employee lifecycle (promotion, transfer, separation)
- Document management

### Attendance
- Check-in/check-out tracking
- Shift management
- Overtime calculation
- Attendance regularization
- Biometric integration

### Leave Management
- Leave types and policies
- Leave allocation
- Leave applications
- Compensatory off
- Holiday lists

### Payroll
- Salary structures
- Salary components (earnings/deductions)
- Payroll processing
- Statutory compliance
- Tax calculations
- Loan management

### Performance
- Appraisal cycles
- Goal setting (KRAs)
- Feedback collection
- Performance analytics

### Training
- Training programs
- Training events
- Feedback and certification
- Skill mapping

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `Employee` | Employee master |
| `Attendance` | Daily attendance |
| `Leave Application` | Leave requests |
| `Salary Structure` | Pay structure templates |
| `Salary Slip` | Monthly pay slips |
| `Payroll Entry` | Bulk payroll processing |
| `Job Applicant` | Recruitment candidates |
| `Appraisal` | Performance reviews |
| `Training Event` | Training sessions |
| `Shift Type` | Shift definitions |

## API Examples

### Create Employee
```python
doc = frappe.get_doc({
    "doctype": "Employee",
    "first_name": "John",
    "last_name": "Doe",
    "gender": "Male",
    "date_of_birth": "1990-01-15",
    "date_of_joining": "2024-01-01",
    "department": "Engineering",
    "designation": "Software Developer",
    "company": "My Company"
})
doc.insert()
```

### Process Payroll
```python
# Create Payroll Entry
pe = frappe.get_doc({
    "doctype": "Payroll Entry",
    "company": "My Company",
    "payroll_frequency": "Monthly",
    "start_date": "2024-01-01",
    "end_date": "2024-01-31",
    "payment_account": "Cash - MC"
})
pe.insert()

# Fill employees
pe.fill_employee_details()

# Create salary slips
pe.create_salary_slips()

# Submit salary slips
pe.submit_salary_slips()
```

### Apply Leave
```python
doc = frappe.get_doc({
    "doctype": "Leave Application",
    "employee": "HR-EMP-00001",
    "leave_type": "Casual Leave",
    "from_date": "2024-02-15",
    "to_date": "2024-02-16",
    "reason": "Personal work"
})
doc.insert()
doc.submit()
```

### Get Leave Balance
```python
from erpnext.hr.doctype.leave_application.leave_application import get_leave_balance_on

balance = get_leave_balance_on(
    employee="HR-EMP-00001",
    leave_type="Casual Leave",
    date="2024-02-01"
)
```

### Mark Attendance
```python
doc = frappe.get_doc({
    "doctype": "Attendance",
    "employee": "HR-EMP-00001",
    "attendance_date": "2024-02-05",
    "status": "Present"
})
doc.insert()
doc.submit()
```

## Salary Structure

A salary structure consists of:
- **Earnings** - Basic, HRA, allowances, bonuses
- **Deductions** - PF, tax, insurance, loans

```python
# Salary Component (formula-based)
{
    "salary_component": "HRA",
    "amount_based_on_formula": 1,
    "formula": "base * 0.40"  # 40% of base
}
```

## Payroll Workflow

1. **Setup** - Define salary components and structures
2. **Assignment** - Assign structures to employees
3. **Payroll Entry** - Create monthly payroll entry
4. **Review** - Verify salary slips
5. **Submit** - Submit slips and create payment entries
6. **Bank** - Generate bank upload file

## Statutory Compliance

Regional compliance support:
- **India** - EPF, ESI, PT, TDS (Form 16)
- **UAE** - WPS files
- **South Africa** - UIF, SDL
- **Other** - Configurable tax slabs

## Reports

- Monthly Attendance Sheet
- Leave Balance Summary
- Salary Register
- Bank Remittance
- Provident Fund Report
- Income Tax Computation
- Employee Analytics
