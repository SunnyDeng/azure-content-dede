<properties
	pageTitle="App Service-API-Apps – Änderungen | Microsoft Azure"
	description="Sie erfahren, welche Neuigkeiten es in Bezug auf API-Apps in Azure App Service gibt."
	services="app-service\api"
	documentationCenter=".net"
	authors="mohitsriv"
	manager="wpickett"
	editor="tdykstra"/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/13/2016"
	ms.author="mohisri"/>

# App Service-API-Apps – Änderungen

Auf der Connect()-Veranstaltung im November 2015 wurden für Azure App Service einige Verbesserungen [angekündigt](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/). Zu diesen Verbesserungen gehören grundlegende Änderungen der API-Apps, um eine bessere Ausrichtung auf mobile Apps und Web-Apps zu erzielen, die Konzeptanzahl zu reduzieren und die Bereitstellungs- und Laufzeitleistung zu verbessern. Ab dem 30. November 2015 werden die neuen API-Apps, die Sie mit dem Azure-Verwaltungsportal oder den neuesten Tools erstellen, diese Änderungen widerspiegeln. In diesem Artikel werden diese Änderungen beschrieben, und Sie erfahren auch, wie Sie vorhandene Apps neu bereitstellen, um die Funktionen nutzen zu können.

## Funktionsänderungen
Die wichtigsten Funktionen von API-Apps – Authentifizierung, CORS und API-Metadaten – wurden direkt in App Service verschoben. Dank dieser Änderung sind die Funktionen für Web-Apps, mobile Apps und API-Apps verfügbar. Alle drei App-Arten verfügen im Ressourcen-Manager über den gleichen Ressourcentyp: **Microsoft.Web/sites**. Das API-Apps-Gateway wird für API-Apps nicht mehr benötigt und daher auch nicht mehr angeboten. Dies erleichtert außerdem die Verwendung von Azure API Management, da nur ein API Management-Gateway vorhanden ist.

![API-Apps-Übersicht](./media/app-service-api-whats-changed/api-apps-overview.png)

Ein wichtiges Entwurfsprinzip beim API-Apps-Update ist, dass Sie Ihre API unverändert und in der gewünschten Sprache nutzen können. Wenn Ihre API bereits als Web-App oder mobile App bereitgestellt wurde, müssen Sie Ihre App nicht neu bereitstellen, um die neuen Funktionen zu nutzen. Wenn Sie gerade mit der Vorschauversion von API-Apps arbeiten, können Sie die unten angegebene ausführliche Migrationsanleitung verwenden.

