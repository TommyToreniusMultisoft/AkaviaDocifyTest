# Upsert

https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_calls_upsert.htm

Upsert gör en insert eller updatering beroende på om det redan finns en post. För att avgöra ifall det finns en post eller inte måste man skicka med vilken kolumn som Salesforce ska titta på för att ta beslut om den ska lägga till en ny post eller uppdatera en befintlig post.

- Finns ingen post görs insert (create)
- Finns 1 post görs update
- Finns flera poster blir det ett fel

#### ExternalIDFieldName

Det fältet som man säger åt Salesfoce att titta på.

- Om det är en tabell som alltid finns i Salesforce måste det fältet ha attributet **idLookup** = true

- Om det är en custom tabell (har __c i slutet på namnet), måste fältet ha attributet **externalId** = true

#### C#

```C#
sObject[] upserts = new sObject[2];
// lägg in de sObjects som ska upsertas
// Sätt ExternallDFieldName = "MyExtId__c"
UpsertResult[] upsertResults = binding.upsert("MyExtId__c", upserts);
// Kontrollera resultat
```

#### SQL

```sql
IF @Action = 'Init'
BEGIN
	SELECT
		1 AS [RequestId],
		'Upsert' AS [Action],
		'Account' AS [SalesforceTableName],
		'BillectaID__c' AS [ExternalIDFieldName] -- Vilket fält som Salesforce ska göra sin jämförelse mot
		
	RETURN 0;
END

IF @Action = 'Upsert' AND @ExtraInformation = 1
BEGIN
	SELECT
		1 AS [MultisoftId], -- För att vi ska veta vilken post det är i resultatet
		NULL AS [SalesforceId], -- Måste finnas (ska vara NULL, finns bara för att göra C# koden lättare)
		-- Fält som ska uppdateras/skapas
		'Test12' AS [FirstName],
		'123' AS [BillectaID__c] -- ExternalIDFieldName värde måste vara ett fält

	RETURN 0;
END

IF @Action = 'Upsert' AND @IsResult = 1
BEGIN
	IF 1 = 0
	BEGIN
		CREATE TABLE #QueryResult
		(
			MultisoftId nvarchar(100) NOT NULL,
			SalesforceId nvarchar(100) NULL,
			ErrorMessage nvarchar(max) NULL,
            IsCreated bit NOT NULL
		);
	END
	
	-- Uppdatera/logga
END
```



