<properties
	pageTitle="Azure Active Directory B2C (Vorschauversion): Self-Service-Kennwortzurücksetzung | Microsoft Azure"
	description="In diesem Thema wird das Einrichten der Self-Service-Kennwortrücksetzung für Ihre Kunden in Azure Active Directory B2C demonstriert."
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="swkrish"/>


# Azure Active Directory B2C-Vorschau: Einrichten der Self-Service-Kennwortrücksetzung für Ihre Kunden

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Das Feature für die Self-Service-Kennwortrücksetzung ermöglicht Ihren Kunden (die für lokale Konten registriert sind) das eigenständige Zurücksetzen ihrer Kennwörter. Dadurch wird die Belastung für Ihre Supportmitarbeiter erheblich reduziert, insbesondere, wenn Ihre Anwendung Millionen von Kunden besitzt, die sie regelmäßig verwenden. Derzeit unterstützen wir nur die Verwendung einer verifizierten E-Mail-Adresse als Wiederherstellungsmethode. Wir werden zukünftig zusätzliche Wiederherstellungsmethoden (verifizierte Telefonnummer, Sicherheitsfragen usw.) hinzufügen.

> [AZURE.NOTE]
Dieser Artikel gilt für die Self-Service-Kennwortzurücksetzung im Kontext einer Anmeldungsrichtlinie. Informationen zu vollständig anpassbaren Richtlinien zur Kennwortrücksetzung, die in Ihrer App aufgerufen werden, finden Sie in [diesem Artikel](./active-directory-b2c-reference-policies.md#create-a-password-reset-policy).

Standardmäßig ist für Ihr Verzeichnis die Self-Service-Kennwortzurücksetzung nicht aktiviert. Verwenden Sie die folgenden Schritte zum Aktivieren der Funktion:

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com/) als Abonnementadministrator an. Dies ist dasselbe Geschäfts-, Schul- oder Unikonto bzw. dasselbe Microsoft-Konto, mit dem Sie Ihr Verzeichnis erstellt haben.
2. Navigieren Sie auf der Navigationsleiste links zur Active Directory-Erweiterung.
3. Suchen Sie Ihr Verzeichnis auf der Registerkarte **Verzeichnis**, und klicken Sie darauf.
4. Klicken Sie auf die Registerkarte **Konfigurieren**.
5. Scrollen Sie nach unten zum Abschnitt **Richtlinie zum Zurücksetzen des Benutzerkennworts**, und legen Sie die Option **Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist** auf **JA** fest. Beachten Sie, dass die Option **Alternative E-Mail-Adresse** aktiviert ist, und lassen Sie diese so.

    ![Self-Service-Kennwortzurücksetzung](./media/active-directory-b2c-reference-sspr/sspr.png)

6. Klicken Sie unten auf der Seite auf **Speichern**. Sie haben es geschafft!

Zum Testen verwenden Sie das Feature „Jetzt ausführen“ für eine beliebige Anmelderichtlinie mit lokalen Konten als Identitätsanbieter. Klicken Sie auf der lokalen Kontoanmeldeseite (auf der Sie eine E-Mail-Adresse und ein Kennwort oder einen Benutzernamen und ein Kennwort eingeben) auf **Sie können nicht auf Ihr Konto zugreifen?**, um die Benutzererfahrung zu überprüfen.

> [AZURE.NOTE]
Die Seiten der Self-Service-Kennwortrücksetzung können mithilfe des [Features für Unternehmensbranding](../active-directory/active-directory-add-company-branding.md) angepasst werden.

<!---HONumber=AcomDC_0323_2016-->