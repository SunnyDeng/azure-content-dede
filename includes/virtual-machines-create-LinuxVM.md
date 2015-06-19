1. Melden Sie sich beim Azure [Verwaltungsportal](http://manage.windowsazure.com) an. Klicken Sie in der Befehlsleiste auf **Neu**.

2. Klicken Sie auf **Virtueller Computer** und dann auf **From Gallery**.

3. Wählen Sie unter **Abbild auswählen** ein Abbild aus einer der Listen aus. (Die verfügbaren Abbilder können sich je nach dem von Ihnen verwendeten Abonnement unterscheiden.) Klicken Sie auf den Pfeil, um fortzufahren.

4. Wenn mehrere Versionen des Images verfügbar sind, wählen Sie unter **Version Release Date** die Version aus, die Sie verwenden möchten.

5. Geben Sie unter **Virtual Machine Name** den Namen ein, den Sie verwenden möchten. Geben Sie für diesen virtuellen Computer **MyTestVM1** ein.

6. Wählen Sie unter **Größe** die Größe für den virtuellen Computer aus. Die Größe, die Sie auswählen, hängt von der Anzahl der Kerne ab, die für Ihre Anwendung erforderlich sind. Wählen Sie für diesen virtuellen Computer die kleinste verfügbare Größe.

7. Geben Sie unter **Neuer Benutzername** den Namen des Kontos ein, das Sie zur Administration des virtuellen Computers verwenden. Sie können nicht "root" für den Benutzernamen verwenden. Geben Sie für diesen virtuellen Computer **NeuerBenutzer1** ein.

8. Wählen Sie unter **Authentication** Kennwort angeben. Geben Sie dann die erforderlichen Informationen ein, und klicken Sie auf den Pfeil, um fortzufahren.

9. Sie können virtuelle Computer zusammen im Clouddienst platzieren. Aber in diesem Tutorial erstellen Sie nur einen einzigen virtuellen Computer. Wählen Sie hierzu **Neuen Clouddienst erstellen** aus.

10. Geben Sie unter **DNS-Name des Clouddiensts** einen Namen ein, der zwischen drei und 24 Kleinbuchstaben und Ziffern enthält. Sie müssen einen eigenen Namen für den Cloud-Dienst eingeben, da dieser in Azure eindeutig sein muss. Der Name des Cloud-Diensts wird zu einem Teil des URI, mit dessen Hilfe der virtuelle Computer über den Cloud-Dienst kontaktiert wird.

11. Wählen Sie unter **Region/Affinitätsgruppe/Virtuelles Netzwerk** den Standort des virtuellen Computers aus.

12. Sie können ein Speicherkonto auswählen, in dem die VHD-Datei gespeichert wird. In diesem Tutorial verwenden wir die Standardeinstellung **Automatisch generiertes Speicherkonto verwenden**.

13. Wählen Sie außerdem unter **Verfügbarkeitsgruppe** für dieses Tutorial die Standardeinstellung **Keine**.

14.	Prüfen Sie unter **Endpunkte** den automatisch erstellten Endpunkt, um Secure Shell-Verbindungen (SSH) zu dem virtuellen Computer zuzulassen. (Endpunkte ermöglichen Ressourcen im Internet oder anderen virtuellen Netzwerken die Kommunikation mit einem virtuellen Computer.) Sie können jetzt weitere Endpunkte hinzufügen oder diese später erstellen. Anweisungen dazu, wie Sie sie später erstellen, finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](../articles/virtual-machines/virtual-machines-set-up-endpoints.md).

15.  Prüfen Sie unter **VM-Agent** die verfügbaren Erweiterungen. Diese Erweiterungen bieten verschiedene Features, die die Verwendung und Verwaltung eines virtuellen Computers vereinfachen. Details finden Sie im Thema zu den [Azure-VM-Erweiterungen](http://go.microsoft.com/FWLink/p/?LinkID=390493).


Nachdem Azure den virtuellen Computer und den Cloud-Dienst erstellt hat, führt das Verwaltungsportal den neuen virtuellen Computer unter **Virtuelle Computer** und den Cloud-Dienst unter **Cloud-Dienste** auf. Sowohl der virtuelle Computer als auch der Clouddienst werden automatisch gestartet. 
<!--HONumber=52-->
