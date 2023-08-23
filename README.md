# Excel-Churn-Calculation
The workbook shows calculation of churn rate from company's Financial records. It uses remittances and invoices to estimate the accuracy of reported churn rate by the Sales and Service Delivery teams. This was necessary because
even though some B2B clients have not yet churned on paper, the accounts have little to no inflows for services due to various reasons like credit notes issued for not meeeting SLAs or making statement on dissatisfied service.
NOTE: Customer details are masked on this workbook to protect company's sensitive information.

*STEP 1: CONSOLIDATING YEARLY RECORDS*
* Customer data on names, date, transaction type, debit amount (invoices) and credit amount (remittances) were extracted from financial syatem.
* Performed data cleansing and transformation on the date field to ensure compatabilty with expected date format in Excel by performing text to columns action on the 'text, data type
* Concatenated split columns with:
  > =IFERROR(DATEVALUE(CONCATENATE(C4,"-",D4,"-",E4)), "")
* Added new 'Month' column by transforming numeric month data for readability with:
  > =TEXT(C2*28, "mmmm")
* Inserted data into pivot table


*STEP 2: DEVELOPING CHURN LOGIC*
* Count of transaction type was done for the required years, giving the number of invoices sent and remittance received per customer per year.
* Difference between invoice and remittance for each customer is calculated, negative numbers indicates higher invoices/requests for payments been sent out by the company (An indication of dissatisfied customer). Sparklines were also addes for the difference as a visual aid
* Active/inactive status on the accounts were determined using values in count of transaction cells, where blank cells shows no activity on the account
  > =IF(ISBLANK(G271),"No Activity","Active")
* Based on account activity, 0, 1 and 'empty cell' values are assigned for each customer with NESTED IF functions
* For the base year, NESTED IF checks account activity with this formula
  > =IF(OR(Y129="No Activity",Z129="No Activity"),"",IF(OR(Y129="Active",Z129="Active"),0,1))
  > 
  > Output: If both remittance and invoice records shows activity, a "0" value is assigned to denote an active customer, otherwise a value of "1" is assigned for churned customer
* For subsequent years, NESTED IF checks account activity with this formula
  > =IF(
  > 
  > AND(AI268=0,  OR(AC268="No Activity", AD268="No Activity") ), 1,
    * CHECKS assigned value for previous year=0 OR 'No activity' in invoices & remittances. If True, assign=1 ELSE
  >
  > IF(
  >
  > OR(
  >
  > AND(AA268="No Activity",AB268="No Activity",AC268="No Activity",AD268="No Activity"),
    * CHECKS Inactivity for invoice & remittance in previous and current year = TRUE OR
  > 
  > AND(AC268="No Activity", AD268="Active"),
    * CHECKS in current year for Inactive remittance and Active invoice status = TRUE OR 
  > 
  > AND(AA268="No Activity", AB268="Active")),
    * CHECKS in previous year for Inactive remittance and Active invoice status = TRUE
  >
  > "",
   * CHECKS on the condition that all NESTED AND functions are TRUE assign=""  i.e. blank cell to denote non-existent customer
  >
  > 0))
    * CHECKS on the condition that NESTED OR function is FALSE assign=0
  
*STEP 3: ESIMATING CHURN*
  * COUNT "Total Customers" i.e. "0"s and "1"s
  * COUNT "Active Customers" i.e. "0"s
  * COUNT "Churned Customers" i.e. "1"s
  * Compute churned as a % of Churned/Total

  
  
