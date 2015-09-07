<properties
   pageTitle="Problembehandlung bei Docker-Clientfehlern unter Windows mit Visual Studio | Microsoft Azure"
	description="Beheben Sie Probleme, die bei der Verwendung von Visual Studio unter Windows zum Erstellen und Bereitstellen von Web-Apps in Docker auftreten können."
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

# Problembehandlung von Docker-Fehlern

Nachdem Sie alle Einstellungen für den Docker-Container Ihrer App konfiguriert haben, sollten Sie sich überzeugen, dass die Einstellungen und Pfade richtig sind. Visual Studio bietet eine Schaltfläche "Überprüfen" im Dialogfeld „Veröffentlichen“, die Sie dabei unterstützt.

Dieses Thema hilft Ihnen, die häufigsten Probleme beim Hosten von Visual Studio-Apps in Docker zu diagnostizieren und entweder zu beheben oder zu umgehen. Dieses Thema wird laufend um weitere Probleme erweitert, sowie sie bekannt werden.

## Sie erhalten eine Fehlerbenachrichtigung, wenn Sie versuchen, die Verbindung mit Ihrem Docker-Host im Dialogfeld "Web veröffentlichen" zu überprüfen

Für dieses Problem gibt es eine Reihe möglicher Lösungen.

- Überprüfen Sie im Dialogfeld **Veröffentlichen** auf der Registerkarte **Verbindung**, ob die **Server-URL** richtig und die `:<port_number>` am Ende der **Server-URL** der Port ist, an dem der Docker-Daemon lauscht.

