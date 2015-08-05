
<properties 
   pageTitle="Versionshinweise zu Azure SDK für .NET 2.7" 
   description="Versionshinweise zu Azure SDK für .NET 2.7" 
   services="app-service/web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="07/20/2015"
   ms.author="juliako"/>


# Versionshinweise zu Azure SDK für .NET 2.7

Dieses Dokument enthält die Versionshinweise für das Azure SDK für .NET 2.7.

Azure SDK 2.7 wird nur in Visual Studio 2015 und Visual Studio 2013 unterstützt. [Azure SDK 2.6](http://azure.microsoft.com/downloads/) ist das letzte unterstützte SDK für Visual Studio 2012.

Ausführliche Informationen zu dieser Version finden Sie im [Ankündigungsbeitrag zu Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) (in englischer Sprache).

##Verbesserungen bei der Anmeldung für Visual Studio 2015

Azure SDK 2.7 für Visual Studio 2015 unterstützt die neuen Identitätsverwaltungsfunktionen in Visual Studio 2015. Dies umfasst die Unterstützung für Konten, bei denen der Zugriff auf Azure über die rollenbasierte Access Control, Cloud-Lösungsanbieter und DreamSpark erfolgt, und für andere Konto- und Abonnementtypen.

Diese Verbesserungen bei der Anmeldung sind nur in Visual Studio 2015 verfügbar. Die Unterstützung für Visual Studio 2013 ist in einem zukünftigen Update enthalten.


##Mobile SDK

Aktualisierte **Mobile Apps**-Vorlagen entsprechend dem neuesten [NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) und Installationsvorgang.

##Service Bus 

Allgemeine Fehlerbehebungen und Verbesserungen. Ausführliche Informationen zu Updates und Funktionen finden Sie in den Versionshinweisen zum neuesten [Service Bus NuGet-Paket](http://www.nuget.org/packages/WindowsAzure.ServiceBus/) (in englischer Sprache).

##HDInsight-Tools 

In dieser Version wurden die folgenden Aktualisierungen vorgenommen. Diese Aktualisierungen befinden sich in der Vorschauphase. Weitere Informationen finden Sie in [diesem Blog](http://go.microsoft.com/fwlink/?LinkId=619108).

- Hive-Diagramme für Hive unter Tez-Aufträge
- Vollständige IntelliSense-Unterstützung für Hive-DML
- Unterstützung von Pig-Vorlagen
- Storm-Vorlagen für Azure-Dienste

###Wichtige Änderungen

- Das alte **Storm**-Projekt muss bei Verwendung dieser Version der Tools aktualisiert werden. Weitere Informationen finden Sie in [diesem Blog](http://go.microsoft.com/fwlink/?LinkId=619108).
- Visual Studio Web Express wird nicht mehr unterstützt. Weitere Informationen finden Sie in [diesem Blog](http://go.microsoft.com/fwlink/?LinkId=619108).

##Azure App Service Tools

In dieser Version wurden die folgenden Aktualisierungen an den Erweiterungen für Webtools vorgenommen. Weitere Informationen finden Sie in [diesem Blog](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/).

- Unterstützung für DreamSpark-Konten
- Vollständige Änderung an Azure Tools zur Unterstützung der neuen Azure Resource Management-APIs
- Unterstützung für Azure App Service in [Cloud Explorer](azure-sdk-dot-net-release-notes-2_7.md#cloud_explorer)

###Bekannte Probleme

Knoten des Web-App-Bereitstellungsslots werden unter dem Knoten "Slots" in Server-Explorer nicht angezeigt, und untergeordnete Knoten des Web-App-Bereitstellungsslots werden unter Cloud Explorer nicht geladen. Dieses Problem wurde behoben und für die nächste SDK-Version vorbereitet.


##<a id="cloud_explorer"></a>Cloud Explorer für Visual Studio 2015

Azure SDK 2.7 enthält Cloud Explorer für Visual Studio 2015, mit dem Sie Ihre Azure-Ressourcen anzeigen, deren Eigenschaften überprüfen und wichtige Entwickleraktionen in Visual Studio ausführen können.

In Cloud Explorer wird Folgendes unterstützt:

- Ansichten "Ressourcengruppe" und "Ressourcentyp" von Azure-Ressourcen 
- Suchen nach Ressourcen anhand des Namens (verfügbar in der Ansicht "Ressourcentyp")
- Unterstützung für Abonnements und Ressourcen mit rollenbasierter Access Control (RBAC) 
- Integrierter Bereich "Aktion", in dem entwicklerbezogene spezifische Aktionen für ausgewählte Ressourcen angezeigt werden. Beispiel: Remotedebugger für mit dem Ressourcen-Manager-Stapel erstellte virtuelle Computer anfügen, Diagnosedaten für virtuelle Computer anzeigen usw.
- Integrierter Bereich "Eigenschaften", in dem entwicklerbezogene Eigenschaften angezeigt werden, die häufig beim Entwickeln und Testen benötigt werden. 
- Schnelles Wechseln des Kontos beim Auflisten von Ressourcen (über den Befehl "Einstellungen" in der Symbolleiste) 
- Filtern von Abonnements beim Auflisten von Ressourcen (über den Befehl "Einstellungen" in der Symbolleiste) 
- Deep-Links zum Azure-Vorschauportal für die Verwaltung von Ressourcen und Ressourcengruppen 
 
 
##Azure-Ressourcen-Manager-Tools 

Die Azure-Ressourcen-Manager-Tools wurden so aktualisiert, dass die rollenbasierte Access Control (RBAC) und neue Abonnementtypen unterstützt werden. In diesen Änderungen ist neben dem klassischen Speicher auch die Möglichkeit enthalten, mithilfe neuer Speicherkonten bei der Bereitstellung Artefakte zu speichern.

Wenn Sie ein Azure-Ressourcengruppenprojekt aus einer früheren SDK-Version mit dem SDK 2.7 verwenden, ist bei der Bereitstellung mithilfe eines neuen Speicherkontos anstatt mit einem klassischen Speicher ein neues Bereitstellungsskript erforderlich. Bevor Änderungen an dem Projekt vorgenommen werden, werden Sie aufgefordert, das neue Skript hinzuzufügen. Das alte Skript wird umbenannt, und Sie müssen alle Änderungen an dem neuen Skript manuell vornehmen.
 
 
##Speicher-Explorer-Tools 

- Unterstützung für das Anzeigen von Anfügeblobs. Weitere Informationen finden Sie in [diesem Blogbeitrag](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
- Unterstützung für das Anzeigen von Premium-Speicherkonten über Server-Explorer. In Server-Explorer werden nur Seitenblobs für Premium-Speicherkonten angezeigt, da sie als einziger Typ in Premium-Speicherkonten unterstützt werden.

##Azure Data Factory-Tools für Visual Studio 

Einführung von **Azure Data Factory-Tools** für Visual Studio. Es folgt eine Aufstellung der aktivierten Funktionen. Weitere Informationen finden Sie in [diesem Blog](http://go.microsoft.com/fwlink/?LinkId=617530).

- **Vorlagenbasierte Dokumenterstellung:** Sie können Vorlagen basierend auf Anwendungsfällen, Datenverschiebungsvorlagen oder Datenverarbeitungsvorlagen auswählen, um eine End-to-End-Datenintegrationslösung bereitzustellen, und so schnell Ihre Arbeit mit Daten Factory beginnen. 
- **Integration im Projektmappen-Explorer zum Erstellen und Bereitstellen von Data Factory-Entitäten:** Pipelines und zugehörige Entitäten können als Visual Studio-Projekte erstellt und bereitgestellt werden. 
- **Integration in der Diagrammansicht für visuelle Interaktion während der Erstellung:** Pipelines und DataSets können über die Diagrammansicht visuell erstellt werden. 
- **Integration im Server-Explorer zum Durchsuchen von und Interagieren mit bereits bereitgestellten Entitäten:** Im Server-Explorer können Sie bereits bereitgestellte Data Factorys und zugehörige Entitäten durchsuchen. Sie können eine bereitgestellte Data Factory oder jede gewünschte Entität (Pipeline, verknüpfter Dienst, DataSets) in Ihr Projekt importieren. 
- **JSON-Bearbeitung mit Schemaüberprüfung und IntelliSense:** Mit IntelliSense und der Schemaüberprüfung können Sie JSON-Dokumente von Data Factory-Entitäten effektiv konfigurieren und bearbeiten. 
- **Veröffentlichung in mehreren Umgebungen:** Sie können erstellte Pipelines für die Entwicklungs-, Test- oder Produktionsumgebung veröffentlichen, indem Sie für jede Umgebung gesonderte Konfigurationsdateien erstellen.
- **Unterstützung für die Pig-, Hive- und .Net-basierte Datenverarbeitung:** Pig- und Hive-Skripts werden in Data Factory-Projekten unterstützt. Unterstützung für Verweise auf C#-Projekte zum Verwalten von .Net-Aktivitäten.

##Siehe auch

[Ankündigungsbeitrag zu Azure SDK 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) (in englischer Sprache)

<!---HONumber=July15_HO4-->