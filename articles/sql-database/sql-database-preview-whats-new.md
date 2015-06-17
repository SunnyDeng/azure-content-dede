<properties 
	pageTitle="Neuerungen in SQL-Datenbank V12" 
	description="Beschreibt die aktuellen Neuerungen der Azure SQL-Datenbank, die im Dezember 2014 oder später hinzugefügt wurden." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="genemi"/>


#Neuerungen in SQL-Datenbank V12


<!--
GeneMi, 2015-March-05 Thursday 20:06pm
Entfernen Sie 'Azure' aus dem Thementitel, und fügen Sie 'V12' hinzu (laut J.G).
-->



Version 12 der Azure SQL-Datenbank wurde im Januar 2015 zur allgemeinen Verfügbarkeit heraufgestuft. Mit diesem wichtigen Update bietet SQL-Datenbank jetzt eine fast vollständige Kompatibilität mit dem Microsoft SQL Server-Modul. Außerdem stellt SQL-Datenbank den Kunden jetzt in größerem Umfang Premium-Leistung zur Verfügung. Diese Verbesserungen optimieren SQL Server-Anwendungsmigrationen in SQL-Datenbank und unterstützen Kunden, die größere Datenbankarbeitsauslastungen verarbeiten müssen.


Einige geografische Regionen befinden sich immer noch im Preview-Status und haben die allgemeine Verfügbarkeit noch nicht erreicht. Informationen zu Ihrer Region finden Sie in der [Tabelle zur allgemeinen Verfügbarkeit nach Region](../sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable). Bis für Ihre Region die allgemeine Verfügbarkeit erreicht ist, sind für V12 eher Testdatenbanken als Produktionsdatenbanken geeignet.


