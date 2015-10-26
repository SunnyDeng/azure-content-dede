<properties 
	pageTitle="Azure Search-Fallstudie für Entwickler: So hat WhatToPedia mit Microsoft Azure ein Infomedia-Portal erstellt" 
	description="Erfahren Sie, wie mit dem Suchdienst von Microsoft Azure ein Informationsportal mit einer Metadaten-Suchmaschine erstellt wird." 
	services="search, sql-database,  storage, web-sites" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe"/>

<tags 
	ms.service="search" 
	ms.devlang="NA" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="search" 
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# Azure Search-Fallstudie für Entwickler

## So hat [WhatToPedia.com](http://whattopedia.com/) mit Microsoft Azure ein Infomedia-Portal erstellt

 ![][6] & Nbsp; & Nbsp; & Nbsp; <font size="9">Das Konzept im Überblick</font>


Unser Konzept besteht im Wesentlichen darin, ein Informationsportal zu erstellen, über das Kunden durch zielgenaue, bewertete Suchergebnisse mit Einzelhändlern in Kontakt gebracht werden, ähnlich wie Reiseportale Touristen in fernen Ländern mit Hotels, Restaurants und Unterhaltungsangeboten zusammenführen.

Das Portal, das unserer Vorstellung entspricht, liefert außergewöhnlich hochwertige Suchergebnisse zu Einzelhändlerdaten in einem bestimmten Markt, wobei es Käufer dabei unterstützt, Geschäfte anhand des Standorts und anderer Angebote und Besonderheiten des Händlers zu finden. Wir bestücken die Suchmaschine mit einem Anfangsdataset, die tatsächliche Relevanz ergibt sich aber im Laufe der Zeit durch die Einzelhandelsabonnenten, die Informationen zu ihren Geschäften bereitstellen. Werbeaktionen, neue Waren, bekannte Marken, spezielle Dienstleistungen – dies alles sind Beispiele für Daten, die die Relevanz unserer Website erhöhen. Hierbei handelt es sich stets um selbst mitgeteilte Daten, die in den Suchbestand integriert werden, nachdem sich ein Einzelhändler als Abonnent registriert hat. Dabei finanziert sich unser Startup-Unternehmen aus Werbung sowie dem Abonnementmodell.

Suchabfragen bilden das vorherrschende Benutzerinteraktionsmodell auf einer reinen Cloud-Plattform. Aus Gründen der Skalierbarkeit und niedriger Kosten erledigen wir fast alles, von der Portaloberfläche bis zur Quellcodeverwaltung, über einen Onlinedienst. Durch Verwendung einer Suchmaschine, die die von uns benötigten Funktionen standardmäßig bereitstellt, können wir eine Suchanwendung schnell erstellen, ohne selbst eine Suchmaschine erstellen und verwalten zu müssen.

## Das haben wir erstellt

