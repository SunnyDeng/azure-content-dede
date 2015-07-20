<properties 
   pageTitle="Erste Schritte mit der dynamischen Datenmaskierung für SQL-Datenbanken (Azure-Vorschauportal)" 
   description="Einstieg in die dynamische Datenmaskierung für SQL-Datenbanken im Azure-Vorschauportal" 
   services="sql-database" 
   documentationCenter="" 
   authors="nadavhelfman"
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="04/02/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein"/>

# Erste Schritte mit der dynamischen Datenmaskierung für SQL-Datenbanken (Azure-Vorschauportal)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure portal](sql-database-dynamic-data-masking-get-started-portal.md)

## Übersicht

Die dynamische Datenmaskierung für SQL-Datenbanken schränkt die Offenlegung vertraulicher Daten ein, indem sie für nicht berechtigte Benutzer maskiert werden. Die dynamische Datenmaskierung wird als Vorschau für die Dienstebenen Basic, Standard und Premium der Version V12 von Azure SQL-Datenbank angeboten.

Die dynamische Datenmaskierung hilft beim Verhindern des unbefugten Zugriffs auf sensible Daten, indem Kunden festlegen dürfen, welcher Anteil der sensiblen Daten mit minimalen Auswirkungen auf die Anwendungsschicht offengelegt wird. Es handelt sich um eine richtlinienbasierte Sicherheitsfunktion, die die sensiblen Daten im Resultset einer Abfrage in festgelegten Datenbankfeldern ausblendet, ohne dass die Daten in der Datenbank geändert werden.

In einem Callcenter kann ein Supportmitarbeiter beispielsweise Anrufer anhand mehrerer Ziffern ihrer Sozialversicherungs- oder Kreditkartennummer identifizieren, wobei diese Datenelemente dem Supportmitarbeiter jedoch nicht vollständig angezeigt werden sollen. Ein Entwickler kann eine Maskierungsregel, die auf jedes Abfrageergebnis angewendet wird, so definieren, dass alle Ziffern von Sozialversicherungs- oder Kreditkartennummern bis auf die letzten vier im Resultset maskiert werden. In einem weiteren Beispiel kann ein Entwickler durch Verwenden der entsprechenden Datenmaske zum Schutz personenbezogener Daten Produktionsumgebungen zu Problembehandlungszwecken abfragen, ohne gegen Vorschriften zu verstoßen.

## Grundlagen der dynamischen Datenmaskierung für SQL-Datenbanken

Sie richten die dynamische Datenmaskierungsrichtlinie im Azure-Vorschauportal ein und schließen die Einrichtung ab, indem Sie die sicherheitsaktivierte Verbindungszeichenfolge einsetzen, die von der Anwendung oder anderen Clients genutzt wird, die auf die Datenbank zugreifen.

### Berechtigungen für die dynamische Datenmaskierung

Die dynamische Datenmaskierung kann von den Rollen "Azure-Datenbankadministrator", "Serveradministrator" oder "Sicherheitsbeauftragter" konfiguriert werden.

### Richtlinie für die dynamische Datenmaskierung

* **Privilegierte Anmeldungen:** eine Gruppe von Anmeldungen, die Daten ohne Maskierung in den SQL-Abfrageergebnissen erhalten.
  
* **Maskierungsregeln:** eine Gruppe von Regeln, die die zu maskierenden Felder und verwendete Maskierungsfunktion definieren. Mithilfe eines Datenbanktabellen- und Spaltennamens bzw. eines Aliasnamens können die vorgesehenen Felder bestimmt werden.

* **Maskieren nach:** kann in Quelle oder Ziel erfolgen. Die Maskierung kann auf Quellebene konfiguriert werden, indem der **Tabellenname** und der **Spaltenname** bestimmt werden, oder auf Ergebnisebene, indem der in der Abfrage verwendete **Alias** bestimmt wird. Wenn Sie mit der Datenarchitektur Ihrer Datenbank vertraut sind und die Anzeige aller Abfrageergebnisse begrenzen möchten, bevorzugen Sie ggf. Sie eine Quellmaskenregel. Sie können eine Ergebnismaskenregel hinzufügen, wenn Sie die Anzeige auf Abfrageergebnisse beschränken möchten, ohne die Datenarchitektur der Datenbank zu analysieren, oder auf ein Feld, das aus verschiedenen Quellen stammen kann.
  
* **Erweiterte Einschränkung:** schränkt die Anzeige sensibler Daten ein, kann sich aber negativ auf die Funktionalität einiger Anwendungen auswirken.

>[AZURE.TIP]Verwenden Sie die Option **Erweiterte Beschränkung** zum Beschränken des Zugriffs für Entwickler, die einen direkten Zugriff auf die Datenbank nutzen und SQL-Abfragen zu Problembehandlungszwecken ausführen.

