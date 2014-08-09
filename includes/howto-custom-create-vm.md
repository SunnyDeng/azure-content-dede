<properties  authors="kathydav" editor="tysonn" manager="jeffreyg" />

# Erstellen eines benutzerdefinierten virtuellen Computers

Wenn Sie einen benutzerdefinierten virtuellen Computer erstellen, stehen Ihnen Optionen zur Auswahl, die bei der Methode **Schnellerfassung** nicht verfügbar sind. Zu diesen Optionen gehört die Verbindung mit einem virtuellen Netzwerk, einem vorhandenen Clouddienst oder einer Verfügbarkeitsgruppe.

Jeder virtuelle Computer ist mit einem Clouddienst verknüpft, entweder direkt oder indirekt über andere virtuelle Computer im selben Clouddienst. Ein allgemeiner Grund zur Platzierung von mehr als einem virtuellen Computer im selben Clouddienst ist der Lastenausgleich für die betreffende Anwendung. Wenn für Ihre Anwendung nur ein virtueller Computer erforderlich ist oder wenn Sie den ersten virtuellen Computer erstellen, erstellen Sie den Clouddienst gleichzeitig mit dem virtuellen Computer. Andernfalls fügen Sie den neuen virtuellen Computer zu einem vorhandenen Clouddienst hinzu.

**Wichtig**: Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, damit Sie sich direkt über den Hostnamen oder über eine lokal eingerichtete Verbindung mit ihm verbinden können, stellen Sie sicher, dass Sie das virtuelle Netzwerk schon dann angeben, wenn Sie den virtuellen Computer erstellen. Ein virtueller Computer kann so konfiguriert werden, dass er nur zu dem Zeitpunkt Teil eines virtuellen Netzwerks werden kann, zu dem er erstellt wird. Weitere Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über virtuelle Azure-Netzwerke][1].

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal][2] an.

2.  Klicken Sie in der Befehlsleiste auf **Neu**.

3.  Klicken Sie auf **Berechnen**, auf **Virtueller Computer** und anschließend auf **From Gallery**.

4.  Wählen Sie unter **Plattform-Images** ein Plattform-Image aus, das Sie verwenden möchten, und klicken Sie anschließend auf den Pfeil, um fortzufahren.

5.  Wenn mehrere Versionen des Images verfügbar sind, wählen Sie unter **Version Release Date** die Version aus, die Sie verwenden möchten.

6.  Geben Sie unter **Name des virtuellen Computers** den Namen ein, der für den virtuellen Computer verwendet werden soll.

7.  Wählen Sie unter **Größe** die Größe für den virtuellen Computer aus. Welche Größe Sie hier angeben, hängt von der Anzahl der Kerne ab, die für Ihre Anwendung benötigt werden.

8.  Geben Sie unter **Neuer Benutzername** einen Namen für das Administratorkonto ein, das zur Verwaltung des Servers verwendet werden soll.

9.  Geben Sie unter **Neues Kennwort** ein nicht zu einfaches Kennwort für das Administratorkonto auf dem virtuellen Computer ein. Geben Sie das Kennwort unter **Kennwort bestätigen** erneut ein.

10. Klicken Sie auf den Pfeil, um fortzufahren.

11. Führen Sie unter **Clouddienst** eine der folgenden Aktionen durch:

* Wenn dies der erste oder der einzige virtuelle Computer in diesem Clouddienst ist, wählen Sie **Create a New Cloud Service**. Geben Sie anschießend unter **Cloud Service DNS Name** einen Namen ein, der aus 3 bis 24 Kleinbuchstaben und Zahlen besteht. Dieser Name wird zu einem Teil des URI, mit dessen Hilfe der virtuelle Computer über den Clouddienst kontaktiert wird.
* Wenn dieser virtuelle Computer zu einem Clouddienst hinzugefügt wird, wählen Sie ihn in der Liste aus.
  
    **Hinweis**: Weitere Informationen über die Platzierung virtueller Computer im selben Clouddienst finden Sie unter [Verbinden virtueller Computer in einem Clouddienst][3].

1.  Wählen Sie unter **Region/Affinitätsgruppe/Virtuelles Netzwerk** die Region, die Affinitätsgruppe oder das virtuelle Netzwerk aus, in der bzw. dem sich der virtuelle Computer befinden soll. Weitere Informationen zu Affinitätsgruppen finden Sie unter [Informationen zu Affinitätsgruppen für virtuelle Netzwerke][4].

2.  Wählen Sie unter **Speicherkonto** ein vorhandenes Speicherkonto für die VHD-Datei, oder verwenden Sie ein automatisch erzeugtes Speicherkonto. Es wird nur ein Speicherkonto pro Region automatisch erstellt. Alle weiteren virtuellen Computer, die Sie mit dieser Einstellung erstellen, befinden sich in diesem Speicherkonto. Es sind maximal 20 Speicherkonten möglich.

3.  Wenn Sie möchten, dass der virtuelle Computer zu einer Verfügbarkeitsgruppe gehört, wählen Sie unter
    **Verfügbarkeitsgruppe** die Option **Verfügbarkeitsgruppe erstellen**, oder fügen Sie den virtuellen Computer zu einer vorhandenen Verfügbarkeitsgruppe hinzu.
    
    **Hinweis**: Virtuelle Computer, die Mitglieder eines Verfügbarkeitsgruppe sind, werden in verschiedenen Fehlerdomänen eingesetzt. Die Platzierung mehrerer virtueller Computer in einer Verfügbarkeitsgruppe sorgt dafür, dass Ihre Anwendung auch bei Netzwerkausfällen, bei Ausfällen der lokalen Hardware und während geplanter Ausfallzeiten verfügbar ist.

4.  Entscheiden sie unter **VM Agent**, ob der VM Agent installiert werden soll. Dieser Agent stellt die Umgebung bereit, in der Sie Erweiterungen installieren können, die Ihnen bei der Interaktion mit dem virtuellen Computer helfen können. Weitere Informationen erhalten Sie unter [Verwenden von Erweiterungen][5].

5.  Prüfen Sie unter **Endpunkte** die neuen Endpunkte, die erstellt werden, um Verbindungen mit dem virtuellen Computer über Remote Desktop oder einen Secure Shell (SSH)-Client zu ermöglichen. Sie können entweder jetzt Endpunkte hinzufügen oder diese später erstellen. Hinweise zur späteren Erstellung von Endpunkten erhalten Sie unter [How to Set Up Communication with a Virtual Machine][6] (Einrichten der Kommunikation mit einem virtuellen Computer, in englischer Sprache).

6.  Klicken Sie auf den Pfeil, um den virtuellen Computer zu erstellen.
    
    ![Benutzerdefinierter virtueller Computer erfolgreich erstellt](./media/howto-custom-create-vm/VMSuccessWindows.png)



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com
[3]: http://www.windowsazure.com/de-de/manage/windows/how-to-guides/connect-to-a-cloud-service/
[4]: http://msdn.microsoft.com/de-de/library/windowsazure/
[5]: http://go.microsoft.com/FWLink/p/?LinkID=390493
[6]: http://www.windowsazure.com/de-de/manage/linux/how-to-guides/setup-endpoints/