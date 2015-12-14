<properties
	pageTitle="Erstellen und Konfigurieren von Microsoft-verwalteten und IT-verwalteten APIs in PowerApps Enterprise | Microsoft Azure"
	description="Informationen zu den verfügbaren APIs in PowerApps sowie dazu, wie Sie diese in Azure-Portal registrieren"
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
   ms.date="11/29/2015"
   ms.author="guayan"/>

# Registrieren einer Microsoft-verwalteten API oder einer IT-verwalteten API
Es gibt **Microsoft-verwaltete** und **IT-verwaltete** APIs. Wenn Sie PowerApps Enterprise aktivieren, stehen Ihnen die Microsoft-verwalteten APIs automatisch zur Verfügung. Der Arbeitsspeicher, die Konnektivität, die Vertrauenswürdigkeit und weitere Elemente werden ebenfalls automatisch für Sie verwaltet. Ihr nächster Schritt besteht darin, bestimmte Benutzereinstellungen einzugeben, etwa ein Twitter-Konto und -Kennwort.

Mit den IT-verwalteten APIs können Sie alles steuern und überwachen, so auch den Arbeitsspeicher, die Konnektivität, die Vertrauenswürdigkeit und weitere Elemente. Die IT-verwalteten APIs umfassen auch die APIs, die Verbindungen mit einem lokalen System wie SQL Server und SharePoint Server herstellen können.

Damit Sie **Microsoft-verwaltete** oder **IT-verwaltete** APIs verwenden können, müssen Sie die APIs im Azure-Portal „registrieren“. Sobald die APIs registriert sind, können Sie diese in Ihren Apps verwenden. Folgende Optionen sind verfügbar:

- Registrieren einer Microsoft-verwalteten API oder einer IT-verwalteten API
- Registrieren einer in [Ihrer App Service-Umgebung](powerapps-register-api-hosted-in-app-service.md) gehosteten API
- Registrieren mithilfe einer [Swagger 2.0-API-Definition](powerapps-register-existing-api-from-api-definition.md)

Dieser Artikel konzentriert sich auf **Registrieren von Microsoft-verwalteten APIs und Registrieren der IT-verwalteten APIs**.

#### Voraussetzungen für den Einstieg

- Anmelden für [PowerApps Enterprise](powerapps-get-started-azure-portal.md)
- Erstellen einer [App Service-Umgebung](powerapps-get-started-azure-portal.md)


## Anzeigen der verfügbaren Microsoft-verwalteten APIs
Die **Microsoft-verwalteten** APIs werden mit PowerApps Enterprise bereitgestellt werden und auch von Microsoft gehostet. In vielen Fällen sind die Microsoft-verwalteten APIs ideal für Ihre Apps. Wenn Ihre App beispielsweise einen Tweet sendet, eine Datei auf OneDrive hochlädt oder Daten aus einer Excel-Datei anzeigt, dann sind diese Microsoft-verwalteten APIs eine gute Wahl.

Einige zusätzliche Vorteile sind:

- Sie erhalten alle Microsoft-verwalteten APIs, die für Sie verfügbar sind, um Ihre eigene Instanz zu registrieren. 
- Die Ressourcen, einschließlich Netzwerk, Arbeitsspeicher oder Sicherheitskonfigurationen, werden automatisch überwacht. Wenn Sie z. B. mehr Arbeitsspeicher benötigen, um Excel-Daten in Ihrer App anzuzeigen, wird automatisch mehr Arbeitsspeicher hinzugefügt. 
- Eine Vertrauensstellung zwischen Ihrer App und der API, etwa Office und Twitter, wird automatisch erstellt. 


#### Microsoft-verwaltete APIs

API | Beschreibung | Link zu Schritten
--- | --- | ---
![][31] | **Dropbox**<br/><br/> Sie können Elemente abrufen, aktualisieren, löschen usw. | [**Erste Schritte**](powerapps-create-api-dropbox.md)
![][32] | **DynamicsCRM Online**<br/><br/> Sie können Elemente abrufen, aktualisieren, löschen usw. | [**Erste Schritte**](powerapps-create-api-crmonline.md)
![][33] | **Excel**<br/><br/> Sie können Elemente abrufen, aktualisieren, löschen usw. | [**Erste Schritte**](powerapps-create-api-excel.md)
![][34] | **Google Drive**<br/><br/> Sie können Elemente abrufen, aktualisieren, löschen usw. | [**Erste Schritte**](powerapps-create-api-googledrive.md)
![][35] | **Microsoft Translator**<br/><br/>Übersetzt Texte, erkennt Sprachen und vieles mehr. | [**Erste Schritte**](powerapps-create-api-microsofttranslator.md)
![][36] | **Office 365 Outlook**<br/><br/>Verwalten Sie Ihre E-Mails. | [**Erste Schritte**](powerapps-create-api-office365-outlook.md)
![][37] | **Office 365 Users**<br/><br/>Zugriff auf Benutzerprofile, zugehörige Vorgesetzte, unterstellte Mitarbeiter und vieles mehr. | [**Erste Schritte**](powerapps-create-api-office365-users.md)
![][38] | **OneDrive**<br/><br/> Sie können Elemente abrufen, aktualisieren, löschen usw. | [**Erste Schritte**](powerapps-create-api-onedrive.md)
![][39] | **Salesforce**<br/><br/> Sie können Elemente abrufen, aktualisieren, löschen usw. | [**Erste Schritte**](powerapps-create-api-salesforce.md)
![][40] | **SharePoint Online**<br/><br/> Sie können Elemente abrufen, aktualisieren, löschen usw. | [**Erste Schritte**](powerapps-create-api-sharepointonline.md)
![][43] | **Twitter**<br/><br/> Senden Sie Tweets, durchsuchen Sie Tweets, finden Sie Follower und vieles mehr. | [**Erste Schritte**](powerapps-create-api-twitter.md)


