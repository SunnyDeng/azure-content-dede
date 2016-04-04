



VM-Erweiterungen helfen Ihnen bei folgenden Aufgaben:

-   Ändern von Sicherheits- und Identitätsfeatures, z. B. Zurücksetzen von Kontowerten und Verwenden von Antischadsoftware
-   Starten, Beenden oder Konfigurieren der Überwachung und Diagnose
-   Zurücksetzen oder Installieren von Konnektivitätsfeatures, z. B. RDP und SSH
-   Diagnostizieren, Überwachen und Verwalten Ihrer virtuellen Computer

Darüber hinaus gibt es viele andere Features. Neue VM-Erweiterungsfeatures werden regelmäßig veröffentlicht. Dieser Artikel beschreibt die Azure-VM-Agents für Windows und Linux und deren Unterstützung der Funktionalität von VM-Erweiterungen. Eine Liste der VM-Erweiterungen nach Featurekategorie finden Sie unter [Azure-VM-Erweiterungen und Features](virtual-machines-windows-extensions-features.md).

##Azure-VM-Agents für Windows und Linux

Der Azure-Agent für virtuelle Computer (VM-Agent) ist ein sicherer, einfacher Prozess, der VM-Erweiterungen auf Instanzen von virtuellen Azure-Computern im Imagekatalog und auf benutzerdefinierten VM-Instanzen installiert, konfiguriert und davon entfernt, wenn auf ihnen der VM-Agent installiert ist. Der VM-Agent dient als sicherer lokaler Steuerungsdienst für Ihre Azure-VM. Die Erweiterungen, die der Agent lädt, bieten bestimmte Features, um Ihre Produktivität bei der Nutzung der Instanz zu erhöhen.

Es gibt zwei Azure-VM-Agents: einen für virtuelle Windows-Computer und einen für virtuelle Linux-Computer. Standardmäßig wird der VM-Agent automatisch installiert, wenn Sie über den Imagekatalog einen virtuellen Computer erstellen, aber Sie können den VM-Agent auch installieren, nachdem die Instanz erstellt wurde. Darüber hinaus ist die Installation auf einem benutzerdefinierten VM-Image möglich, das Sie dann selbst hochladen.

>[AZURE.IMPORTANT] Diese VM-Agents sind sehr einfache Dienste, die eine sichere Verwaltung von Instanzen virtueller Computer ermöglichen. Es gibt möglicherweise Fälle, in denen Sie den VM-Agent nicht benötigen. Wenn dies der Fall ist, achten Sie darauf, dass Sie virtuelle Computer erstellen, auf denen der VM-Agent nicht installiert wird. Obwohl der VM-Agent physisch entfernt werden kann, ist das Verhalten von VM-Erweiterungen auf der Instanz nicht definiert. Daher wird das Entfernen des VM-Agents nach der Installation derzeit nicht unterstützt.

Der VM-Agent wird in den folgenden Situationen aktiviert:

-   Wenn Sie eine Instanz eines virtuellen Computers mithilfe der **Schnellerfassung** im Verwaltungsportal erstellen oder die Methode **Benutzerdefiniert erstellen** im Verwaltungsportal verwenden und dabei das Kontrollkästchen **VM-Agent installieren** aktiviert ist (wie in der folgenden Abbildung gezeigt). Weitere Informationen finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](virtual-machines-windows-classic-createportal.md).

    ![Kontrollkästchen "VM-Agent"](./media/virtual-machines-common-classic-agents-and-extensions/IC719409.png)

-   Wenn Sie eine Instanz eines virtuellen Computers mit dem Cmdlet [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) oder [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx) erstellen. Sie können einen virtuellen Computer erstellen, ohne dass der VM-Agent installiert wird, indem Sie dem Cmdlet [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) den **–DisableGuestAgent**-Parameter hinzufügen.

-   Durch das manuelle Herunterladen und Installieren des VM-Agents (Windows- oder Linux-Version) auf einer vorhandenen VM-Instanz und das anschließende Festlegen des **ProvisionGuestAgent**-Werts auf **true** mithilfe von PowerShell oder eines REST-Aufrufs. (Wenn Sie diesen Wert nach der manuellen Installation des VM-Agents nicht festlegen, wird das Hinzufügen des VM-Agents nicht richtig erkannt.) Im folgenden Codebeispiel wird veranschaulicht, wie dies mithilfe von PowerShell ausgeführt wird, wobei die Argumente `$svc` und `$name` bereits ermittelt wurden.

        $vm = Get-AzureVM –serviceName $svc –Name $name
        $vm.VM.ProvisionGuestAgent = $TRUE
        Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

-   Durch das Erstellen eines VM-Images, auf dem der VM-Agent installiert ist, bevor es in Azure hochgeladen wird. Laden Sie für einen virtuellen Windows-Computer die [MSI-Datei des Windows-VM-Agents](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) herunter, und installieren Sie den VM-Agent. Für einen virtuellen Linux-Computer installieren Sie ihn aus dem GitHub-Repository unter <https://github.com/Azure/WALinuxAgent>. Weitere Informationen zum Installieren des VM-Agents unter Linux finden Sie im [Benutzerhandbuch für den Azure Linux-VM-Agent](virtual-machines-linux-agent-user-guide.md).

>[AZURE.NOTE]In PaaS heißt der VM-Agent **GuestAgent** und ist auf virtuellen Computern mit der Web- und Workerrolle immer verfügbar. (Weitere Informationen finden Sie unter [Azure Role Architecture](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) (in englischer Sprache).) Der VM-Agent für Rollen-VMs kann jetzt den Clouddienst-VMs auf die gleiche Weise Erweiterungen hinzufügen, wie den dauerhaften virtuellen Computern. Der größte Unterschied zwischen VM-Erweiterungen auf Rollen-VMs und persistenten virtuellen Computern ist, dass bei Rollen-VMs Erweiterungen zuerst dem Clouddienst und dann zu Bereitstellungen innerhalb des Clouddiensts hinzugefügt werden.

>Verwenden Sie das Cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) zum Auflisten aller verfügbaren Rollen-VM-Erweiterungen.

##Suchen, Hinzufügen, Aktualisieren und Entfernen von VM-Erweiterungen  

Weitere Informationen zu diesen Aufgaben finden Sie unter [Hinzufügen, Suchen, Aktualisieren und Entfernen von Azure-VM-Erweiterungen](virtual-machines-windows-classic-manage-extensions.md).

<!---HONumber=AcomDC_0323_2016-->