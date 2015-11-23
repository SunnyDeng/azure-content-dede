<properties
	pageTitle="Hinzufügen oder Ändern des Co-Administrators, Dienstadministrators und Kontoadministrators in Azure | Microsoft Azure"
	description="Informationen zum Hinzufügen oder Ändern des Co-Administrators, Dienstadministrators und Kontoadministrators in Azure"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="meerak"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="genli"/>

# Hinzufügen oder Ändern des Co-Administrators, Dienstadministrators und Kontoadministrators in Azure
## Administratorrollen

In Microsoft Azure stehen drei verschiedene Administratorrollen zur Verfügung:

| Administratorrolle | Begrenzung | Beschreibung
| ------------- | ------------- |---------------|
|Kontoadministrator | 1 pro Azure-Konto |Die Person, die sich für Azure-Abonnements angemeldet oder diese erworben hat und für den Zugriff auf das Kontocenter und zum Ausführen verschiedener Verwaltungsaufgaben berechtigt ist. Dazu gehört z. B. die Möglichkeit zum Erstellen von Abonnements, Kündigen von Abonnements, Ändern der Abrechnung für ein Abonnement und Ändern des Dienstadministrators.
| Dienstadministrator | 1 pro Azure-Abonnement |Diese Person ist berechtigt, für Abonnements in dem ihr zugewiesenen Konto auf das Azure-Verwaltungsportal zuzugreifen. Standardmäßig ist der Kontoadministrator für ein neues Abonnement gleichzeitig auch der Dienstadministrator.|
|Co-Administrator|200 pro Abonnement (zusätzlich zum Dienstadministrator)|Diese Person verfügt über die gleichen Zugriffsrechte wie der Dienstadministrator, kann jedoch die Zuordnung von Abonnements zu Azure-Verzeichnissen nicht ändern.|

## Hinzufügen eines Co-Administrators für ein Abonnement
1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.
2. Wählen Sie im Navigationsbereich die Option **Einstellungen** > **Administratoren** > **Hinzufügen** aus. </br>![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Geben Sie die E-Mail-Adresse der Person ein, die Sie als Co-Administrator hinzufügen möchten, und wählen Sie dann das Abonnement aus, auf das der Co-Administrator zugreifen können soll.</br> ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

Die folgende E-Mail-Adresse kann als Co-Administrator hinzugefügt werden:

* **Microsoft-Konto** (früher Windows Live ID) </br> Sie können ein Microsoft-Konto verwenden, um sich bei allen verbraucherorientierten Microsoft-Produkten und Clouddiensten anzumelden, z. B. Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone und Xbox LIVE.
* **Organisationskonto**</br> Ein Organisationskonto ist ein Konto, das unter Azure Active Directory erstellt wird. Die Adresse eines Organisationskontos ähnelt der folgenden Adresse: Benutzer@<your domain>.onmicrosoft.com

**Hinweis**

 * Wenn Sie mit einem Microsoft-Konto angemeldet sind, können Sie nur andere Microsoft-Konten als Co-Administrator hinzufügen. Dies dient der Sicherheit und verhindert, dass nicht zu einer Organisation gehörende Konten erkennen, ob bestimmte Konten (z. B. janedoe@contoso.com) gültig sind.
 * Wenn Sie mit einem Organisationskonto angemeldet sind, können Sie andere Organisationskonten in Ihrer Organisation als Co-Administrator hinzufügen. Beispielsweise kann abby@contoso.com das Konto bob@contoso.com als Dienstadministrator oder Co-Administrator hinzufügen, nicht jedoch john@notcontoso.com. Mit einem Organisationskonto angemeldete Benutzer können Benutzer mit Microsoft-Konten weiterhin als Dienstadministrator oder Co-Administrator hinzufügen.
 * Nachdem es jetzt möglich ist, sich bei Azure mit einem Organisationskonto anzumelden, ändern sich die folgenden Kontoanforderungen für Dienstadministratoren und Co-Administratoren:

| Anmeldemethode| Microsoft-Konto als Co-Administrator oder Dienstadministrator hinzufügen? |Organisationskonto in der gleichen Organisation als Co-Administrator oder Dienstadministrator hinzufügen? |Organisationskonto in einer anderen Organisation als Co-Administrator oder Dienstadministrator hinzufügen?
| ------------- | ------------- |---------------|---------------|
|Microsoft Account |Ja|Nein|Nein|
|Organisationskonto|Ja|Ja|Nein|

## Ändern des Dienstadministrators für ein Abonnement
Nur der Kontoadministrator kann den Dienstadministrator für ein Abonnement ändern.

1. Melden Sie sich als Kontoadministrator beim [Kontoverwaltungsportal](https://account.windowsazure.com/subscriptions) an.
2. Wählen Sie das Abonnement aus, das Sie ändern möchten.
3. Klicken Sie auf der rechten Seite auf **Abonnementdetails bearbeiten**.</br>![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. Geben Sie im Feld **DIENSTADMINISTRATOR** die E-Mail-Adresse des neuen Dienstadministrators ein. ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## Ändern des Kontoadministrators

Informationen zum Übertragen des Besitzes des Azure-Kontos auf ein anderes Konto finden Sie unter [Übertragen eines Azure-Abonnements](../billing-subscription-transfer.md).

<!---HONumber=Nov15_HO3-->