## Anzeigen der verfügbaren IT-verwalteten APIs
Die **IT-verwalteten** APIs werden von Ihnen gesteuert und von Ihnen verwaltet. Sie werden nicht in der Microsoft-verwalteten Umgebung ausgeführt. In einigen Fällen können durch Verwenden dieser APIs in Ihrer eigenen IT-verwalteten Umgebung die Anforderungen Ihrer Apps erfüllt werden. Angenommen, in Ihrer App wird die Twitter-API verwendet, und Sie müssen den Twitter-Schlüssel Ihrer Organisation (anstelle des Microsoft-Twitter-Schlüssels) verwenden. In diesem Fall empfiehlt es sich, die Twitter-API als eine IT-verwaltete API zu konfigurieren. In einem weiteren Beispiel wird in Ihrer App die SQL Server-API verwendet, um eine Verbindung mit einer lokalen Datenbank herzustellen. In einer IT-verwalteten Umgebung können Sie ein virtuelles Netzwerk einrichten oder ExpressRoute verwenden, um Verbindungen mit lokalen Komponenten herzustellen. Sie haben die Wahl.

Einige zusätzliche Vorteile sind:

- Die Ressourcen, einschließlich Netzwerk, Arbeitsspeicher oder Sicherheitskonfigurationen, werden von Ihnen überwacht. Wenn Sie beispielsweise mehr Arbeitsspeicher benötigen, um Excel-Daten in Ihrer App anzuzeigen, steuern Sie, wie viel Arbeitsspeicher in Ihrer Umgebung hinzugefügt werden soll. 
- Sie richten Sie die Vertrauensstellung zwischen Ihren Apps und der API ein und kontrollieren die Sicherheit zwischen den Apps und der API. Beispielsweise bestimmen Sie, ob die Office 365-API Microsoft-verwaltet sein kann (automatische Vertrauensstellung), oder ob die Office 365-API in Ihrer Umgebung (erstellen Sie Ihre eigene Vertrauensstellung) verwendet werden soll. 
- **Alle** Microsoft-verwalteten APIs können auch IT-verwaltete APIs sein. Dies ist z. B. möglich, wenn Sie eine eigene Instanz von Office 365 erstellen und vollständige Kontrolle über diese Instanz haben. Sie können dann Ihre IT-verwaltete Office365-API und die Microsoft-verwaltete Office365-API in derselben Umgebung verwenden. Dies hängt tatsächlich von den Anforderungen Ihrer App ab.
- Wenn Sie Verbindungen mit lokalen Systemen herstellen oder die Bing-Suche-API verwenden, steuern Sie Sicherheit, Authentifizierung, Lizenzierung und vieles mehr.


#### IT-verwaltete APIs
> [AZURE.NOTE]Es sei daran erinnert, dass **alle** Microsoft-verwalteten APIs auch IT-verwaltete APIs sein können. Die folgenden APIs sind nur IT-verwaltete APIs. Sie können keine Microsoft-verwalteten APIs sein.

API | Beschreibung | Link zu Schritten
--- | --- | ---
![][30] | **Bing-Suche**<br/><br/>Betten Sie Suchergebnisse ein, fügen Sie Suchfunktionalität hinzu und vieles mehr. | [**Erste Schritte**](powerapps-create-api-bingsearch.md)
![][42] | **SQL Server**<br/><br/>Sie können Elemente abrufen, aktualisieren, löschen usw. | [**Erste Schritte**](powerapps-create-api-sqlserver.md)
![][41] | **SharePoint Server**<br/><br/>Sie können Elemente abrufen, aktualisieren, löschen usw. | [**Erste Schritte**](powerapps-create-api-sharepointserver.md)


#### Wann Sie eigene Instanzen registrieren sollten

Ein Verwenden der fertigen Microsoft-verwalteten APIs ist bequem. Demgegenüber hat ein Registrieren Ihrer eigenen Instanzen als IT-verwaltete APIs viele Vorteile. Im Grundsatz empfiehlt es sich, dass Sie IT-verwaltete APIs erstellen, wenn Folgendes zutrifft:

