<properties 
   pageTitle="StorSimple – Virtuelles Gerät"
   description="Erfahren Sie, wie Sie virtuellen StorSimple-Geräte mit Update 1.0 erstellen, konfigurieren, bereitstellen und verwalten."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/27/2015"
   ms.author="alkohli" />

# StorSimple – Virtuelles Gerät

[AZURE.INCLUDE [storsimple-version-selector-sva](../../includes/storsimple-version-selector-sva.md)]

##Übersicht
Das virtuelle StorSimple-Gerät ist eine zusätzliche, in der Microsoft Azure StorSimple-Lösung enthaltene Funktion. Das virtuelle StorSimple-Gerät wird auf einem virtuellen Computer in einem virtuellen Microsoft Azure-Netzwerk ausgeführt und kann zum Sichern und Klonen von Daten von Hosts verwendet werden. In den folgenden Themen erfahren Sie mehr über das Konfigurieren und Verwenden des virtuellen StorSimple-Geräts.



- Unterschiede zwischen virtuellem und physischem Gerät

- Sicherheitsüberlegungen für die Verwendung eines virtuellen Geräts

- Voraussetzungen für das virtuelle Gerät

- Erstellen und Konfigurieren des virtuellen Geräts

- Arbeiten mit dem virtuellen Gerät

- Ausführen eines Failovers auf das virtuelle Gerät

- Herunterfahren oder Löschen virtueller Geräte

Dieses Lernprogramm gilt für alle virtuellen StorSimple-Geräte mit Update 1.

## Unterschiede zwischen virtuellem und physischem Gerät

Das virtuelle StorSimple-Gerät ist eine reine Softwareversion von StorSimple, die auf einem einzelnen Knoten eines virtuellen Microsoft Azure-Computers ausgeführt wird. Das virtuelle Gerät unterstützt Notfallwiederherstellungsszenarios, in denen das physische Gerät nicht verfügbar ist. Zudem eignet es sich für Cloud-Entwicklungs- und Testszenarios.

### Unterschiede im Vergleich zum physischen Gerät

Im Folgenden werden einige wichtige Unterschiede zwischen virtuellen und physischen StorSimple-Geräten aufgeführt:

- Das virtuelle Gerät hat nur eine Netzwerkschnittstelle: DATA 0. Das physische Gerät verfügt über sechs Netzwerkschnittstellen: DATA 0 bis DATA 5.
- Das virtuelle Gerät wird während der Konfiguration und nicht im Rahmen einer separaten Aufgabe registriert.
- Der Schlüssel für die Dienstdatenverschlüsselung kann auf dem virtuellen Gerät nicht erneut generiert werden. Während des Schlüsselrollovers wird der Schlüssel auf dem physischen Gerät erneut generiert. Anschließend aktualisieren Sie das virtuelle Gerät mit dem neuen Schlüssel.
- Wenn Updates für das virtuelle Gerät angewendet werden müssen, treten anders als beim physischen Gerät Ausfallzeiten auf.

## Sicherheitsüberlegungen für die Verwendung eines virtuellen Geräts

Beachten Sie bei der Verwendung des virtuellen StorSimple-Geräts die folgenden Sicherheitsaspekte:

- Das virtuelle Gerät wird über das Microsoft Azure-Abonnement geschützt. Wenn Sie daher das virtuelle Gerät verwenden und Ihr Azure-Abonnement beeinträchtigt ist, sind auch die auf dem virtuellen Gerät gespeicherten Daten anfällig.

- Der öffentliche Schlüssel des Zertifikats zum Verschlüsseln von Daten in Azure StorSimple wird im Microsoft Azure-Verwaltungsportal sicher zur Verfügung gestellt, und der private Schlüssel wird auf dem StorSimple-Gerät beibehalten. Auf dem virtuellen StorSimple-Gerät werden die öffentlichen und privaten Schlüssel in Azure gespeichert.

- Das virtuelle Gerät wird im Microsoft Azure-Datencenter gehostet.


## Voraussetzungen für das virtuelle Gerät

In den folgenden Abschnitten wird erläutert, wie Sie das virtuelle StorSimple-Gerät zur Verwendung vorbereiten.

### Anforderungen für Azure

Bevor Sie das virtuelle Gerät bereitstellen, müssen Sie in Ihrer Umgebung die folgenden Vorbereitungen treffen:

