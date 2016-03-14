<properties
   pageTitle="Ausführen eines virtuellen Computers (Windows) | Blaupause | Microsoft Azure"
   description="Erfahren Sie, wie Sie eine einzelne VM unter Azure ausführen und die Skalierbarkeit, Resilienz, Verwaltbarkeit und Sicherheit im Blick behalten."
   services=""
   documentationCenter="na"
   authors="mikewasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="mikewasson"/>

# Ausführen einer einzelnen Windows-VM unter Azure

In diesem Artikel werden verschiedene bewährte Methoden zur Ausführung einer einzelnen Windows-VM unter Azure beschrieben. Dabei geht es vor allem auch um Skalierbarkeit, Resilienz, Verwaltbarkeit und Sicherheit.

> [AZURE.WARNING] Für einzelne VMs unter Azure gibt es kein SLA zur Betriebszeit. Verwenden Sie diese Konfiguration für Entwicklungs- und Testzwecke, aber nicht für eine Bereitstellung in der Produktion.

Azure verfügt über zwei verschiedene Bereitstellungsmodelle: [Resource Manager][resource-manager-overview] und klassisch. In diesem Artikel wird Resource Manager verwendet, der von Microsoft für neue Bereitstellungen empfohlen wird. Es gibt mehrere Möglichkeiten zur Verwendung von Resource Manager, z. B. das [Azure-Portal][azure-portal], [Azure PowerShell][azure-powershell], [Azure-CLI][azure-cli]-Befehle oder [Resource Manager-Vorlagen][arm-templates]. Dieser Artikel enthält ein Beispiel, für das die Azure-CLI (Azure-Befehlszeilenschnittstelle) verwendet wird.

![IaaS: einzelne VM](media/guidance-compute-single-vm.png)

Die Bereitstellung einer einzelnen VM unter Azure umfasst mehr „bewegliche Teile“ als die eigentliche Kern-VM. Es sind Compute-, Netzwerk- und Speicherelemente vorhanden.

- **Ressourcengruppe:** Erstellen Sie eine [Ressourcengruppe][resource-manager-overview] für die Ressourcen dieser VM. Eine _Ressourcengruppe_ ist ein Container, der verwandte Ressourcen enthält.

- **VM:** Sie können eine VM über eine Liste mit veröffentlichten Images oder über eine VHD-Datei bereitstellen, die Sie in Azure Blob Storage hochladen.

- **Betriebssystem-Datenträger:** Der Betriebssystem-Datenträger ist eine virtuelle Festplatte (VHD), die in [Azure-Speicher][azure-storage] gespeichert ist. Dies bedeutet, dass er auch dann noch vorhanden ist, wenn der Hostcomputer ausfällt.

- **Temporärer Datenträger:** Die VM wird mit einem temporären Datenträger erstellt (Laufwerk `D:` unter Windows). Dieser Datenträger wird auf dem Hostcomputer auf einem physischen Laufwerk gespeichert. Er wird _nicht_ im Azure-Speicher gespeichert und kann bei Neustarts und anderen Ereignissen während des VM-Lebenszyklus verloren gehen. Verwenden Sie diesen Datenträger nur für temporäre Daten, z. B. Auslagerungsdateien.

- **Datenträger:** Bei einem [Datenträger][data-disk] für Daten handelt es sich um eine permanente VHD, die für Anwendungsdaten verwendet wird. Datenträger werden im Azure-Speicher gespeichert, z. B. auf dem Betriebssystem-Datenträger.

- **Virtuelles Netzwerk (VNet) und Subnetz\_** Jede VM in Azure wird in einem virtuellen Netzwerk (VNet) bereitgestellt, das weiter in Subnetze unterteilt wird.

- **Öffentliche IP-Adresse:** Eine öffentliche IP-Adresse wird für die Kommunikation mit der VM benötigt, z. B. per Remotedesktop (RDP).

- **Netzwerksicherheitsgruppe (NSG):** Die [NSG][nsg] wird verwendet, um den Netzwerk-Datenverkehr zur VM zuzulassen oder zu verweigern. Mit den standardmäßigen NSG-Regeln wird der gesamte eingehende Datenverkehr aus dem Internet unterbunden.

