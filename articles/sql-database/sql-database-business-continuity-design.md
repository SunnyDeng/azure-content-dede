<properties 
   pageTitle="SQL-Datenbank-Entwurf für Geschäftskontinuität" 
   description="In diesem Abschnitt finden Sie Anleitungen für die Auswahl der Features für die Notfallwiederherstellung mit Geschäftskontinuität (BCDR). Dazu gehören Beschreibungen der Funktionen, die Sie automatisch durch Verwendung einer SQL-Datenbank erhalten."
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/13/2015"
   ms.author="elfish"/>

#Entwerfen für Geschäftskontinuität

Beim Entwerfen von Anwendungen für Geschäftskontinuität müssen Sie die folgenden Fragen beantworten:

1. Welche Funktion der Geschäftskontinuität eignet sich am besten, um meine Anwendung vor Ausfällen zu schützen?
2. Welches Maß an Redundanz und welche Replikationstopologie kann ich verwenden?

##Gründe für das Verwenden der geografischen Wiederherstellung

SQL-Datenbank bietet standardmäßig einen integrierten Basisschutz für jede Datenbank. Dies wird durch das Speichern von Sicherungskopien der Datenbank in geografisch redundantem Azure-Speicher (GRS) erreicht. Wenn Sie diese Methode auswählen, ist keine besondere Konfiguration oder zusätzliche Ressourcenzuordnung erforderlich. Sie können mit diesen Sicherungen Ihre Datenbank in einer beliebigen Region mit dem Befehl "Geo-Restore" wiederherstellen. Im Abschnitt [Wiederherstellen nach einem Ausfall](sql-database-disaster-recovery.md) finden Sie Details zur Verwendung von "geo-restore" zur Wiederherstellung der Anwendung.

Sie sollten den integrierten Schutz verwenden, wenn Ihre Anwendung die folgenden Kriterien erfüllt:

1. Es handelt sich um keine unternehmenskritische Anwendung. Sie besitzt keine bindende SLA, sodass eine Ausfallzeit von 24 Stunden oder länger keine finanzielle Haftung nach sich zieht.
2. Die Häufigkeit von Datenänderungen ist niedrig (z. B. Transaktionen pro Stunde). Ein RPO-Wert von einer Stunde führt nicht zu erheblichen Datenverlusten.
3. Bei der Anwendung muss sehr auf die Kosten geachtet werden, sodass keine zusätzlichen Kosten für die Georeplikation möglich sind. 

> [AZURE.NOTE]Bei der geografischen Wiederherstellung wird keine Rechenkapazität in bestimmten Regionen im Voraus reserviert, um aktive Datenbanken nach einem Ausfall aus einer Sicherungskopie wiederherzustellen. Der Dienst verwaltet die Arbeitsauslastung im Zusammenhang mit "geo-restore"-Anforderungen so, dass die Auswirkungen auf vorhandene Datenbanken in dieser Region minimiert werden und deren Kapazitätsanforderungen Priorität haben. Daher hängt die Wiederherstellungszeit der Datenbank davon ab, wie viele andere Datenbanken in derselben Region zur gleichen Zeit wiederhergestellt werden.

##Gründe für das Verwenden der Georeplikation

Bei der Georeplikation wird eine replizierte (sekundäre) Datenbank in einer anderen Region als die primäre Datenbank erstellt. Damit wird sichergestellt, dass die erforderlichen Daten und Rechenressourcen zur Unterstützung der Arbeitsauslastung der Anwendung nach der Wiederherstellung für die Datenbank zur Verfügung stehen. Im Abschnitt [Wiederherstellen nach einem Ausfall](sql-database-disaster-recovery.md) finden Sie Informationen zum Verwenden eines Failovers zum Wiederherstellen der Anwendung.

Sie sollten die Georeplikation verwenden, wenn Ihre Anwendung die folgenden Kriterien erfüllt:

1. Sie ist geschäftskritisch. Sie besitzt eine bindende SLA mit anspruchsvollen RPO- und RTO-Werten. Der Verlust von Daten und ein Ausfall bei der Verfügbarkeit ziehen eine finanzielle Haftung nach sich. 
2. Die Häufigkeit von Datenänderungen ist hoch (z. B. Transaktionen pro Minute oder Sekunde). Der für den Standardschutz geltende RPO-Wert von einer Stunde führt wahrscheinlich zu nicht akzeptablen Datenverlusten.
3. Die Kosten für die Verwendung der Georeplikation sind deutlich niedriger als die potenziellen finanziellen Haftungsschäden und die damit einhergehenden Geschäftsverluste.

> [AZURE.NOTE]Wenn die Anwendung Datenbanken der Basic-Ebene verwendet, wird die Georeplikation nicht unterstützt.

##Auswählen von standardmäßiger oder aktiver Georeplikation

