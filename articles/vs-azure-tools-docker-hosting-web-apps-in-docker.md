<properties
   pageTitle="Hosten von Web-Apps in Docker | Microsoft Azure"
	description="Erfahren Sie, wie Sie Visual Studio verwenden, um eine Web-App in einem Docker-Container zu hosten."
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tglee"/>
<tags
   ms.service="multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="multiple"
	ms.date="08/20/2015"
	ms.author="kempb"/>

# Hosten von Web-Apps in Docker

[Docker](https://www.docker.com/whatisdocker/) ist ein einfaches Containermodul, in gewisser Weise mit einem virtuellen Computer vergleichbar, das Sie zum Hosten von Anwendungen und Diensten verwenden können. Visual Studio unterstützt Docker für Ubuntu, CoreOS und Windows.

Dieses Beispiel zeigt, wie Sie Visual Studio 2015-Tools für die Docker-Erweiterung zum Veröffentlichen einer ASP.NET 5-App auf einem virtuellen Ubuntu Linux-Computer (wird hier als Docker-Host bezeichnet) in Azure mit installierter Docker-Erweiterung zusammen mit einer ASP.NET 5-Webanwendung verwenden können. Auf die gleiche Weise ist auch eine Veröffentlichung in einem Windows-Container möglich.

Sie können die App auf einem neuen Docker-Host veröffentlichen, der in Azure gehostet wird, oder auf einem lokalen Server, Hyper-V- oder Boot2Docker-Host, indem Sie die Einstellung **Benutzerdefinierter Host** verwenden. Nach dem Veröffentlichen Ihrer App auf einem Docker-Host können Sie mit den Docker-Befehlszeilentools mit dem Container interagieren, in dem Ihre Anwendung veröffentlicht wurde.

## Erstellen und Veröffentlichen eines neuen Docker-Containers

In diesen Verfahren erstellen Sie ein neues ASP.NET 5-Webanwendungsprojekt und einen Containerhost. Dann erstellen Sie das Web-App-Projekt im Docker-Container und führen es aus. Laden Sie zuerst die [Visual Studio 2015-Tools für Docker](https://aka.ms/DockerToolsForVS) herunter, und installieren Sie sie.

### Hinzufügen eines ASP.NET 5-Webanwendungsprojekts

1. Erstellen Sie ein neues ASP.NET-Webanwendungsprojekt. Wählen Sie im Hauptmenü **Datei** und dann **Neues Projekt** aus. Wählen Sie unter **C#**, **Web** die Option **ASP.NET-Webanwendung** aus.

1. Wählen Sie in der Liste der **ASP.NET 5-Vorschauvorlagen** die Option **Website** aus.

1. Da die Web-App in Docker gehostet/ausgeführt werden soll, deaktivieren Sie das Kontrollkästchen **In der Cloud hosten**, falls es aktiviert ist, und wählen Sie dann die Schaltfläche **OK** aus.

  ![][0]

  An diesem Punkt würden Sie in der Regel Code zur Web-App hinzufügen, damit sie etwas Sinnvolles tut. In diesem Beispiel behalten wir aber die Standardeinstellungen bei. (Beachten Sie, dass Sie auch vorhandene ASP.NET 5-Web-Apps verwenden können.)

### Veröffentlichen des Projekts

1. Wählen Sie im Kontextmenü des ASP.NET-Projekts **Veröffentlichen** aus.

1. Wählen Sie Im Abschnitt **Veröffentlichungsziel auswählen** im Dialogfeld **Web veröffentlichen** die Schaltfläche **Docker-Container** aus.

    Wenn die Option "Docker-Container" nicht angezeigt wird, stellen Sie sicher, dass Sie die Visual Studio 2015-Tools für Docker installiert haben und dass Sie im vorherigen Verfahren eine ASP.NET 5-Websitevorlage ausgewählt haben.

    ![][1]

    Das Dialogfeld **Virtuellen Docker-Computer auswählen** wird angezeigt. Hier können Sie den Docker-Host angeben, in dem Sie das Projekt veröffentlichen möchten. Sie können einen neuen Docker-Host erstellen oder einen vorhandenen virtuellen Computer auswählen, der in Azure oder an anderer Stelle gehostet wird. In diesem Beispiel erstellen wir einen neuen Azure-Docker-Host.

1. Falls Sie bereits bei einem Azure-Konto angemeldet sind, fahren Sie mit Schritt 5 fort. Wenn Sie noch nicht bei einem Konto angemeldet sind, wählen Sie die Schaltfläche **Konto hinzufügen** aus.

    ![][2]

1. Geben Sie im Dialogfeld **Anmelden bei Visual Studio** das E-Mail-Konto für Ihr Azure-Abonnement ein, und wählen Sie dann die Schaltfläche **Weiter** aus.

1. Wählen Sie die Schaltfläche **Neu** aus, um einen neuen virtuellen Azure-Docker-Computer zu erstellen, und wählen dann die Schaltfläche **OK** aus.

    ![][3]

    Beachten Sie, dass Sie auch einen vorhandenen Docker-Host verwenden können. Wählen Sie dazu einen **vorhandenen virtuellen Azure-Docker-Computer** in der entsprechenden Dropdownliste aus, statt die Schaltfläche **Neu** zu verwenden. Diese Liste zeigt nicht nur Containerhosts an, sondern alle virtuellen Computer in Ihrem Azure-Mandanten.

    Alternativ können Sie eine Veröffentlichung auf einem benutzerdefinierten Docker-Host vornehmen. Weitere Informationen finden Sie unter **Bereitstellen eines benutzerdefinierten Docker-Hosts** weiter unten in diesem Thema.

1. Geben Sie die folgenden Informationen im Dialogfeld **Einen virtuellen Computer in Microsoft Azure erstellen** ein. Wenn Sie fertig sind, klicken Sie auf die Schaltfläche **OK**. Dadurch wird ein virtueller Linux-Computer mit konfigurierter Docker-Erweiterung erstellt.

    ![][4]

    Beachten Sie, dass Sie jetzt auch die Möglichkeit haben, einen Windows-Containerhost mit Windows Server 2016 Technical Preview 3 (TP3) zu erstellen.

    ![][8]

    |Eigenschaftenname|Einstellung|
    |---|---|
    |Standort|Ändern Sie diese Einstellung auf die Ihrem Gebietsschema am nächsten gelegene Region.|
    |DNS-Name|Geben Sie einen eindeutigen Namen für den virtuellen Computer ein. Wenn der Name von Azure akzeptiert wird, wird rechts ein grüner Kreis mit einem weißen Häkchen angezeigt. Wenn der Name nicht akzeptiert wird, wird ein roter Kreis mit einem weißen x angezeigt. Geben Sie in diesem Fall einen neuen eindeutigen Namen ein.|
    |Image|Wählen Sie ggf. ein Betriebssystemimage zur Verwendung auf dem Docker-Host aus. Wählen Sie in diesem Beispiel ein Ubuntu Server-Image aus. (Beachten Sie, dass ein Windows Server-Image nun in der Liste der verfügbaren Images enthalten ist.)|
    |Benutzername|Geben Sie einen eindeutigen Benutzernamen für den virtuellen Computer ein.|
    |Kennwörter|Geben Sie ein Kennwort für den Benutzer ein, und bestätigen Sie es dann.|
    |Zertifikatverzeichnis |Hiermit wird der Ordner angegeben, in dem Ihre Docker-Zertifikate gespeichert werden. Sie können einen neuen Ordner erstellen oder auf einen vorhandenen Ordner verweisen. Es wird aber empfohlen, dass Sie den Standardordner für Zertifikate verwenden (C:\\Users\\ [*Benutzername*]\\.docker). Andernfalls können die Authentifizierungsoptionen nicht automatisch abgerufen werden, wenn Sie den Host in einem anderen Projekt oder System wiederverwenden.|

1. Wählen Sie die Schaltfläche mit den Auslassungspunkten (...) neben dem Eintrag **Zertifikatverzeichnis** aus, und erstellen Sie dann einen neuen Ordner für Docker-Zertifikate, oder navigieren Sie zu einem vorhandenen Ordner für Docker-Zertifikate.

    Wenn die für den virtuellen Computer erforderlichen Docker-Zertifikate nicht gefunden werden, wird ein Ausrufezeichen neben dem Eintrag angezeigt. Dadurch erfahren Sie, dass die erforderlichen Zertifikate nicht gefunden wurden. Wenn Sie fortfahren, werden vorhandene Zertifikate gelöscht und erneut erstellt.

1. Wählen Sie die Schaltfläche **OK** aus, um mit dem Erstellen des virtuellen Computers zu beginnen. Sie erhalten eine Meldung, dass der virtuelle Computer in Azure erstellt wird.

    Visual Studio erstellt eine Azure-Ressourcen-Manager-Vorlagendatei (ARM), eine Parameterdatei und ein PowerShell-Skript, damit die Befehle später erneut ausgeführt werden können.

    ![][7]

    Visual Studio gibt den Fortschritt des Vorgangs im Fenster **Ausgabe** aus. Visual Studio ruft ein PowerShell-Skript auf, um den virtuellen Computer bereitzustellen. Das Skript verwendet Azure PowerShell-Cmdlets zum Bereitstellen der Azure-Ressourcengruppe. Später verwendet ein weiteres PowerShell-Skript Docker-Befehle zum Veröffentlichen, so wie Sie es beim manuellen Erstellen des Docker-Hosts machen würden.

    Die Bereitstellung des Docker-Hosts kann eine Weile dauern. Überprüfen Sie also den Status im Ausgabefenster, um herauszufinden, wann der Auftrag abgeschlossen ist.

1. Nachdem der Docker-Host vollständig in Azure bereitgestellt wurde, können Sie Ihr Konto im Azure-Portal überprüfen. Sie sollten den neuen virtuellen Computer unter der Kategorie **Virtueller Computer** im Azure-Portal sehen.

1. Sobald der Docker-Host bereit ist, veröffentlichen Sie das Web-App-Projekt. Wählen Sie im Kontextmenü für den Knoten des Webanwendungsprojekts im **Projektmappen-Explorer** die Option **Veröffentlichen** aus. Visual Studio erstellt eine Veröffentlichungsdatei basierend auf dem virtuellen Computer, den Sie erstellt haben.

1. Aktivieren Sie auf der Registerkarte **Verbindung** im Dialogfeld **Web veröffentlichen** das Feld **Verbindung prüfen**, um sicherzustellen, dass der Docker-Host bereit ist. Wenn die Verbindung in Ordnung ist, wählen Sie die Schaltfläche **Veröffentlichen** aus, um die Web-App zu veröffentlichen.

    Wenn Sie zum ersten Mal eine App auf einem Docker-Host veröffentlichen, erfordert es Zeit, Basisimages herunterzuladen, auf die in der Docker-Datei verwiesen wird (z. B. **VON** *Imagename*).

    Beachten Sie, dass die Docker-Datei für das Betriebssystem spezifisch ist. Wenn Sie eine erneute Veröffentlichung in einem anderen Betriebssystem durchführen möchten, müssen Sie die Docker-Datei umbenennen, damit Visual Studio eine neue Standarddatei basierend auf dem Zielbetriebssystem erstellen kann. Beispiel: Wenn Sie zuerst in einem Linux-Container veröffentlichen und später in Windows veröffentlichen möchten, sollten Sie die Docker-Datei mit einem eindeutigen Namen wie DockerLinux umbenennen. Wenn Sie dann die Veröffentlichung in Windows erneut durchführen, erstellt Visual Studio die Docker-Standarddatei für Windows neu. Bei einer späteren erneuten Veröffentlichung einer der beiden Versionen wählen Sie einfach die entsprechende Docker-Datei für das Betriebssystem aus.

## Bereitstellen eines benutzerdefinierten Docker-Hosts

Im vorherigen Verfahren haben Sie einen virtueller Docker-Computer erstellt, der in Azure gehostet wird. Wenn Sie bereits über einen vorhandenen Docker-Host an anderer Stelle verfügen, können Sie ihn anstelle von Azure zum Veröffentlichen verwenden.

### So stellen Sie einen benutzerdefinierten Docker-Host bereit

1. Aktivieren Sie im Dialogfeld **Virtuellen Docker-Computer auswählen** das Kontrollkästchen **Benutzerdefinierter Docker-Host**.

    ![][5]

1. Klicken Sie auf die Schaltfläche **OK**.

1. Fügen Sie im Dialogfeld **Web veröffentlichen** Werte zu den Einstellungen im Abschnitt **CustomDockerHost** hinzu, beispielsweise die Server-URL, den Imagenamen, den Speicherort der Docker-Datei sowie Host- und Containerportnummern.

    Im Abschnitt **Docker – Erweiterte Optionen** können Sie Optionen für die Authentifizierung und Ausführung sowie die Docker-Befehlszeile anzeigen oder ändern.

    ![][6]

1. Nachdem Sie alle erforderlichen Werte eingegeben haben, wählen Sie die Schaltfläche **Verbindung prüfen** aus, um sicherzustellen, dass die Verbindung mit dem Docker-Host ordnungsgemäß funktioniert.

1. Wenn die Verbindung ordnungsgemäß funktioniert, können Sie die Schaltfläche **Weiter** auswählen, um eine Liste der Komponenten anzuzeigen, die veröffentlicht werden, oder Sie können die Schaltfläche **Veröffentlichen** auswählen, um das Projekt sofort zu veröffentlichen.

## Testen des Docker-Hosts

Nachdem das Projekt jetzt auf einem Docker-Host in Azure veröffentlicht wurde, können Sie es testen, indem Sie die Standardeinstellungen überprüfen. Da die Docker-Befehlszeilentools mit der Visual Studio-Erweiterung installiert werden, können Sie direkt über eine Windows-Befehlszeile Befehle an Docker ausgeben.

Das folgende Verfahren gilt für die Kommunikation mit einem Docker-Host, der in Azure bereitgestellt worden ist.

### So testen Sie den Docker-Host

1. Öffnen Sie eine Windows-Eingabeaufforderung.

1. Weisen Sie den Docker-Host zu, und überprüfen Sie die Umgebungsvariablen. Geben Sie dazu die folgenden Befehle an der Eingabeaufforderung ein. (Ersetzen Sie *NameofAzureVM* durch den Namen des Docker-Hosts.)

    ```
    Set DOCKER_HOST=tcp://<NameofAzureVM>.cloudapp.net:2376
    Set DOCKER_TLS_VERIFY=1
    ```

    Wenn Sie diese Befehle aufrufen, müssen Sie nicht jedem Befehl, den Sie ausführen, `–H (Host) tcp://<NameofAzureVM>.cloudapp.net:2376` und `--TLSVERIFY` hinzufügen.

1. Jetzt können Sie Befehle wie die folgenden ausgeben, um zu prüfen, ob der Docker-Host vorhanden ist und funktioniert.

    |Befehlszeile|Beschreibung|
    |---|---|
    |`docker info`|Abrufen der Docker-Versionsinformationen.|
    |`docker ps`|Abrufen einer Liste der ausgeführten Container.|
    |`docker ps –a`|Abrufen einer Liste von Containern, einschließlich der beendeten.|
    |`docker logs <Docker container name>`|Abrufen eines Protokolls für den angegebenen Container.|
    |`docker images`|Abrufen einer Liste von Images.|

    Eine vollständige Liste der Docker-Befehle erhalten Sie, indem Sie einfach den Befehl `docker` an der Eingabeaufforderung eingeben. Weitere Informationen finden Sie unter [Docker Command Line](https://docs.docker.com/reference/commandline/cli/) (in englischer Sprache).

## Nächste Schritte

Da Sie jetzt über einen Docker-Host verfügen, können Sie Docker-Befehle an ihn ausgeben. Weitere Informationen zu Docker finden Sie in der [Docker-Dokumentation](https://docs.docker.com/) und im [Docker-Onlinetutorial](https://www.docker.com/tryit/) (in englischer Sprache).

Informationen zur Verwendung der Docker VM-Erweiterung für Linux in Azure finden Sie unter [Die Docker-Erweiterung für virtuelle Linux-Computer in Azure](virtual-machines-docker-vm-extension.md).

Informationen zu Problemen bei der Verwendung von Docker in Visual Studio finden Sie unter [Problembehandlung von Docker-Clientfehlern unter Windows mithilfe von Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md).

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796678.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796679.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796680.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796681.png
[4]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796682.png
[5]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796683.png
[6]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796684.png
[7]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796685.png
[8]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796686.png

<!---HONumber=August15_HO9-->