- Sie wünschen vollständige Verwaltbarkeit der APIs einschließlich Benutzerzugriff, Sicherheit, wenn Verbindungen mit anderen Systemen hergestellt werden, API-Aufrufeinschränkungen, Überwachung, erweiterte Funktionen wie Richtlinien und vieles mehr.
- Sie wünschen Zugriff auf lokale Daten, da die App Service-Umgebung virtuelle Netzwerke unterstützt.
- Sie möchten die APIs für geschäftliche Benutzer einrichten, weil diese die APIs möglicherweise nicht selbst verwenden können.

In der folgenden Tabelle sind die Funktionen von Microsoft-verwalteten und IT-verwalteten APIs verglichen:

| Funktion | Microsoft-verwaltet | IT-verwaltet |
| ---------- | ----------------- | ------------ |
| API-Aufrufeinschränkungen | Von Microsoft definiert | Von Ihnen selbst definiert (über Richtlinien) |
| Ihr eigener Schlüssel beim Verbinden mit SaaS | Nicht unterstützt | Unterstützt |
| API-Benutzerzugriff | Für jeden Benutzer aktiviert | Vollständig verwaltbar auf AAD-Benutzer- und -Gruppenebene |
| API-Überwachung | Nicht unterstützt | Unterstützt |
| API-Richtlinien | Nicht unterstützt | Unterstützt |
| Benutzerzugriff auf Verbindung | Nur anzeigen | Vollständig verwaltbar auf AAD-Benutzer- und -Gruppenebene |
| Verbindungsverwaltung | Nur anzeigen | Vollständig verwaltbar |


## Registrieren einer Microsoft-verwalteten oder IT-verwalteten API

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option **PowerApps** und dann **Manage APIs** aus: ![][17]
2. Wählen Sie in „Manage APIs“ die Option **Add** aus: ![][18]  
3. Geben Sie in **Add API** die API-Eigenschaften ein:  

	- Geben Sie unter **Name** einen Namen für die API ein. Beachten Sie, dass der eingegebene Name in die Laufzeit-URL der API eingefügt wird. Geben Sie einen aussagekräftigen und innerhalb Ihrer Organisation eindeutigen Namen ein.
	- Wählen Sie in **Source** die Option **From available APIs** aus: ![][19]
4. Wählen Sie **API** aus, und wählen Sie dann die API aus, die Sie registrieren möchten: ![][20]
5. Wählen Sie Ihre spezielle API aus, und fügen Sie alle konfigurierbaren Eigenschaften hinzu.
6. Wählen Sie **ADD** aus, um diese Schritte abzuschließen.

> [AZURE.TIP]Wenn Sie eine API registrieren, wird diese in Ihrer App Service-Umgebung registriert. Nach der Registrierung in der App Service-Umgebung kann sie von anderen Apps innerhalb der gleichen App Service-Umgebung verwendet werden.


## Zusammenfassung und nächste Schritte

In diesem Thema haben Sie erfahren, wie Sie eine eigene Instanz der verfügbaren APIs registrieren, die von PowerApps als fertige APIs bereitgestellt werden. In den folgenden Themen und Ressourcen finden Sie weitere Informationen zu PowerApps:

- [Konfigurieren von APIs](powerapps-configure-apis.md)
- [Hinzufügen einer neuen API](powerapps-register-from-available-apis.md)

<!--References-->

[17]: ./media/powerapps-register-from-available-apis/registered-apis-part.png
[18]: ./media/powerapps-register-from-available-apis/add-api-button.png
[19]: ./media/powerapps-register-from-available-apis/add-api-blade.png
[20]: ./media/powerapps-register-from-available-apis/add-api-select-from-marketplace-blade.png
[30]: ./media/powerapps-register-from-available-apis/bingsearch.png
[31]: ./media/powerapps-register-from-available-apis/dropbox.png
[32]: ./media/powerapps-register-from-available-apis/dynamicscrmonline.png
[33]: ./media/powerapps-register-from-available-apis/excel.png
[34]: ./media/powerapps-register-from-available-apis/googledrive.png
[35]: ./media/powerapps-register-from-available-apis/microsofttranslator.png
[36]: ./media/powerapps-register-from-available-apis/office365outlook.png
[37]: ./media/powerapps-register-from-available-apis/office365users.png
[38]: ./media/powerapps-register-from-available-apis/onedrive.png
[39]: ./media/powerapps-register-from-available-apis/salesforce.png
[40]: ./media/powerapps-register-from-available-apis/sharepointonline.png
[41]: ./media/powerapps-register-from-available-apis/sharepointserver.png
[42]: ./media/powerapps-register-from-available-apis/sqlserver.png
[43]: ./media/powerapps-register-from-available-apis/twitter.png

<!---HONumber=AcomDC_1203_2015-->