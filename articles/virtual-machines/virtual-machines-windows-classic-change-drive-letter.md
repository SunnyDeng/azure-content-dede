<properties
	pageTitle="Einrichten von Laufwerk D eines virtuellen Computers als Datenträger | Microsoft Azure"
	description="Es wird beschrieben, wie Sie Laufwerkbuchstaben für eine Windows-VM ändern, um das Laufwerk D: als Datenlaufwerk zu verwenden."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="cynthn"/>

# Verwenden des Laufwerks D als Datenlaufwerk auf einer Windows-VM 

Wenn Sie für Ihre Anwendung Laufwerk D zum Speichern der Daten verwenden müssen, können Sie diese Anweisungen befolgen, um für den temporären Datenträger einen anderen Laufwerkbuchstaben zu verwenden. Verwenden Sie niemals den temporären Datenträger zum Speichern von Daten, die Sie behalten müssen.

Wenn Sie die Größe eines virtuellen Computers ändern oder diesen **Beenden (Zuordnung aufheben)**, kann dies das Verschieben des virtuellen Computers auf einen neuen Hypervisor auslösen. Ein geplantes oder ungeplantes Wartungsereignis kann ebenfalls diese Neuanordnung auslösen. In diesem Szenario wird dem temporären Datenträger der erste verfügbare Laufwerkbuchstabe zugewiesen. Wenn Sie eine Anwendung nutzen, für die speziell das Laufwerk D: erforderlich ist, müssen Sie diese Schritte ausführen. Sie verschieben die Datei „pagefile.sys“ vorübergehend, fügen einen neuen Datenträger an und weisen ihm den Buchstaben D zu und verschieben die Datei „pagefile.sys“ dann wieder zurück auf das temporäre Laufwerk. Nachdem der Vorgang abgeschlossen ist, wird D: von Azure nicht zurückgenommen, wenn die VM auf einen anderen Hypervisor verschoben wird.

Weitere Informationen zur Verwendung des temporären Datenträgers in Azure finden Sie unter [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) (Grundlegendes zum temporären Laufwerk in Microsoft Azure Virtual Machines).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Anfügen des Datenträgers

Zunächst müssen Sie den Datenträger an den virtuellen Computer anfügen.

- Informationen zur Verwendung des Portals finden Sie unter [Anfügen eines Datenträgers für Daten im Azure-Portal](virtual-machines-windows-attach-disk-portal.md).
- Informationen zur Verwendung des klassischen Portals finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer](virtual-machines-windows-classic-attach-disk.md). 


## Temporäres Verschieben von „pagefile.sys“ zu Laufwerk C

1. Stellen Sie eine Verbindung mit dem virtuellen Computer her. 

2. Klicken Sie mit der rechten Maustaste auf das **Startmenü**, und wählen Sie **System**.

3. Wählen Sie im linken Menü **Erweiterte Systemeinstellungen**.

4. Wählen Sie im Abschnitt **Leistung** die Option **Einstellungen**.

5. Wählen Sie die Registerkarte **Erweitert**.

5. Wählen Sie im Abschnitt **Virtueller Arbeitsspeicher** die Option **Ändern**.

6. Wählen Sie das Laufwerk **C**, und klicken Sie dann auf **Größe wird vom System verwaltet** und **Festlegen**.

7. Wählen Sie das Laufwerk **D**, und klicken Sie dann auf **Keine Auslagerungsdatei** und **Festlegen**.

8. Klicken Sie auf „Übernehmen“. Sie erhalten eine Warnung, dass der Computer neu gestartet werden muss, damit die Änderungen wirksam muss werden.

9. Starten Sie den virtuellen Computer neu.




## Ändern der Laufwerkbuchstaben 

1. Sobald die VM neu gestartet wird, melden Sie sich wieder bei der VM an.

2. Klicken Sie auf das **Startmenü**, geben Sie **diskmgmt.msc** ein, und drücken Sie die EINGABETASTE. Die Datenträgerverwaltung wird gestartet.

3. Klicken Sie mit der rechten Maustaste auf **D**, das temporäre Speicherlaufwerk, und wählen Sie **Laufwerkbuchstaben und -pfade ändern**.

4. Wählen Sie unter dem Laufwerkbuchstaben Laufwerk **G**, und klicken Sie dann auf **OK**.

5. Klicken Sie mit der rechten Maustaste auf den Datenträger, und wählen Sie **Laufwerkbuchstaben und -pfade ändern**.

6. Wählen Sie unter dem Laufwerkbuchstaben Laufwerk **D**, und klicken Sie dann auf **OK**.

7. Klicken Sie mit der rechten Maustaste auf **G**, das temporäre Speicherlaufwerk, und wählen Sie **Laufwerkbuchstaben und -pfade ändern**.

8. Wählen Sie unter dem Laufwerkbuchstaben Laufwerk **E**, und klicken Sie dann auf **OK**.

> [AZURE.NOTE] Wenn Ihr virtueller Computer über andere Datenträger oder Laufwerke verfügt, weisen Sie mit der gleichen Methode die Laufwerkbuchstaben der anderen Datenträger und Laufwerke zu. Sie möchten, dass die Datenträgerkonfiguration wie folgt aussieht:
>- C: Betriebssystemdatenträger  
>- D: Datenträger für Daten  
>- E: Temporärer Datenträger



## Verschieben von „pagefile.sys“ zurück auf das temporäre Speicherlaufwerk 

1. Klicken Sie mit der rechten Maustaste auf das **Startmenü**, und wählen Sie **System**.

2. Wählen Sie im linken Menü **Erweiterte Systemeinstellungen**.

3. Wählen Sie im Abschnitt **Leistung** die Option **Einstellungen**.

4. Wählen Sie die Registerkarte **Erweitert**.

5. Wählen Sie im Abschnitt **Virtueller Arbeitsspeicher** die Option **Ändern**.

6. Wählen Sie das Betriebssystemlaufwerk **C**, und klicken Sie dann auf **Keine Auslagerungsdatei** und **Festlegen**.

7. Wählen Sie das temporäre Speicherlaufwerk **E**, und klicken Sie dann auf **Größe wird vom System verwaltet** und **Festlegen**.

8. Klicken Sie auf **Übernehmen**. Sie erhalten eine Warnung, dass der Computer neu gestartet werden muss, damit die Änderungen wirksam muss werden.

9. Starten Sie den virtuellen Computer neu.




## Nächste Schritte
- Sie können den Speicher erhöhen, der für den virtuellen Computer verfügbar ist, indem Sie [einen zusätzlichen Datenträger anfügen](virtual-machines-windows-attach-disk-portal.md).

<!---HONumber=AcomDC_0323_2016-->