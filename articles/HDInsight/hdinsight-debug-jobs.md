<properties 
	pageTitle="Debuggen von Hadoop in HDInsight: Fehlermeldungen | Microsoft Azure" 
	description="Lernen Sie die Fehlermeldungen kennen, die Sie bei der Administration von HDInsight mit PowerShell erhalten können und Schritte zu deren Behebung." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="bradsev"/>

# Debuggen von Hadoop in HDInsight: Verstehen von Fehlermeldungen

## Einführung
Die in diesem Thema beschriebenen Fehlermeldungen sollen den Benutzern von Hadoop in Azure HDInsight dabei helfen, mögliche Fehlerbedingungen zu verstehen, auf die sie möglicherweise stoßen, wenn sie den Dienst mit Azure PowerShell verwalten. Es werden auch Schritte zur Behebung des Problems erörtert.

Einige dieser Fehlermeldungen können auch im Azure-Portal angezeigt werden, wenn HDInsight-Cluster darüber verwaltet werden. Andere Fehlermeldungen, auf die Sie möglicherweise stoßen, sind jedoch eventuell weniger differenziert, da in diesem Kontext gewisse Beschränkungen für die Lösungsschritte bestehen. Andere Fehlermeldungen erscheinen in einem Kontext, in dem die Lösung offensichtlich ist. Wenn z. B. gegen die Beschränkungen für Parameter verstoßen wird, erscheint die Meldung rechts neben dem Feld, in das der Wert eingegeben wurde. Hier ein Fall, in dem zu viele Datenknoten angefordert wurden. Die Lösung besteht darin, die Anzahl auf einen zulässigen Wert von maximal 22 zu verringern.

![HDI.Debugging.ErrorMessages.Portal][image-hdi-debugging-error-messages-portal]

Die Fehler, auf die ein Benutzer in Azure PowerShell oder im Azure-Portal stoßen kann, sind unter [HDInsight-Fehler](#hdinsight-error-messages) alphabetisch nach Name aufgeführt. Sie sind mit dem entsprechenden Eintrag im Abschnitt mit der [Beschreibung und Lösung der Fehler](#discription-mitigation-errors) verknüpft, der die folgenden Informationen zu dem Fehler enthält:
 	
- **Beschreibung**: Fehlermeldung, die dem Benutzer angezeigt wird.	
- **Lösung**: Schritte, die zur Problembehebung unternommen werden können. 

###HDInsight-Fehlercodes

[AtleastOneSqlMetastoreMustBeProvided](#AtleastOneSqlMetastoreMustBeProvided)
 [AzureRegionNotSupported](#AzureRegionNotSupported)
 [ClusterContainerRecordNotFound](#ClusterContainerRecordNotFound)
 [ClusterDnsNameInvalidReservedWord](#ClusterDnsNameInvalidReservedWord)
 [ClusterNameUnavailable](#ClusterNameUnavailable)
 [ClusterUserNameInvalid](#ClusterUserNameInvalid)
 [ClusterUserNameInvalidReservedWord](#ClusterUserNameInvalidReservedWord) 
[ContainerNameMisMatchWithDnsName](#ContainerNameMisMatchWithDnsName)
 [DataNodeDefinitionNotFound](#DataNodeDefinitionNotFound)
 [DeploymentDeletionFailure](#DeploymentDeletionFailure)
 [DnsMappingNotFound](#DnsMappingNotFound)
 [DuplicateClusterContainerRequest](#DuplicateClusterContainerRequest) 
[DuplicateClusterInHostedService](#DuplicateClusterInHostedService) 
[FailureToUpdateDeploymentStatus](#FailureToUpdateDeploymentStatus) 
[HdiRestoreClusterAltered](#HdiRestoreClusterAltered) 
[HeadNodeConfigNotFound](#HeadNodeConfigNotFound) 
[HeadNodeConfigNotFound](#HeadNodeConfigNotFound) 
[HostedServiceCreationFailure](#HostedServiceCreationFailure) 
[HostedServiceHasProductionDeployment](#HostedServiceHasProductionDeployment) 
[HostedServiceNotFound](#HostedServiceNotFound) 
[HostedServiceWithNoDeployment](#HostedServiceWithNoDeployment) 
[InsufficientResourcesCores](#InsufficientResourcesCores) 
[InsufficientResourcesHostedServices](#InsufficientResourcesHostedServices) 
[InternalErrorRetryRequest](#InternalErrorRetryRequest) 
[InvalidAzureStorageLocation](#InvalidAzureStorageLocation) 
[InvalidNodeSizeForDataNode](#InvalidNodeSizeForDataNode) 
[InvalidNodeSizeForHeadNode](#InvalidNodeSizeForHeadNode) 
[InvalidRightsForDeploymentDeletion](#InvalidRightsForDeploymentDeletion) 
[InvalidStorageAccountBlobContainerName](#InvalidStorageAccountBlobContainerName) 
[InvalidStorageAccountConfigurationSecretKey](#InvalidStorageAccountConfigurationSecretKey) 
[InvalidVersionHeaderFormat](#InvalidVersionHeaderFormat) 
[MoreThanOneHeadNode](#MoreThanOneHeadNode) 
[OperationTimedOutRetryRequest](#OperationTimedOutRetryRequest) 
[ParameterNullOrEmpty](#ParameterNullOrEmpty) 
[PreClusterCreationValidationFailure](#PreClusterCreationValidationFailure)
 [RegionCapabilityNotAvailable](#RegionCapabilityNotAvailable) 
[StorageAccountNotColocated](#StorageAccountNotColocated) 
[SubscriptionIdNotActive](#SubscriptionIdNotActive) 
[SubscriptionIdNotFound](#SubscriptionIdNotFound) 
[UnableToResolveDNS](#UnableToResolveDNS) 
[UnableToVerifyLocationOfResource](#UnableToVerifyLocationOfResource) 
[VersionCapabilityNotAvailable](#VersionCapabilityNotAvailable) 
[VersionNotSupported](#VersionNotSupported) 
[VersionNotSupportedInRegion](#VersionNotSupportedInRegion) 
[WasbAccountConfigNotFound](#WasbAccountConfigNotFound)



## <a id="discription-mitigation-errors"></a>Diagnose und Lösung von Fehlern 


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
- **Lösung**: Geben Sie eine gültige Blob-URL an. Die URL MUSS uneingeschränkt gültig sein, das heißt, sie muss mit *http://* beginnen und mit *.com* enden. Die vollqualifizierte URL befindet sich in der Regel auf der Speicherregisterkarte des Portals unter manage.windowsazure.com. 
### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
- **Beschreibung**: Der Speicherort der Ressource *IhreDnsUrl* konnte nicht überprüft werden. Stellen Sie sicher, dass die vollqualifizierte URL für den Blobendpunkt bereitgestellt wird.  
- **Lösung**: Geben Sie eine gültige Blob-URL an. Die URL MUSS uneingeschränkt gültig sein, das heißt, sie muss mit *http://* beginnen und mit *.com* enden. Die vollqualifizierte URL befindet sich in der Regel auf der Speicherregisterkarte des Portals unter manage.windowsazure.com. 
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

## <a id="resources"></a>Zusätzliche Debuggingressourcen

* [Azure HDInsight SDK-Dokumentation][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[image-hdi-debugging-error-messages-portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png







<!--HONumber=54--> 