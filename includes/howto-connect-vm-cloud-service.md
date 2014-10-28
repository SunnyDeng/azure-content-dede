<properties authors="kathydav" editor="tysonn" manager="donaldg" />

# Verbinden virtueller Computer in einem Clouddienst

Wenn Sie einen virtuellen Computer erstellen, wird automatisch auch ein Clouddienst erzeugt, der diesen Computer enthält. Sie können mehrere virtuelle Computer im selben Clouddienst erstellen, um diese virtuellen Computer miteinander kommunizieren zu lassen und auf diese Weise die Last zwischen ihnen zu verteilen, sodass stets eine hohe Verfügbarkeit der Computer gegeben ist.

Weitere Informationen zum Lastenausgleich zwischen virtuellen Computern finden Sie unter [Lastenausgleich zwischen virtuellen Computern][Lastenausgleich zwischen virtuellen Computern]. Weitere Information zur Verwaltung der Verfügbarkeit Ihrer Anwendung finden Sie unter [Verwaltung der Verfügbarkeit virtueller Computer][Verwaltung der Verfügbarkeit virtueller Computer].

Zuerst müssen Sie einen virtuellen Computer mit einem neuen Clouddienst erstellen. Anschließend können Sie unter diesem Clouddienst zusätzliche virtuelle Computer mit dem ersten virtuellen Computer verbinden.

1.  Gehen Sie zum Erstellen eines virtuellen Computers so vor wie in [Erstellen eines benutzerdefinierten virtuellen Computers][Erstellen eines benutzerdefinierten virtuellen Computers] beschrieben.

2.  Klicken Sie nach dem Erstellen des ersten benutzerdefinierten virtuellen Computers in der Befehlsleiste [Verwaltungsportal][Verwaltungsportal] auf **Neu**.

    ![Neuen virtuellen Computer erstellen][Neuen virtuellen Computer erstellen]

3.  Klicken Sie auf **Virtueller Computer** und dann auf **From Gallery**.

    ![Einen benutzerdefinierten virtuellen Computer erstellen][Einen benutzerdefinierten virtuellen Computer erstellen]

    Das Dialogfeld **Select the virtual machine operating system** wird angezeigt.

4.  Wählen Sie auf der Seite **Bild auswählen** ein Bild aus, und klicken Sie anschließend auf den Pfeil, um fortzufahren.

    Die erste Seite des Menüs **Konfiguration des virtuellen Computers** wird angezeigt.

5.  Geben Sie unter **Name des virtuellen Computers** den Namen ein, der für den virtuellen Computer verwendet werden soll.

6.  Wählen Sie unter **Größe** die Größe für den virtuellen Computer aus. Welche Größe Sie hier angeben, hängt von der Anzahl der Kerne ab, die für Ihre Anwendung benötigt werden.

7.  Geben Sie unter **Neuer Benutzername** einen Namen für das Administratorkonto ein, das zur Verwaltung des Servers verwendet werden soll.

8.  Geben Sie unter **Neues Kennwort** ein sicheres Kennwort für das Administratorkonto ein. Geben Sie das Kennwort unter **Kennwort bestätigen** erneut ein.

9.  Für einen virtuellen Computer, auf dem das Betriebssystem Linux ausgeführt wird, können Sie angeben, dass der Computer mit einem SSH-Schlüssel geschützt werden soll.

10. Wählen Sie unter **Clouddienst** den Clouddienst aus, der den neuen virtuelle Computer enthält.

11. Wählen Sie unter **Speicherkonto** ein Speicherkonto aus, in dem die VHD-Datei gespeichert werden soll, oder behalten Sie den Standardwert in diesem Feld bei, sodass das Speicherkonto automatisch erstellt wird. Es wird nur ein Speicherkonto automatisch erstellt. Alle weiteren virtuellen Computer, die Sie mit dieser Einstellung erstellen, befinden sich in diesem Speicherkonto. Es sind maximal 20 Speicherkonten möglich.

12. Wenn Sie eine Verfügbarkeitsgruppe verwenden möchten, wählen Sie diejenige aus, die bei der Erstellung des ersten virtuellen Computers angelegt wurde.

13. Prüfen Sie die Standard-Endpunktkonfiguration, und ändern Sie sie bei Bedarf.

14. Aktivieren Sie das Kontrollkästchen, um den verbundene virtuellen Computer zu erstellen.

  [Lastenausgleich zwischen virtuellen Computern]: ../../articles/load-balance-virtual-machines/
  [Verwaltung der Verfügbarkeit virtueller Computer]: ../../articles/manage-availability-virtual-machines/
  [Erstellen eines benutzerdefinierten virtuellen Computers]: ../../articles/virtual-machines-create-custom/
  [Verwaltungsportal]: http://manage.windowsazure.com
  [Neuen virtuellen Computer erstellen]: ./media/howto-connect-vm-cloud-service/Create.png
  [Einen benutzerdefinierten virtuellen Computer erstellen]: ./media/howto-connect-vm-cloud-service/CreateNew.png
