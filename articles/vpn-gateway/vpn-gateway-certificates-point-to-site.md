<properties 
   pageTitle="Erstellen selbstsignierter Zertifikate für standortübergreifende Konfigurationen für Punkt-zu-Standort-VPN-Gateway-Verbindungen mithilfe von MakeCert | Microsoft Azure"
   description="In diesem Artikel werden die Schritte erläutert, die zum Erstellen selbstsignierter Stammzertifikate unter Windows 10 mithilfe von MakeCert ausgeführt werden müssen."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/15/2016"
   ms.author="cherylmc" />

# Arbeiten mit selbstsignierten Stammzertifikaten für Punkt-zu-Standort-Konfigurationen

Die folgenden Aufgaben unterstützen Sie beim Erstellen eines Stammzertifikats mithilfe von MakeCert und beim Generieren von Clientzertifikaten aus dem Stammzertifikat. Die folgenden Schritte gelten für die Verwendung von MakeCert unter Windows 10.

## Erstellen eines selbstsignierten Stammzertifikats

MakeCert ist eine Möglichkeit zum Erstellen eines selbstsignierten Stammzertifikats. Die folgenden Schritte führen Sie durch die Erstellung eines selbstsignierten Stammzertifikats mithilfe von MakeCert. Diese Schritte gelten nicht spezifisch für ein Bereitstellungsmodell. Sie sind für das Ressourcen-Manager-Modell und das klassische Modell gültig.

### So erstellen Sie ein selbstsigniertes Stammzertifikat

