<properties linkid="manage-linux-common-tasks-user-root-privileges" urlDisplayName="Use root privileges" pageTitle="Use root privileges on Linux virtual machines in Azure" metaKeywords="" description="Learn how to use root privileges on a Linux virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Using root privileges on Linux virtual machines in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt"></tags>

# Verwenden von Stammberechtigungen auf virtuellen Linux-Computern in Azure

Benutzer können mit dem Befehl `sudo` Befehle mit erweiterten Berechtigungen auf einem virtuellen Linux-Computer ausführen. Das Verhalten kann je nachdem, wie das System bereitgestellt wurde, unterschiedlich sein.

1.  **SSH-Schlüssel und Kennwort oder nur Kennwort**: Der virtuelle Computer wurde entweder mit einem Zertifikat (`.CER`-Datei) und einem Kennwort oder nur einem Benutzernamen und einem Kennwort bereitgestellt. In diesem Fall fordert `sudo` den Benutzer zur Eingabe des Kennworts auf, bevor der Befehl ausgeführt wird.

2.  **Nur SSH-Schlüssel**: Der virtuelle Computer wurde mit einem Zertifikat (`.cer`- oder `.pem`-Datei), jedoch ohne Kennwort bereitgestellt. In diesem Fall fordert `sudo` den Benutzer **nicht** zur Eingabe des Kennworts auf, bevor der Befehl ausgeführt wird.

## SSH-Schlüssel und Kennwort oder nur Kennwort

Melden Sie sich beim virtuellen Linux-Computer mit dem SSH-Schlüssel oder mit dem Kennwort an, und führen Sie dann mit `sudo` Befehle aus. Beispiel:

    # sudo <command>
    [sudo] password for azureuser:

In diesem Fall wird der Benutzer zur Eingabe eines Kennworts aufgefordert. Nachdem das Kennwort eingegeben wurde, führt `sudo` den Befehl mit `root`-Berechtigungen aus.

## Nur SSH-Schlüssel

Melden Sie sich beim virtuellen Linux-Computer mit dem SSH-Schlüssel an, und führen Sie dann mit `sudo` Befehle aus. Beispiel:

    # sudo <command>

In diesem Fall wird der Benutzer **nicht** zur Eingabe eines Kennworts aufgefordert. Nachdem Sie `<enter>` gedrückt haben, wird der Befehl von `sudo` mit `root`-Berechtigungen ausgeführt.

