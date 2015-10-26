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
   ms.date="07/30/2015"
   ms.author="nadavh; ronmat; v-romcal; sstein"/>

# Erste Schritte mit der dynamischen Datenmaskierung für SQL-Datenbanken (Azure-Vorschauportal)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure portal](sql-database-dynamic-data-masking-get-started-portal.md)

## Übersicht

Die dynamische Datenmaskierung für SQL-Datenbanken schränkt die Offenlegung vertraulicher Daten ein, indem sie für nicht berechtigte Benutzer maskiert werden. Die dynamische Datenmaskierung wird als Vorschau für die Dienstebenen Basic, Standard und Premium der Version V12 von Azure SQL-Datenbank angeboten.

Die dynamische Datenmaskierung hilft beim Verhindern des unbefugten Zugriffs auf sensible Daten, indem Kunden festlegen dürfen, welcher Anteil der sensiblen Daten mit minimalen Auswirkungen auf die Anwendungsschicht offengelegt wird. Es handelt sich um eine richtlinienbasierte Sicherheitsfunktion, die die sensiblen Daten im Resultset einer Abfrage in festgelegten Datenbankfeldern ausblendet, ohne dass die Daten in der Datenbank geändert werden.

In einem Callcenter kann ein Supportmitarbeiter beispielsweise Anrufer anhand mehrerer Ziffern ihrer Sozialversicherungs- oder Kreditkartennummer identifizieren, wobei diese Datenelemente dem Supportmitarbeiter jedoch nicht vollständig angezeigt werden sollen. Ein Entwickler kann eine Maskierungsregel, die auf jedes Abfrageergebnis angewendet wird, so definieren, dass alle Ziffern von Sozialversicherungs- oder Kreditkartennummern bis auf die letzten vier im Resultset maskiert werden. In einem weiteren Beispiel kann ein Entwickler durch Verwenden der entsprechenden Datenmaske zum Schutz personenbezogener Daten Produktionsumgebungen zu Problembehandlungszwecken abfragen, ohne gegen Vorschriften zu verstoßen.

## Grundlagen der dynamischen Datenmaskierung für SQL-Datenbanken

Sie richten eine Richtlinie für die dynamische Datenmaskierung im Azure-Vorschauportal durch Auswählen des Vorgangs „Dynamische Datenmaskierung“ auf Ihrem SQL-Datenbank-Konfigurationsblatt ein. Überprüfen Sie vor der Einrichtung der dynamischen Datenmaskierung, ob Sie einen [kompatiblen Client](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) verwenden.


### Berechtigungen für die dynamische Datenmaskierung

Die dynamische Datenmaskierung kann von den Rollen "Azure-Datenbankadministrator", "Serveradministrator" oder "Sicherheitsbeauftragter" konfiguriert werden.

### Richtlinie für die dynamische Datenmaskierung

* **Privilegierte Anmeldungen:** eine Gruppe von Anmeldungen, die Daten ohne Maskierung in den SQL-Abfrageergebnissen erhalten.
  
* **Maskierungsregeln:** eine Gruppe von Regeln, die die zu maskierenden Felder und verwendete Maskierungsfunktion definieren. Mithilfe eines Datenbanktabellen- und Spaltennamens können die vorgesehenen Felder bestimmt werden.

* **Maskierungsfunktionen:** eine Reihe von Methoden, die die Anzeige von Daten in verschiedenen Szenarios steuern.

