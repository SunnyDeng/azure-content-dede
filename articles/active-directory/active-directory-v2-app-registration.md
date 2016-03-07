<properties
	pageTitle="v2.0-Endpunkt: App-Registrierung | Microsoft Azure"
	description="Registrieren einer App bei Microsoft für die Anmeldung und den Zugriff auf Microsoft-Dienste mit dem v2.0-Endpunkt"
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
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Registrieren einer App mit dem v2.0-Endpunkt

Um eine App zu erstellen, die sowohl eine MSA- als auch eine Azure AD-Anmeldung zulässt, müssen Sie zunächst eine Anwendung bei Microsoft registrieren. Derzeit können Sie keine in Azure AD oder MSA vorhandenen Apps nutzen, stattdessen müssen Sie eine neue App erstellen.

> [AZURE.NOTE]
	Nicht alle Szenarios und Funktionen von Azure Active Directory werden vom v2.0-Endpunkt unterstützt. Lesen Sie die Informationen zu den [Einschränkungen des v2.0-Endpunkts](active-directory-v2-limitations.md), um zu bestimmen, ob Sie den v2.0-Endpunkt verwenden sollten.

## Aufrufen des Microsoft App-Registrierungsportals
Navigieren Sie zunächst zu [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com). Dies ist ein neues App-Registrierungsportal, in dem Sie Ihre Microsoft-Apps verwalten können.

Melden Sie sich entweder mit einem persönlichen Microsoft-Konto oder einem Geschäfts- oder Schulkonto an. Wenn Sie noch kein Konto besitzen, melden Sie sich für ein neues persönliches Konto an. Legen Sie los, es dauert nicht lange – wir warten hier aus Sie.

Fertig? Jetzt sollten Sie Ihre Liste der Microsoft-Apps sehen, die wahrscheinlich noch leer ist. Ändern wir das.

Klicken Sie auf **App hinzufügen**, und geben Sie einen Namen für die App ein. Das Portal weist der App eine global eindeutige Anwendungs-ID zu, die Sie später in Ihrem Code verwenden. Wenn Ihre App eine serverseitige Komponente enthält, die Zugriffstoken für Aufrufe von APIs benötigt (denken Sie an Office, Azure oder Ihre eigene Web-API), möchten Sie möglicherweise auch hier einen **geheimen Anwendungsschlüssel** erstellen. <!-- TODO: Link for app secrets -->

Fügen Sie dann die Plattformen hinzu, auf denen Ihre App verwendet wird.

- Geben Sie bei webbasierten Apps einen **Umleitungs-URI** an, an den die Anmeldenachrichten gesendet werden können.
- Notieren Sie sich den standardmäßigen Umleitungs-URI für mobile Apps, der automatisch erstellt wurde.

Optional können Sie das Aussehen und Verhalten der Anmeldeseite im Profilabschnitt anpassen. Achten Sie darauf, vor dem Fortfahren auf **Speichern** zu klicken.

> [AZURE.NOTE] Beim Erstellen einer Anwendung über [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) wird die Anwendung bei dem Home-Mandanten des Kontos registriert, das Sie für die Anmeldung im Portal verwenden. Das bedeutet, dass Sie eine Anwendung nicht mit einem persönlichen Microsoft-Konto bei Ihrem Azure AD-Mandanten registrieren können. Wenn Sie eine Anwendung ausdrücklich bei einem bestimmten Mandanten registrieren möchten, müssen Sie sich mit einem Konto anmelden, das ursprünglich in diesem Mandanten erstellt wurde.

## Erstellen Sie eine Schnellstart-App
Nachdem Sie eine Microsoft-App erstellt haben, können Sie eines unserer Schnellstarttutorials zum v2.0-Endpunkt absolvieren. Hier sind einige Vorschläge:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=AcomDC_0224_2016-->