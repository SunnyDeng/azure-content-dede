<properties 
	pageTitle="Verwenden von Stammberechtigungen auf virtuellen Linux-Computern in Azure" 
	description="Erfahren Sie, wie Sie Stammberechtigungen auf einem virtuellen Linux-Computer in Azure verwenden." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="szark"/>




# Verwenden von Stammberechtigungen auf virtuellen Linux-Computern in Azure

Standardmäßig ist der  `root`-Benutzer auf virtuellen Linux-Computern in Azure deaktiviert. Benutzer können Befehle mit erweiterten Rechten mithilfe des Befehls  `sudo` ausführen. Das Verhalten kann je nachdem, wie das System bereitgestellt wurde, unterschiedlich sein.

1. **SSH-Schlüssel und Kennwort ODER nur Kennwort** - Der virtuelle Computer wurde entweder mit einem Zertifikat (".CER"-Datei) und einem Kennwort oder nur einem Benutzernamen und einem Kennwort bereitgestellt. In diesem Fall fordert  `sudo` den Benutzer zur Eingabe des Kennworts auf, bevor der Befehl ausgeführt wird.

2. **Nur SSH-Schlüssel** - Der virtuelle Computer wurde mit einem Zertifikat (".cer"- oder  `.pem`-Datei) oder SSH-Schlüssel, jedoch ohne Kennwort bereitgestellt.  In diesem Fall fordert  `sudo` den Benutzer **nicht** zur Eingabe des Kennworts auf, bevor der Befehl ausgeführt wird.


## SSH-Schlüssel und Kennwort oder nur Kennwort

Melden Sie sich beim virtuellen Linux-Computer mit der SSH-Schlüssel- oder Kennwortauthentifizierung an, und führen Sie dann mit  `sudo` Befehle aus. Beispiel:

	# sudo <command>
	[sudo] password for azureuser:

In diesem Fall wird der Benutzer zur Eingabe eines Kennworts aufgefordert. Nachdem das Kennwort eingegeben wurde, führt  `sudo` den Befehl mit  `root`-Berechtigungen aus.


## Nur SSH-Schlüssel

Melden Sie sich beim virtuellen Linux-Computer mit dem SSH-Schlüssel an, und führen Sie dann mit  `sudo` Befehle aus. Beispiel:

	# sudo <command>

In diesem Fall wird der Benutzer **nicht** zur Eingabe eines Kennworts aufgefordert. Nachdem Sie `<enter>` gedrückt haben, wird der Befehl von  `sudo` mit  `root`-Berechtigungen ausgeführt.





<!--HONumber=42-->
