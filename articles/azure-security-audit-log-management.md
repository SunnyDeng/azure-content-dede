<properties
   pageTitle="Microsoft Azure-Sicherheits- und Überwachungsprotokollverwaltung | Microsoft Azure"
   description="Dieser Artikel enthält eine Einführung in das Generieren, Sammeln und Analysieren von Sicherheitsprotokollen aus Diensten, die in Azure gehostet werden. Er richtet sich an IT-Experten und Sicherheitsanalysten, die sich täglich mit der Verwaltung von Informationsressourcen befassen, auch an diejenigen, die für die Sicherheits- und Compliancemaßnahmen in ihrer Organisation verantwortlich sind."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="msStevenPo"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/13/2015"
   ms.author="mnayak;tomsh;terrylan"/>

# Microsoft Azure-Sicherheits- und Überwachungsprotokollverwaltung

Mit Azure können Kunden mithilfe von Azure IaaS- (Infrastructure-as-a-Service) und PaaS-Rollen (Platform-as-a-Service) Sicherheitsereignisse generieren und im zentralen Speicher ihrer Abonnements sammeln. Anschließend können die Kunden [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/) verwenden, um die gesammelten Ereignisse zu aggregieren und zu analysieren. Darüber hinaus können diese gesammelten Ereignisse zur kontinuierlichen Überwachung in lokale SIEM-Systeme (Security Information and Event Management) exportiert werden.

Zum Azure-Lebenszyklus der Sicherheitsprotokollierung, Analyse und Überwachung gehören:

- **Generieren**: Instrumentieren von Anwendungen und Infrastruktur zum Auslösen von Ereignissen.
- **Sammeln**: Konfigurieren von Azure zum Sammeln der verschiedenen Sicherheitsprotokolle in einem Speicherkonto.
- **Analysieren**: Verwenden von Azure-Tools wie HDInsight und lokalen SIEM-Systemen zum Analysieren der Protokolle und zum Generieren von Informationen zur Sicherheit.
- **Überwachen und berichten**: Azure bietet zentralisierte Überwachungs- und Analysesysteme, die eine kontinuierliche Sichtbarkeit und rechtzeitige Warnungen bereitstellen.

Dieser Artikel konzentriert sich auf die Generierungs- und Sammlungsphasen des Lebenszyklus.

