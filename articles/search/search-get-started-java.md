<properties
	pageTitle="Erste Schritte mit Azure Search in Java"
	description="Exemplarische Vorgehensweise zur Erstellung einer benutzerdefinierten Azure Search-Anwendung in der Programmiersprache Java."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na"
	ms.date="06/24/2015"
	ms.author="heidist"/>

#Erste Schritte mit Azure Search in Java#

Erfahren Sie, wie Sie eine benutzerdefinierte Java-Suchanwendung erstellen, die Azure Search zum Suchen verwendet. Das Lernprogramm nutzt die [REST-API für den Azure Search-Dienst](https://msdn.microsoft.com/library/dn798935.aspx) zum Erstellen der Objekte und Vorgänge, die in dieser Übung verwendet werden.

Zum Erstellen und Testen dieses Beispiels wurde die folgende Software verwendet:

- [Eclipse-IDE für Java EE-Entwickler](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Achten Sie darauf, dass Sie die EE-Version herunterladen. Ein Überprüfungsschritt erfordert eine Funktion, die nur in dieser Version verfügbar ist.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

Um dieses Beispiel auszuführen, benötigen Sie zudem einen Azure Search-Dienst, für den Sie sich im [Azure-Verwaltungsportal](https://portal.azure.com) anmelden können.

> [AZURE.TIP]Laden Sie den Quellcode für dieses Lernprogramm von [Azure Search Java demo](http://go.microsoft.com/fwlink/p/?LinkId=530197) von Github herunter.

## Informationen zu den Daten

In dieser Beispielanwendung werden Daten von [United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) verwendet, die nach dem Bundesstaat Rhode Island gefiltert wurden, um die Größe des Datasets zu verringern. Wir verwenden diese Daten, um eine Suchanwendung zu erstellen, die markante Gebäude, wie Krankenhäuser und Schulen, sowie geologische Merkmale, wie Flüsse, Seen und Gipfel, zurückgibt.

In dieser Anwendung erstellt das Programm **SearchServlet.java** den Index und lädt ihn unter Verwendung eines [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx)-Konstrukts, wobei das gefilterte USGS-DataSet aus einer öffentlichen Azure SQL-Datenbank abgerufen wird. Vordefinierte Anmeldeinformationen und Verbindungsinformationen zur Onlinedatenquelle werden im Programmcode bereitgestellt. Hinsichtlich des Datenzugriffs ist keine weitere Konfiguration erforderlich.

> [AZURE.NOTE]Wir haben einen Filter auf dieses Dataset angewendet, um unter dem Limit des kostenlosen Tarifs von maximal 10.000 Dokumenten zu bleiben. Bei Verwendung des Standardtarifs gilt dieses Limit nicht, und Sie können den Code bearbeiten, um größere Datasets verwenden zu können. Ausführliche Informationen zur Kapazität der einzelnen Tarife finden Sie unter [Limits und Einschränkungen](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## Informationen zu den Programmdateien

Die folgende Liste beschreibt die Dateien, die für dieses Beispiel relevant sind.

- Search.jsp: Stellt die Benutzeroberfläche bereit.
- SearchServlet.java: Stellt Methoden bereit (vergleichbar mit einem Controller in MVC).
- SearchServiceClient.java: Verarbeitet HTTP-Anforderungen.
- SearchServiceHelper.java: Eine Hilfsklasse, die statische Methoden enthält.
- Document.Java: Stellt das Datenmodell bereit.
- config.Properties: Legt die URL und den API-Schlüssel des Search-Diensts fest.
- Pom.XML: Eine Maven-Abhängigkeit.


## Erstellen des Diensts

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie über die seitliche Navigationsleiste zu **Neu** > **Daten + Speicher** > **Search**.

     ![][1]

3. Konfigurieren Sie den Dienstnamen, den Tarif, die Ressourcengruppe, das Abonnement und den Ort. Diese Einstellungen sind erforderlich und können nach der Dienstbereitstellung nicht mehr geändert werden.

     ![][2]

	- Der **Dienstname** muss eindeutig sein und aus weniger als 15 Kleinbuchstaben ohne Leerzeichen bestehen. Dieser Name wird Bestandteil des Endpunkts Ihres Azure Search-Diensts. Weitere Informationen zu den Benennungsregeln finden Sie unter [Benennungskonventionen](https://msdn.microsoft.com/library/azure/dn857353.aspx).

	- Der **Tarif** bestimmt Kapazität und Abrechnung. Beide Tarife bieten die gleichen Funktionen, aber mit unterschiedlichen Ressourcen.

		- **Free** wird auf Clustern ausgeführt, die gemeinsam mit anderen Abonnenten genutzt werden. Dieser Tarif bietet genügend Kapazität zum Ausführen von Lernprogrammen sowie zum Schreiben von Code für Machbarkeitsstudien, eignet sich jedoch nicht für den Einsatz in Produktionsumgebungen. Die Bereitstellung eines kostenlosen Diensts dauert in der Regel nur wenige Minuten.
		- **Standard** wird auf fest zugeordneten Ressourcen ausgeführt und ist flexibel skalierbar. Ein Standarddienst wird zunächst mit einem Replikat und einer Partition bereitgestellt, die Kapazität kann nach der Diensterstellung jedoch angepasst werden. Die Bereitstellung eines Standarddiensts dauert länger (üblicherweise etwa 15 Minuten).

	- **Ressourcengruppen** sind Container für Dienste und Ressourcen, die einem gemeinsamen Zweck dienen. Wenn Sie eine auf Azure Search, Azure Websites und Azure-Blobspeicher basierte Suchanwendung erstellen möchten, können Sie z. B. eine Ressourcengruppe erstellen, die diese Dienste in den Verwaltungsseiten im Portal gruppiert.

	- Unter **Abonnement** stehen ggf. mehrere Abonnements zur Auswahl, sofern Sie über mehrere Abonnements verfügen.

	- **Ort** ist die Rechenzentrumsregion. Derzeit müssen alle Ressourcen im gleichen Rechenzentrum ausgeführt werden. Die Aufteilung von Ressourcen auf mehrere Rechenzentren wird nicht unterstützt.

4. Klicken Sie auf **Erstellen**, um den Dienst bereitzustellen.

Achten Sie auf Benachrichtigungen in der Navigationsleiste. Wenn der Dienst verwendet werden kann, erscheint eine Benachrichtigung.

<a id="sub-2"></a>
## Ermitteln des Dienstnamens und des API-Schlüssels des Azure Search-Diensts

Nach der Diensterstellung können Sie zum Portal zurückkehren, um URL und `api-key` zu ermitteln. Für Verbindungen mit Ihrem Search-Dienst benötigen Sie sowohl die URL als auch einen `api-key`, um den Aufruf zu authentifizieren.

1. Klicken Sie auf der Navigationsleiste auf **Startseite** und anschließend auf den Search-Dienst, um das Service-Dashboard zu öffnen.

2. Im Service-Dashboard werden Kacheln für wichtige Informationen sowie das Schlüsselsymbol für den Zugriff auf die Administratorschlüssel angezeigt.

  	![][3]

3. Kopieren Sie die Dienst-URL und einen Administratorschlüssel. Sie benötigen diese später, wenn Sie sie der Datei **config.properties** hinzufügen.

## Herunterladen der Beispieldateien

1. Wechseln Sie zu [AzureSearchJavaDemo](http://go.microsoft.com/fwlink/p/?LinkId=530197) auf Github.

2. Klicken Sie auf **Download ZIP**, um die ZIP-Datei auf dem Datenträger zu speichern und dann die darin enthaltenen Dateien zu extrahieren. Es empfiehlt sich, die Dateien in den Java-Arbeitsbereich zu extrahieren, damit Sie das Projekt später leichter finden.

3. Die Beispieldateien sind schreibgeschützt. Klicken Sie mit der rechten Maustaste auf den Ordner, öffnen Sie die Eigenschaftenseite, und deaktivieren Sie das Schreibschutzattribut.

Alle nachfolgenden Dateiänderungen und Ausführungsanweisungen werden an den Dateien in diesem Ordner vorgenommen.

## Importieren des Projekts

1. Wählen Sie in Eclipse **File** | **Import** | **General** | **Existing Projects into Workspace**.

    ![][4]

2. Navigieren Sie in **Select root directory** zu dem Ordner mit den Beispieldateien. Wählen Sie den Ordner aus, der den .projekt-Ordner enthält. Das Projekt sollte in der Liste **Projects** als ausgewähltes Element angezeigt werden.

    ![][12]

3. Klicken Sie auf **Fertig stellen**.

4. Verwenden Sie **Project Explorer**, um die Dateien anzuzeigen und zu bearbeiten. Wenn Project Explorer noch nicht geöffnet ist, klicken Sie auf **Window** | **Show View** | **Project Explorer**, oder verwenden Sie die Verknüpfung, um Project Explorer zu öffnen.

## Konfigurieren von Dienst-URL und API-Schlüssel

1. Doppelklicken Sie in **Project Explorer** auf **config.properties**, um die Konfigurationseinstellungen zu bearbeiten, die den Servernamen und den API-Schlüssel enthalten.

2. Schlagen Sie die Schritte weiter oben in diesem Artikel nach, mit denen Sie die Dienst-URL und den API-Schlüssel im [Azure-Portal](https://portal.azure.com) ermittelt haben, um die Werte zu erhalten, die Sie jetzt in **config.properties** eingeben.

3. Ersetzen Sie in der Datei **config.properties**, "API Key" durch den API-Schlüssel ihres Diensts. Anschließend ersetzen Sie in der gleichen Datei "service name" durch den Dienstname (die erste Komponente der URL http://servicename.search.windows.net).

	![][5]

## Konfigurieren der Projekt-, Build- und Laufzeitumgebungen

1. Klicken Sie in Project Explorer in Eclipse mit der rechten Maustaste auf das Projekt | **Properties** | **Projekt Facets**.

2. Wählen Sie **Dynamic Web Module**, **Java** und **JavaScript** aus.

    ![][6]

3. Klicken Sie auf **Apply**.

4. Wählen Sie **Window** | **Preferences** | **Server** | **Runtime Environments** | **Add..** aus.

5. Erweitern Sie Apache, und wählen Sie die Version des Apache Tomcat-Servers, die Sie zuvor installiert haben. Auf unserem System wurde Version 8 installiert.

	![][7]

6. Geben Sie auf der nächsten Seite das Tomcat-Installationsverzeichnis an. Auf einem Windows-Computer ist dies wahrscheinlich "C:\\Programme\\Apache Software Foundation\\Tomcat *Version*.

6. Klicken Sie auf **Fertig stellen**.

7. Wählen Sie **Window** | **Preferences** | **Java** | **Installed JREs** | **Add** aus.

8. Wählen Sie in **Add JRE** die Option **Standard VM** aus.

10. Klicken Sie auf **Weiter**.

11. Klicken Sie auf der JRE-Startseite in "JRE Definition" auf **Directory**.

12. Navigieren Sie zu **Program Files** | **Java**, und wählen Sie das JDK aus, das Sie zuvor installiert haben. Es ist wichtig, dass Sie das JDK als JRE auswählen.

13. Wählen Sie unter "Installed JREs" die Option **JDK**. Ihre Einstellungen sollte in etwa wie folgender Screenshot aussehen.

    ![][9]

14. Wählen Sie optional **Window** | **Webb Browser** | **Internet Explorer**, um die Anwendung in einem externen Browserfenster zu öffnen. In einem externen Browser wird die Webanwendung optimal dargestellt.

    ![][8]

Sie haben die Konfigurationsaufgaben nun abgeschlossen. Als Nächstes erstellen Sie das Projekt und führen Sie es aus.

## Erstellen des Projekts

1. Klicken Sie in Project Explorer mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Run As** | **Maven build...**, um das Projekt zu konfigurieren.

    ![][10]

8. Geben Sie in "Edit Configuration" unter "Goals" das Ziel "clean install" ein, und klicken Sie dann auf **Run**.

Statusmeldungen werden an das Konsolenfenster ausgegeben. Sie sollten die Meldung BUILD SUCCESS sehen, die angibt, dass das Projekt fehlerfrei erstellt wurde.

## Ausführen der App

In diesem letzten Schritt führen Sie die Anwendung in einer Laufzeitumgebung auf dem lokalen Server aus.

Wenn Sie noch keine Server-Laufzeitumgebung in Eclipse angegeben haben, müssen Sie dies zunächst tun.

1. Erweitern Sie in Project Explorer **WebContent**.

5. Klicken Sie mit der rechten Maustaste auf **Search.jsp** | **Run As** | **Run on Server**. Wählen Sie den Apache Tomcat-Server aus, und klicken Sie dann auf **Run**.

> [AZURE.TIP]Wenn Sie das Projekt nicht im Standardarbeitsbereich gespeichert haben, müssen Sie wahrscheinlich die Einstellung für **Run Configuration** ändern, damit sie auf den Speicherort des Projekts verweist, um einen Server-Startfehler zu vermeiden. Klicken Sie in Project Explorer mit der rechten Maustaste auf **Search.jsp** | **Run As** | **Run Configurations**. Wählen Sie den Apache Tomcat-Server aus. Klicken Sie auf **Arguments**. Klicken Sie auf **Workspace** oder **File System**, um den Ordner, der das Projekt enthält, festzulegen.

Wenn Sie die Anwendung ausführen, sollte ein Browserfenster angezeigt werden, das ein Suchfeld zur Eingabe von Suchbegriffen bereitstellt.

Warten Sie etwa eine Minute, bevor Sie auf **Search** klicken, damit der Dienst Zeit zum Erstellen und Laden des Index hat. Wenn ein HTTP 404-Fehler angezeigt wird, müssen Sie nur etwas länger warten, bevor Sie es erneut versuchen.

## Suchen nach USGS-Daten

Das USGS-Dataset enthält Datensätze, die für den Bundesstaat Rhode Island relevant sind. Wenn Sie auf **Search** klicken und das Suchfeld leer ist, erhalten Sie die ersten 50 Einträge. Dies ist die Standardeinstellung.

Durch die Eingabe eines Suchbegriffs erhält das Suchmodul eine Vorgabe. Geben Sie einen regionalen Namen ein. "Roger Williams" war die erste Gouverneur von Rhode Island. Zahlreiche Parks, Gebäude und Schulen sind nach ihm benannt.

![][11]

Sie können auch einen dieser Begriffe ausprobieren:

- Pawtucket
- Pembroke
- goose +cape

## Nächste Schritte

Dies ist das erste Azure Search-Lernprogramm, das auf Java und dem USGS-Dataset basiert. Im Laufe der Zeit werden wir dieses Lernprogramm erweitern, um zusätzliche Suchfunktionen zu veranschaulichen, die Sie in benutzerdefinierten Lösungen vielleicht verwenden möchten.

Wenn Sie bereits über Erfahrungen mit Azure Search verfügen, können Sie dieses Beispiel als Ausgangspunkt für weitere Experimente nutzen, um die [Suchseite](search-pagination.md) zu erweitern oder eine [Facettennavigation](../search-faceted-navigation/) zu implementieren. Sie können auch die Suchergebnisseite verbessern, indem Sie Statistiken hinzufügen und Dokumente mit Batchvorgängen verarbeiten, damit die Benutzer die Ergebnisse seitenweise anzeigen können.

Neu bei Azure Search? Es wird empfohlen, auch andere Lernprogramme zu bearbeiten, damit Sie ein Verständnis dafür entwickeln, was Sie erstellen können. Besuchen Sie unsere [Dokumentationsseite](http://azure.microsoft.com/documentation/services/search/), um weitere Ressourcen zu finden. Sie können auch die Links in unserer [Video- und Lernprogrammliste](https://msdn.microsoft.com/library/azure/dn798933.aspx) besuchen, um auf weitere Informationen zuzugreifen.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
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
 

<!---HONumber=July15_HO2-->