* **Maskierungsfunktionen:** eine Reihe von Methoden, die die Anzeige von Daten in verschiedenen Szenarios steuern.

| Maskierungsfunktion | Maskierungslogik |
|----------|---------------|
| **Standard** |**Vollständige Maskierung anhand des Datentyps der angegebenen Felder**<br/><br/>• XXXXXXXX oder weniger X-Zeichen verwenden, wenn die Feldbreite weniger als 8 Zeichen für Zeichenfolgendatentypen (nchar, ntext, nvarchar) beträgt.<br/>• Für numerische Datentypen (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real) einen Nullwert verwenden.<br/>• Für Datum/Uhrzeit-Datentypen (date, datetime2, datetime, datetimeoffset, smalldatetime, time) die aktuelle Uhrzeit verwenden.<br/>• Für SQL-Varianten wird der Standardwert des aktuellen Typs verwendet.<br/>• Für XML wird das Dokument <masked/> verwendet.<br/>• Für spezielle Datentypen (timestamp table, hierarchyid, GUID, binary, image, räumliche varbinary-Typen) einen leeren Wert verwenden.
| **Kreditkarte** |**Maskierungsmethode, die die letzten vier Ziffern der festgelegten Felder anzeigt** und eine Konstantenzeichenfolge als Präfix in Form einer Kreditkarte hinzufügt.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Sozialversicherungsnummer** |**Maskierungsmethode, die die letzten zwei Ziffern der festgelegten Felder anzeigt** und eine Konstantenzeichenfolge als Präfix in Form einer US-amerikanischen Sozialversicherungsnummer hinzufügt.<br/><br/>XXX-XX-XX12 |
| **E-Mail** | **Maskierungsmethode, die den ersten Buchstaben und die Domäne anzeigt** und eine Konstantenzeichenfolge als Präfix in Form einer E-Mail-Adresse verwendet.<br/><br/>aXX@XXXX.com |
| **Zufallszahl** | **Maskierungsmethode, die eine Zufallszahl** entsprechend den ausgewählten Grenzen und den tatsächlichen Datentypen generiert. Wenn die festgelegten Grenzen gleich sind, ist die Maskierungsfunktion eine konstante Zahl.<br/><br/>![Navigationsbereich][Image1] |
| **Benutzerdefinierter Text** | **Maskierungsmethode, die den ersten und letzten Buchstaben anzeigt** und in der Mitte eine benutzerdefinierte Auffüllzeichenfolge hinzufügt.<br/>Präfix[Auffüllzeichenfolge]Suffix<br/><br/>![Navigationsbereich][Image2] |

  
<a name="Anchor1"></a>
### Verbindungszeichenfolge mit aktivierter Sicherheit

Beim Einrichten der dynamische Datenmaskierung stellt Azure für die Datenbank eine Verbindungszeichenfolge mit aktivierter Sicherheit bereit. Nur bei Clients, die diese Verbindungszeichenfolge verwenden, werden sensible Daten gemäß der Richtlinie für die dynamische Datenmaskierung maskiert. Sie müssen auch vorhandene Clients (z. B. Anwendungen) so aktualisieren, dass sie das neue Format der Verbindungszeichenfolge verwenden.

* Ursprüngliches Format der Verbindungszeichenfolge: <*Servername*>.database.windows.net
* Sicherheitsaktivierte Verbindungszeichenfolge: <*Servername*>.database.**secure**.windows.net

Sie können auch die Einstellung **FÜR SICHERHEIT AKTIVIERTER ZUGRIFF** von **OPTIONAL** in **ERFORDERLICH** ändern und dadurch sicherstellen, dass es keine Möglichkeit gibt, auf die Datenbank mit der ursprünglichen Verbindungszeichenfolge zuzugreifen und die Richtlinie für die dynamische Datenmaskierung zu ignorieren. Wenn Sie mit der dynamischen Datenmaskierung unter Verwendung bestimmter Clients (z. B. einer Anwendung in der Entwicklungsphase oder SSMS) experimentieren, wählen Sie **OPTIONAL** aus. Wählen Sie für Produktionsumgebungen **ERFORDERLICH** aus.<br/><br/>

![Navigationsbereich][Image3]<br/><br/>

## Einrichten der dynamischen Datenmaskierung für Ihre Datenbank im Azure-Vorschauportal