| Maskierungsfunktion | Maskierungslogik |
|----------|---------------|
| **Standard** |**Vollständige Maskierung anhand des Datentyps der angegebenen Felder**<br/><br/>• XXXXXXXX oder weniger X-Zeichen verwenden, wenn die Feldbreite weniger als 8 Zeichen für Zeichenfolgendatentypen (nchar, ntext, nvarchar) beträgt.<br/>• Für numerische Datentypen (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real) einen Nullwert verwenden.<br/>• Für Datum/Uhrzeit-Datentypen (date, datetime2, datetime, datetimeoffset, smalldatetime, time) 01-01-1900 verwenden.<br/>• Für SQL-Varianten wird der Standardwert des aktuellen Typs verwendet.<br/>• Für XML wird das Dokument <masked/> verwendet.<br/>• Für spezielle Datentypen (timestamp table, hierarchyid, GUID, binary, image, räumliche varbinary-Typen) einen leeren Wert verwenden.
| **Kreditkarte** |**Maskierungsmethode, die die letzten vier Ziffern der festgelegten Felder anzeigt** und eine Konstantenzeichenfolge als Präfix in Form einer Kreditkarte hinzufügt.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Sozialversicherungsnummer** |**Maskierungsmethode, die die letzten zwei Ziffern der festgelegten Felder anzeigt** und eine Konstantenzeichenfolge als Präfix in Form einer US-amerikanischen Sozialversicherungsnummer hinzufügt.<br/><br/>XXX-XX-XX12 |
| **E-Mail** | **Maskierungsmethode, die den ersten Buchstaben und die Domäne durch XXX.com ersetzt,** und dafür eine Konstantenzeichenfolge als Präfix in Form einer E-Mail-Adresse verwendet.<br/><br/>aXX@XXXX.com |
| **Zufallszahl** | **Maskierungsmethode, die eine Zufallszahl** entsprechend den ausgewählten Grenzen und den tatsächlichen Datentypen generiert. Wenn die festgelegten Grenzen gleich sind, ist die Maskierungsfunktion eine konstante Zahl.<br/><br/>![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Benutzerdefinierter Text** | **Maskierungsmethode, die die ersten und letzten Zeichen anzeigt** und in der Mitte eine benutzerdefinierte Auffüllzeichenfolge hinzufügt.<br/>Präfix[Auffüllzeichenfolge]Suffix<br/><br/>![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

  
<a name="Anchor1"></a>
### Verbindungszeichenfolge mit aktivierter Sicherheit

Bei Verwendung eines [kompatiblen Clients](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) müssen Sie vorhandene Clients aktualisieren (Beispiel: Anwendungen), um ein geändertes Verbindungszeichenfolgenformat zu verwenden. Klicken Sie [hier](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) für weitere Informationen.

## Einrichten der dynamischen Datenmaskierung für Ihre Datenbank im Azure-Vorschauportal

1. Rufen Sie das Azure-Vorschauportal unter [https://portal.azure.com](https://portal.azure.com) auf.
	 
2. Navigieren Sie zum Konfigurationsfenster der Datenbank mit den sensiblen Daten, die Sie maskieren möchten.
	
3. Klicken Sie auf die Kachel **Dynamische Datenmaskierung**, woraufhin das Konfigurationsblatt **Dynamische Datenmaskierung** geöffnet wird.

	* Alternativ können Sie nach unten zum Abschnitt **Vorgänge** scrollen und auf **Dynamische Datenmaskierung** klicken.
	 
	![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/4_DDM_Activation.png)<br/><br/>

4. Klicken Sie im Konfigurationsblatt **Dynamische Datenmaskierung** auf **Maske hinzufügen**, um das Konfigurationsblatt **Maskierungsregel hinfügen** zu öffnen.

	![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/5_ddm_policy_tile.png)<br/><br/>

5. Wählen Sie die **Tabelle** und die **Spalte**, um die Felder zu bestimmen, die maskiert werden sollen.

6. Wählen Sie in der Liste der Kategorien für das Maskieren empfindlicher Daten ein **Maskierungsfeldformat**.

	![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/7_DDM_Add_Masking_Rule.png)<br/><br/>

7. Klicken Sie im Fenster der Datenmaskierungsregel auf **Speichern**, um die Gruppe von Maskierungsregeln in der Richtlinie für die dynamische Datenmaskierung zu aktualisieren.

8. Geben Sie die privilegierten Anmeldungen ein, die Zugriff auf sensible Daten ohne Maskierung haben sollen.
 
	![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/6_DDM_Privileged_Logins.png)

	>[AZURE.TIP]Damit die Anwendungsschicht sensible Daten für die privilegierten Anwendungsbenutzer anzeigen kann, fügen Sie die SQL-Anmeldung der Anwendung hinzu, die zum Abfragen der Datenbank verwendet wird. Es wird ausdrücklich empfohlen, dass diese Liste nur eine minimale Anzahl von Anmeldungen enthält, um die Anzeige sensibler Daten zu minimieren.

9. Klicken Sie im Konfigurationsfenster für die Datenmaskierung auf **Speichern**, um die neue oder aktualisierte Maskierungsrichtlinie zu speichern.

10. Bei Verwendung eines [kompatiblen Clients](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) müssen Sie vorhandene Clients aktualisieren (Beispiel: Anwendungen), um ein geändertes Verbindungszeichenfolgenformat zu verwenden. Weitere Informationen finden Sie unter [Vorgängerversionsclients](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).

## Einrichten der dynamischen Datenmaskierung für Ihre Datenbank mithilfe von Powershell-Cmdlets

Siehe [Azure SQL-Datenbank-Cmdlets](https://msdn.microsoft.com/library/azure/mt163521.aspx).


## Einrichten der dynamischen Datenmaskierung für Ihre Datenbank mithilfe der REST-API

Siehe [Vorgänge für Azure SQL-Datenbanken](https://msdn.microsoft.com/library/dn505719.aspx).

<!---HONumber=Oct15_HO3-->