<properties 
   pageTitle="Versionshinweise zu Azure SDK für .NET 2.5.1" 
   description="Versionshinweise zu Azure SDK für .NET 2.5.1" 
   services="app-service" 
   documentationCenter=".net,nodejs,java" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="09/30/2015"
   ms.author="juliako"/>


# Versionshinweise zu Azure SDK für .NET 2.5.1

Dieses Dokument enthält die Versionshinweise für das Azure SDK für .NET 2.5.1.

##Versionshinweise zu Azure SDK für .NET 2.5.1

Das Azure SDK für .NET 2.5.1 enthält die nachfolgend beschriebenen neuen Funktionen und Updates.

- Neue Funktionen/Szenarien in Bezug auf **Erweiterungen für Webtools**. 

	- Azure-Websites wurde in Azure App Service umbenannt. Weitere Informationen finden Sie unter [Azure App Service und vorhandene Azure-Dienste](app-service-changes-existing-services.md).
	- Die neue Unterstützung für Azure API-Apps (Vorschau) ermöglicht es Kunden, ASP.NET-Projekte als API-Apps zu veröffentlichen und anschließend über "Hinzufügen > Azure API-App-Client" in C#-Projekten Code zu generieren, der auf der Struktur der bereitgestellten API-App basiert. 
	- Der Knoten "Websites" im Server-Explorer wurde durch den Knoten "Azure App Service" ersetzt, der eine auf Ressourcengruppen basierenden Gruppierung von Azure API-Apps, mobilen Apps und Web-Apps unterstützt.
	- Dank der neuen Unterstützung von Azure Mobile Apps (Vorschau) können Kunden neue Mobile Apps-Projekte erstellen, Mobile Apps-Controller hinzufügen, die Projekte veröffentlichen und ein Remotedebuggen der Anwendungen durchführen.
	- Der Vorgang "Hinzufügen > Azure API-App-Client" unterstützt ab sofort lokale Swagger JSON-Dateien, sodass Web-API-Entwickler Drittanbieter-NuGets wie beispielsweise Swashbuckle für die Swagger-Erstellung verwenden oder eine manuelle Erstellung durchführen können. Auf diese Weise können Cliententwickler mithilfe der Funktionen zur Codeerstellung in C#-Projekten beliebige Swagger-Endpunkte nutzen. 
	- Die Veröffentlichungsdialogfelder für Web-Apps und API-Apps wurden erweitert, um das Konzept der Ressourcengruppierung im Azure-Portal zu unterstützen. Außerdem wird die Auswahl/Erstellung von Azure-Ressourcengruppen und App Service-Plänen im neuen Bereitstellungsdialogfeld für Web-Apps und API-Apps repräsentiert. 
	- Die Azure API-App-Knoten im Server-Explorer umfassen Links zum für API-Apps-Deep-Link im Azure-Portal sowie weitere Funktionen, darunter z. B. Protokollstreaming und Remotedebuggen.

	Bekannte Probleme und aktuelle Einschränkungen im Azure SDK für .NET 2.5.1 werden in [diesem](app-service-release-notes.md#known_issues_2_5_1) Abschnitt weiter unten beschrieben.


- In dieser Version werden neue Funktionen/Szenarien in Bezug auf **HDInsight-Tools** in Visual Studio aktiviert.
	- Lokale Überprüfung von Hive-Skripts. Klicken Sie auf die Schaltfläche zum Überprüfen in der Symbolleiste, um mögliche Fehler in Ihrem Skript zu ermitteln. 
	- Verbessertes Debuggen von Hive-Aufträgen. Sie können ab sofort Hive-Aufträge debuggen, indem Sie auf Yarn-Protokolle in Visual Studio zugreifen. Wenn Ihre Anwendung Leistungsprobleme aufweist, liefert die Prüfung der YARN-Protokolle wertvolle Informationen.
	- (Öffentliche Vorschau) Automatische Vervollständigung von Schlüsselwörtern und IntelliSense-Unterstützung für Hive. Als Hilfestellung beim Schreiben von Hive-Skripts wurden die HDInsight-Tools für Visual Studio um eine automatische Vervollständigung von Schlüsselwörtern und eine IntelliSense-Unterstützung für Hive erweitert.
	- Storm-Unterstützung. Sie können ab sofort HDInsight-Tools für Visual Studio zum Entwickeln von Storm-Topologien/Spouts/Bolts in C# verwenden. Sie können die entwickelte Topologie anschließend an einen Storm-Cluster übermitteln und den Status von Topologie/Bolt/Spout anzeigen. Sie können Systemprotokolle und Kundenprotokolle für eine Problembehandlung Ihrer Storm-Topologien/Bolts/Spouts nutzen. Es ist außerdem möglich, vorhandene JAVA-Objekte in Storm auf HDInsight zu verwenden.
	
	Weitere Informationen finden Sie unter [Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).



##<a id="known_issues_2_5_1"></a>Bekannte Probleme und Einschränkungen im Azure SDK für .NET 2.5.1

- Azure API-Apps wird als Bereitstellungsziel für mobile Apps angezeigt. Bis zu einer späteren Version sollte Web-Apps das einzige Ziel für mobile Apps sein. 
- Die Azure API-App-Bereitstellung kann erfolgreich sein, möglicherweise wird jedoch der Fortschritt im Azure App Service-Aktivitätsfenster zeitweise nicht aktualisiert. Zur Problemumgehung kann der Status der neuen Azure API-App im Azure-Portal überprüft werden. 
- "Datei > Neues Projekt > API-App > F5" führt zu einem HTTP-Fehler, da keine Datei "default/index.hmtl" vorhanden ist. Zur Problemumgehung können Sie manuell nach der URL für "/api/values" suchen. 
- Die Symbole im Server-Explorer werden zeitweise abgeflacht angezeigt. Dieses Problem kann durch einen Neustart von Visual Studio behoben werden. 
- Wenn bei der Bereitstellung einer Web-App oder einer API-App ein Ausnahmefehler ausgegeben wird (beispielsweise durch eine Kontingentüberschreitung oder einen doppelten Azure API-App-Gatewaynamen), zeigen die Fehler unbearbeiteten JSON-Text. 
- Es kommt zeitweise zu Problemen bei der Projekterstellung, wenn Application Insights zum Zeitpunkt der Projekterstellung überprüft wird.
- Gelegentlich fehlen im generierten Azure API-App-Clientcode Namespaces, diese müssen manuell eingefügt werden (oder automatisch über Visual Studio-Cues importiert werden), damit der Code kompiliert werden kann. 
- Mobile App-Projekte sollten in Web-Apps veröffentlicht werden, aber Sie müssen eine Site auswählen, die Sie als mobile App im Azure-Portal erstellt haben, da für Mobile App-Projekte eine Datenbank erforderlich ist. 
- Die Startseite für Mobile Apps verwendet den Begriff "Mobile Service" anstelle von "Mobile Apps". 
- Die Erstellung eines Mobile App-Projekts kann bis zu einer Minute in Anspruch nehmen. 
- Bei der API-App-Bereitstellung gibt die Azure-API (gelegentlich) einen Fehler aus, nach dem die Berechtigungen nicht ordnungsgemäß festgelegt werden konnten, wenngleich die API-App korrekt bereitgestellt wurde und einsatzbereit ist. Sie können die Berechtigungen im Azure-Portal manuell festlegen.
- Application Insights wird für API-App-Vorlagen und Mobile App-Vorlagen nicht unterstützt.
- API-App-Projekte können nicht zusammen mit Cloud Service-Projekten verwendet werden.
- API-App-Projektvorlagen stehen nur in C# zur Verfügung.
- Die API-App-Nutzung über das Kontextmenü "API-App-Client hinzufügen" wird nur in C# unterstützt.

 

<!---HONumber=Oct15_HO1-->