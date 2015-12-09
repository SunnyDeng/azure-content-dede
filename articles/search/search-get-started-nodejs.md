<properties
	pageTitle="Erste Schritte mit Azure Search in NodeJS | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Exemplarische Vorgehensweise zur Erstellung einer Suchanwendung in einem gehosteten Cloudsuchdienst mit der Programmiersprache NodeJS."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Erste Schritte mit Azure Search in NodeJS

Erfahren Sie, wie Sie eine benutzerdefinierte NodeJS-Suchanwendung erstellen, die Azure Search zum Suchen verwendet. Dieses Lernprogramm verwendet die [REST-API für den Azure Search-Dienst](https://msdn.microsoft.com/library/dn798935.aspx) zum Erstellen der Objekte und Vorgänge, die in dieser Übung verwendet werden.

Zum Entwickeln und Testen dieses Codes wurden [NodeJS](https://nodejs.org) und NPM, [Sublime Text 3](http://www.sublimetext.com/3) und Windows PowerShell unter Windows 8.1 verwendet.

Um dieses Beispiel auszuführen, benötigen Sie einen Azure Search-Dienst, für den Sie sich am [klassischen Azure-Portal](https://portal.azure.com) anmelden können.

> [AZURE.TIP]Laden Sie den Quellcode für dieses Lernprogramm von [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198) herunter.

## Informationen zu den Daten

In dieser Beispielanwendung werden Daten von [United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm) verwendet, die nach dem Bundesstaat Rhode Island gefiltert wurden, um die Größe des Datasets zu verringern. Wir verwenden diese Daten, um eine Suchanwendung zu erstellen, die markante Gebäude, wie Krankenhäuser und Schulen, sowie geologische Merkmale, wie Flüsse, Seen und Gipfel, zurückgibt.

In dieser Anwendung erstellt das Programm **DataIndexer** den Index und lädt ihn unter Verwendung eines [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx)-Konstrukts, wobei das gefilterte USGS-DataSet aus einer öffentlichen Azure SQL-Datenbank abgerufen wird. Anmeldeinformationen und Verbindungsinformationen zur Onlinedatenquelle werden im Programmcode bereitgestellt. Es ist keine weitere Konfiguration erforderlich.

> [AZURE.NOTE]Wir haben einen Filter auf dieses Dataset angewendet, um unter dem Limit des kostenlosen Tarifs von maximal 10.000 Dokumenten zu bleiben. Wenn Sie den Standardtarif verwenden, gilt dieses Limit nicht. Ausführliche Informationen zur Kapazität der einzelnen Tarife finden Sie unter [Limits und Einschränkungen](search-limits-quotas-capacity.md).

## Erstellen des Diensts

1. Melden Sie sich am [klassischen Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie über die seitliche Navigationsleiste zu **Neu** > **Daten + Speicher** > **Search**.

     ![][1]

3. Konfigurieren Sie den Dienstnamen, den Tarif, die Ressourcengruppe, das Abonnement und den Ort. Diese Einstellungen sind erforderlich und können nach der Dienstbereitstellung nicht mehr geändert werden.

     ![][2]

	- Der **Dienstname** muss eindeutig sein und aus weniger als 15 Kleinbuchstaben ohne Leerzeichen bestehen. Dieser Name wird Bestandteil des Endpunkts Ihres Azure Search-Diensts. Weitere Informationen zu den Benennungsregeln finden Sie unter [Benennungskonventionen](https://msdn.microsoft.com/library/azure/dn857353.aspx).

	- Der **Tarif** bestimmt Kapazität und Abrechnung. Beide Tarife bieten die gleichen Features, aber mit unterschiedlichen Ressourcen.

		- **Free** wird auf Clustern ausgeführt, die gemeinsam mit anderen Abonnenten genutzt werden. Diese Version bietet genügend Kapazität für Tutorials sowie zum Schreiben von Code für Machbarkeitsstudien, eignet sich jedoch nicht für den Einsatz in Produktionsumgebungen. Die Bereitstellung eines kostenlosen Diensts dauert in der Regel nur wenige Minuten.
		- **Standard** wird auf fest zugeordneten Ressourcen ausgeführt und ist flexibel skalierbar. Ein Standarddienst wird zunächst mit einem Replikat und einer Partition bereitgestellt, die Kapazität kann nach der Diensterstellung jedoch angepasst werden. Die Bereitstellung eines Standarddiensts dauert länger (üblicherweise etwa 15 Minuten).

	- **Ressourcengruppen** sind Container für Dienste und Ressourcen, die einem gemeinsamen Zweck dienen. Wenn Sie eine auf Azure Search, Azure Websites und Azure-Blobspeicher basierte Suchanwendung erstellen möchten, können Sie z. B. eine Ressourcengruppe erstellen, die diese Dienste in den Verwaltungsseiten im Portal gruppiert.

	- Unter **Abonnement** stehen ggf. mehrere Abonnements zur Auswahl, sofern Sie über mehrere Abonnements verfügen.

	- **Ort** ist die Rechenzentrumsregion. Derzeit müssen alle Ressourcen im gleichen Rechenzentrum ausgeführt werden. Die Aufteilung von Ressourcen auf mehrere Rechenzentren wird nicht unterstützt.

4. Klicken Sie auf **Erstellen**, um den Dienst bereitzustellen.

Achten Sie auf Benachrichtigungen in der Navigationsleiste. Wenn der Dienst verwendet werden kann, erscheint eine Benachrichtigung.

<a id="sub-2"></a>
## Ermitteln des Dienstnamens und des API-Schlüssels des Azure Search-Diensts

Nachdem Sie den Dienst erstellt haben, kehren Sie zum Portal zurück, um die URL oder den `api-key` zu ermitteln. Für Verbindungen mit Ihrem Search-Dienst benötigen Sie sowohl die URL als auch einen `api-key`, um den Aufruf zu authentifizieren.

1. Klicken Sie auf der Navigationsleiste auf **Startseite** und anschließend auf den Search-Dienst, um das Service-Dashboard zu öffnen.

2. Auf dem Service-Dashboard werden Kacheln für wichtige Informationen sowie das Schlüsselsymbol für den Zugriff auf die Administratorschlüssel angezeigt.

  	![][3]

3. Kopieren Sie die Dienst-URL, einen Administratorschlüssel und einen Abfrageschlüssel. Sie benötigen diese später, wenn Sie sie der Datei "config.js" hinzufügen.

## Herunterladen der Beispieldateien

Verwenden Sie eine der folgenden Vorgehensweisen zum Herunterladen des Beispiels.

1. Wechseln Sie zu [AzureSearchNodeJSIndexerDemo](http://go.microsoft.com/fwlink/p/?LinkId=530198).
2. Klicken Sie auf **Download ZIP**, um die ZIP-Datei auf dem Datenträger zu speichern, und extrahieren Sie dann die darin enthaltenen Dateien.

Alle nachfolgenden Dateiänderungen und Ausführungsanweisungen werden an den Dateien in diesem Ordner vorgenommen.

Wenn die path-Anweisung GIT enthält, können Sie stattdessen ein PowerShell-Fenster öffnen und `git clone https://github.com/EvanBoyle/AzureSearchNodeJSIndexerDemo.git` eingeben.

## Aktualisieren der Datei "config.js" mit der Search-Dienst-URL und dem API-Schlüssel

Unter Verwendung der URL und des API-Schlüssels, die Sie zuvor kopiert haben, geben Sie in der Konfigurationsdatei die URL, den Administratorschlüssel und den Abfrageschlüssel an.

Administratorschlüssel gewähren Ihnen uneingeschränkte Kontrolle über die Dienstvorgänge, einschließlich Erstellen oder Löschen eines Indexes und Laden von Dokumenten. Abfrageschlüssel sind hingegen nur für schreibgeschützte Vorgänge vorgesehen und werden in der Regel von Clientanwendungen verwendet, die eine Verbindung mit Azure Search herstellen.

In diesem Beispiel geben wir den Abfrageschlüssel an, um das empfohlene Verfahren der Verwendung von Abfrageschlüsseln in Clientanwendungen hervorzuheben.

Der folgende Screenshot zeigt die in einem Text-Editor geöffnete Datei **config.js**, wobei die relevanten Einträge abgegrenzt wurden, damit Sie sehen können, wo Sie die Datei mit den Werten, die für Ihren Suchdienst gültig sind, aktualisieren müssen.

![][5]


## Hosten eine Laufzeitumgebung für das Beispiel

Das Beispiel erfordert einen HTTP-Server, den Sie mit Npm global installieren können.

Verwenden Sie ein PowerShell-Fenster für die folgenden Befehle:

1. Navigieren Sie zu dem Ordner mit der **package.json**-Datei.
2. Geben Sie `npm install` ein.
2. Geben Sie `npm install -g http-server` ein.

## Erstellen Sie den Index, und führen Sie die Anwendung aus.

1. Geben Sie `npm run indexDocuments` ein.
2. Geben Sie `npm run build` ein.
3. Geben Sie `npm run start_server` ein.
4. Wechseln Sie mit dem Browser zu `http://localhost:8080/index.html`

## Suchen nach USGS-Daten

Das USGS-Dataset enthält Datensätze, die für den Bundesstaat Rhode Island relevant sind. Wenn Sie auf **Search** klicken und das Suchfeld leer ist, erhalten Sie die ersten 50 Einträge. Dies ist die Standardeinstellung.

Durch die Eingabe eines Suchbegriffs erhält das Suchmodul eine Vorgabe. Geben Sie einen regionalen Namen ein. "Roger Williams" war die erste Gouverneur von Rhode Island. Zahlreiche Parks, Gebäude und Schulen sind nach ihm benannt.

![][9]

Sie können auch einen dieser Begriffe ausprobieren:

- Pawtucket
- Pembroke
- goose +cape


## Nächste Schritte

Dies ist das erste Azure Search-Lernprogramm, das auf NodeJS und dem USGS-Dataset basiert. Im Laufe der Zeit werden wir dieses Lernprogramm erweitern, um zusätzliche Suchfunktionen zu veranschaulichen, die Sie in benutzerdefinierten Lösungen vielleicht verwenden möchten.

Wenn Sie bereits über Erfahrungen mit Azure Search verfügen, können Sie dieses Beispiel als Ausgangspunkt für das Testen von Vorschlägen (Typ-ahead- oder AutoVervollständigen-Abfragen), Filtern und Facettennavigation verwenden. Sie können auch die Suchergebnisseite verbessern, indem Sie Statistiken hinzufügen und Dokumente mit Batchvorgängen verarbeiten, damit die Benutzer die Ergebnisse seitenweise anzeigen können.

Neu bei Azure Search? Es wird empfohlen, auch andere Lernprogramme zu bearbeiten, damit Sie ein Verständnis dafür entwickeln, was Sie erstellen können. Besuchen Sie unsere [Dokumentationsseite](http://azure.microsoft.com/documentation/services/search/), um weitere Ressourcen zu finden. Sie können auch die Links in unserer [Video- und Lernprogrammliste](search-video-demo-tutorial-list.md) besuchen, um auf weitere Informationen zuzugreifen.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png

<!---HONumber=AcomDC_1203_2015-->