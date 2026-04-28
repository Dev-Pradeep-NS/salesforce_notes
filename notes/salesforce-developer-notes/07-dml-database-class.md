# 07 - DML and Database Class (Detailed)

## Learning goals
- Understand each DML operation and transaction behavior.
- Use `Database` methods for partial success and detailed error handling.
- Apply rollback and savepoint safely.

## DML operations overview
- `insert`, `update`, `upsert`, `delete`, `undelete`, `merge`

```apex
Account a = new Account(Name = 'Acme');
insert a;
a.Name = 'Acme Updated';
update a;
```

## Upsert with external ID
```apex
Account extAcc = new Account(External_Id__c = 'ACC-1001', Name = 'Ext Account');
upsert extAcc External_Id__c;
```

## Delete and undelete
```apex
delete extAcc;
undelete extAcc;
```

## Merge duplicates
```apex
Account master = [SELECT Id FROM Account LIMIT 1];
Account duplicate = new Account(Name = 'Dup Acme');
insert duplicate;
merge master duplicate;
```

## Database class for partial success
`Database.insert(records, false)` avoids all-or-none failure.

```apex
List<Account> toInsert = new List<Account>{
    new Account(Name = 'Valid A'),
    new Account() // invalid
};

Database.SaveResult[] saveResults = Database.insert(toInsert, false);
for (Database.SaveResult sr : saveResults) {
    if (!sr.isSuccess()) {
        for (Database.Error err : sr.getErrors()) {
            System.debug('Error: ' + err.getMessage());
        }
    }
}
```

## Transaction control and rollback
```apex
Savepoint sp = Database.setSavepoint();
try {
    insert new Account(Name = 'Txn A');
    insert new Contact(LastName = null); // intentional error
} catch (Exception ex) {
    Database.rollback(sp);
}
```

## Transfer-style transaction example (Savepoint pattern)
Use this pattern when two related updates must succeed together, or both revert.

```apex
public with sharing class OpportunityTransferService {
    public static void transferAmount(Id fromOpportunityId, Id toOpportunityId, Decimal amount) {
        if (fromOpportunityId == null || toOpportunityId == null) {
            throw new IllegalArgumentException('Source and target opportunity IDs are required.');
        }
        if (fromOpportunityId == toOpportunityId) {
            throw new IllegalArgumentException('Source and target must be different records.');
        }
        if (amount == null || amount <= 0) {
            throw new IllegalArgumentException('Transfer amount must be greater than zero.');
        }

        // Optional but recommended in sensitive contexts:
        // check object/field access before query/update.

        List<Opportunity> rows = [
            SELECT Id, Name, Amount
            FROM Opportunity
            WHERE Id IN :new List<Id>{fromOpportunityId, toOpportunityId}
            FOR UPDATE
        ];

        Opportunity fromOpp;
        Opportunity toOpp;
        for (Opportunity o : rows) {
            if (o.Id == fromOpportunityId) fromOpp = o;
            if (o.Id == toOpportunityId) toOpp = o;
        }

        if (fromOpp == null || toOpp == null) {
            throw new IllegalArgumentException('Could not find both opportunity records.');
        }
        Decimal fromAmount = (fromOpp.Amount == null) ? 0 : fromOpp.Amount;
        Decimal toAmount = (toOpp.Amount == null) ? 0 : toOpp.Amount;
        if (fromAmount < amount) {
            throw new IllegalArgumentException('Insufficient amount on source opportunity.');
        }

        Savepoint sp = Database.setSavepoint();
        try {
            fromOpp.Amount = fromAmount - amount;
            toOpp.Amount = toAmount + amount;
            update new List<Opportunity>{fromOpp, toOpp};
        } catch (DmlException ex) {
            Database.rollback(sp);
            throw ex;
        }
    }
}
```

Why this is important:
- Shows atomic transaction behavior.
- Demonstrates guard validation before DML.
- Uses `FOR UPDATE` to reduce concurrent update risk.
- Applies rollback only when update fails.

## Important Database methods
- `Database.countQuery('SELECT count() FROM Account')`
- `Database.convertLead(leadConvertReq)`
- `Database.emptyRecycleBin(records)`

## Database result objects matrix
When using `Database` methods, Salesforce returns result objects that include success/failure details per record.

| Operation | Result Object Type |
| --- | --- |
| `insert`, `update` | `Database.SaveResult` |
| `upsert` | `Database.UpsertResult` |
| `merge` | `Database.MergeResult` |
| `delete` | `Database.DeleteResult` |
| `undelete` | `Database.UndeleteResult` |
| `Database.convertLead(...)` | `Database.LeadConvertResult` |
| `Database.emptyRecycleBin(...)` | `Database.EmptyRecycleBinResult` |

### `SaveResult` pattern
```apex
Database.SaveResult[] res = Database.insert(records, false);
for (Database.SaveResult r : res) {
    if (r.isSuccess()) {
        System.debug('Inserted Id: ' + r.getId());
    } else {
        for (Database.Error e : r.getErrors()) {
            System.debug(e.getStatusCode() + ' | ' + e.getMessage());
            System.debug('Fields: ' + String.join(e.getFields(), ', '));
        }
    }
}
```

