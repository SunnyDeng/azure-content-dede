
Wenn Sie Schwierigkeiten bei der Problembehandlung für eine Remotedesktopverbindung (Windows-basiert) oder eine SSH-Verbindung (Linux-basiert) zu einem virtuellen Computer von Azure haben, wird Ihnen dieser Artikel helfen, die Probleme alleine zu lösen – ohne den Support in Anspruch zu nehmen, und die Größe des virtuellen Computers anzupassen. Microsoft Azure wird Ihren virtuellen Computer erneut bereitstellen, wenn Sie diese Operation mithilfe von Azure PowerShell aufrufen.

Beachten Sie, dass nach Beenden dieses Vorgangs kurzlebige Datenträgerdaten verloren gehen und dynamische, dem virtuellen Computer zugeordnete IP-Adressen aktualisiert werden.


## Verwenden von Azure PowerShell

Stellen Sie sicher, dass Sie das aktuelle Azure PowerShell 1.x auf Ihrem virtuellen Computer installiert haben. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../articles/powershell-install-configure.md).

Verwenden Sie diesen Azure PowerShell-Befehl, um Ihren virtuellen Computer erneut bereitzustellen:

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


Während dieser Befehl ausgeführt wird, überprüfen Sie Ihren virtuellen Computer im [Azure-Portal](https://portal.azure.com). Beachten Sie, dass der **Status** Ihrer VM sich wie folgt verändert:

1. Der anfängliche **Status** ist *Ausführen*.

	![Den Anfangsstatus erneut bereitstellen](./media/virtual-machines-common-redeploy-to-new-node/statusrunning1.png)

2. Der **Status** ändert sich dann auf *Aktualisieren*.

	![Den Status „Aktualisieren“ erneut bereitstellen](./media/virtual-machines-common-redeploy-to-new-node/statusupdating.png)

3. Der **Status** ändert sich dann auf *Starten*.

	![Den Status „Starten“ erneut bereitstellen](./media/virtual-machines-common-redeploy-to-new-node/statusstarting.png)

4. Der **Status** springt auf *Ausführen* zurück.

	![Den endgültigen Status erneut bereitstellen](./media/virtual-machines-common-redeploy-to-new-node/statusrunning2.png)

Wenn der **Status** wieder auf *Ausführen* steht, wurde der virtuelle Computer erfolgreich wieder bereitgestellt.

<!---HONumber=AcomDC_0309_2016-->