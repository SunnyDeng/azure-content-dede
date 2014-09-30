<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

**Hinweis**: In diesem Artikel wird die Erstellung eines virtuellen Computers beschrieben, der nicht mit einem virtuellen Netzwerk verbunden ist. Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, damit Sie über den Hostnamen direkte Verbindungen mit den virtuellen Computern herstellen können oder damit Sie
standortübergreifende Verbindungen einrichten können, verwenden Sie stattdessen die Methode **From Gallery**, und legen Sie das virtuelle Netzwerk fest, wenn Sie den virtuellen Computer erstellen. Weitere Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über virtuelle Azure-Netzwerke][].

Führen Sie die folgenden Schritte aus, um einen virtuellen Computer zu erstellen:

1.  Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Verwaltungsportal][] an.

2.  Klicken Sie im Verwaltungsportal unten links auf der Webseite auf **+Neu**, klicken Sie auf **Virtueller Computer** und dann auf **Aus Katalog**.
    ![Neuen virtuellen Computer erstellen][].

3.  Wählen Sie ein Image eines virtuellen Computers mit Windows Server 2008 R2 SP1, und klicken Sie anschließend unten rechts auf der Seite auf den Pfeil für die nächste Seite.

4.  Geben Sie auf der Seite **Konfiguration des virtuellen Computers** die folgenden Informationen ein:

-   Geben Sie den **Namen des virtuellen Computers** ein, z. B. "testwinvm".
-   Geben Sie in das Feld **Name des neuen Benutzers** "Administrator" ein.
-   Geben Sie in das Feld **Neues Kennwort** ein [sicheres Kennwort][] ein.
-   Geben Sie das Kennwort in das Feld **Kennwort bestätigen** nochmals ein.
-   Wählen Sie in der Dropdownliste die geeignete **Größe** aus.

    Klicken Sie auf den Pfeil zum Aufrufen der nächsten Seite, um fortzufahren.

1.  Geben Sie auf der Seite **Virtual machine mode** die folgenden Informationen ein:

-   Wählen Sie **Standalone Virtual Machine**.
-   Geben Sie in das Feld **DNS-Name** eine gültige Unterdomäne im Format **testwinvm.cloudapp.net** ein.
-   Wählen Sie im Feld **Region/Affinity Group/Virtual Network** eine Region aus, in der dieses virtuelle Image gehostet wird.

Klicken Sie auf den Pfeil zum Aufrufen der nächsten Seite, um fortzufahren.

1.  Wählen Sie auf der Seite **Virtual machine options** im Feld **Availability Set** den Eintrag **(none)** aus. Klicken Sie auf das Häkchen, um fortzufahren.

2.  Warten Sie, während Azure den virtuellen Computer vorbereitet.

  [Überblick über virtuelle Azure-Netzwerke]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Neuen virtuellen Computer erstellen]: ./media/create-and-configure-windows-server-2008-vm-in-portal/CreateWinVM.png
  [sicheres Kennwort]: http://msdn.microsoft.com/en-us/library/ms161962.aspx
