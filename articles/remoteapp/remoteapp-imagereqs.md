
<properties 
    pageTitle="Anforderungen an Azure RemoteApp-Images"
    description="Erfahren Sie, welche Anforderungen bei der Erstellung von Images für die Verwendung mit Azure RemoteApp bestehen." 
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
    ms.date="07/06/2015" 
    ms.author="elizapo" />



# Anforderungen für Azure RemoteApp-Images
Azure RemoteApp verwendet ein Windows Server 2012 R2-Image, um alle Programme zu hosten, die Sie an die Benutzer freigeben möchten. Um ein benutzerdefiniertes Image zu erstellen, beginnen Sie mit einem bestehenden Image oder [erstellen ein neues](remoteapp-create-custom-image.md).

> [AZURE.TIP]Wussten Sie, dass Ihnen Ihr Azure RemoteApp-Abonnement Zugriff auf ein Windows Server 2012 R2-Image im Azure-VM-Katalog bietet, mit dem Sie Ihr eigenes Vorlagenimage erstellen können? [Probieren Sie es aus](remoteapp-image-on-azurevm.md).


Das Abbild, das für die Verwendung mit Azure RemoteApp hochgeladen werden soll, muss folgende Anforderungen erfüllen:


- Benutzerdefinierte Anwendungen speichern keine lokalen Daten im Image. Diese Images verfügen über keinen Status und sollten nur Anwendungen enthalten.
- Das Image enthält keine Daten, die verloren gehen können.
- Die Größe des Images sollte ein Vielfaches der Einheit MB betragen. Wenn Sie versuchen, ein Abbild hochzuladen, das kein exaktes Vielfaches ist, treten beim Upload Fehler auf.
- Das Abbild darf nicht größer als 127 GB sein. 
- Es muss sich auf einer VHD-Datei befinden (VHDX-Dateien werden derzeit nicht unterstützt).
- Die VHD darf kein virtueller Computer der 2. Generation sein.
- Die VHD kann eine feste Größe haben oder dynamisch erweiterbar sein. Wir empfehlen eine dynamisch erweiterbare VHD, da das Hochladen dieser in Azure weniger Zeit in Anspruch nimmt.
- Der Datenträger muss mit der Master Boot Record (MBR)-Partitionierung initialisiert werden. Die GPT-Partitionierung (GUID-Partitionstabelle) wird nicht unterstützt. 
- Die VHD muss eine einzige Installation von Windows Server 2012 R2 enthalten. Sie kann mehrere Volumes enthalten, jedoch nur eines mit einer Windows-Installation. 
- Die RDSH-Rolle (Remote Desktop Session Host) und das Desktopdarstellung-Feature müssen installiert sein.
- Die Remotedesktop-Verbindungsbroker-Rolle darf *nicht* installiert sein.
- Encrypting File System (EFS) muss deaktiviert sein.
- Das Abbild muss mit SYSPREP unter Verwendung der Parameter **/oobe /generalize /shutdown** vorbereitet werden. Verwenden Sie nicht den Parameter **/mode:vm**.
- VHD-Uploads aus einer Momentaufnahmenkette werden nicht unterstützt.
 

<!---HONumber=August15_HO6-->