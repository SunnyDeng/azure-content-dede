<properties
	pageTitle="App-Modell v2.0 | Microsoft Azure"
	description="Vorgehensweise beim Registrieren einer App bei Microsoft zum Aktivieren der Anmeldung und Integration von Apps mit App-Modell v2.0."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# App-Modell v2.0 (Vorschauversion): Registrieren einer App bei Microsoft

Um eine App zu erstellen, die sowohl eine MSA- als auch eine Azure AD-Anmeldung zulässt, müssen Sie zunächst eine Anwendung bei Microsoft registrieren. Sie können keine möglicherweise in Azure AD oder MSA vorhandenen Apps nutzen – es ist Zeit, eine brandneue App zu erstellen.

> [AZURE.NOTE]Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion). Anweisungen zum Integrieren in den allgemein verfügbaren Azure AD-Dienst finden Sie im [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

## Besuchen Sie das Microsoft App-Registrierungsportal
Navigieren Sie zunächst zu [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com). Dies ist das neue App-Registrierungportal, in dem Sie alles mögliche im Zusammenhang mit Ihren Microsoft-Apps verwalten können.

Melden Sie sich entweder mit einem persönlichen Microsoft-Konto oder einem Geschäfts- oder Schulkonto an. Wenn Sie noch kein Konto besitzen, melden Sie sich für ein neues persönliches Konto an. Legen Sie los, es dauert nicht lange – wir warten hier aus Sie.

Fertig? Jetzt sollten Sie Ihre Liste der Microsoft-Apps sehen, die wahrscheinlich noch leer ist. Ändern wir das.

<!-- TODO: Verify strings here -->
Klicken Sie auf **App hinzufügen**, und geben Sie einen Namen für die App ein. Das Portal weist der App eine global eindeutige Anwendungs-ID zu, die Sie später in Ihrem Code verwenden. Wenn Ihre App eine serverseitige Komponente enthält, die Zugriffstoken für Aufrufe von APIs benötigt (denken Sie an Office, Azure oder Apps von Drittanbietern), möchten Sie möglicherweise auch hier einen **geheimen Anwendungsschlüssel** erstellen. <!-- TODO: Link for app secrets -->

Fügen Sie als Nächstes die Plattformen hinzu, die Ihre App verwenden wird. – Stellen Sie bei webbasierten Apps einen **Umleitungs-URI** bereit, an den die Anmeldenachrichten gesendet werden können. – Kopieren Sie bei mobilen Apps den standardmäßigen Umleitungs-URI, der automatisch für Sie erstellt wurde.

Optional können Sie das Aussehen und Verhalten der Anmeldeseite im Profilabschnitt anpassen. Achten Sie darauf, vor dem Fortfahren auf **Speichern** zu klicken.

## Erstellen Sie eine Schnellstart-App
Nun, da Sie über eine Microsoft-App verfügen, können Sie eines unserer Schnellstart-Tutorials zum Einrichten und Ausführen mit App-Modell v2.0 abschließen. Hier sind einige Vorschläge:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=August15_HO7-->