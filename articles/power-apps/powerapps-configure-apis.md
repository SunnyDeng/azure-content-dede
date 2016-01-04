<properties
	pageTitle="Ändern oder Aktualisieren Ihrer PowerApps-API-Eigenschaften im Azure-Portal | Microsoft Azure"
	description="Hinzufügen eines benutzerdefinierten Symbols, Aktualisieren der XML-Richtlinie oder Aktualisieren der Swagger-Definition Ihrer PowerApps-API"
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

# Aktualisieren einer vorhandenen API und ihrer Eigenschaften

Die API, die Sie in der App Service-Umgebung registrieren, ist im Wesentlichen ein Proxy zu Ihrem Back-End-Dienst. Nachdem Sie die API erstellt haben, möchten Sie möglicherweise deren Eigenschaften ändern. Beispielsweise könnte es sein, dass Sie Folgendes möchten:

- Hinzufügen eines benutzerdefiniertes Symbols für Ihre API
- Ändern, wie das Back-End geschützt wird, das von der API verwendet wird 
- Ändern des Anzeigenamens Ihrer API in einen benutzerfreundlicheren Namen


#### Voraussetzungen für den Einstieg

- Anmelden für [PowerApps Enterprise](powerapps-get-started-azure-portal.md)
- Erstellen einer [App Service-Umgebung](powerapps-get-started-azure-portal.md)
- Registrieren einer [API](powerapps-register-from-available-apis) in Ihrer Umgebung

## Hinzufügen eines benutzerdefinierten Symbols oder eines benutzerfreundlichen Anzeigenamens

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) das Blatt für Ihre API.
2. Wählen Sie **All settings** aus.
3. Wählen Sie in **Settings** die Option **General** aus: ![][11]

In „Genral“ können Sie die folgenden Einstellungen ändern:

Einstellung | Beschreibung
--- | ---
Anzeigename | Dieser Name wird verwendet, wenn die verfügbaren Verbindungen (*Available connections*) in PowerApps aufgelistet werden. Standardmäßig wird der Ressourcenname der API verwendet, der möglicherweise nicht der beste Name für Ihre PowerApps-Benutzer ist. Sie können einen benutzerfreundlichen Anzeigenamen eingeben. Sie könnten beispielsweise den Namen **Neue Kundenbestellungen** oder **Verlauf einer Bestellung** verwenden.  
Icon URL | Sie können ein benutzerdefiniertes Symbol für Ihre API hinzufügen. Dieses Symbol wird verwendet, wenn die verfügbaren Verbindungen (*Available connections*) und *My connections* in PowerApps aufgelistet werden. Standardmäßig wird das folgende Symbol verwendet: <br/><br/>![][12] <br/><br/>Wenn ein benutzerdefiniertes Symbol verwendet wird:<br/><ul><li>Die URL zu dem Symbol muss öffentlich zugänglich sein.</li><li>Sie kann eine PNG- oder eine SVG-Datei sein. Wenn Sie eine PNG-Datei verwendet wird, werden 40 x 40 Pixel bevorzugt.</li></ul>
URL Scheme | Wählen Sie die Schemas aus, die Ihre API unterstützen soll. Sie können **HTTP**, **HTTPS** oder **HTTP und HTTPS** auswählen. Standardmäßig sind HTTP und HTTPS aktiviert. <br/><br/>Die App Service-Umgebung konfiguriert das Schema automatisch anhand der Back-End-Konfiguration. Daher können Sie, wenn Sie irgendetwas anderes konfigurieren müssen, Ihren Back-End-Dienst entwickeln oder ändern. 
Authenticate with backend service | Nachdem Sie Ihren Back-End-Dienst in der App Service-Umgebung konfiguriert haben, sollten Sie ihn schützen, sodass Clients ihn nur über Ihre API aufrufen können. Abhängig davon, wo Ihr Back-End bereitgestellt wird, sind die folgenden Optionen verfügbar:<br/><br/><ul><li><strong>Only accessible via this API</strong>: Diese Option ist nur verfügbar, wenn das Back-End in einer App Service-Umgebung bereitgestellt wird. Wird diese Option ausgewählt, werden alle Hostnamen in Ihrem Back-End deaktiviert. Da der API-Proxy in derselben App Service-Umgebung ausgeführt wird, kann er weiterhin auf Ihren Back-End zugreifen.</li><li><strong>HTTP basic authentication</strong>: Diese Option ist unabhängig davon verfügbar, wo Ihr Back-End bereitgestellt wird. Wird diese Option ausgewählt, geben Sie einen Benutzernamen und ein Kennwort ein. Wenn der Proxy Ihren Back-End aufruft, verwendet er die HTTP-Standardauthentifizierung, um den Benutzernamen und das Kennwort im HTTP-Header „Authorization“ zu übergeben. Schließlich muss Ihr Back-End-Dienst den eingegebenen Benutzernamen und das eingegebene Kennwort bestätigen (authentifizieren).<br/><br/>Weitere Informationen zum Implementieren von HTTP-Standardauthentifizierung in ASP.NET Web API 2 finden Sie unter [Authentication Filters in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).</li></ul>


