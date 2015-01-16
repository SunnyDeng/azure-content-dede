<properties urlDisplayName="How to create a VSO project and setup Continuous Deployment" pageTitle="Erstellen eines Visual Studio Online-Teamprojekts und Einrichten der kontinuierlichen Bereitstellung - Microsoft Azure" metaKeywords="Visual Studio Online-Teamprojekt erstellen, kontinuierliche Bereitstellung in Azure" description="Erfahren Sie, wie Sie ein Visual Studio Online-Teamprojekt erstellen und für die kontinuierliche Bereitstellung an Microsoft Azure konfigurieren." metaCanonical="" services="cloud-services, visual-studio-online" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="jimlamb" solutions="" writer="jimlamb" manager="kamrani" editor=""  />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jimlamb" />

#Erstellen eines Visual Studio Online-Projekts und Einrichten der kontinuierlichen Bereitstellung in Microsoft Azure 

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Im Microsoft Azure-Verwaltungsportal können Sie ein Teamprojekt in Visual Studio Online erstellen und die Webanwendung für die kontinuierliche Bereitstellung auf einer Website konfigurieren.

##Inhaltsverzeichnis

* [Erstellen eines Teamprojekts](#create_team_project)
* [Erstellen einer neuen Webanwendung und Hinzufügen zur Git-Versionskontrolle](#create_web_app)
* [Einrichten der kontinuierlichen Bereitstellung](#continuous_deployment)

## <a name="create_team_project"></a>Erstellen eines Teamprojekts

1. Melden Sie sich beim Verwaltungsportal an.
2. Klicken Sie unten links auf **Neu**.
3. Klicken Sie auf **Teamprojekt**
4. Geben Sie dem Teamprojekt einen Namen. Sie können den Namen des Teamprojekts nicht mehr ändern, nachdem es erstellt wurde.
5. Wählen Sie den Typ der Versionskontrolle aus, den Sie für das Projekt verwenden möchten. Sie können entweder Git (ein verteiltes Versionskontrollsystem) oder Team Foundation Version Control (ein zentralisiertes Versionskontrollsystem) auswählen. Sie sind nicht sicher, welches System Sie verwenden sollten? [Hier](http://msdn.microsoft.com/en-us/library/ms181368.aspx) erhalten Sie weitere Informationen.
6. Wählen Sie die Prozessvorlage aus. Einen Vergleich der Prozessvorlagen finden Sie unter [Arbeiten mit Teamprojektartefakten](http://msdn.microsoft.com/en-us/library/ms400752.aspx).
7. Wählen Sie das Visual Studio Online-Konto aus, das verwendet wird, um dieses Teamprojekt zu erstellen, Benutzer hinzuzufügen und die Ressourcennutzung zu überwachen.
8. Lassen Sie das Kontrollkästchen **Add to Startboard** aktiviert, damit das neue Teamprojekt automatisch auf dem Startboard angezeigt wird.
9. Klicken Sie auf **Erstellen**.

## <a name="create_web_app"></a>Erstellen einer neuen Webanwendung und Hinzufügen zur Git-Versionskontrolle

1. Klicken Sie im Startboard auf das neue Teamprojekt.
2. Klicken Sie im Bereich **Code** unter **Repositorys** auf das Git-Repository, das denselben Namen wie Ihr Teamprojekt trägt.
3. Klicken Sie im Fensterbereich für Repository/Verzweigung auf den Fensterbefehl **Visual Studio**, um das neue Repository in Visual Studio zu öffnen. Sie werden möglicherweise von Ihrem Webbrowser aufgefordert, das Starten von Visual Studio zu erlauben.
4. Klicken Sie im Teamexplorer-Toolfenster von Visual Studio auf **Dieses Repository clonen**, um einen lokalen Klon des neuen Repositorys auf dem lokalen Datenträger einzurichten.
5. Klicken Sie im Abschnitt **Projektmappen** der Teamexplorer-Startseite auf **Neu...**, um ein neues Projekt im soeben geklonten Repository zu erstellen.
6. Erweitern Sie im Dialogfeld "Neues Projekt" den Knoten für Visual Basic oder Visual C#, je nach bevorzugter Programmiersprache, und wählen Sie **Web** aus.
7. Klicken Sie in der Liste der verfügbaren Projektvorlagen auf **ASP.NET-Webanwendung**, und geben Sie einen Namen für die Webanwendung ein.
8. Klicken Sie auf **OK**.
9. Wechseln Sie zum Teamexplorer-Toolfenster, navigieren Sie zur Seite "Änderungen", und geben Sie eine Commit-Nachricht ein.
10. Klicken Sie auf den Dropdown-Pfeil der Schaltfläche **Commit**, und wählen Sie **Commit und Sync**, um Ihre Änderungen festzuschreiben und den Commit auf das zuvor geklonte Remoterepository zu übertragen.

## <a name="continuous_deployment"></a>Einrichten der kontinuierlichen Bereitstellung

1. Melden Sie sich beim Verwaltungsportal an.
2. Klicken Sie im Startboard auf das zuvor erstellte Teamprojekt.
3. Klicken Sie im Bereich **Build** auf **Kontinuierliche Bereitstellung einrichten**.
4. Wählen Sie die Website aus, auf der Sie Ihre Anwendung bereitstellen möchten.
5. Wählen Sie das Repository aus, in dem sich der Quellcode befindet (zu diesem Zeitpunkt sollte das Teamprojekt nur ein Repository enthalten).
6. Wählen Sie die zu erstellende Verzweigung aus. Visual Studio Online sucht im Inhalt des letzten Commits für diese Verzweigung nach einer einzelnen Visual Studio-Projektmappendatei (*.sln). Wenn eine vorhanden ist, wird eine neue Builddefinition (Name endet auf "_CD") konfiguriert, um diese Lösung zu erstellen. Wenn keine Projektmappendatei oder mehrere vorhanden sind, wird zwar eine neue Builddefinition konfiguriert, diese ist jedoch deaktiviert und muss von Ihnen in Visual Studio bearbeitet werden, um die zu erstellende Lösung anzugeben. 
7. Klicken Sie auf **Erstellen**.
8. Visual Studio Online erstellt eine neue Builddefinition, um Ihr Webanwendungsprojekt zu erstellen und bereitzustellen, und versucht, einen neuen Build dieser Definition in die Warteschlange zu stellen.

###So überprüfen Sie, ob Ihre Bereitstellung erfolgreich abgeschlossen wurde###

1. Klicken Sie im Startboard auf das zuvor erstellte Teamprojekt.
2. Klicken Sie im Bereich **Build** auf **Neuer Build**, um das Build-Fenster zu öffnen.
3. Klicken Sie im Build-Fenster auf das erste Element im Bereich **Bereitstellungen**, um die zugehörige Website zu öffnen.
4. Klicken Sie im Website-Fenster auf den Fensterbefehl **Durchsuchen**, um die Website zu durchsuchen und die Bereitstellung Ihrer Webanwendung zu überprüfen.
