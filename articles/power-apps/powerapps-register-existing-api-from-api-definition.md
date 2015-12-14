<properties
	pageTitle="Erstellen einer Swagger 2.0-API-Definition über eine API in PowerApps Enterprise | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine API mithilfe einer über eine vorhandene API erstellte Swagger 2.0-API-Definition registrieren."
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# Registrieren einer API über eine Swagger 2.0-API-Definition  
Viele Organisationen verfügen bereits über einige vorhandene APIs, die Benutzer verwenden und in ihren Apps nutzen können. Um diese APIs in Ihren Apps zu verwenden, müssen Sie die APIs im Azure-Portal „registrieren“. Folgende Optionen sind verfügbar:

- Registrieren einer [Microsoft-verwalteten API oder einer IT-verwalteten API](powerapps-register-from-available-apis.md)
- Registrieren einer in [Ihrer App Service-Umgebung](powerapps-register-api-hosted-in-app-service.md) gehosteten API
- Registrieren mithilfe einer Swagger 2.0-API-Definition

In diesem Artikel wird erläutert, wie Sie **eine Swagger 2.0-API-Definition registrieren**, die Sie über eine vorhandene API erstellt haben.

#### Voraussetzungen für den Einstieg

- Anmelden für [PowerApps Enterprise](powerapps-get-started-azure-portal.md)
- Erstellen einer [App Service-Umgebung](powerapps-get-started-azure-portal.md)

## Registrieren einer vorhandenen API mithilfe einer Swagger 2.0-API-Definition

Diese vorhandenen APIs können sehr einfach registriert werden. Dazu müssen diese Schritte ausgeführt werden:

1. Erstellen Sie die [Swagger 2.0](http://swagger.io)-API-Definition für die vorhandene API. In PowerApps wird Swagger 2.0 als Format für API-Definitionen verwendet. Mithilfe der auf der [Swagger 2.0-Website](http://swagger.io) aufgeführten Tools können Sie problemlos eine Swagger 2.0-API-Definition erstellen. Was Sie beachten sollten:  

	- Die ``host``-Eigenschaft sollte auf den tatsächlichen Endpunkt Ihrer vorhandenen API verweisen. Verwenden Sie kein Schema und keine Unterpfade. Geben Sie z. B. Folgendes ein: ``api.contoso.com``. <br/><br/>
	- In der ``basePath``-Eigenschaft sollten die Unterpfade für den Endpunkt der vorhandenen API aufgeführt sein, sofern er vorhanden ist. Beginnen Sie mit einem Schrägstrich ``/``. Geben Sie z. B. Folgendes ein: ``/purchaseorderapi``.

2. Stellen Sie sicher, dass die vorhandene API über Ihre App Service-Umgebung sicher aufgerufen werden kann: <br/><br/> a) Wenn Sie keine Bedenken haben, Ihre API über das Internet zugänglich zu machen, können Sie eine HTTP-Standardauthentifizierung zwischen Ihrer App Service-Umgebung und Ihrer vorhandenen API einrichten. Die entsprechenden Informationen finden Sie [hier](powerapps-configure-apis.md). <br/><br/> b) Wenn Sie Ihre API nur innerhalb des Netzwerks Ihrer Organisation zugänglich machen möchten, können Sie für den sicheren Zugriff auf das Netzwerk Ihrer Organisation ein virtuelles Netzwerk in der App Service-Umgebung einrichten. Die entsprechenden Informationen finden Sie [hier](../app-service-app-service-environment-intro.md).

3. Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option **PowerApps** und dann **Manage APIs** aus: ![][11]
4. Wählen Sie in „Manage APIs“ die Option **Add** aus: ![][12]
5. Geben Sie in **Add API** die API-Eigenschaften ein:  

	- Geben Sie unter **Name** einen Namen für die API ein. Beachten Sie, dass der eingegebene Name in die Laufzeit-URL der API eingefügt wird. Geben Sie einen aussagekräftigen und innerhalb Ihrer Organisation eindeutigen Namen ein.
	- Wählen Sie unter **Quelle** die Option **Import from Swagger 2.0** aus.

6. Laden Sie in **API definition (Swagger 2.0)** Ihre Swagger 2.0-API-Definitionsdatei hoch: ![][13]
7. Wählen Sie **ADD** aus, um diese Schritte abzuschließen.

> [AZURE.TIP]Wenn Sie eine API registrieren, wird diese in Ihrer App Service-Umgebung registriert. Nach der Registrierung in der App Service-Umgebung kann sie von anderen Apps innerhalb der gleichen App Service-Umgebung verwendet werden.

## Zusammenfassung und nächste Schritte

In diesem Thema wurde erläutert, wie Sie eine API über eine Swagger 2.0-API-Definition registrieren. In den folgenden Themen und Ressourcen finden Sie weitere Informationen zu PowerApps:

- [Konfigurieren von APIs](powerapps-configure-apis.md)
- [Hinzufügen einer neuen API](powerapps-register-from-available-apis.md)

<!--References-->
[11]: ./media/powerapps-register-existing-api-from-api-definition/registered-apis-part.png
[12]: ./media/powerapps-register-existing-api-from-api-definition/add-api-button.png
[13]: ./media/powerapps-register-existing-api-from-api-definition/add-api-blade.png

<!---HONumber=AcomDC_1203_2015-->