<properties 
    pageTitle="Erstellen von Azure RemoteApp-Images auf Grundlage einer Azure-VM"
    description="Erfahren Sie, wie Sie ein Image für RemoteApp basierend auf einem virtuellen Azure-Computer erstellen." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="elizapo" />



# Erstellen von Azure RemoteApp-Images auf Grundlage eines virtuellen Azure-Computers

Sie können RemoteApp-Images (mit den Apps, die Sie in der Sammlung freigeben) von einem virtuellen Azure-Computer erstellen. Wir haben dem Azure-VM-Imagekatalog außerdem ein Image eines virtuellen Computers hinzugefügt, das allen Anforderungen an RemoteApp-Images entspricht. Sie können dieses VM-Image als Ausgangspunkt für eigene VMs verwenden. Suchen Sie in der Bibliothek einfach nach dem Image "Windows Server Remote Desktop Session Host".

Der Vorgang umfasst zwei Schritte – Erstellen des Images und anschließendes Hochladen aus der Azure-VM-Bibliothek nach Azure RemoteApp.

## Erstellen benutzerdefinierter Images auf Grundlage einer Azure-VM

Führen Sie diese Schritte zum Erstellen eines Images auf Grundlage eines Azure-VM aus.

1. Erstellen Sie einen virtuellen Azure-Computer. Sie können das Image "Windows Server Remote Desktop Session Host" aus dem Imagekatalog für virtuelle Azure-Computer verwenden. Dieses Image erfüllt alle Anforderungen an Azure-RemoteApp-Vorlagenimages. 

	Weitere Informationen finden Sie unter [Erstellen eines virtuellen Windows Server-Computers](virtual-machines-windows-tutorial.md).

2. Stellen Sie eine Verbindung mit dem virtuellen Computer her. Installieren und konfigurieren Sie dann die Apps, die Sie über RemoteApp freigeben möchten. Führen Sie alle weiteren von den Apps benötigten Windows-Konfigurationen aus.

	Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird](virtual-machines-log-on-windows-server.md).

3. Wenn Sie das Image "Windows Server Remote Desktop Session Host" verwenden, können Sie das enthaltene Validierungsskript nutzen, um sicherzustellen, dass Ihr virtueller Computer den Anforderungen an RemoteApp entspricht. Doppelklicken Sie zur Ausführung des Skripts auf dem Desktop auf **ValidateRemoteAppImage**. Stellen Sie sicher, dass alle vom Skript gemeldeten Fehler behoben wurden, bevor Sie mit dem nächsten Schritt fortfahren.

4. Führen Sie Sysprep mit "/generalize" aus, und erfassen Sie das Image. Weitere Informationen hierzu finden Sie unter [Erfassen eines virtuellen Windows-Computers, um ihn als Vorlage zu verwenden](../virtual-machines-capture-image-windows-server.md).

 

## Importieren von Images in die RemoteApp-Imagebibliothek

Führen Sie diese Schritte aus, um das neue Image in RemoteApp zu importieren:

1. Auf der Registerkarte **Vorlagenimages**:
	- Wenn keine Images vorhanden sind, klicken Sie auf **Vorlagenimage hochladen/importieren**. 
	- Wenn mindestens ein Image vorliegt, klicken Sie auf **+**, um ein neues Image hinzuzufügen.

2. Wählen Sie die Option **Importieren Sie ein Image aus Ihrer Bibliothek virtueller Computer** aus, und klicken Sie dann auf **Weiter**.

3. Wählen Sie auf der nächsten Seite das benutzerdefinierte Image aus der Liste aus, und bestätigen Sie, dass Sie die bei der Erstellung des Images aufgeführten Schritte ausgeführt haben. Klicken Sie auf **Weiter**.
4. Geben Sie einen Namen für das neue RemoteApp-Image ein, und wählen Sie den Speicherort aus. Klicken Sie dann auf das Häkchen, um den Importvorgang zu starten.

> [AZURE.NOTE]Sie können Images von jedem Azure-Speicherort, der virtuelle Azure-Computer unterstützt, in jeden Azure-Speicherort importieren, der von Azure-RemoteApp unterstützt wird. Je nach Speicherort kann der Importvorgang bis zu 25 Minuten dauern.

Sie können jetzt Ihre neue Sammlung erstellen – je nach Anforderung entweder eine [Cloud](remoteapp-create-cloud-deployment.md)-Sammlung oder eine [Hybrid](remoteapp-create-hybrid-deployment.md)-Sammlung.
 

<!---HONumber=July15_HO2-->