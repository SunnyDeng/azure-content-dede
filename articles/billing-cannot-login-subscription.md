<properties
	pageTitle="Ich kann mich nicht anmelden, um mein Azure-Abonnement zu verwalten | Microsoft Azure"
	description="Informationen zur Problembehandlung bei einigen häufig auftretenden Anmeldeproblemen bei Azure-Abonnements"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="na"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/30/2015"
	ms.author="genli"/>

# Ich kann mich nicht anmelden, um mein Azure-Abonnement zu verwalten

Dieser Artikel hilft Ihnen bei der Problembehandlung einiger häufiger Ursachen bei Anmeldeproblemen.

## Auf welches Portal möchten Sie zugreifen?

Kontobesitzer können nur auf das [Kontocenter](https://account.windowsazure.com/) zugreifen, während Dienstadministratoren und Co-Administratoren Zugriff auf das [Azure-Portal](https://manage.windowsazure.com/) haben.

Klicken Sie auf die folgenden Links, um Anweisungen zum Aktualisieren der Administratorrollen zu erhalten:

- [So aktualisieren Sie den Dienstadministrator für Ihr Konto](./billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription)

- [So fügen Sie einen neuen Co-Administrator im Verwaltungsportal hinzu](./billing-add-change-azure-subscription-administrator.md#add-a-co-administrator-for-a-subscription)

## Ist Ihr Abonnement mit einem Microsoft-Konto oder Organisationskonto verknüpft?

Ihr Microsoft-Konto ist die E-Mail-Adresse, die Sie zusammen mit Ihrem Kennwort verwenden, um sich bei Windows Live-Programmen oder -Diensten anzumelden, z. B. bei Outlook, Hotmail, MSN oder OneDrive. Sie können ein Microsoft-Konto mit einer beliebigen E-Mail-Adresse einrichten, z. B. auch mit Ihrer Firmen-E-Mail-Adresse. Weitere Informationen finden Sie unter [www.microsoft.com/account](http://www.microsoft.com/account).

Wenn Ihr Konto mit einem Organisationskonto verknüpft ist, wählen Sie die richtige Anmeldeoption aus (siehe Abbildung unten). Weitere Informationen zur Verwendung eines Organisationskontos finden Sie unter [Als Unternehmen für Azure registrieren](./active-directory/sign-up-organization.md).

![Anmeldeseite](./media/billing-cannot-login-subscription/signin.png)

## Co-Administrator: Verwenden Sie den richtigen Kontotyp zum Verwalten anderer Konten?

- Wenn Sie mit einem Microsoft-Konto angemeldet sind, können Sie nur andere Microsoft-Konten als Co-Administratoren hinzufügen. Dies ist eine Sicherheitsanforderung und verhindert, dass nicht zu einer Organisation gehörende Konten erkennen, ob bestimmte Konten (z. B. janedoe@contoso.com) gültig sind.
- Wenn Sie mit einem Organisationskonto angemeldet sind, können Sie andere Organisationskonten in Ihrer Organisation als Co-Administratoren hinzufügen. Beispielsweise kann abby@contoso.com das Konto bob@contoso.com als Dienstadministrator oder Co-Administrator hinzufügen, nicht jedoch john@notcontoso.com. Mit einem Organisationskonto angemeldete Benutzer können zudem Benutzer mit Microsoft-Konten als Dienstadministrator oder Co-Administrator hinzufügen.

Nachdem es jetzt möglich ist, sich bei Azure mit einem Organisationskonto anzumelden, ändern sich die folgenden Kontoanforderungen für Dienstadministratoren und Co-Administratoren:

| Anmeldemethode| Microsoft-Konto als Co-Administrator oder Dienstadministrator hinzufügen? |Organisationskonto in der gleichen Organisation als Co-Administrator oder Dienstadministrator hinzufügen? |Organisationskonto in einer anderen Organisation als Co-Administrator oder Dienstadministrator hinzufügen?
| ------------- | ------------- |---------------|---------------|
|Microsoft Account |Ja|Nein|Nein|
|Organisationskonto|Ja|Ja|Nein|

## Versuchen Sie, den Cache und Cookies mit dem Internet Explorer-Modus „InPrivate-Browsen“ und mit einem anderen Browser zu löschen

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Azure-Experten im [MSDN Azure-Forum oder im Stack Overflow-Forum](http://azure.microsoft.com/support/forums/) Fragen stellen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und klicken Sie auf „Support erhalten“. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](http://azure.microsoft.com/support/faq/).

<!---HONumber=AcomDC_1203_2015-->