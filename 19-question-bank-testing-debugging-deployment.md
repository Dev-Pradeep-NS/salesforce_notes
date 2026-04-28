# 19 - Question Bank: Testing, Debugging, and Deployment

## Q1
Which is true for deployment of Apex to production?

A. 50% test coverage per class is enough  
B. 75% org-wide Apex coverage is required  
C. Tests can rely on production data  
D. Tests are optional for unlocked packages

**Answer:** B

## Q2
Why use `Test.startTest()` and `Test.stopTest()`?

A. To skip governor limits  
B. To isolate tested section and force async completion by `stopTest()`  
C. To deploy metadata  
D. To create users automatically

**Answer:** B

## Q3
Need to test HTTP callout behavior in Apex. Use:

A. `seeAllData=true`  
B. `Test.setMock()` with `HttpCalloutMock`  
C. Anonymous Apex only  
D. Debug logs only

**Answer:** B

## Q4
Best tool for quick one-off script in org (not a test replacement):

A. Execute Anonymous  
B. Batch Apex  
C. App Builder  
D. Validation Rule

**Answer:** A

## Q5
Which best describes Change Sets?

A. Source-driven git-native pipeline  
B. Org-to-org metadata deployment mechanism  
C. Data migration API  
D. UI component framework

**Answer:** B

## Q6
You need full-volume, production-like test environment. Best sandbox:

A. Developer sandbox  
B. Full sandbox  
C. Scratch org  
D. Trial org

**Answer:** B

## Official References
- [Apex Testing Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing_best_practices.htm)
- [Apex Test Methods and Annotations](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing.htm)
- [Use Mock and Stub Objects](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing_http_callouts.htm)
- [Debug Logs](https://help.salesforce.com/s/articleView?id=sf.code_add_users_debug_log.htm&type=5)
- [Change Sets](https://help.salesforce.com/s/articleView?id=sf.changesets.htm&type=5)
- [Sandbox Overview](https://help.salesforce.com/s/articleView?id=sf.data_sandbox.htm&type=5)
