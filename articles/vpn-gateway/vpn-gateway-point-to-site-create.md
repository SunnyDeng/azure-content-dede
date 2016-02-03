<properties
   pageTitle="Konfigurieren einer Punkt-zu-Standort-VPN-Verbindung mit Azure Virtual Network | Microsoft Azure"
   description="Stellen Sie eine sichere Verbindung mit Ihrem Azure Virtual Network durch Erstellen einer Punkt-zu-Standort-VPN-Verbindung her. Anweisungen für VNets, die mit dem Dienstverwaltungs-Bereitstellungsmodell (klassisch) erstellt wurden."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/11/2016"
   ms.author="cherylmc"/>

# Konfigurieren einer Punkt-zu-Standort-VPN-Verbindung mit einem VNet

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal - Classic](vpn-gateway-point-to-site-create.md)

Mit einer Punkt-zu-Standort-Konfiguration können Sie von einem Clientcomputer eine einzelne sichere Verbindung mit Ihrem virtuellen Netzwerk herstellen. Eine VPN-Verbindung wird hergestellt, indem Sie die Verbindung vom Clientcomputer aus starten. Dies ist eine hervorragende Lösung, wenn Sie von einem Remotestandort, z. B. von zu Hause oder in einer Konferenz, eine Verbindung mit Ihrem VNet herstellen möchten. Dieses Methode eignet sich auch, wenn Sie nur wenige Clients besitzen, die mit einem virtuellen Netzwerk verbunden werden müssen. Damit Punkt-zu-Standort-Verbindungen funktionieren, ist kein VPN-Gerät und keine öffentliche IP-Adresse erforderlich. Weitere Informationen zu Punkt-zu-Standort-Verbindungen finden Sie unter [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-vpn-faq.md#point-to-site-connections) und [Informationen zu standortübergreifenden Verbindungen](vpn-gateway-cross-premises-options.md).

Dieser Artikel gilt für Punkt-zu-Standort-VPN-Gatewayverbindungen mit einem virtuellen Netzwerk, das mit dem **klassischen Bereitstellungsmodell** (Dienstverwaltung) erstellt wurde. Wenn Sie eine Punkt-zu-Standort-Verbindung für ein VNet konfigurieren möchten, das mit dem Ressourcen-Manager erstellt wurde, lesen Sie die Informationen unter [Konfigurieren einer Punkt-zu-Standort-VPN-Verbindung mit einem virtuellen Netzwerk mithilfe von PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 


## Informationen zum Erstellen einer Punkt-zu-Standort-Verbindung
 
Die folgenden Abschnitte führen Sie schrittweise durch den Erstellungsprozess einer sicheren Punkt-zu-Standort-Verbindung mit einem virtuellen Netzwerk. Auch wenn zum Konfigurieren einer Punkt-zu-Standort-Verbindung mehrere Schritte erforderlich sind, ist es eine hervorragende Möglichkeit, eine sichere Verbindung von Ihrem Computer mit dem virtuellen Netzwerk herzustellen, ohne ein VPN-Gerät kaufen und konfigurieren zu müssen.

Die Konfiguration einer Punkt-zu-Standort-Verbindung ist in drei Abschnitte unterteilt. **Abschnitt 1** erläutert die Erstellung eines virtuellen Netzwerks und VPN-Gateways. **Abschnitt 2** bietet wichtige Informationen zum Erstellen der für die Authentifizierung erforderlichen Zertifikate. **Abschnitt 3** beschreibt die Schritte, die für den VPN-Client durchgeführt werden müssen, der zur Verbindung mit dem virtuellen Netzwerk verwendet wird. Da die Reihenfolge wichtig ist, in der Sie diese Komponenten konfigurieren, dürfen Sie keine Schritte überspringen oder vorgreifen.

## Abschnitt 1: Erstellen des virtuellen Netzwerks und eines VPN-Gateways

Eine Punkt-zu-Standort-Verbindung erfordert ein virtuelles Netzwerk mit einem Gateway mit dynamischem Routing. Die folgenden Schritte führen Sie durch diesen Prozess:

Schritt 1: Erstellen eines virtuellen Netzwerks

Schritt 2: Erstellen eines Gateways mit dynamischem Routing

### Erstellen eines virtuellen Netzwerks

1. Melden Sie sich beim **klassischen Azure-Portal** an (nicht beim Azure-Portal).
1. Klicken Sie unten links auf dem Bildschirm auf **Neu**. Klicken Sie im Navigationsbereich auf **Netzwerkdienste** und dann auf **Virtuelles Netzwerk**. Klicken Sie auf **Custom Create**, um den Konfigurationsassistenten zu starten.
1. Geben Sie auf dem Bildschirm **Details des virtuellen Netzwerks** die folgenden Informationen ein, und klicken Sie dann auf den Vorwärtspfeil unten rechts.
	- **Name**: Name des virtuellen Netzwerks. Beispiel: „VNetEast“. Das ist der Name, auf den Sie beim Bereitstellen von virtuellen Computern und PaaS-Instanzen für dieses VNet verweisen müssen.
	- **Speicherort**: Der Speicherort steht in direkter Beziehung zu dem physischen Standort (Region), an dem sich Ihre Ressourcen (VMs) befinden soll. Wenn Sie z. B. möchten, dass sich die virtuellen Computer, die Sie für Ihr virtuelles Netzwerk bereitstellen, physisch in "USA West" befinden, wählen Sie diesen Speicherort aus. Sie können die Ihrem virtuellen Netzwerk zugeordnete Region nach dem Erstellen nicht mehr ändern.
1. Geben Sie auf der Seite **DNS Server und VPN-Konnektivität** die folgenden Informationen ein, und klicken Sie dann in der unteren rechten Ecke auf den Weiter-Pfeil.
	- **DNS-Server**: Geben Sie den Namen und die IP-Adresse des DNS-Servers ein, oder wählen Sie einen zuvor registrierten DNS-Server im Kontextmenü aus. Durch diese Einstellung wird kein DNS-Server erstellt. Sie bietet die Möglichkeit, die DNS-Server anzugeben, die Sie zur Namensauflösung für dieses virtuelle Netzwerk verwenden möchten. Wenn Sie den Standarddienst für die Namensauflösung in Azure verwenden möchten, lassen Sie diesen Abschnitt leer.
	- **Standort-zu-Standort-VPNs konfigurieren**: Aktivieren Sie dieses Kontrollkästchen.
1. Geben Sie auf der Seite **Punkt-zu-Standort-Konnektivität** den IP-Adressbereich an, aus dem die VPN-Clients eine IP-Adresse erhalten, wenn eine Verbindung besteht. Es gibt einige Regeln bezüglich der Adressbereiche, die Sie angeben können. Sie müssen unbedingt sicherstellen, dass der angegebene Bereich keine anderen Bereiche überlappt, die sich in Ihrem lokalen Netzwerk befinden.
1. Geben Sie die folgenden Informationen ein, und klicken Sie dann auf den Pfeil "Weiter".
 - **Adressraum**: Umfasst Start-IP und CIDR (Adressenanzahl).
 - **Adressraum hinzufügen**: Fügen Sie einen Adressraum nur hinzu, wenn es für den Netzwerkentwurf erforderlich ist.
1. Geben Sie auf der Seite **Adressräume des virtuellen Netzwerks** die Adressräume an, die für das virtuelle Netzwerk verwendet werden sollen. Dies sind die dynamischen IP-Adressen (DIPS), die den virtuellen Computern und anderen Rolleninstanzen zugewiesen werden, die Sie für dieses virtuelle Netzwerk bereitstellen. Es ist besonders wichtig, einen Bereich auszuwählen, der sich nicht mit den anderen Bereichen überschneidet, die für Ihr lokales Netzwerk verwendet werden. Sie müssen sich mit Ihrem Netzwerkadministrator abstimmen, der ggf. einen Bereich von IP-Adressen aus dem Adressraum Ihres lokalen Netzwerks reservieren muss, den Sie für Ihr virtuelles Netzwerk verwenden können.
1. Geben Sie die folgenden Informationen ein, und klicken Sie dann auf das Häkchen, um das virtuelle Netzwerk zu erstellen.
 - **Adressraum**: Fügen Sie den internen IP-Adressbereich hinzu, den Sie für dieses virtuelle Netzwerk verwenden möchten, einschließlich Start-IP und Anzahl. Es ist wichtig, einen Bereich auszuwählen, der sich nicht mit den anderen Bereichen überschneidet, die für Ihr lokales Netzwerk verwendet werden. Sie müssen sich mit Ihrem Netzwerkadministrator abstimmen, der ggf. einen Bereich von IP-Adressen aus dem Adressraum Ihres lokalen Netzwerks reservieren muss, den Sie für Ihr virtuelles Netzwerk verwenden können.
 - **Subnetz hinzufügen**: Zusätzliche Subnetze sind nicht erforderlich, aber Sie können ein getrenntes Subnetz für virtuelle Computer erstellen, die über statische DIPs verfügen sollen. Vielleicht möchten Sie jedoch auch Ihre virtuellen Computer in einem Subnetz zusammenfassen, das von anderen Rolleninstanzen getrennt ist.
 - **Gatewaysubnetz hinzufügen**: Das Gatewaysubnetz ist für ein Punkt-zu-Standort-VPN erforderlich. Klicken Sie auf diese Option, um das Gatewaysubnetz hinzuzufügen. Das Gatewaysubnetz wird nur für das Gateway des virtuellen Netzwerks verwendet.
1. Nachdem das virtuelle Netzwerk erstellt wurde, wird im klassischen Azure-Portal auf der Seite mit Netzwerken unter **Status** der Eintrag **Erstellt** angezeigt. Nachdem Ihr virtuelles Netzwerk erstellt wurde, können Sie das Gateway mit dynamischem Routing erstellen.

### Erstellen eines Gateways mit dynamischem Routing

Der Gatewaytyp muss als dynamisch konfiguriert werden. Gateways mit statischem Routing sind mit diesem Feature nicht einsetzbar.

1. Klicken Sie im klassischen Azure-Portal auf der Seite **Netzwerke** auf das virtuelle Netzwerk, das Sie gerade erstellt haben, und navigieren Sie zur Seite **Dashboard**.
1. Klicken Sie unten auf der Seite **Dashboard** auf **Gateway erstellen**. Es wird eine Meldung mit der Frage angezeigt: **Möchten Sie ein Gateway für das virtuelle Netzwerk "Yournetwork" erstellen**. Klicken Sie auf **Ja**, um mit der Erstellung des Gateways zu beginnen. Es kann bis zu 15 Minuten dauern, bis das Gateway erstellt worden ist.

## Abschnitt 2: Generieren und Hochladen von Zertifikaten

Zertifikate werden zur Authentifizierung von VPN-Clients für Punkt-zu-Standort-VPNs verwendet. Sie können von einer Unternehmenszertifikatlösung erstellte Zertifikate sowie selbstsignierte Zertifikate verwenden. Sie können bis zu 20 Stammzertifikate in Azure hochladen.

Wenn Sie ein selbstsigniertes Zertifikat verwenden möchten, beachten Sie Erläuterung der folgenden Schritte des Prozesses. Wenn Sie beabsichtigen, eine Unternehmenszertifikatlösung zu verwenden, werden die Schritte in den einzelnen Abschnitten abweichen, aber dennoch müssen Sie folgende Aktionen ausführen:


Schritt 1: Identifizieren oder Generieren eines Stammzertifikats

Schritt 2: Hochladen der Stammzertifikat-CER-Datei in Azure

Schritt 3: Generieren eines Clientzertifikats

Schritt 4: Exportieren und Installieren des Clientzertifikats


### Identifizieren oder Generieren eines Stammzertifikats

Wenn Sie keine Unternehmenszertifikatlösung verwenden, müssen Sie ein selbstsigniertes Stammzertifikat generieren. Die Schritte in diesem Abschnitt wurden für Windows 8 geschrieben. Schritte für Windows 10 finden Sie unter [Arbeiten mit selbstsignierten Stammzertifikaten für Punkt-zu-Standort-Verbindungen](vpn-gateway-certificates-point-to-site.md).

Eine Möglichkeit zum Erstellen eines x. 509-Zertifikats ist die Verwendung des Certificate Creation Tool (makecert.exe). Wenn Sie „makecert“ verwenden möchten, laden Sie das kostenlose Produkt [Microsoft Visual Studio Express](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) herunter, und installieren Sie es.

2. Navigieren Sie zum Ordner "Visual Studio-Tools", und starten Sie die Eingabeaufforderung als Administrator.
3. Mit dem Befehl im folgenden Beispiel wird ein Stammzertifikat im persönlichen Zertifikatspeicher auf Ihrem Computer erstellt und installiert. Zudem wird eine entsprechende *CER*-Datei erstellt, die Sie später in das klassische Azure-Portal hochladen.
4. Wechseln Sie zu dem Verzeichnis, in dem die CER-Datei generiert werden soll, und führen Sie den folgenden Befehl aus, wobei *RootCertificateName* für den Namen steht, den Sie für das Zertifikat verwenden möchten. Wenn Sie das folgende Beispiel ohne Änderungen ausführen, erhalten Sie ein Stammzertifikat und die zugehörige Datei *RootCertificateName.cer*.

>[AZURE.NOTE]Da Sie ein Stammzertifikat erstellt haben, aus dem Clientzertifikate generiert werden, ist es empfehlenswert, dass Sie dieses Zertifikat zusammen mit dessen privaten Schlüssel exportieren und an einem sicheren Ort speichern, von dem es wiederhergestellt werden kann.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### Hochladen der Stammzertifikat-CER-Datei in das klassische Azure-Portal

Sie müssen die entsprechende CER-Datei für jedes Stammzertifikat in Azure hochladen. Sie können bis zu 20 Zertifikate hochladen.

1. Wenn Sie zuvor ein Stammzertifikat generiert haben, wurde auch eine *CER*-Datei erstellt. Sie laden die Datei jetzt in das klassische Azure-Portal hoch. Beachten Sie, dass die CER-Datei nicht den privaten Schlüssel des Stammzertifikats enthält. Sie können bis zu 20 Stammzertifikate hochladen.
1. Klicken Sie im klassischen Azure-Portal auf der Seite **Zertifikate** für Ihr virtuelles Netzwerk auf **Ein Stammzertifikat hochladen**.
1. Suchen Sie auf der Seite **Zertifikat hochladen** nach der CER-Datei für das Stammzertifikat, und klicken Sie dann auf das Häkchen.

### Generieren eines Client-Zertifikats

Die folgenden Schritte gelten für das Generieren eines Clientzertifikats aus dem selbstsignierten Stammzertifikat. Die Schritte in diesem Abschnitt wurden für Windows 8 geschrieben. Schritte für Windows 10 finden Sie unter [Arbeiten mit selbstsignierten Stammzertifikaten für Punkt-zu-Standort-Verbindungen](vpn-gateway-certificates-point-to-site.md). Wenn Sie eine Unternehmenszertifikatlösung verwenden, befolgen Sie die Richtlinien für die Lösung, die Sie verwenden.

1. Öffnen Sie auf dem gleichen Computer, den Sie zum Erstellen des selbstsigniertes Stammzertifikats verwendet haben, ein Visual Studio-Eingabeaufforderungsfenster als Administrator.
2. Wechseln Sie zu dem Speicherort, an dem die Clientzertifikatdatei gespeichert werden soll. *RootCertificateName* steht für das selbstsignierte Stammzertifikat, das Sie generiert haben. Wenn Sie das folgende Beispiel ausführen (wobei Sie "RootCertificateName" in den Namen Ihres Stammzertifikats ändern), erhalten Sie ein Clientzertifikat mit dem Namen "ClientCertificateName" in Ihrem persönlichen Zertifikatspeicher.
3. Geben Sie den folgenden Befehl ein:

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Alle Zertifikate werden im persönlichen Zertifikatspeicher auf dem Computer gespeichert. Aktivieren Sie *Certmgr* zum Überprüfen. Sie können mit diesem Verfahren bei Bedarf beliebig viele Clientzertifikate generieren. Es wird empfohlen, dass Sie für alle Computer, die Sie mit dem virtuellen Netzwerk verbinden möchten, jeweils eindeutige Clientzertifikate erstellen.

### Exportieren und Installieren des Clientzertifikats

Sie müssen auf jedem Computer, den Sie mit dem virtuellen Netzwerk verbinden möchten, ein Clientzertifikat installieren. Die folgenden Schritte führen Sie durch die manuelle Installation des Clientzertifikats.

1. Auf jedem Computer, den Sie mit dem virtuellen Netzwerk verbinden möchten,muss ein Clientzertifikat installiert werden. Dies bedeutet, dass Sie wahrscheinlich mehrere Clientzertifikate erstellen und diese dann exportieren müssen. Verwenden Sie zum Exportieren der Clientzertifikate *certmgr.msc*. Klicken Sie mit der rechten Maustaste auf das Clientzertifikat, das Sie exportieren möchten, klicken Sie auf **Alle Aufgaben** und anschließend auf **Exportieren**.
2. Exportieren Sie das *Clientzertifikat* mit dem privaten Schlüssel. Dies ist eine *PFX*-Datei. Vergessen Sie nicht, sich das Kennwort (Schlüssel) zu notieren oder zu merken, das Sie für dieses Zertifikat festgelegt haben.
3. Kopieren Sie die *PFX*-Datei auf den Clientcomputer. Doppelklicken Sie auf dem Clientcomputer auf die *PFX*-Datei, um sie zu installieren. Geben Sie das Kennwort ein, wenn Sie dazu aufgefordert werden. Ändern Sie den Speicherort der Installation nicht.

## Abschnitt 3: Konfigurieren Ihres VPN-Clients

Zum Herstellen einer Verbindung mit dem virtuellen Netzwerk müssen Sie auch den VPN-Client konfigurieren. Der Client benötigt sowohl ein Clientzertifikat als auch die richtige VPN-Clientkonfiguration, um eine Verbindung herstellen zu können.

Um Ihren VPN-Client zu konfigurieren, führen Sie nacheinander Folgendes aus:

Schritt 1: Erstellen des VPN-Clientkonfigurationspakets.

Schritt 2: Installieren des VPN-Konfigurationspakets auf dem Client und Herstellen der Verbindung

Schritt 3: Überprüfen der Verbindung

### Erstellen des VPN-Clientkonfigurationspakets

1. Navigieren Sie im klassischen Azure-Portal auf der Seite **Dashboard** für Ihr virtuelles Netzwerk zum Menü „Auf einen Blick“ in der rechten Ecke, und klicken Sie auf das VPN-Paket für den Client, den Sie mit Ihrem virtuellen Netzwerk verbinden möchten.
2. 
Die folgenden Clientbetriebssysteme werden unterstützt:
 - Windows 7 (32 Bit und 64 Bit)
 - Windows Server 2008 R2 (nur 64 Bit)
 - Windows 8 (32 Bit und 64 Bit)
 - Windows 8.1 (32 Bit und 64 Bit)
 - Windows Server 2012 (nur 64 Bit)
 - Windows Server 2012 R2 (nur 64 Bit)
 - Windows 10

1. Wählen Sie das Downloadpaket für das Clientbetriebssystem aus, unter dem es installiert wird:
 - Wählen Sie für 32-Bit-Clients **32-Bit-Client-VPN-Paket herunterladen** aus.
 - Wählen Sie für 64-Bit-Clients **64-Bit-Client-VPN-Paket herunterladen** aus.
1. Die Erstellung des Clientpakets kann einige Minuten in Anspruch nehmen. Sobald das Paket fertiggestellt wurde, können Sie die Datei herunterladen. Die heruntergeladene *EXE*-Datei kann sicher auf dem lokalen Computer gespeichert werden.
1. Nachdem Sie das VPN-Clientpaket generiert und aus dem klassischen Azure-Portal heruntergeladen haben, können Sie das Clientpaket auf dem Clientcomputer installieren, von dem Sie eine Verbindung mit dem virtuellen Netzwerk herstellen möchten. Wenn Sie das VPN-Clientpaket auf mehreren Clientcomputern installieren möchten, stellen Sie sicher, dass auf diesen auch ein Clientzertifikat installiert worden ist. Das VPN-Clientpaket enthält Konfigurationsinformationen zum Konfigurieren der VPN-Client-Software, die in Windows integriert. Das Paket installiert keine zusätzlichen Software.

### Installieren des VPN-Konfigurationspakets auf dem Client und Herstellen der Verbindung

1. Kopieren Sie die Konfigurationsdatei lokal auf den Computer, den Sie mit dem virtuellen Netzwerk verbinden möchten, und doppelklicken Sie auf die EXE-Datei. Sobald das Paket installiert worden ist, können Sie die VPN-Verbindung starten. Beachten Sie, dass das Konfigurationspaket nicht von Microsoft signiert ist. Sie können das Paket mithilfe des Signaturdiensts Ihres Unternehmens oder mit [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327) selbst signieren. Sie können das Paket auch ohne Signatur verwenden. Wenn das Paket nicht signiert ist, wird jedoch bei der Installation des Pakets eine Warnung angezeigt.
2. Navigieren Sie auf dem Clientcomputer zu "VPN-Verbindungen", und suchen Sie die VPN-Verbindung, die Sie gerade erstellt haben. Sie hat den gleichen Namen wie das virtuelle Netzwerk. Klicken Sie auf **Verbinden**.
3. Eine entsprechende Meldung wird angezeigt, mit der ein selbstsigniertes Zertifikat für den Gateway-Endpunkt erstellt wird. Klicken Sie auf **Weiter**, um Administratorrechte zu nutzen.
4. Klicken Sie auf der Statusseite **Verbindung** auf **Verbinden**, um die Verbindung herzustellen.
5. Wenn der Bildschirm **Zertifikat auswählen** angezeigt wird, vergewissern Sie sich, dass das angezeigte Clientzertifikat dem Zertifikat entspricht, die Sie zum Herstellen der Verbindung verwenden möchten. Wenn dies nicht der Fall ist, verwenden Sie den Dropdownpfeil, um das richtige Zertifikat auszuwählen, und klicken Sie dann auf **OK**.
6. Sie sind jetzt mit dem virtuellen Netzwerk verbunden und haben vollen Zugriff auf alle Dienste und die virtuellen Computer, die im virtuellen Netzwerk gehostet werden.

### Überprüfen der VPN-Verbindung

1. Um sicherzustellen, dass die VPN-Verbindung aktiv ist, öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und führen Sie *Ipconfig/all* aus.
2. Zeigen Sie die Ergebnisse an. Beachten Sie, dass die IP-Adresse, die Sie erhalten, eine Adresse aus dem Adressbereich der Punkt-zu-Standort-Verbindung ist, den Sie beim Erstellen des virtuellen Netzwerks angegeben haben. Das Ergebnis sollte etwa wie folgt aussehen:

Beispiel:



    PPP adapter VNetEast:
		Connection-specific DNS Suffix .:
		Description.....................: VNetEast
		Physical Address................:
		DHCP Enabled....................: No
		Autoconfiguration Enabled.......: Yes
		IPv4 Address....................: 192.168.130.2(Preferred)
		Subnet Mask.....................: 255.255.255.255
		Default Gateway.................:
		NetBIOS over Tcpip..............: Enabled

## Nächste Schritte

Sie können dem virtuellen Netzwerk virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](../virtual-machines/virtual-machines-create-custom.md).

Weitere Informationen über virtuelle Netzwerke erhalten Sie unter [Dokumentation zu virtuellen Netzwerken](https://azure.microsoft.com/documentation/services/virtual-network/).

<!---HONumber=AcomDC_0121_2016-->