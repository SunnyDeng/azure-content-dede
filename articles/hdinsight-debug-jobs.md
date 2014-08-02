<properties linkid="manage-services-hdinsight-debug-error-messages" urlDisplayName="Debug HDInsight Errors" pageTitle="Debug HDInsight: Error messages | Azure" metaKeywords="dinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="Learn about the error messages you might receive when administering HDInsight using PowerShell, and steps you can take to recover." services="hdinsight" title="Debug HDInsight: Error messages" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

Debuggen von HDInsight: Fehlermeldungen
=======================================

Einführung
----------

Die in diesem Thema beschriebenen Fehlermeldungen sollen den Benutzern von Azure HDInsight dabei helfen, mögliche Fehlerbedingungen zu verstehen, auf die sie möglicherweise stoßen, wenn sie den Dienst mit Azure PowerShell verwalten. Es werden auch Schritte zur Behebung des Problems erörtert.

Einige dieser Fehlermeldungen können auch im Azure-Portal angezeigt werden, wenn die HDInsight-Cluster darüber verwaltet werden. Andere Fehlermeldungen, auf die Sie möglicherweise stoßen, sind jedoch eventuell weniger differenziert, da in diesem Kontext gewisse Beschränkungen für die Lösungsschritte bestehen. Andere Fehlermeldungen erscheinen in einem Kontext, in dem die Lösung offensichtlich ist. Wenn z. B. gegen die Beschränkungen für die Parameter verstoßen wird, erscheint die Meldung rechts neben dem Feld, in das der Wert eingegeben wurde. Hier ein Fall, in dem zu viele Datenknoten angefordert wurden. Die Lösung besteht darin, die Anzahl auf einen zulässigen Wert von maximal 22 zu verringern.

![HDI.Debugging.ErrorMessages.Portal](./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png)

