# Partner WSDL

Behöver uppdateras ifall vi ska anropa en ny version av APIet.

Bara gjort det i Visual Studio och då följt stegen i https://stackoverflow.com/a/41496861

URL i steg 4 är fullständig sökväg till filen Partner.wsdl

## error CS0029

https://developer.salesforce.com/forums/?id=906F0000000Aj5kIAC

Fick det här felet efter att jag uppdaterade wsdl filen:

```text
error CS0029: Cannot implicitly convert type 'SalesforceApi.SalesforceReference.ListViewRecordColumn' to 'SalesforceApi.SalesforceReference.ListViewRecordColumn[]'
```

För att fixa det uppdaterade jag `Web References/SalesforceReference/Reference.cs` filen och kompilerade om.

Sök efter `ListViewRecordColumn[][]` och ersätt med `ListViewRecordColumn[]` (fanns på två ställen).