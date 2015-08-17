<properties 
   pageTitle="Ändern der Dienstebenen und Leistungsstufen von Datenbanken" 
   description="Erfahren Sie, wie Sie eine Cloud-Datenbank mithilfe von Azure SQL-Datenbank-Dienstebenen dynamisch nach oben oder unten skalieren können. Über diese Dienstebenen können Sie basierend auf Geschäfts- und Kostenanforderungen die Leistung nach oben und unten anpassen, und zwar ohne Ausfallzeiten der Anwendung." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="04/02/2015"
   ms.author="sstein"/>


# Ändern der Dienstebenen und Leistungsstufen von Datenbanken 

In diesem Thema werden die erforderlichen Schritte beschrieben, um in einer Azure SQL-Datenbank zwischen Dienstebene und Leistungsstufe zu wechseln.

## Wechseln von Dienstebenen 

Bestimmen Sie anhand der Informationen unter [Aktualisieren von Web-/Business-SQL-Datenbanken auf die neuen Dienstebenen und Leistungsstufen der Azure SQL-Datenbank](sql-database-upgrade-new-service-tiers.md) und [Dienstebenen und Leistungsstufen der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/dn741336.aspx) die geeignete Dienstebene und Leistungsstufe für Ihre Azure SQL-Datenbank.

