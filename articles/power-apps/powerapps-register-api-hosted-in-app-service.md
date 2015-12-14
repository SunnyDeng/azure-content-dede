<properties
	pageTitle="Entwickeln oder Erstellen einer in der App Service-Umgebung gehosteten API in PowerApps Enterprise | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine benutzerdefinierte in der App Service-Umgebung gehostete API im Azure-Portal registrieren."
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

# Registrieren einer in Ihrer App Service-Umgebung gehosteten API
In PowerApps können vorhandene, lokal oder in der Cloud gehostete APIs registriert werden. Dies bietet Ihnen vielfältige Möglichkeiten. In manchen Szenarios möchten Sie eventuell einige neue APIs entwickeln oder erstellen, beispielsweise:

- Implementieren einiger neuer Funktionen zur Verwendung in Ihrer Organisation
- Erstellen von APIs, die auf vorhandenen Funktionen oder Daten aufbauen, sodass Benutzer ihre Apps optimal erstellen können.

Wenn Sie Ihre APIs in Ihrer App Service-Umgebung hosten, können Sie alle vorhandenen Funktionen der [App Service-Umgebung](../app-service-app-service-environment-intro.md) nutzen und zudem von einer optimierten Integration profitieren.

Um diese APIs in Ihren Apps zu verwenden, müssen Sie die APIs im Azure-Portal „registrieren“. Folgende Optionen sind verfügbar:

- Registrieren einer [Microsoft-verwalteten API oder einer IT-verwalteten API](powerapps-register-from-available-apis.md)
- Registrieren einer in Ihrer App Service-Umgebung gehosteten API
- Registrieren mithilfe einer [Swagger 2.0-API-Definition](powerapps-register-existing-api-from-api-definition.md)

In diesem Artikel wird erläutert, wie Sie **eine in Ihrer App Service-Umgebung gehostete API registrieren**.

#### Voraussetzungen für den Einstieg

- Anmelden für [PowerApps Enterprise](powerapps-get-started-azure-portal.md)
- Erstellen einer [App Service-Umgebung](powerapps-get-started-azure-portal.md)


## Entwickeln und Bereitstellen einer API in Ihrer App Service-Umgebung

Das Entwickeln einer API in der App Service-Umgebung geht einfach vonstatten. Sie wählen Ihre bevorzugte Programmiersprache zum Erstellen einer Web-API aus und verwenden dann [Swagger 2.0](http://swagger.io) zum Beschreiben der API-Definition. Beispiele hierfür sind:

- [Erstellen und Bereitstellen einer .NET-API-App in Azure App Service](../app-service-api-dotnet-get-started.md)
- [Erstellen und Bereitstellen einer Java-API-App in Azure App Service](../app-service-api-java-api-app.md)
- [Erstellen und Bereitstellen einer Node.js-API-App in Azure App Service](../app-service-api-nodejs-api-app.md)

Sie haben zudem verschiedene Möglichkeiten, Ihre Web-API in einer APP Service-Umgebung bereitzustellen, z. B. die Bereitstellung über Visual Studio oder die kontinuierliche Bereitstellung über ein Quellcodeverwaltungssystem. Hierfür finden Sie unter [Bereitstellen einer Web-App in Azure App Service](../web-sites-deploy.md) nützliche Informationen.

## Registrieren Ihrer benutzerdefinierten API in der App Service-Umgebung

Nachdem die API in Ihrer APP Service-Umgebung bereitgestellt wurde, führen Sie die folgenden Schritte zum Registrieren der API aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option **PowerApps** und dann **Manage APIs** aus: ![][11]
2. Wählen Sie in „Manage APIs“ die Option **Add** aus: ![][12]  
3. Geben Sie in **Add API** die API-Eigenschaften ein:  

	- Geben Sie unter **Name** einen Namen für die API ein. Beachten Sie, dass der eingegebene Name in die Laufzeit-URL der API eingefügt wird. Geben Sie einen aussagekräftigen und innerhalb Ihrer Organisation eindeutigen Namen ein.	
	- Wählen Sie unter **Source** den Eintrag **Import from APIs hosted in App Service Environment** aus: ![][13]
4. Wählen Sie unter **API hosted in App Service Environment** die API aus, die Sie importieren möchten. In dieser Liste werden alle Web-Apps, API-Apps und mobilen Apps in Ihrer App Service-Umgebung angezeigt, für die jeweils die **apiDefinition.url**-Eigenschaft konfiguriert ist. Für den Import der API wird die Swagger 2.0-API-Definition verwendet, die mit dieser Eigenschaft verfügbar gemacht wird. Stellen Sie sicher, dass diese URL öffentlich zugänglich ist, wenn Sie die API registrieren: ![][14]
5. Wählen Sie **ADD** aus, um diese Schritte abzuschließen.

> [AZURE.TIP]Wenn Sie eine API registrieren, wird diese in Ihrer App Service-Umgebung registriert. Nach der Registrierung in der App Service-Umgebung kann sie von anderen Apps innerhalb der gleichen App Service-Umgebung verwendet werden.

## Zusammenfassung und nächste Schritte
In diesem Thema wurde erläutert, wie Sie eine in Ihrer App Service-Umgebung gehostete API registrieren. In den folgenden Themen und Ressourcen finden Sie weitere Informationen zu PowerApps:

- [Konfigurieren von APIs](../powerapps-configure-apis.md)
- [Hinzufügen einer neuen API](../powerapps-register-from-available-apis.md)

<!--Reference-->
[11]: ./media/powerapps-register-api-hosted-in-app-service/registered-apis-part.png
[12]: ./media/powerapps-register-api-hosted-in-app-service/add-api-button.png
[13]: ./media/powerapps-register-api-hosted-in-app-service/add-api-blade.png
[14]: ./media/powerapps-register-api-hosted-in-app-service/add-api-select-from-ase.png

<!---HONumber=AcomDC_1203_2015-->