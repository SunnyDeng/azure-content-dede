1.  Melden Sie sich beim Azure [Verwaltungsportal][Verwaltungsportal] an. Testen Sie das [kostenlose Testkonto][kostenlose Testkonto], wenn Sie noch kein Abonnement haben.

2.  Klicken Sie in der Befehlsleiste am unteren Ende des Fensters auf **Neu**.

3.  Unter **Compute** klicken Sie auf **Virtueller Computer** und anschließend auf **From Gallery**.

    ![Navigieren Sie zu From Gallery in der Befehlsleiste][Navigieren Sie zu From Gallery in der Befehlsleiste]

4.  Im ersten Bildschirm können Sie für Ihren virtuellen Computer ein **Image** aus einer Liste in der Image-Galerie auswählen. (Die verfügbaren Abbilder können sich je nach dem von Ihnen verwendeten Abonnement unterscheiden.) Klicken Sie auf den Pfeil, um fortzufahren.

    ![Wählen Sie ein Image aus][Wählen Sie ein Image aus]

5.  Im zweiten Bildschirm wählen Sie einen Computernamen, Größe sowie Benutzername und Passwort des Administrators aus. Wenn Sie den virtuellen Azure-Computer nur ausprobieren möchten, füllen Sie die Felder wie im Bild unten angegeben aus. Andernfalls wählen Sie die benötigte Schicht und die Größe, um Ihre Anwendung oder Ihren Leistungsumfang auszuführen. Nachfolgend finden Sie detaillierte Informationen, die Ihnen beim Ausfüllen helfen:

    -   **Neuer Benutzername** bezieht sich auf das Administratorkonto, dass Sie zur Verwaltung des Servers verwenden. Geben Sie für dieses Konto ein individuelles Passwort an und stellen Sie sicher, dass Sie es nicht vergessen. **Sie brauchen den Benutzernamen und das Passwort, um sich am virtuellen Computer anzumelden**.

    -   Die Größe eines virtuellen Computers wirkt sich auf seine Betriebskosten sowie auf die Konfigurationsoptionen, wie die Anzahl der Datenträger, die Sie anschließen können, aus. Detaillierte Informationen finden Sie unter [Größen virtueller Computer und Clouddienste für Azure][Größen virtueller Computer und Clouddienste für Azure].

    ![Konfigurieren der Eigenschaften des virtuellen Computers][Konfigurieren der Eigenschaften des virtuellen Computers]

6.  Im dritten Bildschirm können Sie Ressourcen für Netzwerkerstellung, Speicherung und Verfügbarkeit konfigurieren. Nachfolgend finden Sie detaillierte Informationen, die Ihnen beim Ausfüllen helfen:

    -   Der **DNS-Name des Clouddienstes** ist der allgemeine DNS-Name, der Teil des URIs wird, das verwendet wird, um den virtuellen Computer zu kontaktieren. Sie müssen einen eigenen Namen für den Clouddienst angeben, weil er in Azure einzigartig sein muss. Clouddienste sind wichtig für Szenarios, die [mehrere virtuelle Computer][mehrere virtuelle Computer] verwenden.

    -   Bei **Region/Affinitätsgruppe/Virtuelles Netzwerk** geben Sie eine Region an, die zu Ihrem Ort passt. Sie können stattdessen auch ein virtuelles Netzwerk angeben.

    > [WACOM.NOTE] Wenn der virtuelle Computer ein virtuelles Netzwerk verwenden soll, **müssen** Sie das virtuelle Netzwerk beim Erstellen des virtuellen Computers angeben. Sie können den virtuellen Computer nicht zu einem virtuellen Netzwerk hinzufügen, nachdem Sie ihn erstellt haben. Weitere Informationen finden Sie unter [Überblick über virtuelle Azure-Netzwerke][Überblick über virtuelle Azure-Netzwerke].

    -   Detaillierte Informationen über die Konfiguration von Endpunkten finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer][Einrichten von Endpunkten für einen virtuellen Computer].

    ![Konfigurieren der verbundenen Ressourcen des virtuellen Computers][Konfigurieren der verbundenen Ressourcen des virtuellen Computers]

7.  Im vierten Konfigurationsbildschirm können Sie den VM-Agent und einige der verfügbaren Erweiterungen konfigurieren. Aktivieren Sie das Kontrollkästchen, um den virtuellen Computer zu erstellen.

    ![Konfiguration des VM-Agents und der Erweiterungen für den virtuellen Computer][Konfiguration des VM-Agents und der Erweiterungen für den virtuellen Computer]

    > [WACOM.NOTE] Dieser VM-Agent stellt die Umgebung bereit, in der Sie Erweiterungen installieren können, die Ihnen bei der Interaktion mit dem virtuellen Computer und seiner Verwaltung helfen können. Weitere Informationen erhalten Sie unter [Verwenden von Erweiterungen][Verwenden von Erweiterungen].

8.  Nach der Erstellung des virtuellen Computers wird der neue virtuelle Computer im Verwaltungsportal unter **Virtuelle Computer** aufgelistet. In diesen Abschnitten werden auch der entsprechende Clouddienst und das Speicherkonto erstellt und aufgelistet. Der virtuelle Computer und der Clouddienst werden automatisch gestartet. Das Verwaltungsportal zeigt ihren Status als **Laufend** an.

    ![Konfiguration des VM-Agents und der Endpunkte des virtuellen Computers][Konfiguration des VM-Agents und der Endpunkte des virtuellen Computers]

  [Verwaltungsportal]: http://manage.windowsazure.com
  [kostenlose Testkonto]: http://www.windowsazure.com/de-de/pricing/free-trial/
  [Navigieren Sie zu From Gallery in der Befehlsleiste]: ./media/virtual-machines-create-WindowsVM/fromgallery.png
  [Wählen Sie ein Image aus]: ./media/virtual-machines-create-WindowsVM/chooseimage.png
  [Größen virtueller Computer und Clouddienste für Azure]: http://go.microsoft.com/fwlink/p/?LinkId=466520
  [Konfigurieren der Eigenschaften des virtuellen Computers]: ./media/virtual-machines-create-WindowsVM/vmconfiguration.png
  [mehrere virtuelle Computer]: http://www.windowsazure.com/de-de/documentation/articles/cloud-services-connect-virtual-machine/
  [Überblick über virtuelle Azure-Netzwerke]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Einrichten von Endpunkten für einen virtuellen Computer]: http://www.windowsazure.com/de-de/documentation/articles/virtual-machines-set-up-endpoints/
  [Konfigurieren der verbundenen Ressourcen des virtuellen Computers]: ./media/virtual-machines-create-WindowsVM/resourceconfiguration.png
  [Konfiguration des VM-Agents und der Erweiterungen für den virtuellen Computer]: ./media/virtual-machines-create-WindowsVM/agent-and-extensions.png
  [Verwenden von Erweiterungen]: http://go.microsoft.com/FWLink/p/?LinkID=390493
  [Konfiguration des VM-Agents und der Endpunkte des virtuellen Computers]: ./media/virtual-machines-create-WindowsVM/vmcreated.png
