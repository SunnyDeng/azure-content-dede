<properties
	pageTitle="Hinzufügen oder Erstellen einer neuen API und Einrichten von Berechtigungen für Benutzer in PowerApps | Microsoft Azure"
	description="Hinzufügen, Erstellen und Konfigurieren einer neuen API, einer Verbindung oder eines Verbindungsprofils und Einrichten von Berechtigungen und Rechten mit Benutzerzugriff im Azure-Portal"
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
   ms.date="03/02/2016"
   ms.author="litran"/>


# Hinzufügen einer neuen API, Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer

APIs sind innerhalb einer [App Service-Umgebung](powerapps-get-started-azure-portal.md) vorhanden. APIs können aus den für PowerApps verfügbaren APIs, aus in Ihrer App Service-Umgebung gehosteten API-Apps oder mit Swagger 2.0 erstellt werden. Es sind viele vorgefertigte APIs verfügbar, die Ihrem PowerApps-Dienst problemlos hinzugefügt werden können. Sie können zudem Ihre eigene API im JSON- oder Swagger 2.0-Format hochladen.

In diesem Thema:

- Schritte zum Hinzufügen einer API zu PowerApps und zum Einrichten der Berechtigungen für Benutzer in Ihrem Unternehmen zum Verwenden der API, einschließlich Ändern der zugehörigen Eigenschaften
- Schritte zum Hinzufügen einer Verbindung zu Ihrer API und zum Einrichten der Berechtigungen für Benutzer in Ihrem Unternehmen zur Verwendung der Verbindung


#### Voraussetzungen für den Einstieg

- Aktivieren von [PowerApps in Ihrem Azure-Abonnement](powerapps-get-started-azure-portal.md)
- Erstellen einer [App Service-Umgebung](powerapps-get-started-azure-portal.md)
- Erstellen einer API mit einer der folgenden Methoden:  
	- Erstellen einer [Microsoft-verwalteten API oder einer IT-verwalteten API](powerapps-register-from-available-apis.md)
	- Erstellen einer in [Ihrer App Service-Umgebung](powerapps-register-api-hosted-in-app-service.md) gehosteten API
	- Erstellen mithilfe einer [Swagger 2.0-API-Definition](powerapps-register-existing-api-from-api-definition.md)


## Einrichten des Zugriffs für Benutzer auf die API
Nachdem die API erstellt und Ihrer App Service-Umgebung hinzugefügt wurde, können Sie nun Benutzern in Ihrem Unternehmen die Berechtigungen zur Verwendung der API erteilen.

1. Wählen Sie in PowerApps die Option **Manage APIs** und dann Ihre API aus. Wenn Sie beispielsweise eine *MS Power BI*-API erstellt haben, wählen Sie sie aus, um das zugehörige Blatt zu öffnen. Wählen Sie **API user access** aus: ![][1]  

2. Wählen Sie **Add** aus, um Benutzer hinzuzufügen, und wählen Sie dann die Rechte aus. Wählen Sie abschließend **Add** aus, um die Änderungen zu speichern. Die Anzahl der Benutzer oder Gruppen im Fenster **API user access** erhöht sich.


## Hinzufügen einer neuen Verbindung mit Ihrer API
Im nächsten Schritt wird die „Verbindung“ mit Ihrer API erstellt. Dabei handelt sich es sich um eine Art Verbindungszeichenfolge. Damit kann die API erfolgreich mit Ihrem „Back-End“-System verbunden werden. Für die öffentliche PowerApps Enterprise-Vorschau kann nur die SQL Server- Verbindung hinzugefügt und konfiguriert werden. Weitere Verbindungen werden noch hinzugefügt.

- [Erstellen der SQL Server-Verbindung](powerapps-create-api-sqlserver.md)

## Einrichten des Zugriffs für Benutzer auf die Verbindung zur Laufzeit
Richten Sie nun für Benutzer in Ihrem Unternehmen die Berechtigungen zur Verwendung der Verbindung ein.

1. Öffnen Sie Ihre API, wählen Sie **Connections** und dann die spezifische Verbindung aus. Daraufhin wird ein neues Blatt geöffnet, auf dem der Name der Verbindung ganz oben aufgeführt ist. 
2. Wählen Sie auf diesem neuen Blatt die Option **Connection user access** aus. Im folgenden Beispiel wird die Verbindung **Hybrid Tunnel** ausgewählt. Das neue Blatt wird geöffnet. Auf diesem Blatt wählen Sie **Connection user access** aus: ![][2]
  
3. Wählen Sie in **Connection user access** die Option **Add** und dann die Berechtigung aus, die Sie festlegen möchten: ![][3]
  
4. Wählen Sie einen Benutzer oder eine Gruppe aus. Klicken Sie zum Speichern der Änderungen auf **Add**.

Nun verfügen die Benutzer über die Berechtigungen für die API und die zugehörige Verbindung und können so diese API ihren in PowerApps erstellten Apps hinzufügen. Dies gilt insbesondere in folgenden Fällen:

- Benutzer können die unter **Available Connections** in PowerApps aufgeführte API sehen.
- Benutzer können die unter **My Connections** in PowerApps aufgeführte Verbindung sehen.


## Löschen einer API
Sie können auch eine API löschen, die Sie zuvor hinzugefügt haben. Wählen Sie in PowerApps die Option **Manage APIs** und dann die entsprechende API aus, und klicken Sie auf **Delete**: ![][4]


## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie folgende Schritte ausgeführt:

- Sie haben eine API hinzugefügt und Benutzern in Ihrem Unternehmen die Rechte zur Verwendung der API zugewiesen. Sie können diese Schritte auch jederzeit zum Verwalten des Zugriffs zur Laufzeit ausführen. Wenn beispielsweise BenutzerA Ihr Unternehmen verlässt, können Sie über das Azure-Portal problemlos die Berechtigungen dieses Benutzers entfernen. Dasselbe gilt, wenn BenutzerB neu in Ihr Unternehmen eintritt.
- Sie haben eine Verbindung hinzugefügt (die einer Verbindungszeichenfolge ähnelt). Mit diesem Schritt kann die in Azure gehostete API mit Ihrem System verbunden werden, z. B. mit einem lokalen SQL Server. Sie haben zudem für Benutzer in Ihrem Unternehmen die Berechtigungen zur Verwendung der Verbindung eingerichtet. 
- Abhängig von der Aufgabe haben Sie mit unterschiedlichen Blättern gearbeitet. Um eine Verbindung hinzuzufügen, öffnen Sie die API und verwenden das entsprechende Blatt. Um den Zugriff für Benutzer einzurichten, öffnen Sie die API bzw. die Verbindung, je nachdem, ob Sie den Zugriff für die API oder die Verbindung erteilen. 
- Sie können ferner alle APIs löschen, die Sie in Ihrer App Service-Umgebung erstellt haben.

Als Nächstes können Sie [Ihre PowerApps verwalten und überwachen](powerapps-manage-monitor-usage.md).

[1]: ./media/powerapps-manage-api-connection-user-access/apiuseraccess.png
[2]: ./media/powerapps-manage-api-connection-user-access/connectionuseraccess.png
[3]: ./media/powerapps-manage-api-connection-user-access/selectpermission.png
[4]: ./media/powerapps-manage-api-connection-user-access/deleteapi.png

<!---HONumber=AcomDC_0309_2016-->