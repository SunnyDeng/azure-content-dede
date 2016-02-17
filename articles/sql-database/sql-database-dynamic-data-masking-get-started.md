<properties
   pageTitle="Erste Schritte mit der dynamischen Datenmaskierung für SQL-Datenbanken (Azure-Portal)"
   description="Einstieg in die dynamische Datenmaskierung für SQL-Datenbanken im Azure-Portal"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jeffreyg"
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/01/2015"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>


# Erste Schritte mit der dynamischen Datenmaskierung für SQL-Datenbanken (Azure-Portal)

> [AZURE.SELECTOR]
- [Dynamic Data Masking - Azure Classic Portal](sql-database-dynamic-data-masking-get-started-portal.md)

## Übersicht

Die dynamische Datenmaskierung für SQL-Datenbanken schränkt die Offenlegung vertraulicher Daten ein, indem sie für nicht berechtigte Benutzer maskiert werden. Die dynamische Datenmaskierung wird für die Version V12 von Azure SQL-Datenbank unterstützt.

Die dynamische Datenmaskierung hilft beim Verhindern des unbefugten Zugriffs auf sensible Daten, indem Kunden festlegen dürfen, welcher Anteil der sensiblen Daten mit minimalen Auswirkungen auf die Anwendungsschicht offengelegt wird. Es handelt sich um eine richtlinienbasierte Sicherheitsfunktion, die die sensiblen Daten im Resultset einer Abfrage in festgelegten Datenbankfeldern ausblendet, ohne dass die Daten in der Datenbank geändert werden.

Ein Servicemitarbeiter in einem Callcenter kann Anrufer beispielsweise anhand mehrerer Ziffern ihrer US-Sozialversicherungsnummer oder Kreditkartennummer identifizieren, wobei diese Datenelemente dem Servicemitarbeiter jedoch nicht vollständig angezeigt werden sollen. Es kann eine Maskierungsregel definiert werden, mit der die US-Sozialversicherungsnummer oder Kreditkartennummer im Resultset einer Abfrage bis auf die letzten vier Ziffern ausgeblendet wird. In einem weiteren Beispiel kann eine entsprechende Datenmaske zum Schutz personenbezogener Daten definiert werden, damit ein Entwickler Produktionsumgebungen zu Problembehandlungszwecken abfragen kann, ohne gegen Vorschriften zu verstoßen.

## Grundlagen der dynamischen Datenmaskierung für SQL-Datenbanken

Sie richten eine Richtlinie für die dynamische Datenmaskierung im Azure-Portal durch Auswählen des Vorgangs „Dynamische Datenmaskierung“ auf Ihrem SQL-Datenbank-Konfigurationsblatt oder auf dem Blatt mit den Einstellungen ein.


### Berechtigungen für die dynamische Datenmaskierung

Die dynamische Datenmaskierung kann von den Rollen "Azure-Datenbankadministrator", "Serveradministrator" oder "Sicherheitsbeauftragter" konfiguriert werden.

### Richtlinie für die dynamische Datenmaskierung

* **Von der Maskierung ausgeschlossene SQL-Benutzer:** Eine Gruppe von SQL-Benutzern oder AAD-Identitäten, die in den Ergebnissen von SQL-Abfragen Daten ohne Maskierung erhalten. Beachten Sie, dass Benutzer mit Administratorrechten immer von der Maskierung ausgeschlossen sind und Originaldaten ohne Maskierung angezeigt bekommen.

* **Maskierungsregeln:** Eine Gruppe von Regeln, die die zu maskierenden Felder und verwendete Maskierungsfunktion definieren. Mithilfe eines Datenbankschemanamens, Tabellennamens und Spaltennamens können die vorgesehenen Felder bestimmt werden.

* **Maskierungsfunktionen:** Eine Reihe von Methoden, die die Anzeige von Daten in verschiedenen Szenarios steuern.

