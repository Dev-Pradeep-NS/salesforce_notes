# 11 - Lightning Web Components (Detailed)

## Learning goals
- Build reactive UI with LWC fundamentals.
- Manage parent-child communication and rendering patterns.
- Apply SLDS and component styling correctly.

## LWC structure
Each component typically has:
- `componentName.html`
- `componentName.js`
- `componentName.js-meta.xml`
- optional `componentName.css`

## Lightning Component framework exam view
The Lightning Component framework lets developers build reusable, component-based UI for Salesforce.

Benefits:
- Reusable UI building blocks.
- Client-side interactivity.
- Server-side Apex integration when needed.
- Works with Lightning App Builder and Lightning Experience.
- Better fit than Visualforce for modern Salesforce UI.

PD1 may use "Lightning Component framework" broadly. Know both modern LWC and legacy Aura at a recognition level.

## LWC vs Aura awareness
- **LWC:** modern Salesforce component model based on web standards.
- **Aura:** older Lightning component model that uses `.cmp`, client-side controller JavaScript, helper JavaScript, style, renderer, and Apex server-side controllers.

Aura resource examples:
- Component markup: `.cmp`
- Client-side controller: `.js`
- Helper: `.js`
- Style: `.css`
- Design/config resources

Exam tip: if asked where client-side controller logic lives in Lightning components, the answer is JavaScript.

## Core decorators
- `@api`: public property/method exposed to parent.
- `@wire`: declarative data source wiring.
- `@track`: legacy helper for deep object mutation tracking (modern reactive patterns often reduce need).

## Data binding and events
```html
<template>
    <lightning-input label="Name" value={name} onchange={handleChange}></lightning-input>
    <p>Hello, {name}</p>
</template>
```
```javascript
import { LightningElement } from 'lwc';
export default class Greeting extends LightningElement {
    name = 'World';
    handleChange(event) { this.name = event.target.value; }
}
```

## Working with Apex
LWC can call Apex imperatively or through `@wire`.

Use `@wire` when data is cacheable and should react to parameter changes:
```javascript
import { LightningElement, wire } from 'lwc';
import getAccounts from '@salesforce/apex/AccountController.getAccounts';

export default class AccountList extends LightningElement {
    @wire(getAccounts) accounts;
}
```

Use imperative Apex when the action is user-driven or not naturally reactive:
```javascript
import getAccounts from '@salesforce/apex/AccountController.getAccounts';

async loadAccounts() {
    this.rows = await getAccounts();
}
```

Related Apex pattern:
```apex
public with sharing class AccountController {
    @AuraEnabled(cacheable=true)
    public static List<Account> getAccounts() {
        return [SELECT Id, Name FROM Account LIMIT 50];
    }
}
```

## Getters/setters
```javascript
import { LightningElement, api } from 'lwc';
export default class ScoreBadge extends LightningElement {
    _score = 0;
    @api
    set score(value) { this._score = Number(value) || 0; }
    get score() { return this._score; }
    get variant() { return this._score >= 80 ? 'success' : 'warning'; }
}
```

## Conditional and list rendering
```html
<template lwc:if={hasData}>
    <template for:each={rows} for:item="row">
        <p key={row.id}>{row.name}</p>
    </template>
</template>
<template lwc:else>
    <p>No data found</p>
</template>
```

## Parent-child communication
- Parent -> Child via `@api` property.
- Child -> Parent via custom event.

```javascript
this.dispatchEvent(new CustomEvent('select', { detail: { recordId: this.recordId } }));
```

## Query selectors
```javascript
const button = this.template.querySelector('lightning-button');
const allInputs = this.template.querySelectorAll('lightning-input');
```

## Lifecycle hooks (important)
Common hooks:
- `constructor()`: initialize lightweight defaults only.
- `connectedCallback()`: runs when component is inserted into DOM.
- `renderedCallback()`: runs after every render; avoid infinite loops.
- `disconnectedCallback()`: cleanup timers/listeners.
- `errorCallback(error, stack)`: catch descendant component errors.

```javascript
import { LightningElement } from 'lwc';
export default class LifecycleDemo extends LightningElement {
    connectedCallback() {
        // good place for initial non-DOM setup
    }
    renderedCallback() {
        // guard if doing one-time rendered work
    }
    disconnectedCallback() {
        // cleanup
    }
}
```

## Styling and SLDS
- Prefer SLDS utility classes first.
- Add scoped CSS only for custom behavior.
- Use SLDS styling hooks where relevant.

## Lightning page exposure
Expose an LWC to Lightning App Builder through the `.js-meta.xml` file.

Common targets:
- `lightning__AppPage`
- `lightning__RecordPage`
- `lightning__HomePage`

## UI security notes
- Apex called by LWC should use appropriate sharing mode.
- Enforce CRUD/FLS in Apex for sensitive data.
- Do not trust client-side validation alone.
- Keep business rules in Apex/Flow when data integrity matters.

## Common mistakes
- Mutating complex objects without reassign when needed.
- Missing `key` in list rendering.
- Overusing imperative logic where wire adapters fit better.
- Forgetting `@AuraEnabled(cacheable=true)` for wired Apex reads.
- Putting sensitive authorization decisions only in JavaScript.

## Practice set
1. Create parent-child pair where child emits selected row id.
2. Render paged list with `lwc:if` states (loading, data, empty).
3. Build form component with basic client-side validation and toast event.
4. Add an Apex method for read-only data and call it with `@wire`.
