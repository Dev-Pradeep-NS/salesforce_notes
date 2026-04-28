# 12 - Visualforce (Detailed)

## Learning goals
- Understand Visualforce architecture and controller models.
- Build pages with standard/custom/extension controllers.
- Apply page messaging and execution understanding.

## What is Visualforce
Visualforce is Salesforce's server-rendered markup framework for custom UI pages.  
It remains important in legacy applications and migration projects.

## Architecture
Visualforce Page -> Controller -> Data model (`sObject`) -> Rendered output

Controller types:
- Standard Controller
- Custom Controller
- Controller Extension

MVC mapping:
- Model: Salesforce objects and fields.
- View: Visualforce markup.
- Controller: standard controller, custom Apex controller, or extension.

## Standard controller example
```xml
<apex:page standardController="Account">
    <apex:form>
        <apex:pageBlock title="Account">
            <apex:inputField value="{!Account.Name}" />
            <apex:commandButton value="Save" action="{!save}" />
        </apex:pageBlock>
    </apex:form>
</apex:page>
```

## Custom controller example
```apex
public with sharing class AccountCreateController {
    public String accountName { get; set; }
    public PageReference save() {
        insert new Account(Name = accountName);
        ApexPages.addMessage(new ApexPages.Message(ApexPages.Severity.CONFIRM, 'Saved'));
        return null;
    }
}
```

## Extension controller example
```apex
public with sharing class AccountExtension {
    private final ApexPages.StandardController std;
    public AccountExtension(ApexPages.StandardController controller) {
        std = controller;
    }
    public String getExtraMessage() {
        return 'Extension active';
    }
}
```

## Standard vs custom vs extension
- **Standard controller:** best when standard record behavior is enough.
- **Custom controller:** best when the page needs fully custom logic.
- **Controller extension:** best when you want to add behavior to a standard or custom controller.

Exam tip: standard controllers give built-in actions like `save`, `edit`, `delete`, and `cancel`.

## Getter and setter usage
- Getter methods can be called multiple times by page rendering lifecycle.
- Keep getters lightweight and side-effect free.

## Page messages
```xml
<apex:pageMessages />
```

```apex
ApexPages.addMessage(
    new ApexPages.Message(ApexPages.Severity.ERROR, 'Validation failed')
);
```

## System mode and security
- Visualforce with standard controller enforces standard behavior.
- Custom controller logic still needs explicit security consideration.
- Prefer `with sharing` for custom controllers unless system access is intentional.
- Enforce CRUD/FLS when reading or writing sensitive fields.
- Avoid exposing raw exception details to users.

## Incorporating Visualforce into Lightning
Visualforce pages can still be used in Lightning Experience:
- Add a Visualforce page to a Lightning page.
- Use Visualforce tabs.
- Override standard buttons/actions in supported scenarios.

Use Visualforce mainly for legacy pages, PDF rendering, or existing custom UI that has not moved to Lightning components.

## Web content in Visualforce
Visualforce can include:
- Standard Visualforce components.
- HTML/CSS/JavaScript.
- Static resources.
- Images and documents.

Security reminder: be careful with user-provided content and script injection.

## Common mistakes
- Heavy database calls inside getter methods.
- No null handling for URL parameter dependent logic.
- Mixing too much business logic in controllers.
- Forgetting that controller code needs security checks.
- Using Visualforce for new UI when LWC is the better fit.

## Practice set
1. Create Account edit page with validation + page messages.
2. Add controller extension that computes a dynamic summary.
3. Refactor page logic to service class and keep controller thin.
4. Identify whether three page requirements need standard controller, custom controller, or extension.
