<properties urlDisplayName="Create a virtual machine for a website" pageTitle="Erstellen eines virtuellen Computers f&uuml;r ein Webprojekt mit Visual Studio" metaKeywords="Visual Studio, ASP.NET, web project, virtual machine" description="Erstellen eines virtuellen Computers f&uuml;r eine Website" metaCanonical="" services="" documentationCenter="" title="Erstellen eines virtuellen Computers f&uuml;r eine Website mit Visual Studio" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />

# Erstellen eines virtuellen Computers für eine Website mit Visual Studio

Beim Erstellen eines Webprojekts für eine Azure-Website können Sie einen virtuellen Computer in Azure verteilen. Sie können anschließend den virtuellen Computer mit zusätzlicher Software konfigurieren oder den virtuellen Computer für Diagnose- oder Debugzwecke verwenden.

So erstellen Sie beim Erstellen einer Website einen virtuellen Computer

1.  Wählen Sie in Visual Studio **Datei**, **Neues Projekt**, **Web** und dann **ASP-NET-Webanwendung** aus.
2.  Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** den gewünschten Webanwendungstyp aus. Stellen Sie im Abschnitt „Azure“ des Dialogfelds sicher (in der unteren rechten Ecke), dass das Kontrollkästchen **In der Cloud hosten** aktiviert ist (dieses Kontrollkästchen heißt in einigen Installationen **Remoteressourcen erstellen**).

    ![][0]

3.  Wählen Sie **Virtueller Computer** und dann die Schaltfläche **OK** aus.
4.  Melden Sie sich bei Azure an, wenn Sie dazu aufgefordert werden. Das Dialogfeld „Virtuellen Computer erstellen“ wird angezeigt.

    ![][1]

5.  Geben Sie im Feld „DNS-Name“ einen Namen für den virtuellen Computer an. Der DNS-Name muss in Azure eindeutig sein. Wenn der von Ihnen eingegebene Name nicht verfügbar ist, wird ein rotes Ausrufezeichen angezeigt.
6.  Wählen Sie in der Liste „Image“ das Betriebssystemimage aus, das auf dem virtuellen Computer verwendet werden soll. Sie können sämtliche Standardimages oder Ihr eigenes Image verwenden, das Sie in Azure hochgeladen haben.
7.  Lassen Sie das Kontrollkästchen **IIS und Web Deploy aktivieren** deaktiviert, sofern Sie keinen anderen Webserver installieren möchten. Sie können keine Veröffentlichung in Visual Studio vornehmen, wenn Sie „Web Deploy“ deaktivieren. Sie können jedem gepackten Windows Server-Image IIS und Web Deploy hinzufügen, einschließlich Ihrer eigenen benutzerdefinierten Images.
8.  Wählen Sie in der Liste **Größe** die Größe des virtuellen Computers aus.
9.  Geben Sie die Anmeldeinformationen für diesen virtuellen Computer an. Notieren Sie sie, da Sie sie für den Zugriff auf den Computer per Remotedesktop benötigen.
10. Wählen Sie in der Liste **Ort** Region, virtuelles Netzwerk oder Affinitätsgruppe aus, die bzw. das den virtuellen Computer hostet. Sie können Affinitätsgruppen verwenden, um sicherzustellen, dass Azure-Ressourcen im selben Rechenzentrum zusammenbleiben, die über eine Menge an Netzwerkdatenverkehr untereinander verfügen, oder Sie können Regionen verwenden, um den genauen Rechenzentrumsstandort anzugeben.
11. Wählen Sie **OK** zum Starten der Erstellung des virtuellen Computer aus. Im Fenster **Ausgabe** können Sie den Status anzeigen.

    ![][2]

12. Wenn der virtuelle Computer verteilt ist, werden Veröffentlichungsskripts in einem **PublishScripts**-Knoten in Ihrer Lösung erstellt. Das Veröffentlichungsskript wird ausgeführt und verteilt einen virtuellen Computer in Azure. Das Fenster **Ausgabe** zeigt den Status an. Das Skript führt die folgenden Aktionen zum Einrichten des virtuellen Computers aus:

    -   Erstellt den virtuellen Computer, wenn er nicht vorhanden ist.
    -   Erstellt ein Speicherkonto mit einem Namen, der mit `devtest` beginnt, jedoch nur, wenn kein derartiges Speicherkonto in der angegebenen Region vorhanden ist.
    -   Erstellt einen Cloud-Dienst als einen Container für den virtuellen Computer und erstellt eine Webrolle für die Website.
    -   Konfiguriert Web Deploy auf dem virtuellen Computer.
    -   Konfiguriert IIS und ASP.NET auf dem virtuellen Computer.

    ![][3]

1.  (Optional) Erweitern Sie im **Server-Explorer** den Knoten **Virtuelle Computer**. Wählen Sie den Knoten für den von Ihnen erstellten virtuellen Computer aus, und wählen Sie **Verbindung mit Remotedesktop herstellen** aus, um eine Verbindung zum virtuellen Computer herzustellen.

# Nächste Schritte

[Hier][Hier] finden Sie genauere Informationen, wenn Sie die von Ihnen erstellten Veröffentlichungsskripts anpassen möchten.

  [0]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_NewProject.PNG
  [1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_CreateVM.PNG
  [2]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_Provisioning.png
  [3]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SolutionExplorer.png
  [Hier]: http://msdn.microsoft.com/library/dn642480.aspx