## Aktualisieren der Swagger-Definition Ihrer API

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) das Blatt für Ihre API.
2. Wählen Sie **All settings** aus.
3. Wählen Sie in **Settings** die Option **API definition** aus: ![][13]

**Swagger 2.0** ist das unterstützte API-Definitionsformat. Die aktuelle API-Definition befindet sich im eingebetteten JSON-Editor. Sie können die Definition direkt bearbeiten oder eine neue JSON-Datei hochladen. Nachdem Sie die Änderungen über **Save** gespeichert haben, werden auf diesem Blatt alle Fehler angezeigt, auch sämtliche Fehler in Bezug auf die API-Definition.

- Weitere Informationen zu Swagger 2.0 finden Sie auf der [offiziellen Swagger-Website](http://swagger.io).
- Weitere Informationen dazu, wie Sie Swagger 2.0 erhalten, wenn Sie Ihre API entwickeln, finden Sie unter:  
	- [Erstellen einer ASP.NET-API-App in Azure App Service](../app-service-dotnet-create-api-app.md)
	- [Erstellen und Bereitstellen einer Java-API-App in Azure App Service](../app-service-api-java-api-app.md)
	- [Erstellen und Bereitstellen einer Node.js-API-App in Azure App Service](../app-service-api-nodejs-api-app.md)
	- [Anpassen von mit Swashbuckle generierten API-Definitionen](../app-service-api-dotnet-swashbuckle-customize.md)
- Weitere Informationen zu bewährten Methoden für die Verwendung von Swagger 2.0 für PowerApps finden Sie unter [Entwickeln von APIs für PowerApps](powerapps-develop-api.md).

## Aktualisieren der XML-Richtlinie Ihrer API

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) das Blatt für Ihre API.
2. Wählen Sie **All settings** aus.
3. Wählen Sie in **Settings** die Option **Policy** aus: ![][14]

Diese Richtlinie ist mit der Richtlinie identisch, die von [Azure API Management](https://azure.microsoft.com/services/api-management) unterstützt wird. Die aktuelle Richtlinie befindet sich im eingebetteten XML-Editor. Sie können die Definition direkt bearbeiten oder eine neue XML-Datei hochladen. Nachdem Sie die Änderungen über **Save** gespeichert haben, werden auf diesem Blatt alle Fehler angezeigt, auch sämtliche Probleme in Bezug auf die API-Definition.

[Richtlinien in Azure API Management](../api-management-howto-policies.md) ist eine gute Ressource für weitere Informationen zum Konfigurieren und Verstehen von Richtlinien.


## Zusammenfassung und nächste Schritte
Nachdem Sie Ihre API erstellt haben, können Sie durch Ausführen der Schritte in diesem Thema die Einstellungen der API ändern und sogar einige Einstellungen anpassen.

In den folgenden Themen und Ressourcen finden Sie weitere Informationen zu PowerApps.

- [Konfigurieren einer API, um eine Verbindung mit einem AAD-geschützten Back-End-System herzustellen](powerapps-configure-apis-aad.md)
- [Entwickeln von APIs für PowerApps](powerapps-develop-api.md)

[11]: ./media/powerapps-configure-apis/api-settings-general.png
[12]: ./media/powerapps-configure-apis/api-default-icon.png
[13]: ./media/powerapps-configure-apis/api-settings-api-definition.png
[14]: ./media/powerapps-configure-apis/api-settings-policy.png

<!---HONumber=AcomDC_1203_2015-->