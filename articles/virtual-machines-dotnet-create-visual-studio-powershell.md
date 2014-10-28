<properties linkid="dotnet-visualstudio-create-virtual-machine" urlDisplayName="Create a virtual machine for a website" pageTitle="Creating a virtual machine for a web project using Visual Studio" metaKeywords="Visual Studio, ASP.NET, web project, virtual machine" description="Create a virtual machine for a website" metaCanonical="" services="" documentationCenter="" title="Creating a virtual machine for a website with Visual Studio" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

# Erstellen eines virtuellen Computers für eine Website mit Visual Studio

Wenn Sie ein Webprojekt für eine Azure-Website erstellen, können Sie einen virtuellen Computer in Azure bereitstellen. Anschließend können Sie den virtuellen Computer mit zusätzlicher Software konfigurieren oder zu Diagnose- bzw. Debugzwecken verwenden.

Befolgen Sie die folgenden Schritte, um beim Erstellen einer Website einen virtuellen Computer zu erstellen:

1.  Wählen Sie in Visual Studio **Datei**, **Neues Projekt** und dann **Web** aus. Anschließend wählen Sie **ASP.NET-Webanwendung** aus.
2.  Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** den gewünschten Typ der Webanwendung aus. Stellen Sie dann im Azure-Bereich des Dialogfelds (in der unteren rechten Ecke) sicher, dass das Kontrollkästchen **In der Cloud hosten** aktiviert ist (dieses Kontrollkästchen ist bei einigen Installationen mit **Remoteressourcen erstellen** bezeichnet).

    ![][]

3.  Wählen Sie **Virtueller Computer** und anschließend die Schaltfläche **OK** aus.
4.  Wenn Sie dazu aufgefordert werden, melden Sie sich bei Azure an. Das Dialogfeld zum Erstellen des virtuellen Computers wird angezeigt.

    ![][1]

5.  Geben Sie in das DNS-Namensfeld einen Namen für den virtuellen Computer ein. Der DNS-Name muss in Azure eindeutig sein. Wenn der eingegebene Name nicht verfügbar ist, wird ein rotes Ausrufezeichen angezeigt.
6.  Wählen Sie in der Abbildliste das Betriebssystemabbild aus, das Sie auf dem virtuellen Computer verwenden möchten. Sie können ein beliebiges Standardabbild auswählen oder ein eigenes Abbild zu Azure hochladen.
7.  Lassen Sie das Kontrollkästchen **IIS und Web Deploy aktivieren** aktiviert, sofern Sie nicht planen, einen anderen Webserver zu installieren. Wenn Sie Web Deploy deaktivieren, können Sie nicht über Visual Studio veröffentlichen. Sie können IIS und Web Deploy zu beliebigen gepackten Windows Server-Abbildern hinzufügen, einschließlich Ihrer eigenen benutzerdefinierten Abbilder.
8.  Wählen Sie in der Liste **Größe** die Größe für den virtuellen Computer aus.
9.  Geben Sie die Anmeldeinformationen für diesen virtuellen Computer an. Notieren Sie sich diese, da sie für den Zugriff auf den Computer über den Remotedesktop erforderlich sind.
10. Wählen Sie in der Liste **Speicherort** die Region, das virtuelle Netzwerk oder die Affinitätsgruppe aus, die bzw. das den virtuellen Computer hostet. Sie können Affinitätsgruppen verwenden, um sicherzustellen, dass Azure-Ressourcen mit starkem Netzwerkverkehr in demselben Datencenter verbleiben, oder Sie verwenden Regionen, um den genauen Speicherort im Datencenter anzugeben.
11. Wählen Sie **OK** aus, um die Erstellung des virtuellen Computers zu starten. Sie können den Status im Fenster **Ausgabe** nachverfolgen.

    ![][2]

12. Bei der Bereitstellung des virtuellen Computers werden Veröffentlichungsskripts im Knoten **PublishScripts** Ihrer Projektmappe erstellt. Das Veröffentlichungsskript wird ausgeführt und stellt einen virtuellen Computer in Azure bereit. Das Fenster **Ausgabe** zeigt den Status an. Das Skript führt die folgenden Aktionen aus, um den virtuellen Computer einzurichten:

    -   Erstellt den virtuellen Computer, sofern er nicht bereits vorhanden ist.
    -   Erstellt ein Speicherkonto mit einem Namen der mit `devtest` beginnt, aber nur, wenn sich im angegebenen Bereich nicht bereits ein Speicherkonto mit diesem Namen befindet.
    -   Erstellt einen Clouddienst als Container für den virtuellen Computer und erstellt eine Webrolle für die Website.
    -   Konfiguriert Web Deploy auf dem virtuellen Computer.
    -   Konfiguriert IIS und ASP.NET auf dem virtuellen Computer.

    ![][3]

1.  (Optional) Erweitern Sie in **Server-Explorer** den Knoten **Virtuelle Computer**, wählen Sie dann den Knoten für den erstellten virtuellen Computer aus, und wählen Sie anschließend **Mit Remotedesktop verbinden** aus, um die Verbindung zum virtuellen Computer herzustellen.

# Nächste Schritte

Wenn Sie die erstellten Veröffentlichungsskripts anpassen möchten, finden Sie [hier][hier] ausführlichere Informationen.

  []: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_NewProject.PNG
  [1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_CreateVM.PNG
  [2]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_Provisioning.png
  [3]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SolutionExplorer.png
  [hier]: http://msdn.microsoft.com/library/dn642480.aspx
