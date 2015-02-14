<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#Gewusst wie: Verbinden virtueller Computer in einem Cloud-Dienst

Wenn Sie einen virtuellen Computer erstellen, wird automatisch auch ein Cloud-Dienst erzeugt, der diesen Computer enthält. Sie können innerhalb desselben Cloud-Diensts mehrere virtuelle Computer erstellen, damit die virtuellen Computer untereinander kommunizieren können. 

> [AZURE.NOTE] Wenn sich virtuelle Computer in demselben Cloud-Dienst befinden, können Sie einen Lastenausgleich für sie ausführen und ihre Verfügbarkeit verwalten. Für beide Aufgaben sind zusätzliche Schritte erforderlich. Weitere Informationen finden Sie unter [Lastenausgleich für virtuelle Maschinen](../../articles/load-balance-virtual-machines/) und [Verwalten der Verfügbarkeit virtueller Computer](../../articles/manage-availability-virtual-machines/). 

Zunächst müssen Sie einen virtuellen Computer mit einem neuen Cloud-Dienst erstellen. Dann erstellen Sie weitere virtuelle Computer im gleichen Cloud-Dienst. Dadurch werden die virtuellen Computer miteinander 'verbunden'. 

1. Gehen Sie zum Erstellen des ersten virtuellen Computers so vor wie in [Erstellen eines benutzerdefinierten virtuellen Computers] beschrieben.(../../articles/virtual-machines-create-custom/).

2. Befolgen Sie denselben einfachen Prozess, um die anderen virtuellen Computer zu erstellen. Die Ausnahme besteht darin, dass Sie diese zum Cloud-Dienst hinzufügen, anstatt einen Cloud-Dienst zu erstellen. Wenn Sie z. B. einen Cloud-Dienst namens  *EndpointTest* erstellt haben, wählen Sie diesen Dienst aus. Dies wird in der folgenden Abbildung veranschaulicht:

	![Hinzufügen eines virtuellen Computers zu einem vorhandenen Cloud-Dienst](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

14. Füllen Sie die restlichen Felder auf dieser und der nächsten Seite aus. Klicken Sie dann auf das Häkchen, um den verbundenen virtuellen Computer zu erstellen.

#Ressourcen

Nachdem Sie einen virtuellen Computer erstellt haben, empfiehlt es sich, einen Datenträger hinzuzufügen, damit ein Speicherort für die Daten der Dienste und Arbeitsauslastungen verfügbar ist. Weitere Informationen finden Sie unter einer der folgenden Optionen:

[Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Linux-Computer](http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[Gewusst wie: Anfügen eines Datenträgers an einen Windows-Computer](http://azure.microsoft.com/de-de/documentation/articles/storage-windows-attach-disk/)


<!--HONumber=42-->
