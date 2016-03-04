<properties
	pageTitle="Debuggen von Hadoop in HDInsight: Anzeigen von Protokollen und Verstehen von Fehlermeldungen | Microsoft Azure"
	description="Lernen Sie die Fehlermeldungen kennen, die Sie bei der Administration von HDInsight mit PowerShell erhalten können und Schritte zu deren Behebung."
	services="hdinsight"
	tags="azure-portal"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="jgao"/>

# Analysieren von HDInsight-Protokollen

Jeder Hadoop-Cluster in Azure HDInsight hat ein Azure-Speicherkonto, das als Standarddateisystem verwendet wird. Dieses Konto wird als Standardspeicherkonto bezeichnet. Der Cluster nutzt im standardmäßigen Speicherkonto Azure Table Storage und Blob Storage zum Speichern seiner Protokolle. Informationen zum Ermitteln des Standardspeicherkontos für Ihren Cluster finden Sie unter [Verwalten von Hadoop-Clustern in HDInsight](hdinsight-administer-use-management-portal.md#find-the-default-storage-account). Die Protokolle verbleiben im Speicherkonto, auch nachdem der Cluster gelöscht wurde.

##In Azure-Tabellen geschriebene Protokolle

Die in Azure-Tabellen geschriebenen Protokolle bieten einen ersten Einblick in das, was in einem HDInsight-Cluster passiert.

Wenn Sie einen HDInsight-Cluster erstellen, werden für Linux-basierte Cluster im standardmäßigen Tabellenspeicher automatisch sechs Tabellen erstellt:

- hdinsightagentlog
- syslog
- daemonlog
- hadoopservicelog
- ambariserverlog
- ambariagentlog

Für Windows-basierte Cluster werden drei Tabellen erstellt:

- setuplog: Protokoll mit Ereignissen/Ausnahmen bei der Bereitstellung/Einrichtung von HDInsight-Clustern
- hadoopinstalllog: Protokoll mit Ereignissen/Ausnahmen bei der Installation von Hadoop im Cluster. Diese Tabelle ist möglicherweise hilfreich beim Debuggen von Problemen im Zusammenhang mit Clustern, die mit benutzerdefinierten Parametern erstellt wurden.
- hadoopservicelog: Protokoll mit Ereignissen/Ausnahmen, die von allen Hadoop-Diensten aufgezeichnet wurden. Diese Tabelle ist möglicherweise hilfreich beim Debuggen von Problemen im Zusammenhang mit Auftragsfehlern in HDInsight-Clustern.

Die Tabellendateinamen lauten **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**.

Diese Tabellen enthalten die folgenden Felder:

- ClusterDnsName
- ComponentName
- EventTimestamp
- Host
- MALoggingHash
- Message
- N
- PreciseTimeStamp
- Role
- RowIndex
- Tenant
- TIMESTAMP
- TraceLevel

### Tools für den Zugriff auf die Protokolle

Es gibt viele Tools für den Zugriff auf Daten in diesen Tabellen:

-  Visual Studio
-  Azure-Speicher-Explorer
-  Power Query für Excel

#### Verwenden von Power Query für Excel

Power Query kann von [https://www.microsoft.com/de-de/download/details.aspx?id=39379](http://www.microsoft.com/de-DE/download/details.aspx?id=39379) heruntergeladen und anschließend installiert werden. Auf dieser Downloadseite finden Sie die Systemanforderungen.

**So verwenden Sie Power Query zum Öffnen und Analysieren des Dienstprotokolls**

1. Öffnen Sie **Microsoft Excel**.
2. Klicken Sie im Menü **Power Query** auf **Von Azure** und dann auf **Von Microsoft Azure Table Storage**.
 
	![HDInsight, Hadoop, Excel, PowerQuery, Azure Table Storage öffnen](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Geben Sie den Namen des Speicherkontos ein. Dies kann entweder der Kurzname oder FQDN sein.
4. Geben Sie den Schlüssel des Speicherkontos ein. Eine Liste mit Tabellen sollte angezeigt werden:

	![In Azure Table Storage gespeicherte HDInsight Hadoop-Protokolle](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Klicken Sie im Bereich **Navigator** mit der rechten Maustaste auf die Tabelle „hadoopservicelog“, und wählen Sie **Bearbeiten** aus. Es werden vier Spalten angezeigt. Löschen Sie optional die Spalten **Partition Key**, **Row Key** und **Timestamp**, indem Sie sie auswählen und dann auf dem Menüband auf **Spalten entfernen** klicken.
6. Klicken Sie auf das Symbol zum Erweitern der Spalte „Inhalt“, um die Spalten auszuwählen, die Sie in die Excel-Tabelle importieren möchten. Für diese Demo habe ich „TraceLevel“ und „ComponentName“ gewählt, um mir einige grundlegenden Informationen zu Komponenten mit Problemen zu verschaffen.

	![HDInsight Hadoop-Protokolle, Spalten auswählen](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Klicken Sie auf **OK**, um die Daten zu importieren.
8. Wählen Sie die Spalten **TraceLevel**, **Role** und **ComponentName** aus, und klicken Sie dann auf dem Menüband auf das Steuerelement „Gruppieren nach“.
9. Klicken Sie im Dialogfeld „Gruppieren nach“ auf **OK**.
10. Klicken Sie auf „Übernehmen und schließen“.
 
Sie können jetzt Excel zum Filtern und Sortieren verwenden. Sie können nach Wunsch andere Spalten einbeziehen (z. B. „Message“), um auftretende Probleme detailliert zu untersuchen. Doch das zuvor beschriebene Auswählen und Gruppieren der Spalten bietet einen guten Überblick darüber, was in Hadoop-Diensten passiert. Das gleiche Prinzip kann auf die Tabellen „setuplog“ und „hadoopinstalllog“ angewendet werden.

#### Verwenden von Visual Studio

**So verwenden Sie Visual Studio**

1. Öffnen Sie Visual Studio.
2. Klicken Sie im Menü **Ansicht** auf **Cloud-Explorer**. Oder drücken Sie einfach **STRG+\\, STRG+X**.
3. Wählen Sie in **Cloud-Explorer** die Option **Ressourcentypen** aus. Die andere verfügbare Option ist **Ressourcengruppen**.
4. Erweitern Sie **Speicherkonten**, das Standardspeicherkonto für Ihren Cluster, und klicken Sie dann **Tabellen**.
5. Doppelklicken Sie auf „hadoopservicelog“.
6. Fügen Sie einen Filter hinzu. Beispiel:
	
		TraceLevel eq 'ERROR'

	![HDInsight Hadoop-Protokolle, Spalten auswählen](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)

	Weitere Informationen zum Erstellen von Filtern finden Sie unter [Erstellen von Filterzeichenfolgen für den Tabellen-Designer](https://msdn.microsoft.com/library/azure/ff683669.aspx).
 
##In Azure Blob Storage geschriebene Protokolle

Die [in Azure-Tabellen geschriebenen Protokolle](#log-written-to-azure-tables) bieten einen ersten Einblick in das, was in einem HDInsight-Cluster passiert. Diese Tabellen bieten jedoch keine Protokolle auf Aufgabenebene, die bei der weiteren Untersuchung auftretender Probleme hilfreich sein können. Um diese nächste Detailebene zu bieten, sind HDInsight-Cluster für das Schreiben von Aufgabenprotokollen in Ihr Blob Storage-Konto für alle Aufträge konfiguriert, die über Templeton übermittelt werden. In der Praxis handelt es sich um Aufträge, die mithilfe von Microsoft Azure PowerShell-Cmdlets oder .NET-APIs für die Auftragsübermittlung übermittelt wurden, und nicht um Aufträge, die über einen RDB-/Befehlszeilenzugriff auf den Cluster übermittelt wurden.

Informationen zum Anzeigen der Protokolle finden Sie unter [Zugreifen auf YARN-Anwendungsprotokolle unter Linux-basiertem HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md).

Weitere Informationen zu Anwendungsprotokollen finden Sie unter [Simplifying user-logs management and access in YARN](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) (Vereinfachen der Verwaltung von und des Zugriffs auf Benutzerprotokolle in YARN).
 
 
## Anzeigen von Clusterintegritäts- und Auftragsprotokollen

###Öffnen der Hadoop-Benutzeroberfläche

Klicken Sie im Azure-Portal auf den Namen eines HDInsight-Clusters, um das Clusterblatt zu öffnen. Klicken Sie auf dem Clusterblatt auf **Dashboard**.

![Clusterdashboard starten](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard.png)

Geben Sie die Anmeldeinformationen für den Clusteradministrator ein, wenn Sie dazu aufgefordert werden. Klicken Sie in der daraufhin angezeigten Abfragekonsole auf **Hadoop UI**.

![Hadoop-Benutzeroberfläche starten](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard-hadoop-ui.png)


###Öffnen der YARN-Benutzeroberfläche

Klicken Sie im Azure-Portal auf den Namen eines HDInsight-Clusters, um das Clusterblatt zu öffnen. Klicken Sie auf dem Clusterblatt auf **Dashboard**. Geben Sie die Anmeldeinformationen für den Clusteradministrator ein, wenn Sie dazu aufgefordert werden. Klicken Sie in der daraufhin angezeigten Abfragekonsole auf **YARN UI**.

Über die YARN-Benutzeroberfläche haben Sie folgende Möglichkeiten:

* **Abrufen des Clusterstatus**. Erweitern Sie im linken Bereich **Cluster**, und klicken Sie auf **Info**. Der aktuelle Clusterstatus enthält Informationen wie insgesamt zugeordneter Arbeitsspeicher, verwendete Kerne, Status des Clusterressourcen-Managers, Clusterversion usw.

    ![Clusterdashboard starten](./media/hdinsight-debug-jobs/hdi-debug-yarn-cluster-state.png)

* **Abrufen des Knotenstatus**. Erweitern Sie im linken Bereich **Cluster**, und klicken Sie auf **Knoten**. Hiermit werden alle Knoten im Cluster, die HTTP-Adresse der einzelnen Knoten, die jeweils zugewiesenen Ressourcen usw. aufgeführt.

* **Überwachen des Auftragsstatus**. Erweitern Sie im linken Bereich **Cluster**, und klicken Sie dann auf **Anwendungen**, um alle Aufträge im Cluster aufzulisten. Wenn Sie nur Aufträge in einem bestimmten Zustand betrachten möchten (z. B. neu, übermittelt, ausgeführt usw.), klicken Sie auf den entsprechenden Link unter **Anwendungen**. Sie können außerdem auf den Auftragsnamen klicken, um weitere Informationen zum Auftrag abzurufen, z. B. Ausgabe, Protokolle usw.

###Öffnen der HBase-Benutzeroberfläche

Klicken Sie im Azure-Portal auf den Namen eines HDInsight HBase-Clusters, um das Clusterblatt zu öffnen. Klicken Sie auf dem Clusterblatt auf **Dashboard**. Geben Sie die Anmeldeinformationen für den Clusteradministrator ein, wenn Sie dazu aufgefordert werden. Klicken Sie in der daraufhin angezeigten Abfragekonsole auf **HBase UI**.

## HDInsight-Fehlercodes

Die in diesem Abschnitt beschriebenen Fehlermeldungen sollen den Benutzern von Hadoop in Azure HDInsight dabei helfen, mögliche Fehlerbedingungen zu verstehen, auf die sie möglicherweise stoßen, wenn sie den Dienst mit Azure PowerShell verwalten. Es werden auch Schritte zur Behebung des Problems erörtert.

Einige dieser Fehlermeldungen können auch im Azure-Portal angezeigt werden, wenn HDInsight-Cluster darüber verwaltet werden. Andere Fehlermeldungen, auf die Sie möglicherweise stoßen, sind jedoch eventuell weniger differenziert, da in diesem Kontext gewisse Beschränkungen für die Lösungsschritte bestehen. Andere Fehlermeldungen erscheinen in einem Kontext, in dem die Lösung offensichtlich ist.

### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
- **Beschreibung**: Stellen Sie für mindestens eine Komponente Informationen zur Azure SQL-Datenbank bereit, damit benutzerdefinierte Einstellungen für die Hive- und Oozie-Metastores verwendet werden können.
- **Lösung**: Der Benutzer muss einen gültigen SQL Azure-Metastore angeben. Dann kann er die Anforderung wiederholen.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
- **Beschreibung**: Es konnte kein Cluster in der Region *NameIhrerRegion* erstellt werden. Verwenden Sie eine gültige HDInsight-Region, und wiederholen Sie die Anforderung.
- **Lösung**: Kunden sollten die Clusterregion erstellen, in der sie derzeit unterstützt werden: Südostasien, Westeuropa, Nordeuropa, USA (Osten) oder USA (Westen).  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
- **Beschreibung**: Der Server konnte den angeforderten Clusterdatensatz nicht finden.  
- **Lösung**: Wiederholen Sie den Vorgang.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
- **Beschreibung**: Der Cluster-DNS-Name *IhrDnsName* ist ungültig. Stellen Sie sicher, dass der Name mit einem alphanumerischen Zeichen beginnt und endet. Er darf nur das Sonderzeichen "-" enthalten.  
- **Lösung**: Vergewissern Sie sich, dass Sie für Ihren Cluster einen gültigen DNS-Namen verwenden, der mit einem alphanumerischen Zeichen beginnt und endet und keine anderen Sonderzeichen als den Bindestrich "-" enthält. Wiederholen Sie dann den Vorgang.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
- **Beschreibung**: Der Clustername *IhrClusterName* ist nicht verfügbar. Wählen Sie einen anderen Namen.  
- **Lösung**: Der Benutzer sollte einen eindeutigen, noch nicht vorhandenen Clusternamen angeben und dann den Vorgang wiederholen. Wenn der Benutzer das Portal verwendet, wird er bei der Erstellung über die Benutzeroberfläche informiert, wenn ein Clustername bereits vorhanden ist.


### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
- **Beschreibung**: Das Clusterkennwort ist ungültig. Das Kennwort muss mindestens 10 Zeichen lang sein und mindestens eine Ziffer, einen Groß- und einen Kleinbuchstaben sowie ein Sonderzeichen enthalten. Es darf keine Leerzeichen und nicht den Benutzernamen enthalten.  
- **Lösung**: Geben Sie ein gültiges Clusterkennwort ein, und wiederholen Sie den Vorgang.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
- **Beschreibung**: Der Clusterbenutzername ist ungültig. Stellen Sie sicher, dass der Benutzername keine Sonderzeichen oder Leerzeichen enthält.  
- **Lösung**: Geben Sie einen gültigen Clusterbenutzernamen ein, und wiederholen Sie den Vorgang.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
- **Beschreibung**: Der Cluster-DNS-Name *IhrDnsClusterName* ist ungültig. Stellen Sie sicher, dass der Name mit einem alphanumerischen Zeichen beginnt und endet. Er darf nur das Sonderzeichen "-" enthalten.  
- **Lösung**: Geben Sie einen gültigen DNS-Clusterbenutzernamen ein, und wiederholen Sie den Vorgang.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
- **Beschreibung**: Der Containername in URI *IhreContainerURI* und der DNS-Name *IhrDnsName* im Anforderungstext müssen übereinstimmen.  
- **Lösung**: Stellen Sie sicher, dass der Containername und DNS-Name identisch sind, und wiederholen Sie den Vorgang.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
- **Beschreibung**: Ungültige Clusterkonfiguration. Es können keine Datenknotendefinitionen in der Knotengröße gefunden werden.  
- **Lösung**: Wiederholen Sie den Vorgang.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
- **Beschreibung**: Das Löschen der Bereitstellung für den Cluster ist fehlgeschlagen.  
- **Lösung**: Führen Sie den Löschvorgang erneut aus.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
- **Beschreibung**: Fehler bei der Dienstkonfiguration. Die erforderlichen DNS-Zuordnungsinformationen wurden nicht gefunden.  
- **Lösung**: Löschen Sie den Cluster, und erstellen Sie einen neuen Cluster.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
- **Beschreibung**: Es wurde zweimal versucht, einen Clustercontainer zu erstellen. Für *NameIhresContainers* ist ein Datensatz vorhanden, die ETags stimmen jedoch nicht überein.
- **Lösung**: Geben Sie für den Container einen eindeutigen Namen an, und wiederholen Sie den Vorgang.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
- **Beschreibung**: Der gehostete Dienst *NameIhresGehostetenDiensts* enthält bereits einen Cluster. Ein gehosteter Dienst kann nicht mehrere Cluster enthalten.  
- **Lösung**: Hosten Sie den Cluster in einem anderen gehosteten Dienst.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
- **Beschreibung**: Der Server konnte den Status der Clusterbereitstellung nicht aktualisieren.  
- **Lösung**: Wiederholen Sie den Vorgang. Falls dies mehrfach passiert, wenden Sie sich an den CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
- **Beschreibung**: Der Cluster *IhrClusterName* wurde im Rahmen von Wartungsarbeiten gelöscht. Erstellen Sie den Cluster neu.
- **Lösung**: Erstellen Sie den Cluster neu.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
- **Beschreibung**: Ungültige Clusterkonfiguration. Die erforderliche Hauptknotenkonfiguration wurde in den Knotengrößen nicht gefunden.
- **Lösung**: Wiederholen Sie den Vorgang.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
- **Beschreibung**: Der gehostete Dienst *NameIhresGehostetenDiensts* konnte nicht erstellt werden. Wiederholen Sie die Anforderung.  
- **Lösung**: Wiederholen Sie die Anforderung.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
- **Beschreibung**: Der gehostete Dienst *NameIhresGehostetenDiensts* verfügt bereits über eine Produktionsbereitstellung. Ein gehosteter Dienst kann nicht mehrere Produktionsbereitstellungen enthalten. Wiederholen Sie die Anforderung mit einem anderen Clusternamen.
- **Lösung**: Verwenden Sie einen anderen Clusternamen, und wiederholen Sie die Anforderung.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
- **Beschreibung**: Der gehostete Dienst *NameIhresGehostetenDiensts* konnte nicht für den Cluster gefunden werden.  
- **Lösung**: Wenn der Cluster im Fehlerzustand ist, löschen Sie ihn, und versuchen Sie es erneut.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
- **Beschreibung**: Dem gehosteten Dienst *NameIhresGehostetenDiensts* wurde keine Bereitstellung zugeordnet.  
- **Lösung**: Wenn der Cluster im Fehlerzustand ist, löschen Sie ihn, und versuchen Sie es erneut.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
- **Beschreibung**: Die Abonnement-ID *IhreAbonnementID* hat keine Kerne mehr übrig, um den Cluster *IhrClusterName* zu erstellen. Erforderlich: *erforderlicheRessourcen*, Verfügbare: *verfügbareRessourcen*.  
- **Lösung**: Geben Sie Ressourcen in Ihrem Abonnement frei, oder erhöhen Sie die für das Abonnement verfügbaren Ressourcen, und versuchen Sie erneut, den Cluster zu erstellen.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
- **Beschreibung**: Die Abonnement-ID *IhreAbonnementID* hat kein Kontingent für einen neuen gehosteten Dienst, um den Cluster *IhrClusterName* zu erstellen.  
- **Lösung**: Geben Sie Ressourcen in Ihrem Abonnement frei, oder erhöhen Sie die für das Abonnement verfügbaren Ressourcen, und versuchen Sie erneut, den Cluster zu erstellen.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
- **Beschreibung**: Auf dem Server ist ein interner Fehler aufgetreten. Wiederholen Sie die Anforderung.  
- **Lösung**: Wiederholen Sie die Anforderung.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
- **Beschreibung**: Der Azure Storage-Standort *DatenRegionName* ist kein gültiger Standort. Stellen Sie sicher, dass die Region korrekt ist, und wiederholen Sie die Anforderung.
- **Lösung**: Wählen Sie einen Speicherort aus, an dem HDInsight unterstützt wird. Vergewissern Sie sich, dass Ihr Cluster am selben Standort bereitgestellt wird, und wiederholen Sie den Vorgang.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
- **Beschreibung**: Ungültige Größe des virtuellen Computers für Datenknoten. Nur die Größe "Large VM" wird für alle Datenknoten unterstützt.  
- **Lösung**: Geben Sie die unterstützte Knotengröße für den Datenknoten an, und wiederholen Sie den Vorgang.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
- **Beschreibung**: Ungültige Größe des virtuellen Computers für den Hauptknoten. Nur die Größe "ExtraLarge VM" wird für Hauptknoten unterstützt.  
- **Lösung**: Geben Sie die unterstützte Knotengröße für den Hauptknoten an, und wiederholen Sie den Vorgang.

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
- **Beschreibung**: Die verwendete Abonnement-ID *IhreAbonnementID* verfügt nicht über ausreichende Berechtigungen, um den Löschvorgang für den Cluster *IhrClusterName* auszuführen.  
- **Lösung**: Wenn der Cluster im Fehlerzustand ist, löschen Sie ihn, und versuchen Sie es erneut.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
- **Beschreibung**: Der Blobcontainername *IhrContainername* des externen Speicherkontos ist ungültig. Stellen Sie sicher, dass der Name mit einem Buchstaben beginnt und nur Kleinbuchstaben, Ziffern und Bindestriche enthält.  
- **Lösung**: Geben Sie einen gültigen Blobcontainernamen für das Speicherkonto ein, und wiederholen Sie den Vorgang.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
- **Beschreibung**: Die Konfiguration für das externe Speicherkonto *NameIhresSpeicherkontos* ist erforderlich, damit Details zum geheimen Schlüssel festgelegt werden können.  
- **Lösung**: Geben Sie einen gültigen geheimen Schlüssel für das Speicherkonto ein, und wiederholen Sie den Vorgang.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
- **Beschreibung**: Der Versionsheader *IhrVersionsheader* hat nicht das gültige Format JJJJ-MM-TT.  
- **Lösung**: Geben Sie den Versionsheader im gültigen Format ein, und wiederholen Sie die Anforderung.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
- **Beschreibung**: Ungültige Clusterkonfiguration. Es wurde mehr als eine Hauptknotenkonfiguration gefunden.  
- **Lösung**: Bearbeiten Sie die Konfiguration so, dass nur ein Hauptknoten angegeben ist.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
- **Beschreibung**: Der Vorgang konnte nicht innerhalb der zulässigen Zeit oder nach der maximalen Anzahl von Versuchen abgeschlossen werden. Wiederholen Sie die Anforderung.  
- **Lösung**: Wiederholen Sie die Anforderung.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
- **Beschreibung**: Der Parameter *IhrParametername* darf nicht null oder leer sein.  
- **Lösung**: Geben Sie einen gültigen Wert für den Parameter an.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
- **Beschreibung**: Mindestens eine der Eingaben für die Anforderung zur Clustererstellung ist ungültig. Stellen Sie sicher, dass die Eingabewerte korrekt sind, und wiederholen Sie die Anforderung.  
- **Lösung**: Stellen Sie sicher, dass die Eingabewerte korrekt sind, und wiederholen Sie die Anforderung.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
- **Beschreibung**: Die Regionsfunktion ist für die Region *NameIhrerRegion* und Abonnement-ID *IhreAbonnementID* nicht verfügbar.  
- **Lösung**: Geben Sie eine Region an, die HDInsight-Cluster unterstützt. Öffentlich unterstützte Regionen: Südostasien, Westeuropa, Nordeuropa, USA Ost oder USA West.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
- **Beschreibung**: Das Speicherkonto *NameIhresSpeicherkontos* befindet sich in der Region *NameAktuellerRegion*. Sie sollte identisch mit der Clusterregion *IhrClusterRegionName* sein.  
- **Lösung**: Geben Sie entweder ein Speicherkonto in der gleichen Region an, in dem sich Ihr Cluster befindet, oder erstellen Sie einen neuen Cluster in der gleichen Region wie das vorhandene Speicherkonto, wenn sich Ihre Daten bereits im Speicherkonto befinden. Wenn Sie das Portal verwenden, wird der Benutzer im Vorhinein über die Benutzeroberfläche über dieses Problem informiert.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
- **Beschreibung**: Die angegebene Abonnement-ID *IhreAbonnementID* ist nicht aktiv.  
- **Lösung**: Aktivieren Sie Ihr Abonnement erneut, oder erwerben Sie ein gültiges Abonnement.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
- **Beschreibung**: Die Abonnement-ID *IhreAbonnementID* wurde nicht gefunden.  
- **Lösung**: Vergewissern Sie sich, dass Ihre Abonnement-ID gültig ist, und wiederholen Sie den Vorgang.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
- **Beschreibung**: DNS *IhreDnsUrl* konnte nicht aufgelöst werden. Stellen Sie sicher, dass die vollqualifizierte URL für den Blobendpunkt bereitgestellt wird.  
- **Lösung**: Geben Sie eine gültige Blob-URL an. Die URL MUSS uneingeschränkt gültig sein, d. h., sie muss mit „*http://* “ beginnen und auf *.com* enden.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
- **Beschreibung**: Der Speicherort der Ressource *IhreDnsUrl* konnte nicht überprüft werden. Stellen Sie sicher, dass die vollqualifizierte URL für den Blobendpunkt bereitgestellt wird.  
- **Lösung**: Geben Sie eine gültige Blob-URL an. Die URL MUSS uneingeschränkt gültig sein, d. h., sie muss mit „*http://* “ beginnen und auf *.com* enden.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
- **Beschreibung**: Die Versionsfunktion ist nicht für Version *AngegebeneVersion* und Abonnement-ID *IhreAbonnementID* verfügbar.  
- **Lösung**: Wählen Sie eine verfügbare Version aus, und wiederholen Sie den Vorgang.

### <a id="VersionNotSupported"></a>VersionNotSupported
- **Beschreibung**: Die Version *AngegebeneVersion* wird nicht unterstützt.
- **Lösung**: Wählen Sie eine unterstützte Version aus, und wiederholen Sie den Vorgang.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
- **Beschreibung**: Die Version *AngegebeneVersion* ist nicht in der Azure-Region *AngegebeneRegion* verfügbar.  
- **Lösung**: Wählen Sie eine Version aus, die in der angegebenen Region unterstützt wird, und wiederholen Sie den Vorgang.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
- **Beschreibung**: Ungültige Clusterkonfiguration. Die erforderliche WASB-Kontokonfiguration wurde in externen Konten nicht gefunden.  
- **Lösung**: Vergewissern Sie sich, dass das Konto vorhanden ist und ordnungsgemäß in der Konfiguration angegeben wurde, und wiederholen Sie den Vorgang.

## Nächste Schritte

[Debuggen von Tez-Aufträgen in HDInsight mithilfe von Ambari-Ansichten](hdinsight-debug-ambari-tez-view.md)[Aktivieren von Heapdumps für Hadoop-Dienste auf Linux-basierten HDInsight-Clustern](hdinsight-hadoop-collect-debug-heap-dump-linux.md)[Verwalten von HDInsight-Clustern mithilfe der Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md)

<!----HONumber=AcomDC_0218_2016-->