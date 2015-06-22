<properties 
	pageTitle="Erste Schritte mit Azure-Suche NodeJS" 
	description="Schrittweise Anleitung zum Erstellen einer benutzerdefinierten Azure Search-Anwendung mit NodeJS als Programmiersprache." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="03/25/2015" 
	ms.author="heidist"/>

#Erste Schritte mit Azure-Suche NodeJS#

Erfahren Sie, wie Sie eine benutzerdefinierte NodeJS-Suchanwendung erstellen, in der Azure Search für deren Suchfunktionalität verwendet wird. Für das Lernprogramm wird die [Azure-Suchdienst-REST-API](https://msdn.microsoft.com/library/dn798935.aspx) genutzt, um die Objekte und Vorgänge zu erstellen, die in dieser Übung verwendet werden.

Zum Entwickeln und Testen dieses Codes wurden [NodeJS](https://nodejs.org) und NPM, [Sublime Text 3](http://www.sublimetext.com/3) und Windows PowerShell unter Windows 8.1 verwendet.

Damit Sie dieses Beispiel ausführen können, benötigen Sie einen Azure-Suchdienst, bei dem Sie sich im [Azure-Portal](https://portal.azure.com) anmelden können. 

> [AZURE.TIP] Laden Sie den Quellcode für dieses Lernprogramm aus [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198) herunter. 

##Informationen zu den Daten##

In dieser Beispielanwendung werden Daten von [United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) verwendet, die nach dem Bundesstaat Rhode Island gefiltert sind, um das Dataset zu verkleinern. Mit diesen Daten wird eine Suchanwendung erstellt, die Landmarkengebäude wie Krankenhäuser und Schulen sowie geologische Merkmale wie Flüsse, Seen und Gipfel zurückgibt.

In dieser Anwendung wird im **DataIndexer**-Programm der Index mit einem [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx)-Konstrukt erstellt und geladen, in dem das gefilterte USGS-Dataset aus einer öffentlichen Azure SQL-Datenbank abgerufen wird. Die Anmelde- und Verbindungsinformationen für die Onlinedatenquelle werden im Programmcode bereitgestellt. Es ist keine weitere Konfiguration erforderlich.

> [AZURE.NOTE] Wir haben einen Filter auf dieses Dataset angewendet, damit die Grenze von 10.000 Dokumenten für die Preisstufe "Kostenlos" nicht überschritten wird. Wenn Sie die Standardstufe verwenden, gilt diese Grenze nicht. Ausführliche Informationen über die Kapazität für jede Preisstufe finden Sie unter [Grenzen und Einschränkungen](https://msdn.microsoft.com/library/azure/dn798934.aspx).

##Erstellen des Diensts##

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie auf der Sprungleiste auf **Neu** | **Daten + Speicher** | **Search**.
 
     ![][1]

3. Konfigurieren Sie den Dienstnamen, die Preisstufe, die Ressourcengruppe, das Abonnement und den Speicherort. Diese Einstellungen sind erforderlich und können nicht mehr geändert werden, sobald der Dienst bereitgestellt wird.

     ![][2]

	- Der **Dienstname** muss eindeutig und klein geschrieben sein und darf weder mehr als 14 Zeichen noch Leerzeichen enthalten. Dieser Name wird Bestandteil des Endpunkts Ihres Azure-Suchdiensts. Weitere Informationen zu Namenskonventionen finden Sie unter [Benennungsregeln](https://msdn.microsoft.com/library/azure/dn857353.aspx). 
	
	- Die **Preisstufe** bestimmt Kapazität und Abrechnung. Beide Stufen stellen die gleichen Funktionen bereit, dies allerdings auf unterschiedlichen Ressourcenebenen. 
	
		- **Kostenlos** wird in Clustern ausgeführt, die gemeinsam mit anderen Abonnenten genutzt werden. Diese Version bietet ausreichend Kapazität für Lernprogramme und zum Schreiben von Code für Machbarkeitstests, eignet sich jedoch nicht für den Einsatz in Produktionsumgebungen. Eine Bereitstellung eines kostenlosen Diensts beansprucht in der Regel nur wenige Minuten.
		- **Standard** wird auf dedizierten Ressourcen ausgeführt und ist in hohem Maße skalierbar. Zunächst wird ein Standarddienst mit einem Replikat und einer Partition bereitgestellt, Sie können die Kapazität jedoch anpassen, sobald der Dienst erstellt ist. Eine Bereitstellung eines Standarddiensts dauert etwas länger, in der Regel ungefähr 15 Minuten.
	
	- **Ressourcengruppen** sind Container für Dienste und Ressourcen, die einem gemeinsamen Zweck dienen. Wenn Sie eine auf Azure Search, Azure Websites und Azure-BLOB-Speicher basierte Suchanwendung erstellen möchten, können Sie z. B. eine Ressourcengruppe erstellen, die diese Dienste auf den Verwaltungsseiten im Portal gruppiert.
	
	- In **Abonnement** können Sie unter mehreren Abonnements wählen, wenn Sie über mehrere Abonnements verfügen.
	
	- **Speicherort** ist die Region des Rechenzentrums. Derzeit müssen sämtliche Ressourcen im selben Rechenzentrum ausgeführt werden. Verteilen von Ressourcen über mehrere Rechenzentren wird nicht unterstützt.

4. Klicken Sie auf **Erstellen**, um den Dienst bereitzustellen. 

Achten Sie auf Benachrichtigungen auf der Sprungleiste. Es wird eine Benachrichtigung angezeigt, sobald der Dienst verwendet werden kann.

<a id="sub-2"></a>
##Suchen nach dem Dienstnamen und dem API-Schlüssel (api-key) Ihres Azure-Suchdiensts

Nachdem der Dienst erstellt wurde, können Sie zum Portal zurückkehren und die URL sowie den `api-key` ermitteln. Für Verbindungen mit Ihrem Suchdienst benötigen Sie sowohl die URL als auch einen `api-key`, um den Aufruf zu authentifizieren. 

1. Klicken Sie auf der Sprungleiste auf **Startseite**, und klicken Sie dann auf den Suchdienst, um das Dashboard für den Dienst zu öffnen. 

2. Auf dem Dashboard des Diensts sehen Sie Kacheln für wichtige Informationen sowie das Schlüsselsymbol für den Zugriff auf die Administratorschlüssel.

  	![][3]

3. Kopieren Sie die Dienst-URL, einen Administratorschlüssel und einen Abfrageschlüssel. Sie benötigen alle drei Angaben später, wenn Sie sie zur Datei "config.js" hinzufügen.

##Herunterladen der Beispieldateien

Gehen Sie wie folgt vor, um das Beispiel herunterzuladen.

1. Wechseln Sie zu [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198).
2. Klicken Sie auf **Download ZIP**, speichern Sie die ZIP-Datei, und extrahieren Sie dann alle Dateien, die sie enthält.

Alle späteren Dateiänderungen und Ausführungsanweisungen werden für Dateien in diesem Ordner vorgenommen.

Alternativ können Sie, wenn GIT in Ihrer Pfadanweisung enthalten ist, ein PowerShell-Fenster öffnen und `git clone https://github.com/EvanBoyle/AzureSearchNodeJSIndexerDemo.git` eingeben. 

##Aktualisieren von "config.js" mit Ihrer Suchdienst-URL und Ihrem API-Schlüssel

Verwenden Sie die URL und die API-Schlüssel (api-key), die Sie früher kopiert haben, um in der Konfigurationsdatei die URL, den Administratorschlüssel (admin-key) und den Abfrageschlüssel (query-key) anzugeben.

Administratorschlüssel gewähren uneingeschränkte Kontrolle über Dienstvorgänge, einschließlich Erstellen oder Löschen eines Indexes und Laden von Dokumenten. Im Gegensatz dazu werden Abfrageschlüssel für schreibgeschützte Vorgänge verwendet, die üblicherweise von Clientanwendungen verwendet werden, die Verbindungen mit Azure Search herstellen.

In diesem Beispiel wird der Abfrageschlüssel (query-key) eingefügt, damit die bestmögliche Methode für die Verwendung des Abfrageschlüssels in Clientanwendungen genutzt wird.

Der folgende Screenshot zeigt **config.js** in einem Texteditor geöffnet, wobei die entsprechenden Einträge gekennzeichnet sind, sodass Sie sehen können, an welchen Stellen die Datei mit den Werten aktualisiert werden muss, die für Ihren Suchdienst gültig sind.

![][5]


##Hosten eine Laufzeitumgebung für das Beispiel

Das Beispiel erfordert einen HTTP-Server, die Sie mithilfe von npm global installieren können. 

Verwenden Sie ein PowerShell-Fenster für die folgenden Befehle.

1. Navigieren Sie zu dem Ordner, der die Datei **package.json** enthält.
2. Geben Sie `npm install` ein.
2. Geben Sie `npm install -g http-server` ein.

##Erstellen des Indexes und Ausführen der Anwendung

1. Geben Sie `npm run indexDocuments` ein. 
2. Geben Sie `npm run build` ein.
3. Geben Sie `npm run start_server` ein.
4. Wechseln Sie in Ihrem Browser zu `http://localhost:8080/index.html`

##Suchen nach USGS-Daten##

Der USGS-Datenbestand enthält Datensätze, die für den Bundesstaat Rhode Island gelten. Wenn Sie bei leerem Suchfeld auf **Search** klicken, erhalten Sie die obersten 50 Einträge. Dies ist das Standardverhalten. 

Durch Eingabe eines Suchbegriffs wird die Suchmaschine veranlasst, einen gezielteren Suchvorgang auszuführen. Geben Sie einen regionalen Namen ein. "Roger Williams" war der erste Gouverneur von Rhode Island. Zahlreiche Parks, Gebäude und Schulen sind nach ihm benannt.

![][9]

Sie könnten auch jeden der folgenden Begriffe ausprobieren:

- Pawtucket
- Pembroke
- goose +cape


##Nächste Schritte##

Dies ist das erste Azure Search-Lernprogramm, das auf NodeJS und dem USGS-Dataset basiert. Im Laufe der Zeit wird dieses Lernprogramm erweitert, um weitere Suchfunktionen zu veranschaulichen, die Sie möglicherweise in Ihren benutzerdefinierten Lösungen verwenden möchten.

Wenn Sie bereits einige Kenntnisse zu Azure Search haben, können Sie dieses Beispiel als Ausgangspunkt für das Ausprobieren von Vorschlagsprogrammen (Wortvorschlags- oder AutoVervollständigen-Abfragen), Filtern und Facettennavigation verwenden. Sie können auch die Seite für die Suchergebnisse verbessern, indem Sie Zählungen hinzufügen und Dokumente stapelweise verarbeiten, sodass Benutzer durch die Ergebnisse blättern können.

Azure Search ist Neuland für Sie? Wir empfehlen, dass Sie weitere Lernprogramme durcharbeiten, damit Sie ein Verständnis dafür entwickeln, was Sie erstellen können. Besuchen Sie unsere [Dokumentationsseite](http://azure.microsoft.com/documentation/services/search/), um weitere Ressourcen zu finden. Sie können auch die Links in unserer [Liste der Videos und Lernprogramme](https://msdn.microsoft.com/library/azure/dn798933.aspx) verwenden, um auf weitere Informationen zuzugreifen.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png

<!--HONumber=54--> 