1. Laden Sie auf einem Computer mit Windows 10 das [Windows Software Development Kit (SDK) für Windows 10](https://dev.windows.com/de-DE/downloads/windows-10-sdk) herunter, und installieren Sie es.

2. Nach der Installation finden Sie das Hilfsprogramm „makecert.exe“ unter folgendem Pfad: C:\\Programme (x86)\\Windows Kits\\10\\bin<arch>.
		
	Beispiel:
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. Als Nächstes erstellen und installieren Sie ein Stammzertifikat im Speicher für persönliche Zertifikate auf dem Computer. Im folgenden Beispiel wird eine entsprechende *CER*-Datei erstellt, die Sie später hochladen. Führen Sie den folgenden Befehl als Administrator aus, wobei *RootCertificateName* der Name ist, den Sie für das Zertifikat verwenden möchten.

	Hinweis: Wenn Sie das folgende Beispiel ohne Änderungen ausführen, erhalten Sie ein Stammzertifikat und die zugehörige Datei *RootCertificateName.cer*. Die CER-Datei finden Sie in dem Verzeichnis, in dem Sie den Befehl ausgeführt haben. Das Zertifikat befindet sich unter „Zertifikate - Aktueller Benutzer\\Eigene Zertifikate\\Zertifikate“.

    	makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

	>[AZURE.NOTE]Da Sie ein Stammzertifikat erstellt haben, aus dem Clientzertifikate generiert werden, ist es empfehlenswert, dass Sie dieses Zertifikat zusammen mit dessen privaten Schlüssel exportieren und an einem sicheren Ort speichern, von dem es wiederhergestellt werden kann.

## Erstellen, Exportieren und Installieren des Clientzertifikats

Sie können ein Clientzertifikat aus einem selbstsignierten Zertifikat generieren und es anschließend exportieren und installieren. Die folgenden Schritte gelten nicht spezifisch für ein Bereitstellungsmodell. Sie sind für das Ressourcen-Manager-Modell und das klassische Modell gültig.

### So generieren Sie ein Clientzertifikat aus einem selbstsignierten Stammzertifikat

Die folgenden Schritte führen Sie durch eine Möglichkeit, ein Clientzertifikat aus einem selbstsignierten Zertifikat zu generieren. Sie können mehrere Clientzertifikate aus demselben Stammzertifikat generieren. Jedes Clientzertifikat kann anschließend exportiert und auf dem Clientcomputer installiert werden.

1. Öffnen Sie auf dem gleichen Computer, den Sie zum Erstellen des selbstsignierten Stammzertifikats verwendet haben, eine Eingabeaufforderung als Administrator.

2. Wechseln Sie zu dem Speicherort, an dem die Clientzertifikatdatei gespeichert werden soll. *RootCertificateName* steht für das selbstsignierte Stammzertifikat, das Sie generiert haben. Wenn Sie das folgende Beispiel ausführen (wobei Sie "RootCertificateName" in den Namen Ihres Stammzertifikats ändern), erhalten Sie ein Clientzertifikat mit dem Namen "ClientCertificateName" in Ihrem persönlichen Zertifikatspeicher.

3. Geben Sie den folgenden Befehl ein:

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Alle Zertifikate werden im Zertifikatspeicher „Zertifikate - Aktueller Benutzer\\Eigene Zertifikate\\Zertifikate“ auf dem Computer gespeichert. Sie können mit diesem Verfahren bei Bedarf beliebig viele Clientzertifikate generieren.

### So exportieren Sie ein Clientzertifikat

1. Verwenden Sie *certmgr.msc* zum Exportieren eines Clientzertifikats. Klicken Sie mit der rechten Maustaste auf das Clientzertifikat, das Sie exportieren möchten, klicken Sie auf **Alle Aufgaben** und anschließend auf **Exportieren**. Dadurch wird der Zertifikatexport-Assistent geöffnet.

2. Klicken Sie im Assistenten auf **Weiter**, wählen Sie dann **Ja, privaten Schlüssel exportieren**, und klicken Sie dann auf **Weiter**.

3. Auf der Seite **Exportdateiformat** können Sie die Standardwerte übernehmen. Klicken Sie auf **Next**.
 
4. Auf der Seite **Sicherheit** müssen Sie den privaten Schlüssel schützen. Wenn Sie ein Kennwort verwenden möchten, sollten Sie sich das für dieses Zertifikat festgelegte Kennwort unbedingt merken oder notieren. Klicken Sie dann auf **Weiter**.

5. Wählen Sie unter **Zu exportierende Datei** die Option **Durchsuchen**, um zu dem Speicherort zu wechseln, in den das Zertifikat exportiert werden soll. Geben Sie unter **Dateiname** einen Namen für die Zertifikatdatei ein. Klicken Sie auf **Next**.

6. Klicken Sie auf **Fertig stellen**, um das Zertifikat zu exportieren.

### Installieren eines Clientzertifikats

Auf jedem Client, der über eine Punkt-zu-Standort-Verbindung mit Ihrem virtuellen Netzwerk verbunden werden soll, muss ein Clientzertifikat installiert sein. Dieses Zertifikat wird zusätzlich zum erforderlichen VPN-Konfigurationspaket benötigt. Die folgenden Schritte führen Sie durch die manuelle Installation des Clientzertifikats.

1. Suchen Sie die *PFX*-Datei, und kopieren Sie sie auf den Clientcomputer. Doppelklicken Sie auf dem Clientcomputer auf die *PFX*-Datei, um sie zu installieren. Lassen Sie den **Speicherort** auf **Aktueller Benutzer** eingestellt, und klicken Sie dann auf **Weiter**.

2. Nehmen Sie auf der Seite **Zu importierende Datei** keine Änderungen vor. Klicken Sie auf **Weiter**.

3. Geben Sie auf der Seite **Schutz durch privaten Schlüssel** ggf. das Kennwort für das Zertifikat ein, oder überprüfen Sie die Richtigkeit des Sicherheitsprinzipals, der das Zertifikat installiert, und klicken Sie dann auf **Weiter**.

4. Behalten Sie auf der Seite **Zertifikatspeicher** den Standardspeicherort bei, und klicken Sie dann auf **Weiter**.

5. Klicken Sie auf **Fertig stellen**. Klicken Sie in der **Sicherheitswarnung** für die Zertifikatinstallation auf **Ja**. Das Zertifikat wird nun erfolgreich importiert.

## Nächste Schritte

Setzen Sie die Punkt-zu-Standort-Konfiguration fort.

- Schritte für das Bereitstellungsmodell **Ressourcen-Manager** finden Sie unter [Konfigurieren einer Punkt-zu-Standort-VPN-Verbindung mit einem virtuellen Netzwerk mithilfe von PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
- Schritte für das **klassische ** Bereitstellungsmodell finden Sie unter [Konfigurieren einer Punkt-zu-Standort-VPN-Verbindung mit einem VNet](vpn-gateway-point-to-site-create.md).

<!---HONumber=AcomDC_0121_2016-->