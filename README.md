# budget-app
02/11  

Prompt

Budgets - A budget is a line item of spend for an employee. Every employee can request for budget for themselves or for an employee within their org if they have reporting structure per the organization hierarchy. For every budget line we will assign a unique identifier. All budgets are associated with an AOP. The budget can be active or inactive should the user want to remove a budget. The total amounts across active budgets in an AOP cannot exceed the total amount of all active budgets. For every budget line entry, the user will provide a project, a short description, the amount and the ldap of the employee responsible for budget. If the employee is not a manager, then the default is themselves and not be able to set budget for others. If the user is a manager, they should able to set budget to one of their employee within their organization. A users cannot set budget for employees who are not within their organization. For every budget line, we will also have purchase requisition amount, purchase order amount and receipt amount. These amounts are typically referred to as actuals. The actuals cannot exceed the budget. We will have APIs to update actuals. The actuals update will be incremental meaning, we will get a purchase request to add an amount of say $30. All amounts are in us dollars. Here is the information we will collect for actuals:
Purchase requests: purchase requisition reference, requestor ldap, budget identifier (should be valid against the budgets we created), requisition amount, date
Purchase orders: purchase order number, purchase order line number, requestor ldap, budget identifier (should be valid against the budgets we created), purchase item, amount, date
Receipts will only have receipt date, purchase order number, purchase order line number, purchase item and will be matched to ensure that receipts are made against purchase orders
We will have CRUD APIs for budgets, purchase requests, purchase orders and receipts. When any of the actuals are update, we will summarize the totals for that budget id and update the budget. When updating budget, if any of the amounts exceed budget, we will raise and error and fail the API or show the user an error if they are using the front end

Budget management features: Here are all the capabilities that I would the user to be able to do:
Copy budget: User should be able to specify a budget identifier and the target AOP to copy a budget to. When copying the budget, a new budget identifier should be generated in the destination AOP. If the destination AOP is an active AOP, it is important to ensure that the total budgets within AOP cannot exceed the amount on AOP
Remove budget: The database should never lose a budget. Removed of budget is a flag in database
Reconcile budget: At any time, the user should be able to run a reconciliation process where the amount on AOP is compared with the totals on active budgets and ensure that the budget for the AOP comply
Reduce budget: If the user wants to reduce the amount in a budget, it cannot be reduce below the amounts of active actuals

The user experience layer will be based on forms. I will have the following screens:
Users: We should be able to add a new users, remove a user and modify a users. Ldap is the key and should be non-modifiable.
Organization: We should be able to add a manager-subordinate relationship
Cost Centers: Need a form to add, remove cost centers. Removal of a cost center is managed by marking delete flag. No removal is permitted if there is an active budget record for that cost center
AOP: Ability to create a new AOP, reset status for AOP
Budgets: These as same as AOP detail. User should able to add a budget line item to an AOP. They should also be able to remove or modify AOP and the rules defined above should apply. Finally, there should be the ability to execute to each of the budget management feature referred above using this form
Purchase requests and Purchase orders: There should be a CRUD form for each of these transactions

Authentication: There is no active sign-on for this application. If the user enters “IKnowYou241202”, the system should know that they are a valid user and allow for subsequent operations. If the user does not enter this code, then the error should be displayed to state the they are not an authenticated user.

Reporting: The user should be able to report data fairly easily per the following:
Show me my organization: Show the organization hierarchy for the user. Note that if a person A reports to B and B reports to C, when the user C says show me my organization, the report should show the hierarchy of A to B and B to C. If C has D and E reporting to them, that should also be shown
Show me my budget: Show the budgets totals for the user. This should only include the total budgets across C, D and E per the above example. If D and E have additional reports, then those should be automatically included as well

The application will be a 3 tier application on GCP. The total number of users is only 10. We expect to manage about 1000 budget lines in total. The cost across all GCP services should not exceed $50.
 The code repository should be github. Please include setup instructions for github, GCP and provide CI/CD instruction for object creation. 
---------------------------
Instructions

Front end - react

Back end - Python

DB - MYSql

Infra - on GCP

Give me step by step instruction for following to start with. Keep the folder structure simple and do not create many files meaning if backend logic can done in one file that would be great. Same with frontend

a. Folder structure in github
b. Code and instructions to for all the database tables.
c. Backend and frontend logic only for the Employees and organization

Make the code extensible for other features. Like UI should be built with all these features in mind but only for Employees and organization for now
