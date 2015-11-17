<properties
	pageTitle="Einrichten von Laufwerk D eines virtuellen Computers als Datenträger | Microsoft Azure"
	description="Beschreibt das Ändern von Laufwerkbuchstaben für einen mithilfe des klassischen Bereitstellungsmodells erstellten virtuellen Windows-Computer, sodass Sie Laufwerk D als Datenlaufwerk verwenden können."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="cynthn"/>

# Verwenden des Laufwerks D als Datenlaufwerk auf einer Windows-VM 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Wenn Sie Laufwerk D zum Speichern der Daten verwenden müssen, befolgen Sie diese Anweisungen, um für den temporären Datenträger einen anderen Laufwerkbuchstaben zu verwenden. Verwenden Sie niemals den temporären Datenträger zum Speichern von Daten, die Sie behalten müssen.

## Anfügen des Datenträgers

Zunächst müssen Sie den Datenträger an den virtuellen Computer anfügen. Informationen zum Anfügen eines neuen Datenträgers finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer][Attach].

Wenn Sie einen vorhandenen Datenträger verwenden möchten, stellen Sie sicher, dass Sie auch die virtuelle Festplatte in das Speicherkonto hochgeladen haben. Anweisungen finden Sie in den Schritten 3 und 4 unter [Erstellen und Hochladen einer Windows Server-VHD nach Azure][VHD].


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




## Ändern des Laufwerkbuchstabens 

1. Sobald die VM neu gestartet wird, melden Sie sich wieder bei der VM an.

2. Klicken Sie auf das **Startmenü**, geben Sie **diskmgmt.msc** ein, und drücken Sie die EINGABETASTE. Die Datenträgerverwaltung wird gestartet.

3. Klicken Sie mit der rechten Maustaste auf **D**, das temporäre Speicherlaufwerk, und wählen Sie **Laufwerkbuchstaben und -pfade ändern**.

4. Wählen Sie unter dem Laufwerkbuchstaben Laufwerk **G**, und klicken Sie dann auf **OK**.

5. Klicken Sie mit der rechten Maustaste auf den Datenträger, und wählen Sie **Laufwerkbuchstaben und -pfade ändern**.

6. Wählen Sie unter dem Laufwerkbuchstaben Laufwerk **D**, und klicken Sie dann auf **OK**.

7. Klicken Sie mit der rechten Maustaste auf **G**, das temporäre Speicherlaufwerk, und wählen Sie **Laufwerkbuchstaben und -pfade ändern**.

8. Wählen Sie unter dem Laufwerkbuchstaben Laufwerk **E**, und klicken Sie dann auf **OK**.

> [AZURE.NOTE]Wenn Ihr virtueller Computer über andere Datenträger oder Laufwerke verfügt, weisen Sie mit der gleichen Methode die Laufwerkbuchstaben der anderen Datenträger und Laufwerke zu. Die Datenträgerkonfiguration sollte wie folgt aussehen: – C: Betriebssystemdatenträger – D: Datenträger – E: temporärer Datenträger



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




## Zusätzliche Ressourcen
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Logon]

[Trennen eines Datenträgers von einem virtuellen Windows-Computer][Detach]

[Informationen zu Azure-Speicherkonten][Storage]

<!--Link references-->
[Attach]: storage-windows-attach-disk.md

[VHD]: virtual-machines-create-upload-vhd-windows-server.md

[Logon]: virtual-machines-log-on-windows-server.md

[Detach]: storage-windows-detach-disk.md

[Storage]: ../storage-whatis-account.md

<!---HONumber=Nov15_HO2-->