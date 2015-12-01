<properties
	pageTitle="Erstellen eines virtuellen Windows-Computers mit Überwachung und Diagnose mithilfe von Azure-Ressourcen-Manager-Vorlagen | Microsoft Azure"
	description="Verwenden Sie eine Azure-Ressourcen-Manager-Vorlage, um einen neuen virtuellen Windows-Computer mit Azure-Diagnoseerweiterung zu erstellen."
	services="virtual-machines"
	documentationCenter=""
	authors="sbtron"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/13/2015"
	ms.author="saurabh"/>

# Erstellen eines virtuellen Windows-Computers mit Überwachung und Diagnose mithilfe von Azure-Ressourcen-Manager-Vorlagen

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt die Verwendung des Ressourcen-Manager-Bereitstellungsmodells.

Die Azure-Diagnoseerweiterung stellt Überwachungs- und Diagnosefunktionen auf einem Windows-basierten virtuellen Azure-Computer bereit. Sie können diese Funktionen auf dem virtuellen Computer nutzen, indem Sie die Erweiterung in die Azure-Ressourcen-Manager-Vorlage einbinden. Weitere Informationen zum Einbinden von Erweiterungen in eine Vorlage für virtuelle Computer finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen mit VM-Erweiterungen](virtual-machines-extensions-authoring-templates.md). Dieser Artikel beschreibt das Hinzufügen der Azure-Diagnoseerweiterung zu einer Vorlage für virtuelle Windows-Computer.
  

## Hinzufügen der Azure-Diagnoseerweiterung zur VM-Ressourcendefinition 

Um die Diagnoseerweiterung auf einem virtuellen Windows-Computer zu aktivieren, müssen Sie die Erweiterung als VM-Ressource in der Ressourcen-Manager-Vorlage hinzufügen.

Bei einem einfachen, auf Ressourcen-Manager basierenden virtuellen Computer fügen Sie die Erweiterungskonfiguration dem Array *resources* für den virtuellen Computer hinzu.

	"resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


Eine weitere gängige Konvention ist es, die Erweiterungskonfiguration im Stammressourcenknoten der Vorlage hinzuzufügen, statt sie unterhalb des Ressourcenknotens des virtuellen Computers zu definieren. Bei diesem Ansatz müssen Sie mithilfe der Werte *name* und *value* explizit eine hierarchische Beziehung zwischen der Erweiterung und dem virtuellen Computer angeben. Beispiel:
  
	"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

Die Erweiterung ist immer dem virtuellen Computer zugeordnet. Sie können diese direkt unterhalb des Ressourcenknotens des virtuellen Computers definieren. Alternativ dazu können Sie sie auch auf der Basisebene definieren und die hierarchische Benennungskonvention verwenden, um sie dem virtuellen Computer zuzuordnen.

Bei VM-Skalierungsgruppen wird die Erweiterungskonfiguration in der *extensionProfile*-Eigenschaft von *VirtualMachineProfile* angegeben.
   
Die *publisher*-Eigenschaft mit dem Wert **Microsoft.Azure.Diagnostics** und die *type*-Eigenschaft mit dem Wert **IaaSDiagnostics** definieren die Azure-Diagnoseerweiterung eindeutig.

Der Wert der *name*-Eigenschaft kann zum Verweisen auf die Erweiterung in der Ressourcengruppe verwendet werden. Wenn Sie diese Eigenschaft auf **Microsoft.Insights.VMDiagnosticsSettings** festlegen, kann sie problemlos im Azure-Portal identifiziert werden – so wird sichergestellt, dass die Überwachungsdiagramme im Azure-Portal richtig angezeigt werden.

Die Eigenschaft *typeHandlerVersion* gibt die Version der Erweiterung an, die Sie verwenden möchten. Indem Sie *autoUpgradeMinorVersion* auf **true** festlegen, stellen Sie sicher, dass Sie die neueste verfügbare Nebenversion der Erweiterung erhalten. Es wird dringend empfohlen, *autoUpgradeMinorVersion* immer auf **true** festzulegen, damit Sie jederzeit die neueste verfügbare Diagnoseerweiterung mit allen neuen Funktionen und Bugfixes verwenden.

