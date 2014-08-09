<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Verwalten der Verfügbarkeit virtueller Computer

Um die Verfügbarkeit Ihrer Anwendung sicherzustellen, wird dringend empfohlen, für Redundanzzwecke mehrere virtuelle Computer zu verwenden. Um Redundanz bereitzustellen, konfigurieren Sie mindestens einen virtuellen Computer so, dass er die gleiche Funktion oder Rolle ausführt. Diese Redundanz ist erforderlich, um einen garantierten Servicelevel zu erreichen. Sie sollten auch eventuelle Abhängigkeiten berücksichtigen. Wenn der virtuelle Computer "IIS1" beispielsweise von Diensten abhängig ist, die vom virtuellen Computer "SQL1" bereitgestellt werden, wird Redundanz für den virtuellen Computer "SQL1" von "SQL2" bereitgestellt, um Dienstunterbrechungen zu vermeiden. Weitere Informationen zu Vereinbarungen zum Servicelevel finden Sie im Abschnitt "Clouddienste, virtuelle Computer und das virtuelle Netzwerk" unter [Vereinbarungen zum Servicelevel (SLAs)][1].

Durch dieses Vorgehen können Sie sicherstellen, dass Ihre Anwendung auch bei lokalen Netzwerkfehlern, lokalen Festplattenfehlern und geplanten Ausfällen der Plattform verfügbar ist.

Sie können die Verfügbarkeit einer Anwendung verwalten, die mehrere virtuelle Computer verwendet, indem Sie die virtuellen Computer einem Verfügbarkeitssatz hinzufügen. Verfügbarkeitssätze sind direkt mit Ausfalldomänen und Updatedomänen verknüpft. Eine Ausfalldomäne wird in Azure definiert, um einzelne Fehlerquellen zu vermeiden, wie z. B. den Netzwerkswitch oder die Stromversorgungseinheit eines Serverracks. Eine Ausfalldomäne ist nahezu gleichwertig mit einem Rack aus physischen Servern. Wenn mehrere virtuelle Computer in einem Clouddienst verbunden sind, werden die virtuellen Computer durch den Verfügbarkeitssatz in unterschiedliche Ausfalldomänen platziert. Das folgende Diagramm zeigt zwei Verfügbarkeitssätze mit jeweils zwei virtuellen Computern.

![Updatedomänen](./media/manage-vm-availability/UpdateDomains.png)

Azure aktualisiert in regelmäßigen Abständen das Betriebssystem, das die Instanzen einer Anwendung hostet. Ein virtueller Computer wird bei Anwendung eines Updates heruntergefahren. Eine Updatedomäne wird verwendet, um sicherzustellen, dass nicht alle Instanzen virtueller Computer gleichzeitig aktualisiert werden. Wenn Sie einem Verfügbarkeitssatz mehrere virtuelle Computer zuweisen, stellt Azure sicher, dass die Computer unterschiedlichen Updatedomänen zugeordnet werden. Das vorherige Diagramm zeigt zwei virtuelle Computer, auf denen Internet Information Services (IIS) in separaten Updatedomänen ausgeführt werden, sowie zwei virtuelle Computer mit SQL Server ebenfalls in separaten Updatedomänen.

Sie sollten eine Kombination aus Verfügbarkeitssätzen und Endpunkten mit Lastenausgleich verwenden, um sicherzustellen, dass Ihre Anwendung stets verfügbar ist und effizient ausgeführt wird. Weitere Informationen zur Verwendung von Endpunkten mit Lastenausgleich finden Sie unter [Lastenausgleich virtueller Computer](../load-balance-virtual-machines).

Diese Aufgabe umfasst die folgenden Schritte:

