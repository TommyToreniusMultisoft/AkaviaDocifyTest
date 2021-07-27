## Akavia

https://www.akavia.se/

Ett fackförbund för akademiker. Det bildades 2020 av att Jusek och Civilekonomerna gick ihop.
De har idag ca 130 000 medlemmar, som består av:

- Ekonomer
- Jurister
- Samhällsvetare
- IT-akademiker
- Personalvetare
- Kommunikatörer

## Softadmin systemet

Vårat system är ett integrationssystem, så kallat Middleware. Därför kallas systemet för just **Middleware** av Akavia.

För att hantera alla deras medlemmar har de valt att använda Salesforce.

> TODO Varför valde de oss?

Vi integrerar mot deras Salesforce lösning och mot alla andra företag som de skickar medlemsinformation till.

Majoriteten av integrationer är antigen en filinläsning och uppdatera data i Salesforce. Eller hämta ut data ifrån Salesforce och skapa en fil som sedan skickas vidare.

Multisoft skapar [integrationstyper](Softadmin\Integrationstyper.md) som Akavia sedan kan välja hur ofta de ska köras, samt inställningar rörande varifrån filer ska läsas eller skrivas. Se mer under [Lägg upp en Integration](Guider\Lägg-upp-en-Integration.md) 

