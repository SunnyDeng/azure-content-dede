| Compute-Optionen | Audience (Zielgruppe) |
| ------------------ | --------   |
| [App Service](../app-service-web-overview.md) | Skalierbare Web-Apps, Mobile Apps, API-Apps und Logik-Apps für beliebige Geräte |
| [Cloud-Dienste](services/cloud-services/) | Hoch verfügbare, skalierbare, n-schichtige Cloud-Apps mit mehr Kontrolle über das Betriebssystem |
| [Virtuelle Computer](https://msdn.microsoft.com/library/azure/jj156143.aspx) | Angepasste Windows- und Linux-VMs mit vollständiger Kontrolle des Betriebssystems |

<a name="tellmevm"></a>
## Weitere Informationen über virtuelle Computer

Mithilfe von Azure Virtual Machines können Sie virtuelle Computer in der Cloud erstellen und verwenden. Aufgrund der Bereitstellung von *IaaS (Infrastructure as a Service)* kann die VM-Technologie auf unterschiedliche Arten genutzt werden. Hier einige Beispiele:

- **Virtuelle Computer(VMs) für Entwicklung und Test.** Entwicklungsgruppen verwenden im Allgemeinen VMs, da sie eine schnelle und einfache Möglichkeit zum Erstellen eines Computer mit speziellen Konfigurationen bieten, die zum Kodieren und Testen einer Anwendung erforderlich sind. Virtuelle Azure-Computer bieten einen einfachen und wirtschaftlichen Weg, um diese VMs zu erstellen, zu verwenden und anschließend wieder zu entfernen, wenn sie nicht mehr benötigt werden.
- **Ausführen von Anwendungen in der Cloud.** Für einige Anwendungen ist es aus wirtschaftlichen Gesichtspunkten sinnvoll, diese in der öffentlichen Cloud auszuführen. Ein Beispiel ist eine Anwendung mit großen Bedarfsspitzen. Sie könnten natürlich Ihr eigenes Rechenzentrum mit entsprechender Hardware ausstatten, um den Spitzenbedarf zu bewältigen. Jedoch wäre diese Hardware wohl die meiste Zeit nicht ausgelastet. Wenn Sie diese Anwendung stattdessen auf Azure ausführen, bezahlen Sie für zusätzliche virtuelle Computer nur, wenn Sie diese benötigen, und können sie herunterfahren, wenn sie nicht benötigt werden. Ein weiteres Beispiel sind Startup-Unternehmen, die kurzfristig und unverbindlich On-Demand-Ressourcen benötigen. Auch in diesem Fall ist Azure die richtige Wahl.
- **Erweitern Ihres eigenen Datencenters in die öffentliche Cloud.** Mit Azure Virtual Network können Sie ein virtuelles Netzwerk (VNET) für Ihr Unternehmen erstellen, das eine Erweiterung Ihres eigenen lokalen Netzwerks darstellt, und VMs zu diesem VNET hinzufügen. Dies ermöglicht die Ausführung von Anwendungen wie z. B. [SharePoint](virtual-machines-sharepoint-infrastructure-services.md), [SQL Server](virtual-machines-sql-server-infrastructure-services.md) und anderen auf Azure VM. Dieser Ansatz ist möglicherweise einfacher umzusetzen oder kostengünstiger als deren Ausführung auf virtuellen Computern in Ihrem eigenen Datencenter.   
- **Notfallwiederherstellung.** Anstatt fortlaufend für ein selten genutztes Sicherungsdatencenter zu bezahlen, bezahlen Sie mit IaaS-basierter Notfallwiederherstellung nur für die tatsächlich genutzten Ressourcen, wenn Sie diese wirklich benötigen. Wenn Ihr primäres Datencenter ausfällt, können Sie virtuelle Azure-Computer für die Ausführung wichtiger Anwendungen erstellen und anschließend abschalten, wenn diese nicht mehr benötigt werden.

Wie andere virtuelle Computer verfügt eine VM in Azure über ein Betriebssystem sowie Speicher- und Netzwerkfunktionen und kann ein breites Spektrum an Anwendungen ausführen. Sie können ein von Azure oder einem seiner Partner bereitgestelltes Image oder ein eigenes verwenden. Erhältlich sind verschiedene Versionen, Editionen und Konfigurationen von:
 
-	Windows Server 
-	Linux-Server wie z. B. Suse, Ubuntu und CentOS
-	SQL Server
-	BizTalk Server 
-	SharePoint Server

Virtuelle Computer verwenden virtuelle Festplatten (VHDs), um ihr Betriebssystem (OS) und die Daten zu speichern. VHDs werden auch für die Images verwendet, die Sie auswählen können, um ein Betriebssystem zu installieren. Die folgende Abbildung stellt dies dar sowie zwei Tools zum Erstellen und Verwalten Ihrer VMs.

<a name="fig_createvms"></a> ![vm_diagram](./media/virtual-machines-choose-me-content/diagram.png)

**Abbildung: Azure Virtual Machines bietet IaaS (Infrastructure as a Service).**

VMs können über ein browserbasiertes Portal, über Befehlszeilentools mit Unterstützung für die Skripterstellung oder direkt über die REST-API verwaltet werden. Microsoft-Partner wie RightScale und ScaleXtreme bieten ebenfalls Verwaltungsdienste auf Basis der REST-API an.

Neben dem Betriebssystem sind die folgenden weiteren Konfigurationsoptionen für VMs verfügbar:

- Die Größe, welche Faktoren bestimmt, wie z. B. die Anzahl der Datenträger, die angefügt werden können, und die Verarbeitungsleistung. Azure bietet eine Vielzahl von Größen zur Unterstützung vieler Anwendungstypen. Ausführliche Informationen finden Sie unter [Größen für virtuelle Computer](virtual-machines-size-specs.md).  
- Der Azure-Region, in der Ihre neue VM gehostet wird, z. B. in den USA, in Europa oder in Asien. 
- VM-Erweiterungen, durch die Ihr virtueller Computer zusätzliche Funktionen erhält, wie das Ausführen von Anti-Virus-Software oder die Verwendung der Windows PowerShell-Funktion zum Konfigurieren des gewünschten Zustands.

Zu den weiteren Vorteilen von VMs gehören:

**Nutzungsbasierte Zahlung** – Bei Azure wird ein Stundenpreis basierend auf Größe und Betriebssystem der VM berechnet. Angefangene Stunden werden nur nach den Minuten der Nutzung abgerechnet. Speicherplatz wird separat bewertet und in Rechnung gestellt. Ausführliche Informationen finden Sie unter [Virtuelle Computer – Preisdetails](https://azure.microsoft.com/pricing/details/virtual-machines/).

**Resilienz** – Azure überwacht die physische Hardware, die einen virtuellen Computer hostet. Wenn ein physischer Server, auf dem eine VM ausgeführt wird, ausfällt, erkennt Azure dies, verschiebt die VM auf neue Hardware und startet sie erneut. Dieser Vorgang wird manchmal als Dienstreparatur bezeichnet. Azure schützt auch die Daten eines virtuellen Computers durch redundante Kopien der virtuellen Festplatten im Blob-Speicher.

<!---HONumber=AcomDC_0128_2016-->