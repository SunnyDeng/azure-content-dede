<properties
   pageTitle="Gewusst wie: Integrieren von OneDrive for Business und Azure RemoteApp | Microsoft Azure"
   description="Erfahren Sie, wie Sie OneDrive for Business mit Azure RemoteApp verwenden."
   services="remoteapp"
   documentationCenter=""
   authors="pavithir"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="01/13/2016"
   ms.author="elizapo"/>

# Gewusst wie: Integrieren von OneDrive for Business und Azure RemoteApp

Sie können OneDrive for Business als Dateirepository mit Azure RemoteApp verwenden. OneDrive for Business ist eine hervorragende Möglichkeit, Dateien auf allen Geräten und in allen Arbeitsbereichen synchronisiert zu halten. Auf dem [Benutzerprofil-Datenträger](remoteapp-upd.md) können Benutzer ihre Dateien für Azure RemoteApp-Apps speichern. Auf diese Dateien kann dann jedoch nur mithilfe von Azure RemoteApp zugegriffen werden. Mit OneDrive for Business hingegen können Benutzer jederzeit und überall auch ohne Azure RemoteApp auf Dateien zugreifen. Dieser Artikel behandelt die unterstützten OneDrive for Business-Versionen und die verschiedenen Methoden, die Administratoren zum Einrichten von OneDrive for Business für Azure RemoteApp zur Verfügung stehen.

## Werden alle Versionen von OneDrive unterstützt?

Es gibt zwei Versionen von OneDrive: OneDrive und OneDrive for Business. Für Azure RemoteApp wird nur OneDrive for Business unterstützt. Die persönliche OneDrive-Version funktioniert, wird jedoch nicht offiziell unterstützt. Darüber hinaus wird nur die neueste Version von OneDrive for Business, also der Synchronisierungsclient der nächsten Generation, für Azure RemoteApp (und RDSH-/Citrix-/Terminalserver) unterstützt.

