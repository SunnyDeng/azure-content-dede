<properties 
	pageTitle="Verwalten und Überwachen von Connectors und API-Apps in App Service | Microsoft Azure" 
	description="Anzeigen der Leistung von Connectors und API-Apps in Azure App Service; Microservices-Architektur" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="mandia"/>

# Verwalten und Überwachen integrierter API-Apps und Connectors

>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview.

Sie haben eine integrierte API-App erstellt. Was nun?

In Azure ist jede API-App eine separate in Azure gehostete Website. Daher können Sie einfach erkennen, wie viele Anforderungen gestellt und wie viele Daten vom Connector verwendet wurden. Sie können Ihre App-API auch sichern, Warnungen erstellen, Tinfoil Security aktivieren sowie Benutzer und Rollen hinzufügen.

In diesem Thema werden einige der Möglichkeiten zum Verwalten Ihrer App-API beschrieben.

Um diese integrierten Features anzuzeigen, öffnen Sie die API-App im [Azure-Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). Wenn sich die API-App in Ihrem Startmenü befindet, wählen Sie sie aus, um das Eigenschaftenfenster zu öffnen. Sie können auch auf **Durchsuchen** klicken, **API-Apps** auswählen und dann Ihre API-App auswählen:

![][browse]

## Anzeigen der von Ihnen eingegebenen Eigenschaften

Wenn Sie die API-App öffnen, stehen mehrere Features und Aufgaben zur Verfügung:

![][settings]

Ihre Möglichkeiten:

- Unter **Einstellungen** werden spezifische Informationen zur API-App gezeigt, einschließlich Details zu Ihrem Abonnement, und die Benutzer mit Zugriff auf Ihre API-App aufgelistet. Sie können auch die Anzahl der Instanzen Ihrer App-API mithilfe des Features "Staffelung" erhöhen oder verringern, das neben anderen Features zur Verfügung steht.
- Klicken Sie zum Steuern der API-App auf die Schaltflächen **Starten** und **Beenden**.
- Wenn Produktupdates an den zugrunde liegenden Dateien erfolgen, die von Ihrer App-API verwendet werden, können Sie auf **Aktualisieren** klicken, um die neuesten Versionen zu erhalten. Wenn z. B. eine Korrektur oder ein Sicherheitsupdate von Microsoft veröffentlicht wird, wird Ihre App-API durch Klicken auf **Aktualisieren** automatisch mit dieser Korrektur aktualisiert. 
- Klicken Sie auf **Plan ändern**, um ein Upgrade oder Downgrade anhand der Datenverwendung Ihrer API-App vorzunehmen. Dieses Feature dient auch zum Anzeigen Ihrer Datennutzung.
- Wenn Sie einen Connector erstellen, der Tabellen aufweist, wie z. B. der SQL-Connector, können Sie optional den Namen einer Tabelle eingeben, mit der eine Verbindung hergestellt werden soll. Ein Schema auf Grundlage der Tabelle wird automatisch erstellt und ist verfügbar, wenn Sie auf **Schemas herunterladen** klicken. Dieses heruntergeladene Schema können Sie dann zum Erstellen einer Transformation oder Zuordnung nutzen. 

## Ändern Sie den Connector oder API-Konfigurationswerte, die Sie eingegeben haben.

Nachdem Sie Connector konfiguriert oder erstellt haben, können Sie die Werte ändern, die Sie eingegeben haben. Wenn Sie z. B. den SQL-Connector konfiguriert haben und den SQL Server-Namen oder Tabellennamen ändern möchten, ist dies auf dem Blatt "API-App" für den Connector möglich.

Dazu müssen diese Schritte ausgeführt werden:

1. Öffnen Sie Ihren Connector oder Ihre API-App. In diesem Fall wird das Blatt "API-App" geöffnet.
2. Klicken Sie unter **Essentials** auf den Link unter der Eigenschaft "Host". Der Link wird als *slackconnector* oder *microsoftsqlconnector123* angezeigt:

	![][apiapphost]

3. Klicken Sie auf dem Blatt "API-App-Host" auf **Einstellungen**. Klicken Sie auf dem Blatt "Einstellungen" auf **Anwendungseinstellungen**. Der Konfigurationswerte sind unter **App-Einstellungen** aufgeführt:
	
	![][hostsettings]

4. Klicken Sie auf die Einstellung, die Sie ändern möchten, geben den neuen Wert ein, und **speichern** Sie Ihre Änderungen.


## Installieren des Hybrid Connection Managers (optional)

![][hcsetup]

Der Hybrid Connection Manager bietet Ihnen die Möglichkeit zur Verbindungsherstellung mit einem lokalen System, beispielsweise SQL Server oder SAP. Diese Hybridkonnektivität verwendet Azure Service Bus zur Verbindungsherstellung und zur Steuerung der Sicherheit zwischen Ihren Azure-Ressourcen und Ihren lokalen Ressourcen.

Siehe [Verwenden des Hybrid Connection Managers in Azure App Service](app-service-logic-hybrid-connection-manager.md).

> [AZURE.NOTE] Der Hybrid Connection Manager wird nur benötigt, wenn Sie auf eine lokale Ressource hinter der Firewall eine Verbindung herstellen wollen. Wenn Sie keine Verbindung mit einem lokalen System herstellen möchten, ist der Hybrid Connection Manager ggf. nicht auf Ihrem Blatt "Connector" aufgeführt.

## Überwachen der Leistung
Leistungsmetriken sind integrierte Features und gehören zu jeder API-App, die Sie erstellen. Diese Metriken sind spezifisch für Ihre in Azure gehostete API-App. Beispielmetriken:

