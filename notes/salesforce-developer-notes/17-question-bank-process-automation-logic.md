# 17 - Question Bank: Process Automation and Logic

## Q1
A same-record field update should happen before save for best performance. Best tool?

A. After-save Flow  
B. Before-save Record-Triggered Flow  
C. Scheduled Flow  
D. Batch Apex

**Answer:** B

## Q2
A requirement is "reject save if amount is negative." Best first choice:

A. Trigger  
B. Validation Rule  
C. Queueable Apex  
D. Platform Event

**Answer:** B

## Q3
An aggregate parent total is needed from child records in a master-detail relationship. Best solution:

A. Roll-up Summary Field  
B. Trigger only  
C. SOSL  
D. Approval Process

**Answer:** A

## Q4
Which is safest for user input in query criteria?

A. Dynamic SOQL string concatenation  
B. Bind variables in static SOQL  
C. No filtering  
D. Hardcoded object IDs only

**Answer:** B

## Q5
Trigger must process 200 records and related data. What is critical?

A. SOQL in each loop iteration  
B. DML in loop  
C. Bulkification with collections/maps  
D. One trigger per record

**Answer:** C

## Q6
Need partial success when inserting many records. Use:

A. `insert records;`  
B. `Database.insert(records, false)`  
C. `upsert` only  
D. `merge`

**Answer:** B

## Q7
Which is true about sharing keywords?

A. `with sharing` enforces CRUD/FLS automatically  
B. `without sharing` bypasses all security layers  
C. Sharing keywords affect record access context; CRUD/FLS still need explicit handling  
D. `inherited sharing` is invalid Apex

**Answer:** C

## Q8
Which query type is best for full-text search across multiple objects?

A. SOQL  
B. SOSL  
C. AggregateResult  
D. Database.countQuery

**Answer:** B

## Official References
- [Record-Triggered Flow (Trailhead)](https://trailhead.salesforce.com/content/learn/modules/record-triggered-flows)
- [Validation Rules (Trailhead)](https://trailhead.salesforce.com/content/learn/modules/point_click_business_logic/validation_rules)
- [SOQL and SOSL Reference](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/)
- [Apex Triggers](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers.htm)
- [Apex Developer Guide - Database Class](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_methods_system_database.htm)
- [Secure Apex Classes](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_classes_keywords_sharing.htm)