Die Fehler, auf die ein Benutzer in Azure PowerShell oder im Azure-Portal stoßen kann, sind unter [HDInsight-Fehler](#hdinsight-error-messages) alphabetisch nach Name aufgeführt. Sie sind mit dem entsprechenden Eintrag im Abschnitt mit der[Beschreibung und Lösung der Fehler](#discription-mitigation-errors) verknüpft, der die folgenden Informationen zu dem Fehler enthält:

-   **Beschreibung**: Fehlermeldung, die angezeigt wird
-   **Lösung**: Schritte, die zur Problembehebung unternommen werden können

### HDInsight-Fehler

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

Diagnose und Lösung der Fehler
------------------------------

### AtleastOneSqlMetastoreMustBeProvided

-   **Beschreibung**: Stellen Sie Azure SQL-Datenbankinformationen für mindestens eine Komponente bereit, damit benutzerdefinierte Einstellungen für die Hive- und Oozie-Metastores verwendet werden können.
-   **Lösung**: Der Benutzer muss einen gültigen SQL Azure-Metastore angeben. Dann kann er die Anforderung wiederholen.

### AzureRegionNotSupported

-   **Beschreibung**: Es konnte kein Cluster in der Region *NameIhrerRegion* erstellt werden. Verwenden Sie eine gültige HDInsight-Region, und wiederholen Sie die Anforderung.
-   **Lösung**: Der Kunde sollte die Clusterregion erstellen, die ihn derzeit unterstützt: Nordeuropa, USA (Osten) oder USA (Westen).

### ClusterContainerRecordNotFound

-   **Beschreibung**: Der Server konnte den angeforderten Clusterdatensatz nicht finden.
-   **Lösung**: Wiederholen Sie den Vorgang.

### ClusterDnsNameInvalidReservedWord

-   **Beschreibung**: Cluster-DNS-Name *IhrDnsName* ist ungültig. Stellen Sie sicher, dass der Name mit einem alphanumerischen Zeichen beginnt und endet. Er darf nur das Sonderzeichen '-' enthalten.
-   **Lösung**: Vergewissern Sie sich, dass Sie für Ihren Cluster einen gültigen DNS-Namen verwenden, der mit einem alphanumerischen Zeichen beginnt und endet und keine anderen Sonderzeichen als den Bindestrich '-' enthält. Wiederholen Sie dann den Vorgang.

### ClusterNameUnavailable

-   **Beschreibung**: Der Clustername *IhrClustername* ist ungültig. Wählen Sie einen anderen Namen.
-   **Lösung**: Der Benutzer sollte einen eindeutigen, noch nicht vorhandenen Clusternamen angeben und dann den Vorgang wiederholen. Wenn der Benutzer das Portal verwendet, wird er bei der Erstellung über die Benutzeroberfläche informiert, wenn ein Clustername bereits vorhanden ist.

### ClusterPasswordInvalid

-   **Beschreibung**: Das Clusterkennwort ist ungültig. Das Kennwort muss mindestens 10 Zeichen lang sein und mindestens eine Zahl, einen Groß- und einen Kleinbuchstaben sowie ein Sonderzeichen enthalten. Es darf keine Leerzeichen und nicht den Benutzernamen enthalten.
-   **Lösung**: Geben Sie ein gültiges Clusterkennwort ein, und wiederholen Sie den Vorgang.

### ClusterUserNameInvalid

-   **Beschreibung**: Der Clusterbenutzername ist ungültig. Stellen Sie sicher, dass der Benutzername keine Sonderzeichen oder Leerzeichen enthält.
-   **Lösung**: Geben Sie einen gültigen Clusterbenutzernamen ein, und wiederholen Sie den Vorgang.

### ClusterUserNameInvalidReservedWord

-   **Beschreibung**: Der Cluster-DNS-Name *IhrDnsClustername* ist ungültig. Stellen Sie sicher, dass der Name mit einem alphanumerischen Zeichen beginnt und endet. Er darf nur das Sonderzeichen '-' enthalten.
-   **Lösung**: Geben Sie einen gültigen DNS-Clusterbenutzernamen ein, und wiederholen Sie den Vorgang.

### ContainerNameMisMatchWithDnsName

-   **Beschreibung**: Der Containername in URI *IhrContainerURI* und der DNS-Name *IhrDnsName* im Anforderungstext müssen identisch sein.
-   **Lösung**: Stellen Sie sicher, dass Containername und DNS-Name identisch sind, und wiederholen Sie den Vorgang.

### DataNodeDefinitionNotFound

-   **Beschreibung**: Ungültige Clusterkonfiguration. Es können keine Datenknotendefinitionen in der Knotengröße gefunden werden.
-   **Lösung**: Wiederholen Sie den Vorgang.

### DeploymentDeletionFailure

-   **Beschreibung**: Das Löschen der Bereitstellung ist für den Cluster fehlgeschlagen.
-   **Lösung**: Führen Sie den Löschvorgang erneut aus.

### DnsMappingNotFound

-   **Beschreibung**: Fehler bei der Dienstkonfiguration. Die erforderlichen DNS-Zuordnungsinformationen wurden nicht gefunden.
-   **Lösung**: Löschen Sie den Cluster, und erstellen Sie einen neuen Cluster.

### DuplicateClusterContainerRequest

-   **Beschreibung**: Es wurde zweimal versucht, den Clustercontainer zu erstellen. Für *NameIhresContainers* existiert ein Datensatz, die ETags stimmen aber nicht überein.
-   **Lösung**: Geben Sie für den Container einen eindeutigen Namen an, und wiederholen Sie den Erstellvorgang.

### DuplicateClusterInHostedService

-   **Beschreibung**: Der gehostete Dienst *NameIhresgehostetenDienstes* enthält bereits einen Cluster. Ein gehosteter Dienst kann nicht mehrere Cluster enthalten.
-   **Lösung**: Hosten Sie den Cluster in einem anderen gehosteten Dienst.

### FailureToUpdateDeploymentStatus

-   **Beschreibung**: Der Server konnte den Status der Clusterbereitstellung nicht aktualisieren.
-   **Lösung**: Wiederholen Sie den Vorgang. Falls dies mehrfach passiert, wenden Sie sich an den CSS.

### HdiRestoreClusterAltered

-   **Beschreibung**: Der Cluster *IhrClustername* wurde im Rahmen von Wartungsarbeiten gelöscht. Erstellen Sie den Cluster erneut.
-   **Lösung**: Erstellen Sie den Cluster erneut.

### HeadNodeConfigNotFound

-   **Beschreibung**: Ungültige Clusterkonfiguration. Die erforderliche Hauptknotenkonfiguration wurde in den Knotengrößen nicht gefunden.
-   **Lösung**: Wiederholen Sie den Vorgang.

### HostedServiceCreationFailure

-   **Beschreibung**: Der gehostete Dienst *NameIhresgehostetenDiensts* konnte nicht erstellt werden. Wiederholen Sie die Anforderung.
-   **Lösung**: Wiederholen Sie die Anforderung.

### HostedServiceHasProductionDeployment

-   **Beschreibung**: Der gehostete Dienst *NameIhresgehostetenDiensts* enthält bereits eine Produktionsbereitstellung. Ein gehosteter Dienst kann nicht mehrere Produktionsbereitstellungen enthalten. Wiederholen Sie die Anforderung mit einem anderen Clusternamen.
-   **Lösung**: Verwenden Sie einen anderen Clusternamen, und wiederholen Sie die Anforderung.

### HostedServiceNotFound

-   **Beschreibung**: Der gehostete Dienst *NameIhresgehostetenDiensts* konnte für den Cluster nicht gefunden werden.
-   **Lösung**: Wenn der Cluster im Fehlerzustand ist, löschen Sie ihn, und versuchen Sie es erneut.

### HostedServiceWithNoDeployment

-   **Beschreibung**: Dem gehosteten Dienst *NameIhresgehostetenDiensts* wurde keine Bereitstellung zugeordnet.
-   **Lösung**: Wenn der Cluster im Fehlerzustand ist, löschen Sie ihn, und versuchen Sie es erneut.

### InsufficientResourcesCores

-   **Beschreibung**: Die Abonnement-ID *IhreAbonnementID* hat keine Kerne mehr übrig, um den Cluster *IhrClustername* zu erstellen. Erforderlich: *erforderlicheRessourcen*, verfügbar: *verfügbareRessourcen*.
-   **Lösung**: Geben Sie Ressourcen in Ihrem Abonnement frei, oder erhöhen Sie die für das Abonnement verfügbaren Ressourcen, und versuchen Sie erneut, den Cluster zu erstellen.

### InsufficientResourcesHostedServices

-   **Beschreibung**: Die Abonnement-ID *IhreAbonnementID* verfügt für einen neuen gehosteten Dienst über kein Kontingent, um den Cluster *IhrClustername* zu erstellen.
-   **Lösung**: Geben Sie Ressourcen in Ihrem Abonnement frei, oder erhöhen Sie die für das Abonnement verfügbaren Ressourcen, und versuchen Sie erneut, den Cluster zu erstellen.

### InternalErrorRetryRequest

-   **Beschreibung**: Auf dem Server ist ein interner Fehler aufgetreten. Wiederholen Sie die Anforderung.
-   **Lösung**: Wiederholen Sie die Anforderung.

### InvalidAzureStorageLocation

-   **Beschreibung**: Der Ort für den Azure-Speicher *DatenRegionName* ist kein gültiger Speicherort. Stellen Sie sicher, dass die Region korrekt ist, und wiederholen Sie die Anforderung.
-   **Lösung**: Wählen Sie einen Speicherort aus, der HDInsight unterstützt. Vergewissern Sie sich, dass es sich um einen zusammengestellten Cluster handelt, und wiederholen Sie den Vorgang.

### InvalidNodeSizeForDataNode

-   **Beschreibung**: Ungültige VM-Größe für Datenknoten. Nur die Größe "Large VM" wird für alle Datenknoten unterstützt.
-   **Lösung**: Geben Sie die unterstützte Knotengröße für den Datenknoten an, und wiederholen Sie den Vorgang.

### InvalidNodeSizeForHeadNode

-   **Beschreibung**: Ungültige VM-Größe für Hauptknoten. Nur die Größe "ExtraLarge VM" wird für Hauptknoten unterstützt.
-   **Lösung**: Geben Sie die unterstützte Knotengröße für den Hauptknoten an, und wiederholen Sie den Vorgang.

### InvalidRightsForDeploymentDeletion

-   **Beschreibung**: Die verwendete Abonnement-ID *IhreAbonnementID* verfügt nicht über ausreichende Berechtigungen, um den Löschvorgang für den Cluster *IhrClustername* auszuführen.
-   **Lösung**: Wenn der Cluster im Fehlerzustand ist, verwerfen Sie ihn, und versuchen Sie es erneut.

### InvalidStorageAccountBlobContainerName

-   **Beschreibung**: Der Blob-Containername *IhrContainername* des externen Speicherkontos ist ungültig. Stellen Sie sicher, dass der Name mit einem Buchstaben beginnt und nur Kleinbuchstaben, Zahlen und Bindestriche enthält.
-   **Lösung**: Geben Sie einen gültigen Blob-Containernamen für das Speicherkonto ein, und wiederholen Sie den Vorgang.

### InvalidStorageAccountConfigurationSecretKey

-   **Beschreibung**: Die Konfiguration für das externe Speicherkonto *NameIhresSpeicherkontos* ist erforderlich, damit Details zum geheimen Schlüssel festgelegt werden können.
-   **Lösung**: Geben Sie einen gültigen geheimen Schlüssel für das Speicherkonto ein, und wiederholen Sie den Vorgang.

### InvalidVersionHeaderFormat

-   **Beschreibung**: Der Versionsheader *IhrVersionsheader* hat nicht das gültige Format JJJJ-MM-TT.
-   **Lösung**: Geben Sie den Versionsheader im gültigen Format ein, und wiederholen Sie die Anforderung.

### MoreThanOneHeadNode

-   **Beschreibung**: Ungültige Clusterkonfiguration. Es wurde mehr als eine Hauptknotenkonfiguration gefunden.
-   **Lösung**: Bearbeiten Sie die Konfiguration so, dass nur ein Hauptknoten angegeben ist.

### OperationTimedOutRetryRequest

-   **Beschreibung**: Der Vorgang konnte nicht innerhalb der erlaubten Zeit oder der maximalen Anzahl von Versuchen abgeschlossen werden. Wiederholen Sie die Anforderung.
-   **Lösung**: Wiederholen Sie die Anforderung.

### ParameterNullOrEmpty

-   **Beschreibung**: Der Parameter *IhrParametername* darf nicht null oder leer sein.
-   **Lösung**: Geben Sie einen gültigen Wert für den Parameter an.

### PreClusterCreationValidationFailure

-   **Beschreibung**: Mindestens eine der Eingaben für die Anforderung zur Clustererstellung ist ungültig. Stellen Sie sicher, dass die Eingabewerte korrekt sind, und wiederholen Sie die Anforderung.
-   **Lösung**: Stellen Sie sicher, dass die Eingabewerte korrekt sind, und wiederholen Sie die Anforderung.

### RegionCapabilityNotAvailable

-   **Beschreibung**: Die Regionsfunktion für Region *NameIhrerRegion* und Abonnement-ID *IhreAbonnementID* ist nicht verfügbar.
-   **Lösung**: Geben Sie eine Region an, die HDInsight-Cluster unterstützt. Folgende Regionen werden öffentlich unterstützt: USA (Osten), USA (Westen), Nordeuropa

### StorageAccountNotColocated

-   **Beschreibung**: Das Speicherkonto *NameIhresSpeicherkontos* befindet sich in Region *aktuelleRegionName*. Sie sollte identisch mit Clusterregion *IhrClusterRegionName* sein.
-   **Lösung**: Geben Sie entweder ein Speicherkonto in der gleichen Region an, in dem sich Ihr Cluster befindet, oder, wenn sich Ihre Daten bereits im Speicherkonto befinden, erstellen Sie einen neuen Cluster in der gleichen Region wie das vorhandene Speicherkonto. Wenn Sie das Portal verwenden, wird der Benutzer im Vorhinein über die Benutzeroberfläche über dieses Problem informiert.

### SubscriptionIdNotActive

-   **Beschreibung**: Die angegebene Abonnement-ID *IhreAbonnementID* ist nicht aktiv.
-   **Lösung**: Reaktivieren Sie Ihr Abonnement, oder erwerben Sie ein gültiges Abonnement.

### SubscriptionIdNotFound

-   **Beschreibung**: Die Abonnement-ID *IhreAbonnementID* wurde nicht gefunden.
-   **Lösung**: Vergewissern Sie sich, dass Ihre Abonnement-ID gültig ist, und wiederholen Sie den Vorgang.

### UnableToResolveDNS

-   **Beschreibung**: DNS *IhrDnsURL* konnte nicht aufgelöst werden. Stellen Sie sicher, dass die vollqualifizierte URL für den Blob-Endpunkt bereitgestellt wird.
-   **Lösung**: Geben Sie eine gültige Blob-URL an. Die URL MUSS uneingeschränkt gültig sein, das heißt, sie muss mit *http://* beginnen und mit *.com* enden. Die vollqualifizierte URL befindet sich in der Regel auf der Speicher-Registerkarte des Portals unter manage.windowsazure.com.

### UnableToVerifyLocationOfResource

-   **Beschreibung**: Der Speicherort für die Ressource *IhrDnsURL* konnte nicht überprüft werden. Stellen Sie sicher, dass die vollqualifizierte URL für den Blob-Endpunkt bereitgestellt wird.
-   **Lösung**: Geben Sie eine gültige Blob-URL an. Die URL MUSS uneingeschränkt gültig sein, das heißt, sie muss mit *http://* beginnen und mit *.com* enden. Die vollqualifizierte URL befindet sich in der Regel auf der Speicher-Registerkarte des Portals unter manage.windowsazure.com.

### VersionCapabilityNotAvailable

-   **Beschreibung**: Die Versionsfunktion für Version *angegebeneVersion* und Abonnement-ID *IhreAbonnementID* ist nicht verfügbar.
-   **Lösung**: Wählen Sie eine Version aus, die verfügbar ist, und wiederholen Sie den Vorgang.

### VersionNotSupported

-   **Beschreibung**: Die Version *angegebeneVersion* wird nicht unterstützt.
-   **Lösung**: Wählen Sie eine Version aus, die unterstützt wird, und wiederholen Sie den Vorgang.

### VersionNotSupportedInRegion

-   **Beschreibung**: Version *angegebeneVersion* ist in der Azure-Region *angegebeneRegion* nicht verfügbar.
-   **Lösung**: Wählen Sie eine Version aus, die in der angegebenen Region unterstützt wird, und wiederholen Sie den Vorgang.

### WasbAccountConfigNotFound

-   **Beschreibung**: Ungültige Clusterkonfiguration. Die erforderliche WASB-Kontokonfiguration wurde in externen Konten nicht gefunden.
-   **Lösung**: Vergewissern Sie sich, dass das Konto existiert und in der Konfiguration korrekt angegeben ist, und wiederholen Sie den Vorgang.

Zusätzliche Debuggingressourcen
-------------------------------

-   [Dokumentation des Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

