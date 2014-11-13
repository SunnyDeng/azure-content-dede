<properties urlDisplayName="Capture an image" pageTitle="Erfassen eines Images des virtuellen Computers unter Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Erfahren Sie, wie Sie ein Image eines virtuellen Azure-Computers unter Windows Server 2008 R2 erfassen k&ouml;nnen. " metaCanonical="" services="virtual-machines" documentationCenter="" title="Aufnehmen eines Image eines virtuellen Windows Server-Computers" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Erfassen eines virtuellen Windows-Computers, um ihn als Vorlage zu verwenden

Dieser Artikel erläutert, wie Sie einen virtuellen Windows-Computer erfassen, um ihn als Vorlage zum Erstellen anderer virtueller Computer zu verwenden. Diese Vorlage für virtuelle Computer umfasst den Betriebssystemdatenträger und alle an den virtuellen Computer angefügten Datenträger. Da die Vorlage keine Netzwerkkonfiguration enthält, müssen Sie die Konfiguration später vornehmen, wenn Sie die anderen virtuellen Computer erstellen, die auf dieser Vorlage basieren.

Nachdem Sie den virtuellen Computer erfasst haben, ist er unter **Eigene Images** verfügbar, wenn Sie einen virtuellen Computer erstellen. Die Imagedatei wird als VHD unter Ihrem Speicherkonto gespeichert. Weitere Informationen zu Images finden Sie unter [Verwalten von Datenträgern und Images][Verwalten von Datenträgern und Images].

## Voraussetzungen

Diese Schritte setzen voraus, dass Sie bereits einen virtuellen Azure-Computer erstellt, das Betriebssystem konfiguriert und beliebige Datenträger angefügt haben. Falls dies noch nicht geschehen ist, finden Sie hier Anweisungen:

-   [Erstellen eines benutzerdefinierten virtuellen Computers][Erstellen eines benutzerdefinierten virtuellen Computers]
-   [Anfügen eines Datenträgers an einen virtuellen Computer][Anfügen eines Datenträgers an einen virtuellen Computer]

## Erfassen des virtuellen Computers

1.  Stellen Sie eine Verbindung mit dem virtuellen Computer her, indem Sie auf der Befehlsleiste auf **Verbinden** klicken. Ausführliche Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird].

2.  Öffnen Sie ein Eingabeaufforderungsfenster als ein Administrator.

3.  Wechseln Sie in das Verzeichnis `%windir%\system32\sysprep`, und führen Sie dann "sysprep.exe" aus.

4.  Das Dialogfeld **Systemvorbereitungstool** wird angezeigt. Gehen Sie wie folgt vor:

    -   Wählen Sie unter **Systembereinigungsaktion** die Option **Out-of-Box-Experience (OOBE) für System aktivieren** und stellen Sie sicher, dass **Verallgemeinern** aktiviert ist. Weitere Informationen zur Verwendung von Sysprep finden Sie unter [Verwenden von Sysprep: Einführung][Verwenden von Sysprep: Einführung].

    -   Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren**.

    -   Klicken Sie auf **OK**.

    ![Sysprep ausführen][Sysprep ausführen]

5.  Sysprep fährt den virtuellen Computer herunter. Dadurch wird der Status des virtuellen Computers im [Verwaltungsportal][Verwaltungsportal] in **Angehalten** geändert.

6.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer aus, den Sie konfigurieren möchten.

7.  Klicken Sie in der Befehlsleiste auf **Aufnehmen**.

    ![Virtuellen Computer erfassen][Virtuellen Computer erfassen]

    Das Dialogfeld **Virtuellen Computer erfassen** wird angezeigt.

8.  Geben Sie unter **Imagename** den Namen für das neue Image ein.

9.  Bevor Sie ein Windows Server-Image zu Ihren benutzerdefinierten Images hinzufügen, muss es durch Ausführen von Sysprep, wie in den vorherigen Schritten beschrieben, verallgemeinert werden. Klicken Sie auf **Ich habe Sysprep auf dem virtuellen Computer ausgeführt**, um anzugeben, dass Sie dies durchgeführt haben.

10. Aktivieren Sie das Kontrollkästchen, um das Image zu erfassen. Wenn Sie ein Image eines generalisierten virtuellen Computers erfassen, wird der virtuelle Computer gelöscht.

    Das neue Image steht nun unter **Images** zur Verfügung.

    ![Image-Erfassung erfolgreich][Image-Erfassung erfolgreich]

## Nächste Schritte

Das Image kann jetzt als Vorlage zum Erstellen virtueller Computer verwendet werden. Dazu erstellen Sie mithilfe der Methode **Aus Katalog** einen benutzerdefinierten virtuellen Computer und wählen das gerade erstellte Image aus. Anweisungen hierzu finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers][Erstellen eines benutzerdefinierten virtuellen Computers].

  [Verwalten von Datenträgern und Images]: http://go.microsoft.com/fwlink/p/?LinkId=397536
  [Erstellen eines benutzerdefinierten virtuellen Computers]: ../virtual-machines-create-custom/
  [Anfügen eines Datenträgers an einen virtuellen Computer]: ../storage-windows-attach-disk/
  [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]: http://www.windowsazure.com/de-de/manage/windows/how-to-guides/log-on-a-windows-vm/
  [Verwenden von Sysprep: Einführung]: http://technet.microsoft.com/de-de/library/bb457073.aspx
  [Sysprep ausführen]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
  [Verwaltungsportal]: http://manage.windowsazure.com
  [Virtuellen Computer erfassen]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
  [Image-Erfassung erfolgreich]: ./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png
