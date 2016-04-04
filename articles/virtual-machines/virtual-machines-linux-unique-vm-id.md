<properties
   pageTitle="Auf eine VM-ID zugreifen"
   description="Beschreibt die Verwendung und den Zugriff auf die eindeutige Azure VM-ID"
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
   
# Verwenden einer eindeutigen Azure VM-ID und darauf zugreifen

Eine eindeutige Azure VM-ID ist ein 128-Bit-Bezeichner, der in dem SMBIOS aller Azure IaaS-VMs codiert und gespeichert ist. Derzeit kann er mithilfe von BIOS-Plattformbefehlen gelesen werden.

Die eindeutige Azure VM-ID ist eine schreibgeschützte Eigenschaft. Die eindeutige Azure VM-ID ändert sich weder beim Neustart/Herunterfahren (entweder geplant oder ungeplant), noch beim Starten/Beenden der Speicherplatzfreigabe, der Dienstreparatur oder der Wiederherstellung vor Ort. Wenn die VM jedoch eine Momentaufnahme ist und zum Erstellen einer neuen Instanz kopiert wird, wird eine neue Azure VM-ID konfiguriert.

> [AZURE.NOTE] Wenn Sie ältere VMs haben, die vor dem Rollout dieser neuen Funktion (18. September 2014 ) erstellt wurden und seitdem in Betrieb sind, starten Sie bitte Ihre VM neu, um automatisch eine eindeutige Azure-ID zu erhalten.


So greifen Sie aus der VM heraus auf die eindeutige Azure VM-ID zu:


## Erstellen einer VM
 

Weitere Informationen finden Sie unter [Create a Virtual Machine](virtual-machines-linux-creation-choices.md) (Erstellen eines virtuellen Computers).


## Herstellen der Verbindung zur VM
 

Weitere Informationen finden Sie unter [SSH von Linux](virtual-machines-linux-ssh-from-linux.md).


## Abfragen einer eindeutigen VM-ID

Befehl (das Beispiel verwendet **Ubuntu**):

    sudo dmidecode | grep UUID
    
Die erwarteten Ergebnisse aus dem Beispiel:

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
Aufgrund der Big-Endian-Byte-Reihenfolge wird die tatsächliche eindeutige VM-ID in diesem Fall die folgende sein:

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
Die eindeutige Azure VM-ID kann in verschiedenen Szenarios verwendet werden (je nachdem, ob die VM in Azure oder lokal ausgeführt wird). Darüber hinaus unterstützt es Ihre Lizenzierung, Ihre Berichterstattung oder Ihre allgemeinen Nachverfolgungsanforderungen, die Sie möglicherweise auf Ihren Azure-IaaS-Bereitstellungen haben. Viele unabhängige Softwareanbieter, die Anwendungen in Azure erstellen und zertifizieren, müssen eine Azure VM möglicherweise während ihres gesamten Lebenszyklus identifizieren und feststellen können, ob die VM in Azure, lokal oder auf einem anderen Cloudanbieter ausgeführt wird. Dieser Plattformbezeichner kann z.B. helfen, herauszufinden, ob die Software ordnungsgemäß lizenziert ist. Außerdem kann er VM-Daten ihrer Quelle zuzuordnen, um so z.B. beim Festlegen der richtigen Metriken für die richtige Plattform zu unterstützen, oder diese Metriken u.a. nachzuverfolgen und zuzuordnen.

<!---HONumber=AcomDC_0323_2016-->