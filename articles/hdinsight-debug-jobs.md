c<properties urlDisplayName="Debug HDInsight Hadoop Errors" pageTitle="Debuggen von Hadoop in HDInsight: Fehlermeldungen | Azure" metaKeywords="hdinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="Learn about the error messages you might receive when administering HDInsight using PowerShell, and steps you can take to recover." services="hdinsight" title="Debug Hadoop in HDInsight: Error messages" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />

# Debuggen von Hadoop in HDInsight: Fehlermeldungen

##Einführung
Die in diesem Thema beschriebenen Fehlermeldungen sollen den Benutzern von Hadoop in Azure HDInsight dabei helfen, mögliche Fehlerbedingungen zu verstehen, auf die sie möglicherweise stoßen, wenn sie den Dienst mit Azure PowerShell verwalten. Es werden auch Schritte zur Behebung des Problems erörtert. 

Einige dieser Fehlermeldungen können auch im Azure-Portal angezeigt werden, wenn HDInsight-Cluster darüber verwaltet werden. Andere Fehlermeldungen, auf die Sie möglicherweise stoßen, sind jedoch eventuell weniger differenziert, da in diesem Kontext gewisse Beschränkungen für die Lösungsschritte bestehen. Andere Fehlermeldungen erscheinen in einem Kontext, in dem die Lösung offensichtlich ist. Wenn z. B. gegen die Beschränkungen für Parameter verstoßen wird, erscheint die Meldung rechts neben dem Feld, in das der Wert eingegeben wurde. Hier ein Fall, in dem zu viele Datenknoten angefordert wurden. Die Lösung besteht darin, die Anzahl auf einen zulässigen Wert von maximal 22 zu verringern.

![HDI.Debugging.ErrorMessages.Portal][image-hdi-debugging-error-messages-portal]