![][monitoring]

Sie können:

- Durch Auswahl von **Anforderungen und Fehler** können Sie unterschiedliche Leistungsmetriken hinzufügen, beispielsweise gängige HTTP-Fehlercodes wie die HTTP-Statuscodes 200, 400 oder 500. Sie können auch Antwortzeiten, die Anzahl von Anforderungen an eine API-App sowie die Menge ein- und ausgehender Daten anzeigen. Auf der Grundlage der Leistungsmetriken können Sie E-Mail-Warnungen erstellen, wenn eine Metrik einen gewählten Schwellenwert überschreitet. 
- Unter **Nutzung** ist es möglich, die **CPU**-Auslastung durch die API-App, das aktuelle **Nutzungskontingent** in MB und die maximale Datennutzung basierend auf Ihrem Tarif anzuzeigen. Über **Geschätzte Ausgaben** können Sie die potenziellen Kosten für die Ausführung Ihrer API-App anzeigen.
- Wählen Sie **Prozesse**, um den Prozess-Explorer zu öffnen. Dieser zeigt Ihre Webinstanzen und ihre Eigenschaften, einschließlich Threadanzahl und Arbeitsspeichernutzung.

Mithilfe dieser Tools können Sie bestimmen, ob der App Service-Plan basierend auf Ihren Geschäftsanforderungen zentral hoch- oder herunterskaliert werden sollte. Diese Features sind in das Portal integriert, sodass keine zusätzliche Tools erforderlich sind.

## Steuern der Sicherheit

Für API-Apps gilt die rollenbasierte Sicherheit. Diese Rollen gelten für die gesamte Azure-Umgebung, einschließlich API-Apps und anderer Azure-Ressourcen. Es gibt die folgenden Rollen:

Rolle | Beschreibung
--- | ---
Besitzer | Verfügen über Vollzugriff auf die Verwaltungsfunktionen und können anderen Benutzern oder Gruppen Zugriff gewähren.
Mitwirkender | Verfügen über Vollzugriff auf die Verwaltungsfunktionen. Können anderen Benutzern oder Gruppen keinen Zugriff gewähren.
Reader | Können alle Ressourcen mit Ausnahme geheimer Schlüssel anzeigen.
Benutzerzugriffsadministrator | Können alle Ressourcen anzeigen, Rollen erstellen/verwalten und Supporttickets erstellen/verwalten.

Siehe [Rollenbasierte Zugriffssteuerung über das Microsoft Azure-Portal](../active-directory/role-based-access-control-configure.md).

Sie können Benutzer problemlos hinzufügen und ihnen bestimmte Rollen für Ihre API-App zuweisen. Das Portal zeigt die Benutzer, die Zugriff haben, und die ihnen zugewiesene Rolle:

![][access]

- Wählen Sie **Benutzer** aus, um einen Benutzer hinzuzufügen, eine Rolle zuzuweisen und einen Benutzer zu entfernen.
- Wählen Sie **Rollen** aus, um alle Benutzer mit einer bestimmten Rolle anzuzeigen, einen Benutzer einer Rolle hinzuzufügen und einen Benutzer aus einer Rolle zu entfernen. 


## Weitere praktische Optionen
- Wählen Sie **API-Definition** aus, um die für Ihre spezifische API automatisch erstellte Swagger-Datei zu öffnen.
- Wählen Sie **Abhängigkeiten** aus, um die von Ihrer API-App benötigten Dateien anzuzeigen. Wenn Sie z. B. den SAP-Connector verwenden, installieren Sie einige zusätzlichen Dateien für den lokalen Hybrid Connection Manager. Diese Abhängigkeiten werden auf dem Blatt "API-App" angezeigt. 

> [AZURE.IMPORTANT] Wenn Sie die Eigenschaften Ihrer API-App öffnen und unter **Essentials** nachsehen, gibt es die Links **Host** und **Gateway**, über die neue Blätter geöffnet werden:
> 
> ![][host]
> 
> Diese Eigenschaften sind spezifisch für die Website, die Ihre API-App hostet. Bei Verwendung einer integrierten API-App oder eines Connectors gelten die meisten dieser Eigenschaften nicht wirklich, und es wird empfohlen, dass Sie diese Eigenschaften nicht aktualisieren. Wenn Sie Ihre eigenen API-App in Visual Studio erstellt und in Ihrem Azure-Abonnement bereitgestellt haben, können Sie die Blätter "Host" und "Gateway" verwenden. Unter [Verwalten von API-Apps](../app-service-api/app-service-api-manage-in-portal.md) finden weitere Informationen zu den Aufgaben, die Sie auf diesen Blättern für Ihre benutzerdefiniert erstellte API-App ausführen können.


>[AZURE.NOTE] Wenn Sie sich zunächst mit Azure-Logik-Apps vertraut machen möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie [Logik-Apps testen](https://tryappservice.azure.com/?appservice=logic). Sie können eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte und keine Verpflichtungen erforderlich.

## Weitere Informationen

[Überwachen von Logik-App](app-service-logic-monitor-your-logic-apps.md)<br/>[Liste der Connectors und API-Apps in Microsoft Azure App Service](app-service-logic-connectors-list.md)<br/>[Rollenbasierte Zugriffssteuerung über das Microsoft Azure-Portal](../active-directory/role-based-access-control-configure.md)<br/>[Verwenden des Hybrid Connection Managers in Azure App Service](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png

<!---HONumber=AcomDC_0224_2016-->