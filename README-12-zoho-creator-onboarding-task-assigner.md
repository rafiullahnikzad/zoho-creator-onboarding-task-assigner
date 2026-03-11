# zoho-creator-onboarding-task-assigner

![Zoho Creator](https://img.shields.io/badge/Zoho-Creator-blue) ![Deluge](https://img.shields.io/badge/Language-Deluge-orange) ![Topic](https://img.shields.io/badge/Topic-HR%20Onboarding-green)

## Business Problem

Employee onboarding relies on manual email checklists. Tasks are forgotten or duplicated across IT, HR, Finance, and management with no central visibility on completion.

## Solution

Define a task template list. When a new hire is added, loop through all templates, create individual Onboarding_Task records, set due dates from start date, and notify each department.

## Use Case

Any company onboarding new employees who wants to automate task creation across departments and track all onboarding items in one Zoho Creator dashboard.

## Trigger

**On Submit — New_Employee Form**

## Fields Required

| Field Link Name | Type | Notes |
|---|---|---|
| employee_name | Single Line | |
| employee_email | Email | |
| job_title | Single Line | |
| start_date | Date | Used to calculate due dates |
| reporting_manager_email | Email | Manager tasks assigned here |

## Setup

1. Create Onboarding_Tasks form with: task_name, employee_name, assigned_department, assigned_to_email, due_date, task_status.
2. Update department emails in the deptEmails map.
3. Add or remove tasks in taskTemplates to match your onboarding process.

## Deluge Script

```javascript
// ============================================================
// SCRIPT: Employee Onboarding Task Auto-Assigner
// TRIGGER: On Submit - New_Employee Form
// AUTHOR: Rafiullah Nikzad | rafiullahnikzad.netlify.app
// ============================================================

empName = input.employee_name;
startDate = input.start_date;
jobTitle = input.job_title;

taskTemplates = list();
taskTemplates.add({"task":"Setup laptop and email account","dept":"IT","days":1});
taskTemplates.add({"task":"Create ID card and access badge","dept":"HR","days":1});
taskTemplates.add({"task":"Add employee to payroll system","dept":"Finance","days":2});
taskTemplates.add({"task":"Conduct HR policy briefing","dept":"HR","days":3});
taskTemplates.add({"task":"Provide software tool access","dept":"IT","days":3});
taskTemplates.add({"task":"Schedule welcome meeting","dept":"Manager","days":1});
taskTemplates.add({"task":"Assign 30-day onboarding plan","dept":"Manager","days":5});

deptEmails = map();
deptEmails.put("IT", "it@yourcompany.com");
deptEmails.put("HR", "hr@yourcompany.com");
deptEmails.put("Finance", "finance@yourcompany.com");
deptEmails.put("Manager", input.reporting_manager_email);

for each taskItem in taskTemplates
{
    taskDept = taskItem.get("dept");
    dueDate = startDate.addDay(taskItem.get("days"));
    assigneeEmail = deptEmails.get(taskDept);

    add row to Onboarding_Tasks
    [
        task_name = taskItem.get("task")
        employee_name = empName
        assigned_department = taskDept
        assigned_to_email = assigneeEmail
        due_date = dueDate
        task_status = "Pending"
    ]

    sendmail
    [
        from: zoho.adminuserid
        to: assigneeEmail
        subject: "Onboarding Task for " + empName + ": " + taskItem.get("task")
        message: empName + " starts on " + startDate + ".\nTask: " + taskItem.get("task") + "\nDue: " + dueDate
    ]
}
```

## Notes

- Extend taskTemplates with role-specific tasks using if(jobTitle == ...) blocks.
- Build a dashboard report filtered by assigned_to_email for each department.
- Add completion percentage rollup field on the employee record.

---
**Author:** Rafiullah Nikzad — Senior Zoho Developer
**Portfolio:** rafiullahnikzad.netlify.app | **Community:** Zoho Afghanistan on LinkedIn