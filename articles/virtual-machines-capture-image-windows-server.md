<properties linkid="manage-windows-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Aufnehmen eines Image eines virtuellen Windows Server-Computers
===============================================================

Sie können Images aus dem Image-Katalog verwenden, um auf einfache Weise virtuelle Computer zu erstellen. Alternativ können Sie eigene Images erfassen und verwenden, um benutzerdefinierte virtuelle Computer zu erstellen. Ein Image stellt die Datei einer virtuellen Festplatte (.vhd) dar, die als Vorlage zum Erstellen eines virtuellen Computers dient. Ein Image ist eine Vorlage, weil es nicht über die spezifischen Einstellungen eines konfigurierten virtueller Computers verfügt. Beispiele für diese Einstellungen sind Computername und Benutzerkontoeinstellungen. Wenn Sie mehrere virtuelle Computer erstellen möchten, die auf die gleiche Weise eingerichtet sind, können Sie das Image eines konfigurierten virtuellen Computers erfassen und dieses als Vorlage verwenden.

1.  Stellen Sie eine Verbindung mit dem virtuellen Computer her. Eine Beschreibung der dafür erforderlichen Schritte finden Sie unter [Anmeldung auf einem virtuellen Windows Server-Computer](http://www.windowsazure.com/en-us/manage/windows/how-to-guides/log-on-a-windows-vm/).

2.  Öffnen Sie ein Eingabeaufforderungsfenster als Administrator.

3.  Wechseln Sie zum Verzeichnis `%windir%\system32\sysprep`, und führen Sie dann „sysprep.exe“ aus.

4.  Das Dialogfeld **Systemvorbereitungstool** wird angezeigt.

    Wählen Sie unter **Systembereinigungsaktion** die Option **Out-of-Box-Experience (OOBE) für System aktivieren**, und stellen Sie sicher, dass **Verallgemeinern** aktiviert ist. Weitere Informationen zur Verwendung von Sysprep finden Sie unter [How to Use Sysprep: An Introduction](http://technet.microsoft.com/en-us/library/bb457073.aspx).

5.  Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren**.

6.  Klicken Sie auf **OK**.

7.  Sysprep fährt den virtuellen Computer herunter. Dadurch wird der Status des Computers im [Verwaltungsportal](http://manage.windowsazure.com) in **Angehalten** geändert.

8.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer aus, den Sie konfigurieren möchten.

9.  Klicken Sie in der Befehlsleiste auf **Aufnehmen**.

    Das Dialogfeld **Ein Image eines virtuellen Computers erstellen** wird angezeigt.

10. Geben Sie unter **Imagename** den Namen für das neue Image ein.

11. Bevor Sie ein Windows Server-Image zu Ihren benutzerdefinierten Images hinzufügen, muss es durch Ausführen von Sysprep, wie in den vorherigen Schritten beschrieben, verallgemeinert werden. Klicken Sie auf **Ich habe Sysprep auf dem virtuellen Computer ausgeführt**, um anzugeben, dass Sie dies durchgeführt haben.

12. Klicken Sie auf das Häkchen, um das Image zu aufzunehmen. Wenn Sie ein Image eines virtuellen Computers aufnehmen, wird der Computer gelöscht.

    Das neue Image steht nun unter **Images** zur Verfügung.

    Wenn Sie einen virtuellen Computer mit der Option **Aus Katalog** erstellen, können Sie das Image verwenden, das Sie aufgenommen haben, indem Sie auf der Seite **Image auswählen** auf **Eigene Images** klicken.