1. Rufen Sie das Azure-Vorschauportal unter [https://portal.azure.com](https://portal.azure.com) auf.
	 
2. Navigieren Sie zum Konfigurationsfenster der Datenbank mit den sensiblen Daten, die Sie maskieren möchten.
	
3. Klicken Sie auf die Kachel **Dynamische Datenmaskierung**, woraufhin das Konfigurationsblatt **Dynamische Datenmaskierung** geöffnet wird.

	* Alternativ können Sie nach unten zum Abschnitt **Vorgänge** scrollen und auf **Dynamische Datenmaskierung** klicken.
	 
	![Navigationsbereich][Image4]<br/><br/>

4. Klicken Sie im Konfigurationsblatt **Dynamische Datenmaskierung** auf **AKTIVIERT**, um diese Funktion zu aktivieren.

	![Navigationsbereich][Image5]<br/><br/>

5. Geben Sie die privilegierten Anmeldungen ein, die Zugriff auf sensible Daten ohne Maskierung haben sollen.
 
	![Navigationsbereich][Image6]

	>[AZURE.TIP]Damit die Anwendungsschicht sensible Daten für die privilegierten Anwendungsbenutzer anzeigen kann, fügen Sie die SQL-Anmeldung der Anwendung, die zum Abfragen der Datenbank verwendet wird, und die Datenbank hinzu. Es wird ausdrücklich empfohlen, dass diese Liste nur eine minimale Anzahl von Anmeldungen enthält, um die Anzeige sensibler Daten zu minimieren.

6. Klicken Sie auf **Maske hinzufügen**, um das Konfigurationsblatt **Maskierungsregel hinzufügen** zu öffnen.
	
7. Wählen Sie **Maskieren nach** aus, um anzugeben, ob die Maskierung für die Quelle oder das Ziel erfolgen soll. Die Maskierung kann auf Quellebene konfiguriert werden, indem der **Tabellenname** und der **Spaltenname** bestimmt werden, oder auf Ergebnisebene, indem der in der Abfrage verwendete **Alias** bestimmt wird. Bitte beachten Sie, dass der **Tabellenname** sich auf alle Schemas in der Datenbank bezieht und kein Schemapräfix enthalten sollte. Wenn Sie mit der Datenarchitektur Ihrer Datenbank vertraut sind und die Anzeige aller Abfrageergebnisse begrenzen möchten, bevorzugen Sie ggf. Sie eine Quellmaskenregel. Sie können eine Ergebnismaskenregel hinzufügen, wenn Sie die Anzeige auf Abfrageergebnisse beschränken möchten, ohne die Datenarchitektur der Datenbank zu analysieren, oder auf ein Feld, das aus verschiedenen Quellen stammen kann.

8. Geben Sie den **Tabellennamen** und den **Spaltennamen** bzw. den **Aliasnamen** ein, um die Felder zu bestimmen, die maskiert werden sollen.

9. Wählen Sie in der Liste der Kategorien für das Maskieren empfindlicher Daten ein **Maskierungsfeldformat**.

	![Navigationsbereich][Image7]<br/><br/>

10. Klicken Sie im Fenster der Datenmaskierungsregel auf **Speichern**, um die Gruppe von Maskierungsregeln in der Richtlinie für die dynamische Maskierung zu aktualisieren.

11. Erwägen Sie das Aktivieren von **USE EXTENDED RESTRICTIONS**, um die Anzeige vertraulicher Daten mittels Ad-hoc-Abfragen zu begrenzen.

12. Klicken Sie im Konfigurationsfenster für die Datenmaskierung auf **Speichern**, um die neue oder aktualisierte Maskierungsregel zu speichern.

13. Klicken Sie unter **Für Sicherheit aktivierter Zugriff** auf **OPTIONAL** oder **ERFORDERLICH**. Weitere Informationen finden Sie unter [Für Sicherheit aktivierte Verbindungszeichenfolge](#Anchor1).

	![Navigationsbereich][Image8]
	
## Einrichten der dynamischen Datenmaskierung für Ihre Datenbank mithilfe der REST-API

Siehe [Vorgänge für Azure SQL-Datenbanken](https://msdn.microsoft.com/library/dn505719.aspx).

[Image1]: ./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png
[Image2]: ./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png
[Image3]: ./media/sql-database-dynamic-data-masking-get-started/3_DDM_Current_Preview.png
[Image4]: ./media/sql-database-dynamic-data-masking-get-started/4_DDM_Activation.png
[Image5]: ./media/sql-database-dynamic-data-masking-get-started/5_DMM_Policy_Tile.png
[Image6]: ./media/sql-database-dynamic-data-masking-get-started/6_DDM_Privileged_Logins.png
[Image7]: ./media/sql-database-dynamic-data-masking-get-started/7_DDM_Add_Masking_Rule.png
[Image8]: ./media/sql-database-dynamic-data-masking-get-started/8_DDM_Security_Enabled_Access.png
 

<!---HONumber=July15_HO2-->