Für Datenbanken der Standard-Ebene kann keine aktive Georeplikation verwendet werden. Wenn die Anwendung also die Datenbanken der Standardebene verwendet und die oben genannten Kriterien erfüllt, sollten Sie die standardmäßige Georeplikation aktivieren. Für Premium-Datenbanken können hingegen beide Optionen ausgewählt werden. Die standardmäßige Georeplikation wurde als einfachere und kostengünstigere Lösung für die Notfallwiederherstellung entworfen und ist damit besonders für Anwendungen geeignet, die nur vor ungeplanten Ereignissen wie Ausfällen geschützt werden sollen. Mit der standardmäßigen Georeplikation können Sie nur die gekoppelte Region für die Wiederherstellung verwenden und haben nicht die Möglichkeit, mehr als eine sekundäre Datenbank zu erstellen. Dieses letztere Feature ist wichtig für Upgradeszenarios für die Anwendung. Wenn dieses Szenario also für die Anwendung wichtig ist, sollten Sie stattdessen die aktive Georeplikation aktivieren. Weitere Informationen finden Sie unter [Aktualisieren von Anwendungen ohne Ausfallzeit](sql-database-business-continuity-application-upgrade.md).

> [AZURE.NOTE]Die aktive Georeplikation unterstützt auch schreibgeschützten Zugriff auf die sekundäre Datenbank, wodurch zusätzliche Kapazität für die schreibgeschützten Arbeitsauslastungen bereitgestellt wird.

##Aktivieren der Georeplikation

Sie können die Georeplikation im Azure-Portal oder durch Aufrufen des entsprechenden REST-API- oder PowerShell-Befehls aktivieren.

###Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.Azure.com) an.
2. Wählen Sie auf der linken Bildschirmseite **DURCHSUCHEN** und dann **SQL-Datenbanken** aus.
3. Navigieren Sie zu Ihrem Datenbank-Blatt, wählen Sie die **Geo Replication map**, und klicken Sie auf **Configure Geo-Replication**.
4. Navigieren Sie zum Blatt "Georeplikation". Wählen Sie die Zielregion aus. 
5. Navigieren Sie zum Blatt "Create Secondary". Wählen Sie einen vorhandenen Server in der Zielregion aus, oder erstellen Sie einen neuen.
6. Wählen Sie den Typ für das sekundäre Replikat aus (*lesbar* oder *nicht lesbar*)
7. Klicken Sie auf **Erstellen**, um die Konfiguration abzuschließen.

> [AZURE.NOTE]Die gekoppelte Region für die Notfallwiederherstellung wird auf dem Blatt "Georeplikation" als *empfohlen* gekennzeichnet. Wenn Sie eine Datenbank der Premium-Ebene verwenden, können Sie eine andere Region auswählen. Bei Verwendung einer Standard-Datenbank können Sie diese nicht ändern. Bei einer Premium-Datenbank kann der Typ des sekundären Replikats (*lesbar* oder *nicht lesbar*) ausgewählt werden. Bei Standard-Datenbanken kann nur ein *nicht lesbares*, sekundäres Replikat ausgewählt werden.
 
###PowerShell

Verwenden Sie das PowerShell-Cmdlet [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx) zum Automatisieren der Konfiguration der Georeplikation.

So erstellen Sie Georeplikation mit einem nicht lesbaren sekundären Replikat für eine Premium- oder Standard-Datenbank:
		
		Start-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName" -PartnerServer "PartnerServerName" –ContinuousCopy -OfflineSecondary
So erstellen Sie Georeplikation mit einem lesbaren sekundären Replikat für eine Premium-Datenbank:

		Start-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName" -PartnerServer "PartnerServerName" –ContinuousCopy
		 
Dieser Befehl ist asynchron. Verwenden Sie nach der Rückgabe das Cmdlet [Get-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720235.aspx) zum Überprüfen des Status dieses Vorgangs. Das "ReplicationState"-Feld des zurückgegebenen Objekts muss nach Abschluss des Vorgangs den Wert "CATCH_UP" aufweisen.

		Get-AzureSqlDatabaseCopy -ServerName "PrimaryServerName" -DatabaseName "PrimaryDatabaseName" -PartnerServer "SecondaryServerName"


###REST-API 

Mit der [Start Database Copy](https://msdn.microsoft.com/library/azure/dn509576.aspx)-API können Sie programmgesteuert eine Konfiguration für die Georeplikation erstellen.

Diese API ist asynchron. Verwenden Sie nach der Rückgabe die [Get Database Copy](https://msdn.microsoft.com/library/azure/dn509570.aspx)-API zum Überprüfen des Status dieses Vorgangs. Das "ReplicationState"-Feld des Antworttexts muss nach Abschluss des Vorgangs den Wert "CATCH_UP" aufweisen.


##Auswählen der Failover-Konfiguration 

Sie sollten beim Entwerfen der Anwendung für die Geschäftskontinuität verschiedene Konfigurationsoptionen berücksichtigen. Die Auswahl hängt von der Bereitstellungstopologie für die Anwendung ab und davon, welche Teile der Anwendungen am anfälligsten für einen Ausfall sind. Anleitungen finden Sie unter [Entwerfen von Cloud-Lösungen für die Notfallwiederherstellung mithilfe der aktiven Georeplikation](https://msdn.microsoft.com/library/azure/dn741328.aspx).


 

<!---HONumber=July15_HO2-->