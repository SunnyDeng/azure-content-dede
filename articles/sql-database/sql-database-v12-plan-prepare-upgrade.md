<properties
	pageTitle="Planen Sie Ihr Upgrade auf die SQL-Datenbank V12 | Microsoft Azure"
	description="Beschreibt die Vorbereitungen und Einschränkungen im Zusammenhang mit einem Upgrade auf Version V12 der Azure SQL-Datenbank."
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
	ms.date="10/01/2015"
	ms.author="genemi"/>


# Planen und Vorbereiten des Upgrades auf die SQL-Datenbank V12


In diesem Thema werden die Planung und die Vorbereitungen beschrieben, die Sie ausführen müssen, um das Upgrade Ihrer Azure SQL-Datenbanken von Version V11 auf V12 vorzunehmen.


Ein neues [Azure-Vorschauportal](http://portal.azure.com/) ist verfügbar, um Sie bei der Durchführung des Upgrades auf V12 zu unterstützen.


In der folgenden Tabelle sind weitere Hilfethemen für V12 aufgeführt.


| Titel und Link | Beschreibung des Inhalts |
| :--- | :--- |
| [Neuerungen in SQL-Datenbank V12](sql-database-v12-whats-new.md) | Beschreibt die Details, wie V12 die Azure SQL-Datenbank der vollständigen Funktionalität von Microsoft SQL Server weiter annähert. |
| [Erstellen einer Datenbank in SQL-Datenbank V12](sql-database-get-started.md) | Beschreibt, wie Sie eine neue Azure-SQL-Datenbank unter Version V12 erstellen können. In diesem Thema werden verschiedene Optionen beschrieben, die über eine leere Datenbank hinausgehen. |


## Vorausplanung


In den folgenden Unterabschnitten werden die Lern- und Entscheidungsvorgänge beschrieben, die berücksichtigt werden müssen, bevor Aktionen für ein Upgrade Ihrer Azure SQL-Datenbank auf V12 ausgeführt werden.

### Versionsklärung


Dieses Dokument beschäftigt sich mit dem Upgrade von Microsoft Azure SQL-Datenbank von Version V11 auf V12. Formal ähneln die Versionsnummern den folgenden beiden Werten, die mithilfe der Transact-SQL-Anweisung **SELECT @@version;** abgerufen werden:


- 12\.0.2000.8 *(oder etwas höher, V12)*
- 11\.0.9228.18 *(V11)*
 - V11 wurde zum Teil auch als SAWA V2 bezeichnet.

### Planung der Dienstebene


Ab V12 unterstützt die Azure SQL-Datenbank nur die Dienstebenen Basic, Standard und Premium. Die Web- und Business-Dienstebene wird unter V12 nicht unterstützt. Wenn Sie ein Upgrade Ihrer Azure SQL-Datenbank planen, die zurzeit die Web- oder Business-Dienstebene verwendet, müssen Sie daher entscheiden, welche neue Dienstebene genutzt werden soll.


Ausführliche Informationen zu den Dienstebenen Basic, Standard und Premium finden Sie hier:

- [SQL-Datenbank – Dienstebenen](sql-database-service-tiers.md)
- [Upgrade von Web-/Business-Datenbanken der SQL-Datenbank auf neue Dienstebenen](sql-database-upgrade-new-service-tiers.md)



### Überprüfen der Konfiguration der geografischen Replikation


Wenn Ihre Azure SQL-Datenbank für Georeplikation konfiguriert ist, sollten Sie ihre aktuelle Konfiguration dokumentieren und die Georeplikation dann beenden, bevor Sie mit den Vorbereitungsaktionen für das Upgrade beginnen. Nachdem das Upgrade abgeschlossen ist, müssen Sie Ihre Datenbank erneut für Georeplikation konfigurieren.


Die Strategie besteht darin, die Quelle intakt zu lassen und den Test mit einer Kopie der Datenbank auszuführen.


## Vorbereitungsmaßnahmen


Nachdem Sie Ihre Planung abgeschlossen haben, können Sie die Aktionsschritte ausführen, die in den folgenden Unterabschnitten beschrieben werden, um die endgültige Upgradephase vorzubereiten.


Ausführliche Beschreibungen der endgültigen Upgradephase sind in den Hilfethemen verfügbar, auf die die Links oben in diesem Hilfethema verweisen.


### Dienstebenenaktionen


Der Tarif des Web- und Business-Diensts wird unter V12 nicht unterstützt.


Wenn Ihre V11 Azure SQL-Datenbank eine Web- oder Business-Datenbank ist, bietet Ihnen der Upgradevorgang an, Ihre Datenbank in eine unterstützte Ebene umzuschalten. Das Upgrade empfiehlt eine Ebene, die dem Workloadverlauf Ihrer Datenbank entspricht. Sie können jedoch jede gewünschte Ebene auswählen, die unterstützt wird.


Sie können die Schritte verringern, die während des Upgrades erforderlich sind, indem Sie Ihre V11-Datenbank von der Web- und Business-Ebene abkoppeln, bevor Sie mit dem Upgrade beginnen. Dies kann mithilfe des neuen [Azure-Vorschauportals](http://portal.azure.com/) erfolgen.


Wenn Sie sich nicht sicher sind, auf welche Dienstebene Sie wechseln sollten, kann die S2-Stufe der Standardebene eine sinnvolle Wahl für den Anfang sein. Alle niedrigeren Ebenen verfügen über weniger Ressourcen als bei der Web- und Business-Ebene verfügbar waren.


### Anhalten der Georeplikation während des Upgrades


Das Upgrade auf V12 kann nicht ausgeführt werden, wenn Georeplikation für Ihre Datenbank aktiv ist. Sie müssen Ihre Datenbank zuvor so konfigurieren, dass die Georeplikation nicht mehr verwendet wird.


Nachdem das Upgrade abgeschlossen ist, können Sie Ihre Datenbank erneut für die Verwendung von Georeplikation konfigurieren.


### Client auf einem virtuellen Azure-Computer


Wenn Ihr Clientprogramm eine Verbindung mit SQL-Datenbank V12 herstellt, wobei der Client auf einem virtuellen Azure-Computer ausgeführt wird, müssen Sie die folgenden Portbereiche auf dem virtuellen Computer öffnen:

- 11000-11999
- 14000-14999


Klicken Sie auf [hier](sql-database-develop-direct-route-ports-adonet-v12.md), um ausführliche Informationen über die Ports für die SQL-Datenbank V12 zu erhalten. Die Ports werden für Leistungssteigerungen in der SQL-Datenbank V12 benötigt.


##<a id="limitations"></a>Einschränkungen während und nach dem Upgrade auf V12


### Portale für V12


Es gibt drei Portale für Azure, wobei jedes über unterschiedliche Funktionen im Hinblick auf SQL-Datenbank V12 verfügt.


- [http://portal.azure.com/](http://portal.azure.com/)<br/>Dieses Azure-Vorschauportal ist neu und befindet sich noch im Vorschaustatus. Dieses Portal hat noch nicht vollständig die allgemeine Verfügbarkeit erreicht. Dieses Portal:
 - Kann Ihre V12-Server und -Datenbanken verwalten.
 - Kann ein Upgrade Ihrer V11-Datenbank auf V12 durchführen.


- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Dieses Azure-Portal wird möglicherweise abgeschafft. Dieses Portal:
 - Kann Ihre V12-Server und -Datenbanken verwalten.
 - Kann *kein* Upgrade Ihrer V11-Datenbank auf V12 durchführen.


- (http://*yourservername*.database.windows.net)<br/> Azure SQL-Datenbankverwaltungsportal:
 - Kann V12-Server *nicht* verwalten.


Wir empfehlen Kunden, die Verbindung zu ihren Azure SQL-Datenbanken mit Visual Studio 2013 (VS2013) herzustellen. VS2013 kann für folgende Aufgaben verwendet werden:


- Ausführen einer Transact-SQL-Anweisung
- Entwerfen eines Schemas
- Entwickeln einer Datenbank, entweder online oder offline


Sie können die Verbindung stattdessen mit [Visual Studio Community 2013](https://www.visualstudio.com/de-DE/news/vs2013-community-vs.aspx/) herstellen, bei dem es sich um eine kostenlose, voll funktionsfähige Version von VS2013 handelt.


Im älteren Azure-Portal können Sie auf der Datenbankseite auf **In Visual Studio öffnen** klicken, um VS2013 auf Ihrem Computer zu starten und die Verbindung zu Ihrer Azure SQL-Datenbank herzustellen.


Als weitere Alternative können Sie SQL Server Management Studio (SSMS) 2014 mit[CU6](http://support.microsoft.com/kb/3031047/) verwenden, um die Verbindung zur Azure SQL-Datenbank herzustellen. Weitere Informationen finden Sie in diesem Blogbeitrag:<br/>[Clienttool-Updates für Azure SQL-Datenbank](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


### Einschränkung *während* Upgrades auf V12


Während des Upgrades auf V12 kann weiterhin auf Daten in V11-Datenbanken zugegriffen werden. Es gibt jedoch einige Einschränkungen zu beachten.


| Einschränkung | Beschreibung |
| :--- | :--- |
| Dauer des Upgrades | Die Dauer des Upgrades, hängt von der Größe, der Edition und der Anzahl der Datenbanken auf dem Server ab. Der Upgradevorgang kann Stunden bis Tage dauern, insbesondere bei Servern mit Datenbanken mit den folgenden Eigenschaften:<br/><br/>* Größer als 50 GB oder <br/>* Andere Dienstebene als Premium<br/><br/>Auch die Erstellung neuer Datenbanken auf dem Server während des Upgrades kann die Dauer für das Upgrade verlängern. |
| Keine Georeplikation | Georeplikation wird auf einem V12-Server nicht unterstützt, für den aktuell ein Upgrade aus V11 ausgeführt wird. |
| Datenbank ist in der abschließenden Phase des Upgrades auf V12 kurzzeitig nicht verfügbar | Die Datenbanken, die zu Ihrem V11-Server gehören, bleiben während des Upgradeprozesses verfügbar. Die Verbindung mit dem Server und den Datenbanken ist jedoch in der abschließenden Phase, wenn der Wechsel von V11 zur vorbereiteten V12 beginnt, kurzzeitig nicht verfügbar.<br/><br/>Der Wechselzeitraum kann von 40 Sekunden bis zu 5 Minuten lang sein. Für die meisten Server ist zu erwarten, dass der Wechsel innerhalb von 90 Sekunden abgeschlossen ist. Die Wechselzeit verlängert sich für Server, die eine große Anzahl von Datenbanken haben, oder verlängert sich, wenn es umfangreiche Schreibworkloads für die Datenbanken gibt. |


### Einschränkung *nach* Upgrades auf V12


| Einschränkung | Beschreibung |
| :--- | :--- |
| Keine Zurücksetzung auf V11 | Nachdem das Upgrade abgeschlossen wurde, kann das Ergebnis nicht mehr zurückgesetzt oder rückgängig gemacht werden. |
| Web- oder Business-Ebene | Sobald das Upgrade gestartet wurde, kann der Server für die neue V12-Datenbank die Web- oder Business-Dienstebene nicht mehr erkennen oder akzeptieren. |



### Exportieren und Importieren *nach* Upgrades auf V12


Sie können eine V12-Datenbank über das [Azure-Vorschauportal](http://portal.azure.com/) exportieren oder importieren. Alternativ können Sie den Export oder Import mithilfe der folgenden Tools ausführen:


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- Datenebenenanwendungs-Framework (DacFx)


Zur Verwendung der Tools müssen Sie allerdings zuerst die aktuellen Updates installieren, um sicherzustellen, dass die neuen V12-Funktionen unterstützt werden:


- [Kumulatives Update 6 für SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Februar 2015: Update für SQL Server-Datenbanktools in Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Februar 2015: Datenebenenanwendungs-Framework (DacFx) für Azure SQL-Datenbank V12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE]Die vorherigen Toollinks wurden am oder nach dem 2. März 2015 aktualisiert. Es wird empfohlen, die neueren Updates der Tools zu verwenden.


#### Automatisierter Export


Automatisierter Export ist weiterhin als Vorschau verfügbar. Bei Verwendung von automatisiertem Export sind keine Clienttool-Updates erforderlich. Bei Auswahl der resultierenden Datei und deren Import auf einen lokalen Server werden die oben aufgeführten Tool-Updates benötigt, damit der Import erfolgreich ist.


### Wiederherstellung einer gelöschten V11-Datenbank in V12

Im folgenden Szenario wird erläutert, dass eine gelöschte V11 Azure SQL-Datenbank auf einem V12 Azure SQL-Datenbankserver wiederhergestellt werden kann.

1. Angenommen, Sie verwenden einen V11 Azure SQL-Datenbankserver. <br/> Auf dem Server befindet sich eine Datenbank in der veralteten Web- und Business-Dienstebene.
2. Sie löschen diese Datenbank.
3. Sie führen ein Upgrade des Servers auf V12 aus.
4. Im nächsten Schritt stellen Sie die Datenbank auf den Server wieder her. <br/> Dabei erfolgt ein Upgrade der Datenbank auf V12, mit Stufe S0 der Dienstebene Standard.
5. Sie können die Datenbank in jede unterstützte Dienstebene umschalten, wenn S0 nicht Ihre Wahl ist.


### Powershell-Cmdlets


Powershell-Cmdlets sind verfügbar, um ein Upgrade auf Azure SQL-Datenbank V12 von V11 oder einer anderen niedrigeren Version als V12 zu starten, zu beenden oder zu überwachen.


Referenzdokumentation zu diesen Powershell-Cmdlets finden Sie unter:


- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)


Mit dem Cmdlet „Stop-“ wird der Vorgang abgebrochen, nicht angehalten. Ein Upgrade kann nach einem Abbruch nicht fortgesetzt werden, es muss wieder von vorne begonnen werden. Das Cmdlet "Stop-" bereinigt und setzt alle entsprechenden Ressourcen frei.


## Fehlerbehebung


Wenn aus einem bestimmten Grund ein Upgradefehler auftritt, bleibt Ihre V11-Datenbank aktiv und ist wie üblich verfügbar.


## Verwandte Links


- Microsoft Azure [Vorschaufeatures](http://azure.microsoft.com/services/preview/)


<!--Anchors-->
[Subheading 1]: #subheading-1
 

<!---HONumber=Oct15_HO2-->