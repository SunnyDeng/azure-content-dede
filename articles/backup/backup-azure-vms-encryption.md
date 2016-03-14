<properties
   pageTitle="Azure Backup – Sicherung von Azure IaaS-VMs mit verschlüsselten Datenträgern | Microsoft Azure"
   description="Erfahren Sie, wie Azure Backup Daten behandelt, die während der IaaS-VM-Sicherung mit BitLocker oder dmcrypt verschlüsselt wurden. Dieser Artikel behandelt die Unterschiede bei der Sicherung und Wiederherstellung, wenn verschlüsselte Datenträger verwendet werden."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="11/27/2015"
   ms.author="markgal; jimpark"/>

# Behandeln verschlüsselter Festplatten während der Sicherung virtueller Computer

Unternehmen, die ihre VM-Daten in Azure verschlüsseln möchten, können unter Windows Bitlocker und auf Linux-Computern dmcrypt verwenden. Bei beiden handelt es sich um Verschlüsselungslösungen auf Volumeebene. Dieser Artikel befasst sich mit den Besonderheiten der Einrichtung der Sicherung für solche Azure-VMs und die Auswirkung auf die Wiederherstellungs-Workflows aufgrund der verschlüsselten Daten.

## Funktionsweise der Sicherung

Die gesamte Lösung besteht aus zwei Schichten – der VM-Schicht und der Speicherschicht.

1. Die VM-Schicht behandelt die vom Gastbetriebssystem sichtbaren Daten und die auf dem virtuellen Computer ausgeführten Anwendungen. In dieser Schicht wird auch die Verschlüsselungssoftware (Bitlocker oder dmcrypt) ausgeführt, mit der die Daten im Hintergrund auf den Volumes verschlüsselt werden, bevor sie auf den Datenträger geschrieben werden.
2. Die Speicherschicht befasst sich mit den Seitenblobs und den mit dem virtuellen Computer verbundenen Datenträgern. Sie hat keine Kenntnisse von den Daten, die auf den Datenträger geschrieben werden, oder darüber, ob diese verschlüsselt sind. In dieser Schicht arbeitet die Sicherungsfunktion für virtuelle Computer.

![Gemeinsamer Einsatz von Bitlocker-Verschlüsselung und Azure-VM-Sicherung](./media/backup-azure-vms-encryption/how-it-works.png)

Die gesamte Verschlüsselung der Daten erfolgt im Hintergrund und nahtlos in der VM-Schicht. Daher werden die Daten verschlüsselt, die in die an die virtuellen Computer angefügten Seitenblobs geschrieben werden. Wenn [Azure Backup eine Momentaufnahme der Datenträger des virtuellen Computers erstellt und Daten überträgt](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines), werden die verschlüsselten Daten aus den Seitenblobs kopiert.

## Lösungskomponenten

Es gibt viele Aspekte dieser Lösung, die konfiguriert und verwaltet werden müssen, damit dies ordnungsgemäß funktioniert:

| Funktion | Verwendete Software | Zusätzliche Hinweise |
| -------- | ------------- | ------- |
| Verschlüsselung | BitLocker oder dmcrypt | Da die Verschlüsselung in einer *anderen* Schicht als bei Azure Backup erfolgt, ist es egal, welche Verschlüsselungssoftware verwendet wird. Der Vorgang wurde jedoch nur mit Bitlocker und dmcrypt überprüft.<br><br> Um die Daten zu verschlüsseln, ist ein Schlüssel erforderlich. Außerdem muss der Schlüssel sicher aufbewahrt werden, damit nur autorisierter Zugriff auf die Daten möglich ist. |
| Schlüsselverwaltung | CloudLink SecureVM<br>oder Azure-Schlüsseltresor | Der Schlüssel ist wichtig zum Verschlüsseln und Entschlüsseln der Daten. Ohne den richtigen Schlüssel können die Daten nicht abgerufen werden. Dies wird *sehr* wichtig durch:<br><li>Schlüsselrollover<li>Langfristig Aufbewahrung<br><br>Es ist z. B. möglich, dass der Schlüssel, der zum Sichern der Daten vor sieben Jahren verwendet wurde, nicht derselbe Schlüssel ist, der heute verwendet wird. Ohne den Schlüssel von vor sieben Jahren ist es jedoch unmöglich, wiederhergestellte Daten aus dieser Zeit zu verwenden.|
| Datensicherung | Azure Backup | Verwenden von Azure Backup zum Sichern der Azure IaaS-VMs mit dem [Azure-Verwaltungsportal](http://manage.windowsazure.com) oder mithilfe von PowerShell |
| Datenwiederherstellung | Azure Backup | Verwenden Sie Azure Backup zum Wiederherstellen von Datenträgern oder vollständigen virtuellen Computern von einem Wiederherstellungspunkt. Die Daten werden von Azure Backup nicht während des Wiederherstellungsvorgangs entschlüsselt.|
| Entschlüsselung | BitLocker oder dmcrypt | Um Daten von einem wiederhergestellten Datenträger oder einem wiederhergestellten virtuellen Computer zu lesen, benötigt die Software den Schlüssel aus der Schlüsselverwaltungssoftware. Ohne den richtigen Schlüssel können die Daten nicht entschlüsselt werden. |

> [AZURE.IMPORTANT]  Die Schlüsselverwaltung – einschließlich von Schlüsselrollovern – ist nicht Aufgabe von Azure Backup. Dieser Aspekt ist sehr wichtig für den gesamten Sicherungs-/Wiederherstellungsvorgang, er muss jedoch separat verwaltet werden.

## CloudLink SecureVM

[CloudLink SecureVM](http://www.cloudlinktech.com/choose-your-cloud/microsoft-azure/) ist eine Lösung für die VM-Verschlüsselung, die zum Sichern der Azure-IaaS-VM-Daten verwendet werden kann. CloudLink SecureVM wird für die Verwendung mit Azure Backup unterstützt.

### Supportinformationen

- CloudLink SecureVM, Version 4.0 (Build 21536.121 oder höher)
- Azure PowerShell, Version 0.9.8 oder höher

### Schlüsselverwaltung

Wenn Sie für virtuelle Computer mit Sicherungen einen Rollover der Schlüssel durchführen oder diese ändern möchten,benötigen Sie unbedingt die zum Zeitpunkt der Sicherung verwendeten Schlüssel. Deshalb wird es z. B. empfohlen, eine Sicherung des Schlüsselspeichers oder des kompletten SecureVM-Systems zu erstellen.

### Dokumentation und Ressourcen

- [Deployment guide – PDF (in englischer Sprache)](http://www.cloudlinktech.com/Azure/CL_SecureVM_4_0_DG_EMC_Azure_R2.pdf)
- [Deploying and using SecureVM – Video (in englischer Sprache)](https://www.youtube.com/watch?v=8AIRe92UDNg)

<!---HONumber=AcomDC_0302_2016-->