**[Registrieren](https://portal.azure.com) Sie sich für SQL-Datenbank, um den Vorteil dieser neuen Generation für Microsoft Azure zu nutzen. Zunächst benötigen Sie ein Microsoft Azure-Abonnement. Sie können sich für eine [kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial) registrieren, um sich über die [Preisdetails](http://azure.microsoft.com/pricing/details/sql-database) zu informieren.**


Der Pfad zur Planung und zum Upgrade der V11-Datenbanken auf V12 beginnt mit dem [Planen und Vorbereiten des Upgrades auf das aktuelle SQL-Datenbankupdate V12 (Preview)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


###Wichtige Features


Folgendes zählt zu den wichtigen Features von Azure SQL-Datenbank V12:


- Die **Sicherheit** profitiert von dem neuen Feature der [Benutzer in eigenständigen Datenbanken](../sql-database-preview-whats-new/#UsersInContainedDatabases). Zwei weitere Features sind die [Sicherheit auf Zeilenebene](../sql-database-preview-whats-new/#RowLevelSecurity) und die [dynamische Datenmaskierung](../sql-database-preview-whats-new/#DynamicDataMasking), obwohl sich diese noch im Vorschaustadium befinden und noch nicht zur allgemeinen Verfügbarkeit zählen.


- **Vereinfachte Verwaltung** großer Datenbanken, um größere Arbeitsauslastungen mit parallelen Abfragen zu unterstützen (nur Premium), [Tabellenpartitionierung](http://msdn.microsoft.com/library/ms187802.aspx), [Onlineindizierung](http://msdn.microsoft.com/library/ms188388.aspx), problemlose erneute Erstellung großer Indizes ohne Größeneinschränkungen von 2 GB sowie mehr Optionen für die Anweisung [alter database](http://msdn.microsoft.com/library/bb522682.aspx).


- **Unterstützung für wichtige Programmierfunktionen**, um einen stabileren Anwendungsentwurf mit [CLR-Integration](http://msdn.microsoft.com/library/ms189524.aspx), T-SQL-[Fensterfunktionen](http://msdn.microsoft.com/library/ms189461.aspx), [XML-Indizes](http://msdn.microsoft.com/library/bb934097.aspx) und [Änderungsnachverfolgung](http://msdn.microsoft.com/library/bb933875.aspx) für Daten zu ermöglichen.


- **Bahnbrechende Leistung** mit Unterstützung für In-Memory-[Columnstore](http://msdn.microsoft.com/library/gg492153.aspx)-Abfragen (nur Premium-Ebene) für Data Mart und kleinere analytische Arbeitsauslastungen.


- **Überwachung und Problembehandlung** wurden durch visuelle Darstellungen in mehr als 100 neuen Tabellensichten in einer erweiterten Sammlung von DMVs ([Database Management Views, Datenbankverwaltungssichten](http://msdn.microsoft.com/library/ms188754.aspx)) optimiert.


- **Neue S3-Leistungsstufe in der Standardebene** bietet größere Preisflexibiliät zwischen den Ebenen Standard und Premium. S3 stellt eine größere Anzahl von DTUs (Database Throughput Units, Datenbankdurchsatzeinheiten) sowie alle Funktionen zur Verfügung, die in der Standardebene verfügbar sind.


##1. Erläuterungen zu den Einzelheiten der Optimierungen der neuen V12


In diesem Abschnitt werden die neuen Funktionen in jeder Kategorie genannt und beschrieben.


###1.1 Kategorie: Erweiterte Datenbankverwaltung


| Funktion | Beschreibung |
| :--- | :--- |
| . | ***Dezember 2014:*** |
| <a name="UsersInContainedDatabases" id="UsersInContainedDatabases"></a>Benutzer in eigenständigen Datenbanken | Sie können jetzt in Ihren eigenständigen Datenbanken Benutzer erstellen, ohne sich bei der entsprechenden master-Datenbank anmelden zu müssen. Dadurch ist es viel einfacher, die Datenbank auf einen anderen Server zu verschieben. Der Verbindungscode in den Clientanwendungen ist unabhängig davon identisch, ob Sie eigenständige Datenbankbenutzer verwenden oder nicht.<br/><br/>Dieses Feature ist möglicherweise für Kunden erforderlich, die von höheren garantierten Vereinbarungen zum Servicelevel profitieren möchten.<br/><br/>Microsoft empfiehlt im Allgemeinen in Betracht zu ziehen, dieses Feature zu verwenden. Einige Kunden weisen jedoch möglicherweise bestimmte Szenarien auf, in denen zu diesem Zeitpunkt das übliche *login+user*-Paar die bessere Wahl darstellt.<br/><br/>Weitere Informationen finden Sie unter:<br/>- [Richtlinien für und Einschränkungen von Azure SQL-Datenbanken](http://msdn.microsoft.com/library/azure/ff394108.aspx)<br/>- [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/ee336235.aspx)<br/>- [Eigenständige Datenbanken](http://msdn.microsoft.com/library/azure/ff929071.aspx) |
| Tabellenpartitionierung | Zuvor bestehende Einschränkungen bei der [Tabellenpartitionierung](http://msdn.microsoft.com/library/ms190787.aspx) wurden beseitigt. |
| Größere Transaktionen | Mit V12 entfällt nun die Einschränkung von maximal 2 GB Datenänderungen in einer einzelnen Transaktion. <br/><br/> Ein Vorteil besteht darin, dass das erneute Erstellen eines großen Index nicht mehr durch den Grenzwert für die Transaktionsgröße von 2 GB eingeschränkt wird. Allgemeine Informationen finden Sie unter [Ressourceneinschränkungen der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/dn338081.aspx). |
| Onlineindexerstellung und erneute Erstellung | Vor V12 unterstützte Azure SQL-Datenbank im Allgemeinen die Klausel (ONLINE=ON) der ALTER INDEX-Anweisung. Dies wurde jedoch für Indizes für eine Spalte vom BLOB-Typ nicht unterstützt. V12 unterstützt nun (ONLINE=ON) auch für Indizes für BLOB-Spalten.<br/><br/> Durch die ONLINE-Funktion können Abfragen selbst dann von einem Index profitieren, während der Index erneut erstellt wird. |
| CHECKPOINT-Unterstützung | Mit V12 können Sie die T-SQL-Anweisung "CHECKPOINT" für Ihre Datenbank ausgeben. |
| ALTER TABLE-Erweiterung | Gestattet die Ausführung vieler "alter column"-Aktionen, während die Tabelle verfügbar bleibt. Weitere Informationen finden Sie unter [ALTER TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms190273.aspx) |
| TRUNCATE TABLE-Erweiterung | Ermöglicht das Abschneiden bestimmter Partitionen. Weitere Informationen finden Sie unter [TRUNCATE TABLE (Transact-SQL)](http://msdn.microsoft.com/library/ms177570.aspx) |
| Weitere Optionen zu ALTER DATABASE | V12 unterstützt eine größere Anzahl von Optionen, die in der Anweisung "ALTER DATABASE" verfügbar sind. <br/><br/> Weitere Informationen finden Sie unter [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx) oder [Azure SQL-Datenbank - Transact-SQL-Referenz](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Weitere DBCC-Befehle | In V12 sind jetzt verschiedene weitere DBCC-Befehle verfügbar. Weitere Informationen finden Sie jetzt unter [Azure SQL-Datenbank - Transact-SQL-Referenz](http://msdn.microsoft.com/library/azure/ee336281.aspx). |


###1.2 Kategorie: Programmierungs- und Anwendungsunterstützung


| Funktion | Beschreibung |
| :--- | :--- |
| . | ***Februar 2015:*** |
| <a name="DynamicDataMasking" id="DynamicDataMasking"></a> Vorschau der dynamischen Datenmaskierung | Beim Generieren eines Rowsets aus einer Abfrage können Datenbereiche mittels einer festgelegten Datenmaskierungsrichtlinie durch X-Zeichen ersetzt werden, um vertrauliche Informationen zu überlagern und zu schützen. Nach Ende des Maskierungsvorgangs wird das geänderte Rowset an den Client gesendet.<br/><br/>Ein Anwendungsbeispiel wäre die Maskierung der letzten Ziffern einer Kreditkartennummer.<br/><br/>**HINWEIS:** Dieses Feature befindet sich noch in der Previewphase. Die allgemeine Verfügbarkeit für Produktionszwecke wurde noch nicht bekanntgegeben.<br/><br/>Ausführliche Informationen finden Sie unter [Erste Schritte mit der dynamischen Datenmaskierung der Azure SQL-Datenbank](http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/). |
| . | ***Januar 2015:*** |
| <a name="RowLevelSecurity" id="RowLevelSecurity"></a> Vorschau der zeilenbasierten Sicherheit (RLS) | **Vorsicht:** Die RLS-Funktion befindet sich momentan in der *preview*-Phase. Dies gilt auch für geografische Regionen, für die die allgemeine Verfügbarkeit von V12 bereits bekanntgegeben wurde. Solange RLS den Status der allgemeinen Verfügbarkeit noch nicht erreicht hat, eignet sich die Funktion nicht für geschäftswichtige Produktionsdatenbanken.<br/><br/>Sie können RLS über die neue T-SQL-Anweisung CREATE SECURITY POLICY implementieren. RLS bewirkt, dass dem Datenbankserver Bedingungen hinzugefügt werden, durch die einige Datenzeilen herausgefiltert werden, bevor ein Rowset an den Aufrufer zurückgegeben wird.<br/><br/>In Fachkreisen wird RLS gelegentlich auch als differenzierte Zugriffssteuerung bezeichnet.<br/><br/>Ein Codebeispiel und weitere Informationen finden Sie unter [Vorschau der zeilenbasierten Sicherheit](http://msdn.microsoft.com/library/7221fa4e-ca4a-4d5c-9f93-1b8a4af7b9e8.aspx). |
| . | ***Dezember 2014:*** |
| Fensterfunktionen in T-SQL-Abfragen | Auf die ANSI-Fensterfunktionen wird über die [OVER-Klausel](http://msdn.microsoft.com/library/ms189461.aspx) zugegriffen. <br/><br/> Die Beiträge in Itzik Ben-Gans hervorragendem [Blog](http://sqlmag.com/sql-server-2012/how-use-microsoft-sql-server-2012s-window-functions-part-1) bieten weitere Erläuterungen zu den Fensterfunktionen und der OVER-Klausel. |
| .NET CLR-Integration | Das CLR-Modul (Common Language Runtime) von .NET Framework ist in V12 integriert. <br/><br/> Nur SAFE-Assemblys, die vollständig in Binärcode kompiliert sind, werden unterstützt. Details finden Sie unter [Einschränkungen des CLR-Integrationprogrammiermodells](http://msdn.microsoft.com/library/ms403273.aspx). <br/><br/> Weitere Informationen zu dieser Funktion finden Sie in den folgenden Themen: <br/> * [Einführung in die SQL Server-CLR-Integration](http://msdn.microsoft.com/library/ms254498.aspx) <br/> * [CREATE ASSEMBLY Transact-SQL)](http://msdn.microsoft.com/library/ms189524.aspx). |
| Ändern der Nachverfolgung für Daten | Das Ändern der Nachverfolgung für Daten kann nun auf Datenbank- oder Tabellenebene konfiguriert werden. <br/><br/> Weitere Informationen zum Ändern der Nachverfolgung finden Sie unter [Informationen zur Änderungsnachverfolgung (SQL Server)](http://msdn.microsoft.com/library/bb933875.aspx). |
| XML-Indizes | V12 ermöglicht die Verwendung der T-SQL-Anweisungen "CREATE XML INDEX" und "CREATE SELECTIVE XML INDEX". <br/><br/> Informationen zu XML-Indizes sind hier verfügbar: <br/> * [CREATE XML INDEX (Transact-SQL)](http://msdn.microsoft.com/library/bb934097.aspx) <br/> * [Erstellen, Ändern und Löschen selektiver XML-Indizes](http://msdn.microsoft.com/library/jj670109.aspx) | |
| Tabelle als Heap | V12 ermöglicht das Erstellen einer Tabelle, die keinen Clusterindex besitzt. <br/><br/> Diese Funktion ist insbesondere hilfreich, weil sie die T-SQL-Anweisung "SELECT...INTO" unterstützt, die eine Tabelle aus einem Abfrageergebnis erstellt. |
| Anwendungsrollen | Zur Steuerung von Sicherheit und Berechtigungen ermöglicht V12 das Ausgeben von Anweisungen "GRANT", "DENY" und "REMOVE" für [Anwendungsrollen](http://msdn.microsoft.com/library/ms190998.aspx). |


###1.3 Kategorie: Bessere Kundenerkenntnisse


| Funktion | Beschreibung |
| :--- | :--- |
| . | ***Dezember 2014:*** |
| DMVs (dynamische Verwaltungssichten) | In V12 wurden verschiedene DMVs hinzugefügt. Weitere Informationen finden Sie jetzt unter [Azure SQL-Datenbank - Transact-SQL-Referenz](http://msdn.microsoft.com/library/azure/ee336281.aspx). |
| Änderungsnachverfolgung | In V12 wird die Änderungsnachverfolgung vollständig unterstützt. <br/><br/> Details zu dieser Funktion finden Sie unter [Aktivieren und Deaktivieren der Änderungsnachverfolgung (SQL Server)](http://msdn.microsoft.com/library/bb964713.aspx). |


###1.4 Leistungsoptimierungen auf der Premium-Dienstebene


Diese Leistungsoptimierungen gelten für die Stufen P2 und P3 in der Premium-Dienstebene.


| Funktion | Beschreibung |
| :--- | :--- |
| . | ***Dezember 2014:*** |
| Parallele Verarbeitung für Abfragen | V12 bietet ein höheres Maß an Parallelität für Abfragen, die davon profitieren können. |
| Kürzere E/A-Latenz | V12 weist eine erheblich kürzere Latenz für Eingabe-/Ausgabevorgänge auf. |
| Größere Anzahl von IOPS | V12 kann eine größere Anzahl von IOPS (Input/Output Per Second) verarbeiten. |
| Protokollrate | V12 kann eine größere Anzahl von Datenänderungen pro Sekunde protokollieren. |


###1.5 Zusammenfassung der Erweiterungen


V12 hebt die SQL-Datenbank unter Kompatibilitätsaspekten fast auf eine Stufe mit den vollständigen Funktionen des SQL Server-Produkts. Im Mittelpunkt von V12 stehen die beliebtesten Features sowie die Programmierbarkeitsunterstützung. Auf diese Weise wird Ihre Entwicklung effizienter und angenehmer.  Es ist jetzt noch einfacher, Ihre SQL-Datenbankanwendungen in die Cloud zu verschieben.


Außerdem überzeugt V12 auf der Premium-Ebene durch große Leistungsoptimierungen. Für einige Anwendungen wird eine große Verbesserung bei der Abfragegeschwindigkeit erzielt. Für Anwendungen mit hohen Arbeitsauslastungen gilt ein zuverlässig stabiler Durchsatz.


##<a name="V12AzureSqlDbPreviewGaTable"></a>2. Status der allgemeinen Verfügbarkeit von V12 aufgeschlüsselt nach Region


Die V12-Version der Azure SQL-Datenbank wurde im Dezember 2014 in den Regionen, die in der folgenden Tabelle angegeben sind, zu Vorschau- und Testzwecken veröffentlicht. Der Wechsel von der Preview zur allgemeinen Verfügbarkeit findet je nach Region zu unterschiedlichen Terminen statt. In der folgende Tabelle ist der aktuelle V12-Freigabestatus nach Region aufgeschlüsselt.


> [AZURE.NOTE]
> Die Preview wird zu einem ermäßigtem [Preis](http://azure.microsoft.com/pricing/details/sql-database/) angeboten. Am 31. März 2015 werden die Preise für alle Regionen auf das Preisniveau für die allgemeine Verfügbarkeit umgestellt.


| Azure-Region | Aktuelle Version<br/>Status der V12 | Datum der Heraufstufung<br/>zur allgemeinen Verfügbarkeit |
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
| Brasilien Süd | Nicht verfügbar | 3. Quartal 2015 (geschätzt) |
| Australien (Ost) | Vorschau | 2. Quartal 2015 (geschätzt) |
| Australien (Südost) | Vorschau | 2. Quartal 2015 (geschätzt) |


In allen Regionen, für die allgemeine Verfügbarkeit bekanntgegeben wurde, verwenden alle neuen Abonnements und Nachfolgerdatenbanken die V12-Dienstarchitektur. Damit ist der Zugriff auf aktuelle Features sichergestellt. In diesem Artikel werden die neuen Features von V12 vorgestellt.


Zum Zeitpunkt der allgemeinen Verfügbarkeit können sich Kunden mit Servern oder Datenbanken vor V12 für ein Upgrade (oder die Migration) ihrer Datenbanken auf die V12-Dienstarchitektur entscheiden, um die neuen Features in ihrer Produktionsumgebung zu nutzen. Die V12-Datenbanken müssen der [Preisstufe](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) Basic, Standard oder Premium zugeordnet sein.


##3. Vorgehensweise


Informationen zum Upgrade von Datenbanken von Azure SQL-Datenbank V11 auf V12 finden Sie unter [Planen und Vorbereiten des Upgrades auf die aktuelle Preview des Azure SQL-Datenbankupdates](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


Versionsnummern wie V12 verweisen auf den Wert, der von der folgenden Transact-SQL-Anweisung zurückgegeben wird:<br/>
**SELECT @@version;**


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(oder etwas höher, V12)*


*Rabatt:* Während des Zeitraums der V12 Preview können Sie V12 zu einem Rabattpreis verwenden. Die neue S3-Stufe stellt eine größere Anzahl von DTUs (Database Throughput Units, Datenbankdurchsatzeinheiten) sowie alle Funktionen zu einem Preis von US-Dollar 0,2016/Stunde zur Verfügung, die in der Standardebene verfügbar sind. Der Rabatt beträgt in der Phase der V12 Preview US-Dollar 0,1008/Stunde. Details finden Sie unter [Preise für SQL-Datenbank](http://azure.microsoft.com/pricing/details/sql-database/).


##4. Warnhinweise zur V12 Preview


Beim Upgrade und nach dem Upgrade auf Azure SQL-Datenbankupdate V12 (Preview) sind die folgenden Vorsichtsmaßnahmen einzuhalten.


###4.1 Vorschauportal für V12


Nur das erste der folgenden beiden Azure-Verwaltungsportale unterstützt V12-Datenbanken:


- [http://portal.azure.com/](http://portal.azure.com/)
 - Dieses neuere Portal weist noch den Vorschaustatus auf und hat noch nicht das Stadium der allgemeinen Verfügbarkeit erreicht.<br/><br/>
- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)
 - Dieses ältere Portal wird nicht hinsichtlich der Unterstützung von V12 aktualisiert.


Wir empfehlen Kunden, die Verbindung zu ihren Azure SQL-Datenbanken mit Visual Studio 2013 (VS2013) herzustellen. VS2013 kann für folgende Aufgaben verwendet werden:


- Ausführen einer T-SQL-Anweisung
- Entwerfen eines Schemas
- Entwickeln einer Datenbank, entweder online oder offline


Sie können die Verbindung stattdessen mit [Visual Studio Community 2013](https://www.visualstudio.com/de-de/news/vs2013-community-vs.aspx/) herstellen, bei dem es sich um eine kostenlose, voll funktionsfähige Version von VS2013 handelt.


Im älteren Azure-Verwaltungsportal können Sie auf der Datenbankseite auf **In Visual Studio öffnen** klicken, um VS2013 auf Ihrem Computer zu starten, damit die Verbindung mit Azure SQL-Datenbank hergestellt wird.


Als andere Alternative können Sie SQL Server Management Studio (SSMS) 2014 mit [CU6](http://support.microsoft.com/kb/3031047/) verwenden, um die Verbindung zu Azure SQL-Datenbank herzustellen. Weitere Informationen finden Sie in folgendem Blogbeitrag:<br/>[Updates für Clienttools für Azure SQL-Datenbank](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


###4.2 Vorsichtsmaßnahmen beim Upgrade auf V12


> [AZURE.NOTE]
> Während des Upgrades auf V12 kann weiterhin auf Daten in Datenbankversionen vor V12 zugegriffen werden.


- Bei Datenbanken mit mehr als 50 GB kann das Upgrade auf V12 bis zu 24 Stunden dauern.
- Während des Upgrades einer Datenbank auf einen Azure SQL-Datenbank V12-Server können die folgenden Aktionen erst nach Ende des Upgrades durchgeführt werden:
 - Erstellen einer neuen Datenbank
 - Kopieren einer Datenbank auf den Server
 - Wiederherstellen einer gelöschten Datenbank
 - Wiederherstellen einer Datenbank bis zu einem bestimmten Zeitpunkt
 - Georeplikation
- Ab dem Zeitpunkt, zu dem das Upgrade auf V12 abgeschlossen wurde, benötigt das System einige Minuten, um den DNS (Domain Name System)-Eintrag in die V12-Datenbank zu übernehmen. Nach dem DNS-Update kann Ihre Clientanwendung eine Verbindung mit Ihrer Datenbank herstellen.
- Die Preisstufe des Web- und Business-Diensts wird unter V12 nicht unterstützt. Diese wird auch in zukünftigen Versionen nicht unterstützt.


###4.3 Vorsichtsmaßnahmen *after* dem Upgrade auf V12


- Alle Datenbanken, für die direkt ein Upgrade auf V12 ausgeführt wird, können nicht auf die frühere Version zurückgesetzt werden.
- Der Rabatt von 50 % für die Azure SQL-Datenbank V12 gilt für alle geografischen Regionen bis Dienstag, den 31. März 2015 (Ablaufdatum der Preview). Der Rabatt gilt in den Regionen sowohl für die Preview als auch für die allgemeine Verfügbarkeit.


###4.4 Exportieren und Importieren *after* dem Upgrade auf V12


Sie können eine V12-Datenbank mithilfe des [Azure-Webportals](http://portal.azure.com/) exportieren oder importieren. Alternativ können Sie den Export oder Import mithilfe der folgenden Tools ausführen:


- SQL Server Management Studio (SSMS) 2014
- Visual Studio 2013
- Data-Tier Application Framework (DacFx)


Zur Verwendung der Tools müssen Sie allerdings zuerst die aktuellen Updates installieren, um sicherzustellen, dass die neuen V12-Funktionen unterstützt werden:


- [Kumulatives Update 6 für SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Update von Februar 2015: SQL Server-Datenbanktools in Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Februar 2015: Data-Tier Application Framework (DacFx) für Azure SQL-Datenbank V12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Die vorherigen Toollinks wurden am oder nach dem 2. März 2015 aktualisiert. Es wird empfohlen, dass Sie die neueren Updates der Tools verwenden.


[2. Status der allgemeinen Verfügbarkeit von V12 aufgeschlüsselt nach Region]:#V12AzureSqlDbPreviewGaTable


<!-- EndOfFile -->


<!--HONumber=47-->
 