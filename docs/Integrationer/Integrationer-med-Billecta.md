# Integrationer med Billecta

## Dagliga betalfiler

#### Syfte

A-kassan behöver dagliga betalfiler från alla förbund som har medlemmar anslutna till A-kassan. Med den här integrationen kan Middleware hämta ut betalningar ifrån Billecta och skicka informationen vidare till A-kassan.

#### Hämta information med

``GET /v1/akavia/PaymentsByProducts?id=[creditorPublicId]&from=[datetime]&to=[datetime]&productId=[productPublicId]``

- id=[creditorPublicId]
  - GUID för fakturaavsändaren
- from=[datetime]
  - Vilken dag vi vill hämta betalningar (från och med datumet)
- to=[datetime]
  - Vilken dag vill hämta betalningar (till och med datumet)
- productId=[productPublicId]
  - GUID för den produkten vi vill hämta betalningar för

#### Svar tillbaka är

- Personnummer
- OCR nummer
- Datum då banken tog emot betalningen
- Betalt belopp

##### Exempelsvar:

```json
[
    {
        "DebtorOrgNo": "19980302-2228",
        "OCR": "362349712647653",
        "PaymentDate": "2020-11-26",
        "Amount": 140.0
    },
    {
        "DebtorOrgNo": "19580501-6127",
        "OCR": "330506570454451",
        "PaymentDate": "2020-11-27",
        "Amount": 140.0
    }
]
```

# Fakturaunderlag

#### Syfte

Skicka alla medlemmar som ska faktureras den här månaden.

#### Skicka till Billecta

För enskild faktura (skapas direkt)

``POST /v1/akavia/CreateInvoice`` 

För flera fakturor (läggs på kö och ett jobb tar hand om att skapa fakturor)

``POST /v1/akavia/CreateInvoices``

#### Data som kommer skickas

-  Salesforce-ID
-  Personnummer
-  Medlemsnummer
-  Medlemskategori
-  Avgiftsklass
-  A-kassa

#### Exempel

Enskildfaktura:
```json
{
	CreditorPublicId: "4d9023ee-8d43-4e61-a6fc-96cce27cbefe",
    ExternalId: "123",
    SocialSecurityNumber: "191212121212",
    MemberNumber: "123456",
    MembershipCategory: "Yrkesverksam",
    FeeCategory: "Fullbetalande",
    HaveAkassa: true,
    IsCreditInvoice: false
}
```

För batch så ska det vara en json fil som innehåller flera poster från ovan (utan CreditorPublicId), men själva anropet är:

```json
{
    "CreditorPublicId": "4d9023ee-8d43-4e61-a6fc-96cce27cbefe",
    "File":
    {
      "FilePublicId": "Guid som vi skapar",
      "ContentType": "application/json",
      "Data": "VGVzdA== base64",
      "FileName": "File.json"
    }
}
```



# Sökbar information i Salesforce

#### Syfte

Hålla CRM så up-to-date som möjligt. Men också låta så många anrop som möjlig gå via Middleware för att ha bättre kontroll på att vi inte anropar Salesforce fler gånger än nödvändigt då de har en gräns på hur många gånger vi får anropa dem (under en rulladen 24h period).

#### Hämta data

Här ska det hämtas persondata och fakturadata ifrån Billecta och bara skickas vidare till Salesforce.

**Persondata**

Hämta för enskild person

``GET /v1/akavia/debtorpaymentstatus/{id}``

* {id}
  * Debtor public id (guid) för kunden

Hämta för alla under en period

``GET /v1/akavia/debtorpaymentstatuses/{id}?from={from}``

- {id}
  - GUID för fakturaavsändare
- from=[DateTime]
  - Från och med datum

**Förväntade kolumner att få tillbaka**

| Fältnamn                           | Kommentar |
| -------------------------- | ---- |
| Externalid                 | Matchas mot Salesforce-ID CRM |
| Saldo                      | Värdet kommer från Billecta.<br />Detta är "total-saldo" på personen, och inte på en specifik faktura |
| Förfallet  belopp          |      |
| Påminnelsestatus           | Högsta påminnelsestatus visas på personkortet |
| Senaste  betaldatum        |      |
| Autogiro: medgivandestatus | Status: pågående/godkänd/misslyckad |
| Betalsätt                  | BG/AG/kortbetalning |
| Distributionssätt          | post/Kivra/e-faktura/e-post/ingen leverans (för dem som har AG) |

**Exempel svar**

```Json
{
    "Balance_c": -811.0,
    "OverdueAmount_c": 2.0,
    "ReminderStatus_c": 1,
    "LatestPaymentDate_c": "2020-12-09",
    "PaymentMethod_c": "BG",
    "DistributionMethod_c": "Ingen leverans",
    "AutogiroApprovalStatus_c": "",
    "BillectaID_c": "90997df4-e137-42e8-b347-d3f95971e30e"
}
```



**Fakturadata**

Hämta allt för en fakturaavsändare

``GET /v1/Akavia/InvoiceActionStatuses?id=[Guid(CreditorPublicId)]&from=[DateTime]``

- id=[Guid(CreditorPublicId)]
  - GUID för fakturaavsändare
- from=[DateTime]
  - Från och med datum (klockslag verkar påverka)

Hämta info om en enskild faktura

``GET /v1/Akavia/InvoiceActionStatus?id=[string(ActionPublicId)]``

- id=[string(ActionPublicId)]
  - Billectas id-värde för fakturan

**Exempelsvar**

<span style="color:red">_**Kommer ExternalId finnas med ifall den har ett värde?**_</span>

```json
[
    {
        "DistributionMethod_c": "Ingen leverans",
        "Amount_c": 1.0,
        "PaymentMethod_c": "BG",
        "RemainingAmount_c": 0.0,
        "PaymentStatus_c": "Betald",
        "Name": "1",
        "OCR_c": "346072418176055",
        "ExpirationDate_c": "2020-10-18",
        "InvoiceDate_c": "2020-09-18",
        "InvoiceLink_c": "https://app.billecta.com/ht/Download?fileId=e75393b3-9e2d-4d1a-8c7d-7a48df74cd12&actionpublicid=2405395441",
        "InvoicePeriodStart_c": "",
        "InvoicePeriodEnd_c": "",
        "PaymentDate_c": "2020-09-18",
        "Reminder1_c": "Ej skickad",
        "Reminder2_c": "Ej skickad",
        "Reminder3_c": "Ej skickad",
        "InvoiceStatus_c": "Skickad",
        "MembershipCategory_c": "NOT IMPLEMENTED",
        "FeeCategory_c": "NOT IMPLEMENTED",
        "AutogiroStatusMessage_c": "",
        "AutogiroStatusMessageDate_c": ""
    }
]
```



# A-Kassan Saldomatchning

#### Syfte

A-Kassan har önskemål om att varje månade löpande kunna matcha Akavias medlemmar som betalar a-kasseavgift, men de inbetalningar som kommer in via de dagliga betalfilerna.

#### Hämta data

``GET /v1/akavia/paymentsperproduct/{id}?productid={productid}``

- {id}
  - GUID för fakturaavsändare
- productid={productid}``
  - GUID för produkten

**Exempelsvar**

```json
[
    {
        "SSN": "19121212-1212",
        "Name": "Tommy Torenius",
        "Amount": 2.0
    }
]
```

#### Data som ska skickas till A-Kassan

| Fältnamn                        | Kommentar                 |
| ------------------------------- | ------------------------- |
| Personnummer                    |                           |
| Namn                            |                           |
| Saldo (oavsett  noll/+ eller -) | A-kassesaldo på medlemmen |