>[AZURE.NOTE]OneDrive (für Verbraucher/persönliche Edition) wird für Azure RemoteApp nicht unterstützt. Darüber hinaus werden nicht alle Versionen von OneDrive for Business unterstützt, da sie noch nicht für Windows Server zertifiziert wurden. Obwohl der neue Client (Synchronisierungsclient der nächsten Generation) und die älteren Groove-Versionen scheinbar einwandfrei mit Azure RemoteApp funktionieren, bieten ältere Synchronisierungsmodule bei Citrix-/Terminalservern (Windows Server) nicht den vollen Funktionsumfang (siehe Artikel [https://support.microsoft.com/de-de/kb/2965687](https://support.microsoft.com/kb/2965687)). Verwenden Sie den neuen Synchronisierungsclient für Azure RemoteApp (und andere Windows Server-Bereitstellungen).

## Welche unterschiedlichen Einrichtungsoptionen für OneDrive for Business stehen zur Verfügung?

- **Herkömmliche Einrichtung des OneDrive for Business-Synchronisierungsmoduls:**
Der OneDrive for Business-Synchronisierungsclient kann für eine Server-SKU (Remotedesktop- sowie RemoteApp-Sitzung/Terminalserversitzung) und Ordner installiert werden, die für die Synchronisierung in der RemoteApp-Sitzung ausgewählt wurden. Die Vorgehensweise entspricht der bei einer Windows-Client-SKU. Der Standardspeicherort, in dem OneDrive for Business Dateien synchronisiert, entspricht dem Speicherort, in dem sich der Benutzerprofil-Datenträger befindet, der zum Speichern von Benutzerdaten und -einstellungen in Azure RemoteApp verwendet wurde: C:\\users<Benutzername>. Dieser Datenträger folgt dem Benutzer auf jede VM, bei der er sich anmeldet. Daher stehen dem Benutzer auch die ODB-Dateien zur Verfügung. Die OneDrive for Business-App muss durch den Administrator für alle Benutzer veröffentlicht werden. Benutzer müssen sie für jede neue Sitzung starten (der Start kann auch mit einem Anmeldeskript automatisiert werden), um sicherzustellen, dass das Synchronisierungsmodul aktiviert wird. OneDrive for Business lädt die gesamte Datei auf die VM herunter, auf der die Sitzung ausgeführt wird. Die Workload beim Synchronisieren aller Inhalte eines Benutzers ist sehr hoch (CPU/übertragene Daten/beanspruchter Speicherplatz). Dieser Vorgang ist daher für Terminalcomputer, bei denen sich eine große Anzahl von Benutzern kurz bei den einzelnen Maschinen anmelden, nicht optimal geeignet. Dank der selektiven Synchronisierung kann die Workload reduziert werden, gewisse Bedenken bleiben jedoch bestehen.
- **„Virtualisieren“ von OneDrive for Business/Umleitung vom lokalen Fat Client-Gerät in die Sitzung:** 
Bei der Synchronisierung von OneDrive in einen Ordner auf dem Clientgerät können Sie auf einem Laufwerk die [Umleitung](remoteapp-redirection.md) dieses Laufwerks zu Azure RemoteApp auswählen. Das Laufwerk muss auf allen Clients der Benutzer gleich sein, und OneDrive muss in einen Ordner auf diesem Laufwerk synchronisiert werden. Wenn Benutzer von einem anderen Client auf RemoteApp zugreifen, sind diese Dateien möglicherweise nicht verfügbar (Problemumgehung: Sie können auf die Dateien jederzeit mithilfe der Onlineversion von OneDrive zugreifen.). 
- **Darstellen von OneDrive for Business als Laufwerk in der Azure RemoteApp-Umgebung ohne Zwischenspeicherung/Synchronisierung der Dateien:** 
(HTTP-URL von OneDrive for Business einem Laufwerk auf der VM zuordnen) 
Das Zuordnen von OneDrive for Business zum Netzlaufwerk in der RDSH-Umgebung wird unterstützt. Szenarien, in denen diese Methode verwendet werden kann: 
	- Bei der Verwendung von Thin Client-Geräten (kein lokaler Speicher) für den Zugriff auf Azure RemoteApp: Dateien müssen auf OneDrive for Business gespeichert sein, müssen für die Anwendung jedoch „lokal“ erscheinen, und der Administrator möchte die Dateien nicht auf einer VM synchronisieren.
	- Bei zahlreichen großen Dateien auf OneDrive for Business, die für die Synchronisierung ausgewählt wurden. Angesichts der Workload bei der Synchronisierung werden möglicherweise nicht alle Dateien synchronisiert, wenn der Benutzer sie verwenden möchten. Wenn die gesamte Größe der Dateien 50 GB überschreitet, können sie nicht auf dem Benutzerprofil-Datenträger gespeichert werden.

In den oben genannten Szenarien können Administratoren die Optionen zum Zuordnen eines Laufwerks auf der VM zur OneDrive for Business-HTTP-URL des Benutzers nutzen. Mögliche Optionen:

- Office-Binärdateien im Image speichern und das OneDrive for Business-Synchronisierungsmodul nicht aktivieren
- KEINE Office-Binärdateien ins Image aufnehmen. In diesem Fall ist das Paket für die Desktopdarstellung erforderlich. Das bedeutet, dass der WebClient-Dienst (d. h. WebDAV) als Teil des Pakets für die Desktopdarstellung installiert werden muss. 

### Installieren des Pakets für die Desktopdarstellung unter Windows Server 2012 R2
So installieren Sie das Paket für die Desktopdarstellung:

1. Klicken Sie im Server-Manager auf **Verwalten -> Rollen und Features hinzufügen**.
2. Klicken Sie auf **Features** und anschließend auf **Benutzeroberflächen und Infrastruktur -> Desktopdarstellung**.

### Zuordnen eines Laufwerks zur OneDrive for Business-URL

Befolgen Sie die Anweisungen im Supportartikel
[https://support.microsoft.com/kb/2616712](https://support.microsoft.com/kb/2616712).
 
Wichtig: Bei der Einrichtung müssen Sie **Angemeldet bleiben** auswählen.

Allgemeine Anweisungen:

1.	Suchen Sie die URL für das Laufwerk. Die URL für die Laufwerkzuordnung erhalten Sie, wenn Sie online zum Basisverzeichnis in OneDrive for Business navigieren. Beispiel:
 
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com/_layouts/15/onedrive.aspx?AjaxDelta=1&isStartPlt1=something
2.	Öffnen Sie die URL mit einem Browser in der RemoteApp-Sitzung, und wählen Sie **Angemeldet bleiben**, bevor Sie sich unter der URL für Ihr Konto anmelden.
3.	Öffnen Sie Windows-Explorer, und ordnen Sie der oben genannten URL ein Laufwerk zu. Wird die URL nicht aufgelöst, kann die kürzere Form verwendet werden:
	
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com. 

	Dadurch wird das zugeordnete Laufwerk umgehend erstellt. Es sieht folgendermaßen aus:
 
	![OneDrive for Business als zugeordnetes Netzlaufwerk](./media/remoteapp-onedrive/ra-mappeddrive.png)

<!---HONumber=AcomDC_0121_2016-->