- **Netzwerkschnittstellenkarte (NIC):** Die NIC ermöglicht der VM die Kommunikation mit dem virtuellen Netzwerk.

- **Diagnose:** Diagnoseprotokolle sind für die Verwaltung und Problembehandlung des virtuellen Computers von entscheidender Bedeutung.

## Empfehlungen für virtuelle Computer

- Wählen Sie beim Verschieben einer vorhandenen Workload nach Azure die [VM-Größe][virtual-machine-sizes], die Ihren lokalen Servern am ehesten entspricht. Wir empfehlen Ihnen die DS- und GS-Serie, bei denen Storage Premium für Workloads mit hoher E/A-Intensität verwendet werden kann.

    - Wenn für Ihre Workload kein Datenträgerzugriff mit hoher Leistung und niedriger Latenz erforderlich ist, können Sie die Verwendung der anderen VM-Größen des Standard-Tarifs in Erwägung ziehen, z. B. die A-Serie oder D-Serie.

- Wenn Sie die VM und anderen Ressourcen bereitstellen, müssen Sie einen Standort angeben. Es ist im Allgemeinen ratsam, einen Standort zu wählen, der sich in der Nähe Ihrer internen Benutzer oder Ihrer Kunden befindet. Nicht alle VM-SKUs sind unter Umständen aber an allen Standorten verfügbar. Weitere Informationen finden Sie unter [Dienste nach Region][services-by-region].

- Informationen zur Auswahl eines veröffentlichten VM-Image finden Sie unter [Navigieren zwischen und Auswählen von Images für virtuelle Azure-Computer][select-vm-image].

## Empfehlungen für Datenträger und Speicher

Wir empfehlen die Verwendung von [Storage Premium][premium-storage], um die bestmögliche E/A-Datenträgerleistung zu erzielen. Beachten Sie aber, dass für Storage Premium VMs der DS- oder GS-Serie erforderlich sind.

- Für Storage Premium basieren die Kosten auf der Größe des bereitgestellten Datenträgers. IOPS und Durchsatz (also die Datenübertragungsrate) richten sich ebenfalls nach der Datenträgergröße. Berücksichtigen Sie beim Bereitstellen eines Datenträgers also alle drei Faktoren (Kapazität, IOPS und Durchsatz).

- Für Standardspeicher basieren die Kosten auf der Menge der Daten, die auf Datenträger geschrieben werden. Daher ist es eine bewährte Vorgehensweise, die maximale Größe (1.023 GB) bereitzustellen. Achten Sie aber darauf, beim Formatieren der Datenträger die Schnellformatierung zu verwenden. Bei einer vollständigen Datenträgerformatierung werden Nullen auf den Datenträger geschrieben, sodass wirklich Speicherplatz belegt wird. Weitere Informationen finden Sie unter [Preise für Azure Storage][storage-price].

- Wenn Sie den Standardspeicher wählen, empfehlen wir Ihnen den georedundanten Speicher (GRS). Diese Art von Speicher ist auch dann weiter verfügbar, wenn es zu einem vollständigen regionalen Ausfall kommt oder ein Katastrophenfall auftritt, bei dem der Standort in der primären Region nicht mehr wiederherstellbar ist.

- Fügen Sie einen oder mehrere Datenträger hinzu. Wenn Sie eine neue virtuelle Festplatte (VHD) erstellen, ist sie nicht formatiert. Melden Sie sich an der VM an, um den Datenträger zu formatieren.

- Wenn Sie über eine große Zahl von Datenträgern verfügen, sollten Sie sich über die E/A-Grenzwerte des Speicherkontos bewusst sein. Weitere Informationen finden Sie unter [Einschränkungen für Datenträger virtueller Computer][vm-disk-limits].

- Erstellen Sie ein separates Speicherkonto zum Speichern der Diagnoseprotokolle, um eine optimale Leistung zu erzielen. Ein standardmäßiger lokal redundanter Speicher (LRS) reicht für Diagnoseprotokolle aus.

## Netzwerkempfehlungen

- Erstellen Sie für eine einzelne VM ein VNet mit einem Subnetz. Erstellen Sie außerdem eine NSG und öffentliche IP-Adresse.