Das Element *settings* enhält Konfigurationseigenschaften für die Erweiterung, die festgelegt und in die Erweiterung eingelesen werden können (wird zuweilen als „öffentliche Konfiguration“ bezeichnet). Die Eigenschaft *xmlcfg* enthält die XML-basierte Konfiguration für die Diagnoseprotokolle, Leistungsindikatoren usw., die vom Diagnose-Agent gesammelt werden. Weitere Informationen zu dem XML-Schema finden Sie unter [Diagnosekonfigurationsschema](https://msdn.microsoft.com/library/azure/dn782207.aspx). Üblicherweise wird die tatsächliche XML-Konfiguration als Variable in der Azure-Ressourcen-Manager-Vorlage gespeichert ///them???und dann verkettet und mit Base64 codiert, um den Wert für *xmlcfg* festzulegen. Informationen zum Speichern der XML-Konfiguration in Variablen finden Sie im Abschnitt zu [Diagnosekonfigurationsvariablen](#diagnostics-configuration-variables). Die *storageAccount*-Eigenschaft gibt den Namen des Speicherkontos an, an das die Diagnosedaten übertragen werden.
 
Die Eigenschaften in *protectedSettings* (zuweilen als „private Konfiguration“ bezeichnet) können festgelegt, danach jedoch nicht in die Erweiterung eingelesen werden. Da *protectedSettings* schreibgeschützt ist, eignet sich diese Eigenschaft sehr gut zum Speichern von geheimen Informationen, wie z. B. des Schlüssels für das Speicherkonto, in das die Diagnosedaten geschrieben werden.

## Angeben des Diagnosespeicherkontos als Parameter 

Der oben stehende JSON-Codeausschnitt für die Diagnoseerweiterung setzt das Vorhandensein der beiden Parameter *existingdiagnosticsStorageAccountName* und *existingdiagnosticsStorageAccountName* voraus, um das Diagnosespeicherkonto anzugeben, in dem Diagnosedaten gespeichert werden. Wenn Sie das Diagnosespeicherkonto als Parameter angeben, lässt es sich für verschiedene Umgebungen ganz einfach ändern – so können Sie z. B. ein Konto zum Testen und ein anderes für Ihre Produktionsbereitstellung verwenden.

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
			}        
		},
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
      		}
        }

Es empfiehlt sich, ein Diagnosespeicherkonto in einer anderen Ressourcengruppe als der Ressourcengruppe für den virtuellen Computer anzugeben. Eine Ressourcengruppe kann als Bereitstellungseinheit mit eigener Lebensdauer angesehen werden. Ein virtueller Computer kann bereitgestellt und bei Konfigurationsupdates erneut bereitgestellt werden. Möglicherweise möchten Sie die Diagnosedaten jedoch für alle VM-Bereitstellungen weiterhin im gleichen Speicherkonto speichern. Indem Sie das Speicherkonto in einer anderen Ressourcengruppe bereitstellen, können Daten aus verschiedenen VM-Bereitstellungen in diesem Konto gespeichert werden, und die Behandlung von Problemen über die verschiedenen Versionen hinweg wird vereinfacht.

>[AZURE.NOTE]Wenn Sie eine Vorlage für einen virtuellen Windows-Computer in Visual Studio erstellen, kann das standardmäßige Speicherkonto so eingerichtet werden, dass es das gleiche Speicherkonto verwendet, in das die VHD des virtuellen Computers hochgeladen wurde. Dadurch wird die anfängliche Einrichtung des virtuellen Computers vereinfacht. Sie sollten die Vorlage bearbeiten und ein anderes Speicherkonto angeben, das als Parameter übergeben werden kann.

## Diagnosekonfigurationsvariablen
 