Sie können über das Azure-Verwaltungsportal, über [PowerShell](https://msdn.microsoft.com/library/azure/dn546726.aspx) oder über [REST-API](https://msdn.microsoft.com/library/dn505719.aspx) problemlos zwischen den Dienstebenen wechseln.

Beachten Sie beim Wechseln zwischen Dienstebenen Folgendes: 
- Stellen Sie vor der Durchführung eines Upgrades zwischen Dienstebenen und Leistungsstufen sicher, dass Sie auf dem Server über Kontingente verfügen. Wenn Sie zusätzliche Kontingente benötigen, rufen Sie den Kundendienst an.
- Für Verbunddatenbanken kann kein Upgrade auf die Dienstebenen Basic, Standard oder Premium durchgeführt werden.

- Für ein Downgrade einer Datenbank sollte die Datenbank kleiner als die in der Zieldienstebene maximal zulässige Größe sein. Weitere Informationen zu den zulässigen Größe für jede Dienstebene finden Sie in der Tabelle mit den Dienstebenen und Datenbankgrößen weiter unten in diesem Abschnitt.

- Beim Aktualisieren einer Datenbank, für die die Option für [standardmäßige Georeplikation](https://msdn.microsoft.com/library/azure/dn758204.aspx) oder [aktive Georeplikation](https://msdn.microsoft.com/library/azure/dn741339.aspx) aktiviert ist, müssen Sie vor der Aktualisierung der primären Datenbank zunächst die zugehörigen sekundären Datenbanken auf die gewünschte Leistungsstufe aktualisieren.

- Beim Downgrade von einer Premium-Dienstebene müssen Sie zuerst alle geografischen Replikationsbeziehungen beenden. Sie können die im Thema [Beenden einer fortlaufenden Kopierbeziehung](https://msdn.microsoft.com/library/azure/dn741323.aspx) beschriebenen Schritte verwenden, um den Replikationsprozess zwischen der primären und aktiven sekundären Datenbank zu beenden.

- Die Angebote des Wiederherstellungsdienstes variieren für die verschiedenen Dienstebenen. Wenn Sie ein Downgrade durchführen, verlieren Sie eventuell die Möglichkeit einer Zeitpunktwiederherstellung, oder der Aufbewahrungszeitraum für Sicherungen verkürzt sich. Weitere Informationen finden Sie unter [Sichern und Wiederherstellen der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/jj650016.aspx).

- Sie können innerhalb von 24 Stunden bis zu vier einzelne Datenbankänderungen (Dienstebene oder Leistungsstufen) vornehmen.

- Die neuen Eigenschaften für die Datenbank werden erst angewendet, wenn die Änderungen abgeschlossen sind.

Bitte beachten Sie Folgendes:
- Die Dienstebenen "Business" und "Web" werden im September 2015 eingestellt. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Einstellung von Web Edition und Business Edition](https://msdn.microsoft.com/library/azure/dn741330.aspx).

<note included>
- Bitte beachten Sie unbedingt Folgendes: [ Die aktuelle Implementierung von Verbünden wird zusammen mit den Dienstebenen "Business" und "Web" eingestellt](https://msdn.microsoft.com/library/azure/dn741330.aspx). Es wird empfohlen, die Funktion [Flexible Skalierung für Azure SQL-Datenbank](sql-database-elastic-scale-get-started.md) zu verwenden, um eine Sharding-Lösung mit horizontaler Skalierung in Azure SQL-Datenbank zu erstellen. Wenn Sie dies ausprobieren möchten, finden Sie entsprechende Informationen unter "Erste Schritte mit der Vorschauversion von Elastic Scale für die Azure SQL-Datenbank".

## Upgrade auf eine höhere Dienstebene
Verwenden Sie eine der folgenden Methoden, um ein Upgrade einer Datenbank durchzuführen. Die Schritte sind speziell für ein Upgrade auf eine Premium-Dienstebene dargestellt, gelten jedoch für alle Upgrades.

### Verwenden des Azure-Verwaltungsportals
1. Verwenden Sie Ihr Microsoft-Konto, um sich am Azure-Verwaltungsportal anzumelden.
2. Navigieren Sie zur Registerkarte **SQL-DATENBANKEN** .
3. Wählen Sie aus der Liste **Datenbanken** eine Datenbank aus. Daraufhin wird die Datenbank auf dem **Datenbank-Dashboard** oder der Seite **Schnellstart** geöffnet.
4. Wählen Sie die Registerkarte **Skalieren** für die Datenbank aus.
5. Wählen Sie im Abschnitt **Allgemein** als Dienstebene **PREMIUM** aus.
6. Wählen Sie für die **Leistungsstufe** den Wert **P1**, **P2** oder **P3** aus. Die Ressourcen, von denen jede Leistungsstufe unterstützt wird, werden in Datenbankdurchsatzeinheiten (DTUs) angegeben. Weitere Informationen zu Leistungsstufen und DTUs finden Sie unter "Dienstebenen und Leistungsstufen der Azure SQL-Datenbank".
8. Klicken Sie in der Befehlsleiste unten im Bildschirm auf die Schaltfläche **Speichern**.
9. Es wird eine **Bestätigung** angezeigt. Lesen Sie die bereitgestellten Informationen, und aktivieren Sie das Kontrollkästchen zur Bestätigung.


### Verwenden von Azure PowerShell
1. Verwenden Sie "Set-AzureSqlDatabase", um die Leistungsstufe, die maximale Datenbankgröße und die Dienstebene für die Datenbank anzugeben. Eine Liste der von den verschiedenen Dienstebenen unterstützten Datenbankgrößen finden Sie unter "Dienstebenen der Azure SQL-Datenbank (Editionen)".
2. Legen Sie den Serverkontext mithilfe des "New-AzureSqlDatabaseServerContext"-Cmdlets fest. Die Beispielsyntax ist im Abschnitt "Verwenden von Azure PowerShell-Befehlen" angegeben.
3. Rufen Sie ein Handle für die Datenbank und für die Zielleistungsstufe ab. Geben Sie die Leistungsstufe mithilfe von "Set-AzureSqlDatabase –ServiceObjective" an.

**Verwendungsbeispiel**
In diesem Beispiel: 
- In diesem Beispiel wird ein Upgrade auf eine Premium-Dienstebene veranschaulicht.
- Das $db-Handle, das auf den Datenbanknamen "somedb" verweist, wird erstellt.
- Das $P1-Handle, das auf die Premium-Leistungsstufe "1" verweist, wird erstellt.
- Die Leistungsstufe für die "$db"-Datenbank wird auf "$P1" festgelegt.

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName "somedb"

		$P1= Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P1"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P1 –Edition Premium



## Downgrade auf eine niedrigere Dienstebene
Verwenden Sie eine der folgenden Methoden, um ein Downgrade einer Datenbank auf eine niedrigere Dienstebene durchzuführen.

### Verwenden des Azure-Verwaltungsportals
1. Verwenden Sie Ihr Microsoft-Konto, um sich am Azure-Verwaltungsportal anzumelden.
2. Navigieren Sie zur Registerkarte **SQL-DATENBANKEN** .
3. Wählen Sie die Registerkarte **SKALIEREN** für die gewünschte Datenbank aus.
4. Wählen Sie im Abschnitt **Allgemein** die Dienstebene aus, auf die heruntergestuft werden soll.
5. Klicken Sie in der Befehlsleiste unten im Bildschirm auf die Schaltfläche **Speichern**.
6. Lesen Sie ggf. die Informationen auf der Seite **Bestätigung**, und aktivieren Sie das Kontrollkästchen, um die Änderung zu bestätigen.

### Verwenden von Azure PowerShell
1. Verwenden Sie "Set-AzureSqlDatabase", um die Dienstebene, die Leistungsstufe und die maximale Größe für die Datenbank anzugeben.
2. Legen Sie den Serverkontext mithilfe von "New-AzureSqlDatabaseServerContext" fest, und verwenden Sie die im Abschnitt "Verwenden von Azure PowerShell-Befehlen" angegebene Beispielsyntax .
3. Gehen Sie wie folgt vor:
 - Rufen Sie ein Handle für die Datenbank ab.
 - Rufen Sie ein Handle für die Leistungsstufe ab.
 - Verwenden Sie "Set-AzureSqlDatabase –ServiceObjective", um die Dienstebene, die Leistungsstufe und die maximale Größe für die Datenbank anzugeben.

**Verwendungsbeispiel**

Dieses Beispiel zeigt, wie für eine Datenbank auf Premium-Dienstebene ein Downgrade auf Standard-Dienstebene durchgeführt wird:

- Das $db-Handle, das auf den Datenbanknamen "somedb" zeigt, wird erstellt.

- Die Variable "$S2", die auf die Standard-Leistungsstufe "S2" verweist, wird erstellt.

- Die Leistungsstufe für die "$db"-Datenbank wird auf $S2 festgelegt.

- Geben Sie die Dienstebene der Datenbank und die maximale Größe für die Datenbank mit den Parametern "–Edition" und "–MaxSizeGB" an. Der für den Parameter "–MaxSizeGB" angegebene Wert muss für die Ziel-Dienstebene gültig sein. Eine Tabelle mit den Werten für die maximale Größe (MaxSize) jeder Dienstebene finden Sie weiter oben in diesem Thema.

		Windows PowerShell:

		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$S2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "S2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $S2 –Edition Standard –MaxSizeGB 40

##Ändern von Leistungsstufen
Sie können die Leistungsstufen einer Standard- oder Premium-Datenbank mithilfe der folgenden Methoden hinauf- oder herabsetzen. Es kann einige Zeit dauern, bis die Leistungsstufe der Datenbank geändert ist. Weitere Informationen finden Sie im folgenden Abschnitt [Auswirkungen von Änderungen an Premium-Datenbanken](https://msdn.microsoft.com/library/azure/dn369872.aspx#Impact).

Wenn Sie die Leistungsstufe einer Premium-Datenbank ändern, für die Aktive geografische Replikation-Beziehungen konfiguriert sind, verwenden Sie die folgende Reihenfolge für primäre und aktive sekundäre Datenbanken:

Der Grund hierfür ist, dass die aktiven sekundären Datenbanken die gleiche Leistungsstufe wie die primäre Datenbank oder eine höhere aufweisen müssen.
- Wenn Sie die Leistungsstufe von einer höheren in eine niedrigere ändern, beginnen Sie zuerst mit der primären Datenbank, gefolgt von einer oder mehreren aktiven sekundären Datenbanken.

- Wenn Sie die Leistungsstufe von einer niedrigeren auf eine höhere ändern, beginnen Sie mit den aktiven sekundären Datenbanken, schließlich gefolgt von der primären Datenbank.

###Verwenden des Azure-Verwaltungsportals
1. Verwenden Sie Ihr Microsoft-Konto, um sich am Azure-Verwaltungsportal anzumelden.
2. Navigieren Sie zur Registerkarte **SQL-DATENBANKEN** .
3. Wählen Sie für das Konto oder für einen bestimmten Server eine Datenbank aus der Liste **Datenbanken** aus. Daraufhin wird die Datenbank auf dem **Datenbank-Dashboard** oder der Seite **Schnellstart** geöffnet.
5. Wählen Sie die Registerkarte **Skalieren** für die Datenbank aus.
6. Wählen Sie für die Option **Leistungsstufe** eine Leistungsstufe aus.
7. Klicken Sie in der Befehlsleiste unten im Bildschirm auf die Schaltfläche **Speichern**.

###Verwenden von Azure PowerShell
1. Verwenden Sie "Set-AzureSqlDatabase", um die Leistungsstufe für die Datenbank anzugeben.
2. Legen Sie den Serverkontext mithilfe des Cmdlets "New-AzureSqlDatabaseServerContext" fest. Die Beispielsyntax ist im Abschnitt "Verwenden von Azure PowerShell-Befehlen" angegeben.
3. Gehen Sie wie folgt vor:
- Rufen Sie ein Handle für die Datenbank ab.
- Rufen Sie ein Handle für die Leistungsstufe ab.
- Geben Sie die Leistungsstufe mithilfe von "Set-AzureSqlDatabase –ServiceObjective" an.

**Verwendungsbeispiel**

In diesem Beispiel:

- Das $db-Handle, das auf den Datenbanknamen "somedb" zeigt, wird erstellt.

- Das $P2-Handle, das auf die Premium-Leistungsstufe "2" verweist, wird erstellt.

- Die Leistungsstufe für die "$db"-Datenbank wird auf "$P2" festgelegt.

		Windows PowerShell
		$db = Get-AzureSqlDatabase $serverContext –DatabaseName “somedb”

		$P2 = Get-AzureSqlDatabaseServiceObjective $serverContext -ServiceObjectiveName "P2"

		Set-AzureSqlDatabase $serverContext –Database $db –ServiceObjective $P2

##Auswirkungen von Änderungen an Datenbanken
Dieser Abschnitt beschreibt die Auswirkungen, die sich durch ein Upgrade auf eine Standard- oder Premium-Dienstebene bzw. durch Änderungen der Leistungsstufe Ihrer Datenbank ergeben können.

###Behandeln von Anwendungsverbindungen bei Änderungen der Datenbank
Verbindungen mit der Datenbank können vorübergehend unterbrochen werden, bis die Änderung einer Leistungsstufe oder ein Upgrade/Downgrade abgeschlossen ist. Anschließend kann es einige Sekunden dauern, bis die Verbindungen wiederhergestellt sind. SQL-Datenbankanwendungen sollten die nötige Resilienz für Verbindungsunterbrechungen aufweisen, da diese bei einer SQL-Datenbank jederzeit vorkommen können, wenn ein Computer im Datencenter ausfällt und der SQL-Datenbankdienst ein Failover der Datenbank ausführt. Die Anwendung erfordert keine Implementierungsänderung, damit eine Premium-Datenbank verwendet oder die Leistungsstufe einer Premium-Datenbank geändert werden kann.

###Verstehen und Einschätzen der Latenz bei Datenbankänderungen
Eine Änderung des SLO einer Datenbank umfasst häufig auch die Verschiebung von Daten. Daher können viele Stunden verstreichen, bis die Änderungsanforderung abgeschlossen ist und die zugehörige Abrechnungsgrundlage wirksam wird. Die Datenverschiebung tritt bei Änderungen auf, wenn ein Upgrade/Downgrade einer Datenbank ausgeführt wird oder wenn die Leistungsstufe der Datenbank geändert wird.

**Latenz bei SLO-Änderungen, die Datenverschiebungen umfassen**

Nachdem die Speichergröße der Datenbank ermittelt wurde, kann die Latenz einer SLO-Änderungsanforderung mithilfe der folgenden Heuristik geschätzt werden:

3 x (5 Minuten + Datenbankgröße / 150 MB/Minute)

Wenn die Datenbankgröße beispielsweise 50 GB beträgt, wird die Latenz der SLO-Änderungsanforderung mithilfe der folgenden Heuristik geschätzt:

3 x (5 Minuten + 50 GB x 1024 MB / GB / 150 MB/Minute) ≈17 Stunden

Die untere und obere Grenze dieser heuristischen Schätzwerte variieren zwischen 15 Minuten bei einer leeren Datenbank und etwa 2 Tagen bei einer Datenbank mit 150 GB. Schätzungen können je nach den Bedingungen im Rechenzentrum weiter variieren.

**Latenz beim Wechsel von einer höheren auf eine niedrigere Leistungsstufe**

In der Regel findet keine Datenverschiebung statt, wenn die Leistungsstufe einer Datenbank von einer höheren in eine niedrigere geändert wird. In solchen Fällen ist die Latenz der SLO-Änderung wesentlich schneller und wird normalerweise innerhalb von Sekunden abgeschlossen.

Warnung: Die obige Aussage gilt nur für Downgrades zwischen Premium- und Standard-Dienstebenen. Downgrades auf die Web-, Business- oder Basic-Dienstebene umfassen das Verschieben von Daten.

###Überprüfen des Status der Änderung an einer Datenbank
Sie können eine der folgenden Methoden verwenden, um den Status der Datenbank während eines Upgrades/Downgrades zwischen Dienstebenen oder beim Ändern der Leistungsstufe zu überprüfen.

####Verwenden des Azure-Verwaltungsportals
1. Verwenden Sie Ihr Microsoft-Konto, um sich am Azure-Verwaltungsportal anzumelden.
2. Wählen Sie aus der Liste **Datenbanken** eine Datenbank aus. Daraufhin wird die Datenbank auf dem **Datenbank-Dashboard** oder der Seite **Schnellstart** geöffnet.
3. Im **Datenbank-Dashboard** finden Sie die Statusinformationen im Bereich **Auf einen Blick** im Abschnitt **Edition**.
4. Das Servicelevelziel (Service Level Objective, SLO) stellt die Leistungsstufe innerhalb einer Dienstebene dar.


##Verwenden der Azure PowerShell-Befehle
Dieser Abschnitt beschreibt die Voraussetzungen für die Verwendung der Azure PowerShell-Befehle.

**Voraussetzungen**

Um die in diesem Thema beschriebenen Azure PowerShell-Cmdlets zu verwenden, muss die folgende Software auf dem Computer installiert sein, auf dem PowerShell ausgeführt wird. 
1. Laden Sie unter der folgenden Adresse eine Version von Windows PowerShell herunter, die mindestens die Versionsnummer 3.0 aufweist: http://www.microsoft.com/de-de/download/details.aspx?id=34595.

2. Laden Sie Azure PowerShell im Abschnitt "Befehlszeilentool" unter [Downloadseite für Azure SDK und Tools](http://azure.microsoft.com/downloads/) herunter.

Gehen Sie wie folgt vor:
Navigieren Sie im **Startbildschirm** oder **Startmenü** zu Azure PowerShell, und starten Sie dann Azure PowerShell.

Geben Sie den Benutzernamen und das Kennwort für den Server ein.

Erstellen Sie den Serverkontext mithilfe von**New-AzureSqlDatabaseServerContext**.

**Beispiel**

		Windows PowerShell
		$subId = <Subscription ID>
		$thumbprint = <Certificate Thumbprint>
		$myCert = Get-Item Cert:\CurrentUser\My\$thumbprint
		Set-AzureSubscription -SubscriptionName "mySubscription" -SubscriptionId $subId -Certificate $myCert
		Select-AzureSubscription -SubscriptionName "mySubscription"
		$serverContext = New-AzureSqlDatabaseServerContext -ServerName "myserver" -UseSubscription


**Referenz zu Azure PowerShell**
Ausführliche Informationen zu den in diesem Thema verwendeten Azure PowerShell-Cmdlets finden Sie unter [Azure SQL-Datenbank-Cmdlets](https://msdn.microsoft.com/library/dn546726.aspx).

[New-AzureSqlDatabaseServerContext](http://go.microsoft.com/fwlink/?LinkId=391026)

[New-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391027)

[Set-AzureSqlDatabase](http://go.microsoft.com/fwlink/?LinkId=391412)
 

<!---HONumber=August15_HO6-->