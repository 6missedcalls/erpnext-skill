# ERPNext Projects Module

The Projects module provides project management and time tracking capabilities.

## Key Features

### Project Management
- Project creation and tracking
- Project templates
- Budget tracking
- Milestone management
- Client portal access

### Task Management
- Task creation and assignment
- Task dependencies
- Progress tracking
- Multiple views (List, Gantt, Kanban, Calendar)
- Subtasks

### Time Tracking
- Timesheet logging
- Activity types
- Billable vs non-billable hours
- Project costing

### Resource Management
- Employee allocation
- Workload balancing
- Availability tracking

### Billing
- Project-based invoicing
- Time-based billing
- Fixed-price projects
- Expense claims

## Key DocTypes

| DocType | Description |
|---------|-------------|
| `Project` | Project master |
| `Task` | Project tasks |
| `Timesheet` | Time entries |
| `Activity Type` | Work categories |
| `Project Template` | Reusable templates |
| `Project Type` | Project categories |

## API Examples

### Create Project
```python
doc = frappe.get_doc({
    "doctype": "Project",
    "project_name": "Website Redesign",
    "expected_start_date": "2024-02-01",
    "expected_end_date": "2024-04-30",
    "company": "My Company",
    "customer": "CUST-00001"
})
doc.insert()
```

### Create Task
```python
doc = frappe.get_doc({
    "doctype": "Task",
    "subject": "Design Mockups",
    "project": "PROJ-00001",
    "exp_start_date": "2024-02-01",
    "exp_end_date": "2024-02-15",
    "assigned_to": "user@example.com"
})
doc.insert()
```

### Log Time
```python
doc = frappe.get_doc({
    "doctype": "Timesheet",
    "employee": "HR-EMP-00001",
    "time_logs": [{
        "activity_type": "Development",
        "project": "PROJ-00001",
        "task": "TASK-00001",
        "from_time": "2024-02-05 09:00:00",
        "to_time": "2024-02-05 17:00:00",
        "hours": 8,
        "billable": 1
    }]
})
doc.insert()
doc.submit()
```

### Get Project Status
```python
project = frappe.get_doc("Project", "PROJ-00001")
print(f"Progress: {project.percent_complete}%")
print(f"Total Billed: {project.total_billed_amount}")
print(f"Total Costing: {project.total_costing_amount}")
```

## Task Dependencies

Create task chains:
```python
task1 = frappe.get_doc({"doctype": "Task", "subject": "Design"})
task1.insert()

task2 = frappe.get_doc({
    "doctype": "Task",
    "subject": "Development",
    "depends_on": [{"task": task1.name}]
})
task2.insert()
```

## Project Billing

### Time & Material
- Bill based on timesheet hours
- Apply activity-specific rates
- Auto-generate invoices

### Fixed Price
- Define project budget
- Track against milestones
- Invoice on completion

## Reports

- Project Summary
- Task Summary
- Project Profitability
- Employee Hours Utilization
- Timesheet Billing Summary
- Project Wise Stock Tracking
- Delayed Tasks
