<properties
	pageTitle="Azure Active Directory B2C (Vorschauversion): Self-Service-Kennwortzurücksetzung | Microsoft Azure"
	description="Ein Thema, in dem das Einrichten der Self-Service-Kennwortzurücksetzung für Ihre Kunden in Azure Active Directory B2C demonstriert wird."
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
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C (Vorschauversion): Einrichten der Self-Service-Kennwortzurücksetzung für Ihre Kunden

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Diese Funktion ermöglicht Ihren Kunden (die sich für lokale Konten angemeldet haben) das eigenständige Zurücksetzen ihrer Kennwörter. Dadurch wird die Belastung für Ihre Supportmitarbeiter erheblich reduziert, insbesondere, wenn Ihre Anwendung Millionen von Kunden besitzt, die sie regelmäßig verwenden. Derzeit unterstützen wir nur die Verwendung einer verifizierten E-Mail-Adresse als Wiederherstellungsmethode. Wir werden zukünftig zusätzliche Wiederherstellungsmethoden (verifizierte Telefonnummer, Sicherheitsfragen usw.) hinzufügen. Standardmäßig ist für Ihr Verzeichnis die Self-Service-Kennwortzurücksetzung nicht aktiviert. Verwenden Sie die folgenden Schritte zum Aktivieren der Funktion:

1. Melden Sie sich beim [Azure-Portal](https://manage.windowsazure.com/) als Abonnementadministrator an. Dies ist dasselbe Geschäfts-, Schul- oder Unikonto bzw. dasselbe Microsoft-Konto, mit dem Sie Ihr Verzeichnis erstellt haben.
2. Navigieren Sie auf der linken Navigationsleiste zur Active Directory-Erweiterung.
3. Suchen Sie Ihr Verzeichnis auf der Registerkarte **Verzeichnis**, und klicken Sie darauf.
4. Klicken Sie auf die Registerkarte **Konfigurieren**.
5. Scrollen Sie nach unten zum Bereich **Richtlinie zum Zurücksetzen des Benutzerkennworts**, und legen Sie die Option **Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist** auf **JA** fest. Beachten Sie, dass die Option **Alternative E-Mail-Adresse** aktiviert ist, und lassen Sie diese so.

    ![Self-Service-Kennwortzurücksetzung](./media/active-directory-b2c-reference-sspr/sspr.png)
 
6. Klicken Sie unten auf der Seite auf **Speichern**. Sie haben es geschafft!

Zum Testen verwenden Sie die Funktion "Jetzt ausführen" für eine beliebige Anmelderichtlinie (die lokale Konten als Identitätsanbieter hat). Klicken Sie auf der lokalen Kontoanmeldeseite (auf der Sie Ihre E-Mail-Adresse und das Kennwort oder den Benutzername und das Kennwort eingeben) auf **Kein Zugriff auf Ihr Konto?**, um die Kundenerfahrung zu überprüfen.

> [AZURE.NOTE]Die Seiten der Self-Service-Kennwortzurücksetzung können mithilfe der [Unternehmensbranding-Funktion](active-directory-add-company-branding.md) angepasst werden.

<!---HONumber=Sept15_HO4-->