- Die öffentliche IP-Adresse kann dynamisch oder statisch sein. Die Standardeinstellung ist „Dynamisch“.

    - Reservieren Sie eine [statische IP-Adresse][static-ip], falls Sie eine feste IP-Adresse benötigen, die sich nicht ändert. Es kann beispielsweise sein, dass einen A-Eintrag in DNS erstellen oder die IP-Adresse auf eine Positivliste setzen müssen.

    - Standardmäßig verfügt die IP-Adresse nicht über einen vollständig qualifizierten Domänennamen (FQDN). Weitere Informationen finden Sie unter [Erstellen eines vollständig qualifizierten Domänennamens im Azure-Portal][fqdn].

- Ordnen Sie eine NIC zu, und weisen Sie sie der IP-Adresse, dem Subnetz und der NSG zu.

- Die NSG-Standardregeln lassen RDP nicht zu. Fügen Sie zum Aktivieren von RDP der NSG eine Regel hinzu, die den eingehenden Datenverkehr zu TCP-Port 3389 zulässt.

## Skalierbarkeit

Sie können eine VM zentral hoch- oder herunterskalieren, indem Sie die VM-Größe ändern. Mit dem folgenden Azure-CLI-Befehl wird die Größe einer VM geändert:

```text
azure vm set -g <<resource-group>> --vm-size <<new-vm-size>
    --boot-diagnostics-storage-uri <<storage-account-uri>> <<vm-name>>
```

Durch das Ändern der VM-Größe wird ein Neustart des Systems ausgelöst, und nach dem Neustart werden Ihr vorhandenes Betriebssystem und Ihre Datenträger neu zugeordnet. Alle Daten auf dem temporären Datenträger gehen verloren. Mit der Option `--boot-diagnostics-storage-uri` kann die [Startdiagnose][boot-diagnostics] alle Fehler protokollieren, die mit dem Startvorgang verbunden sind.

Es ist unter Umständen nicht möglich, die Skalierung von einer SKU-Familie zu einer anderen durchzuführen (z. B. von der A-Serie zur G-Serie). Verwenden Sie den folgenden CLI-Befehl, um eine Liste mit den verfügbaren Größen für eine vorhandene VM zu erhalten:

```text
azure vm sizes -g <<resource-group>> --vm-name <<vm-name>>
```

Zum Skalieren auf eine Größe, die nicht aufgeführt ist, müssen Sie die VM-Instanz löschen und eine neue erstellen. Beim Löschen einer VM werden die VHDs nicht gelöscht.

## Verfügbarkeit

- Für einzelne VMs gibt es kein SLA zur Verfügbarkeit. Um ein SLA zur Verfügbarkeit zu erhalten, müssen Sie mehrere VMs in einer Verfügbarkeitsgruppe bereitstellen.

- Für Ihre VM können sich Auswirkungen aufgrund von [geplanter Wartung][planned-maintenance] oder [ungeplanter Wartung][manage-vm-availability] ergeben. Sie können [VM-Neustartprotokolle][reboot-logs] verwenden, um zu ermitteln, ob ein VM-Neustart durch einen geplanten Wartungsvorgang verursacht wurde.

- Vermeiden Sie es, eine einzelne VM in eine Verfügbarkeitsgruppe einzufügen. Durch das Einfügen der VM in eine Verfügbarkeitsgruppe wird Azure angewiesen, die VM als Teil einer Gruppe mit mehreren Instanzen zu behandeln. Sie erhalten dann keine erweiterte Warnung oder Benachrichtigung zu Neustarts bei geplanten Wartungsvorgängen.

- VHDs werden per [Azure Storage][azure-storage] gesichert, der repliziert wird, um die Dauerhaftigkeit und Verfügbarkeit sicherzustellen.

- Als Schutz vor versehentlichen Datenverlusten während des normalen Betriebs (z. B. aufgrund eines Benutzerfehlers) sollten Sie auch Point-in-Time-Sicherungen implementieren, indem Sie [Blob-Momentaufnahmen][blob-snapshot] oder ein anderes Tool verwenden.

## Verwaltbarkeit

