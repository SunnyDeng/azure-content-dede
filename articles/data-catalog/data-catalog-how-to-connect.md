<properties
   pageTitle="Herstellen einer Verbindung mit Datenquellen"
   description="Anleitungsartikel zum Herstellen einer Verbindung mit Datenquellen, die mit Azure Data Catalog gefunden wurden."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="11/10/2015"
   ms.author="maroche"/>


# Herstellen einer Verbindung mit Datenquellen

## Einführung
**Microsoft Azure Data Catalog** ist ein vollständig verwalteter Clouddienst, der als Registrierungs- und Ermittlungssystem für Datenquellen von Unternehmen dient. Mit anderen Worten ist es die Aufgabe von **Azure Data Catalog**, den Benutzern zu helfen, Datenquellen zu ermitteln, zu verstehen und zu nutzen, und Unternehmen zu helfen, mehr Nutzen aus ihren vorhandenen Daten zu ziehen. Ein wichtiger Aspekt dieses Szenarios ist die Verwendung der Daten. Nachdem ein Benutzer eine Datenquelle gefunden und ihren Zweck verstanden hat, besteht der nächste Schritt darin, eine Verbindung mit der Datenquelle herzustellen, um die darin enthaltenen Daten nutzen zu können.

##Speicherorte von Datenquellen
Während der Registrierung einer Datenquelle empfängt **Azure Data Catalog** Metadaten über die Datenquelle. Zu diesen Metadaten zählen die Details zum Speicherort der Datenquelle. Die Details zum Speicherort variieren von Datenquelle zu Datenquelle, enthalten jedoch immer die zum Herstellen einer Verbindung erforderlichen Informationen. Der Speicherort für eine SQL Server-Tabelle enthält z. B. den Servernamen, Datenbanknamen, Schemanamen und Tabellennamen, während der Speicherort für einen SQL Server Reporting Services-Bericht den Namen des Servers und den Pfad zum Bericht enthält. Bei anderen Datenquellentypen gibt der Speicherort die Struktur und die Funktionen des Quellsystems wieder.

##Integrierte Clienttools
Die einfachste Methode zum Herstellen einer Verbindung mit einer Datenquelle ist das Menü „Öffnen in“ im **Azure Data Catalog**-Portal. Dieses Menü enthält eine Liste mit Optionen, die zum Herstellen einer Verbindung mit der ausgewählten Datenressource verwendet werden können. In der standardmäßigen Kachelansicht ist dieses Menü auf jeder Kachel verfügbar.

 ![Öffnen einer SQL Server-Tabelle in Excel über die Datenressourcenkachel](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Bei Verwendung der Listenansicht ist das Menü in der Suchleiste am oberen Rand des Portalfensters verfügbar.

 ![Öffnen eines SQL Server Reporting Services-Berichts im Berichts-Manager über die Suchleiste](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

##Ihre Daten, Ihre Tools
Die im Menü verfügbaren Optionen hängen vom Typ der momentan ausgewählten Datenressource ab. Das Menü „Öffnen in“ enthält natürlich nicht alle möglichen Tools. Trotzdem können Sie mühelos mit jedem beliebigen Clienttool eine Verbindung mit der Datenquelle herstellen. Wenn eine Datenressource im **Azure Data Catalog**-Portal ausgewählt ist, wird der vollständige Speicherort im Eigenschaftenbereich angezeigt.

 ![Verbindungsinformationen für eine SQL Server-Tabelle](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Die Verbindungsdetails variieren je nach Datenquellentyp, das Portal enthält jedoch alle Informationen, die Sie benötigen, um in jedem Clienttool eine Verbindung mit der Datenquelle herzustellen. Benutzer können die Verbindungsdetails für die mit **Azure Data Catalog** gefundenen Datenquellen kopieren und im Tool ihrer Wahl mit den Daten arbeiten.

##Herstellen einer Verbindung und Berechtigungen für Datenquellen
**Azure Data Catalog** macht Datenquellen zwar auffindbar, der Zugriff auf die Daten selbst wird aber nach wie vor vom Datenquellenbesitzer oder Administrator gesteuert. Benutzer erhalten nicht automatisch Zugriffsberechtigungen für die Datenquellen, die sie in **Azure Data Catalog** finden.

Um es einfacher zu machen für Benutzer, die eine Datenquelle finden, aber keine Zugriffsberechtigung besitzen, können Benutzer der Datenquelle eine Anmerkung hinzufügen und dabei in der Eigenschaft „Zugriff anfordern“ Informationen angeben. Die dort angegebenen Informationen (einschließlich Links zum Prozess oder Ansprechpartner für den Zugriff auf die Datenquelle) werden zusammen mit den Informationen zum Speicherort der Datenquelle im Portal angezeigt.

 ![Verbindungsinformationen mit Anweisungen zum Anfordern des Zugriffs](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

##Zusammenfassung
Durch die Registrierung einer Datenquelle in **Azure Data Catalog** werden strukturelle und beschreibende Metadaten aus der Datenquelle in den Katalogdienst kopiert, sodass die darin enthaltenen Daten leichter auffindbar sind. Nachdem eine Datenquelle registriert und gefunden wurde, können Benutzer über das Menü „Öffnen in“ im **Azure Data Catalog**-Portal oder mit ihren bevorzugten Tools eine Verbindung mit der Datenquelle herstellen.

<!---HONumber=Nov15_HO3-->