* [Schritt 1: Einen virtuellen Computer und einen Verfügbarkeitssatz
  erstellen](#createset)
* [Schritt 2: Einen virtuellen Computer zum Clouddienst hinzufügen und
  ihn beim Erstellungsvorgang dem Verfügbarkeitssatz
  hinzufügen](#addmachine)
* [Schritt 3: (Optional) Einen Verfügbarkeitssatz für zuvor erstellte
  virtuelle Computer erstellen](#previousmachine)
* [Schritt 4: (Optional) Einen zuvor erstellten virtuellen Computer
  einem Verfügbarkeitssatz hinzufügen](#existingset)

## <a id="createset"> </a>Schritt 1: Einen virtuellen Computer und einen Verfügbarkeitssatz erstellen

Um einen Verfügbarkeitssatz zu erstellen, der virtuelle Computer enthält, können Sie den ersten virtuellen Computer und gleichzeitig den Verfügbarkeitssatz erstellen. Dann können Sie dem Verfügbarkeitssatz virtuelle Computer bei deren Erstellung hinzufügen. Sie können auch virtuelle Computer erstellen, einen Verfügbarkeitssatz erstellen und dann alle Computer dem Satz hinzufügen.

**So erstellen Sie einen virtuellen Computer und einen Verfügbarkeitssatz**

1.  Melden Sie sich beim Azure-Verwaltungsportal an, falls noch nicht geschehen.

2.  Klicken Sie in der Befehlsleiste auf **Neu**.
    
    ![Erstellen eines virtuellen Computers](./media/manage-vm-availability/Create.png)

3.  Klicken Sie auf **Virtueller Computer** und dann auf **From Gallery**.
    
    Das Dialogfeld **Select the virtual machine operating system** wird angezeigt.

4.  Wählen Sie unter **Platform Images** ein Image aus, und klicken Sie auf den Pfeil, um fortzufahren.
    
    Das Dialogfeld **Konfiguration des virtuellen Computers** wird angezeigt.

5.  Geben Sie unter **Name des virtuellen Computers** den Namen ein, der für den virtuellen Computer verwendet werden soll.

6.  Geben Sie unter **Neuer Benutzername** einen Namen für das Administratorkonto ein, das zur Verwaltung des Servers verwendet werden soll.

7.  Geben Sie unter **Neues Kennwort** ein sicheres Kennwort für das Administratorkonto ein. Geben Sie unter **Kennwort bestätigen** das zuvor eingegebene Kennwort erneut ein.

8.  Wählen Sie unter **Größe** die Größe für den virtuellen Computer aus. Welche Größe Sie hier angeben, hängt von der Anzahl der Kerne ab, die für Ihre Anwendung benötigt werden.

9.  Klicken Sie auf den Pfeil, um fortzufahren.
    
    Das Dialogfeld **Virtual machine mode** wird angezeigt.

10. Wählen Sie **Stand-alone virtual machine** aus.

11. Geben Sie unter **DNS-Name** einen Namen für den Clouddienst ein, der für den Computer erstellt wird. Der Name kann drei bis 24 Kleinbuchstaben und Zahlen enthalten.

12. Wählen Sie unter **Speicherkonto** ein Speicherkonto zum Speichern der VHD-Datei aus. Sie können auch angeben, dass automatisch ein Speicherkonto erstellt werden soll. Es wird nur ein Speicherkonto pro Region automatisch erstellt. Alle weiteren virtuellen Computer, die Sie mit dieser Einstellung erstellen, befinden sich in diesem Speicherkonto. Es sind maximal 20 Speicherkonten möglich.

13. Wählen Sie unter **Region/Affinity Group/Virtual Network** die Region, die Affinitätsgruppe oder das virtuelle Netzwerk für den virtuellen Computer aus. Weitere Informationen zu Affinitätsgruppen finden Sie unter [Informationen zu Affinitätsgruppen für virtuelle Netzwerke][2].

14. Klicken Sie auf den Pfeil, um fortzufahren.
    
    Das Dialogfeld **Virtual machine options** wird angezeigt.

15. Wählen Sie unter **Verfügbarkeitssatz** die Option **Verfügbarkeitssatz erstellen** aus.

16. Geben Sie unter **Name des Verfügbarkeitssatzes** einen Namen für den Verfügbarkeitssatz ein.

17. Klicken Sie auf den Pfeil, um den virtuellen Computer und den Verfügbarkeitssatz zu erstellen.
    
    Wenn Sie im Dashboard des neuen virtuellen Computers auf **Konfigurieren** klicken, sehen Sie, dass der virtuelle Computer ein Element des neuen Verfügbarkeitssatzes ist.

## <a id="addmachine"> </a>Schritt 2: Einen virtuellen Computer zum Clouddienst hinzufügen und ihn beim Erstellungsvorgang dem Verfügbarkeitssatz hinzufügen

Im vorherigen Schritt wurde gezeigt, wie Sie einen virtuellen Computer und einen Verfügbarkeitssatz zur gleichen Zeit erstellen. Sie können nun einen virtuellen Computer erstellen, ihn mit dem Clouddienst des ersten virtuellen Computers verbinden und ihn dann dem zuvor erstellten Verfügbarkeitssatz hinzufügen.

**So verbinden Sie einen neuen virtuellen Computer und fügen ihn dem Verfügbarkeitssatz hinzu**

1.  Melden Sie sich beim Azure-Verwaltungsportal an, falls noch nicht geschehen.

2.  Klicken Sie in der Befehlsleiste auf **Neu**.
    
    ![Erstellen eines virtuellen Computers](./media/manage-vm-availability/Create.png)

3.  Klicken Sie auf **Virtueller Computer** und dann auf **From Gallery**.
    
    ![Erstellen aus Galerie](./media/manage-vm-availability/CreateNew.png)
    
    Das Dialogfeld **Select the virtual machine operating system** wird angezeigt. Sie können nun ein Image aus der Galerie auswählen.

4.  Klicken Sie auf **Platform Images**, wählen Sie das Plattformimage aus, das Sie verwenden möchten, und klicken Sie dann zum Fortfahren auf den Pfeil.
    
    Das Dialogfeld **Konfiguration des virtuellen Computers** wird angezeigt.

5.  Geben Sie unter **Name des virtuellen Computers** den Namen ein, der für den virtuellen Computer verwendet werden soll.

6.  6\. Geben Sie unter **Neuer Benutzername** einen Namen für das Administratorkonto ein, das zur Verwaltung des Servers verwendet werden soll.

7.  Geben Sie unter **Neues Kennwort** ein sicheres Kennwort für das Administratorkonto auf dem virtuellen Computer ein. Geben Sie das Kennwort unter **Kennwort bestätigen** erneut ein.

8.  Wählen Sie unter **Größe** die Größe für den virtuellen Computer aus. Welche Größe Sie hier angeben, hängt von der Anzahl der Kerne ab, die für Ihre Anwendung benötigt werden.

9.  Für einen virtuellen Computer, auf dem das Betriebssystem Linux ausgeführt wird, können Sie angeben, dass der Computer mit einem SSH-Schlüssel geschützt werden soll.

10. Klicken Sie auf den Pfeil, um fortzufahren.
    
    Das Dialogfeld **Virtual machine mode** wird angezeigt.

11. Wählen Sie **Connect to existing Virtual Machine** aus, um einen neuen virtuellen Computer zu erstellen, der mit dem ersten virtuellen Computer im Verfügbarkeitssatz verbunden wird. Wählen Sie den Clouddienst aus, der den virtuellen Computer im Verfügbarkeitssatz enthält.

12. Wählen Sie unter **Speicherkonto** ein Speicherkonto für die VHD-Datei aus.

13. Wählen Sie unter **Region/Affinity Group/Virtual Network** die Region für den virtuellen Computer aus.

14. Klicken Sie auf den Pfeil, um fortzufahren.
    
    Das Dialogfeld **Virtual machine options** wird angezeigt.

15. Wählen Sie den Verfügbarkeitssatz aus, der bei der Erstellung des ersten virtuellen Computers erstellt wurde.

16. Aktivieren Sie das Kontrollkästchen, um den verbundenen virtuellen Computer zu erstellen und dem Verfügbarkeitssatz hinzuzufügen.
    
    Wenn Sie im Dashboard des neuen virtuellen Computers auf **Konfigurieren** klicken, sehen Sie, dass der virtuelle Computer ein Element des neuen Verfügbarkeitssatzes ist.

## <a id="previousmachine"> </a>Schritt 3: (Optional) Einen Verfügbarkeitssatz für zuvor erstellte virtuelle Computer erstellen

Sie können einen Verfügbarkeitssatz erstellen und ihm einen bereits erstellten virtuellen Computer hinzufügen. Nachdem Sie einen virtuellen Computer erstellt haben, können Sie die Größe des Computers festlegen und angeben, ob er zu einem Verfügbarkeitssatz gehört.

**So erstellen Sie einen neuen Verfügbarkeitssatz**

1.  Melden Sie sich beim Azure-Verwaltungsportal an, falls noch nicht geschehen.

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer aus, den Sie konfigurieren möchten.

3.  Klicken Sie auf **Konfigurieren**.

4.  Wählen Sie im Bereich **Verfügbarkeitssatz** die Option **Verfügbarkeitssatz erstellen** aus, und geben Sie einen Namen für den Satz ein.

5.  Klicken Sie auf **Speichern**.
    
    **Hinweis:** Dies kann dazu führen, dass der virtuelle Computer neu gestartet wird, um die Zugehörigkeit zum Verfügbarkeitssatz wirksam zu machen.

## <a id="existingset"> </a>Schritt 4: (Optional) Einen zuvor erstellten virtuellen Computer einem Verfügbarkeitssatz hinzufügen

Sie können einen vorhandenen virtuellen Computer einem zuvor erstellten Verfügbarkeitssatz hinzufügen. Um einen virtuellen Computer einem Verfügbarkeitssatz hinzuzufügen, muss er mit dem gleichen Clouddienst wie die anderen virtuellen Computer im Satz verbunden sein. Weitere Informationen zur Verbindung virtueller Computer finden Sie unter [Verbinden virtueller Computer in einem Clouddienst](../virtual-machines-connect-cloud-service).

**So fügen Sie einen vorhandenen virtuellen Computer einem Verfügbarkeitssatz hinzu**

1.  Melden Sie sich beim Azure-Verwaltungsportal an, falls noch nicht geschehen.

2.  Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den virtuellen Computer aus, den Sie dem Verfügbarkeitssatz hinzufügen möchten.

3.  Klicken Sie auf **Konfigurieren**.

4.  Wählen Sie im Bereich **Verfügbarkeitssatz** den zuvor erstellten Verfügbarkeitssatz aus.

5.  Klicken Sie auf **Speichern**.
    
    **Hinweis:** Dies kann dazu führen, dass der virtuelle Computer neu gestartet wird, um die Zugehörigkeit zum Verfügbarkeitssatz wirksam zu machen.

<!-- LINKS -->



[1]: http://www.windowsazure.com/de-de/support/legal/sla/
[2]: http://msdn.microsoft.com/library/windowsazure/jj156085.aspx