- Führen Sie den folgenden CLI-Befehl aus, um die VM-Diagnose zu aktivieren:
    
    ```text
    azure vm enable-diag <<resource-group>> <<vm-name>>
    ```
    
    Dieser Befehl ermöglicht grundlegende Integritätsmetriken, Diagnoseinfrastruktur-Protokolle und Startdiagnose. Weitere Informationen finden Sie unter [Aktivieren von Überwachung und Diagnose][enable-monitoring].

- Verwenden Sie die Erweiterung [Azure-Protokollsammlung][log-collector], um Protokolle zu erfassen und in den Azure-Speicher hochzuladen.

- Unter Azure wird zwischen den Zuständen „Stopped“ (Beendet) und „De-allocated“ (Zuordnung aufgehoben) unterschieden. Wenn der VM-Status „Stopped“ lautet, fallen für Sie Kosten an. Wenn der VM-Status „De-allocated“ lautet, fallen für Sie keine Kosten an. (Weitere Informationen finden Sie unter [Azure VM – Häufig gestellte Fragen][vm-faq].)

    Verwenden Sie den folgenden CLI-Befehl, um die Zuordnung für eine VM aufzuheben:
    
    ```text
    azure vm deallocate <<resource-group>> <<vm-name>>
    ```
    
    Hinweis: Mit der Schaltfläche **Beenden** im Azure-Portal können Sie die Zuordnung für die VM auch aufheben. Wenn Sie das Herunterfahren aber unter Windows (per RDP) durchführen, wird die VM zwar beendet, aber die Zuordnung wird _nicht_ aufgehoben. Es fallen also weiter Kosten für Sie an.

- Wenn Sie eine VM löschen, werden die VHDs nicht gelöscht. Dies bedeutet, dass Sie die VM problemlos löschen können, ohne dass Daten verloren gehen. Allerdings wird Ihnen der Speicherplatz weiter in Rechnung gestellt. Um die VHD zu löschen, löschen Sie die Datei aus dem [Blob-Speicher][blog-storage].

- Laden Sie zum Ändern der Größe des Betriebssystem-Datenträgers die VHD-Datei herunter, und verwenden Sie ein Tool wie [Resize-VHD][Resize-VHD], um die Größe der VHD zu ändern. Laden Sie die VHD mit der geänderten Größe in den Blob-Speicher hoch, und löschen Sie dann die VM-Instanz. Stellen Sie eine neue Instanz bereit, für die die VHD mit der geänderten Größe verwendet wird.


## Sicherheit

- Verwenden Sie [Azure Security Center][security-center], um sich eine zentrale Übersicht über den Sicherheitszustand Ihrer Azure-Ressourcen zu verschaffen. Mit Security Center werden potenzielle Sicherheitsprobleme überwacht, z. B. Systemupdates, Antischadsoftware und Endpunkt-ACLs, und Sie erhalten eine umfassende Darstellung des Sicherheitszustands Ihrer Bereitstellung. **Hinweis:** Zum Zeitpunkt der Erstellung dieses Texts befindet sich Security Center noch in der Vorschauphase.

- Verwenden Sie die [rollenbasierte Access Control][rbac] (RBAC), um zu definieren, welche Mitglieder Ihres DevOps-Teams die von Ihnen bereitgestellten Azure-Ressourcen (VM, Netzwerk usw.) verwalten können.

- Erwägen Sie die Installation von [Sicherheitserweiterungen][security-extensions].

- Verwenden Sie [Azure Disk Encryption][disk-encryption], um das Betriebssystem und Datenträger zu verschlüsseln. **Hinweis:** Zum Zeitpunkt der Erstellung dieses Texts befindet sich Azure Disk Encryption noch in der Vorschauphase.

## Problembehandlung

- Führen Sie zum Zurücksetzen des lokalen Administratorkennworts den Azure-CLI-Befehl `vm reset-access` aus.
    
    ```text
    azure vm reset-access -u <<user>> -p <<new-password>> <<resource-group>> <<vm-name>>
    ```
    
- Falls sich Ihre VM in einem Zustand befindet, in dem sie nicht mehr gestartet werden kann, können Sie die [Startdiagnose][boot-diagnostics] nutzen, um Fehler beim Startvorgang zu diagnostizieren.

- Sehen Sie sich [Überwachungsprotokolle][audit-logs] an, um Bereitstellungsaktionen und andere VM-Ereignisse zu verfolgen.

