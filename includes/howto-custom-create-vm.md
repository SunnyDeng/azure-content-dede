
#Erstellen eines benutzerdefinierten virtuellen Computers

Ein *benutzerdefinierter* virtueller Computer ist ein virtueller Computer, den Sie mit der Option **Aus Katalog** erstellen, da Sie Ihnen durch diese Option mehr Konfigurationsoptionen zur Verfügung stehen als durch die Methode **Schnellerfassung**. Die Optionen umfassen:

- Mehr Imageoptionen zum Erstellen des virtuellen Computers (VM)
- Verbinden des virtuellen Computers mit einem virtuellen Netzwerk 
- Hinzufügen des virtuellen Computers zu einem vorhandenen Cloud-Dienst 
- Hinzufügen des virtuellen Computers zu einer Verfügbarkeitsgruppe

**Wichtig**: Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, damit Sie sich direkt über den Hostnamen oder über eine lokal eingerichtete Verbindung mit ihm verbinden können, stellen Sie sicher, dass Sie das virtuelle Netzwerk schon dann angeben, wenn Sie den virtuellen Computer erstellen. Ein virtueller Computer kann so konfiguriert werden, dass er nur zu dem Zeitpunkt Teil eines virtuellen Netzwerks werden kann, zu dem er erstellt wird. Weitere Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über virtuelle Azure-Netzwerke](http://go.microsoft.com/fwlink/p/?LinkID=294063).

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2. Klicken Sie in der Befehlsleiste auf **Neu**.

3. Klicken Sie auf **Berechnen**, auf **Virtueller Computer** und anschließend auf **From Gallery**.

4. Wählen Sie das Image aus, das Sie verwenden möchten, und klicken Sie dann auf den Pfeil, um den Vorgang fortzusetzen.

5. Wenn mehrere Versionen des Images verfügbar sind, wählen Sie unter **Version Release Date** die Version aus, die Sie verwenden möchten.

6. Geben Sie unter **Name des virtuellen Computers** den Namen ein, der für den virtuellen Computer verwendet werden soll.

7. Wählen Sie unter **Ebene** und **Größe** die geeignete Größe für den virtuellen Computer aus. Die ausgewählte Größe wirkt sich auf die maximale Konfiguration des virtuellen Computer sowie auf die Kosten aus. Ausführliche Informationen zur Konfiguration finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Azure](http://go.microsoft.com/fwlink/p/?LinkID=389844).

8. Geben Sie unter **Neuer Benutzername** einen Namen für das Administratorkonto ein, das zur Verwaltung des Servers verwendet werden soll.

9. Geben Sie unter **Neues Kennwort** ein sicheres Kennwort für das Administratorkonto ein. Geben Sie das Kennwort unter **Kennwort bestätigen** erneut ein.

10. Klicken Sie auf den Pfeil, um fortzufahren.

11. Führen Sie unter **Clouddienst** eine der folgenden Aktionen durch:
	
	- Wenn dies der erste oder der einzige virtuelle Computer in diesem Clouddienst ist, wählen Sie **Create a New Cloud Service**. Geben Sie anschießend unter **Cloud Service DNS Name** einen Namen ein, der aus 3 bis 24 Kleinbuchstaben und Zahlen besteht. Dieser Name wird zu einem Teil des URI, mit dessen Hilfe der virtuelle Computer über den Clouddienst kontaktiert wird.
	- Wenn dieser virtuelle Computer zu einem Clouddienst hinzugefügt wird, wählen Sie ihn in der Liste aus.

	**Hinweis**: Weitere Informationen über die Platzierung virtueller Computer im selben Clouddienst finden Sie unter [Verbinden virtueller Computer in einem Clouddienst](http://www.windowsazure.com/manage/windows/how-to-guides/connect-to-a-cloud-service/).

12. Wählen Sie unter **Region/Affinitätsgruppe/Virtuelles Netzwerk** die Region, die Affinitätsgruppe oder das virtuelle Netzwerk aus, in der bzw. dem sich der virtuelle Computer befinden soll. Weitere Informationen zu Affinitätsgruppen finden Sie unter [Informationen zu Affinitätsgruppen für virtuelle Netzwerke](http://msdn.microsoft.com/library/azure/jj156085.aspx).

13. Wählen Sie unter **Speicherkonto** ein vorhandenes Speicherkonto für die VHD-Datei, oder verwenden Sie ein automatisch erzeugtes Speicherkonto. Es wird nur ein Speicherkonto pro Region automatisch erstellt. Alle weiteren virtuellen Computer, die Sie mit dieser Einstellung erstellen, befinden sich in diesem Speicherkonto. Es sind maximal 20 Speicherkonten möglich.

14. Wenn Sie möchten, dass der virtuelle Computer zu einer Verfügbarkeitsgruppe gehört, wählen Sie unter **Verfügbarkeitsgruppe** die Option **Verfügbarkeitsgruppe erstellen**, oder fügen Sie den virtuellen Computer zu einer vorhandenen Verfügbarkeitsgruppe hinzu.

	**Hinweis**: Virtuelle Computer, die Mitglieder einer Verfügbarkeitsgruppe sind, werden in verschiedenen Fehlerdomänen eingesetzt. Die Platzierung mehrerer virtueller Computer in einer Verfügbarkeitsgruppe sorgt dafür, dass Ihre Anwendung auch bei Netzwerkausfällen, bei Ausfällen der lokalen Hardware und während geplanter Ausfallzeiten verfügbar ist.

15.  Prüfen Sie unter **Endpunkte** die neuen Endpunkte, die erstellt werden, um Verbindungen mit dem virtuellen Computer über Remote Desktop oder einen Secure Shell (SSH)-Client zu ermöglichen. Sie können entweder jetzt Endpunkte hinzufügen oder diese später erstellen. Anweisungen dazu, wie Sie sie später erstellen, finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](../articles/virtual-machines/virtual-machines-set-up-endpoints.md).

16.  Entscheiden sie unter **VM Agent**, ob der VM Agent installiert werden soll. Dieser Agent stellt die Umgebung bereit, in der Sie Erweiterungen installieren können, die Ihnen bei der Interaktion mit dem virtuellen Computer helfen können. Nähere Informationen finden Sie unter [Verwalten von Erweiterungen](http://go.microsoft.com/FWLink/p/?LinkID=390493).

17. Klicken Sie auf den Pfeil, um den virtuellen Computer zu erstellen.


	![Benutzerdefinierter virtueller Computer erfolgreich erstellt](./media/howto-custom-create-vm/VMSuccessWindows.png)

##Nächste Schritte##
Nachdem der virtuelle Computer erstellt wurde, wird er automatisch gestartet. Wenn im Portal der Status "Wird ausgeführt" angezeigt wird, können Sie sich beim virtuellen Computer anmelden. Weitere Anweisungen finden Sie in den folgenden Artikeln:

- [Anmelden bei einem mit Linux betriebenen virtuellen Computer](../articles/virtual-machines/virtual-machines-linux-how-to-log-on.md)
- [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird](../articles/virtual-machines/virtual-machines-log-on-windows-server.md)

<!---HONumber=July15_HO4-->