### Authentifizierung
Die vorhandenen fertigen Authentifizierungsfunktionen für API-Apps, Mobile Services/Apps und Web-Apps wurden vereinheitlicht und sind im Verwaltungsportal auf einem zentralen Blatt für die Azure App Service-Authentifizierung verfügbar. Eine Einführung in die Authentifizierungsdienste in App Service finden Sie unter [Erweitern der App Service-Authentifizierung/Autorisierung](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

Für API-Szenarien gibt es einige relevante neue Funktionen:

- **Unterstützung für die direkte Verwendung von Azure Active Directory**, ohne dass per Clientcode das AAD-Token gegen ein Sitzungstoken ausgetauscht werden muss: Ihr Client kann auch nur die AAD-Token im Autorisierungsheader gemäß Bearertokenspezifikation enthalten. Dies bedeutet auch, dass auf Client- oder Serverseite kein App Service-spezifisches SDK erforderlich ist. 
- **Dienst-zu-Dienst oder „interner“ Zugriff**: Wenn Sie über einen Daemonprozess oder einen anderen Client verfügen, der Zugriff auf APIs ohne Schnittstelle benötigt, können Sie ein Token mit einem AAD-Dienstprinzipal anfordern und zur Authentifizierung mit Ihrer Anwendung an App Service übergeben.
- **Verzögerte Autorisierung**: Viele Anwendungen verfügen über unterschiedliche Zugriffsbeschränkungen für unterschiedliche Teile der Anwendung. Es kann sein, dass einige APIs bei Ihnen öffentlich verfügbar sein sollen, während für andere eine Anmeldung erforderlich ist. Für die ursprüngliche Authentifizierungs- bzw. Autorisierungsfunktion galt das Prinzip „ganz oder gar nicht“, sodass die Anmeldung für die gesamte Website erforderlich war. Diese Option ist immer noch vorhanden, aber Sie können alternativ dazu für Ihren Anwendungscode zulassen, dass Zugriffsentscheidungen gefällt werden, nachdem der Benutzer von App Service authentifiziert wurde.
 
Weitere Informationen zu den neuen Authentifizierungsfeatures finden Sie unter [Authentifizierung und Autorisierung für API-Apps in Azure App Service](app-service-api-authentication.md). Informationen zum Migrieren vorhandener API-Apps aus dem vorherigen API-Apps-Modell zum neuen Modell finden Sie weiter unten in diesem Artikel unter [Migrieren von vorhandenen API-Apps](#migrating-existing-api-apps).
 
### CORS
Anstelle einer kommagetrennten **MS\_CrossDomainOrigins**-App-Einstellung enthält das Azure-Verwaltungsportal jetzt ein Blatt zum Konfigurieren von CORS (Cross-Origin Resource Sharing). Alternativ dazu kann die Konfiguration auch mit den Ressourcen-Manager-Tools durchgeführt werden, wie z. B. Azure PowerShell, Befehlszeilenschnittstelle (CLI) oder [Ressourcen-Explorer](https://resources.azure.com/). Legen Sie die **cors**-Eigenschaft für Ihre Ressource **&lt;Websitename&gt;/web** auf den Ressourcentyp **Microsoft.Web/sites/config** fest. Beispiel:

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### API-Metadaten
Das Blatt „API-Definition“ ist jetzt für Web-Apps, mobile Apps und API-Apps verfügbar. Im Verwaltungsportal können Sie entweder eine relative URL oder eine absolute URL angeben, die auf einen Endpunkt zeigt, auf dem eine Swagger 2.0-Darstellung Ihrer API gehostet wird. Alternativ dazu können Sie die Konfiguration auch mit den Ressourcen-Manager-Tools durchführen. Legen Sie die **apiDefinition**-Eigenschaft für Ihre Ressource **&lt;Websitename&gt;/web** auf den Ressourcentyp **Microsoft.Web/sites/config** fest. Beispiel:

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

Zu diesem Zeitpunkt muss der Metadaten-Endpunkt ohne Authentifizierung für viele nachgelagerte Clients (z. B. Visual Studio-REST-API-Clientgenerierung und PowerApps-Flow „API hinzufügen“) öffentlich verfügbar sein, um genutzt werden zu können. Dies bedeutet aber Folgendes: Wenn Sie die App Service-Authentifizierung verwenden und die API-Definition direkt aus Ihrer App verfügbar machen möchten, müssen Sie die oben beschriebene Option für die „Verzögerte Authentifizierung“ verwenden, damit die Route zu Ihren Swagger-Metadaten öffentlich ist.

## Verwaltungsportal
Durch die Auswahl von **Neu > Web und mobil > API-App** im Portal werden API-Apps erstellt, die die in diesem Artikel beschriebenen neuen Funktionen widerspiegeln. Bei Auswahl von **Durchsuchen > API-Apps** werden nur diese neuen API-Apps angezeigt. Wenn Sie zu einer API-App navigieren, weist das Blatt das gleiche Layout und die gleichen Funktionen wie für Web-Apps und mobile Apps auf. Die einzigen Unterschiede sind der Schnellstartinhalt und die Reihenfolge der Einstellungen.

Vorhandene API-Apps (oder aus Logik-Apps erstellte Marketplace-API-Apps) mit den vorherigen Vorschaufunktionen werden im Logik-Apps-Designer weiterhin angezeigt. Beim Durchsuchen werden alle Ressourcen einer Ressourcengruppe angezeigt.

## Visual Studio

Die meisten Web-Apps-Tools funktionieren mit neuen API-Apps, da sie den gleichen zugrunde liegenden Ressourcentyp **Microsoft.Web/sites** aufweisen. Die Azure Visual Studio-Tools sollten dagegen auf Version 2.8.1 oder höher aktualisiert werden, da damit mehrere Funktionen verfügbar gemacht werden, die speziell für APIs gelten. Laden Sie das SDK über die [Seite mit den Azure-Downloads](https://azure.microsoft.com/downloads/) herunter.

Aufgrund der Rationalisierung der App Service-Typen erfolgt auch die Veröffentlichung einheitlich unter **Veröffentlichen > Microsoft Azure App Service**:

![API-Apps veröffentlichen](./media/app-service-api-whats-changed/api-apps-publish.png)

Weitere Informationen zum SDK 2.8.1 erhalten Sie im [Blogbeitrag](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/) mit der Ankündigung.

Alternativ dazu können Sie das Veröffentlichungsprofil manuell aus dem Verwaltungsportal importieren, um die Veröffentlichung zu ermöglichen. Für Cloud-Explorer, die Codegenerierung und die API-App-Auswahl/-Erstellung ist aber SDK 2.8.1 oder höher erforderlich.

## Migrieren von vorhandenen API-Apps
Wenn Ihre benutzerdefinierte API mit der vorherigen Vorschauversion von API-Apps bereitgestellt wurde, bitten wir Sie, bis zum 31. Dezember 2015 zum neuen Modell für API-Apps zu migrieren. Da sowohl das neue als auch das alte Modell auf Web-APIs basiert, die in App Service gehostet werden, kann der Großteil des vorhandenen Codes wiederverwendet werden.

### Hosting und erneute Bereitstellung
Die Schritte für die erneute Bereitstellung entsprechen den Schritten zum Bereitstellen einer vorhandenen Web-API für App Service. Schritte:

1. Erstellen Sie eine leere API-App. Hierfür können Sie im Portal die Option „Neu“ > „API-App“, in Visual Studio die Veröffentlichungsfunktion oder die Ressourcen-Manager-Tools verwenden. Legen Sie beim Verwenden der Ressourcen-Manager-Tools oder -Vorlagen den Wert **kind** für den Ressourcentyp **Microsoft.Web/sites** auf **api** fest, damit die Schnellstarts und Einstellungen im Verwaltungsportal auf API-Szenarien ausgerichtet sind.
2. Verbinden Sie Ihr Projekt mit der leeren API-App, und stellen Sie es bereit, indem Sie einen der von App Service unterstützten Bereitstellungsmechanismen verwenden. Weitere Informationen finden Sie in der [Dokumentation zur Azure App Service-Bereitstellung](../app-service-web/web-sites-deploy.md). 
  
### Authentifizierung
Die App Service-Authentifizierungsdienste unterstützen die gleichen Funktionen, die auch für das vorherige API-Apps-Modell verfügbar waren. Verwenden Sie die folgenden Client- und Server-SDKs, wenn Sie Sitzungstoken verwenden und SDKs benötigen:

- Client: [Azure Mobile Client-SDK](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- Server: [Microsoft Azure Mobile App-.NET-Authentifizierungserweiterung](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/) 

Falls Sie stattdessen die App Service-Alpha-SDKs verwendet haben, sollten Sie den Hinweis beachten, dass diese jetzt als veraltet gelten:

- Client: [Microsoft Azure AppService-SDK](http://www.nuget.org/packages/Microsoft.Azure.AppService)
- Server: [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service)

Vor allem bei Azure Active Directory ist aber kein App Service-spezifisches SDK erforderlich, wenn Sie das AAD-Token direkt verwenden.

### Interner Zugriff
Das vorherige API-Apps-Modell enthielt eine integrierte interne Zugriffsebene. Hierfür war die Nutzung des SDK zum Signieren von Anforderungen erforderlich. Wie oben beschrieben können mit dem neuen API-Apps-Modell die AAD-Dienstprinzipale als Alternative für die Dienst-zu-Dienst-Authentifizierung verwendet werden, ohne dass ein App Service-spezifisches SDK benötigt wird. Weitere Informationen finden Sie unter [Dienstprinzipalauthentifizierung für API-Apps in Azure App Service](app-service-api-dotnet-service-principal-auth.md).

### Ermittlung
Das vorherige API-Apps-Modell verfügte über APIs zum Ermitteln anderer API-Apps zur Laufzeit in derselben Ressourcengruppe hinter demselben Gateway. Dies ist besonders nützlich in Architekturen, mit denen Microservice-Muster implementiert werden. Dies wird zwar nicht direkt unterstützt, aber es sind einige Optionen verfügbar:

1. Verwenden Sie die Azure-Ressourcen-Manager-APIs für die Ermittlung.
2. Ordnen Sie Azure API Management vor Ihren per App Service gehosteten APIs an. Azure API Management dient als „Fassade“ und kann auch dann eine stabile externe URL bereitstellen, wenn sich Ihre interne Topologie ändert.
3. Erstellen Sie Ihre eigene Ermittlungs-API-App, und sorgen Sie dafür, dass andere API-Apps beim Starten für die Ermittlungs-App registriert werden.
4. Füllen Sie die App-Einstellungen aller API-Apps (und Clients) zur Bereitstellungszeit mit den Endpunkten der anderen API-Apps. Dies eignet sich für Vorlagenbereitstellungen und wird aufgrund des Umstands ermöglicht, dass Sie mit API-Apps jetzt die Kontrolle über die URL haben.

## Nächste Schritte

Weitere Informationen finden Sie in den Artikeln im [Abschnitt zur API-Apps-Dokumentation](https://azure.microsoft.com/documentation/services/app-service/api/). Sie wurden aktualisiert und für das neue Modell für API-Apps angepasst. Darüber hinaus haben Sie in den Foren Zugriff auf weitere Details und Anleitungen zur Migration:

- [MSDN-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureAPIApps)
- [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-api-apps)

<!---HONumber=AcomDC_0316_2016-->