## Azure-CLI-Befehle (Beispiel)

Mit dem folgenden Windows-Batchskript werden die [Azure-CLI][azure-cli]-Befehle ausgeführt, um eine einzelne VM-Instanz und die dazugehörigen Netzwerk- und Speicherressourcen bereitzustellen. Dies ist im vorherigen Diagramm dargestellt.

```bat
ECHO OFF
SETLOCAL

IF "%~1"=="" (
    ECHO Usage: %0 subscription-id
    EXIT /B
    )

:: Set up variables to build out the naming conventions for deploying
:: the cluster

SET LOCATION=eastus2
SET APP_NAME=app1
SET ENVIRONMENT=dev
SET USERNAME=testuser
SET PASSWORD=AweS0me@PW

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1

:: Set up the names of things using recommended conventions
SET RESOURCE_GROUP=%APP_NAME%-%ENVIRONMENT%-rg
SET VM_NAME=%APP_NAME%-vm0

SET IP_NAME=%APP_NAME%-pip
SET NIC_NAME=%VM_NAME%-0nic
SET NSG_NAME=%APP_NAME%-nsg
SET SUBNET_NAME=%APP_NAME%-subnet
SET VNET_NAME=%APP_NAME%-vnet
SET VHD_STORAGE=%VM_NAME:-=%st0
SET DIAGNOSTICS_STORAGE=%VM_NAME:-=%diag

:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see...
SET VM_SIZE=Standard_DS1

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --resource-group %RESOURCE_GROUP% ^
  --subscription %SUBSCRIPTION%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% %POSTFIX%

:: Create the NIC
CALL azure network nic create --network-security-group-name %NSG_NAME% ^
  --public-ip-name %IP_NAME% --subnet-name %SUBNET_NAME% --subnet-vnet-name ^
  %VNET_NAME%  --name %NIC_NAME% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS %POSTFIX% %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS %POSTFIX% %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --nic-name %NIC_NAME% --vnet-name %VNET_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new -g %RESOURCE_GROUP% --vm-name %VM_NAME% ^
  --size-in-gb 128 --vhd-name data1.vhd --storage-account-name %VHD_STORAGE%

:: Allow RDP
CALL azure network nsg rule create -g %RESOURCE_GROUP% --nsg-name %NSG_NAME% ^
  --direction Inbound --protocol Tcp --destination-port-range 3389 ^
  --source-port-range * --priority 100 --access Allow RDPAllow
```

<!-- links -->

[arm-templates]: ../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md
[audit-logs]: https://azure.microsoft.com/de-DE/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../virtual-machines/virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-storage]: ../storage/storage-introduction.md
[blob-snapshot]: ../storage/storage-blob-snapshots.md
[blog-storage]: ../storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/de-DE/blog/boot-diagnostics-for-virtual-machines-v2/
[data-disk]: ../virtual-machines/virtual-machines-disks-vhds.md
[disk-encryption]: ../azure-security-disk-encryption.md
[enable-monitoring]: ../azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../virtual-machines/virtual-machines-create-fqdn-on-portal.md
[log-collector]: https://azure.microsoft.com/de-DE/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../virtual-machines/virtual-machines-manage-availability.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[password-reset]: ../virtual-machines/virtual-machines-windows-reset-password.md
[planned-maintenance]: ../virtual-machines/virtual-machines-planned-maintenance.md
[premium-storage]: ../storage/storage-premium-storage-preview-portal.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[reboot-logs]: https://azure.microsoft.com/de-DE/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/de-DE/library/hh848535.aspx
[resource-manager-overview]: ../resource-group-overview.md
[security-center]: https://azure.microsoft.com/de-DE/services/security-center/
[security-extensions]: ../virtual-machines/virtual-machines-extensions-features.md#security-and-protection
[select-vm-image]: ../virtual-machines/resource-groups-vm-searching.md
[services-by-region]: https://azure.microsoft.com/de-DE/regions/#services
[static-ip]: ../virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../virtual-machines/virtual-machines-size-specs.md
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-faq]: ../virtual-machines/virtual-machines-questions.md

<!---HONumber=AcomDC_0302_2016-->