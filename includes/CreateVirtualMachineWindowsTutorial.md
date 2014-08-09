
# Erstellen eines virtuellen Windows Server-Computers

Mithilfe der Bildergalerie im Azure Verwaltungsportal erstellen Sie einfach einen virtuellen Windows Server-Computer. In diesem Tutorial erfahren Sie, wie Sie einen virtuellen Windows Server-Computer in der Cloud erstellen, auf den Sie dann zugreifen und den Sie anpassen können. Sie benötigen keine Erfahrung mit Azure, um dieses Tutorial zu verwenden.

Sie erhalten Informationen zu folgenden Themen:

* [Informationen zu virtuellen Computern in Azure](#virtualmachine)
* [Erstellen eines virtuellen Computers](#custommachine)
* [Anmelden bei einem virtuellen Computer nach dessen
  Erstellung](#logon)
* [Anschließen eines Datenträgers an den neuen virtuellen
  Computer](#attachdisk)

<b>Hinweis:</b> In diesem Tutorial wird ein virtueller Computer erstellt,
der nicht mit einem virtuellen Netzwerk verbunden ist. Wenn der virtuelle Computer ein virtuelles Netzwerk verwenden soll, müssen Sie das virtuelle Netzwerk beim Erstellen des virtuellen Computers angeben. Weitere Informationen über virtuelle Netzwerke erhalten Sie unter
[Überblick über virtuelle Azure-Netzwerke][1].

## <a id="virtualmachine"> </a>Informationen zu virtuellen Computern in Azure

Ein virtueller Computer in Azure ist ein Server in der Cloud, den Sie steuern und verwalten können. Nachdem Sie einen virtuellen Computer in Azure erstellt haben, können Sie diesen jederzeit löschen und neu erstellen, und Sie können auf den virtuellen Computer zugreifen, wie auf jeden anderen Server. Weitere Informationen erhalten Sie unter
[Virtuelle Computer][2].

## <a id="custommachine"> </a>Erstellen eines virtuellen Computers

Dieses Tutorial zeigt Ihnen, wie Sie die Methode **Aus Galerie** im Verwaltungsportal verwenden, um einen benutzerdefinierten virtuellen Computer zu erstellen. Diese Methode bietet mehr Optionen zur Konfiguration eines virtuellen Computer zum Zeitpunkt seiner Erstellung als die Methode **Schnellerfassung**.

1.  Melden Sie sich bei Ihrem Abonnement an, um das Azure-[Verwaltungsportal][3] zu verwenden. Wenn Sie nicht über ein Abonnement verfügen, können Sie ein [kostenloses Testkonto][4] erstellen.

2.  Klicken Sie in der Befehlsleiste auf **Neu**.

3.  Klicken Sie auf **Virtueller Computer** und dann auf **From Gallery**.

4.  Klicken Sie unter **Bild auswählen** auf **Windows Server 2012 R2 Datacenter**. (Die verfügbaren Abbilder können sich je nach dem von Ihnen verwendeten Abonnement unterscheiden.) Klicken Sie auf den Pfeil, um fortzufahren.

5.  Wenn mehrere Versionen des Images verfügbar sind, wählen Sie unter **Version Release Date** die Version aus, die Sie verwenden möchten.

6.  Geben Sie unter **Name des virtuellen Computers** den Namen ein, der für den virtuellen Computer verwendet werden soll. Geben Sie für diesen virtuellen Computer **MyTestVM1** ein.

7.  Wählen Sie in **Größe** die Größe des virtuellen Computers aus. Die Größe, die Sie auswählen, hängt von der Anzahl der Kerne ab, die für Ihre Anwendung erforderlich sind. Wählen Sie für diesen virtuellen Computer die kleinste verfügbare Größe.

8.  Geben Sie unter **Neuer Benutzername** einen Namen für das Administratorkonto ein, das zur Verwaltung des Servers verwendet werden soll. Geben Sie für diesen virtuellen Computer **MyTestVM1Admin** ein.

9.  Geben Sie unter **Neues Kennwort** ein sicheres Kennwort für das Administratorkonto auf dem virtuellen Computer ein. Geben Sie das Kennwort unter **Kennwort bestätigen** erneut ein. Klicken Sie auf den Pfeil, um fortzufahren.

10. Sie können virtuelle Computer zusammen in einem Clouddienst platzieren. Aber in diesem Tutorial erstellen Sie nur einen einzigen virtuellen Computer. Wählen Sie hierzu **Neuen Clouddienst erstellen** aus.

11. Geben Sie unter **DNS-Name des Clouddiensts** einen Namen ein, der zwischen drei und 24 Kleinbuchstaben und Ziffern enthält. Dieser Name wird zu einem Teil des URI, mit dessen Hilfe der virtuelle Computer über den Clouddienst kontaktiert wird. Geben Sie für diesen virtuellen Computer **MeinService1** ein.

12. Wählen Sie unter **Region/Affinitätsgruppe/Virtuelles Netzwerk** den Standort des virtuellen Computers aus.

13. Übernehmen Sie die Standardeinstellung **Automatisch generiertes Speicherkonto verwende**, um die VHD zu speichern, die von dem virtuellen Computer verwendet wird.

14. Wählen Sie außerdem unter **Verfügbarkeitsgruppe** für dieses Tutorial die Standardeinstellung **Keine**. Klicken Sie auf den Pfeil, um fortzufahren.

15. Entscheiden sie unter **VM Agent**, ob der VM Agent installiert werden soll. Dieser Agent stellt die Umgebung bereit, in der Sie Erweiterungen installieren können, die Ihnen bei der Interaktion mit dem virtuellen Computer helfen können. Weitere Informationen erhalten Sie unter [Verwenden von Erweiterungen][5].

16. Prüfen Sie unter **Endpunkte** die neuen Endpunkte, die erstellt werden, um Verbindungen zu den virtuellen Computern zu ermöglichen, beispielsweise über Remote Desktop oder Windows PowerShell-Remoting. Sie können entweder jetzt Endpunkte hinzufügen oder diese später erstellen. Hinweise zur späteren Erstellung von Endpunkten erhalten Sie unter [How to Set Up Communication with a Virtual Machine][6](Einrichten der Kommunikation mit einem virtuellen Computer, in englischer Sprache).

17. Aktivieren Sie das Kontrollkästchen, um den virtuellen Computer zu erstellen.
    
    Nachdem der virtuelle Computer und der Clouddienst erstellt wurden, wird der neue virtuelle Computer im Verwaltungsportal unter **Virtuelle Computer** und der Clouddienst unter **Cloud Services** aufgeführt. Sowohl der virtuelle Computer als auch der Clouddienst werden automatisch gestartet.

## <a id="logon"> </a>Anmelden bei einem virtuellen Computer nach dessen Erstellung

Sie können sich bei dem von Ihnen erstellten virtuellen Computer anmelden, um sowohl dessen Einstellungen als auch die darauf ausgeführten Anwendungen zu verwalten.

1.  Melden Sie sich beim Azure [Verwaltungsportal][3] an.

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer **MyTestVM1** aus.

3.  Klicken Sie in der Befehlsleiste auf **Verbinden**.

4.  Klicken Sie auf **Öffnen**, um die Remotedesktopprotokoll-Datei zu verwenden, die automatisch für den virtuellen Computer erstellt wurde.

5.  Klicken Sie auf **Verbinden**.
    
    ![Stellen Sie eine Verbindung
    her.](./media/CreateVirtualMachineWindowsTutorial/connectpublisher.png)

6.  Geben Sie in das Kennwortfeld den Benutzernamen und das Kennwort ein, das Sie beim Erstellen des virtuellen Computers angegeben haben, und klicken Sie dann auf **OK**.

7.  Klicken Sie auf **Ja**, um die Identität des virtuellen Computers zu bestätigen.
    
    ![Bestätigen Sie die Identität des
    Computers.](./media/CreateVirtualMachineWindowsTutorial/connectverify.png)
    
    Sie können jetzt mit dem virtuellen Computer wie mit einem Server in
    Ihrem Büro arbeiten.

## <a id="attachdisk"> </a>Anschließen eines Datenträgers an den neuen virtuellen Computer

Möglicherweise muss Ihre Anwendung Daten speichern. Schließen Sie dazu einen Datenträger an den virtuellen Computer an. Am einfachsten ist es, einen leeren Datenträger an den virtuellen Computer anzuschließen.

1.  Melden Sie sich beim Azure [Verwaltungsportal][3] an.

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer **MyTestVM1** aus.

3.  Klicken Sie in der Befehlsleiste auf **Anschließen**, und klicken Sie dann auf **Leeren Datenträger anschließen**.
    
    Das Dialogfeld **Leeren Datenträger anfügen** wird angezeigt.


   
    > [WACOM.NOTE] Anstelle des Dashboards und der Befehlsleiste wird
    > eventuell die Seite "Schnellstart" angezeigt. Wenn dies passiert,
    > klicken Sie oben auf **Dashboard**.

4.  Der **Name des virtuellen Computers**, der **Speicherort**, der **Dateiname** und die **Host-Cache-Voreinstellungen** sind bereits ausgefüllt. Sie müssen lediglich die für den Datenträger gewünschte Größe eingeben. Geben Sie **5** in das Feld **Größe** ein.
    
    **Hinweis:** Alle Datenträger werden aus einer VHD-Datei im Azure-Speicher erstellt. Sie können einen Namen für die VHD-Datei angeben, die dem Speicher hinzugefügt wird. Der Name des Datenträgers wird jedoch von Azure automatisch erzeugt.

5.  Klicken Sie auf das Häkchen, um den Datenträger an den virtuellen Computer anzufügen.

6.  Klicken Sie auf den Namen des virtuellen Computers. Daraufhin wird das Dashboard angezeigt, und Sie können überprüfen, ob der Datenträger erfolgreich an den virtuellen Computer angeschlossen wurde.
    
    Der virtuelle Computer hat jetzt zwei Datenträger. Der von Ihnen angeschlossene Datenträger wird in der Tabelle **Datenträger** aufgeführt.
    
    ![Schließen Sie einen leeren Datenträger
    an.](./media/CreateVirtualMachineWindowsTutorial/attachemptysuccess.png)
    
    Nach dem Anschließen des Datenträgers an den virtuellen Computer ist der Datenträger offline und nicht initialisiert. Bevor Sie diesen zum Speichern von Daten verwenden können, müssen Sie sich bei dem virtuellen Computer anmelden und den Datenträger initialisieren.

7.  Stellen Sie wie im vorherigen Abschnitt **Anmelden bei einem virtuellen Computer nach dessen Erstellung** beschrieben eine Verbindung zu dem virtuellen Computer her.

8.  Öffnen Sie den **Server-Manager**, nachdem Sie sich auf dem virtuellen Computer angemeldet haben. Erweitern Sie im linken Bereich das Feld **Speicher**, und klicken Sie dann auf **Datenträgerverwaltung**.
    
    ![Initialisieren Sie den Datenträger im
    Server-Manager.](./media/CreateVirtualMachineWindowsTutorial/servermanager.png)

9.  Klicken Sie mit der rechten Maustaste auf **Datenträger 2** und anschließend auf **Datenträger initialisieren**.
    
    ![Starten Sie die
    Initialisierung.](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

10. Klicken Sie auf **OK**, um den Initialisierungsvorgang zu starten.

11. Klicken Sie mit der rechten Maustaste auf den Speicherplatzzuordnungsbereich für Datenträger 2, klicken Sie auf **Neues einfaches Volume**, und beenden Sie anschließend den Assistenten mit den Standardwerten.
    
    ![Erstellen Sie das
    Volume.](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)
    
    Der Datenträger ist nun online und betriebsbereit mit einem neuen Laufwerksbuchstaben.
    
    ![Initialisierung
    erfolgreich](./media/CreateVirtualMachineWindowsTutorial/initializesuccess.png)

## Nächste Schritte

Weitere Informationen zur Konfiguration von virtuellen Windows-Computern auf Azure finden Sie in den folgenden Artikeln:

-[Verbinden virtueller Computer in einem Clouddienst][7]

-[Verwalten der Verfügbarkeit virtueller Computer][8]



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://go.microsoft.com/fwlink/p/?LinkID=271224
[3]: http://manage.windowsazure.com
[4]: http://go.microsoft.com/fwlink/p/?LinkID=23435
[5]: http://go.microsoft.com/FWLink/p/?LinkID=390493
[6]: http://www.windowsazure.com/de-de/manage/linux/how-to-guides/setup-endpoints/
[7]: http://www.windowsazure.com/de-de/documentation/articles/cloud-services-connect-virtual-machine/
[8]: http://www.windowsazure.com/de-de/documentation/articles/manage-availability-virtual-machines/