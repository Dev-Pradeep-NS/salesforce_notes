# 16 - Question Bank: Developer Fundamentals

Use these as exam-style scenario questions. Try answering before opening the answer block.

## Q1
A company wants to track `Equipment` records linked to `Account`, with custom lifecycle stages and custom approvals. What is the best starting point?

A. Use standard `Asset` object with no customization  
B. Create custom object `Equipment__c` related to `Account`  
C. Use `Case` object and rename labels  
D. Use `Contact` object with custom fields

**Answer:** B  
**Why:** Requirement needs custom lifecycle and process control; custom object is a better fit than forcing unrelated standard objects.

## Q2
A team must model many-to-many between `Candidate` and `Position`. Best option?

A. Lookup on Candidate only  
B. Master-detail on Position only  
C. Junction object with two relationships  
D. Single object with text field list

**Answer:** C  
**Why:** Many-to-many in Salesforce is modeled using a junction object.

## Q3
Which statement is most accurate about multitenancy?

A. Limits are optional in sandboxes  
B. Limits exist to enforce fairness and platform stability  
C. Limits apply only to API calls  
D. Limits can be turned off with permission sets

**Answer:** B

## Q4
A parent must own child visibility, and deleting parent must remove children. Use:

A. Lookup  
B. Master-Detail  
C. External lookup  
D. Text reference

**Answer:** B

## Q5
An admin needs a calculated, read-only field based on other fields. Best choice:

A. Trigger  
B. Roll-up summary  
C. Formula field  
D. Scheduled flow

**Answer:** C

## Q6
Which tool is best for very large data loads?

A. Data Import Wizard  
B. Data Loader / Bulk API  
C. Page Layout editor  
D. Report builder

**Answer:** B

## Q7
An app requirement is fully met by an existing managed package. First recommendation:

A. Build custom from scratch immediately  
B. Evaluate AppExchange package security, licensing, and fit  
C. Use Visualforce only  
D. Ignore package updates

**Answer:** B

## Q8
Which is true about schema changes?

A. Field/API name changes can impact Apex and tests  
B. Schema changes never affect automation  
C. Required fields only affect UI, not DML  
D. Validation rules do not affect test classes

**Answer:** A

## Official References
- [Salesforce Certified Platform Developer I Exam Guide](https://trailhead.salesforce.com/en/credentials/platformdeveloperi/)
- [Platform Basics (Trailhead)](https://trailhead.salesforce.com/content/learn/modules/lex_implementation_basics)
- [Data Modeling (Trailhead)](https://trailhead.salesforce.com/content/learn/modules/data_modeling)
- [Object Relationships (Trailhead)](https://trailhead.salesforce.com/content/learn/modules/data_modeling/object_relationships)
- [Multitenancy (Trailhead)](https://trailhead.salesforce.com/content/learn/modules/starting_force_com/starting_multitenancy)
- [Bulk API 2.0 and Bulk API Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.api_asynch.meta/api_asynch/)