| Maskierungsfunktion | Maskierungslogik |
|----------|---------------|
| **Standard** |**Vollständige Maskierung anhand des Datentyps der angegebenen Felder**<br/><br/>• XXXX oder weniger X-Zeichen verwenden, wenn die Feldbreite weniger als 4 Zeichen für Zeichenfolgendatentypen (nchar, ntext, nvarchar) beträgt.<br/>• Für numerische Datentypen (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real) einen Nullwert verwenden.<br/>• Für Datum/Uhrzeit-Datentypen (date, datetime2, datetime, datetimeoffset, smalldatetime, time) 01-01-1900 verwenden.<br/>• Für SQL-Varianten wird der Standardwert des aktuellen Typs verwendet.<br/>• Für XML wird das Dokument <masked/> verwendet.<br/>• Für spezielle Datentypen (timestamp table, hierarchyid, GUID, binary, image, räumliche varbinary-Typen) einen leeren Wert verwenden.
| **Kreditkarte** |**Maskierungsmethode, die die letzten vier Ziffern der festgelegten Felder anzeigt** und eine Konstantenzeichenfolge als Präfix in Form einer Kreditkarte hinzufügt.<br/><br/>XXXX-XXXX-XXXX-1234|
| **US-Sozialversicherungsnummer** |**Maskierungsmethode, die die letzten vier Ziffern der festgelegten Felder anzeigt** und eine Konstantenzeichenfolge als Präfix in Form einer US-Sozialversicherungsnummer hinzufügt.<br/><br/>XXX-XX-1234 |
| **E-Mail** | **Maskierungsmethode, die den ersten Buchstaben und die Domäne durch XXX.com ersetzt** und dafür eine Konstantenzeichenfolge als Präfix in Form einer E-Mail-Adresse verwendet.<br/><br/>aXX@XXXX.com |
| **Zufallszahl** | **Maskierungsmethode, die eine Zufallszahl** entsprechend den ausgewählten Grenzen und den tatsächlichen Datentypen generiert. Wenn die festgelegten Grenzen gleich sind, ist die Maskierungsfunktion eine konstante Zahl.<br/><br/>![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Benutzerdefinierter Text** | **Maskierungsmethode, die die ersten und letzten Zeichen anzeigt** und in der Mitte eine benutzerdefinierte Auffüllzeichenfolge hinzufügt. Wenn die ursprüngliche Zeichenfolge kürzer als das verfügbar gemachte Präfix und Suffix ist, wird nur die Auffüllzeichenfolge verwendet. <br/>Präfix[Auffüllung]Suffix<br/><br/>![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>
### Empfohlene Felder für die Maskierung

Vom DDM-Empfehlungsmodul werden bestimmte Felder Ihrer Datenbank als potenzielle Felder mit vertraulichen Daten angegeben, bei denen es sich um gute Kandidaten für die Maskierung handelt. Auf dem Blatt „Dynamische Datenmaskierung“ im Portal werden die empfohlenen Spalten für Ihre Datenbank angezeigt. Sie können einfach für eine oder mehrere Spalten auf **Maske hinzufügen** und dann auf **Speichern** klicken, um eine Maskierung auf diese Felder anzuwenden.

## Einrichten der dynamischen Datenmaskierung für Ihre Datenbank im Azure-Portal

1. Rufen Sie das Azure-Portal unter [https://portal.azure.com](https://portal.azure.com) auf.

2. Navigieren Sie zum Blatt mit den Einstellungen der Datenbank mit den sensiblen Daten, die Sie maskieren möchten.

3. Klicken Sie auf die Kachel **Dynamische Datenmaskierung**, woraufhin das Konfigurationsblatt **Dynamische Datenmaskierung** geöffnet wird.

	* Alternativ können Sie nach unten zum Abschnitt **Vorgänge** scrollen und auf **Dynamische Datenmaskierung** klicken.

    ![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>


4. Auf dem Konfigurationsblatt **Dynamische Datenmaskierung** werden ggf. einige Datenbankspalten angezeigt, die vom Empfehlungsmodul für die Maskierung gekennzeichnet wurden. Klicken Sie zum Akzeptieren der Empfehlungen einfach für eine oder mehrere Spalten auf **Maske hinzufügen**. Es wird basierend auf dem Standardtyp der jeweiligen Spalte eine Maskierung erstellt. Sie können die Maskierungsfunktion ändern, indem Sie auf die Maskierungsregel klicken und das Maskierungsfeldformat in ein anderes Format Ihrer Wahl ändern. Klicken Sie auf **Speichern**, um Ihre Einstellungen zu speichern.

    ![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>


5. Klicken Sie zum Hinzufügen einer Maskierung für eine beliebige Spalte der Datenbank oben auf dem Konfigurationsblatt **Dynamische Datenmaskierung** auf **Maske hinzufügen**, um das Konfigurationsblatt **Maskierungsregel hinzufügen** zu öffnen.

    ![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>

6. Wählen Sie **Schema**, **Tabelle** und **Spalte** aus, um das Feld zu bestimmen, das maskiert werden soll.

7. Wählen Sie in der Liste der Kategorien für das Maskieren empfindlicher Daten ein **Maskierungsfeldformat**.

    ![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>

8. Klicken Sie auf dem Datenmaskierungsregel-Blatt auf **Speichern**, um die Gruppe von Maskierungsregeln in der Richtlinie für die dynamische Datenmaskierung zu aktualisieren.

9. Geben Sie die SQL-Benutzer oder AAD-Identitäten ein, die von der Maskierung ausgeschlossen werden sollen und Zugriff auf die vertraulichen Daten ohne Maskierung haben. Hierbei sollte es sich um eine durch Semikolons getrennte Liste mit Benutzern handeln. Beachten Sie, dass Benutzer mit Administratorrechten immer Zugriff auf die Originaldaten ohne Maskierung haben.

    ![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

	>[AZURE.TIP] Damit die Anwendungsschicht sensible Daten für die privilegierten Anwendungsbenutzer anzeigen kann, fügen Sie den SQL-Benutzer oder die AAD-Identität hinzu, die von der Anwendung zum Abfragen der Datenbank verwendet wird. Es wird ausdrücklich empfohlen, dass diese Liste nur eine minimale Anzahl von privilegierten Benutzern enthält, um die Anzeige sensibler Daten zu minimieren.

10. Klicken Sie auf dem Konfigurationsblatt für die Datenmaskierung auf **Speichern**, um die neue oder aktualisierte Maskierungsrichtlinie zu speichern.

## Einrichten der dynamischen Datenmaskierung für Ihre Datenbank mithilfe von Powershell-Cmdlets

Siehe [Azure SQL-Datenbank-Cmdlets](https://msdn.microsoft.com/library/azure/mt574084.aspx).


## Einrichten der dynamischen Datenmaskierung für Ihre Datenbank mithilfe der REST-API

Siehe [Vorgänge für Azure SQL-Datenbanken](https://msdn.microsoft.com/library/dn505719.aspx).

<!---HONumber=AcomDC_0128_2016-->