Die Fehler, auf die ein Benutzer in Azure PowerShell oder im Azure-Portal stoßen kann, sind im Abschnitt [HDInsight-Fehler](#hdinsight-error-messages) alphabetisch nach Name aufgeführt. Sie sind mit dem entsprechenden Eintrag im Abschnitt mit der [Beschreibung und Lösung der Fehler](#discription-mitigation-errors) verknüpft, der die folgenden Informationen zu dem Fehler enthält:
 	
- **Beschreibung**: angezeigte Fehlermeldung	
- **Lösung**: Schritte, die zur Problembehebung unternommen werden können 

###HDInsight-Fehler

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



<h2><a id="discription-mitigation-errors"></a>Diagnose und Lösung der Fehler</h2> 


<h3><a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided</h3>
- **Beschreibung**: Stellen Sie Azure SQL-Datenbankinformationen für mindestens eine Komponente bereit, damit benutzerdefinierte Einstellungen für die Hive- und Oozie-Metastores verwendet werden können.   
- **Lösung**: Der Benutzer muss einen gültigen SQL Azure-Metastore angeben. Dann kann er die Anforderung wiederholen.  

<h3><a id="AzureRegionNotSupported"></a>AzureRegionNotSupported</h3>
- **Beschreibung**: Es konnte kein Cluster in der Region *NameIhrerRegion* erstellt werden. Verwenden Sie eine gültige HDInsight-Region, und wiederholen Sie die Anforderung.   
- **Lösung**: Der Kunde sollte die Clusterregion erstellen, die ihn derzeit unterstützt: Südostasien, Westeuropa, Nordeuropa, USA Ost oder USA West.  

<h3><a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound</h3>
- **Beschreibung**: Der Server konnte den angeforderten Clusterdatensatz nicht finden.  
- **Lösung**: Wiederholen Sie den Vorgang. 

<h3><a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord</h3>
- **Beschreibung**: Cluster-DNS-Name *IhrDnsName* ist ungültig. Stellen Sie sicher, dass der Name mit einem alphanumerischen Zeichen beginnt und endet. Er darf nur das Sonderzeichen "-" enthalten.  
- **Lösung**: Vergewissern Sie sich, dass Sie für Ihren Cluster einen gültigen DNS-Namen verwenden, der mit einem alphanumerischen Zeichen beginnt und endet und keine anderen Sonderzeichen als den Bindestrich "-" enthält. Wiederholen Sie dann den Vorgang.

<h3><a id="ClusterNameUnavailable"></a>ClusterNameUnavailable</h3>
- **Beschreibung**: Der Clustername *IhrClustername* ist ungültig. Wählen Sie einen anderen Namen.  
- **Lösung**: Der Benutzer sollte einen eindeutigen, noch nicht vorhandenen Clusternamen angeben und dann den Vorgang wiederholen. Wenn der Benutzer das Portal verwendet, wird er bei der Erstellung über die Benutzeroberfläche informiert, wenn ein Clustername bereits vorhanden ist. 
 

<h3><a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid</h3>
- **Beschreibung**: Das Clusterkennwort ist ungültig. Das Kennwort muss mindestens 10 Zeichen lang sein und mindestens eine Ziffer, einen Groß- und einen Kleinbuchstaben sowie ein Sonderzeichen enthalten. Es darf keine Leerzeichen und nicht den Benutzernamen enthalten.  
- **Lösung**: Geben Sie ein gültiges Clusterkennwort ein, und wiederholen Sie den Vorgang. 

<h3><a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid</h3>
- **Beschreibung**: Der Clusterbenutzername ist ungültig. Stellen Sie sicher, dass der Benutzername keine Sonderzeichen oder Leerzeichen enthält.  
- **Lösung**: Geben Sie einen gültigen Clusterbenutzernamen ein, und wiederholen Sie den Vorgang.

<h3><a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord</h3>
- **Beschreibung**: Der Cluster-DNS-Name *IhrDnsClustername* ist ungültig. Stellen Sie sicher, dass der Name mit einem alphanumerischen Zeichen beginnt und endet. Er darf nur das Sonderzeichen "-" enthalten.  
- **Lösung**: Geben Sie einen gültigen DNS-Clusterbenutzernamen ein, und wiederholen Sie den Vorgang.

<h3><a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName</h3>
- **Beschreibung**: Der Containername in URI *IhrContainerURI* und der DNS-Name *IhrDnsName* im Anforderungstext müssen identisch sein.  
- **Lösung**: Stellen Sie sicher, dass Containername und DNS-Name identisch sind, und wiederholen Sie den Vorgang.

<h3><a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound</h3>
- **Beschreibung**: Ungültige Clusterkonfiguration. Es können keine Datenknotendefinitionen in der Knotengröße gefunden werden.  
- **Lösung**: Wiederholen Sie den Vorgang.

<h3><a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure</h3> 	
- **Beschreibung**: Das Löschen der Bereitstellung ist für den Cluster fehlgeschlagen.  
- **Lösung**: Führen Sie den Löschvorgang erneut aus.

<h3><a id="DnsMappingNotFound"></a>DnsMappingNotFound</h3> 
- **Beschreibung**: Fehler bei der Dienstkonfiguration. Die erforderlichen DNS-Zuordnungsinformationen wurden nicht gefunden.  
- **Lösung**: Löschen Sie den Cluster, und erstellen Sie einen neuen Cluster.

<h3><a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest</h3>
- **Beschreibung**: Es wurde zweimal versucht, den Clustercontainer zu erstellen. Für *NameIhresContainers* ist ein Datensatz vorhanden, die ETags stimmen jedoch nicht überein.   
- **Lösung**: Geben Sie für den Container einen eindeutigen Namen an, und wiederholen Sie den Erstellvorgang. 

<h3><a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService</h3>
- **Beschreibung**: Der gehostete Dienst *NameIhresgehostetenDienstes* enthält bereits einen Cluster. Ein gehosteter Dienst kann nicht mehrere Cluster enthalten.  
- **Lösung**: Hosten Sie den Cluster in einem anderen gehosteten Dienst. 

<h3><a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus</h3>
- **Beschreibung**: Der Server konnte den Status der Clusterbereitstellung nicht aktualisieren.  
- **Lösung**: Wiederholen Sie den Vorgang. Falls dies mehrfach passiert, wenden Sie sich an den CSS. 

<h3><a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered</h3>
- **Beschreibung**: Der Cluster *IhrClustername* wurde im Rahmen von Wartungsarbeiten gelöscht. Erstellen Sie den Cluster neu.     
- **Lösung**: Erstellen Sie den Cluster neu.

<h3><a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound</h3>
- **Beschreibung**: Ungültige Clusterkonfiguration. Die erforderliche Hauptknotenkonfiguration wurde in den Knotengrößen nicht gefunden.
- **Lösung**: Wiederholen Sie den Vorgang.

<h3><a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure</h3>
- **Beschreibung**: Der gehostete Dienst *NameIhresgehostetenDiensts* konnte nicht erstellt werden. Wiederholen Sie die Anforderung.  
- **Lösung**: Wiederholen Sie die Anforderung.

<h3><a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment</h3>
- **Beschreibung**: Der gehostete Dienst *NameIhresgehostetenDiensts* enthält bereits eine Produktionsbereitstellung. Ein gehosteter Dienst kann nicht mehrere Produktionsbereitstellungen enthalten. Wiederholen Sie die Anforderung mit einem anderen Clusternamen.   
- **Lösung**: Verwenden Sie einen anderen Clusternamen, und wiederholen Sie die Anforderung.

<h3><a id="HostedServiceNotFound"></a>HostedServiceNotFound</h3>
- **Beschreibung**: Der gehostete Dienst *NameIhresgehostetenDiensts* konnte für den Cluster nicht gefunden werden.  
- **Lösung**: Wenn der Cluster im Fehlerzustand ist, löschen Sie ihn, und versuchen Sie es erneut. 

<h3><a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment</h3>
- **Beschreibung**: Dem gehosteten Dienst *NameIhresgehostetenDiensts* wurde keine Bereitstellung zugeordnet.  
- **Lösung**: Wenn der Cluster im Fehlerzustand ist, löschen Sie ihn, und versuchen Sie es erneut. 

<h3><a id="InsufficientResourcesCores"></a>InsufficientResourcesCores</h3>
- **Beschreibung**: Die Abonnement-ID *IhreAbonnementID* hat keine Kerne mehr übrig, um den Cluster *IhrClustername* zu erstellen. Erforderlich: *erforderlicheRessourcen*, verfügbar: *verfügbareRessourcen*.  
- **Lösung**: Geben Sie Ressourcen in Ihrem Abonnement frei, oder erhöhen Sie die für das Abonnement verfügbaren Ressourcen, und versuchen Sie erneut, den Cluster zu erstellen.

<h3><a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices</h3>
- **Beschreibung**: Die Abonnement-ID *IhreAbonnementID* verfügt für einen neuen gehosteten Dienst über kein Kontingent, um den Cluster *IhrClustername* zu erstellen.  
- **Lösung**: Geben Sie Ressourcen in Ihrem Abonnement frei, oder erhöhen Sie die für das Abonnement verfügbaren Ressourcen, und versuchen Sie erneut, den Cluster zu erstellen.

<h3><a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest</h3>
- **Beschreibung**: Auf dem Server ist ein interner Fehler aufgetreten. Wiederholen Sie die Anforderung.  
- **Lösung**: Wiederholen Sie die Anforderung. 

<h3><a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation</h3>
- **Beschreibung**: Der Ort für den Azure-Speicher *DatenRegionName* ist kein gültiger Standort. Stellen Sie sicher, dass die Region korrekt ist, und wiederholen Sie die Anforderung.   
- **Lösung**: Wählen Sie einen Speicherort aus, der HDInsight unterstützt. Vergewissern Sie sich, dass Ihr Cluster am selben Standort bereitgestellt wird, und wiederholen Sie den Vorgang. 

<h3><a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode</h3>
- **Beschreibung**: Ungültige VM-Größe für Datenknoten. Nur die Größe "Large VM" wird für alle Datenknoten unterstützt.  
- **Lösung**: Geben Sie die unterstützte Knotengröße für den Datenknoten an, und wiederholen Sie den Vorgang. 

<h3><a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode</h3>
- **Beschreibung**: Ungültige VM-Größe für Hauptknoten. Nur die Größe "ExtraLarge VM" wird für Hauptknoten unterstützt.  
- **Lösung**: Geben Sie die unterstützte Knotengröße für den Hauptknoten an, und wiederholen Sie den Vorgang.

<h3><a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion</h3>
- **Beschreibung**: Die verwendete Abonnement-ID *IhreAbonnementID* verfügt nicht über ausreichende Berechtigungen, um den Löschvorgang für den Cluster *IhrClustername* auszuführen.  
- **Lösung**: Wenn der Cluster im Fehlerzustand ist, verwerfen Sie ihn, und versuchen Sie es erneut.  

<h3><a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName</h3>
- **Beschreibung**: Der Blob-Containername *IhrContainername* des externen Speicherkontos ist ungültig. Stellen Sie sicher, dass der Name mit einem Buchstaben beginnt und nur Kleinbuchstaben, Ziffern und Bindestriche enthält.  
- **Lösung**: Geben Sie einen gültigen Blob-Containernamen für das Speicherkonto ein, und wiederholen Sie den Vorgang.

<h3><a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey</h3>
- **Beschreibung**: Die Konfiguration für das externe Speicherkonto *NameIhresSpeicherkontos* ist erforderlich, damit Details zum geheimen Schlüssel festgelegt werden können.  
- **Lösung**: Geben Sie einen gültigen geheimen Schlüssel für das Speicherkonto ein, und wiederholen Sie den Vorgang.

<h3><a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat</h3>
- **Beschreibung**: Der Versionsheader *IhrVersionsheader* hat nicht das gültige Format JJJJ-MM-TT.  
- **Lösung**: Geben Sie den Versionsheader im gültigen Format ein, und wiederholen Sie die Anforderung. 

<h3><a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode</h3>
- **Beschreibung**: Ungültige Clusterkonfiguration. Es wurde mehr als eine Hauptknotenkonfiguration gefunden.  
- **Lösung**: Bearbeiten Sie die Konfiguration so, dass nur ein Hauptknoten angegeben ist. 

<h3><a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest</h3>
- **Beschreibung**: Der Vorgang konnte nicht innerhalb der erlaubten Zeit oder der maximalen Anzahl von Versuchen abgeschlossen werden. Wiederholen Sie die Anforderung.  
- **Lösung**: Wiederholen Sie die Anforderung. 

<h3><a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty</h3>
- **Beschreibung**: Der Parameter *IhrParametername* darf nicht null oder leer sein.  
- **Lösung**: Geben Sie einen gültigen Wert für den Parameter an. 

<h3><a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure</h3>
- **Beschreibung**: Mindestens eine der Eingaben für die Anforderung zur Clustererstellung ist ungültig. Stellen Sie sicher, dass die Eingabewerte korrekt sind, und wiederholen Sie die Anforderung.  
- **Lösung**: Stellen Sie sicher, dass die Eingabewerte korrekt sind, und wiederholen Sie die Anforderung. 

<h3><a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable</h3>
- **Beschreibung**: Die Regionsfunktion ist für Region *NameIhrerRegion* und Abonnement-ID *IhreAbonnementID* nicht verfügbar.  
- **Lösung**: Geben Sie eine Region an, die HDInsight-Cluster unterstützt. Folgende Regionen werden öffentlich unterstützt: Südostasien, Westeuropa, Nordeuropa, USA Ost oder USA West. 

<h3><a id="StorageAccountNotColocated"></a>StorageAccountNotColocated</h3>
- **Beschreibung**: Das Speicherkonto *NameIhresSpeicherkontos* befindet sich in der Region *aktuelleRegionName*. Sie sollte identisch mit der Clusterregion *IhrClusterRegionName* sein.  
- **Lösung**: Geben Sie entweder ein Speicherkonto in der gleichen Region an, in dem sich Ihr Cluster befindet, oder, wenn sich Ihre Daten bereits im Speicherkonto befinden, erstellen Sie einen neuen Cluster in der gleichen Region wie das vorhandene Speicherkonto. Wenn Sie das Portal verwenden, wird der Benutzer im Vorhinein über die Benutzeroberfläche über dieses Problem informiert. 

<h3><a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive</h3>
- **Beschreibung**: Die angegebene Abonnement-ID *IhreAbonnementID* ist nicht aktiv.  
- **Lösung**: Reaktivieren Sie Ihr Abonnement, oder erwerben Sie ein gültiges Abonnement.

<h3><a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound</h3>
- **Beschreibung**: Die Abonnement-ID *IhreAbonnementID* wurde nicht gefunden.  
- **Lösung**: Vergewissern Sie sich, dass Ihre Abonnement-ID gültig ist, und wiederholen Sie den Vorgang. 

<h3><a id="UnableToResolveDNS"></a>UnableToResolveDNS</h3>
- **Beschreibung**: DNS *IhreDnsURL* konnte nicht aufgelöst werden. Stellen Sie sicher, dass die vollqualifizierte URL für den Blob-Endpunkt bereitgestellt wird.  
- **Lösung**: Geben Sie eine gültige Blob-URL an. Die URL MUSS uneingeschränkt gültig sein, das heißt, sie muss mit *http://* beginnen und mit *.com* enden. Die vollqualifizierte URL befindet sich in der Regel auf der Speicher-Registerkarte des Portals unter manage.windowsazure.com.  

<h3><a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource</h3>
- **Beschreibung**: Der Speicherort für die Ressource *IhreDnsURL* konnte nicht überprüft werden. Stellen Sie sicher, dass die vollqualifizierte URL für den Blob-Endpunkt bereitgestellt wird.  
- **Lösung**: Geben Sie eine gültige Blob-URL an. Die URL MUSS uneingeschränkt gültig sein, das heißt, sie muss mit *http://* beginnen und mit *.com* enden. Die vollqualifizierte URL befindet sich in der Regel auf der Speicher-Registerkarte des Portals unter manage.windowsazure.com. 

<h3><a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable</h3>
- **Beschreibung**: Die Versionsfunktion ist für Version *angegebeneVersion* und Abonnement-ID *IhreAbonnementID* nicht verfügbar.  
- **Lösung**: Wählen Sie eine Version aus, die verfügbar ist, und wiederholen Sie den Vorgang. 

<h3><a id="VersionNotSupported"></a>VersionNotSupported</h3>
- **Beschreibung**: Die Version *angegebeneVersion* wird nicht unterstützt.   
- **Lösung**: Wählen Sie eine Version aus, die unterstützt wird, und wiederholen Sie den Vorgang.

<h3><a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion</h3>
- **Beschreibung**: Die Version *angegebeneVersion* ist in der Azure-Region *angegebeneRegion* nicht verfügbar.  
- **Lösung**: Wählen Sie eine Version aus, die in der angegebenen Region unterstützt wird, und wiederholen Sie den Vorgang. 

<h3><a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound</h3>
- **Beschreibung**: Ungültige Clusterkonfiguration. Die erforderliche WASB-Kontokonfiguration wurde in externen Konten nicht gefunden.  
- **Lösung**: Vergewissern Sie sich, dass das Konto existiert und in der Konfiguration korrekt angegeben ist, und wiederholen Sie den Vorgang. 

<h2><a id="resources"></a>Zusätzliche Debuggingressourcen</h2> 

* [Dokumentation zum Azure HDInsight SDK][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/de-de/library/dn479185.aspx

[image-hdi-debugging-error-messages-portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png






"<!--HONumber=35.1-->" 
