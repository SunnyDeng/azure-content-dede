<properties
   pageTitle="Aktivieren oder Deaktivieren der Azure VM-Überwachung"
   description="Beschreibt, wie die Azure VM-Überwachung aktiviert oder deaktiviert wird"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="drewm"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# Aktivieren oder Deaktivieren der Azure VM-Überwachung

Dieser Abschnitt beschreibt, wie die Überwachung auf virtuellen Computern, die auf Azure laufen, aktiviert oder deaktiviert wird. Die Überwachung ist auf Azure Virtual Machines standardmäßig aktiviert, wenn sie vom [Azure-Portal](https://portal.azure.com) aus bereitgestellt werden, und Überwachungsgraphen werden standardmäßig mit einem Zeitabstand von einer Minute zur Verfügung gestellt. Sie können die Überwachung aktivieren oder deaktivieren, indem Sie das Portal oder die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows (Azure-CLI) verwenden.

## Aktivieren bzw. Deaktivieren der Überwachung über das Azure-Portal
 
Sie können die Überwachung Ihrer Azure-VM aktivieren. Daten zu Ihrer Instanz werden dann in Zeitabständen von einer Minute bereitgestellt (Speicheränderungen greifen). Detaillierte Diagnosedaten für die VM sind dann in den Portalgraphen oder über die API verfügbar. Standardmäßig ist im Azure-Portal die Überwachung aktiviert, jedoch können Sie sie, wie unten beschrieben, abstellen. Sie können die Überwachung aktivieren, während die VM läuft, oder während sie sich im Zustand „Beendet“ befindet.

- Öffnen Sie das Azure-Portal unter **[https://portal.azure.com](https://portal.azure.com)**.

- Klicken Sie in der linken Navigation auf „Virtuelle Computer“.

- Wählen Sie in der Liste „Virtuelle Computer“ eine laufende oder beendete Instanz aus. Das Blatt „Virtuelle Computer“ öffnet sich.

- Klicken Sie auf „Alle Einstellungen“.

- Klicken Sie auf „Diagnose“.

- Ändern Sie den Status auf „Ein“ oder „Aus“. In diesem Blatt können Sie auch die Ebene der Überwachungsdetails auswählen, die Sie für Ihren virtuellen Computer aktivieren wollen.

[Azure.Note] Standardmäßig ist beim Erstellen eines neuen virtuellen Computers bei „Diagnose“ der Status „Ein“ eingestellt.

![Aktivieren bzw. Deaktivieren der Überwachung über das Azure-Portal][1]


## Aktivierung bzw. Deaktivierung der Überwachung über die Azure-Befehlszeilenschnittstelle
 
So aktivieren Sie die Überwachung für eine Azure-VM.

- Erstellen Sie eine Datei namens PrivateConfig.json mit dem folgenden Inhalt. { „storageAccountName“: „das Speicherkonto, das die Daten erhalten soll“, „storageAccountKey“: „der Schlüssel des Kontos“ }
- Führen Sie den folgenden Azure-CLI-Befehl aus:

        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] Sie können von Version 2.0 zu neueren Versionen wechseln, wenn diese verfügbar werden.

Weitere Informationen zur Konfiguration von Überwachungsmetriken und Beispielen finden Sie im Dokument - **[Using Linux Diagnostic Extension to Monitor Linux VM’s performance and diagnostic data](virtual-machines-linux-diagnostic-extension/) (Verwenden der Linux-Diagnoseerweiterung zum Überwachen der Leistung und Diagnosedaten von Linux-VMs).

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png
 

<!---HONumber=AcomDC_0323_2016-->