- [Konfigurieren Sie ein virtuelles Netzwerk in Azure](https://msdn.microsoft.com/library/azure/jj156074.aspx) für das virtuelle Gerät. 
- Sie können die von Azure bereitgestellten DNS-Standardserver verwenden, anstatt einen eigenen DNS-Servernamen anzugeben. 
- Punkt-zu-Standort und Standort-zu-Standort sind optional, aber nicht erforderlich. Sie können diese Optionen gegebenenfalls für erweiterte Szenarios konfigurieren. 

>[AZURE.IMPORTANT]**Stellen Sie sicher, dass sich das virtuelle Netzwerk in der gleichen Region befindet, wie die mit dem Gerät zu verwendenden Cloud-Speicherkonten.**

- Erstellen Sie im virtuellen Netzwerk [virtuelle Azure-Computer](https://msdn.microsoft.com/library/azure/jj156003.aspx) (Hostserver). Diese Server müssen die folgenden Anforderungen erfüllen: 							
	- Es muss sich um virtuelle Windows- oder Linux-Computer mit iSCSI-Initiatorsoftware handeln.
	- Sie müssen im gleichen virtuellen Netzwerk wie das virtuelle Gerät ausgeführt werden.
	- Das iSCSI-Ziel des virtuellen Geräts muss über die interne IP-Adresse des virtuellen Geräts eine Verbindung herstellen können.

- Stellen Sie sicher, dass die iSCSI-Unterstützung und der Cloud-Datenverkehr im gleichen virtuellen Netzwerk konfiguriert wurden.

### StorSimple-Anforderungen

Nehmen Sie vor dem Erstellen eines virtuellen Geräts die folgenden Updates für den Azure StorSimple-Dienst vor:


- Fügen Sie den virtuellen Computern, die als Hostserver für Ihr virtuelles Gerät fungieren sollen, [Zugriffssteuerungsdatensätze](https://msdn.microsoft.com/library/1747f56e-858a-4cfe-a020-949d7db23b8b#sec02) hinzu.

- Sie benötigen Sie ein [Speicherkonto](storsimple-manage-storage-accounts.md) in derselben Region wie das virtuelle Gerät. Speicherkonten in anderen Regionen können zu Leistungseinbußen führen.

- Stellen Sie sicher, dass zum Erstellen des virtuellen Geräts ein anderes Speicherkonto verwendet wird als für die Daten. Die Verwendung ein und desselben Speicherkontos kann zu Leistungseinbußen führen.

Stellen Sie vorab sicher, dass die folgenden Informationen vorliegen:


- Sie verfügen über ein Konto für das Azure-Verwaltungsportal sowie über die Anmeldeinformationen.

- Sie verfügen über die Anmeldeinformationen für Ihr Azure-Speicherkonto.

- Sie verfügen über eine Kopie des Schlüssels für die Dienstdatenverschlüsselung des physischen Geräts.

- Sie verfügen über eine Kopie des Cloudspeicher-Verschlüsselungsschlüssels für die einzelnen Volumecontainer.


## Erstellen und Konfigurieren des virtuellen Geräts

Stellen Sie vor Durchführung dieser Schritte sicher, dass die [Voraussetzungen für das virtuelle Gerät](#Prerequisites-for-the-virtual-device) erfüllt sind.

Nach Abschluss dieser Schritte können Sie [mit dem virtuellen Gerät arbeiten](#Work-with-the-StorSimple-virtual-device).

### Erstellen des virtuellen Geräts

Nachdem Sie ein virtuelles Netzwerk erstellt, den StorSimple-Manager-Dienst konfiguriert und das physische StorSimple-Gerät beim Dienst registriert haben, können Sie mithilfe der folgenden Schritte ein virtuelles StorSimple-Gerät erstellen.

Führen Sie die folgenden Schritte durch, um das virtuelle StorSimple-Gerät zu erstellen:



1.  Wechseln Sie im Verwaltungsportal zum **StorSimple-Manager**-Dienst.

- Wechseln Sie zur Seite **Geräte**. Klicken Sie auf der Seite "Geräte" unten auf **Virtuelles Gerät erstellen**.

- Geben Sie im Dialogfeld **Virtuelles Gerät erstellen** Folgendes an:

	a. **Name** – ein eindeutiger Name für Ihr virtuelles Gerät.


	b. **Version** – wählen Sie die Version des virtuellen Geräts aus. Diese Option ist nicht vorhanden, wenn Sie nur physische Geräte mit Update 1 für diesen Dienst registriert haben. Dieses Feld wird nur angezeigt, wenn Sie eine Kombination aus physischen Geräten ohne und mit Update 1 im Dienst registriert haben. Angesichts der Tatsache, dass die Version des virtuellen Geräts angibt, von welchem physischen Gerät Sie ein Failover ausführen oder klonen können, ist es wichtig, eine geeignete Version des virtuellen Geräts zu erstellen. Auswahl:

	- Versionsupdate 0.3, wenn Sie ein Failover oder eine Notfallwiederherstellung von einem physischen Gerät mit GA-Releaseversion oder Updates von 0.1 bis 0.3 ausführen. 
	- Versionsupdate 1, wenn Sie ein Failover oder einen Klonvorgang von einem physischen Gerät mit Update 1 ausführen. 

 
	b. **Virtuelles Netzwerk** – der Name des virtuellen Netzwerks, das für dieses virtuelle Gerät verwendet werden soll.

	c. **Subnetz** – das Subnetz im virtuellen Netzwerk für die Verwendung mit dem virtuellen Gerät.

	d. **Speicherkonto zum Erstellen des virtuellen Geräts** – das Speicherkonto, das bei der Bereitstellung zum Speichern des Images des virtuellen Geräts verwendet wird. Dieses Speicherkonto sollte sich in der gleichen Region wie das virtuelle Gerät und das virtuelle Netzwerk befinden. Es sollte weder vom physischen noch vom virtuellen Gerät zum Speichern von Daten verwendet werden. Hierfür wird standardmäßig ein neues Speicherkonto erstellt. Wenn Sie jedoch bereits über ein hierfür geeignetes Speicherkonto verfügen, können Sie dieses aus der Liste auswählen.
	

>[AZURE.NOTE]Das virtuelle Gerät funktioniert nur mit Azure-Speicherkonten. Andere Clouddienstanbieter, wie z. B. Amazon, HP und OpenStack (die vom physischen Gerät unterstützt werden), werden für das virtuelle StorSimple-Gerät nicht unterstützt.
	
- Klicken Sie auf das Häkchen, um zu bestätigen, dass die auf dem virtuellen Gerät gespeicherten Daten in einem Microsoft-Datencenter gehostet werden. Wenn Sie lediglich ein physisches Gerät verwenden, wird der Verschlüsselungsschlüssel auf Ihrem Gerät beibehalten. Daher kann Microsoft dieses nicht entschlüsseln. Wenn Sie ein virtuelles Gerät verwenden, werden die Ver- und Entschlüsselungsschlüssel in Microsoft Azure gespeichert. Weitere Informationen finden Sie unter [Sicherheitsüberlegungen bei der Verwendung eines virtuellen Geräts](#security-considerations-for-using-a-virtual-device).

### Konfigurieren und Registrieren virtueller Geräte

Stellen Sie vorab sicher, dass Sie über eine Kopie des Verschlüsselungsschlüssels für die Dienstdaten verfügen. Der Schlüssel für die Dienstdatenverschlüsselung wurde beim Konfigurieren des ersten StorSimple-Geräts erstellt, und Sie wurden aufgefordert, diesen an einem sicheren Ort zu speichern. Wenn Sie über keine Kopie des Schlüssels für die Dienstdatenverschlüsselung verfügen, wenden Sie sich an den Microsoft-Support.

Führen Sie die folgenden Schritte durch, um das virtuelle StorSimple-Gerät zu konfigurieren und zu registrieren:


1. Wählen Sie das **virtuelle StorSimple-Gerät** als Ihr Gerät aus, und doppelklicken Sie darauf, um auf den Schnellstart zuzugreifen.

- Klicken Sie auf **Gerätesetup abschließen**. Dadurch wird der Konfigurationsassistent gestartet.

- Geben Sie den **Verschlüsselungsschlüssel für Dienstdaten** in das entsprechende Feld ein.

- Klicken Sie auf das Häkchen, um die Erstkonfiguration sowie die Registrierung des virtuellen Geräts abzuschließen. Das Geräteadministratorkennwort wird mit Standardwerten vorkonfiguriert und muss im Anschluss an die Geräteregistrierung geändert werden.

### Ändern der Konfigurationseinstellungen des Geräts

Im folgenden Abschnitt werden die Gerätekonfigurationseinstellungen beschrieben, die für virtuelle StorSimple-Geräte konfiguriert werden müssen.

#### Konfigurieren des CHAP-Initiators

Dieser Parameter enthält die Anmeldeinformationen, die Ihr virtuelles Gerät (Ziel) von den Initiatoren (Servern) erwartet, die auf die Volumes zugreifen. Die Initiatoren stellen einen CHAP-Benutzernamen und ein CHAP-Kennwort bereit, um sich bei der Authentifizierung bei Ihrem Gerät zu identifizieren.

#### Konfigurieren des CHAP-Ziels

Dieser Parameter enthält die Anmeldeinformationen, die Ihr virtuelles Gerät verwendet, wenn ein CHAP-fähiger Initiator eine wechselseitige oder bidirektionale Authentifizierung anfordert. Ihr virtuelles Gerät verwendet einen Benutzernamen und ein Kennwort für das Zurücksetzen von CHAP, um sich beim Authentifizierungsvorgang beim Initiator zu identifizieren. Beachten Sie, dass es sich bei den CHAP-Zieleinstellungen um globale Einstellungen handelt. Wenn diese angewendet werden, verwenden alle mit dem virtuellen Speichergerät verbundenen Volumes die CHAP-Authentifizierung.

#### Konfigurieren des StorSimple-Momentaufnahme-Managers

Der StorSimple-Momentaufnahme-Manager befindet sich auf dem Windows-Host und ermöglicht Administratoren die Verwaltung der Sicherungen Ihres StorSimple-Geräts in Form von lokalen und Cloud-Momentaufnahmen.

>[AZURE.NOTE]**Für das virtuelle Gerät handelt es sich beim Windows-Host um einen virtuellen Azure-Computer.**

Beim Konfigurieren eines Geräts im StorSimple-Momentaufnahme-Manager werden Sie aufgefordert, zur Authentifizierung des Speichergeräts die IP-Adresse und das Kennwort des StorSimple-Geräts anzugeben.

Führen Sie die folgenden Schritte durch, um den StorSimple-Momentaufnahme-Manager für die Verwendung mit dem virtuellen StorSimple-Gerät zu konfigurieren.

1. Wechseln Sie auf dem virtuellen Gerät zu **Geräte > Konfigurieren**.

- Scrollen Sie nach unten zum Abschnitt **Momentaufnahme-Manager**. Geben Sie ein Kennwort mit einer Länge von 14 oder 15 Zeichen ein. Stellen Sie sicher, dass das Kennwort aus einer Kombination von drei der vier folgenden Elementen besteht: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen.

- Bestätigen Sie das Kennwort.

- Klicken Sie unten auf der Seite auf **Speichern**.

Das Kennwort für den StorSimple-Momentaufnahme-Manager wird jetzt aktualisiert und kann für die Authentifizierung bei den Windows-Hosts verwendet werden.

#### Konfigurieren des Geräteadministratorkennworts

Wenn Sie über die Windows PowerShell-Benutzeroberfläche auf das virtuelle Gerät zugreifen, müssen Sie ein Kennwort eingeben. Für die Sicherheit Ihrer Daten müssen Sie dieses Kennwort ändern, bevor das virtuelle Gerät verwendet werden kann.

Führen Sie die folgenden Schritte durch, um das Geräteadministratorkennwort für das virtuelle StorSimple-Gerät zu konfigurieren.

1. Wechseln Sie auf dem virtuellen Gerät zu **Geräte > Konfigurieren**.
 
1. Scrollen Sie nach unten zum Kennwortabschnitt für den **Geräteadministrator**. Geben Sie ein Administratorkennwort ein, das zwischen 8 und 15 Zeichen lang ist. Beim Kennwort muss es sich um eine Kombination von drei der vier folgenden Elemente handeln: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen.

1. Bestätigen Sie das Kennwort.
 
1. Klicken Sie unten auf der Seite auf **Speichern**.

Das Geräteadministratorkennwort wurde jetzt aktualisiert. Greifen Sie mit diesem geänderten Kennwort auf die Windows PowerShell-Benutzeroberfläche auf Ihrem virtuellen Gerät zu.

#### Konfigurieren der Remoteverwaltung 

Der Remotezugriff auf Ihr virtuelles Gerät über die Windows PowerShell-Benutzeroberfläche ist standardmäßig nicht aktiviert. Sie müssen die Remoteverwaltung zunächst auf dem virtuellen Gerät und anschließend auf dem Client aktivieren, der für den Zugriff auf das virtuelle Gerät verwendet wird.

Sie können eine Verbindung über HTTP oder HTTPS herstellen. Aus Sicherheitsgründen wird empfohlen, für die Verbindung mit dem virtuellen Gerät HTTPS mit einem selbstsignierten Zertifikat zu verwenden.

Führen Sie die folgenden Schritte durch, um die Remoteverwaltung für das virtuelle StorSimple-Gerät zu konfigurieren.


1. Wechseln Sie auf dem virtuellen Gerät zu **Geräte > Konfigurieren**.

2. Scrollen nach unten zum Abschnitt **Remoteverwaltung**.

3. Legen Sie **Remoteverwaltung aktivieren** auf **Ja** fest.

4. Sie können jetzt eine Verbindung über HTTP herstellen. Die Standardeinstellung ist eine HTTPS-Verbindung. Verbindungen über HTTP sind nur in vertrauenswürdigen Netzwerken zulässig.

5. Klicken Sie auf **Remoteverwaltungszertifikat herunterladen**, um ein Remoteverwaltungszertifikat herunterzuladen. Geben Sie einen Speicherort zum Speichern der Datei an. Dieses Zertifikat muss auf dem Client oder dem Hostcomputer installiert werden, der für die Verbindung mit dem virtuellen Gerät verwendet wird.

6. Klicken Sie unten auf der Seite auf **Speichern**.


## Arbeiten mit dem virtuellen StorSimple-Gerät

Nachdem Sie das virtuelle StorSimple-Gerät erstellt und konfiguriert haben, können Sie mit diesem arbeiten. Sie können auf einem virtuellen wie auf einem physischen StorSimple-Gerät mit Volumecontainern, Volumes und Sicherungsrichtlinien arbeiten. Der einzige Unterschied besteht darin, dass das virtuelle Gerät aus der Geräteliste ausgewählt werden muss. Anweisungen zu den entsprechenden Aufgaben finden Sie in den folgenden Abschnitten:


- [Volumecontainer](storsimple-manage-volume-containers.md)

- [Volumes](storsimple-manage-volumes.md)

- [Sicherungsrichtlinien](storsimple-manage-backup-policies.md)

In den folgenden Abschnitten werden einige der Unterschiede bei der Arbeit mit virtuellen Geräten erläutert.

### Verwalten virtueller StorSimple-Geräte

Da es sich um ein reines Softwaregerät handelt, ist der Verwaltungsaufwand im Vergleich zu einem physischen Gerät minimal. Folgende Optionen stehen zur Auswahl:

- **Softwareupdates** – Sie können das Datum des letzten Softwareupdates sowie alle Updatestatusmeldungen anzeigen. Mit der Schaltfläche **Updates scannen** unten auf der Seite können Sie manuell nach Updates suchen. Wenn Updates verfügbar sind, klicken Sie zu deren Installation auf **Updates installieren**. Da nur eine einzige Schnittstelle zum virtuellen Gerät vorhanden ist, tritt beim Übernehmen der Updates eine kurze Dienstunterbrechung auf. Das virtuelle Gerät wird heruntergefahren und (ggf.) neu gestartet, um alle veröffentlichten Updates zu übernehmen.
- **Supportpaket** – Sie können ein Supportpaket erstellen und hochladen, um den Microsoft-Support beim Beheben von Problemen mit Ihrem virtuellen Gerät zu unterstützen.

### Speicherkonten für ein virtuelles Gerät

Die erstellten Speicherkonten werden vom StorSimple-Manager-Dienst, vom virtuellen Gerät und vom physischen Gerät verwendet. Beim Erstellen von Speicherkonten wird empfohlen, in den Anzeigenamen eine Regions-ID einzufügen, um sicherzustellen, dass die Region für alle Systemkomponenten einheitlich ist. Um Leistungsprobleme mit virtuellen Geräten auszuschließen, müssen sich alle Komponenten in derselben Region befinden.

### Deaktivieren von virtuellen StorSimple-Geräten

Beim Deaktivieren eines virtuellen Geräts werden der virtuelle Computer und die bei der Bereitstellung erstellten Ressourcen gelöscht. Nach der Deaktivierung kann das virtuelle Gerät nicht im vorherigen Zustand wiederhergestellt werden. Vor dem Deaktivieren des virtuellen Geräts müssen alle von diesem abhängige Clients und Hosts beendet oder entfernt werden.

Das Deaktivieren eines virtuellen Geräts führt zu den folgenden Aktionen:

- Das virtuelle Gerät wird entfernt.

- Die für das virtuelle Gerät erstellten OSDisks und Datenträger werden entfernt.

- Der bei der Bereitstellung erstellte gehostete Dienst und das virtuelle Netzwerk werden beibehalten. Wenn diese nicht verwendet werden, sollten sie manuell gelöscht werden.

- Für das virtuelle Gerät erstellte Cloud-Momentaufnahmen werden beibehalten.

Sobald das Gerät auf der Seite des StorSimple-Manager-Diensts als deaktiviert angezeigt wird, können Sie das virtuelle Gerät aus der Geräteliste des StorSimple-Manager-Diensts löschen.

### Remotezugriff auf virtuelle StorSimple-Geräte

Wenn Sie Windows PowerShell-Remoting auf der Konfigurationsseite des StorSimple-Geräts aktiviert haben, können Sie dieses für Verbindungen des virtuellen Geräts mit anderen virtuellen Computern im gleichen virtuellen Netzwerk verwenden. So können Sie beispielsweise eine Verbindung von dem virtuellen Hostcomputer herstellen, den Sie für die für die iSCSI-Verbindung konfiguriert und verwendet haben. Bei den meisten Bereitstellungen ist bereits ein öffentlicher Endpunkt für den Zugriff auf den virtuellen Hostcomputer geöffnet, der für den Zugriff auf das virtuelle Gerät verwendet werden kann.

>[AZURE.WARNING]**Aus Sicherheitsgründen wird dringend empfohlen, für Endpunktverbindungen HTTPS zu verwenden und die Endpunkte nach Abschluss der PowerShell-Remotesitzung zu löschen.**

Beim Einrichten von Remoting für Ihr virtuelles Gerät sollten Sie die Schritte unter [Remoteverbindungen mit Windows PowerShell](https://msdn.microsoft.com/library/dn772393.aspx) durchführen.

Wenn Sie jedoch von einem anderen Computer außerhalb des virtuellen Netzwerks oder außerhalb der Microsoft Azure-Umgebung eine Direktverbindung mit dem virtuellen Gerät herstellen möchten, müssen Sie wie im Folgenden beschrieben zusätzliche Endpunkte erstellen.

Führen Sie die folgenden Schritte durch, um auf dem virtuellen Gerät einen öffentlichen Endpunkt zu erstellen:

1. Melden Sie sich beim Verwaltungsportal an.

- Klicken Sie auf **Virtuelle Computer**, und wählen Sie dann den als virtuelles Gerät zu verwendenden virtuellen Computer aus.

- Klicken Sie auf **Endpunkte**. Auf der Seite "Endpunkte" werden alle Endpunkte für den virtuellen Computer aufgelistet.

- Klicken Sie auf **Hinzufügen**. Das Dialogfeld Endpunkt hinzufügen wird angezeigt. Klicken Sie auf den Pfeil, um fortzufahren.

- Geben Sie unter **Name** den folgenden Namen für den Endpunkt ein: **WinRMHttps**.

- Geben Sie unter **Protokoll** den Wert **TCP** ein.

- Geben Sie unter **Öffentlicher Port** die Portnummern für die Verbindung ein.

- Geben Sie unter **Privater Port** den Wert **5986** ein.

- Aktivieren Sie das Kontrollkästchen, um den Endpunkt zu erstellen.

Nach dem Erstellen des Endpunkts können Sie die Details zum Ermitteln der öffentlichen virtuellen IP-Adresse anzeigen. Notieren Sie sich diese Adresse.

Es wird empfohlen, Verbindungen von einem anderen virtuellen Computer im gleichen virtuellen Netzwerk herzustellen, da so die Anzahl der öffentlichen Endpunkte im virtuellen Netzwerk minimiert wird. Mithilfe dieser Methode stellen Sie die Verbindung mit einem virtuellen Computer einfach über eine Remotedesktopsitzung her, um anschließend diesen virtuellen Computer wie jeden anderen Windows-Client in einem lokalen Netzwerk hierfür zu konfigurieren. Sie müssen die öffentliche Portnummer nicht anfügen, da der Port bereits bekannt ist.

### Beenden und Neustarten

Im Gegensatz zu einem physischen StorSimple-Gerät verfügt das virtuelle StorSimple-Gerät über keine Ein-/Ausschaltschalter. Mitunter kann es jedoch vorkommen, dass Sie das virtuelle Gerät beenden und neu starten müssen. So muss der virtuelle Computer möglicherweise neu gestartet werden, um bestimmte Updatevorgänge abzuschließen. Die einfachste Möglichkeit zum Starten, Beenden und Neustarten eines virtuellen Geräts bietet die Verwaltungskonsole für virtuelle Computer.

In der Verwaltungskonsole wird der Status des virtuellen Geräts als **Wird ausgeführt** angezeigt, da dieses nach dem Erstellen standardmäßig gestartet wird. Sie können einen virtuellen Computer zu einem beliebigen Zeitpunkt beenden und neu starten.

Um ein virtuelles Gerät zu beenden, klicken Sie auf dessen Namen und anschließend auf **Herunterfahren**. Während des Herunterfahrens des virtuellen Geräts wird der Status **Wird beendet** angezeigt. Nach dem Beenden des virtuellen Geräts ändert sich der Status in **Beendet**.

Wenn ein virtuelles Gerät ausgeführt wird und Sie dieses neu starten möchten, klicken Sie auf den Namen und anschließend auf **Neu starten**. Während des Neustarts des virtuellen Geräts wird der Status **Wird neu gestartet** angezeigt. Wenn das virtuelle Gerät zur Verwendung bereit ist, wird der Status **Wird ausgeführt** angezeigt.

Sie können zum Starten, Beenden und Neustarten des virtuellen Geräts auch die folgenden Windows PowerShell-Cmdlets verwenden. Für jedes Cmdlet wird ein Beispiel aufgeführt.

`Start-AzureVMC:\PS>Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    

`Stop-AzureVMC:\PS>Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Restart-AzureVMC:\PS>Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

### Zurücksetzen auf Werkseinstellungen

Wenn Sie Ihr virtuelles Gerät neu einrichten möchten, deaktivieren und löschen Sie dieses einfach, und erstellen Sie ein neues. Genau wie beim Zurücksetzen des physischen Geräts sind für das neue virtuelle Gerät keine Updates installiert. Führen Sie daher vor der Verwendung eine Überprüfung auf Updates durch.


## Ausführen eines Failovers auf das virtuelle Gerät

Die Notfallwiederherstellung ist eines der wichtigsten Szenarios, für die das virtuelle StorSimple-Gerät entwickelt wurde. In diesem Szenario sind das physische StorSimple-Gerät oder das gesamte Datencenter möglicherweise nicht verfügbar. Glücklicherweise können Sie mit einem virtuellen Gerät den Betrieb an einem anderen Speicherort wiederherstellen. Bei der Notfallwiederherstellung wechseln die Volumecontainer vom Quellgerät den Eigentümer und werden auf das virtuelle Gerät übertragen. Als Voraussetzung für die Notfallwiederherstellung müssen ein virtuelles Gerät erstellt und konfiguriert, alle Volumes innerhalb der Volumecontainer offline geschaltet und dem Volumecontainer eine Cloud-Momentaufnahme zugeordnet sein.

>[AZURE.NOTE]**Ein Failover oder Klonen eines Geräts mit Update 1 auf ein Gerät mit Software vor Update 1 ist nicht möglich. Wenn Sie ein Zielgerät mit Software vor Update 1 auswählen, werden Sie benachrichtigt, dass Sie das Zielgerät vor der Durchführung des Failovers aktualisieren müssen.**

### So stellen Sie Ihr physisches Gerät auf dem virtuellen StorSimple-Gerät wieder her

1. Stellen Sie sicher, dass der Volumecontainer für das Failover über zugeordnete Cloud-Momentaufnahmen verfügt.

- Öffnen Sie die Seite **Geräte**, und klicken Sie dann auf die Registerkarte **Volumecontainer**.

- Wählen Sie einen Volumecontainer für das Failover auf dem virtuellen Gerät aus. Klicken Sie auf den Volumecontainer, um die Liste der Volumes innerhalb des Containers anzuzeigen. Wählen Sie ein Volume aus, und klicken Sie auf **Offline schalten**, um das Volume offline zu schalten. Wiederholen Sie diesen Vorgang für alle Volumes im Volumecontainer.

- Wiederholen Sie den vorherigen Schritt für alle Volumecontainer, für die auf dem virtuellen Gerät ein Failover durchgeführt werden soll.

- Wählen Sie auf der Seite **Geräte** das Gerät aus, für das ein Failover durchgeführt werden soll, und klicken Sie anschließend auf **Failover**, um den Assistenten **Gerätefailover** zu öffnen.

- Wählen Sie unter **Volumecontainer für Failover auswählen** die Volumecontainer aus, für die ein Failover durchgeführt werden soll. In dieser Liste werden nur offline geschaltete Volumecontainer angezeigt, die eine Cloud-Momentaufnahme beinhalten. Wenn ein erwarteter Volumecontainer nicht angezeigt wird, brechen Sie den Assistenten ab, und stellen Sie sicher, dass dieser offline ist.

- Wählen Sie auf der nächsten Seite unter **Choose a target device for the volumes in the selected containers** das virtuelle Gerät aus der Dropdownliste der verfügbaren Geräte aus. Nur die Geräte, die über die verfügbare Kapazität verfügen, werden in der Liste angezeigt.

- Überprüfen Sie die Failover-Einstellungen auf der Seite **Failover bestätigen**. Wenn diese richtig sind, klicken Sie auf das Häkchensymbol.

Der Failovervorgang wird gestartet. Wechseln Sie nach Abschluss des Failovers zur Seite "Geräte", und wählen Sie das virtuelle Gerät aus, das als Ziel für den Failovervorgang verwendet wurde. Wechseln Sie zur Seite "Volumecontainer". Es sollten alle angezeigt Volumecontainer und Volumes des alten Geräts angezeigt werden.

>[AZURE.NOTE]**Der auf dem virtuellen Gerät unterstützte Speicherplatz beträgt 30 TB.**

## Herunterfahren oder Löschen virtueller Geräte

Wenn Sie zuvor ein virtuelles StorSimple-Gerät konfiguriert und verwendet haben, jedoch keine weiteren Verwendungsgebühren anfallen sollen, können Sie das virtuelle Gerät herunterfahren. Beim Herunterfahren des virtuellen Geräts werden dessen Betriebssystem oder die Datenträger im Speicher nicht gelöscht. Es fallen keine weiteren Gebühren für Ihr Abonnement an, die Speichergebühren für das Betriebssystem und die Datenträger laufen jedoch weiter.

Wenn Sie das virtuelle Gerät herunterfahren oder löschen, wird es auf der Seite "Geräte" des StorSimple-Manager-Diensts als **Offline** angezeigt. Sie können dieses auch deaktivieren oder als Gerät löschen, wenn Sie zudem die vom virtuellen Gerät erstellten Sicherungen löschen möchten. Weitere Informationen finden Sie unter [Deaktivieren](https://msdn.microsoft.com/library/33b7811b-36ba-4609-b165-0796ad456435#BKMK_acis_deactivate).

### So fahren Sie das virtuelle StorSimple-Gerät herunter

1. Melden Sie sich beim Verwaltungsportal an.

2. Klicken Sie auf **Virtuelle Computer**, und wählen Sie das virtuelle Gerät aus.

3. Klicken Sie auf **Herunterfahren**.

### So löschen Sie das virtuelle StorSimple-Gerät

1. Melden Sie sich beim Verwaltungsportal an.

- Klicken Sie auf **Virtuelle Computer**, und wählen Sie das virtuelle Gerät aus.

- Klicken Sie auf **Löschen**, und wählen Sie alle Datenträger des virtuellen Computers aus.

## Nächste Schritte

Erfahren Sie, wie Sie eine [Wiederherstellung aus einem Sicherungssatz durchführen](../storsimple-restore-from-backupset.md).

<!---HONumber=July15_HO2-->