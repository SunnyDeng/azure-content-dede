<properties 
	pageTitle="Erste Schritte mit Azure Search in Java" 
	description="Schrittweise Anleitung zum Erstellen einer benutzerdefinierten Azure Search-Anwendung mit Java als Programmiersprache." 
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
	ms.date="03/30/2015" 
	ms.author="heidist"/>

#Erste Schritte mit Azure Search in Java#

Erfahren Sie, wie Sie eine benutzerdefinierte Java-Suchanwendung erstellen, in der Azure Search für deren Suchfunktionalität verwendet wird. Für das Lernprogramm wird die [Azure-Suchdienst-REST-API](https://msdn.microsoft.com/library/dn798935.aspx) genutzt, um die Objekte und Vorgänge zu erstellen, die in dieser Übung verwendet werden.

Die folgende Software wurde verwendet, um dieses Beispiel zu erstellen und zu testen:

- [Eclipse IDE for Java EE Developers](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Laden Sie die EE-Version herunter. Für einen der Überprüfungsschritte ist ein Feature erforderlich, das nur in dieser Version vorhanden ist.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

Damit Sie dieses Beispiel ausführen können, benötigen Sie einen Azure-Suchdienst, bei dem Sie sich im [Azure-Verwaltungsortal](https://portal.azure.com) anmelden können. 

> [AZURE.TIP] Laden Sie den Quellcode für dieses Lernprogramm aus [Azure Search Java demo](http://go.microsoft.com/fwlink/p/?LinkId=530197) auf Github herunter. 

## Informationen zu den Daten

In dieser Beispielanwendung werden Daten von [United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) verwendet, die nach dem Bundesstaat Rhode Island gefiltert sind, um das Dataset zu verkleinern. Mit diesen Daten wird eine Suchanwendung erstellt, die Landmarkengebäude wie Krankenhäuser und Schulen sowie geologische Merkmale wie Flüsse, Seen und Gipfel zurückgibt.

In dieser Anwendung wird im **SearchServlet.java**-Programm der Index mit einem [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx)-Konstrukt erstellt und geladen, in dem das gefilterte USGS-Dataset aus einer öffentlichen Azure SQL-Datenbank abgerufen wird. Vordefinierte Anmelde- und Verbindungsinformationen für die Onlinedatenquelle werden im Programmcode bereitgestellt. Hinsichtlich des Datenzugriffs ist keine weitere Konfiguration erforderlich.

> [AZURE.NOTE] Wir haben einen Filter auf dieses Dataset angewendet, damit die Grenze von 10.000 Dokumenten für die Preisstufe "Kostenlos" nicht überschritten wird. Wenn Sie die Standardstufe verwenden, gilt diese Grenze nicht, und Sie können den Code so ändern, dass ein größerer Dataset verwendet wird. Ausführliche Informationen über die Kapazität für jede Preisstufe finden Sie unter [Grenzen und Einschränkungen](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## Informationen zu den Programmdateien

In der folgenden Liste sind die Dateien beshcrieben, die für dieses Beispiel relevant sind.

- Search.jsp: Stellt die Benutzeroberfläche bereit.
- SearchServlet.java: Stellt Methoden bereit (vergleichbar mit einem Controller in MVC).
- SearchServiceClient.java: Verarbeitet HTTP-Anforderungen.
- SearchServiceHelper.java: Eine Hilfsklasse, die statische Methoden bereitstellt.
- Document.java: Stellt das Datenmodell bereit.
- config.properties: Legt die Suchdienst-URL und den API-Schlüssel (api-key) fest.
- Pom.xml: Eine Maven-Abhängigkeit.


## Erstellen des Diensts

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
## Suchen nach dem Dienstnamen und dem API-Schlüssel (api-key) Ihres Azure-Suchdiensts

Nachdem der Dienst erstellt wurde, können Sie zum Portal zurückkehren und die URL sowie den `api-key` ermitteln. Für Verbindungen mit Ihrem Suchdienst benötigen Sie sowohl die URL als auch einen `api-key`, um den Aufruf zu authentifizieren. 

1. Klicken Sie auf der Sprungleiste auf **Startseite**, und klicken Sie dann auf den Suchdienst, um das Dashboard für den Dienst zu öffnen. 

2. Auf dem Dashboard des Diensts sehen Sie Kacheln für wichtige Informationen sowie das Schlüsselsymbol für den Zugriff auf die Administratorschlüssel.

  	![][3]

3. Kopieren Sie die Dienst-URL und einen Administratorschlüssel. Diese benötigen Sie später, wenn Sie sie zur Datei **config.properties** hinzufügen.

## Herunterladen der Beispieldateien

1. Wechseln Sie zu [AzureSearchJavaDemo](http://go.microsoft.com/fwlink/p/?LinkId=530197) auf Github.

2. Klicken Sie auf **Download ZIP**, speichern Sie die ZIP-Datei auf einem Datenträger, und extrahieren Sie dann alle Dateien, die sie enthält. Es empfiehlt sich, dass Sie die Dateien in Ihren Java-Arbeitsbereich extrahieren, damit es später einfacher wird, das Projekt zu finden.

3. Die Beispieldateien sind schreibgeschützt. Klicken Sie mit der rechten Maustaste auf den Ordner, wählen Sie "Eigenschaften" aus, und deaktivieren Sie das Attribut "Schreibgeschützt".

Alle späteren Dateiänderungen und Ausführungsanweisungen werden für Dateien in diesem Ordner vorgenommen.  

## Importieren des Projekts

1. Wählen Sie in Eclipse **File** | **Import** | **General** | **Existing Projects into Workspace** aus.

    ![][4]

2. Navigieren Sie in **Select root directory** zu dem Ordner, der die Beispieldateien enthält. Wählen Sie den Ordner aus, der den Projektordner (.project) enthält. Das Projekt sollte in der Liste **Projects** als ausgewähltes Element angezeigt werden.

    ![][12]

3. Klicken Sie auf **Finish**.

4. Verwenden Sie **Project Explorer**, um die Dateien anzuzeigen und zu bearbeiten. Ist dieses Tool noch nicht geöffnet, klicken Sie auf **Window** | **Show View** | **Project Explorer**, oder verwenden Sie die Tastenkombination, um es zu öffnen.

## Konfigurieren der Dienst-URL und des API-Schlüssels

1. Doppelklicken Sie in **Project Explorer** auf **config.properties**, damit Sie die Konfigurationseinstellungen bearbeiten können, die den Servernamen und den API-Schlüssel (api-key) enthalten. 
 
2. Sehen Sie sich die früheren Schritte in diesem Artikel an, in denen Sie nach der Dienst-URL und dem API-Schlüssel im [Azure-Portal](https://portal.azure.com) gesucht haben, um die Werte zu ermitteln, die Sie nun in **config.properties** eingeben.

3. Ersetzen Sie in **config.properties** den Platzhalter "[ServiceAPIKey]" durch den API-Schüssel Ihres Diensts. Ersetzen Sie dann in derselben Datei den Platzhalter "[ServiceName]" durch den Dienstnamen (die erste Komponente der URL "http://dienstname.search.windows.net").

	![][5]

## Konfigurieren des Projekts, des Builds und der Laufzeitumgebung

1. Klicken Sie in Eclipse in "Project Explorer" mit der rechten Maustaste auf das Projekt | **Properties** | **Project Facets**.

2. Wählen Sie **Dynamic Web Module**, **Java** und **JavaScript** aus.

    ![][6]

3. Klicken Sie auf **Apply**.

4. Wählen Sie **Window** | **Preferences** | **Server** | **Runtime Environments** | **Add...** aus.

5. Erweitern Sie "Apache", und wählen Sie die Version des Apache Tomcat-Servers aus, den Sie zuvor installiert haben. Im vorliegenden Fall wurde Version 8 installiert.

	![][7]

6. Geben Sie auf der nächsten Seite das Tomcat-Installationsverzeichnis an. Auf einem Windows-Computer ist dies wahrscheinlich "C:\Programme\Apache Software Foundation\Tomcat *version*.

6. Klicken Sie auf **Finish**.
 
7. Wählen **Window** | **Preferences** | **Java** | **Installed JREs** | **Add** aus.

8. Wählen Sie in **Add JRE** die Option **Standard VM** aus.

10. Klicken Sie auf **Next**.
 
11. Klicken Sie in "JRE Definition" in "JRE home" auf **Directory**.

12. Navigieren Sie zu **Program Files** | **Java**, und wählen Sie das JDK aus, das Sie zuvor installiert haben. Sie müssen das JDK als Java-Laufzeitumgebung (JRE) auswählen.

13. Wählen Sie in "Installed JREs" die Option **JDK** aus. Ihre Einstellungen sollten weitgehend so aussehen wie auf dem folgenden Screenshot.

    ![][9]

14. Wählen Sie optional **Window** | **Web Browser** | **Internet Explorer** aus, um die Anwendung in einem externen Browserfenster zu öffnen. Das Verwenden eines externen Browsers gibt Ihnen einen besseren Eindruck von der Webanwendung.

    ![][8]

Sie haben nun alle Konfigurationsaufgaben abgeschlossen. Im nächsten Schritt werden Sie das Projekt erstellen und ausführen.

## Erstellen des Projekts
 
1. Klicken Sie in "Project Explorer" mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Run As** | **Maven build...** aus, um das Projekt zu konfigurieren.

    ![][10]

8. Geben Sie in "Edit Configuration" in "Goals" den Text "clean install" ein, und klicken Sie dann auf **Run**.
 
Statusmeldungen werden an das Konsolenfenster ausgegeben. Sie sollten BUILD SUCCESS sehen, wodurch angegeben wird, dass beim Erstellen des Projekts keine Fehler aufgetreten sind.

## Ausführen der App

In diesem letzten Schritt führen Sie die Anwendung in einer lokalen Serverlaufzeitumgebung aus. 

Sofern Sie bisher noch keine Serverlaufzeitumgebung in Eclipse angegeben haben, müssen Sie die zuerst tun.

1. Erweitern Sie in "Project Explorer" den Eintrag **WebContent**.

5. Klicken Sie mit der rechten Maustaste auf **Search.jsp** | **Run As** | **Run on Server**. Wählen Sie den Apache Tomcat-Server aus, und klicken Sie dann auf **Run**.

> [AZURE.TIP] Wenn Sie für das Speichern Ihres Projekts einen nicht standardmäßigen Arbeitsbereich verwendet haben, müssen Sie wahrscheinlich, um einen Fehler beim Serverstart zu vermeiden, die Einstellung **Run Configurations** so ändern, dass sie auf den Projektspeicherort verweist. Klicken Sie in "Project Explorer" mit der rechten Maustaste auf **Search.jsp** | **Run As** | **Run Configurations**. Wählen Sie den Apache Tomcat-Server aus. Klicken Sie auf **Arguments**. Klicken Sie auf **Workspace** oder **File System**, um den Ordner festzulegen, der das Projekt enthält.

Wenn Sie die Anwendung ausführen, sollte ein Browserfenster angezeigt werden, in dem ein Suchfeld zur Eingabe von Begriffen bereitgestellt wird. 

Warten Sie etwa eine Minute, bevor Sie auf **Search** klicken, damit der Dienst genügend Zeit hat, den Index zu erstellen und zu laden. Wird der HTTP-Fehler 404 angezeigt, müssen Sie nur ein wenig länger warten, bevor Sie es erneut versuchen.

## Suchen nach USGS-Daten

Der USGS-Datenbestand enthält Datensätze, die für den Bundesstaat Rhode Island gelten. Wenn Sie bei leerem Suchfeld auf **Search** klicken, erhalten Sie die obersten 50 Einträge. Dies ist das Standardverhalten. 

Durch Eingabe eines Suchbegriffs wird die Suchmaschine veranlasst, einen gezielteren Suchvorgang auszuführen. Geben Sie einen regionalen Namen ein. "Roger Williams" war der erste Gouverneur von Rhode Island. Zahlreiche Parks, Gebäude und Schulen sind nach ihm benannt.

![][11]

Sie könnten auch jeden der folgenden Begriffe ausprobieren:

- Pawtucket
- Pembroke
- goose +cape

## Nächste Schritte

Dies ist das erste Azure Search-Lernprogramm, das auf Java und dem USGS-Dataset basiert. Im Laufe der Zeit wird dieses Lernprogramm erweitert, um weitere Suchfunktionen zu veranschaulichen, die Sie möglicherweise in Ihren benutzerdefinierten Lösungen verwenden möchten.

Wenn Sie bereits einige Kenntnisse zu Azure Search haben, können Sie dieses Beispiel als Ausgangspunkt für weiteres Ausprobieren verwenden, etwa Erweitern der [Suchseite](../search-pagination/) oder Implementieren von [Facettennavigation](../search-faceted-navigation/). Sie können auch die Seite für die Suchergebnisse verbessern, indem Sie Zählungen hinzufügen und Dokumente stapelweise verarbeiten, sodass Benutzer durch die Ergebnisse blättern können.

Azure Search ist Neuland für Sie? Wir empfehlen, dass Sie weitere Lernprogramme durcharbeiten, damit Sie ein Verständnis dafür entwickeln, was Sie erstellen können. Besuchen Sie unsere [Dokumentationsseite](http://azure.microsoft.com/documentation/services/search/), um weitere Ressourcen zu finden. Sie können auch die Links in unserer [Liste der Videos und Lernprogramme](https://msdn.microsoft.com/library/azure/dn798933.aspx) verwenden, um auf weitere Informationen zuzugreifen.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-11.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png

<!--HONumber=54-->