WhatToPedia ist ein Infomedia-Startup-Unternehmen. Wir haben [WhatToPedia.com](http://whattopedia.com/) gegründet und – vorläufig nur für einen Testmarkt in Nordeuropa – am 2. Februar 2015 freigeschaltet. Unsere Kundenbasis bilden in erster Linie Brick and Mortar-Geschäfte, die eine kostengünstige und einfach zu verwaltende Onlinepräsenz benötigen.

Unsere Aufgabe ist es, Kunden durch ein hervorragendes Onlinesucherlebnis anzulocken, wobei Ergebnisse basierend auf Ort oder Umgebung, Marken, Geschäftsnamen oder Geschäftstypen höhergestuft werden. Die auf diese Weise betriebene Kundenaquisition hat einen sich selbst verstärkenden Effekt, wodurch Einzelhändler motiviert werden, unsere Portalwebsite zu abonnieren. Abonnements sind kostenpflichtig, dies jedoch zu einem erschwinglichen Preis.

 ![][7]

Nachdem sich ein Einzelhändler für ein Abonnement angemeldet hat, übernimmt er sein vorhandenes Profil (zunächst von uns aus erworbenen Daten erstellt) und aktualisiert dieses Profil mit zusätzlichen Daten über Werbeaktionen, unterstützte Marken oder Ankündigungen. Geschäftsinterne Möglichkeiten, wie z. B. gesprochene Sprachen, akzeptierte Währungen, steuerfreies Einkaufen, können selbst mitgeteilt werden, um gezielt Kunden anzuziehen, die nach solchen Zusatzleistungen suchen.

## Wer wir sind

Mein Name ist Thomas Segato (Microsoft Consulting), und ich habe mit Jesper Boelling, leitender Entwickler bei WhatToPedia, am Entwurf dieser Lösung gearbeitet.

WhatToPedia ist ein Startup-Unternehmen, das sein neues Portalgeschäft zunächst testweise in Schweden vermarkten möchte, wo die meisten der 60.000 Einzelhändler kleine und mittelgroße Brick and Mortar-Unternehmen sind. Da wir wissen, dass in Europa verschiedene Sprachen gesprochen und in verschiedenen Währungen gezahlt wird, erstellen wir Lösungen, die auf einen mehrsprachigen Kunden zugeschnitten sind. Dazu haben wir eine Suchmaschine, die unsere Anforderungen bezüglich Mehrsprachigkeit unterstützt, gesucht und in Azure Search gefunden.

Azure Search war ein Wendepunkt für unser Projekt. Bevor Azure Search verfügbar war, haben wir sehr viel Energie darauf verwandt, uns durch die Höhen und Tiefen der Erstellung einer eigenen Suchmaschine zu mühen. Dadurch, dass Azure Search als Onlinedienst verfügbar wurde, sind die größten technischen und administrativen Hürden für unsere Lösung gefallen, was eine schnellere Markteinführung bedeutete, und dies mit einer robusteren Suchfunktionalität.

## So sind wir vorgegangen

Unsere Vorstellung bestand darin, eine komplette Infrastruktur zu erstellen, die ausschließlich auf Clouddiensten basiert. Microsoft wurde als strategische Plattform gewählt, weil Microsoft der Anbieter war, der die erforderlichen Dienste (sowohl für Zusammenarbeit als auch für Entwicklung) und bedarfsgerechte Skalierung zu erschwinglichen Preisen angeboten hat.
 
### Spitzenkomponenten

Wir haben ein Unternehmen erstellt, nicht nur eine Website. Die Unterstützung des gesamten Projekts erforderte einen umfassenden Satz an Tools und Anwendungen. Wir haben Visual Studio und Visual Studio Online für die Entwicklung, Team Foundation Service (TFS) Online für die Quellcodeverwaltung und die Scrum-Verwaltung, Office 365 für Kommunikation und Zusammenarbeit und natürlich Microsoft Azure für alle websitebezogenen Vorgänge und Speicher verwendet. Die Visual Studio-IDE bietet direkte Bereitstellung in Azure mit Integration in TFS Online, wodurch sich eine zusätzliche Produktivitätssteigerung ergibt.

Die folgende Abbildung veranschaulicht die in der WhatToPedia-Infrastruktur verwendeten Spitzenkomponenten.

   ![][8]

### So verwenden wir Microsoft Azure

Wenn Sie die grünen Kästchen in der vorherigen Abbildung betrachten, sehen Sie, dass die WhatToPedia-Lösung auf folgenden Diensten basiert:

- [Azure Search](http://azure.microsoft.com/services/search/)
- [Azure-Websites, für die MVC 4 verwendet wird](http://azure.microsoft.com/services/websites/)
- [Azure WebJobs für geplante Aufgaben](../websites-webjobs-resources.md)
- [Azure SQL-Datenbank](http://azure.microsoft.com/services/sql-database/)
- [Azure-Blobspeicher](http://azure.microsoft.com/services/storage/)
- [E-Mail-Zustellung über SendGrid](http://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

Das Herzstück der Lösung sind die Daten und die Suche. Der Datenfluss vom Wiederverkäufer (Reseller) zum Endkunden ist nachstehend dargestellt:

  ![][9]

Primärer Datenspeicher ist die Azure SQL-Datenbank mit Wiederverkäufer- und Buchhaltungsdaten. Diese Daten bestehen aus dem Anfangsdataset und den einzelhändlerspezifischen Daten, die im Laufe der Zeit hinzugefügt werden. Zum Senden von Aktualisierungen aus der SQL-Datenbank an den Suchbestand in Azure Search verwenden wir einen Azure WebJobs-Auftrag.

### Darstellungsschicht

Das Portal ist eine Azure-Website, die in MVC 4 und [Twitter Bootstrap](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29) implementiert ist. Wir haben MVC gewählt, weil es einen saubereren Ansatz für HTML bietet als eine auf ASP.NET-Formularen basierende Entwicklung. Um zu vermeiden, dass Apps für mehrere Geräte erstellt und mehrere mobile Plattformen verwaltet werden müssen, wurde Twitter Bootstrap gewählt, das alle Geräte und Plattformen unterstützt.

### Authentifizierung, Berechtigungen und vertrauliche Daten

Kunden navigieren auf der Website anonym. Daher gibt es weder Anmeldeanforderungen für Kunden, noch speichern wir irgendwelche Kundendaten.

Einzelhändler sind eine andere Geschichte. Zu diesen speichern wir die öffentlich zugänglichen Profilinformationen, Rechnungserstellungsinformationen und Medieninhalte, die auf der Website verfügbar gemacht werden sollen. Jeder Einzelhändler, der die Website abonniert hat, erhält eine Benutzeranmeldung, mit der sich der Benutzer authentifiziert, bevor er Aktualisierungen am Profil des Abonnenten vornehmen kann. Wir speichern alle Abonnentendaten sicher in einer Azure SQL-Datenbank und im Azure-Blobspeicher. Wir haben uns für ein Authentifizierungsmodell entschieden, das auf Authentifizierung über .NET-Formulare basiert. Warum wir diesen Ansatz gewählt haben? Aufgrund seiner Einfachheit. Wir benötigen die Rollen, die UI-Unterstützung und die weiteren überflüssigen Features nicht, die mit anderen Ansätzen verbunden sind.

Um sicherzustellen, dass Einzelhändler nur ihre eigenen Daten sehen können, haben wir für jeden Einzelhändler eine Einzelhändler-ID erstellt, die danach für alle Lese- und Schreibvorgänge verwendet wird, die sich auf einzelhändlerspezifische Daten beziehen. Bei diesem Ansatz sind wir zu der Überzeugung gekommen, dass es nicht erforderlich ist, einzelnen Einzelhändlern Datenbankberechtigungen zu erteilen. Alle Einzelhändler interagieren mit dem System unter einer einzigen Datenbankrolle, wobei die Einzelhändler-ID unser Mittel zur Datenisolierung ist.

Weil es in unserem Geschäft nur um nachgelagerte Effekte geht (Kunden und Einzelhändler zusammenführen, wodurch Anreize für Werbung und Abonnements entstehen), enden unsere Dienste bei der Verarbeitung von Käufen über das Web. Daher finden Sie keinen Warenkorb auf unserer Website, wodurch sich unsere Sicherheitsanforderungen vereinfachen.

Eine weitere Vereinfachung, die wir umgesetzt haben, besteht darin, dass wir unsere Rechnungserstellung und Kontenabrechnung ausgelagert haben. Durch direkte Weiterleitung der Zahlungsinformationen von Kunden an einen Drittanbieter ([SveaWebPay](http://www.sveawebpay.se/)) verringern wir die Risiken, die mit dem Speichern und dem Schutz vertraulicher Daten in unseren Datenspeichern verbunden wären.

### Suchmaschine

Das Herzstück unserer Lösung ist die Suchmaschine, die auf dem Azure-Suchdienst aufsetzt. Zunächst haben wir eine benutzerdefinierte Suchmaschine erstellt. Dabei haben wir jedoch festgestellt, dass die Komplexität und der Aufwand tatsächlich sehr hoch sind, und das hat uns dazu veranlasst, nach Alternativen zu suchen.

Zu den grundlegenden Funktionen, die für uns am wichtigsten waren, gehörten:

- Filter
- Facettennavigation
- Höherstufen von Ergebnissen
- Seitenverwaltung über AJAX

Durch eine Suche im Internet sind wir auf folgendes Video gestoßen, das uns dazu veranlasst hat, es mit Azure Search zu versuchen: [Deep Dive at TechEd Europe](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410)

Nachdem wir uns das Video angesehen hatten, konnten wir anhand des Gesehenen einen Prototyp erstellen. Da wir bereits ein Datenmodell in MVC hatten, war die Erstellung des Prototyps einfach, denn die Daten enthielten suchbare Begriffe, und wir hatten bereits die Anforderungen für die Sortierung, Facettierung und Filterung der Daten ausgearbeitet.

Den Prototyp haben wir dann wie folgt erstellt.

**Konfigurieren des Azure-Suchdiensts**

1. Anmelden beim Azure-Portal und Hinzufügen des Suchdiensts zu unserem Abonnement: Wir haben die gemeinsam genutzte Version verwendet (kostenlos für unser Abonnement).
2. Erstellen eines Index: Für den Prototyp haben wir die Benutzeroberfläche des Portals verwendet, um die Suchfelder zu definieren und die Bewertungsprofile zu erstellen. Unser Bewertungsprofil basiert auf Standortdaten: Land | Stadt | Straße (siehe „Hinzufügen von Bewertungsprofilen“).
3. Kopieren der Dienst-URL und des API-Schlüssels (api-key) für Administratoren in unsere Konfigurationsdateien: Dieser Schlüssel befindet sich im Portal auf der Seite des Suchdiensts und wird zur Authentifizierung beim Dienst verwendet.
	
**Entwickeln eines Suchindexerauftrags – Windows-Konsole**

1. Einlesen aller Wiederverkäufer aus der Datenbank.
2. Aufrufen der Azure-Suchdienst-API, um Wiederverkäufer einzeln hochzuladen (siehe http://msdn.microsoft.com/library/azure/dn798930.aspx)).
3. Festlegen einer Eigenschaft in der Datenbank, die angibt, dass der Wiederverkäufer für die inkrementelle Indizierung indiziert ist. Wir haben dazu ein „indexer“-Feld hinzugefügt, in dem der Indexstatus jedes Profils gespeichert wird (indiziert oder nicht indiziert). 

Nähere Informationen hierzu finden Sie im Anhang mit dem Codeausschnitt, durch den der Indexerauftrag erstellt wird.

**Entwickeln eines Webportals für die Suche – MVC**

1. Aufrufen des Azure-Suchdiensts, um alle Dokumente aus der Suche zu erhalten (siehe http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. Extrahieren der folgenden Informationen aus der Antwort des Suchdiensts (durch Verwenden von json.net http://james.newtonking.com/json)
   - Ergebnisse
   - Facetten
   - Ergebniszählungen
   - Entwickeln einer Benutzeroberfläche für die Anzeige von Suchergebnissen, Facetten und Zählungen (diese hatten wir bereits).

Dies ist der Code, den wir zum Abrufen der Ergebnisse aus Azure Search verwendet haben:

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**Höherstufen nach Standort**

Die wahrscheinlich wichtigste Anforderung bei der Überprüfung des Prototyps bestand darin, der Abfrage ein Schlüsselwort zur Standortsuche hinzuzufügen. Es ist unverzichtbar für unser Portal, dass, wenn ein Benutzer einen Stadtnamen in die Suchabfrage eingibt, Wiederverkäufer aus der angegebenen Stadt höher eingestuft werden als Wiederverkäufer, bei denen das Stadtschlüsselwort in der Beschreibung enthalten ist. Für diese Anforderung haben wir ein Bewertungsprofil verwendet, das das Feld für Stadt höher einstuft als alle anderen Felder.

**Unterstützung mehrerer Sprachen**

Wir mussten richtige Suchergebnisse in richtigen Sprachen anzeigen und eine Option bereitstellen, über die sich die gleichen Ergebnisse in verschiedenen Sprachen finden lassen. Diese Aufgabe stellte uns vor zwei Herausforderungen:

- Suchen nach Wörtern in mehreren Sprachen
- Anzeigen der Suchergebnisse in der richtigen Sprache

Das Darstellungsproblem haben wir gelöst, indem wir ein Dokument für jede Sprache mit lokalisiertem Text und eine Eigenschaft mit der Sprache hinzugefügt haben. Wenn ein Benutzer einen Suchbegriff eingibt, werden `$filter`-Ausdrücke in der vom Benutzer gewählten Sprache verwendet.

Jedes der Dokumente hat eine verdeckte Eigenschaft namens „cities“, die auf dem Typ „collection“ basiert. In dieser Eigenschaft sind Stadtnamen in allen Sprachen gespeichert, wodurch Suchen in verschiedenen Sprachen möglich werden.

###Datenspeicher

Alle Daten (Profil, Abonnement und Kontoführung) werden in der SQL-Datenbank gespeichert. Alle Mediendateien werden im Azure-Blobspeicher gespeichert, einschließlich der Bilder und Videos, die vom jeweiligen Einzelhändler bereitgestellt werden. Durch die Verwendung des separaten Blobspeichers werden die Auswirkungen von Dateiuploads gekapselt. Dateien werden nie mit der Website vermengt, sodass die Website nicht jedes Mal neu erstellt werden muss, wenn Dateien hinzugefügt wurden.

Ein wichtiger Vorteil unseres Speicherkonzepts besteht darin, dass mehrere Entwickler einen einzelnen Entwicklungsspeicher gemeinsam nutzen können. Eine der Anforderungen für das WhatToPedia-Projekt war, dass es möglich sein muss, eine Entwicklungsumgebung innerhalb von 15 Minuten erstellen zu können, einschließlich der Daten, Bilder und Videos vom jeweiligen Wiederverkäufer. Durch Abrufen der neuesten Daten aus TFS Online, Ausführen eines SQL-Skripts und Ausführen des Importauftrags kann eine vollständige Umgebung innerhalb kürzester Zeit eingerichtet werden. Diese Vorgehensweise verbessert auch den Stagingprozess.

###WebJobs

Wir verwenden Azure WebJobs, um Daten für den Index zu aktualisieren. Durch Erstellen eines Suchindexerauftrags ließ sich der Indizierungsteil sehr einfach in unsere Lösung integrieren. Die einzige Codeänderung, die wir vorgenommen haben, um dem Indexerauftrag gerecht zu werden, bestand darin, unserem Datenmodell das Feld `Indexed` hinzuzufügen, damit der Indexstatus angegeben werden kann. Immer dann, wenn ein neues Profil hinzugefügt oder aktualisiert wird, wird das Feld `Indexed` auf „false“ festgelegt. Dasselbe gilt, wenn ein Einzelhändler seine Profildaten über das Portal ändert.

Der Auftrag sucht nach allen Zeilen, in denen `Indexed` auf „false“ festgelegt ist. Findet er eine solche Zeile, wird das Dokument an Azure Search gesendet und das Feld `Indexed` wird auf „true“ festgelegt. Wir mussten nicht zwischen Hinzufügen und Aktualisieren von Daten unterscheiden und einen entsprechenden Mechanismus einrichten, weil dies bereits vom Azure-Suchdienst übernommen wird. Wenn Sie ein Dokument hinzufügen, das bereits vorhanden ist, führt der Dienst automatisch eine Aktualisierung aus.

Alle Webaufträge wurden als Konsolenanwendungen entwickelt, die als ZIP-Dateien auf Azure-Websites hochgeladen, entpackt und dann geplant werden können.

Der Auftrag ist so geplant, dass er alle 5 Minuten als geplante Webaufgabe ausgeführt wird. Wir haben berechnet, dass der Dienst ungefähr drei Minuten benötigt, um 3.000 Dokumente hochzuladen. Dies erfüllt unsere Anforderungen.

> [AZURE.NOTE]Es gibt ein Prototypindexerfeature, das kürzlich in Azure Search eingeführt wurde. Dieses Feature kam für uns zu spät, um es in unserer ersten Version zu verwenden. Es scheint aber genau das Problem zu lösen, für das wir unsere Indexeraufgabe verwenden: das Automatisieren von Datenladevorgängen.


###Sicherungsstrategie

Wir haben eine mehrstufige Sicherungsstrategie entwickelt, um in den verschiedensten Szenarios eine Wiederherstellung zu ermöglichen, angefangen bei der Wiederherstellung einzelner Transaktionen bis hin zur Wiederherstellung bei einem vollständigen Systemausfall. Die zu schützenden Ressourcen umfassen drei Arten von Daten: Website, Abonnentendaten und Mediendateien.

Zunächst einmal wissen wir, weil wir den Quellcode der Website in TFS Online aufbewahren, dass die Website nach einem Ausfall wiederhergestellt werden kann, indem sie aus TFS erneut veröffentlicht wird.

Die Abonnentendaten in der Azure SQL-Datenbank sind die empfindlichste Ressource. Wir sichern diese Daten mit der integrierten Funktion (siehe [Sichern und Wiederherstellen der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/jj650016.aspx)). Der Sicherungszeitplan sieht so aus: eine vollständige Datenbanksicherung einmal wöchentlich, eine differenzielle Datenbanksicherung einmal täglich und Transaktionsprotokollsicherungen alle 5 Minuten. In Anbetracht des Umfangs der Daten ist diese Lösung mehr als ausreichend für unsere unmittelbaren und geplanten Datenvolume.

Bild- und Videodateien speichern wir darüber hinaus im Azure-Blobspeicher. Wir sind nach wie vor dabei, den ultimativen Sicherungsplan für diese Daten zu evaluieren, wobei Cloudberry Explorer für Azure als eine mögliche Lösung in Frage kommt. Momentan verwenden wir einen WebJobs-Auftrag, um Bilder und Videos an einen anderen Speicherort zu kopieren.

##Was wir gelernt haben

Da wir bereits Daten hatten, war es einfach, einen Machbarkeitstest einzurichten. Innerhalb weniger Stunden hatten wir einen Prototyp mit Facetten und Zählungen, Seitenverwaltung, Bewertungsprofilen und Suchergebnisse erstellt. Die Suchergebnisse waren so genau, dass wir uns entschieden haben, einige der Filter, die dem Endkunden angezeigt wurden, zu entfernen.

Die größte Überraschung für uns war festzustellen, wie schnell wir uns in Azure Search eingearbeitet hatten und wie viel wir zurückbekommen haben. Genau genommen haben wir den Machbarkeitstest in wenigen Stunden eingerichtet (siehe Hinweis unten), wobei wir 500 Codezeilen durch drei Codezeilen in der Front-End-Anwendung ersetzt (plus einem neuen WebJob-Auftrags) und dabei bessere Ergebnisse erzielt haben.

Zuvor waren in unserem Code Seitenverwaltung, Zählungen und weitere Verhaltensweisen implementiert, die für Suchvorgänge Standard sind. Bei Verwendung von Azure Search enthalten unsere Ergebnisse Suchtreffer, Facetten, Seitenverwaltungsdaten und Zählungen – eben all die Daten, die wir benötigen und vorher selbst bereitstellen mussten. Azure Search bietet zudem die Möglichkeit des Höherstufens sowie eine integrierte Facettennavigation, die wir in unserer ursprünglichen Lösung nicht hatten.

Die größte Herausforderung bei der Implementierung bestand darin, dass Azure Search in einer Vorschauversion vorlag und es schwierig war, Informationen sowie veröffentlichte Erfahrungen zu finden. Nachdem wir aber einige Puzzlesteine zusammengefügt hatten, haben wir festgestellt, dass sich der Azure-Suchdienst aufgrund seiner REST-API und seines JSON-Datenformats ziemlich einfach verwenden lässt. Wir konnten das Framework direkt aus den meisten Open Source-Plug-ins wie JQuery JSON.Net aufrufen und Tools wie Fiddler für schnelle Experimente und das Debugging verwenden.

> [AZURE.NOTE]Hilfreich war nicht nur, dass die Daten bereits vorbereitet waren, sondern auch, dass sich diejenigen von uns, die den Prototyp erstellten, bestens mit Suchtechnologien auskannten, so dass wir ziemlich schnell produktiv werden konnten und die integrierten Features so richtig zu schätzen wussten. Wenn Sie jedoch über die reinen Suchabfragen hinaus auch die Facettennavigation, Filter und dergleichen verwenden möchten, dauert die Erstellung des Prototyps vermutlich länger.

###Steuern von Facetten auf der Suchdarstellungsseite

Während des Machbarkeitstests mussten wir recht schmerzhaft erfahren, dass Facetten sorgfältig im Voraus geplant werden müssen. Nachdem wir bereits ein ziemliches Datenvolumen in die Lösung geladen hatten, merkten wir, dass die schiere Menge der Facetten für eine Darstellung für die Benutzer zu groß war.

Dies lösten wir, indem wir den Parameter für die Anzahl der Facetten herabsetzten. Dieser Parameter setzt der Anzahl der Facetten, die dem Benutzer zurückgegeben werden können, eine feste Obergrenze. Nähere Ausführungen zu diesem Parameter im Rahmen der Facettennavigation finden Sie [hier](search-faceted-navigation.md).

###WebJobs für die Planung von Aufgaben

Azure-Suche war nicht die einzige angenehme Überraschung für uns. Wir stellten fest, dass die Verwendung von WebJobs zur Automatisierung unserer Datenladeoperationen für Azure Search unserer früheren Vorgehensweise deutlich überlegen ist, die aus einem dedizierten virtuellen Computer bestand, auf dem Windows Scheduler mit geplanten Aufgaben zur Aktualisierung des Suchindexes ausgeführt wurde. WebJobs ist einfacher zu konfigurieren und zu debuggen und gegenüber einem dedizierten virtuellen Computer viel preiswerter.

###Azure Storage Explorer zum Aktualisieren von Bildern

Wie wir feststellten, ist auch der [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) (verfügbar auf Codeplex), den wir zum Verwalten von Bild- und Videoaktualisierungen auf der Website verwenden, überaus hilfreich für uns. Wir verwenden den Explorer als Entwicklertool für die manuelle Aktualisierung von Bildern und Videos, die sich bereits auf unserer Hauptwebsite befinden. Er hat sich als flexibler erwiesen als die Bereitstellung von Änderungen im Portal und macht zudem Testiterationen bei der Aktualisierung von Bildern überflüssig.

##Abschließende Worte

Vielen Dank den großartigen Leuten bei WhatToPedia, dass sie uns an ihrer Geschichte teilhaben lassen!

Wir hoffen, dass Sie diese Fallstudie hilfreich fanden. Wenn Sie tiefer in die Arbeit mit Azure Search einsteigen möchten, finden Sie vermutlich auch die folgenden Informationen hilfreich:

- [MSDN-Forum zu Azure Search](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow hat auch ein Tag](http://stackoverflow.com/questions/tagged/azure-search)
- [Dokumentationsseite auf Azure.com](http://azure.microsoft.com/documentation/services/search/)
- [Azure Search-Dokumentation auf MSDN](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##Anhang: Suchindexer-WebJob

Der folgende Code erstellt den Indexer, der im Abschnitt über die Erstellung des Prototyps erwähnt ist.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 

<!---HONumber=Oct15_HO3-->