- Erweitern Sie im Dialogfeld **Veröffentlichen** auf der Registerkarte **Verbindung** den Abschnitt **Docker – Erweiterte Optionen**, und stellen Sie sicher, dass die richtigen **Authentifizierungsoptionen** angegeben wurden.
  - Wenn der Docker-Daemon auf dem Server für die Verwendung von TLS-Sicherheit konfiguriert ist, sucht die Windows-Befehlszeilenschnittstelle von Docker (docker.exe) standardmäßig im Ordner `<%userprofile%>\.docker` nach dem Clientschlüssel (key.pem) und dem Zertifikat (cert.pem). Wenn diese Elemente nicht vorhanden sind, müssen sie mithilfe von OpenSSL erstellt werden. Weitere Informationen zum Konfigurieren von Docker für TLS finden Sie unter [Protect the Docker Daemon Socket with HTTPS](https://docs.docker.com/articles/https/) (in englischer Sprache).

	Eine Möglichkeit sicherzustellen, dass Docker ordnungsgemäß vom Windows-Client beim Linux-Server authentifiziert wird, besteht darin, den Inhalt des Textfelds "Vorschau" in ein neues Befehlsfenster zu kopieren und `<command>` in "Info" zu ändern, wie hier gezeigt:

    ```
    // This example assumes the Docker daemon is configured to use the default port
    // of 2376 to listen for connections.docker.

    --tls -H tcp://contoso.cloudapp.net:2376 info
    ```

    Alternativ zum Kopieren der Clientzertifikat- und Schlüsseldateien in den Ordner ".docker" können Sie die **Authentifizierungsoptionen** durch Hinzufügen der folgenden Parameter ändern:

    ```
    --tls --tlscert=C:\mycert\cert.pem --tlskey=C:\mycert\key.pem
    ```
- Stellen Sie sicher, dass der Docker-Daemon auf dem Docker-Hostcomputer Version 1.6 oder höher ist.

## Timeoutfehler beim Verwenden eigener Zertifikate ohne Clientzertifikat im Docker-Ordner

Wenn Sie zum Erstellen des Docker-Hosts in Visual Studio eigene Zertifikate verwenden möchten (also im Dialogfeld **Virtuellen Computer in Microsoft Azure erstellen** das Kontrollkästchen **Docker-Zertifikate automatisch erstellen** deaktivieren), müssen Sie die Clientzertifikat- und Schlüsseldateien (cert.pem bzw. key.pem) in den Docker-Ordner (`<%userprofile%>\.docker`) kopieren. Andernfalls erhalten Sie beim Veröffentlichen Ihres Projekts nach einer Stunde einen Timeoutfehler, und der Veröffentlichungsvorgang schlägt fehl.

## PowerShell 3.0 ist zum Veröffentlichen in Docker-Containern erforderlich

Wenn Sie Windows 7 oder Windows Server 2008 als Betriebssystem verwenden, müssen Sie zuerst PowerShell 3.0 installieren, bevor Sie in Docker-Containern veröffentlichen können. PowerShell 3.0 ist im [Windows Management Framework 3.0](https://www.microsoft.com/de-DE/download/details.aspx?id=34595) enthalten. Nach der Installation müssen Sie das System neu starten.

Als alternative Problemumgehung können Sie ein Upgrade auf Windows 8.1 oder Windows 10 ausführen, in denen PowerShell 3.0 bereits enthalten ist.

## Das PowerShell-Fenster wird nicht automatisch geschlossen

Nach dem Erstellen eines virtuellen Computers wird das PowerShell-Fenster manchmal nicht automatisch geschlossen. Durch Schließen dieses Fensters wird auch Visual Studio geschlossen. Da sich das Fenster nicht auf Visual Studio- oder Docker-Funktionen auswirkt, lassen Sie es geöffnet, bis Sie Ihre Arbeit abgeschlossen haben.

## Häufig gestellte Fragen

F: Wie erstelle ich einen neuen Docker-fähigen Linux-Computer mithilfe der Visual Studio-Tools in Azure?

A: Informationen dazu finden Sie unter [Hosten von Web-Apps in Docker](vs-azure-tools-docker-hosting-web-apps-in-docker.md).

F: Welche Visual Studio-Projektvorlagen werden zur Veröffentlichung in einem Linux-Docker-Container unterstützt?

A: Visual Studio unterstützt aktuell die Webvorlagen für C#-Konsolenanwendung (Paket) und C#-ASP.NET 5-Vorschau, einschließlich:

- Leer

- Web-API

- Webanwendung.

F: Wie veröffentliche ich ein ASP.NET 5-Web- oder -Konsolenprojekt in Docker mithilfe von MSBUILD über die Befehlszeile?

A: Verwenden Sie den folgenden MSBUILD-Befehl:

    `msbuild <projectname.xproj> /p:deployOnBuild=true;publishProfile=<profilename>`

F: Wie veröffentliche ich ein ASP.NET 5-Web- oder -Konsolenprojekt in Docker mithilfe von PowerShell über die Befehlszeile?

A: Verwenden Sie den folgenden PowerShell-Befehl:

```
.\contoso-Docker-publish.ps1 -packOutput $env:USERPROFILE\AppData\Local\Temp\PublishTemp -pubxmlFile .\contoso-Docker.pubxml
```

F: Ich habe einen eigenen Linux-Server, auf dem Docker installiert ist. Wie gebe ich das im Dialogfeld **Veröffentlichen im Web** an?

A: Lesen Sie den Abschnitt **Bereitstellen eines benutzerdefinierten Docker-Hosts** im Thema [Hosten von Web-Apps in Docker](vs-azure-tools-docker-hosting-web-apps-in-docker.md).

F: Ich verwende einen eigenen Linux-Server, auf dem Docker installiert ist. Wie kann ich Schlüssel und Zertifikate generieren, um die Authentifizierung mithilfe von TLS zu konfigurieren?

A: Eine Möglichkeit besteht darin, OpenSSL auf dem Server zu verwenden, um die erforderlichen Zertifikate und Schlüssel für die Zertifizierungsstelle, den Server und den Client zu erzeugen. Anschließend können Sie Software von Drittanbietern verwenden, um eine SSH/SFTP-Verbindung herzustellen, und dann die Zertifikate auf den lokalen Windows-Entwicklungscomputer kopieren. Standardmäßig versucht die Docker-Befehlszeilenschnittstelle, die Zertifikate im Ordner `<userprofile>\.docker` zu verwenden.

Eine weitere Option besteht darin, OpenSSL für Windows herunterzuladen die erforderlichen Zertifikate und Schlüssel zu generieren und anschließend die Zertifizierungsstelle, die Serverzertifikate und die Schlüssel auf den Linux-Computer hochzuladen. Weitere Informationen zum Einrichten einer sicheren Verbindung mit Docker finden Sie unter [Protect the Docker Daemon Socket mit HTTPS](https://docs.docker.com/articles/https/) (in englischer Sprache).

<!---HONumber=August15_HO9-->