### `UpsertResult` pattern
```apex
Database.UpsertResult[] upsertRes = Database.upsert(records, Account.External_Id__c, false);
for (Database.UpsertResult r : upsertRes) {
    if (r.isSuccess()) {
        System.debug('Id: ' + r.getId() + ', Created? ' + r.isCreated());
    }
}
```

### `DeleteResult` / `UndeleteResult` pattern
```apex
Database.DeleteResult[] delRes = Database.delete(records, false);
Database.UndeleteResult[] undelRes = Database.undelete(records, false);
```

### `LeadConvertResult` pattern
```apex
Database.LeadConvert lc = new Database.LeadConvert();
lc.setLeadId(leadId);
lc.setDoNotCreateOpportunity(false);
Database.LeadConvertResult lcr = Database.convertLead(lc);
if (!lcr.isSuccess()) {
    for (Database.Error e : lcr.getErrors()) {
        System.debug(e.getMessage());
    }
}
```

## DML cookbook (class-style consolidated examples)
### Standalone DML flow in one place
```apex
public with sharing class DmlCookbook {
    public static void insertAccounts() {
        List<Account> rows = new List<Account>();
        for (Integer i = 1; i <= 5; i++) {
            rows.add(new Account(Name = 'Cookbook ' + i));
        }
        insert rows;
    }

    public static void updateAccounts() {
        List<Account> rows = [SELECT Id, Name FROM Account WHERE Name LIKE 'Cookbook %' LIMIT 50];
        for (Account a : rows) a.Name = a.Name + ' Updated';
        update rows;
    }

    public static void upsertByExternalId() {
        List<Account> rows = new List<Account>{
            new Account(External_Id__c = 'CB-001', Name = 'Cookbook Ext 1'),
            new Account(External_Id__c = 'CB-002', Name = 'Cookbook Ext 2')
        };
        upsert rows External_Id__c;
    }

    public static void deleteAndUndelete() {
        List<Account> rows = [SELECT Id, Name FROM Account WHERE Name LIKE 'Cookbook %' LIMIT 10];
        delete rows;
        undelete rows;
    }
}
```

### Duplicate-delete pattern (safe version)
Avoid deduping only by `Name` in production; use stronger keys when possible.
```apex
List<Contact> contacts = [SELECT Id, FirstName, LastName, Email FROM Contact WHERE Email != null];
Map<String, Contact> firstByEmail = new Map<String, Contact>();
List<Contact> toDelete = new List<Contact>();
for (Contact c : contacts) {
    String key = c.Email.trim().toLowerCase();
    if (!firstByEmail.containsKey(key)) {
        firstByEmail.put(key, c);
    } else {
        toDelete.add(c);
    }
}
if (!toDelete.isEmpty()) delete toDelete;
```

### Merge combinations (quick reference)
Merge supports Accounts, Contacts, and Leads.
- master + one duplicate:
```apex
// merge masterAcc duplicateAcc;
```
- master + list of duplicates (up to two at a time):
```apex
// merge masterAcc new List<Account>{dup1, dup2};
```

### Bulkification: bad vs good
Bad (query inside loop):
```apex
for (Account a : [SELECT Id FROM Account LIMIT 50]) {
    List<Opportunity> opps = [SELECT Id, Name FROM Opportunity WHERE AccountId = :a.Id];
}
```

Good (single grouped query):
```apex
List<Account> accs = [SELECT Id FROM Account LIMIT 50];
Map<Id, List<Opportunity>> oppByAcc = new Map<Id, List<Opportunity>>();
for (Opportunity o : [SELECT Id, Name, AccountId FROM Opportunity WHERE AccountId IN :accs]) {
    if (!oppByAcc.containsKey(o.AccountId)) oppByAcc.put(o.AccountId, new List<Opportunity>());
    oppByAcc.get(o.AccountId).add(o);
}
```

## Best practices
- Group DML by object type.
- Keep DML outside loops.
- Capture and log result object details.
- Decide between all-or-none vs partial success intentionally.
- Use external IDs for integration-heavy upsert logic.
- Validate keys used for dedupe logic (for example, email, external IDs) instead of display names.

## Common mistakes
- Mixing too many responsibilities in one transaction.
- No compensation strategy when partial records fail.
- Not validating required fields before DML.
- Swallowing DML errors with only debug logs and no caller-facing handling.

## Official references
- [Transaction Control (Savepoint and Rollback)](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/langCon_apex_transaction_control.htm)
- [Database Class Methods](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_methods_system_database.htm)
- [Result Objects in Database Methods](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_methods_system_database_saveresult.htm)
- [DML Statements](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/langCon_apex_dml_examples.htm)
- [Merge Statement](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/langCon_apex_dml_examples_merge.htm)
- [Record Locking with FOR UPDATE](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/langCon_apex_locking_statements.htm)
- [Secure Apex (Sharing, CRUD, FLS)](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_classes_keywords_sharing.htm)

## Practice set
1. Upsert Accounts by external id from a list payload.
2. Insert mixed-valid records and collect failed-row report.
3. Implement rollback for multi-step transaction if any critical step fails.
4. Build a transfer-style service method with validation, `FOR UPDATE`, and rollback.
5. Build one class with `insert`, `update`, `upsert`, `delete`, `undelete`, and `merge` demo methods.
6. Write a dedupe method using email-based keying and bulk delete duplicates.