Der oben stehende JSON-Codeausschnitt für die Diagnoseerweiterung definiert eine *accountid*-Variable, um das Abrufen des Speicherkontoschlüssels für den Diagnosespeicher zu vereinfachen:
	
	"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


Die *xmlcfg*-Eigenschaft für die Diagnoseerweiterung wird mithilfe von mehreren miteinander verketteten Variablen definiert. Die Werte dieser Variablen liegen im XML-Format vor und müssen daher beim Festlegen der JSON-Variablen ordnungsgemäß mit Escapezeichen versehen werden.

Nachfolgend sehen Sie den XML-Code für die Diagnosekonfiguration, der standardmäßige Leistungsindikatoren auf Systemebene sowie einige Windows-Ereignisprotokolle und Diagnoseinfrastrukturprotokolle erfasst. Der Code wurde mit Escapezeichen versehen und ordnungsgemäß formatiert, sodass die Konfiguration direkt in den Variablenabschnitt Ihrer Vorlage kopiert werden kann. Ein visuell besser lesbares Beispiel einer XML-Konfigurationsdatei finden Sie unter [Diagnosekonfigurationsschema](https://msdn.microsoft.com/library/azure/dn782207.aspx).
    
        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\% Processor Time" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU utilization" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\% Privileged Time" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU privileged time" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\% User Time" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU user time" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Processor Information(_Total)\\Processor Frequency" sampleRate="PT15S" unit="Count"><annotation displayName="CPU frequency" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\System\\Processes" sampleRate="PT15S" unit="Count"><annotation displayName="Processes" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Process(_Total)\\Thread Count" sampleRate="PT15S" unit="Count"><annotation displayName="Threads" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Process(_Total)\\Handle Count" sampleRate="PT15S" unit="Count"><annotation displayName="Handles" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Memory\\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent"><annotation displayName="Memory usage" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Memory\\Available Bytes" sampleRate="PT15S" unit="Bytes"><annotation displayName="Memory available" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Memory\\Committed Bytes" sampleRate="PT15S" unit="Bytes"><annotation displayName="Memory committed" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Memory\\Commit Limit" sampleRate="PT15S" unit="Bytes"><annotation displayName="Memory commit limit" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\% Disk Time" sampleRate="PT15S" unit="Percent"><annotation displayName="Disk active time" locale="de-DE"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\% Disk Read Time" sampleRate="PT15S" unit="Percent"><annotation displayName="Disk active read time" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\% Disk Write Time" sampleRate="PT15S" unit="Percent"><annotation displayName="Disk active write time" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond"><annotation displayName="Disk operations" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond"><annotation displayName="Disk read operations" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond"><annotation displayName="Disk write operations" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond"><annotation displayName="Disk speed" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond"><annotation displayName="Disk read speed" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond"><annotation displayName="Disk write speed" locale="de-DE"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\LogicalDisk(_Total)\\% Free Space" sampleRate="PT15S" unit="Percent"><annotation displayName="Disk free space (percentage)" locale="de-DE"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId="')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": ""><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

Der XML-Knoten für die Metrikdefinition in der oben stehenden Konfiguration ist ein wichtiges Konfigurationselement, da er festlegt, wie die weiter oben im XML-Code im Knoten *PerformanceCounter* definierten Leistungsindikatoren aggregiert und gespeichert werden. Mit diesen Metriken werden die Diagramme und Warnungen im Azure-Vorschauportal gesteuert – wenn Sie also die Überwachungsdaten im Portal anzeigen möchten, ist es wichtig, diese Metriken in die Konfiguration einzubinden.

Im Folgenden sehen Sie ein Beispiel des XML-Codes für die Metrikdefinition:

		<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
			<MetricAggregation scheduledTransferPeriod="PT1H"/>
			<MetricAggregation scheduledTransferPeriod="PT1M"/>
		</Metrics>

Das Attribut *resourceID* identifiziert den virtuellen Computer in Ihrem Abonnement eindeutig. Stellen Sie sicher, dass Sie die Funktionen „subscription()“ und „resourceGroup()“ verwenden, damit die Vorlage diese Werte automatisch basierend auf dem Abonnement und der Ressourcengruppe aktualisiert, in dem bzw. die Bereitstellung erfolgt.

Wenn Sie mehrere virtuelle Computer in einer Schleife erstellen, müssen Sie den *resourceID*-Wert mit einer copyIndex()-Funktion auffüllen, um die einzelnen virtuellen Computer voneinander zu unterscheiden. Der *xmlCfg*-Wert kann folgendermaßen aktualisiert werden, um diese Funktionalität zu unterstützen:

	"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 


Die MetricAggregation-Werte *PT1H* und *PT1M* weisen auf eine Aggregierung ///während einer Stunde und eine Aggregierung während einer Minute hin.

Die oben stehende Metrikkonfiguration generiert Tabellen mit den folgenden Benennungskonventionen in Ihrem Diagnosespeicherkonto:

- **WADMetrics**: Standardpräfix für alle WADMetrics-Tabellen.
- **PT1H** oder **PT1M**: Weist darauf hin, dass die Tabelle Daten enthält, die ///während einer Stunde bzw. einer Minute aggregiert wurden.
- **P10D**: Gibt an, dass die Tabelle Daten ///für einen Zeitraum von 10 Tagen ab dem Tag enthält, an dem die Tabelle mit dem Sammeln von Daten begonnen hat.
- **V2S**: Zeichenfolgenkonstante.
- **yyyymmdd**: Das Datum, an dem die Tabelle mit dem Sammeln von Daten begonnen hat.

Beispiel: *WADMetricsPT1HP10DV2S20151108* enthält Metrikdaten, die ab 11. November 2015 10 Tage lang gesammelt und ///während einer Stunde aggregiert wurden.

Jede WADMetrics-Tabelle enthält die folgenden Spalten:

- **PartitionKey**: Der Partitionsschlüssel wird basierend auf dem *resourceID*-Wert gebildet um die VM-Ressource eindeutig zu identifizieren. Beispiel: 002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>.  
- **RowKey**: Folgt dem Format <Descending time tick>:<Performance Counter Name>. Zur Berechnung der absteigenden Zeiteinheiten werden die maximalen Zeiteinheiten abzüglich der Anfangszeit des Aggregationszeitraums herangezogen. Beispiel: Wenn der Sammelzeitraum am 10. Nov. 2015 um 00:00 Uhr UTC begann, lautet die Berechnung: DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks). Für den Leistungsindikator für die im Arbeitsspeicher verfügbaren Bytes sieht der Zeilenschlüssel wie folgt aus: 2519551871999999999\_\_:005CMemory:005CAvailable:0020Bytes
- **CounterName**: Der Name des Leistungsindikators. Entspricht dem in der XML-Konfiguration definierten *counterSpecifier*-Wert.
- **Maximum**: Der Höchstwert des Leistungsindikators während des Aggregierungszeitraums.
- **Minimum**: Der Mindestwert des Leistungsindikators während des Aggregierungszeitraums.
- **Total**: Die Summe aller Werte des Leistungsindikators, die während des Aggregierungszeitraums gemeldet wurden.
- **Count**: Die Gesamtanzahl von Werten, die für den Leistungsindikator gemeldet wurden.
- **Average**: ///Der Durchschnitt der Werte „Total“/„Count“ des Leistungsindikators während des Aggregierungszeitraums.


## Nächste Schritte

- Eine vollständige Beispielvorlage für einen virtuellen Windows-Computer mit Diagnoseerweiterung finden Sie unter[201-vm-monitoring-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension).   
- Stellen Sie die Ressourcen-Manager-Vorlage über [Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell.md) oder die [Azure-Befehlszeile](virtual-machines-deploy-rmtemplates-powershell.md) bereit.
- Weitere Informationen zum [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).


<!---HONumber=Nov15_HO4-->


