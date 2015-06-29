<properties
	pageTitle="Erstellen eines virtuellen Computers für ein Webprojekt mit Visual Studio"
	description="Erstellen eines virtuellen Computers für eine Website"
	services="virtual-machines"
	documentationCenter=""
	authors="kempb"
	manager="douge"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="kempb"/>

# Erstellen eines virtuellen Computers für eine Website mit Visual Studio

Wenn Sie ein Webprojekt für eine Azure-Website erstellen, können Sie einen virtuellen Computer in Azure bereitstellen. Anschließend können Sie den virtuellen Computer mit zusätzlicher Software konfigurieren oder zu Diagnose- bzw. Debugzwecken verwenden.

Befolgen Sie die folgenden Schritte, um beim Erstellen einer Website einen virtuellen Computer zu erstellen:

1. Wählen Sie in Visual Studio **Datei**, **Neues Projekt** und dann **Web** aus. Anschließend wählen Sie **ASP.NET-Webanwendung** aus (unter dem Knoten **Visual C#** oder **Visual Basic**).
2. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** den gewünschten Typ der Webanwendung aus. Stellen Sie dann im Azure-Bereich des Dialogfelds (in der unteren rechten Ecke) sicher, dass das Kontrollkästchen **In der Cloud hosten** aktiviert ist (dieses Kontrollkästchen ist bei einigen Installationen mit **Remoteressourcen erstellen** bezeichnet).

	![][0]

3. Wählen Sie im Dropdown-Listenfeld unter Microsoft Azure **Virtueller Computer** aus, und klicken Sie dann auf **OK**.
4. Melden Sie sich bei Azure an, wenn Sie dazu aufgefordert werden. Das Dialogfeld **Virtuellen Computer erstellen** wird angezeigt.

	![][2]

5. Geben Sie im Feld "DNS-Name" einen Namen für den virtuellen Computer an. Der DNS-Name muss in Azure eindeutig sein. Wenn der eingegebene Name nicht verfügbar ist, wird ein rotes Ausrufezeichen angezeigt.
6. Wählen Sie in der Liste **Image** das Image des virtuellen Computers aus, das als Grundlage für den virtuellen Computer dienen soll. Sie können ein beliebiges Standard-Azure-VM-Image oder ein eigenes in Azure hochgeladenes Image auswählen.
7. Lassen Sie das Kontrollkästchen **IIS und Web Deploy aktivieren** aktiviert, sofern Sie nicht planen, einen anderen Webserver zu installieren. Wenn Sie Web Deploy deaktivieren, können Sie nicht über Visual Studio veröffentlichen. Sie können IIS und Web Deploy zu beliebigen gepackten Windows Server-Abbildern hinzufügen, einschließlich Ihrer eigenen benutzerdefinierten Abbilder.
8. Wählen Sie in der Liste **Größe** die Größe für den virtuellen Computer aus.
9. Geben Sie die Anmeldeinformationen für diesen virtuellen Computer an. Notieren Sie sich diese, da sie für den Zugriff auf den Computer über den Remotedesktop erforderlich sind.
10. Wählen Sie in der Liste **Standort** die Region zum Hosten des virtuellen Computers aus.
11. Klicken Sie auf **OK**, um mit der Erstellung des virtuellen Computers zu beginnen. Sie können den Fortschritt des Vorgangs im **Ausgabefenster verfolgen.
	![][3]

12. Bei der Bereitstellung des virtuellen Computers werden Veröffentlichungsskripts im Knoten **PublishScripts** Ihrer Projektmappe erstellt. Das Veröffentlichungsskript wird ausgeführt und verteilt einen virtuellen Computer in Azure. Das Fenster **Ausgabe** zeigt den Status an. Das Skript führt die folgenden Aktionen zum Einrichten des virtuellen Computers aus:

	* Erstellt den virtuellen Computer, wenn er nicht vorhanden ist.
	* Erstellt ein Speicherkonto mit einem Namen, der mit `devtest` beginnt, jedoch nur, wenn kein derartiges Speicherkonto in der angegebenen Region vorhanden ist.
	* Erstellt einen Cloud-Dienst als einen Container für den virtuellen Computer und erstellt eine Webrolle für die Website.
	* Konfiguriert Web Deploy auf dem virtuellen Computer.
	* Konfiguriert IIS und ASP.NET auf dem virtuellen Computer.

	![][4]

<br/> 13. (Optional:) Stellen Sie eine Verbindung mit dem neuen virtuellen Computer her. Erweitern Sie in **Server-Explorer** den Knoten **Virtuelle Computer**, wählen Sie dann den Knoten für den erstellten virtuellen Computer aus, und wählen Sie anschließend im Kontextmenü **Mit Remotedesktop verbinden** aus.

	![][5]

## Nächste Schritte

Wenn Sie die erstellten Veröffentlichungsskripts anpassen möchten, finden Sie [hier](http://msdn.microsoft.com/library/dn642480.aspx) ausführlichere Informationen.

[0]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-dotnet-create-visual-studio-powershell/VS_CreateVM_Connect.png
 

<!---HONumber=58_postMigration-->