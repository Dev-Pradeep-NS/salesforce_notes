# 20 - Full-Length Mixed Mock (PD1 Style)

Time suggestion: 45-60 minutes for this set.

## Q1 (Developer Fundamentals)
Best model for many-to-many relation between `Student` and `Course`?

A. One lookup on Student  
B. One lookup on Course  
C. Junction object  
D. Formula field

**Answer:** C

## Q2 (Process Automation)
Same-record update should be most performant and low-latency. Pick best:

A. Before-save flow  
B. After-save flow  
C. Scheduled flow  
D. Batch Apex

**Answer:** A

## Q3 (Automation Logic)
Need parent count of detail records in master-detail relation. Best:

A. Trigger  
B. Roll-up summary  
C. Queueable  
D. SOSL

**Answer:** B

## Q4 (Apex Security)
`with sharing` ensures:

A. CRUD/FLS enforcement automatically  
B. Record-sharing enforcement context  
C. No security checks needed  
D. No query limits

**Answer:** B

## Q5 (SOQL/SOSL)
Need text search across `Account` and `Contact` using keyword. Use:

A. SOQL  
B. SOSL  
C. CountQuery  
D. Upsert

**Answer:** B

## Q6 (UI)
Child component sends selection event to parent in LWC using:

A. Custom Event dispatch  
B. SOQL binding  
C. Workflow outbound message  
D. Visualforce remoting

**Answer:** A

## Q7 (Testing)
To execute enqueued jobs in test context:

A. `System.runAs()`  
B. `Test.startTest()` only  
C. `Test.stopTest()`  
D. `@future`

**Answer:** C

## Q8 (Deployment)
Which deploys metadata from sandbox to production without git pipeline?

A. Change Sets  
B. Data Import Wizard  
C. Report export  
D. Approval Process

**Answer:** A

## Score Guide
- 8/8: strong readiness
- 6-7/8: close; focus weak domain
- <=5/8: revisit weak domain notes and retry in 48 hours

## Official References
- [Salesforce Certified Platform Developer I](https://trailhead.salesforce.com/en/credentials/platformdeveloperi/)
- [PD1 Exam Prep Trailmix](https://trailhead.salesforce.com/users/strailhead/trailmixes/prepare-for-your-salesforce-platform-developer-i-credential)
- [Apex Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/)
- [LWC Dev Guide](https://developer.salesforce.com/docs/component-library/documentation/en/lwc)
- [Flow Builder (Trailhead)](https://trailhead.salesforce.com/content/learn/modules/flow-builder)
