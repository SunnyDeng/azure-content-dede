<properties
	pageTitle="Einrichten eines Azure AD-Mandanten | Microsoft Azure"
	description="Informationen zum Einrichten einen Azure Active Directory-Mandanten für das Registrieren und Erstellen von Anwendungen."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/28/2015"
	ms.author="dastrock"/>

# Einrichten eines Azure Active Directory-Mandanten

In Azure Active Directory (Azure AD) ist ein [Mandant](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) ein Stellvertreter einer Organisation. Es handelt sich um eine dedizierte Instanz des Azure AD-Diensts, den eine Organisation erhält und besitzt, nachdem sie sich für einen Microsoft-Clouddienst wie Azure, Microsoft Intune oder Office 365 registriert hat. Jeder Azure AD-Mandant ist eindeutig und von anderen Azure AD-Mandanten getrennt.

Ein Mandant enthält die Benutzer in einem Unternehmen und die dazugehörigen Informationen, wie z. B. Kennwörter, Benutzerprofildaten, Berechtigungen usw. Er enthält außerdem Gruppen, Anwendungen und andere Informationen, die eine Organisation und ihre Sicherheit betreffen.

Damit sich Azure AD-Benutzer bei Ihrer Anwendung anmelden können, müssen Sie Ihre Anwendung in einem eigenen Mandanten registrieren. Das Veröffentlichen einer Anwendung in einem Azure AD-Mandanten ist **völlig kostenlos**. Die meisten Entwickler erstellen für Experimentier-, Entwicklungs-, Staging- und Testzwecke meist mehrere Mandanten und Anwendungen. Organisationen, die sich für die Nutzung Ihrer Anwendung registrieren, können optional auch Lizenzen erwerben, wenn sie erweiterte Verzeichnisfeatures nutzen möchten.

Wie also wird ein Azure AD-Mandanten eingerichtet? Der Prozess ist möglicherweise ein wenig unterschiedlich, wenn Sie:

- [bereits für ein Office 365-Abonnement verfügen](#use-an-existing-office-365-subscription)
- [bereits über ein Azure-Abonnement verfügen, das einem Microsoft-Konto zugeordnet ist](#use-an-msa-azure-subscription)
- [bereits über ein Azure-Abonnement verfügen, das einem Organisationskonto zugeordnet ist](#use-an-organizational-azure-subscription)
- [über keines der genannten verfügen und ganz von vorn beginnen möchten](#start-from-scratch)

## Verwenden eines vorhandenes Office 365-Abonnements
Wenn Sie zwar über ein Office 365-Abonnement, jedoch nicht über ein Azure-Abonnement verfügen (und sich nicht am [Azure-Verwaltungsportal](https://manage.windowsazure.com) anmelden können), befolgen Sie [diese Anweisungen](https://technet.microsoft.com/library/dn832618.aspx) für den Zugriff auf Ihren Azure AD-Mandanten.

## Verwenden eines Azure-MSA-Abonnements
Wenn Sie sich zuvor mit Ihrem persönlichen Microsoft-Konto für ein Azure-Abonnement registriert haben, verfügen Sie bereits über einen Mandanten! Im [Azure-Verwaltungsportal](https://manage.windowsazure.com) sollte unter "Alle Elemente" und "Active Directory" ein Mandant mit dem Namen "Standardmandant" aufgeführt sein. Sie können diesen Mandanten wie gewünscht nutzen, sollten aber das Erstellen eines Organisationsadministratorkontos erwägen.

Gehen Sie dazu folgendermaßen vor. Alternativ können Sie einen neuen Mandanten und in diesem Mandanten einen Administrator erstellen, indem Sie einen ähnlichen Prozess befolgen.

1.	Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com) mit Ihrem persönlichen Konto an.
2.	Navigieren Sie im Portal (auf der linken Navigationsleiste) zum Abschnitt "Active Directory".
3.	Wählen Sie in der Liste der verfügbaren Verzeichnisse den Eintrag "Standardverzeichnis" aus.
4.	Klicken Sie oben auf der Seite auf den Link "Benutzer". Sie sehen einen einzelnen Benutzer in der Liste mit dem Wert "Microsoft-Konto" in der Spalte "Erstellt aus".
5.	Klicken Sie unten auf der Seite auf "Benutzer hinzufügen".
6.	Geben Sie zum Hinzufügen eines Benutzers die folgenden Details in das Formular ein:
    - Benutzertyp: Neuer Benutzer in Ihrer Organisation
    - Benutzername: (Wählen Sie einen Benutzernamen für diesen Administrator.)
    - Vorname/Nachname/Anzeigename: (Wählen Sie entsprechende Werte.)
    - Rolle: Globaler Administrator
    - Alternative E-Mail-Adresse: (Geben Sie entsprechende Werte ein.)
    - Optional: Multi-Factor Authentication aktivieren
    - Klicken Sie schließlich zum Abschließen der Erstellung des Benutzers (und Anzeigen des temporären Kennworts) auf die grüne Schaltfläche "ERSTELLEN".
7.	Wenn Sie das Formular "Benutzer hinzufügen" ausgefüllt und das temporäre Kennwort für den neuen Administrator erhalten haben, müssen Sie dieses Kennwort aufzeichnen, da Sie sich als dieser neue Benutzer anmelden müssen, um das Kennwort zu ändern. Sie können das Kennwort auch mithilfe einer alternativen E-Mail-Adresse direkt an den Benutzer senden.
8.	Um das temporäre Kennwort zu ändern, melden Sie sich bei https://login.microsoftonline.com mit diesem neuen Benutzerkonto an, und ändern Sie bei Aufforderung das Kennwort.


## Verwenden eines Azure-Organisationsabonnements
Wenn Sie sich zuvor mit Ihrem Organisationskonto für ein Azure-Abonnement registriert haben, verfügen Sie bereits über einen Mandanten! Im [Azure-Verwaltungsportal](https://manage.windowsazure.com) sollte ein Mandant unter "Alle Elemente" und "Active Directory" aufgeführt sein. Sie können diesen Mandanten gemäß Ihren Anforderungen nutzen. Sie können aber auch über die Schaltfläche "Neu" links unten im Portal einen neuen Mandanten erstellen.


## Ganz von vorn beginnen
Wenn Ihnen diese genannten Schritte unverständlich sind, machen Sie sich keine Sorgen. Besuchen Sie einfach [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization), um sich mit einer neuen Organisation bei Azure zu registrieren. Nachdem Sie den Vorgang abgeschlossen haben, haben Sie einen eigenen Azure AD-Mandanten mit dem Domänennamen, den Sie bei der Registrierung gewählt haben. Sie finden Ihren Mandanten im [Azure-Verwaltungsportal](https://manage.windowsazure.com), indem Sie im linken Navigationsbereich zu "Active Directory" navigieren.

Im Rahmen der Registrierung bei Azure müssen Sie Kreditkarteninformationen angeben. Sie können vertrauensvoll fortfahren, da das Veröffentlichen von Anwendungen in Azure AD oder Erstellen neuer Mandanten nicht berechnet wird.

<!---HONumber=Oct15_HO1-->