## Protokollgenerierung:
Sicherheitsereignisse werden im Windows-Ereignisprotokoll für die Kanäle **System**, **Sicherheit** und **Anwendung** auf virtuellen Computern ausgelöst. Um sicherzustellen, dass Ereignisse ohne potenzielle Datenverluste protokolliert werden, muss die Größe des Ereignisprotokolls unbedingt entsprechend konfiguriert werden. Richten Sie die Größe des Ereignisprotokolls an der Anzahl von Ereignissen, die durch die Überwachungsrichtlinieneinstellungen generiert werden, und an den definierten Ereignissammlungsrichtlinien aus. Weitere Informationen finden Sie unter [Planning for security audit monitoring and management](http://technet.microsoft.com/library/ee513968.aspx#BKMK_4) (in englischer Sprache).

>[AZURE.NOTE]Wenn Sie die Windows-Ereignisweiterleitung (Windows Event Forwarding, WEF) oder die Azure-Diagnose (wird im Abschnitt [Protokollsammlung](#log-collection) erklärt) verwenden, um Protokolle aus Cloud Services oder virtuellen Computern abzurufen, sollten Sie die möglichen Auswirkungen von Systemausfällen berücksichtigen. Wenn beispielsweise Ihre WEF-Umgebung für einige Zeit ausfällt, müssen Sie entweder sicherstellen, dass die Protokollgröße ausreicht, um einen größeren Zeitraum abzudecken, oder sich auf einen möglichen Protokolldatenverlust einstellen.

Für in Azure bereitgestellte Cloud Services-Anwendungen und für virtuelle Computer, die über den [Azure Marketplace für virtuelle Computer](http://azure.microsoft.com/marketplace/virtual-machines/#microsoft) erstellt wurden, ist eine Reihe von Betriebssystem-Sicherheitsereignissen standardmäßig aktiviert. Kunden können Ereignisse für die Überwachung hinzufügen, entfernen oder ändern, indem sie die Betriebssystem-Überwachungsrichtlinie anpassen. Weitere Informationen finden Sie unter [Security Policy Settings Reference](http://technet.microsoft.com/library/jj852210.aspx) (in englischer Sprache).

Mithilfe der folgenden Methoden können Sie weitere Protokolle über Betriebssystem- (z. B. Überwachungsrichtlinienänderungen) und Windows-Komponenten (z. B. IIS) generieren:

- Gruppenrichtlinien zum Einführen von Richtlinieneinstellungen für virtuelle Computer in Azure, die einer Domäne angehören.
- DSC (Desired State Configuration) zum Übertragen und Verwalten von Richtlinieneinstellungen. Weitere Informationen finden Sie unter [Azure PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx) (in englischer Sprache).
- Startcode für die Dienstbereitstellungsrolle zum Einführen von Einstellungen für Cloud Services (PaaS-Szenario).

Durch das Konfigurieren von Azure-Rollenstarttasks kann Code ausgeführt werden, bevor eine Rolle gestartet wird. Definieren Sie einen Starttask für eine Rolle, indem Sie der Definition der Rolle in der Dienstdefinitionsdatei das Element **Startup** hinzufügen, wie im folgenden Beispiel gezeigt. Weitere Informationen finden Sie unter [Ausführen von Starttasks in Azure](http://msdn.microsoft.com/library/azure/hh180155.aspx).

Die Taskdatei, die als Starttask ausgeführt werden soll (im folgenden Beispiel "EnableLogOnAudit.cmd") muss im Buildpaket enthalten sein. Wenn Sie Visual Studio verwenden, fügen Sie die Datei dem Cloudprojekt hinzu, klicken Sie mit der rechten Maustaste auf den Dateinamen, klicken Sie auf **Eigenschaften**, und legen Sie dann **In Ausgabeverzeichnis kopieren** auf **Immer kopieren** fest.

    <Startup>
        <Task commandLine="EnableLogOnAudit.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

Inhalt von "EnableLogOnAudit.cmd":

    @echo off
    auditpol.exe /set /category:"Logon/Logoff" /success:enable /failure:enable
    Exit /B 0

Die im vorherigen Beispiel verwendete Datei [Auditpol.exe](https://technet.microsoft.com/library/cc731451.aspx) ist ein im Windows Server-Betriebssystem enthaltenes Befehlszeilenprogramm, mit dem Sie Überwachungsrichtlinieneinstellungen verwalten können.

Verschiedene Komponenten des Windows-Betriebssystems können so konfiguriert werden, dass sie zusätzlich zu Windows-Ereignisprotokollen auch Protokolle generieren, die für die Analyse und Überwachung der Sicherheit wichtig sind. Beispielsweise werden IIS-Protokolle (Internet Information Services) und "http.err"-Protokolle für Webrollen automatisch generiert und können für die Sammlung konfiguriert werden. Diese Protokolle bieten wichtige Informationen, mit deren Hilfe nicht autorisierte Zugriffe oder Angriffe auf die Webrolle identifiziert werden können. Weitere Informationen finden Sie unter [Konfigurieren der Protokollierung in IIS](http://technet.microsoft.com/library/hh831775.aspx) und unter [Advanced Logging for IIS – Custom Logging](http://www.iis.net/learn/extensions/advanced-logging-module/advanced-logging-for-iis-custom-logging) (in englischer Sprache).

Um die IIS-Protokollierung in einer Webrolle zu ändern, können Kunden der Definitionsdatei des Webrollendiensts einen Starttask hinzufügen. Im folgenden Beispiel wird die HTTP-Protokollierung für eine Website namens "Contoso" aktiviert. Außerdem wird angegeben, dass IIS alle Anforderungen für die Website "Contoso" protokollieren soll.

Der Task, der die IIS-Konfiguration aktualisiert, muss in der Dienstdefinitionsdatei der Webrolle enthalten sein. Durch die folgenden Änderungen an der Dienstdefinitionsdatei wird ein Starttask ausgeführt, der die IIS-Protokollierung durch Ausführen eines Skripts namens "ConfigureIISLogging.cmd" konfiguriert.

    <Startup>
        <Task commandLine="ConfigureIISLogging.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

Inhalt von "ConfigureIISLogging.cmd"

    @echo off
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /dontLog:"True" /commit:apphost
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /selectiveLogging:"LogAll" /commit
    Exit /B 0


## <a name="log-collection"></a>Protokollsammlung
Das Sammeln von Sicherheitsereignissen und Protokollen von Cloud Services oder virtuellen Computern in Azure erfolgt über zwei Methoden:

- Azure-Diagnose, sammelt Ereignisse im Azure-Speicherkonto eines Kunden
- Windows-Ereignisweiterleitung (Windows Event Forwarding, WEF), eine Technologie in Windows-Computern

In der folgenden Tabelle sind einige wichtige Unterschiede zwischen diesen beiden Technologien aufgeführt. Die geeignete Methode zum Implementieren der Protokollsammlung sollte basierend auf Ihren Anforderungen und diesen wesentlichen Unterschieden gewählt werden.

| Azure-Diagnose | Windows-Ereignisweiterleitung |
|-----|-----|
|Unterstützt Azure Virtual Machines und Azure Cloud Services. | Unterstützt nur in Domänen eingebundene Azure Virtual Machines. |
|Unterstützt eine Vielzahl von Protokollformaten, z. B. Windows-Ereignisprotokolle, ETW-Ablaufverfolgungen ([Ereignisablaufverfolgung für Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)) und IIS-Protokolle. Weitere Informationen finden Sie unter [Von der Azure-Diagnose unterstützte Datenquellen](#diagnostics). |Unterstützt nur Windows-Ereignisprotokolle. |
|Überträgt gesammelte Daten in Azure Storage. |Verschiebt gesammelte Daten auf zentrale Sammelserver. |

##	Sammlung von Sicherheitsereignisdaten mit der Windows-Ereignisweiterleitung
Für in Domänen eingebundene Azure Virtual Machines können Sie WEF mithilfe von Gruppenrichtlinien auf die gleiche Weise konfigurieren wie für lokale in eine Domäne eingebundene Computer. Weitere Informationen hierzu finden Sie unter [Hybrid Cloud](http://www.microsoft.com/server-cloud/solutions/hybrid-cloud.aspx).

Bei dieser Vorgehensweise könnte eine Organisation ein IaaS-Abonnement erwerben, es über [ExpressRoute](http://azure.microsoft.com/services/expressroute/) oder ein Site-to-Site-VPN mit ihrem Unternehmensnetzwerk verbinden und dann die in Azure verfügbaren virtuellen Computer in die Unternehmensdomäne einbinden. Anschließend können Sie von den in der Domäne eingebundenen Computern aus die Windows-Ereignisweiterleitung konfigurieren.

Die Ereignisweiterleitung ist in zwei Teile unterteilt: Quelle und Sammler. Die Quelle ist der Computer, in dem die Sicherheitsprotokolle generiert werden. Der Sammler ist der zentrale Server, der die Ereignisprotokolle sammelt und konsolidiert. IT-Administratoren können Ereignisse abonnieren, damit sie Ereignisse empfangen und speichern können, die von Remotecomputern (der Ereignisquelle) weitergeleitet werden. Weitere Informationen finden Sie unter [Einrichten von Computern zum Weiterleiten und Sammeln von Ereignissen](http://technet.microsoft.com/library/cc748890.aspx).

Gesammelte Windows-Ereignisse können zur weiteren Analyse an lokale Analysetools wie z. B. SIEM gesendet werden.

## Sammlung von Sicherheitsdaten mit der Azure-Diagnose
Mit der Azure-Diagnose können Sie Diagnosedaten von einer Clouddienst-Worker- oder Webrolle oder von virtuellen Computern sammeln, die in Azure ausgeführt werden. Die Azure-Diagnose ist eine vordefinierte Gast-Agent-Erweiterung, die für die Datensammlung aktiviert und konfiguriert werden muss. Das Abonnement eines Kunden kann das Übertragen der Daten per Push in Azure Storage umfassen.

Die Daten sind während der Übertragung verschlüsselt (mithilfe von HTTPS). Für die Beispiele in diesem Dokument wird Azure-Diagnose 1.2 verwendet. Es wird empfohlen, für die Sammlung von Sicherheitsdaten ein Upgrade auf Version 1.2 oder höher durchzuführen. Weitere Informationen finden Sie unter [Sammeln von Protokollierungsdaten mit der Azure-Diagnose](http://msdn.microsoft.com/library/gg433048.aspx).

Das folgende Diagramm zeigt einen allgemeinen Datenfluss für die Sammlung von Sicherheitsdaten, bei dem die Azure-Diagnose und weitere Analyse- und Überwachungsfunktionen verwendet werden.

![][1]

Die Azure-Diagnose verschiebt Protokolle aus Cloud Services-Kundenanwendungen und [Azure Virtual Machines](virtual-machines-about.md) in Azure Storage. Basierend auf einem Protokollformat werden einige Daten in Azure-Tabellen und einige in Blobs gespeichert. Daten, die in [Azure Storage](storage-introduction.md) gesammelt werden, können mithilfe der Azure Storage-Clientbibliothek zur Überwachung und Analyse in lokale SIEM-Systeme heruntergeladen werden.

Darüber hinaus kann HDInsight zur weiteren Analyse der Daten in der Cloud verwendet werden. Im Folgenden sehen Sie Beispiele für die Sammlung von Sicherheitsdaten, in denen die Azure-Diagnose verwendet wird.

### Sammlung von Sicherheitsdaten von Azure Virtual Machines mithilfe der Azure-Diagnose

In den folgenden Beispielen werden die Azure-Diagnose 1.2 und Azure PowerShell-Cmdlets verwendet, um die Sammlung von Sicherheitsdaten von virtuellen Computern zu aktivieren. Die Daten werden von virtuellen Computern in einem geplanten Intervall (das konfigurierbar ist) gesammelt und per Push an Azure Storage innerhalb eines Kundenabonnements übertragen. In diesem Abschnitt führen wir durch zwei Szenarien zur Protokollsammlung mit der Azure-Diagnose:

1. Einrichten einer neuen Instanz einer Pipeline zur Sicherheitsprotokollsammlung auf einem virtuellen Computer
2. Aktualisieren einer vorhandenen Pipeline zur Sicherheitsprotokollsammlung mit einer neuen Konfiguration auf einem virtuellen Computer

#### Einrichten einer neuen Instanz einer Pipeline zur Sicherheitsprotokollsammlung auf einem virtuellen Computer
In diesem Beispiel richten wir eine neue Instanz der Pipeline zur Sicherheitsprotokollsammlung ein, die Azure-Diagnose verwendet, und stellen Anmeldefehlerereignisse (Ereignis-IDs 4624 und 4625) auf den virtuellen Computern fest. Sie können die folgenden Schritte von Ihrer Entwicklungsumgebung aus implementieren oder eine Remotedesktopsitzung über das Remotedesktopprotokoll (RDP) für den Knoten in der Cloud verwenden.

##### Schritt 1: Installieren Sie das Azure PowerShell-SDK.
Das Azure PowerShell-SDK bietet Cmdlets für die Konfiguration der Azure-Diagnose in Azure Virtual Machines. Die erforderlichen Cmdlets sind in Azure PowerShell, Version 0.8.7 oder höher, verfügbar. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

##### Schritt 2: Vorbereiten der Konfigurationsdatei
Bereiten Sie die Konfigurationsdatei basierend auf den Ereignissen vor, die Sie sammeln möchten. Es folgt ein Beispiel für eine Konfigurationsdatei der Azure-Diagnose zum Erfassen von Windows-Ereignissen aus dem Kanal **Sicherheit** mit hinzugefügten Filtern, um nur erfolgreiche und fehlerhafte Anmeldeereignisse zu sammeln. Weitere Informationen finden Sie unter [Azure Diagnostics 1.2 Configuration Schema](http://msdn.microsoft.com/library/azure/dn782207.aspx).

Das Speicherkonto kann in der Konfigurationsdatei angegeben werden, oder es kann als Parameter bereitgestellt werden, wenn Sie Azure PowerShell-Cmdlets zum Einrichten von Azure-Diagnose ausführen.

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
            <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
                <WindowsEventLog scheduledTransferPeriod="PT1M">
                    <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
                </WindowsEventLog>
            </DiagnosticMonitorConfiguration>
        </WadCfg>
    </PublicConfig>

Wenn Sie den vorherigen Inhalt als XML-Datei speichern, legen Sie die Codierung auf **UTF-8** fest. Wenn Sie den Editor verwenden, steht die Codierungsoption im Dialogfeld "Speichern unter" zur Verfügung. In der folgenden Tabelle werden einige wichtige Attribute der Konfigurationsdatei aufgeführt.

| Attribut | Beschreibung |
|----- |-----|
| overallQuotaInMB | Die maximale Menge des lokalen Speicherplatzes, die von der Azure-Diagnose genutzt werden kann. (Der Wert ist konfigurierbar.) |
| scheduledTransferPeriod | Das Intervall zwischen geplanten Datenübertragungen an Azure Storage, auf die nächste Minute aufgerundet. |
| Name | In WindowsEventLog entspricht dieses Attribut der XPath-Abfrage, die die zu sammelnden Windows-Ereignisse beschreibt. Sie können die Datensammlung durch Hinzufügen eines Filters filtern, z. B. nach Ereignis-ID, Name des Anbieters oder Kanal. |

Alle Windows-Ereignisprotokolldaten werden in eine Tabelle namens **WADWindowsEventLogsTable** verschoben. Derzeit wird das Umbenennen der Tabelle nicht von der Azure-Diagnose unterstützt.

##### <a name="step3"></a> Schritt 3: Überprüfen der XML-Konfigurationsdatei
Gehen Sie folgendermaßen vor, um sicherzustellen, dass in der XML-Konfigurationsdatei kein Fehler vorliegt und dass sie mit dem Azure-Diagnose-Schema kompatibel ist:

1. Führen Sie zum Herunterladen der Schemadatei den folgenden Befehl aus, und speichern Sie dann die Datei.

    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfigSchema.xsd'

2. Nachdem Sie die Schemadatei heruntergeladen haben, können Sie die XML-Konfigurationsdatei anhand des Schemas überprüfen. So überprüfen Sie die Datei mithilfe von Visual Studio:
  - Öffnen Sie die XML-Datei in Visual Studio.
  - Drücken Sie F4, um die **Eigenschaften** zu öffnen.
  - Klicken Sie auf **Schema**, klicken Sie auf **Hinzufügen**, wählen Sie die Schemadatei aus, die Sie heruntergeladen haben (WadConfigSchema.XSD), und klicken Sie dann auf **OK**.

3.	Klicken Sie im Menü **Ansicht** auf **Fehlerliste**, um nachzusehen, ob Überprüfungsfehler aufgetreten sind.

##### <a name="step4"></a> Schritt 4: Konfigurieren der Azure-Diagnose
 Gehen Sie folgendermaßen vor, um die Azure-Diagnose zu aktivieren und die Datensammlung zu starten:

 1.	Geben Sie zum Öffnen von Azure PowerShell **Add-AzureAccount** ein, und drücken Sie die EINGABETASTE.
 2.	Melden Sie sich mit Ihrem Azure-Konto an.
 3.	Führen Sie das folgende PowerShell-Skript aus: Stellen Sie sicher, dass Sie "storage\_name", "key", "config\_path", "service\_name" und "vm\_name" aktualisieren.

 ```PowerShell
$storage_name ="<Storage Name>"
$key = "<Storage Key>"
$config_path="<Path Of WAD Config XML>"
$service_name="<Service Name. Usually it is same as VM Name>"
$vm_name="<VM Name>"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM
 ```

##### Schritt 5: Generieren von Ereignissen
Zu Demonstrationszwecken erstellen wir einige Anmeldeereignisse und stellen sicher, dass die Daten in Azure Storage übertragen werden. Wie zuvor in Schritt 2 gezeigt, ist die XML-Datei so konfiguriert, dass die Ereignis-ID 4624 (Anmeldung erfolgreich) und die Ereignis-ID 4625 (Anmeldungsfehler) aus dem Kanal **Sicherheit** erfasst werden.

 So generieren Sie diese Ereignisse

1.	Öffnen Sie eine RDP-Sitzung mit dem virtuellen Computer.
2.	Geben Sie falsche Anmeldeinformationen ein, um einige fehlerhafte Anmeldeereignisse (Ereignis-ID 4625) zu generieren.
3.	Nach einigen fehlerhaften Anmeldeversuchen geben Sie die richtigen Anmeldeinformationen ein, um ein erfolgreiches Anmeldeereignis (EventID 4624) zu generieren.

##### Schritt 6: Anzeigen von Daten
Basierend auf der Konfiguration in der XML-Datei sollten ungefähr fünf Minuten nach Abschluss der vorherigen Schritte Daten beim Kundenspeicherkonto eingehen. Es gibt viele Tools zum Anzeigen der Daten aus Azure Storage. Weitere Informationen finden Sie unter:

- [Durchsuchen von Speicherressourcen mit Server-Explorer](http://msdn.microsoft.com/library/azure/ff683677.aspx)
- [Azure-Speicher-Explorer 6, Vorschau 3 (August 2014)](http://azurestorageexplorer.codeplex.com/)

So zeigen Sie Ihre Daten an

1.	Klicken Sie in Visual Studio (2013, 2012 und 2010 mit SP1) auf **Anzeigen** und dann auf **Server-Explorer**.
2.	Navigieren Sie zum Speicherkonto.
3.	Klicken Sie auf **Tabellen**, und doppelklicken Sie dann auf die entsprechenden Tabellen, um die von den virtuellen Computern gesammelten Sicherheitsprotokolle anzuzeigen. ![][2]

4.	Klicken Sie mit der rechten Maustaste auf die Tabelle "WADWindowsEventLogsTable", und klicken Sie dann auf **Daten anzeigen**, um die Tabellenansicht zu öffnen:

![][3]

In der obigen Speichertabelle sind **PartitionKey**, **RowKey** und **Timestamp** Systemeigenschaften.

- **PartitionKey** ist ein Zeitstempel in Sekunden. Dies ist ein eindeutiger Bezeichner für die Partition innerhalb der Tabelle.
- **RowKey** ist ein eindeutiger Bezeichner für eine Entität innerhalb einer Partition.

Zusammen wird durch **PartitionKey** und **RowKey** jede Entität innerhalb einer Tabelle eindeutig identifiziert.

- "Timestamp" ist ein Datums-/Uhrzeitwert, der auf dem Server verwaltet wird, um den Zeitpunkt der letzten Änderung eines Elements nachzuverfolgen.

>[AZURE.NOTE]Die maximale Zeilengröße in einer Azure Storage-Tabelle ist auf 1 MB beschränkt. Ein Speicherkonto kann bis zu 200 TB Daten aus Blobs, Warteschlangen und Tabellen enthalten, wenn das Konto nach Juni 2012 erstellt wurde. Daher kann die Tabellengröße auf bis zu 200 TB anwachsen, wenn Blobs und Warteschlangen keinen Speicherplatz belegen. Für Konten, die vor Juni 2012 erstellt wurden, gilt ein Grenzwert von 100 TB.

Der Speicher-Explorer ermöglicht Ihnen auch das Bearbeiten von Tabellendaten. Doppelklicken Sie auf eine bestimmte Zeile in der Tabellenansicht, um das hier gezeigte Fenster "Entität bearbeiten" zu öffnen:

![][4]

#### Aktualisieren einer vorhandenen Pipeline zur Sicherheitsprotokollsammlung mit einer neuen Konfiguration auf einem virtuellen Computer
In diesem Abschnitt aktualisieren wir eine vorhandene Pipeline zur Sicherheitsprotokollsammlung mit der Azure-Diagnose auf einem virtuellen Computer. Außerdem ermitteln wir Fehler im Windows-Anwendungsereignisprotokoll.

##### Schritt 1: Ergänzen der Konfigurationsdatei durch relevante Ereignisse
Die im vorherigen Beispiel erstellte Azure-Diagnose-Datei muss aktualisiert werden, um die Fehlertypen des Windows-Anwendungsereignisprotokolls zu berücksichtigen.

>[AZURE.NOTE]Alle vorhandenen Konfigurationseinstellungen der Azure-Diagnose müssen mit der neuen Konfigurationsdatei zusammengeführt werden. Die in der neuen Datei definierten Einstellungen überschreiben die vorhandenen Konfigurationen.

Zum Abrufen der vorhandenen Konfigurationseinstellung können Sie das Cmdlet **Get-AzureVMDiagnosticsExtension** verwenden. Im Folgenden finden Sie ein Azure PowerShell-Beispielskript zum Abrufen der vorhandenen Konfiguration:

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    $config = Get-AzureVMDiagnosticsExtension -VM $VM1 | Select -Expand PublicConfiguration | % {$_.substring($_.IndexOf(':"')+2,$_.LastIndexOf('",')-$_.IndexOf(':"')-2)}
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($config))
Aktualisieren Sie die Konfiguration der Azure-Diagnose für das Sammeln von Fehlern und kritischen Ereignissen im Windows-Anwendungsereignisprotokoll wie folgt:

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level =2)]]" />
                <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Überprüfen Sie die Konfigurationsdatei anhand derselben Schritte, die zuvor in [Schritt 3: Überprüfen der XML-Konfigurationsdatei](#step3) aufgeführt wurden.

##### Schritt 2: Aktualisieren der Azure-Diagnose für die Verwendung der neuen Konfigurationsdatei
Verwenden Sie die Cmdlets **Set-AzureVMDiagnosticsExtension** und **Update-AzureVM**, um die Konfiguration zu aktualisieren, wie weiter oben in [Schritt 4: Konfigurieren der Azure-Diagnose](#step4) gezeigt wurde.

##### Schritt 3: Überprüfen der Konfigurationseinstellungen
Führen Sie den folgenden Befehl aus, um sicherzustellen, dass die Einstellungen aktualisiert wurden:

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    Get-AzureVMDiagnosticsExtension -VM $VM1

##### Schritt 4: Generieren von Ereignissen
Führen Sie für dieses Beispiel den folgenden Befehl aus, um ein Anwendungsereignisprotokoll des Typs **Fehler** zu generieren:

    eventcreate /t error /id 100 /l application /d "Create event in application log for Demo Purpose"

![][5]

Öffnen Sie die Ereignisanzeige, um sicherzustellen, dass das Ereignis erstellt wurde.

![][6]

##### Schritt 5: Anzeigen von Daten
Öffnen Sie den Server-Explorer in Visual Studio, um die Protokolldaten anzuzeigen. Sie sollten einen Eintrag **EventID 100** vorfinden, der auf **ContosoDesktop** wie hier gezeigt erstellt wurde:

![][7]

## Sammlung von Sicherheitsdaten von Azure Cloud Services mithilfe der Azure-Diagnose

Jetzt verwenden wir die Azure-Diagnose, um dieselben beiden Szenarien zur Protokollsammlung wie im vorherigen Abschnitt über Virtual Machines (IaaS) von Azure Cloud Services aus zu untersuchen:

1.	Einrichten einer neuen Instanz der Sicherheitsprotokollpipeline in einem Clouddienst
2.	Aktualisieren einer vorhandenen Pipeline zur Sicherheitsprotokollsammlung mit einer neuen Konfiguration in einem Clouddienst

Die schrittweise exemplarische Vorgehensweise in diesem Abschnitt umfasst folgende Aktionen:

1.	Erstellen eines Clouddiensts
2.	Konfigurieren des Clouddiensts für die Sicherheitsprotokollsammlung mithilfe der Azure-Diagnose
3.	Veranschaulichen der Generierung und Sammlung von Sicherheitsereignissen für den Clouddienst:

    - Hinzufügen eines Administrators zu einer lokalen Gruppe mit Rechteerweiterungen
    - Erstellung neuer Prozesse
4.	Aktualisieren einer vorhandenen Pipeline zur Sicherheitsprotokollsammlung in einem Clouddienst:

    - Aktivieren der Überwachung von Hostfirewallereignissen (als Beispiel für Sicherheitsereignisse im Netzwerk) mit Auditpol
    - Konfigurieren der zu sammelnden Firewallüberwachungsdaten und Anzeigen der gesammelten Ereignisse im Kundenspeicherkonto
5.	Anzeigen der Verteilung von Windows-Sicherheitsereignissen und der Erkennen von Spitzen
6.	Konfigurieren der Sammlung von IIS-Protokollen und Überprüfen der Daten

Alle Ereignisse und Protokolle werden in einem Kundenspeicherkonto in Azure gesammelt. Die Ereignisse können vom Kunden angezeigt und in lokale SIEM-Systeme exportiert werden. Sie können auch mithilfe von HDInsight aggregiert und analysiert werden.

### Einrichten einer neuen Instanz einer Pipeline zur Protokollsammlung in einem Clouddienst
In diesem Beispiel richten wir eine neue Instanz einer Pipeline zur Sicherheitsprotokollsammlung ein, die die Azure-Diagnose verwendet. Wir stellen fest, dass einer lokalen Gruppe ein Benutzer hinzugefügt wurde, und ermitteln Ereignisse zur Erstellung neuer Prozesse in einer Clouddienstinstanz.

#### Schritt 1: Erstellen und Bereitstellen eines Clouddiensts (Webrolle)

1.	Starten Sie auf dem Entwicklungscomputer Visual Studio 2013.
2.	Erstellen Sie ein neues Clouddienstprojekt. (Im Beispiel wird "ContosoWebRole" verwendet.)
3.	Wählen Sie die Webrolle **ASP.NET** aus.
4.	Wählen Sie das Projekt **MVC** aus.
5.	Klicken Sie im Projektmappen-Explorer auf **Rollen**, und doppelklicken Sie dann auf die Webrolle (WebRole1), um das Fenster **Eigenschaften** zu öffnen.
6.	Deaktivieren Sie auf der Registerkarte **Konfiguration** das Kontrollkästchen **Diagnose aktivieren**, um die Version der Azure-Diagnose zu deaktivieren, die mit Visual Studio 2013 ausgeliefert wird. ![][8]

7.	Erstellen Sie die Projektmappe, um zu überprüfen, ob Fehler vorliegen.
8.	Öffnen Sie die Datei "WebRole1/Controllers/HomeController.cs".
9.	Fügen Sie die folgende Methode hinzu, damit die Beispielanwendung den HTTP-Statuscode 500 als Beispielereignis im IIS-Protokoll protokolliert (dieses wird im IIS-Beispiel weiter unten verwendet):

    ```
    public ActionResult StatusCode500()
        {
            throw new InvalidOperationException("Response.StatusCode is 500");
        }
    ```

10.	 Klicken Sie mit der rechten Maustaste auf den Namen des Clouddienstprojekts, und klicken Sie auf **Veröffentlichen**.

#### Schritt 2: Vorbereiten der Konfigurationsdatei
Wir werden jetzt die Azure-Diagnose-Konfigurationsdatei vorbereiten, um Ereignisse hinzuzufügen, mit denen die folgenden Situationen erkannt werden können:

- Hinzufügen neuer Benutzer zu einer lokalen Gruppe
- Erstellung neuer Prozesse

```
<?xml version="1.0" encoding="UTF-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
</PublicConfig>
```

#### Schritt 3: Überprüfen der XML-Konfigurationsdatei
Überprüfen Sie die Konfigurationsdatei anhand derselben Schritte, die zuvor in [Schritt 3: Überprüfen der XML-Konfigurationsdatei](#step3) aufgeführt wurden.
#### Schritt 4: Konfigurieren der Azure-Diagnose
Führen Sie das folgende Azure PowerShell-Skript zum Aktivieren der Azure-Diagnose aus. (Aktualisieren Sie dabei "storage\_name", "key", "config\_path" und "service\_name".)

    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Um zu überprüfen, dass der Dienst die aktuelle Diagnosekonfiguration verwendet, führen Sie den folgenden Azure PowerShell-Befehl aus:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Schritt 5: Generieren von Ereignissen
So generieren Sie Ereignisse

1.	Um eine Remotedesktopsitzung mit Ihrer Clouddienstinstanz zu starten, öffnen Sie in Visual Studio den Server-Explorer, klicken Sie mit der rechten Maustaste auf die Rolleninstanz, und klicken Sie auf "Mithilfe von Remotedesktop verbinden".
2.	Öffnen Sie ein Eingabeaufforderungsfenster mit erhöhten Rechten, und führen Sie die folgenden Befehle aus, um ein lokales Administratorkonto auf dem virtuellen Computer zu erstellen:


    net user contosoadmin <enterpassword> /add net localgroup administrators contosoadmin /add

3.	Öffnen Sie die Ereignisanzeige, öffnen Sie den Kanal **Sicherheit**, und beachten Sie, dass ein Ereignis 4732 erstellt wurde, wie hier gezeigt:

![][9]

#### Schritt 6: Anzeigen von Daten
Warten Sie etwa fünf Minuten, bis der Azure-Diagnose-Agent Ereignisse per Push an die Speichertabelle übertragen hat.

![][10]

Um das Ereignis zur Erstellung von Prozessen zu überprüfen, öffnen Sie einen Editor. Wie hier gezeigt, wurde ein Ereignis zur Prozesserstellung im Sicherheitskanal protokolliert.

![][11]

Sie können nun dasselbe Ereignis in Ihrem Speicherkonto wie folgt anzeigen:

![][12]

### Aktualisieren einer vorhandenen Pipeline zur Sicherheitsprotokollsammlung in einem Clouddienst mit einer neuen Konfiguration
In diesem Abschnitt aktualisieren wir eine vorhandene Pipeline zur Sicherheitsprotokollsammlung mit der Azure-Diagnose und ermitteln Änderungsereignisse für die Windows-Firewall in einer Cloud Services-Instanz.

Zum Erkennen von Änderungen an der Firewall wird die vorhandene Konfiguration durch Änderungsereignisse für die Firewall ergänzt.

#### Schritt 1: Abrufen der vorhandenen Konfiguration
>[AZURE.NOTE]Die neuen Konfigurationseinstellungen überschreiben die vorhandene Konfiguration. Es ist daher wichtig, alle vorhandenen Konfigurationseinstellungen der Azure-Diagnose mit der neuen Konfigurationsdatei zusammenzuführen.

Zum Abrufen der vorhandenen Konfigurationseinstellung können Sie das Cmdlet **Get-AzureServiceDiagnosticsExtension** verwenden:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Schritt 2: Ergänzen der XML-Konfigurationsdatei durch Firewallereignisse

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Überprüfen Sie den XML-Inhalt anhand desselben Überprüfungsprozesses, der weiter oben in [Schritt 3: Überprüfen der XML-Konfigurationsdatei](#step3) beschrieben wurde.

#### Schritt 3: Aktualisieren der Azure-Diagnose für die Verwendung der neuen Konfiguration

Führen Sie das folgende Azure PowerShell-Skript aus, um die Azure-Diagnose auf die Verwendung der neuen Konfiguration zu aktualisieren. (Aktualisieren Sie "storage\_name", "key", "config\_path" und "service\_name" mit Ihren Clouddienstinformationen.)

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Um zu überprüfen, dass der Dienst die aktuelle Diagnosekonfiguration verwendet, führen Sie den folgenden Azure PowerShell-Befehl aus:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Schritt 4: Aktivieren von Firewallereignissen

1.	Öffnen Sie eine Remotedesktopsitzung mit Ihrer Clouddienstinstanz.
2.	Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie den folgenden Befehl aus:

    ```
    auditpol.exe /set /category:"Policy Change" /subcategory:"MPSSVC rule-level Policy Change" /success:enable /failure:enable
    ```

#### Schritt 5: Generieren von Ereignissen

1.	Öffnen Sie die Windows-Firewall, und klicken Sie auf **Eingehende Regeln**.
2.	Klicken Sie auf **Neue Regel hinzufügen** und dann auf **Port**.
3.	Geben Sie im Feld **Lokale Ports** den Wert **5000** ein, und klicken Sie dann drei Mal auf **Weiter**.
4.	Geben Sie im Feld **Name** den Namen **Test5000** ein, und klicken Sie dann auf **Fertig stellen**.
5.	Öffnen Sie die Ereignisanzeige, öffnen Sie den Kanal **Sicherheit**, und beachten Sie, dass ein Ereignis mit der ID 4946 erstellt wurde, wie hier gezeigt:

![][13]

#### Schritt 6: Anzeigen von Daten
Warten Sie etwa fünf Minuten, bis der Azure-Diagnose-Agent die Ereignisdaten per Push an die Speichertabelle übertragen hat.

![][14]

### Verteilung von Sicherheitsereignissen und Erkennen von Spitzen
Nachdem sich die Ereignisse im Kundenspeicherkonto befinden, können Anwendungen mithilfe der Speicherclientbibliotheken auf die Ereignisaggregation zugreifen und diese durchführen. Beispielcode für den Zugriff auf Tabellendaten finden Sie unter [Abrufen von Tabellendaten](storage-dotnet-how-to-use-tables.md).

Es folgt ein Beispiel für die Ereignisaggregation: Spitzen in der Ereignisverteilung können auf anomale Aktivitäten näher untersucht werden.

![][15]

### IIS-Protokollsammlung und -verarbeitung mit HDInsight
In diesem Abschnitt sammeln wir IIS-Protokolle aus Ihrer Webrolleninstanz und verschieben die Protokolle in ein Azure-Blob im Speicherkonto des Kunden.

#### Schritt 1: Ergänzen der Konfigurationsdatei durch die IIS-Protokollsammlung

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <Directories scheduledTransferPeriod="PT5M">
        <IISLogs containerName="iislogs" />
        </Directories>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

In der vorherigen Konfiguration der Azure-Diagnose ist **containerName** der Name des Blobcontainers, in den Protokolle innerhalb des Kundenspeicherkontos verschoben werden.

Überprüfen Sie die Konfigurationsdatei anhand derselben Schritte, die zuvor in [Schritt 3: Überprüfen der XML-Konfigurationsdatei](#step3) aufgeführt wurden.


#### Schritt 2: Aktualisieren der Azure-Diagnose für die Verwendung einer neuen Konfiguration
Führen Sie das folgende Azure PowerShell-Skript aus, um die Azure-Diagnose auf die Verwendung der neuen Konfiguration zu aktualisieren. (Aktualisieren Sie "storage\_name", "key", "config\_path" und "service\_name" mit Ihren Clouddienstinformationen.)

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Um zu überprüfen, dass der Dienst die aktuelle Diagnosekonfiguration verwendet, führen Sie den folgenden Azure PowerShell-Befehl aus:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Schritt 3: Generieren von IIS-Protokollen

1.	Öffnen Sie einen Webbrowser, und navigieren Sie zur Clouddienst-Webrolle (z. B. http://contosowebrole.cloudapp.net/).
2.	Navigieren Sie zu den Seiten **Info** und **Kontakt**, um einige Protokollereignisse zu erstellen.
3.	Navigieren Sie zu einer Seite, die einen Statuscode 500 generiert (z. B. http://contosowebrole.cloudapp.net/Home/StatusCode500). Ein Fehler ähnlich dem folgenden wird angezeigt. Beachten Sie, dass wir in Schritt 1 des Abschnitts "Einrichten einer neuen Instanz einer Pipeline zur Protokollsammlung in einem Clouddienst" Code für **StatusCode500** hinzugefügt haben. ![][16]
4.	Öffnen Sie eine Remotedesktopsitzung mit Ihrer Clouddienstinstanz.
5.	Öffnen Sie IIS-Manager.
6.	Die IIS-Protokollierung ist standardmäßig aktiviert und auf das stündliche Generieren von Dateien festgelegt, die alle Felder im W3C-Format enthalten. Klicken Sie auf **Durchsuchen**, und Sie finden mindestens eine Protokolldatei, wie hier gezeigt: ![][17]

7.	Warten Sie etwa fünf Minuten, bis der Azure-Diagnose-Agent die Protokolldatei per Push in den Blobcontainer geschoben hat. Öffnen Sie zum Überprüfen der Daten **Server-Explorer** > **Speicher** > **Speicherkonto** > **Blobs**. Wie hier gezeigt, wurde das Blob **iislogs** erstellt: ![][18]

8.	Klicken Sie mit der rechten Maustaste, und wählen Sie **Blobcontainer anzeigen**, um die im Blob gespeicherte IIS-Protokolldatei anzuzeigen: ![][19]
9.	Sobald die IIS-Ereignisse sich im Speicherkonto des Kunden befinden, können Anwendungen, die die HDInsight-Analyse nutzen, eine Ereignisaggregation ausführen. Das folgenden Liniendiagramm ist ein Beispiel für einen Ereignisaggregationstask, der den HTTP-Statuscode 500 aufweist: ![][20]

## Empfehlungen zur Sicherheitsprotokollsammlung
Wenn Sie Sicherheitsprotokolle sammeln, empfehlen wir Folgendes:

- Sammeln Sie Ihre eigenen dienstanwendungsspezifischen Überwachungsprotokollereignisse.
- Konfigurieren Sie nur die Daten, die zur Analyse und Überwachung benötigen. Das Sammeln zu vieler Daten erschwert die Problembehandlung und kann sich auf Ihre Kosten für Dienste oder Speicher auswirken.
- Führen Sie vorhandene Azure-Diagnose-Konfigurationseigenschaften mit den von Ihnen vorgenommenen Änderungen zusammen. Die neue Konfigurationsdatei überschreibt die vorhandenen Konfigurationseinstellungen.
- Wählen Sie das Intervall für **ScheduledTransferPeriod** mit Bedacht. Kürzere Datenübertragungszeiten steigern die Relevanz der Daten, können jedoch die Speicherkosten und den Mehraufwand für die Verarbeitung erhöhen.

>[AZURE.NOTE]Die andere Variable, die sich erheblich auf die Menge der gesammelten Daten auswirkt, ist der Protokolliergrad. Im Folgenden finden Sie ein Beispiel dafür, wie Protokolle nach Protokolliergrad gefiltert werden:

    System!*[System[(Level =2)]]

Der Protokolliergrad ist kumulativ. Wenn der Filter auf **Warnung** festgelegt wird, werden auch **Fehler** und **kritische Ereignisse** gesammelt.

- Löschen Sie regelmäßig die Diagnosedaten aus Azure Storage, wenn sie nicht länger benötigt werden.

>[AZURE.NOTE]Weitere Informationen zu Diagnosedaten finden Sie unter [Speichern und Anzeigen von Diagnosedaten in Azure Storage](https://msdn.microsoft.com/library/azure/hh411534.aspx). Die Container und Tabellen, in denen Diagnosedaten gespeichert werden, sind genau wie andere Container und Tabellen. Sie können daraus Blobs und Entitäten auf die gleiche Weise löschen wie bei anderen Daten. Sie können die Diagnosedaten programmgesteuert über eine der Speicherclientbibliotheken oder visuell über einen [Speicher-Explorer-Client](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) löschen.

- Es hat sich bewährt, Dienstdaten und Sicherheitsprotokolldaten in separaten Speicherkonten zu speichern. Durch diese Isolierung wird sichergestellt, dass die Speicherung von Sicherheitsprotokolldaten nicht die Speicherleistung für Produktionsdienstdaten beeinträchtigt.
- Wählen Sie die Aufbewahrungsdauer für Protokolle basierend auf der Konformitätsrichtlinie und den Anforderungen zur Datenanalyse und -überwachung in Ihrer Organisation.

## Exportieren von Sicherheitsprotokollen auf ein anderes System
Sie können Blobdaten mithilfe der Azure Storage-Clientbibliothek herunterladen und dann zur Verarbeitung auf Ihr lokales System exportieren. Beispielcode zum Verwalten von Blobdaten finden Sie unter [Verwenden des Blobspeichers mit .NET](storage-dotnet-how-to-use-blobs.md).

Auf ähnliche Weise können Sie Sicherheitsdaten, die in Azure-Tabellen gespeichert sind, mithilfe der Azure Storage-Clientbibliothek herunterladen. Weitere Informationen über den Zugriff auf Daten, die in Tabellen gespeichert sind, finden Sie unter [Verwenden des Tabellenspeichers mit .NET](storage-dotnet-how-to-use-tables.md).

## Azure Active Directory-Berichte
Azure Active Directory (Azure AD) umfasst eine Reihe von Sicherheits-, Nutzungs- und Überwachungsprotokollberichten, die einen Einblick in die Integrität und Sicherheit Ihres Azure AD-Mandanten bereitstellen. Azure AD bietet z. B. die Möglichkeit, Benutzeraktivitäten und anomale Oberflächenzugriffe automatisch zu analysieren und die Ergebnisse den Kunden in Berichten zur Verfügung zu stellen.

Diese Berichte sind über das [Azure-Verwaltungsportal](https://manage.windowsazure.com/) unter **Active Directory** > **Verzeichnis** zugänglich. Einige dieser Berichte sind kostenlos, und andere werden als Teil einer Azure AD Premium Edition angeboten. Weitere Informationen zu Azure AD-Berichten finden Sie unter [Anzeigen von Zugriffs- und Nutzungsberichten](http://msdn.microsoft.com/library/azure/dn283934.aspx).

## Azure-Vorgangsprotokolle
Protokolle für Vorgänge im Zusammenhang mit Ihren Azure-Abonnementressourcen stehen auch über das Feature **Vorgangsprotokolle** im Verwaltungsportal zur Verfügung.

Öffnen Sie zum Anzeigen der **Vorgangsprotokolle** das [Azure-Verwaltungsportal](https://manage.windowsazure.com/), klicken Sie auf **Verwaltungsdienste**, und klicken Sie dann auf **Vorgangsprotokolle**.

## <a name="diagnostics"></a> Von der Azure-Diagnose unterstützte Datenquellen

| Datenquelle | Beschreibung |
|----- | ----- |
| IIS-Protokolle | Informationen zu IIS-Websites |
| Infrastrukturprotokolle der Azure-Diagnose | Informationen zur Azure-Diagnose |
| Protokolle zu IIS-Anforderungsfehlern | Informationen zu fehlerhaften Anforderungen an eine IIS-Website oder -Anwendung |
| Windows-Ereignisprotokolle | An das Windows-System für die Ereignisprotokollierung gesendete Informationen |
| Leistungsindikatoren | Leistungsindikatoren des Betriebssystems und benutzerdefinierte Leistungsindikatoren |
| Absturzabbilder | Informationen zum Status des Prozesses im Fall eines Anwendungsabsturzes |
| Benutzerdefinierte Fehlerprotokolle | Von Ihrer Anwendung oder Ihrem Dienst erstellte Protokolle |
| .NET-EventSource | Von Ihrem Code mit der .NET-EventSource-Klasse generierte Ereignisse |
| Manifestbasierte ETW | Von einem beliebigen Prozess generierte Ereignisse der Ereignisablaufverfolgung für Windows |

## Zusätzliche Ressourcen
Die folgenden Ressourcen bieten allgemeine Informationen zu Microsoft Azure und verwandte Microsoft-Dienste:

- [Microsoft Azure Trust Center](http://azure.microsoft.com/support/trust-center/)

    Informationen zur Einbettung von Sicherheit und Datenschutz in die Entwicklung von Azure und zur Einhaltung einer Vielzahl von internationalen und branchenspezifischen Compliancestandards mit Azure

- [Microsoft Azure-Startseite](http://www.microsoft.com/windowsazure/)

    Allgemeine Informationen und Links zu Microsoft Azure

- [Microsoft Azure-Dokumentationscenter](http://msdn.microsoft.com/windowsazure/default.aspx)

    Anleitungen für Azure-Dienste und Automatisierungsskripts

- [Microsoft Security Response Center (MSRC)](http://www.microsoft.com/security/msrc/default.aspx)

    Das MSRC arbeitet mit Partnern und Sicherheitsexperten auf der ganzen Welt zusammen, um Sicherheitsvorfälle zu verhindern und die Sicherheit von Microsoft-Produkten zu erhöhen.

- [E-Mail an das Microsoft Security Response Center](mailto:secure@microsoft.com)

    Senden Sie eine E-Mail, um Sicherheitsrisiken für Microsoft, einschließlich Microsoft Azure, zu melden.

<!--Image references-->
[1]: ./media/azure-security-audit-log-management/sec-security-data-collection-flow.png
[2]: ./media/azure-security-audit-log-management/sec-storage-table-security-log.PNG
[3]: ./media/azure-security-audit-log-management/sec-wad-windows-event-logs-table.png
[4]: ./media/azure-security-audit-log-management/sec-edit-entity.png
[5]: ./media/azure-security-audit-log-management/sec-app-event-log-cmd.png
[6]: ./media/azure-security-audit-log-management/sec-event-viewer.png
[7]: ./media/azure-security-audit-log-management/sec-event-id100.png
[8]: ./media/azure-security-audit-log-management/sec-diagnostics.png
[9]: ./media/azure-security-audit-log-management/sec-event4732.png
[10]: ./media/azure-security-audit-log-management/sec-step6.png
[11]: ./media/azure-security-audit-log-management/sec-process-creation-event.png
[12]: ./media/azure-security-audit-log-management/sec-process-creation-event-storage.png
[13]: ./media/azure-security-audit-log-management/sec-event4946.png
[14]: ./media/azure-security-audit-log-management/sec-event4946-storage.png
[15]: ./media/azure-security-audit-log-management/sec-event-aggregation.png
[16]: ./media/azure-security-audit-log-management/sec-status-code500.png
[17]: ./media/azure-security-audit-log-management/sec-w3c-format.png
[18]: ./media/azure-security-audit-log-management/sec-blob-iis-logs.png
[19]: ./media/azure-security-audit-log-management/sec-view-blob-container.png
[20]: ./media/azure-security-audit-log-management/sec-hdinsight-analysis.png

<!---HONumber=Oct15_HO3-->