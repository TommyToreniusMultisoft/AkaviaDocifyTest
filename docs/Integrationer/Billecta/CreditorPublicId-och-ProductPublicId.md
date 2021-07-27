# CreditorPublicId och ProductPublicId

Båda är GUID-värden som Billecta använder för att kunna integrera med andra systemet.
Exempelvis `4d9023ee-8d43-4e61-a6fc-96cce27cbefe`

![image-20210108105438667](CreditorPublicId-och-ProductPublicId.assets/image-20210108105438667.png)

Middleware använder de värderna vid anrop till Billecta för att filtrera vilken information som ska hämtas.

## CreditorPublicId

GUID-värdet för det företag som skickar fakturor. För att hämta det värdet, logga först in på Billecta.

![image-20201118182253103](CreditorPublicId-och-ProductPublicId.assets/image-20201118182253103.png)

Uppe i vänsterhörnet kan man först klicka på företaget och sedan på Alla fakturaavsändare.



![image-20201118182338438](CreditorPublicId-och-ProductPublicId.assets/image-20201118182338438.png)

Under fler finns **CreditorPublicId**

![image-20201118182429429](CreditorPublicId-och-ProductPublicId.assets/image-20201118182429429.png)

## ProductPublicId

GUID-värde för den produkt som Middleware ska hämta information om. De hittas i vänstermenyn under Register -> Produkter

![image-20201118182502052](CreditorPublicId-och-ProductPublicId.assets/image-20201118182502052.png)

![image-20201118182528499](CreditorPublicId-och-ProductPublicId.assets/image-20201118182528499.png)

Längst till höger har vi tre punkter som ger menyn ovan.

![image-20201118182556598](CreditorPublicId-och-ProductPublicId.assets/image-20201118182556598.png)