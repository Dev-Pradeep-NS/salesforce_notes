# 18 - Question Bank: User Interface

## Q1
Team needs modern reusable UI components in Lightning Experience. Best choice:

A. Visualforce only  
B. LWC  
C. Workflow rule  
D. Approval process

**Answer:** B

## Q2
A legacy page needs quick extension of standard record behavior without replacing all base logic. Best fit:

A. Custom controller only  
B. Standard controller extension  
C. Trigger  
D. Batch class

**Answer:** B

## Q3
Child LWC must notify parent of selected record. Preferred pattern:

A. Parent polls database  
B. Child dispatches custom event  
C. Child writes to static variable  
D. Child updates page layout metadata

**Answer:** B

## Q4
Which lifecycle hook runs when component is inserted in DOM?

A. `renderedCallback` only  
B. `connectedCallback`  
C. `constructor` after every render  
D. `errorCallback` first

**Answer:** B

## Q5
To expose an LWC to App Builder, configure:

A. Apex class sharing  
B. `.js-meta.xml` targets  
C. Validation rule  
D. Named credential

**Answer:** B

## Q6
Best practice for UI security:

A. Client-side checks only  
B. Enforce CRUD/FLS and sharing in Apex for sensitive operations  
C. Disable server validation  
D. Use `without sharing` everywhere

**Answer:** B

## Official References
- [Lightning Web Components Developer Guide](https://developer.salesforce.com/docs/component-library/documentation/en/lwc)
- [Create and Configure Lightning Web Components](https://developer.salesforce.com/docs/platform/lwc/guide/create-components.html)
- [LWC Lifecycle Hooks](https://developer.salesforce.com/docs/platform/lwc/guide/create-lifecycle-hooks.html)
- [Communicate with Events](https://developer.salesforce.com/docs/platform/lwc/guide/events-create-dispatch.html)
- [Visualforce Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.pages.meta/pages/)
