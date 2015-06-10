<properties 
	pageTitle="Neuerungen in SQL-Datenbank V12" 
	description="Beschreibt die neuesten Features, die in Azure SQL-Datenbank V12 hinzugefügt wurden (nur bis Mai 2015)." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="genemi"/>


# Neuerungen in SQL-Datenbank V12


Azure SQL-Datenbank Version V12 ([in einigen Regionen im Vorschaustatus](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable)) bietet jetzt fast vollständige Kompatibilität mit Microsoft SQL Server Engine. Die neuen Verbesserungen optimieren die Migration von SQL Server-Anwendungen in die SQL-Datenbank und ermöglichen Ihrem System, größere Datenbankarbeitsauslastungen robust zu verarbeiten.


[Registrieren Sie sich für SQL-Datenbank](https://portal.azure.com), um [erste Schritte](sql-database-get-started.md) mit dieser neuen Generation in Microsoft Azure auszuführen. Zunächst benötigen Sie ein Microsoft Azure-Abonnement. Sie können sich für eine [kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial) registrieren und sich über [Preise](http://azure.microsoft.com/pricing/details/sql-database) informieren.


Der Weg zur Planung und Durchführung eines Upgrades für Datenbanken einer niedrigeren Version auf V12 beginnt bei [Planen und Vorbereiten des Upgrades auf das aktuelle SQL-Datenbankupdate V12 (Preview)](sql-database-v12-plan-prepare-upgrade.md).


> [AZURE.TIP]Die primäre Webseite für Informationen zu neuen Funktionen in Azure SQL-Datenbank ist unsere Webseite "Dienstupdates" unter [http://azure.microsoft.com/updates/](http://azure.microsoft.com/updates/). Wählen Sie auf der Webseite "Dienstupdates" im Steuerelement **Dienste** die Option **SQL-Datenbank** aus.


## Wichtige Features


- Das **Azure-Vorschauportal** ist zum Erstellen von Datenbanken und Servern der SQL-Datenbank Version V12 oder optional einer früheren Version [verfügbar](http://portal.azure.com/). Geben Sie im Azure-Vorschauportal Ihre SQL-Datenbank und dann einen SQL-Datenbank-Server zum Hosten an.
 - Mit Stand April 2015 wird das [ältere Azure-Portal](http://manage.windowsazure.com/) auch noch weiterhin unterstützt.


- **Wählen Sie eine Version** des SQL-Datenbankservers, wenn Sie das Azure-Vorschauportal verwenden, um eine neue Datenbank zu erstellen. Die Standardversion ist V12, Sie können jedoch die frühere Version des SQL-Datenbankservers auswählen.


- Die **Sicherheit** profitiert von dem neuen Feature der [Benutzer in eigenständigen Datenbanken](sql-database-v12-whats-new.md#UsersInContainedDatabases). Zwei weitere Features sind die [Sicherheit auf Zeilenebene](sql-database-v12-whats-new.md#RowLevelSecurity) und die [dynamische Datenmaskierung](sql-database-v12-whats-new.md#DynamicDataMasking), obwohl sich diese noch im Vorschaustadium befinden und die allgemeine Verfügbarkeit noch nicht erreicht haben.


- **Vereinfachte Verwaltung** großer Datenbanken, um größere Arbeitsauslastungen durch parallele Abfragen zu unterstützen (nur Premium), [Tabellenpartitionierung](http://msdn.microsoft.com/library/ms187802.aspx), [ Onlineindizierung](http://msdn.microsoft.com/library/ms188388.aspx), problemlose erneute Erstellung großer Indizes ohne Größeneinschränkung von 2 GB sowie mehr Optionen für den Befehl [ALTER DATABASE](http://msdn.microsoft.com/library/bb522682.aspx).


- **Unterstützung von wichtigen Programmierfunktionen**, um einen stabileren Anwendungsentwurf mit [CLR-Integration](http://msdn.microsoft.com/library/ms189524.aspx), Transact-SQL-[Fensterfunktionen](http://msdn.microsoft.com/library/ms189461.aspx), [XML-Indizes](http://msdn.microsoft.com/library/bb934097.aspx) und [ Änderungsnachverfolgung](http://msdn.microsoft.com/library/bb933875.aspx) für Daten zu ermöglichen.


- **Bahnbrechende Leistung** durch Unterstützung von In-Memory-[Columnstore-Index](http://msdn.microsoft.com/library/gg492153.aspx)-Abfragen (nur Premium-Ebene) für Data Mart und kleinere analytische Arbeitsauslastungen.


- **Überwachung und Problembehandlung** wurden durch visuelle Darstellungen in mehr als 100 neuen Tabellensichten in einer erweiterten Sammlung von ([DMVs](http://msdn.microsoft.com/library/ms188754.aspx) (Database Management Views, Datenbankverwaltungssichten) optimiert.


- **Neue S3-Leistungsstufe in der Standardebene** bietet größere Flexibilität der [Preise](sql-database-upgrade-new-service-tiers.md) zwischen den Ebenen "Standard" und "Premium". S3 stellt eine größere Anzahl von DTUs (Database Throughput Units, Datenbankdurchsatzeinheiten) sowie alle Funktionen zur Verfügung, die in der Standardebene verfügbar sind.


## Verbesserungen in V12: Erweiterte Datenbankverwaltung


| Funktion | Beschreibung |
| :--- | :--- |
| . | ***1. Mai 2015:*** |
| PowerShell für ein Upgrade auf V12 von einer früheren Version | Neue Powershell-Cmdlets sind verfügbar, um ein Upgrade auf Azure SQL-Datenbank V12 von Version V11 oder einer anderen Version vor V12 zu starten, abzubrechen oder zu überwachen.<br/><br/>Referenzdokumentation zu diesen Cmdlets finden Sie unter:<br/>* [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)<br/>* [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)<br/>* [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx) |
| . | ***April 2015:*** |
| Transparente Datenverschlüsselung (TDE) | Die transparente Datenverschlüsselung (Vorschau) unterstützt den Schutz vor unberechtigtem Zugriff durch Echtzeit-Verschlüsselung und Entschlüsselung der Datenbank, der zugehörigen Sicherungen und von Transaktionsprotokolldateien im Ruhezustand. TDE erfordert keine Änderungen an Ihrer Anwendung.<br/><br/>Details finden Sie unter:<br/>- [Transparente Datenverschlüsselung mit Azure SQL-Datenbank](http://msdn.microsoft.com/library/dn948096.aspx) -Schrittanleitungen.<br/>- [Die transparente Datenverschlüsselung (TDE)](http://msdn.microsoft.com/library/bb934049.aspx) – allgemeine Beschreibung. |
| Transact-SQL-Zertifikate, Schlüssel und Kryptografiefunktionen | Zertifikate, symmetrische Schlüssel und asymmetrische Schlüssel können jetzt in Azure SQL Database V12 erstellt werden. Die meisten Kryptografiefunktionen werden nun unterstützt.<br/><br/>Weitere Informationen finden Sie unter:<br/>- [CREATE CERTIFICATE (Transact-SQL)](http://msdn.microsoft.com/library/ms187798.aspx)<br/>- [CREATE SYMMETRIC KEY (Transact-SQL)](http://msdn.microsoft.com/library/ms188357.aspx)<br/>- [CREATE ASYMMETRIC KEY (Transact-SQL)](http://msdn.microsoft.com/library/ms174430.aspx)<br/>- [Kryptografiefunktionen (Transact-SQL)](http://msdn.microsoft.com/library/ms173744.aspx) |
| Elastische SQL-Datenbankpools | Einfache Aufgaben werden komplexer, wenn Sie auf Tausende von Datenbanken skalieren. Dies ist der Nachteil von explosionsartigem Wachstum. SaaS-Entwickler verbringen unzählige Stunden mit dem Schreiben von komplexer Logik zur Behandlung von Schemaänderungen und anderen administrativen Vorgängen, während das Unternehmen wächst.<br/><br/>Mit [elastischen Datenbanken](sql-database-elastic-pool.md) wird dies alles einfacher. Sie erstellen ein Skript, übermitteln es als Auftrag, und Azure SQL-Datenbank übernimmt den Rest. |
| Tarifempfehlungen und der STA (Service Tier Advisor) | Beim Verschieben der Datenbank von Web- oder Business-Editionen in eine der neuen Basic-, Standard- oder Premium-Dienstebenen können Sie jetzt Tarifempfehlungen direkt im Azure-Vorschauportal erhalten.<br/><br/>Der Azure SQL-Datenbankdienst analysiert die Leistungs- und Featureanforderungen für vorhandene Datenbanken. Der [STA (Service Tier Advisor)](sql-database-service-tier-advisor.md) liest die Leistungsdaten, um die optimale Dienstebene für die Datenbank zu empfehlen. |
| DMV-Berechtigungen: | Für die Ausführung mehrerer dynamischer Verwaltungssichten (Dynamic Management Views, DMVs) war bisher die Berechtigung VIEW SERVER STATE erforderlich. In Azure SQL-Datenbank V12 können jetzt in vielen Fällen die DMVs über das SQL-Datenbank-Administratorkonto in Datenbanken ausgeführt werden, die sich auf der Basic- oder Standard-Dienstebene befinden.<br/><br/>Auf der Premium-Ebene kann mit der Berechtigung VIEW DATABASE STATE in der Datenbank weiteren Benutzern der Zugriff auf die DMVs gewährt werden.<br/><br/>Details finden Sie im Abschnitt "Berechtigungen" für eine bestimmte einzelne [DMV](http://msdn.microsoft.com/library/ms188754.aspx). |
| DBCC SQLPERF | Der Befahl [DBCC SQLPERF](http://msdn.microsoft.com/library/ms189768.aspx) ist jetzt in Azure SQL-Datenbank V12 verfügbar. Die Optionen *Wartezeit zurücksetzen* und *Statistiken sperren* werden in SQL-Datenbank V12 nicht unterstützt. |
| . | ***Dezember 2014:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>Benutzer in eigenständigen Datenbanken | Sie können jetzt in Ihrer Datenbank Benutzer ohne entsprechende Anmeldung in der Master-Datenbank erstellen. Diese werden als *eigenständige Benutzer* bezeichnet. Dadurch ist es viel einfacher, die Datenbank auf einen anderen Server zu verschieben. Der Verbindungscode in Ihren Clientanwendungen bleibt gleich, unabhängig davon, ob Sie eigenständige Datenbankbenutzer verwenden oder nicht.<br/><br/>Die Verwendung dieses Features ist eine hervorragende Möglichkeit, von höheren garantierten Service-Level-Vereinbarungen zu profitieren.<br/><br/>Microsoft empfiehlt generell die Verwendung dieses Features. Es können jedoch auch bestimmte Szenarien vorliegen, bei denen die herkömmliche Kombination *Anmeldung + Benutzer* die bessere Wahl sein kann.<br/><br/>Weitere Informationen finden Sie unter:<br/>- [Sicherheitsrichtlinien und Einschränkungen der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [Eigenständige Datenbanken](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| Tabellenpartitionierung | Bisherige Einschränkungen bei der [Tabellenpartitionierung](http://msdn.microsoft.com/library/ms190787.aspx) wurden beseitigt. |
| Größere Transaktionen | Mit V12 entfällt nun die Einschränkung von maximal 2 GB Datenänderungen in einer einzelnen Transaktion. <br/><br/> Ein Vorteil besteht darin, dass das erneute Erstellen eines großen Index nicht mehr durch den Grenzwert für die Transaktionsgröße von 2 GB eingeschränkt wird. Allgemeine Informationen finden Sie unter [Ressourceneinschränkungen für Azure SQL-Datenbanken](http://msdn.microsoft.com/library/azure/dn338081.aspx). |
| Onlineindexerstellung und erneute Erstellung | Vor V12 wurde von Azure SQL-Datenbank in der Regel die Klausel (ONLINE=ON) des Befehls [ALTER INDEX](http://msdn.microsoft.com/library/ms188388.aspx) unterstützt, jedoch nicht für Indizes in einer Spalte vom Typ BLOB. V12 unterstützt (ONLINE=ON) jetzt auch für Indizes in BLOB-Spalten.<br/><br/> Durch die ONLINE-Funktion können Abfragen selbst dann von einem Index profitieren, während der Index erneut erstellt wird. |
| CHECKPOINT-Unterstützung | Bei V12 können Sie den Transact-SQL-Befehl [CHECKPOINT](http://msdn.microsoft.com/library/ms188748.aspx) für Ihre Datenbank ausgeben. |
| ALTER TABLE-Erweiterung | Gestattet die Ausführung vieler "ALTER COLUMN"-Aktionen, während die Tabelle verfügbar bleibt. Weitere Informationen finden Sie unter [ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx). |
| TRUNCATE TABLE-Erweiterung | Ermöglicht das Abschneiden bestimmter Partitionen. Weitere Informationen finden Sie unter [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx). |
| Weitere Optionen zu ALTER DATABASE | V12 unterstützt eine größere Anzahl der Optionen, die in dem Befehl [ALTER DATABASE](http://msdn.microsoft.com/library/ms174269.aspx) verfügbar sind. <br/><br/> Weitere Informationen finden Sie unter [Transact-SQL-Referenz für die Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Weitere DBCC-Befehle | In V12 sind jetzt einige weitere[DBCC](http://msdn.microsoft.com/library/ms188796.aspx)-Befehle verfügbar. Ausführlichere Informationen finden Sie unter[Transact-SQL-Referenz für die Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/ee336281.aspx). |


## Programmierungs- und Anwendungsunterstützung


| Funktion | Beschreibung |
| :--- | :--- |
| . | ***1. Mai 2015:*** |
| Verbesserte Indexgrößen | In SQL-Datenbank V12 können bis zu 32 Spalten in einem einzigen zusammengesetzten Indexschlüssel kombiniert werden. Die maximal zulässige Größe von Indexwerten ist 900 Byte bei einem gruppierten Index oder 1700 Byte bei einem nicht gruppierten Index.<br/><br/>Für Version V11 und alle anderen Versionen vor SQL-Datenbank V12 sind die Beschränkungen 16 Spalten und 900 Byte. |
| . | ***April 2015:*** |
| Vorschau der Volltextsuche | Die [Volltextsuche (FTS)](http://msdn.microsoft.com/library/ms142571.aspx) bietet Ihnen Möglichkeiten zum Abfragen von zeichenbasierten Spalten, die leistungsstärker sind als der LIKE-Operator. Beispiel:<br/><br/>-FREETEXT: Sucht nach Ausdrücken, die der *Bedeutung* Ihres Suchbegriffs entsprechen, selbst wenn der genaue Wortlaut nicht übereinstimmt.<br/>- CONTAINS: Sucht nach Zellwerten, die Ihre beiden Suchbegriffe in großer *physischer Nähe* zueinander enthalten.<br/><br/>**HINWEIS:** Dieses Feature befindet sich im Vorschaustatus, und seine allgemeine Verfügbarkeit für die Produktion wurde noch nicht bekannt gegeben. Der Funktionsbereich der FTS-Vorschau ist eine Teilmenge des FTS-Bereichs in Microsoft SQL Server. |
| . | ***Februar 2015:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> Vorschau der dynamischen Datenmaskierung | Beim Generieren eines Rowsets aus einer Abfrage können Datenbereiche mittels einer festgelegten Datenmaskierungsrichtlinie durch X-Zeichen ersetzt werden, um vertrauliche Informationen zu überlagern und zu schützen. Nach Abschluss des Maskierungsvorgangs wird das geänderte Rowset an den Client gesendet.<br/><br/>Ein Verwendungsbeispiel ist die Maskierung aller Ziffern einer Kreditkartennummer mit Ausnahme einiger der letzten Ziffern.<br/><br/>**HINWEIS:** Dieses Feature befindet sich noch im Vorschaustatus, und seine allgemeine Verfügbarkeit für die Produktion wurde noch nicht bekannt gegeben.<br/><br/>Ausführliche Informationen finden Sie unter [Erste Schritte mit der dynamischen Datenmaskierung von Azure SQL-Datenbank](sql-database-dynamic-data-masking-get-started.md). |
| . | ***Januar 2015:*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> Vorschau der zeilenbasierten Sicherheit (RLS) | Sie können RLS über den neuen Transact-SQL-Befehl [CREATE SECURITY POLICY](http://msdn.microsoft.com/library/dn765135.aspx) implementieren. RLS bewirkt, dass dem Datenbankserver Bedingungen hinzugefügt werden, durch die einige Datenzeilen herausgefiltert werden, bevor ein Rowset an den Aufrufer zurückgegeben wird.<br/><br/>In der Branche wird RLS auch als präzise Zugriffssteuerung bezeichnet.<br/><br/>**HINWEIS:** Dieses Feature befindet sich noch im Vorschaustatus, und seine allgemeine Verfügbarkeit für die Produktion wurde noch nicht bekannt gegeben.<br/><br/>Ein Codebeispiel und mehr finden Sie unter [Sicherheit auf Zeilenebene (Vorschau)](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx). |
| . | ***Dezember 2014:*** |
| Fensterfunktionen in Transact-SQL-Abfragen | Auf die ANSI-Fensterfunktionen wird über die [OVER-Klausel](http://msdn.microsoft.com/library/ms189461.aspx) zugegriffen. <br/><br/> In dem hervorragenden [Blogbeitrag](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1) von Itzik Ben-Gan werden die Fensterfunktionen und die OVER-Klausel noch weitergehend erläutert. |
| .NET CLR-Integration | Das CLR-Modul (Common Language Runtime) von .NET Framework ist in V12 integriert. <br/><br/> Nur SAFE-Assemblys, die vollständig in Binärcode kompiliert sind, werden unterstützt. Ausführlichere Informationen finden Sie unter [Beschränkungen des Programmiermodells für die CLR-Integration](http://msdn.microsoft.com/library/ms403273.aspx). <br/><br/> Weitere Informationen zu diesem Feature finden Sie in den folgenden Themen: <br/> * [Einführung in die CLR-Integration für SQL Server](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY (Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| Änderungsnachverfolgung für Daten | Die Änderungsnachverfolgung für Daten kann nun auf Datenbank- oder Tabellenebene konfiguriert werden. <br/><br/> Weitere Informationen zur Änderungsnachverfolgung finden Sie unter [Informationen zur Änderungsnachverfolgung (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx). |
| XML-Indizes | V12 ermöglicht die Verwendung der Transact-SQL-Befehle [CREATE XML INDEX](http://msdn.microsoft.com/library/bb934097.aspx) und [CREATE SELECTIVE XML INDEX](http://msdn.microsoft.com/library/jj670104.aspx). |
| Tabelle als Heap | V12 ermöglicht das Erstellen einer Tabelle, die keinen gruppierten Index besitzt. <br/><br/> Dieses Feature ist besonders hilfreich, weil es den Transact-SQL-Befehl [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx) unterstützt, der aus einem Abfrageergebnis eine Tabelle erstellt. |
| Anwendungsrollen | Zur Steuerung der Sicherheit und Berechtigungen ermöglicht V12 die Ausgabe der Befehle [GRANT](http://msdn.microsoft.com/library/ms187965.aspx) - [DENY](http://msdn.microsoft.com/library/ms188338.aspx) - [REVOKE](http://msdn.microsoft.com/library/ms187728.aspx) für [Anwendungsrollen](http://msdn.microsoft.com/library/ms190998.aspx). |


## Bessere Kundenerkenntnisse


| Funktion | Beschreibung |
| :--- | :--- |
| . | ***Dezember 2014:*** |
| DMVs (dynamische Verwaltungssichten) | In V12 wurden verschiedene DMVs hinzugefügt. Ausführliche Informationen finden Sie unter [Transact-SQL-Referenz für die Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Änderungsnachverfolgung | In V12 wird die Änderungsnachverfolgung vollständig unterstützt. <br/><br/> Ausführlichere Informationen zu diesem Feature finden Sie unter [Aktivieren und Deaktivieren der Änderungsnachverfolgung (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx). |
| ColumnStore-Indizes | Ein ColumnStore-Index verbessert die Systemleistung für Datawarehouses, wenn eine indizierte Spalte Wiederholungen desselben Werts enthält. Der [ColumnStore-Index komprimiert](http://msdn.microsoft.com/library/gg492088.aspx) die doppelten Werte, um die Anzahl der physischen Zeilen, auf die bei Abfragen zugegriffen werden muss, zu reduzieren. |


## Leistungsoptimierungen auf der Premium-Dienstebene


Diese Leistungsoptimierungen gelten für die Stufen P2 und P3 in der Premium-Dienstebene.


| Funktion | Beschreibung |
| :--- | :--- |
| . | ***Dezember 2014:*** |
| Parallele Verarbeitung für Abfragen | V12 bietet ein höheres Maß an Parallelität für Abfragen, die davon profitieren können. |
| Kürzere E/A-Latenz | V12 weist eine erheblich kürzere Latenz für Eingabe-/Ausgabevorgänge auf. |
| Größere Anzahl von IOPS | V12 kann eine größere Anzahl von IOPS (Input/Output Per Second) verarbeiten. |
| Protokollrate | V12 kann eine größere Anzahl von Datenänderungen pro Sekunde protokollieren. |


## V12 wird ab 1. August 2015 API-Standard  


| Funktion | Beschreibung |
| :--- | :--- |
| . | ***August 2015:*** |
| Erstellen eines Servers mithilfe von REST oder PowerShell | Wenn Sie einen Server ohne Angabe einer Serverversion erstellen, ändert sich die Standardversion von V11 in V12.<br/><br/>Dies entspricht dem [Azure-Vorschauportal](http://portal.azure.com). |
| Erstellen einer Datenbank mithilfe von Transact-SQL, [REST](http://msdn.microsoft.com/library/azure/gg715283.aspx) oder [PowerShell](http://msdn.microsoft.com/library/azure/dn806332.aspx) | Wenn Sie auf V11-Servern eine neue Datenbank ohne Angabe eines Editions- oder Dienstziels erstellen, ist das Standard-Dienstziel [S0](http://azure.microsoft.com/pricing/details/sql-database/) anstelle von Web und Business. Dies entspricht V12-Servern im Azure-Vorschauportal. |


## Zusammenfassung der Optimierungen


V12 hebt die SQL-Datenbank unter Kompatibilitätsaspekten fast auf eine Stufe mit den vollständigen Funktionen des SQL Server-Produkts. Im Mittelpunkt von V12 stehen die beliebtesten Features sowie die Programmierbarkeitsunterstützung. Auf diese Weise wird Ihre Entwicklung effizienter und angenehmer. Es ist jetzt noch einfacher, Ihre SQL-Datenbankanwendungen in die Cloud zu verschieben.


Außerdem überzeugt V12 auf der Premium-Ebene durch große Leistungsoptimierungen. Für einige Anwendungen wird eine große Verbesserung bei der Abfragegeschwindigkeit erzielt. Für Anwendungen mit hohen Arbeitsauslastungen gilt ein zuverlässig stabiler Durchsatz.


## <a name="V12AzureSqlDbPreviewGaTable"></a>Status der allgemeinen Verfügbarkeit von V12 aufgeschlüsselt nach Region


> [AZURE.NOTE]
> [Pricing](http://azure.microsoft.com/pricing/details/sql-database/) wird während der Vorschau zu einem ermäßigtem Preis angeboten. Am 31. März 2015 werden die Preise für alle Regionen wieder auf die Preise der allgemeinen Verfügbarkeit umgestellt.


| Azure-Region | Aktuelle Version<br/>Status von V12 | Datum der Heraufstufung<br/>auf allgemeine Verfügbarkeit |
| :--- | :--- | :--- |
| USA (Mitte/Süden) | Allgemeine Verfügbarkeit | 9. Februar 2015 |
| USA (Mitte) | Allgemeine Verfügbarkeit | 9. Februar 2015 |
| USA (Mitte/Norden) | Allgemeine Verfügbarkeit | 9. Februar 2015 |
| USA (West) | Allgemeine Verfügbarkeit | 9. Februar 2015 |
| USA (Ost) | Allgemeine Verfügbarkeit | 9. Februar 2015 |
| USA (Ost) 2 | Allgemeine Verfügbarkeit | 9. Februar 2015 |
| Ostasien | Allgemeine Verfügbarkeit | 24. Februar 2015 |
| Südostasien | Allgemeine Verfügbarkeit | 24. Februar 2015 |
| Japan West | Allgemeine Verfügbarkeit | 24. Februar 2015 |
| Japan Ost | Allgemeine Verfügbarkeit | 24. Februar 2015 |
| Nordeuropa | Allgemeine Verfügbarkeit | 29. Januar 2015 |
| Westeuropa | Allgemeine Verfügbarkeit | 29. Januar 2015 |
| Brasilien Süd | Allgemeine Verfügbarkeit | 21. April 2015 |
| Australien (Ost) | Vorschau | 2. Quartal 2015 (geschätzt) |
| Australien (Südost) | Vorschau | 2. Quartal 2015 (geschätzt) |


In allen Regionen, für die allgemeine Verfügbarkeit bekanntgegeben wurde, verwenden alle neuen Abonnements und Nachfolgerdatenbanken die V12-Dienstarchitektur. Damit ist der Zugriff auf aktuelle Features sichergestellt. In diesem Artikel werden die neuen Features von V12 vorgestellt.


Für jede Region, für die noch die Vorschau und noch nicht die allgemeine Verfügbarkeit gilt, müssen Sie [die Option für die Verwendung von V12-Datenbanken aktivieren](sql-database-v12-sign-up.md).


Wenn Sie zum Zeitpunkt der allgemeinen Verfügbarkeit über Server und Datenbanken mit einer niedrigeren Version als V12 verfügen, können Sie bestimmen, ob ein Upgrade (oder ein Wechsel) Ihrer Datenbanken auf die V12 Dienstearchitektur durchgeführt werden soll. Anschließend können Sie die neuen Features für die Produktion verwenden. Für V12-Datenbanken gelten die [Tarife](sql-database-upgrade-new-service-tiers.md) "Basic", "Standard" oder "Premium".


## Vorgehensweise


Informationen zum Upgrade von Datenbanken von Azure SQL-Datenbank V11 auf V12 finden Sie unter [Planen und Vorbereiten des Upgrades auf die Azure SQL-Datenbank V12](sql-database-v12-plan-prepare-upgrade.md).


Versionsnummern wie V12 verweisen auf den Wert, der von der folgenden Transact-SQL-Anweisung zurückgegeben wird:<br/> **SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(oder etwas höher, V12)*


Aktuelle Preisinformationen zu V12 finden Sie unter [SQL-Datenbank Preise](http://azure.microsoft.com/pricing/details/sql-database/).


## Warnhinweise zum Upgrade auf V12


Es gibt einige wichtige Informationen über Einschränkungen während und nach einem Upgrade Ihrer Datenbank von V11 auf V12. Ausführlichere Informationen finden Sie unter diesem Link ungefähr in der [Mitte](sql-database-v12-plan-prepare-upgrade.md#limitations) des Themas *Planen und Vorbereiten des Upgrades auf die Azure SQL-Datenbank V12*.


## Weitere Quellen für Neuigkeiten


Das Thema *Neuerungen* für Azure SQL-Datenbank V12 wird abgeschlossen und voraussichtlich nach dem 30. April 2015 nicht mehr aktualisiert. Informationen zu neuen Features und weitere Ankündigungen sind über den folgenden Ankündigungslink erhältlich:


- [Ankündigungen](http://azure.microsoft.com/updates/?service=sql-database) für Azure SQL-Datenbank auf unserer Webseite **Dienstupdates**.
 - Klicken Sie auf das Symbol **RSS** auf der Webseite, wenn das Steuerelement "Dienste"auf *SQL-Datenbank* eingestellt ist .
- Folgen Sie uns auf Twitter: **@SQLTechCenter**.


[V12 general availability (GA) status per region]: #V12AzureSqlDbPreviewGaTable

<!---HONumber=58-->