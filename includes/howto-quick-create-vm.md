<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Schnelles Erstellen eines virtuellen Computers

Verwenden Sie die Methode **Schnellerfassung**, um schnell einen virtuellen Computer im Verwaltungsportal zu erstellen. Wenn Sie diesen Computer erstellen, benötigen Sie nur ein einziges Dialogfeld, um die Konfigurationsdetails festzulegen.

**Hinweis**: In diesem Artikel wird die Erstellung eines virtuellen Computers beschrieben, der nicht mit einem virtuellen Netzwerk verbunden ist. Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, wählen Sie stattdessen die Methode **Aus dem Katalog**, und geben Sie das virtuelle Netzwerk zu dem Zeitpunkt an, zu dem Sie den virtuellen Computer erstellen. Weitere Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über virtuelle Azure-Netzwerke][].

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal][] an.

2.  Klicken Sie in der Befehlsleiste auf **Neu**.

    ![Einen neuen virtuellen Computer erstellen][]

3.  Klicken Sie auf **Virtuelle Computer** und anschließend auf **Schnellerfassung**.

    ![Schnell einen neuen virtuellen Computer erstellen][]

    Das Dialogfeld **Neuen virtuellen Computer erstellen** wird angezeigt.

4.  Geben Sie die folgenden Informationen für den neuen virtuellen Computer ein:

    -   **DNS-Name** - Der Name, der sowohl für den virtuellen Computer, den Sie erstellen, als auch für den Clouddienst verwendet wird, der den virtuellen Computer enthält.
    -   **Image** - Das Plattform-Image, das zur Erstellung des virtuellen Computers verwendet wird.
    -   **Benutzername** - Der Name des Kontos, über das der virtuelle Computer verwaltet werden soll.
    -   **Kontokennwort** - Geben Sie ein nicht zu einfaches Kennwort für das Konto ein, und wiederholen Sie es zur Bestätigung.
    -   **Standort** - Die Region, in der sich der virtuelle Computer befindet.

5.  Aktivieren Sie das Kontrollkästchen, um den virtuellen Computer zu erstellen.

    **Hinweis:** Ein Speicherkonto, das diesen virtuellen Computer enthält, wird automatisch erstellt.

    Der neue Computer wird nun auf der Seite **Virtuelle Computer** aufgelistet.

    ![Virtueller Computer erfolgreich erstellt][]

  [Überblick über virtuelle Azure-Netzwerke]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [Einen neuen virtuellen Computer erstellen]: ./media/howto-quick-create-vm/create.png
  [Schnell einen neuen virtuellen Computer erstellen]: ./media/howto-quick-create-vm/createquick.png
  [Virtueller Computer erfolgreich erstellt]: ./media/howto-quick-create-vm/vmsuccesswindows.png
