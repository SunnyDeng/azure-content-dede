<properties 
	pageTitle="Power BI-Tutorial für den DocumentDB-Connector | Microsoft Azure" 
	description="Verwenden Sie dieses Power BI-Tutorial, um JSON zu importieren, aufschlussreiche Berichte zu erstellen und Daten mithilfe des DocumentDB- und Power BI-Connectors zu visualisieren." 
	keywords="Power BI-Tutorial, Daten visualisieren, Power BI-Connector"
	services="documentdb" 
	authors="h0n" 
	manager="jhubbard" 
	editor="mimig" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2015" 
	ms.author="hawong"/>

# Power BI-Tutorial für DocumentDB: Visualisieren von Daten mithilfe des Power BI-Connectors
[PowerBI.com](https://powerbi.microsoft.com/) ist ein Onlinedienst zum Erstellen und Freigeben von Dashboards und Berichten mit Daten, die für Sie und Ihr Unternehmen wichtig sind. Power BI Desktop ist ein dediziertes Berichterstellungstool, mit dem Sie Daten aus verschiedenen Datenquellen abrufen, die Daten zusammenführen und transformieren, leistungsstarke Berichte und Visualisierungen erstellen und Berichte in Power BI veröffentlichen können. Mit der neuesten Version von Power BI Desktop können Sie jetzt über den DocumentDB-Connector für Power BI eine Verbindung mit Ihrem DocumentDB-Konto herstellen.

In diesem Power BI-Tutorial gehen wir die Schritte zum Herstellen einer Verbindung mit einem DocumentDB-Konto in Power BI Desktop durch, navigieren zu einer Sammlung, aus der die Daten mithilfe des Navigators extrahiert werden sollen, transformieren JSON-Daten mit dem Abfrage-Editor von Power BI Desktop in ein Tabellenformat und erstellen und veröffentlichen einen Bericht in PowerBI.com.

Nach Abschluss dieses Power BI-Tutorials können Sie die folgenden Fragen beantworten:

-	Wie kann ich mit Power BI Desktop Berichte mit Daten aus DocumentDB erstellen? 
-	Wie kann ich in Power BI Desktop eine Verbindung mit einem DocumentDB-Konto herstellen?
-	Wie kann ich Daten aus einer Sammlung in Power BI Desktop abrufen?
-	Wie kann ich verschachtelte JSON-Daten in Power BI Desktop transformieren?
-	Wie kann ich meine Berichte in PowerBI.com veröffentlichen und freigeben? 

## Voraussetzungen

Vergewissern Sie sich vor dem Ausführen der Anweisungen dieses Power BI-Tutorials, dass Sie über Folgendes verfügen:

- [Die neueste Version von Power BI Desktop](https://powerbi.microsoft.com/desktop).
- Zugriff auf unser Demokonto oder Daten in Ihrem Azure DocumentDB-Konto. 
	- Das Demokonto ist mit den Daten zu Vulkanen gefüllt, die in diesem Tutorial enthalten sind. Für dieses Demokonto gelten keine SLAs, und es dient nur zur Demonstration. Wir behalten uns das Recht vor, Änderungen an diesem Demokonto vorzunehmen, dazu gehören u. a. das Kündigen des Kontos, das Ändern des Schlüssels, das Einschränken des Zugriffs, das Ändern und Löschen der Daten sowie weitere Änderungen, jederzeit und ohne Vorankündigung oder Grund. 
		- URL: https://analytics.documents.azure.com
		- Schlüssel mit Leseberechtigung: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
	- Sie können aber auch ein eigenes Konto erstellen. Weitere Informationen finden Sie unter [Erstellen eines DocumentDB-Datenbankkontos über das Azure-Portal](https://azure.microsoft.com/documentation/articles/documentdb-create-account/). Um anschließend Beispieldaten zu Vulkanen abzurufen, die den in diesem Tutorial verwendeten ähneln (aber keine GeoJSON-Blöcke enthalten), besuchen Sie die [NOAA-Website](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) (in englischer Sprache), und importieren Sie dann die Daten mithilfe des [DocumentDB-Datenmigrationstools](https://azure.microsoft.com/documentation/articles/documentdb-import-data/).


Zum Freigeben von Berichten in PowerBI.com müssen Sie über ein Konto in PowerBI.com verfügen. Weitere Informationen zur kostenlosen Power BI-Version und zu Power BI Pro erhalten Sie unter [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## Erste Schritte
In diesem Tutorial wird angenommen, dass Sie als Geologe Vulkane auf der ganzen Welt studieren. Die Daten zu Vulkanen werden in einem DocumentDB-Konto gespeichert, und die JSON-Dokumente sehen wie das folgende aus.

	{
    	"Volcano Name": "Rainier",
   		"Country": "United States",
  		"Region": "US-Washington",
  		"Location": {
    		"type": "Point",
    		"coordinates": [
      		-121.758,
      		46.87
    		]
  		},
  		"Elevation": 4392,
  		"Type": "Stratovolcano",
  		"Status": "Dendrochronology",
  		"Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
	}	

Sie möchten die Daten zu Vulkanen aus dem DocumentDB-Konto abrufen und in einem interaktiven Power BI-Bericht wie dem folgenden visualisieren.

![Nach Abschluss dieses Power BI-Tutorials mit dem Power BI-Connector können Sie Daten mit dem Power BI Desktop-Bericht zu Vulkanen visualisieren.](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

Sind Sie bereit, es zu versuchen? Lassen Sie uns anfangen.


1. Führen Sie Power BI Desktop auf Ihrer Arbeitsstation aus.
2. Nach dem Start von Power BI Desktop wird eine *Willkommensseite* angezeigt.

	![Power BI Desktop – Willkommensseite – Power BI-Connector](./media/documentdb-powerbi-visualize/power_bi_connector_welcome.png)

3. Direkt über die *Willkommensseite* können Sie **Daten abrufen**, **aktuelle Quellen** anzeigen oder **andere Berichte öffnen**. Klicken Sie oben rechts auf das X, um das Dialogfeld zu schließen. Die Ansicht **Bericht** von Power BI Desktop wird angezeigt.

	![Power BI Desktop-Berichtsansicht – Power BI-Connector](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview.png)

4. Wählen Sie das Menüband **Start** aus, und klicken Sie dann auf **Daten abrufen**. Das Fenster **Daten abrufen** wird angezeigt.

5. Klicken Sie auf **Azure**, wählen Sie **Microsoft Azure DocumentDB (Beta)** aus, und klicken Sie dann auf **Verbinden**. Das Fenster **Verbindung mit Microsoft Azure DocumentDB** wird angezeigt.

	![Power BI Desktop-Datenabruf – Power BI-Connector](./media/documentdb-powerbi-visualize/power_bi_connector_pbigetdata.png)

6. Geben Sie die Endpunkt-URL des DocumentDB-Kontos an, von dem Sie die Daten abrufen möchten, wie unten dargestellt, und klicken Sie dann auf **OK**. Sie können die URL aus dem URI-Feld auf dem Blatt **Schlüssel** des Azure-Portals nutzen, oder Sie können die oben angegebenen Informationen für das Demokonto verwenden. Weitere Informationen finden Sie unter [Schlüssel](documentdb-manage-account.md#keys).


	*Hinweis: In diesem Tutorial geben wir keinen Datenbanknamen, Sammlungsnamen bzw. keine SQL-Anweisung an, da diese Felder optional sind. Stattdessen verwenden wir den Navigator zum Auswählen der Datenbank und der Sammlung, um zu bestimmen, woher die Daten stammen.*

    ![Power BI-Tutorial für den Power BI-Connector für DocumentDB – Fenster für Desktopverbindung](./media/documentdb-powerbi-visualize/power_bi_connector_pbiconnectwindow.png)

7. Wenn Sie zum ersten Mal eine Verbindung mit diesem Endpunkt herstellen, werden Sie aufgefordert, den Kontoschlüssel anzugeben. Geben Sie den Kontoschlüssel an, und klicken Sie auf **Verbinden**.
	
	*Hinweis: Es wird empfohlen, den Schlüssel mit Leseberechtigung beim Erstellen von Berichten zu verwenden. Dadurch wird verhindert, dass der Hauptschlüssel unnötig potenziellen Sicherheitsrisiken ausgesetzt wird. Der Schlüssel mit Leseberechtigung ist auf dem Blatt „Schlüssel mit Leseberechtigung“ des Azure-Portals verfügbar. Alternativ können Sie die oben angegebenen Informationen für das Demokonto verwenden.*

    ![Power BI-Tutorial für den Power BI-Connector für DocumentDB – Kontoschlüssel](./media/documentdb-powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)

8. Wenn das Konto erfolgreich verbunden wurde, wird der **Navigator** angezeigt. Der **Navigator** zeigt eine Liste der Datenbanken für das Konto an.
9. Klicken Sie auf die Datenbank, aus der die Daten für den Bericht kommen sollen, und erweitern Sie sie. Eine Liste der Sammlungen in der Datenbank wird angezeigt.  

10. Wählen Sie jetzt eine Sammlung aus, von der Sie Daten abrufen möchten, z. B. „volcano1“.

	*Hinweis: Im Vorschaubereich wird eine Liste der **Datensatz**-Elemente angezeigt. Ein Dokument wird mit dem Typ **Datensatz** in Power BI dargestellt. Auch ein geschachtelter JSON-Block innerhalb eines Dokuments ist ein **Datensatz**.*

    ![Power BI-Tutorial für den Power BI-Connector für DocumentDB – Navigatorfenster](./media/documentdb-powerbi-visualize/power_bi_connector_pbinavigator.png)

11. Klicken Sie auf **Bearbeiten**, um den Abfrage-Editor zu starten, damit wir die Daten transformieren können.

## Vereinfachen und Transformieren von JSON-Dokumenten
1. Im Abfrage-Editor von Power BI wird im mittleren Bereich die Spalte **Dokument** angezeigt. ![Power BI Desktop – Abfrage-Editor](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditor.png)

2. Klicken Sie auf das Erweiterungssteuerelement rechts in der Spaltenüberschrift **Dokument**. Das Kontextmenü mit einer Liste von Feldern wird angezeigt. Wählen Sie die Felder aus, die Sie für Ihren Bericht benötigen, z. B. „Volcano Name“, „Country“, „Region“, „Location“, „Elevation“, „Type“, „Status“ und „Last Know Eruption“. Klicken Sie dann auf **OK**.
    
	![Power BI-Tutorial für den Power BI-Connector für DocumentDB – Erweitern von Dokumenten](./media/documentdb-powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)

3. Im mittleren Bereich wird eine Vorschau des Ergebnisses mit den ausgewählten Feldern angezeigt.

	![Power BI-Tutorial für den Power BI-Connector für DocumentDB – Einfügen von Ergebnissen](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflatten.png)

4. In diesem Beispiel ist die Eigenschaft „Location“ ein GeoJSON-Block in einem Dokument. Wie Sie sehen können, wird „Location“ mit dem Typ **Datensatz** in Power BI Desktop dargestellt.
5. Klicken Sie rechts neben der Spaltenüberschrift „Location“ auf das Erweiterungssteuerelement. Das Kontextmenü mit Typ- und Koordinatenfeldern wird angezeigt. Wir wählen das Koordinatenfeld aus und klicken auf **OK**.

    ![Power BI-Tutorial für den Power BI-Connector für DocumentDB – Standortdatensatz](./media/documentdb-powerbi-visualize/power_bi_connector_pbilocationrecord.png)

6. Im mittleren Bereich wird nun eine Spalte „coordinates“ mit dem Typ **Liste** angezeigt. Wie am Anfang des Tutorials dargestellt, weisen die GeoJSON-Daten in diesem Tutorial den Typ „Punkt“ auf. Die Werte für den Breiten- und Längengrad wurden im Koordinatenarray aufgezeichnet.

	*Hinweis: Das Element „ coordinates[0]“ stellt den Längengrad und „coordinates[1]“ den Breitengrad dar.* ![Power BI-Tutorial für den Power BI-Connector für DocumentDB – Koordinatenliste](./media/documentdb-powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)

7. Um das Koordinatenarray zu vereinfachen, erstellen wir eine **benutzerdefinierte Spalte** mit dem Namen „LatLong“. Wählen Sie das Menüband **Spalte hinzufügen** aus, und klicken Sie auf **Benutzerdefinierte Spalte hinzufügen**. Das Fenster **Benutzerdefinierte Spalte hinzufügen** wird angezeigt.

8. Geben Sie einen Namen für die neue Spalte an, z. B. „LatLong“.

9. Geben Sie dann die benutzerdefinierte Formel für die neue Spalte ein. In unserem Beispiel verketten wir die Werte für Breiten- und Längengrad, getrennt durch ein Komma, wie unten dargestellt, mit der folgenden Formel: Text.From([coordinates]{1})&","&Text.From([coordinates]{0}). Klicken Sie auf **OK**.
	
    *Hinweis: Weitere Informationen zu DAX (Data Analysis Expressions), einschließlich der DAX-Funktionen, finden Sie unter [DAX Basic in Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop) (in englischer Sprache).*

    ![Power BI-Tutorial für den Power BI-Connector für DocumentDB – Hinzufügen einer benutzerdefinierten Spalte](./media/documentdb-powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Jetzt wird im mittleren Bereich die neue LatLong-Spalte mit den Werten für Breiten- und Längengrade, die durch ein Komma getrennt sind, angezeigt.

	![Power BI-Tutorial für den Power BI-Connector für DocumentDB – Benutzerdefinierte „LatLong“-Spalte](./media/documentdb-powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)

11. Jetzt ist das Vereinfachen der Daten in ein Tabellenformat abgeschlossen. Sie können sämtliche im Abfrage-Editor verfügbare Funktionen zum Formen und Transformieren von Daten in DocumentDB nutzen. Sie können z. B. den Datentyp für „Elevation“ in **Dezimalzahl** ändern, indem Sie den **Datentyp** im Menüband **Start** ändern.

    ![Power BI-Tutorial für den Power BI-Connector für DocumentDB – Ändern des Spaltentyps](./media/documentdb-powerbi-visualize/power_bi_connector_pbichangetype.png)

12. Klicken Sie auf **Schließen und übernehmen**, um das Datenmodell zu speichern.
    
    ![Power BI-Tutorial für den Power BI-Connector für DocumentDB – Schließen und anwenden](./media/documentdb-powerbi-visualize/power_bi_connector_pbicloseapply.png)

## Erstellen der Berichte
In der Berichtsansicht von Power BI Desktop können Sie Berichte erstellen, um Daten zu visualisieren. Sie können Berichte erstellen, indem Sie Felder in den Zeichenbereich **Bericht** ziehen.

![Power BI Desktop-Berichtsansicht – Power BI-Connector](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportview2.png)
 
In der Berichtsansicht wird Folgendes angezeigt:

 1. Der Bereich **Felder**, in dem eine Liste von Datenmodellen mit Feldern, die Sie für Ihre Berichte verwenden können, angezeigt wird.

 2. Der Bereich **Visualisierungen**. Ein Bericht kann eine einzelne oder mehrere Visualisierungen enthalten. Wählen Sie die zu Ihren Bedürfnissen passenden Visualisierungstypen im Bereich **Visualisierungen** aus.

 3. Der Zeichenbereich **Bericht**, in dem Sie die visuelle Struktur für den Bericht erstellen.

 4. Die Seite **Bericht**. Sie können mehrere Berichtsseiten in Power BI Desktop hinzufügen.

Das folgende Beispiel zeigt die grundlegenden Schritte zum Erstellen eines einfachen interaktiven Berichts mit einer Kartenansicht.

1. In unserem Beispiel erstellen wir eine Kartenansicht, die den Standort der einzelnen Vulkane anzeigt. Klicken Sie im Bereich **Visualisierungen** auf den Visualisierungstyp für Karten, wie im Screenshot oben dargestellt. Der Visualisierungstyp für Karten wird im Zeichenbereich **Bericht** angezeigt. Zudem wird im Bereich **Visualisierung** ein Satz von Eigenschaften angezeigt, die zum Visualisierungstyp für Karten gehören.

2. Ziehen Sie jetzt das Feld „LatLong“ aus dem Bereich **Felder** auf die Eigenschaft **Standort** im Bereich **Visualisierungen**.
3. Ziehen Sie dann das Feld „Volcano Name“ auf die Eigenschaft **Legende**.  

4. Ziehen Sie anschließend das Feld „Elevation“ auf die Eigenschaft**Werte**.

5. Jetzt sollte eine Karte mit einer Reihe von Blasen angezeigt werden. Die Blasen markieren den Standort der einzelnen Vulkane, und die Größe der Blasen korreliert mit der Höhe (Elevation) der Vulkane.

6. Sie haben jetzt einen einfachen Bericht erstellt. Sie können den Bericht weiter anpassen, indem Sie weitere Visualisierungen hinzufügen. In unserem Fall haben wir einen Datenschnitt für den Vulkantyp hinzugefügt, um den Bericht interaktiv zu gestalten.

    ![Screenshot des Power BI Desktop-Abschlussberichts nach Abschluss des Power BI-Tutorials für DocumentDB](./media/documentdb-powerbi-visualize/power_bi_connector_pbireportfinal.png)

## Veröffentlichen und Freigeben des Berichts
Zum Freigeben des Berichts müssen Sie über ein Konto in PowerBI.com verfügen.

1. Klicken Sie in Power BI Desktop auf das Menüband **Start**.
2. Klicken Sie auf **Veröffentlichen**. Sie werden aufgefordert, den Benutzernamen und das Kennwort für Ihr PowerBI.com-Konto einzugeben.
3. Sobald die Anmeldeinformationen authentifiziert wurden, wird der Bericht in Ihrem PowerBI.com-Konto veröffentlicht.
4. Sie können den Bericht dann in PowerBI.com freigeben.

## Nächste Schritte
- Weitere Informationen zu Power BI erhalten Sie [hier](https://support.powerbi.com/knowledgebase).
- Um weitere Informationen zu DocumentDB zu erhalten, klicken Sie [hier](https://azure.microsoft.com/documentation/services/documentdb/).

<!---HONumber=AcomDC_0128_2016-->