<properties
   pageTitle="Azure AD Connect-Synchronisierung: generischer SQL-Connector"
   description="Dieser Artikel beschreibt die Konfiguration des generischen SQL-Connectors von Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="12/11/2015"
   ms.author="andkjell"/>

# Technische Referenz für den generischen SQL-Connector

Dieser Artikel beschreibt den generischen SQL-Connector. Der Artikel bezieht sich auf folgende Produkte:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   Mindestens Hotfix 4.1.3461.0 ([KB2870703](https://support.microsoft.com/kb/2870703)) erforderlich.

Der Connector steht für MIM2016 und FIM2010R2 im [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495) zum Download bereit.

## Übersicht über den generischen SQL-Connector

Der generische SQL-Connector ermöglicht die Integration des Synchronisierungsdiensts in ein Datenbanksystem mit ODBC-Konnektivität.

Im Anschluss finden Sie einen allgemeinen Überblick über die von der aktuellen Connectorversion unterstützten Features:

| Funktion | Support |
| --- | --- |
| Verbundene Datenquelle | Der Connector wird mit allen 64-Bit-ODBC-Treibern unterstützt. Getestet wurde er mit Folgendem: <li>Microsoft SQL Server und SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 & 11g</li><li>MySQL 5.x</li>
| Szenarios | <li>Objektlebenszyklusverwaltung</li><li>Kennwortverwaltung</li> |
| Vorgänge | <li>Vollständiger Import und Deltaimport, Export</li><li>Export: Hinzufügen, Löschen, Aktualisieren und Ersetzen</li><li>Kennwort festlegen, Kennwort ändern</li>
| Schema | <li>Dynamische Erkennung von Objekten und Attributen</li>

### Voraussetzungen

Zur Verwendung des Connectors muss auf dem Synchronisierungsserver neben den weiter oben ggf. erwähnten Hotfixes Folgendes vorhanden sein:

- Microsoft .NET 4.5.2 Framework oder eine höhere Version
- 64-Bit-ODBC-Clienttreiber

### Berechtigungen für die verbundene Datenquelle

Zum Erstellen des generischen SQL-Connectors sowie zum Ausführen der unterstützten Aufgaben benötigen Sie Folgendes:

- db\_datareader
- db\_datawriter

### Ports und Protokolle

Welche Ports für die ordnungsgemäße Verwendung des ODBC-Treibers benötigt werden, können Sie der Dokumentation des Datenbankanbieters entnehmen.

## Erstellen eines neuen Connectors

Wählen Sie zum Erstellen eines generischen SQL-Connectors im Synchronisierungsdienst die Option **Verwaltungs-Agent** und anschließend **Erstellen** aus. Wählen Sie den Connector **Generisch, SQL (Microsoft)** aus.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### Konnektivität

Der Connector verwendet eine ODBC-DSN-Datei für die Konnektivität. Erstellen Sie die DSN-Datei mithilfe von **ODBC-Datenquellen** (im Startmenü unter **Verwaltung**). Erstellen Sie im Verwaltungstool einen Datei-DSN, um ihn für den Connector bereitzustellen.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

Der Konnektivitätsbildschirm ist der erste, der beim Erstellen eines neuen generischen SQL-Connectors angezeigt wird. Beginnen Sie mit der Angabe folgender Informationen:

- DSN-Dateipfad
- Authentifizierung
    - Benutzername
    - Kennwort

Die Datenbank muss eine der folgenden Authentifizierungsmethoden unterstützen:

- **Windows-Authentifizierung**: Die authentifizierende Datenbank überprüft den Benutzer anhand der Windows-Anmeldeinformationen. In diesem Fall wird das vom Synchronisierungsdienst verwendete Dienstkonto verwendet. Dieses Konto benötigt Berechtigungen für die Datenbank.
- **SQL-Authentifizierung**: Die authentifizierende Datenbank verwendet zum Herstellen der Datenbankverbindung den im Konnektivitätsbildschirm definierten Benutzernamen und das dazugehörige Kennwort. Wenn Sie Benutzername und Kennwort in der DSN-Datei speichern, haben die im Konnektivitätsbildschirm angegebenen Anmeldeinformationen Vorrang.
- **Azure SQL-Datenbankauthentifizierung**: Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung](sql-database-aad-authentication.md).

**DN ist Anker**: Wenn Sie diese Option aktivieren, wird der DN auch als Ankerattribut verwendet. Die Option kann bei einer einfachen Implementierung verwendet werden, es gelten jedoch folgende Einschränkungen:

-	Der Connector unterstützt nur einen einzelnen Objekttyp. Daher müssen alle Verweisattribute auf den gleichen Objekttyp verweisen.

**Exporttyp: Objekt ersetzen**: Falls nur einige Attribute geändert wurden, wird beim Exportieren das gesamte Objekt mit allen Attributen exportiert und das vorhandene Objekt ersetzt.

### Schema 1 (Objekttyperkennung)

Auf dieser Seite wird für den Connector die Suche nach den verschiedenen Objekttypen in der Datenbank konfiguriert.

Jeder Objekttyp wird als Partition angezeigt und unter **Konfigurieren von Partitionen und Hierarchien** weiter konfiguriert.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Objekttyp-Erkennungsmethode**: Der Connector unterstützt folgende Objekttyp-Erkennungsmethoden:

- **Fester Wert**: Geben Sie eine kommagetrennte Liste mit Objekttypen an. Beispiel: Benutzer,Gruppe,Abteilung. ![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **Tabelle/Sicht/Gespeicherte Prozedur**: Geben Sie den Namen der Tabelle, Sicht oder gespeicherten Prozedur und anschließend den Namen der Spalte mit der Objekttypenliste an. Geben Sie bei Verwendung einer gespeicherten Prozedur außerdem Parameter im Format **[Name]:[Richtung]:[Wert]** an. Geben Sie die Parameter jeweils in einer separaten Zeile an. (Drücken Sie STRG+EINGABETASTE, um eine neue Zeile zu erhalten.) ![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **SQL-Abfrage**: Diese Option ermöglicht die Angabe einer SQL-Abfrage, die eine einzelne Spalte mit Objekttypen zurückgibt. (Beispiel: `SELECT [Column Name] FROM TABLENAME`) Die zurückgegebene Spalte muss vom Typ „String“ (varchar) sein.

### Schema 2 (Attributtyperkennung)

Auf dieser Seite wird die Erkennung der Attributnamen und -typen konfiguriert. Die Konfigurationsoptionen werden für jeden auf der vorherigen Seite ermittelten Objekttyp aufgeführt.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Attributtyp-Erkennungsmethode**: Der Connector unterstützt für jeden im Schema 1-Bildschirm erkannten Objekttyp folgende Attributtyp-Erkennungsmethoden:

- **Tabelle/Sicht/Gespeicherte Prozedur**: Geben Sie den Namen der Tabelle, Sicht oder gespeicherten Prozedur an, die für die Suche nach den Attributnamen verwendet werden soll. Geben Sie bei Verwendung einer gespeicherten Prozedur außerdem Parameter im Format **[Name]:[Richtung]:[Wert]** an. Geben Sie die Parameter jeweils in einer separaten Zeile an. (Drücken Sie STRG+EINGABETASTE, um eine neue Zeile zu erhalten.) Wenn Sie Attributnamen in einem mehrwertigen Attribut ermitteln möchten, geben Sie eine kommagetrennte Tabellen- oder Sichtenliste an. Mehrwertige Szenarien, bei denen die übergeordnete und die untergeordnete Tabelle die gleichen Spaltennamen enthalten, werden nicht unterstützt.
- **SQL-Abfrage**: Diese Option ermöglicht die Angabe einer SQL-Abfrage, die eine einzelne Spalte mit Attributnamen zurückgibt. (Beispiel: `SELECT [Column Name] FROM TABLENAME`) Die zurückgegebene Spalte muss vom Typ „String“ (varchar) sein.

### Schema 3 (Definieren von Anker und DN)

Auf dieser Seite können Sie jeweils das Anker- und DN-Attribut für die erkannten Objekttypen konfigurieren. Sie können mehrere Attribute auswählen, um einen eindeutigen Anker zu erhalten.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- Mehrwertige und boolesche Attribute werden nicht aufgeführt.
- Für DN und Anker kann nicht das gleiche Attribut verwendet werden, es sei denn, auf der Konnektivitätsseite wurde die Option **DN ist Anker** aktiviert.
- Ist auf der Konnektivitätsseite die Option **DN ist Anker** aktiviert, wird auf dieser Seite nur das DN-Attribut benötigt. Dieses Attribut wird dann auch als Ankerattribut verwendet. ![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### Schema 4 (Definieren von Attributtyp, Verweis und Richtung)

Auf dieser Seite können Sie für die Attribute jeweils den Attributtyp (beispielsweise ganze Zahl, Verweis, Zeichenfolge, Binärwert oder boolescher Wert) und die Richtung konfigurieren. Hier werden alle Attribute der Seite **Schema 2** aufgeführt (auch mehrwertige Attribute).

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **Datentyp**: Dient dazu, den Attributtyp den Typen zuzuordnen, die dem Synchronisierungsmodul bekannt sind. Standardmäßig wird der im SQL-Schema erkannte Typ verwendet, „Datum/Uhrzeit“ und „Verweis“ sind jedoch nicht so einfach erkennbar. Für diese muss **Datum/Uhrzeit** oder **Verweis** angegeben werden.
- **Richtung**: Die Richtung des Attributs kann auf „Import“, „Export“ oder „Import/Export“ festgelegt werden. „Import/Export“ ist die Standardeinstellung. ![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Hinweise:

- Falls ein Attributtyp nicht vom Connector erkannt werden kann, wird der Datentyp „Zeichenfolge“ verwendet.
- **Geschachtelte Tabellen** können als einspaltige Datenbanktabellen betrachtet werden kann. Oracle speichert die Zeilen einer geschachtelten Tabelle in keiner bestimmten Reihenfolge. Beim Abrufen der geschachtelten Tabelle in eine PL/SQL-Variable werden die Zeilen jedoch mit fortlaufenden tiefgestellten Zeichen (beginnend mit 1) versehen. Dies ermöglicht einen arrayähnlichen Zugriff auf einzelne Zeilen.
- **VARRYS** werden vom Connector nicht unterstützt.

### Schema 5 (Definieren der Partition für Verweisattribute)

Auf dieser Seite wird für alle Referenzattribute konfiguriert, auf welche Partition (also auf welchen Objekttyp) das jeweilige Attribut verweist.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Bei Verwendung von **DN ist Anker** muss der gleiche Objekttyp verwendet werden, der auch den Ausgangspunkt des Verweises darstellt. Sie können nicht auf einen anderen Objekttyp verweisen.

### Globale Parameter

Auf der Seite für globale Parameter können Sie den Deltaimport, das Datums-/Uhrzeitformat sowie die Kennwortmethode konfigurieren.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

Der generische SQL-Connector unterstützt folgende Methoden für den Deltaimport:

- **Trigger**: Weitere Informationen finden Sie unter [Generieren von Deltasichten mithilfe von Triggern](https://technet.microsoft.com/library/cc708665.aspx).
- **Wasserzeichen**: Hierbei handelt es sich um eine allgemeine Methode, die für jede beliebige Datenbank verwendet werden kann. Die Wasserzeichenabfrage wird auf der Grundlage des Datenbankanbieters vorab aufgefüllt. Jede verwendete Tabelle/Sicht muss über eine Wasserzeichenspalte verfügen. Damit müssen Einfüge- und Aktualisierungsvorgänge für Tabellen sowie für abhängige (mehrwertige oder untergeordnete) Tabellen nachverfolgt werden. Die Uhren zwischen Synchronisierungsdienst und Datenbankserver müssen synchronisiert werden. Andernfalls werden unter Umständen einige Einträge im Deltaimport ausgelassen. Einschränkung:
    - Die Wasserzeichenstrategie unterstützt keine gelöschten Objekte.
- **Momentaufnahme** (funktioniert nur mit Microsoft SQL Server) [Generieren von Deltasichten mithilfe von Momentaufnahmen](https://technet.microsoft.com/library/cc720640.aspx)
- **Änderungsnachverfolgung** (funktioniert nur mit Microsoft SQL Server) [Informationen zur Änderungsprotokollierung](https://msdn.microsoft.com/library/bb933875.aspx) Einschränkungen:
    - Anker- und DN-Attribut müssen Teil des Primärschlüssels für das ausgewählte Objekt in der Tabelle sein.
    - Die SQL-Abfrage wird beim Importieren und Exportieren mit Änderungsnachverfolgung nicht unterstützt.

**Zusätzliche Parameter**: Geben Sie mithilfe der Zeitzone des Datenbankservers den Standort Ihres Datenbankservers an. Dieser Wert dient zur Unterstützung der verschiedenen Formate von Datums-/Uhrzeitattributen.

Der Connector speichert Datum und Datum/Uhrzeit immer im UTC-Format. Zur ordnungsgemäßen Konvertierung von Datums- und Uhrzeitangaben müssen die Zeitzone des Datenbankservers und das verwendete Format angegeben werden. Das Format muss im .NET-Format angegeben werden.

Beim Exportieren muss jedes Datums-/Uhrzeitattribut für den Connector im UTC-Format angegeben werden.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Kennwortkonfiguration**: Der Connector bietet Kennwortsynchronisierungsfunktionen und unterstützt das Festlegen und Ändern von Kennwörtern.

Der Connector stellt zwei Methoden zur Unterstützung der Kennwortsynchronisierung bereit:

- **Gespeicherte Prozedur**: Diese Methode erfordert zwei gespeicherte Prozeduren, um das Festlegen und Ändern von Kennwörtern zu unterstützen. Geben Sie alle Parameter für das Hinzufügen und Ändern von Kennwörtern unter „Parameter für die gespeicherte Prozedur zum Festlegen von Kennwörtern“ bzw. unter „Parameter für die gespeicherte Prozedur zum Ändern von Kennwörtern“ ein, wie im folgenden Beispiel zu sehen: ![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **Kennworterweiterung**: Diese Methode erfordert eine Kennworterweiterungs-DLL. (Geben Sie den Namen der Erweiterungs-DLL an, die die Schnittstelle [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) implementiert.) Die Kennworterweiterungsassembly muss im Erweiterungsordner platziert werden, damit der Connector die DLL zur Laufzeit laden kann. ![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Außerdem muss auf der Seite **Erweiterung konfigurieren** die Kennwortverwaltung aktiviert werden. ![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### Konfigurieren von Partitionen und Hierarchien

Wählen Sie auf der Seite mit den Partitionen und Hierarchien alle Objekttypen aus. Jeder Objekttyp befindet sich in einer eigenen Partition.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Darüber hinaus können Sie die auf der Konnektivitätsseite oder auf der Seite mit den globalen Parametern definierten Werte überschreiben.

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### Konfigurieren von Ankern

Diese Seite ist schreibgeschützt, da der Anker bereits definiert wurde. Dem ausgewählten Ankerattribut wird immer der Objekttyp angefügt, um sicherzustellen, dass er objekttypübergreifend eindeutig ist.

![anchors](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## Konfigurieren des Ausführungsschrittparameters

Diese Schritte werden für die connectorspezifischen Ausführungsprofile konfiguriert. Die Konfigurationen übernehmen das eigentliche Importieren und Exportieren von Daten.

### Vollständiger Import und Deltaimport

Der generische SQL-Connector unterstützt vollständige Importe und Deltaimporte mit folgenden Methoden:

- Tabelle
- Sicht
- Gespeicherte Prozedur
- SQL-Abfrage

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Tabelle/Sicht**

Zum Importieren mehrwertiger Attribute für ein Objekt müssen Sie unter **Name der mehrwertigen Tabelle/Sichten** die kommagetrennten Tabellen-/Sichtnamen und unter **Verknüpfungsbedingung** die entsprechenden Verknüpfungsbedingungen für die übergeordnete Tabelle angeben.

Ein Beispiel: Sie möchten das Employee-Objekt und alle dazugehörigen mehrwertigen Attribute importieren. Es gibt zwei Tabellen: die Haupttabelle „Employee“ und die mehrwertige Tabelle „Department“. Gehen Sie wie folgt vor:

- Geben Sie unter **Tabelle/Sicht/Gespeicherte Prozedur** die Zeichenfolge **Employee** ein.
- Geben Sie unter **Name der mehrwertigen Tabelle/Sichten** die Zeichenfolge „Department“ ein.
- Geben Sie unter **Verknüpfungsbedingung** die Verknüpfungsbedingung zwischen „Employee“ und „Department“ ein (beispielsweise `Employee.DEPTID=Department.DepartmentID`). ![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Gespeicherte Prozeduren**

![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- Bei großen Datenmengen empfiehlt sich die Implementierung der Paginierung für gespeicherte Prozeduren.
- Damit Ihre gespeicherte Prozedur die Paginierung unterstützt, müssen Sie den Start- und den Endindex angeben. Weitere Informationen finden Sie unter [Effiziente Paginierung bei großen Datenmengen](https://msdn.microsoft.com/library/bb445504.aspx).
- „@StartIndex“ und „@EndIndex“ werden zur Ausführungszeit durch entsprechende Seitengrößenwerte ersetzt, die auf der Seite **Schritt konfigurieren** konfiguriert wurden. Ein Beispiel: Angenommen, der Connector ruft die erste Seite ab, und die Seitengröße ist auf 500 festgelegt. In diesem Fall wird „@StartIndex“ auf 1 und „@EndIndex“ auf 500 festgelegt. Dieser Wert nimmt zu, wenn der Connector weitere Seiten abruft, was die Änderung von „@StartIndex“ und „@EndIndex“ nach sich zieht.
- Geben Sie zum Ausführen einer parametrisierten gespeicherten Prozedur die Parameter im Format `[Name]:[Direction]:[Value]` an. Geben Sie die Parameter jeweils in eine separate Zeile ein. (Drücken Sie STRG+EINGABETASTE, um eine neue Zeile zu erhalten.)
- Der generische SQL-Connector unterstützt auch Importvorgänge aus einer verteilten Umgebung (etwa Verbindungsserver in Microsoft SQL Server). Falls Informationen aus einer Tabelle auf einem Verbindungsserver abgerufen werden sollen, muss die Tabelle im folgenden Format angegeben werden: `[ServerName].[Database].[Schema].[TableName]`
    - In verteilten Umgebungen unterstützt der Connector nur Microsoft-Verbindungsserver.
- Der generische SQL-Connector unterstützt nur Objekte mit ähnlicher Struktur (sowohl Aliasname als auch Datentyp) zwischen Ausführungsschrittinformationen und Schemaerkennung. Wenn sich bei dem ausgewählten Objekt das Schema und die für den Ausführungsschritt angegebenen Informationen unterscheiden, unterstützt der SQL-Connector das entsprechende Szenario nicht.

**SQL-Abfrage**

![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- Abfragen mit mehreren Resultsets werden nicht unterstützt.
- Die SQL-Abfrage unterstützt die Paginierung und stellt Start- und Endindex als Variable zur Verfügung.

### Deltaimport

![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Die Deltaimportkonfiguration erfordert einige weitere Konfigurationsschritte sowie eine andere unterstützte Methode (wie etwa beim vollständigen Import).

- Bei der Nachverfolgung von Deltaänderungen mithilfe der Trigger- oder Momentaufnahmemethode kann der Benutzer in der entsprechenden Tabelle den Namen der Verlaufstabelle oder der Momentaufnahmedatenbank angeben.
- Darüber hinaus muss der Benutzer Verknüpfungsbedingungen für die Verlaufstabelle und die übergeordnete Tabelle angeben. Beispiel: `Employee.ID=History.EmployeeID`
- Um die Transaktion der übergeordneten Tabelle anhand der Verlaufstabelle nachverfolgen zu können, muss der Benutzer den Namen der Spalte mit den Vorgangsinformationen (Hinzufügung/Aktualisierung/Löschung) angeben.
- Wenn der Benutzer Deltaänderungen mithilfe der Wasserzeichenmethode nachverfolgen möchte, muss er den Namen der Spalte mit den Vorgangsinformationen unter **Wasserzeichenspaltenname** angeben. So kann der Connector diese Spalte dann bei Deltaimporten berücksichtigen.
- Die Spalte **Änderungstypattribut** wird für den Änderungstyp benötigt. Diese Spalte ordnet eine Änderung aus der primären oder mehrwertigen Tabelle einem Änderungstyp in der Deltasicht zu. Diese Spalte kann für Änderungen auf Attributebene den Änderungstyp „Attribut ändern“ und für Änderungen auf Objektebene den Änderungstyp „Hinzufügen“, „Ändern“ oder „Löschen“ enthalten. Sollte es sich um einen anderen Wert als den Standardwert „Hinzufügen“, „Ändern“ oder „Löschen“ handeln, kann dieser mithilfe dieser Option definiert werden.

### Export

![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Der generische SQL-Connector unterstützt vier Methoden für den Export:

- Tabelle
- Sicht
- Gespeicherte Prozedur
- SQL-Abfrage

**Tabelle/Sicht**

Bei Verwendung der Option „Tabelle“ oder „Sicht“ generiert der Connector die entsprechenden Abfragen und führt den Export aus.

**Gespeicherte Prozeduren**

![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Bei Verwendung der Option „Gespeicherte Prozedur“ werden drei verschiedene gespeicherte Prozeduren für verschiedene Einfüge-/Aktualisierungs-/Löschvorgänge benötigt:

- **Name der gespeicherten Prozedur zum Hinzufügen**: Diese gespeicherte Prozedur wird ausgeführt, wenn der Connector ein Objekt erhält, das in die entsprechende Tabelle eingefügt werden soll.
- **Name der gespeicherten Prozedur zum Aktualisieren**: Diese gespeicherte Prozedur wird ausgeführt, wenn der Connector ein Objekt erhält, das in der entsprechenden Tabelle aktualisiert werden soll.
- **Name der gespeicherten Prozedur zum Löschen**: Diese gespeicherte Prozedur wird ausgeführt, wenn der Connector ein Objekt erhält, das in der entsprechenden Tabelle gelöscht werden soll.
- Attribut aus dem Schema, das als Parameterwert für die gespeicherte Prozedur verwendet wird. Beispiel: „EmployeeName: INPUT: @EmployeeName“ („EmployeeName“ wird im Connectorschema ausgewählt, und der Connector ersetzt den entsprechenden Wert beim Export.)
- Geben Sie beim Ausführen parametrisierter gespeicherter Prozeduren alle Parameter im Format `[Name]:[Direction]:[Value]` an. Geben Sie die Parameter jeweils in eine separate Zeile ein. (Drücken Sie STRG+EINGABETASTE, um eine neue Zeile zu erhalten.)

**SQL-Abfrage**

![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Bei Verwendung der Option „SQL-Abfrage“ werden drei verschiedene Abfragen für verschiedene Einfüge-/Aktualisierungs-/Löschvorgänge benötigt:

- **Abfrage für Einfügevorgänge**: Diese Abfrage wird ausgeführt, wenn der Connector ein Objekt erhält, das in die entsprechende Tabelle eingefügt werden soll.
- **Abfrage für Aktualisierungsvorgänge**: Diese Abfrage wird ausgeführt, wenn der Connector ein Objekt erhält, das in der entsprechenden Tabelle aktualisiert werden soll.
- **Abfrage für Löschvorgänge**: Diese Abfrage wird ausgeführt, wenn der Connector ein Objekt erhält, das in der entsprechenden Tabelle gelöscht werden soll.
- Attribut aus dem Schema, das als Parameterwert für die Abfrage verwendet wird. Beispiel: `Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## Problembehandlung

-	Informationen zum Aktivieren der Protokollierung für die Behandlung von Connectorproblemen finden Sie unter [Vorgehensweise: Aktivieren der ETW-Ablaufverfolgung für Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).

<!---HONumber=AcomDC_1217_2015-->