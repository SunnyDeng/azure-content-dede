

Wenn Sie ein Webanwendungsprojekt für Azure erstellen, können Sie einen virtuellen Computer in Azure bereitstellen. Anschließend können Sie den virtuellen Computer mit zusätzlicher Software konfigurieren oder zu Diagnose- bzw. Debugzwecken verwenden.

Befolgen Sie die folgenden Schritte, um beim Erstellen einer Webanwendung einen virtuellen Computer zu erstellen:

1. Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt** > **Web**. Wählen Sie dann **ASP.NET-Webanwendung** aus (unter dem Knoten **Visual C#** oder **Visual Basic**).
2. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** den gewünschten Typ der Webanwendung aus. Stellen Sie dann im Azure-Bereich des Dialogfelds (in der unteren rechten Ecke) sicher, dass das Kontrollkästchen **In der Cloud hosten** aktiviert ist (dieses Kontrollkästchen ist bei einigen Installationen mit **Remoteressourcen erstellen** bezeichnet).

	![][0]

3. Wählen Sie für dieses Beispiel in der Dropdownliste unter Microsoft Azure die Option **Virtueller Computer (V1)** aus, und klicken Sie dann auf die Schaltfläche **OK**.
4. Melden Sie sich bei Azure an, wenn Sie dazu aufgefordert werden. Das Dialogfeld **Virtuellen Computer erstellen** wird angezeigt.

	![][2]

5. Geben Sie im Feld **DNS-Name** einen Namen für den virtuellen Computer ein. Der DNS-Name muss in Azure eindeutig sein. Wenn der eingegebene Name nicht verfügbar ist, wird ein rotes Ausrufezeichen angezeigt.
6. Wählen Sie in der Liste **Image** das Image aus, das als Grundlage für den virtuellen Computer dienen soll. Sie können ein beliebiges Standardimage für virtuelle Azure-Computer oder ein eigenes in Azure hochgeladenes Image auswählen.
7. Lassen Sie das Kontrollkästchen **IIS und Web Deploy aktivieren** aktiviert, sofern Sie nicht planen, einen anderen Webserver zu installieren. Wenn Sie Web Deploy deaktivieren, können Sie nicht über Visual Studio veröffentlichen. Sie können IIS und Web Deploy zu beliebigen gepackten Windows Server-Abbildern hinzufügen, einschließlich Ihrer eigenen benutzerdefinierten Abbilder.
8. Wählen Sie in der Liste **Größe** die Größe für den virtuellen Computer aus.
9. Geben Sie die Anmeldeinformationen für diesen virtuellen Computer an. Notieren Sie sich diese, da sie für den Zugriff auf den Computer über den Remotedesktop erforderlich sind.
10. Wählen Sie in der Liste **Standort** die Region zum Hosten des virtuellen Computers aus.
11. Klicken Sie auf die Schaltfläche **OK**, um die Erstellung des virtuellen Computers zu starten. Sie können den Fortschritt des Vorgangs im Fenster **Ausgabe** verfolgen.

	![][3]

12. Bei der Bereitstellung des virtuellen Computers werden veröffentlichte Skripts im Knoten **PublishScripts** Ihrer Projektmappe erstellt. Das veröffentlichte Skript wird ausgeführt und stellt einen virtuellen Computer in Azure bereit. Das Fenster **Ausgabe** zeigt den Status an. Das Skript führt die folgenden Aktionen zum Einrichten des virtuellen Computers aus:

	* Erstellt den virtuellen Computer, wenn er nicht vorhanden ist.
	* Erstellt ein Speicherkonto mit einem Namen, der mit `devtest` beginnt, jedoch nur, wenn kein derartiges Speicherkonto in der angegebenen Region vorhanden ist.
	* Erstellt einen Clouddienst als Container für den virtuellen Computer und erstellt eine Webrolle für die Webanwendung.
	* Konfiguriert Web Deploy auf dem virtuellen Computer.
	* Konfiguriert IIS und ASP.NET auf dem virtuellen Computer.

	![][4]

13. (Optional:) Stellen Sie eine Verbindung mit dem neuen virtuellen Computer her. Erweitern Sie in **Server-Explorer** den Knoten **Virtuelle Computer**, wählen Sie dann den Knoten für den erstellten virtuellen Computer aus, und wählen Sie anschließend im Kontextmenü **Mit Remotedesktop verbinden** aus. Alternativ können Sie in **Cloud Explorer** im Kontextmenü die Option **Open in Portal** auswählen und dort eine Verbindung mit dem virtuellen Computer herstellen.

 ![][5]


## Nächste Schritte

Wenn Sie die erstellten veröffentlichten Skripts anpassen möchten, lesen Sie die detaillierteren Informationen unter [Verwenden von Windows PowerShell-Skripts zum Veröffentlichen in Entwicklungs- und Testumgebungen](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png

<!---HONumber=AcomDC_0323_2016-->