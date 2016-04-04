<properties 
   pageTitle="Virtuelles StorSimple-Gerät – Update 2 | Microsoft Azure"
   description="Erfahren Sie, wie Sie ein virtuelles StorSimple-Gerät in einem virtuellen Microsoft Azure-Netzwerk erstellen, bereitstellen und verwalten. (Gilt für StorSimple Update 2)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="alkohli" />

# Bereitstellen und Verwalten eines virtuellen StorSimple-Geräts in Azure (Update 2)

> [AZURE.SELECTOR]
- [Update 2](../articles/storsimple/storsimple-virtual-device-u2.md)
- [Update 1](../articles/storsimple/storsimple-virtual-device-u1.md)
- [GA-Version](../articles/storsimple/storsimple-virtual-device.md)

##Übersicht
Das virtuelle StorSimple-Gerät ist eine zusätzliche, in der Microsoft Azure StorSimple-Lösung enthaltene Funktion. Das virtuelle StorSimple-Gerät wird auf einem virtuellen Computer in einem virtuellen Microsoft Azure-Netzwerk ausgeführt und kann zum Sichern und Klonen von Daten von Hosts verwendet werden.


#### Vergleich virtueller Gerätemodelle

Das virtuelle StorSimple-Gerät steht in zwei Modellen zur Verfügung, dem Standardmodell 8010 (ehemals 1100) und dem in Update 2 eingeführten Premium-Modell 8020. Ein Vergleich der beiden Modelle ist unten in Tabellenform aufgeführt.


| Gerätemodell | 8010<sup>1</sup> | 8020 |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Maximale Kapazität** | 30 TB | 64 TB |
| **Azure-VM** | Standard\_A3 (4 Kerne, 7 GB Arbeitsspeicher) | Standard\_DS3 (4 Kerne, 14 GB Arbeitsspeicher) |
| **Versionskompatibilität** | Versionen unter Vorgängerversionen von Update 2 oder höher | Versionen unter Update 2 oder höher |
| **Regionale Verfügbarkeit** | Alle Azure-Regionen | Azure-Regionen, die Storage Premium unterstützen<br></br>Eine Liste dieser Regionen finden Sie unter [Unterstützte Regionen für 8020](#supported-regions-for-8020). |
| **Speichertyp** | Verwendet Azure Storage Standard<br></br> Erfahren Sie, wie Sie ein [Storage Standard-Konto erstellen](). | Verwendet Azure Storage Premium<br></br>Erfahren Sie, wie Sie ein [Storage Premium-Konto erstellen](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk). |
| **Informationen zu Workloads** | Abrufen von Dateien aus Sicherungskopien auf Elementebene | Cloudentwicklungs- und Testszenarios, niedrige Latenz und Workloads mit höherer Leistung <br></br>Sekundäres Gerät für die Notfallwiederherstellung |
 
<sup>1</sup> *Ehemals 1100*.

#### Unterstützte Regionen für 8020

Die derzeit für 8020 unterstützten Storage Premium-Regionen sind unten in Tabellenform aufgeführt. Diese Liste wird laufend aktualisiert, wenn Storage Premium in weiteren Regionen verfügbar wird.

| S.-Nr. | Derzeit in folgenden Regionen unterstützt |
|---------------------------------------------------------|--------------------------------|
| 1 | USA (Mitte) |
| 2 | USA (Ost) |
| 3 | USA (Ost) 2 |
| 4 | USA (West) |
| 5 | Nordeuropa |
| 6 | Westeuropa |
| 7 | Südostasien |
| 8 | Japan Ost |
| 9 | Japan (Westen) |
| 10 | Australien (Osten) |
| 11 | Australien (Südost)* |
| 12 | Asien (Osten)* |
| 13 | USA (Mitte/Süden)* |

**Storage Premium wurde vor Kurzem in diesen Regionen eingeführt.

In diesem Artikel werden die einzelnen Schritte beim Bereitstellen eines virtuellen StorSimple-Geräts in Azure beschrieben. In diesem Artikel lernen Sie Folgendes:

- Unterschiede zwischen virtuellem und physischem Gerät

- Erstellen und Konfigurieren des virtuellen Geräts

- Herstellen einer Verbindung mit dem virtuellen Gerät

- Arbeiten mit dem virtuellen Gerät

Dieses Tutorial gilt für alle virtuellen StorSimple-Geräte mit Update 2 und höher.

## Unterschiede zwischen virtuellem und physischem Gerät

Das virtuelle StorSimple-Gerät ist eine reine Softwareversion von StorSimple, die auf einem einzelnen Knoten eines virtuellen Microsoft Azure-Computers ausgeführt wird. Das virtuelle Gerät unterstützt Notfallwiederherstellungsszenarien, in denen das physische Gerät nicht verfügbar ist. Zudem kann es zum Abruf einzelner Elemente aus Sicherungen, bei der lokalen Notfallwiederherstellung und für Entwicklungs- und Testszenarien in der Cloud verwendet werden.

#### Unterschiede im Vergleich zum physischen Gerät

In der folgenden Tabelle werden einige wichtige Unterschiede zwischen virtuellen und physischen StorSimple-Geräten aufgeführt.

| | Physisches Gerät | Virtuelles Gerät |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Standort** | Befindet sich im Datencenter. | Wird in Azure ausgeführt. |
| **Netzwerkschnittstellen** | Verfügt über sechs Netzwerkschnittstellen: DATA 0 bis DATA 5. | Besitzt nur eine Netzwerkschnittstelle: DATA 0. |
| **Registrierung** | Wird während der Konfiguration registriert. | Die Registrierung ist eine separate Aufgabe. |
| **Verschlüsselungsschlüssel für Dienstdaten** | Generieren Sie den Schlüssel auf dem physischen Gerät erneut, und aktualisieren Sie dann das virtuelle Gerät mit dem neuen Schlüssel. | Der Schlüssel kann auf dem virtuellen Gerät nicht neu generiert werden. |


## Voraussetzungen für das virtuelle Gerät

In den folgenden Abschnitten werden die Konfigurationsvoraussetzungen für das virtuelle StorSimple-Gerät beschrieben. Überprüfen Sie vor der Bereitstellung eines virtuellen Geräts die [Sicherheitsüberlegungen für die Verwendung eines virtuellen Geräts](storsimple-security.md#storsimple-virtual-device-security).

#### Anforderungen für Azure

Bevor Sie das virtuelle Gerät bereitstellen, müssen Sie in Ihrer Umgebung die folgenden Vorbereitungen treffen:

- [Konfigurieren Sie ein virtuelles Netzwerk in Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) für das virtuelle Gerät. Wenn Sie Storage Premium verwenden, müssen Sie ein virtuelles Netzwerk in einer Azure-Region erstellen, die Storage Premium unterstützt. Weitere Informationen zu [Regionen, die derzeit für 8020 unterstützt werden](#supported-regions-for-8020).
- Es empfiehlt sich, die von Azure bereitgestellten DNS-Standardserver zu verwenden, anstatt einen eigenen DNS-Servernamen anzugeben. Wenn Ihr DNS-Servername ungültig ist oder der DNS-Server IP-Adressen nicht ordnungsgemäß auflösen kann, ist die Erstellung des virtuellen Geräts nicht möglich.
- Punkt-zu-Standort und Standort-zu-Standort sind optional, aber nicht erforderlich. Sie können diese Optionen gegebenenfalls für erweiterte Szenarios konfigurieren. 
- Sie können [virtuelle Azure-Computer](../virtual-machines/virtual-machines-linux-about.md) (Hostserver) im virtuellen Netzwerk erstellen, die die vom virtuellen Gerät verfügbar gemachten Volumes verwenden können. Diese Server müssen die folgenden Anforderungen erfüllen: 							
	- Es muss sich um virtuelle Windows- oder Linux-Computer mit iSCSI-Initiatorsoftware handeln.
	- Sie müssen im gleichen virtuellen Netzwerk wie das virtuelle Gerät ausgeführt werden.
	- Sie müssen über die interne IP-Adresse des virtuellen Geräts eine Verbindung zum iSCSI-Ziel des virtuellen Geräts herstellen können.

- Stellen Sie sicher, dass die iSCSI-Unterstützung und der Clouddatenverkehr im gleichen virtuellen Netzwerk konfiguriert wurden.


#### StorSimple-Anforderungen

Nehmen Sie vor dem Erstellen eines virtuellen Geräts die folgenden Updates für den Azure StorSimple-Dienst vor:


- Fügen Sie den virtuellen Computern, die als Hostserver für Ihr virtuelles Gerät fungieren sollen, [Zugriffssteuerungsdatensätze](storsimple-manage-acrs.md) hinzu.

- Verwenden Sie ein [Speicherkonto](storsimple-manage-storage-accounts.md#add-a-storage-account) in derselben Region wie das virtuelle Gerät. Speicherkonten in anderen Regionen können zu Leistungseinbußen führen. Sie können ein Storage Standard- oder Premium-Konto mit dem virtuellen Gerät verwenden. Weitere Informationen zum Erstellen eines [Storage Standard-Kontos]() oder eines [Storage Premium-Kontos](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

- Verwenden Sie zum Erstellen des virtuellen Geräts ein anderes Speicherkonto als das, das für die Daten verwendet wird. Die Verwendung ein und desselben Speicherkontos kann zu Leistungseinbußen führen.

Stellen Sie vorab sicher, dass die folgenden Informationen vorliegen:

- Ein Konto für das klassische Azure-Portal sowie die zugehörigen Anmeldeinformationen

- Eine Kopie des Schlüssels für die Dienstdatenverschlüsselung des physischen Geräts.


## Erstellen und Konfigurieren des virtuellen Geräts

Stellen Sie vor Durchführung dieser Schritte sicher, dass die [Voraussetzungen für das virtuelle Gerät](#prerequisites-for-the-virtual-device) erfüllt sind.

Nachdem Sie ein virtuelles Netzwerk erstellt, einen StorSimple Manager-Dienst konfiguriert und das physische StorSimple-Gerät beim Dienst registriert haben, können Sie mithilfe der folgenden Schritte ein virtuelles StorSimple-Gerät erstellen und konfigurieren.

### Schritt 1: Erstellen eines virtuellen Geräts

Führen Sie die folgenden Schritte aus, um das virtuelle StorSimple-Gerät zu erstellen.

[AZURE.INCLUDE [Erstellen eines virtuellen Geräts](../../includes/storsimple-create-virtual-device-u2.md)]


### Schritt 2: Konfigurieren und Registrieren des virtuellen Geräts

Stellen Sie vorab sicher, dass Sie über eine Kopie des Verschlüsselungsschlüssels für die Dienstdaten verfügen. Der Schlüssel für die Dienstdatenverschlüsselung wurde beim Konfigurieren des ersten StorSimple-Geräts erstellt, und Sie wurden aufgefordert, diesen an einem sicheren Ort zu speichern. Wenn Sie über keine Kopie des Schlüssels für die Dienstdatenverschlüsselung verfügen, wenden Sie sich an den Microsoft-Support.

Führen Sie die folgenden Schritte durch, um das virtuelle StorSimple-Gerät zu konfigurieren und zu registrieren:
[AZURE.INCLUDE [Konfigurieren und Registrieren eines virtuellen Geräts](../../includes/storsimple-configure-register-virtual-device.md)]

### Schritt 3: (Optional) Ändern der Konfigurationseinstellungen des Geräts

Der folgende Abschnitt beschreibt die Gerätekonfigurationseinstellungen, die für das virtuelle StorSimple-Gerät erforderlich sind, wenn Sie CHAP oder StorSimple Snapshot Manager verwenden oder das Geräteadministratorkennwort ändern möchten.

#### Konfigurieren des CHAP-Initiators

Dieser Parameter enthält die Anmeldeinformationen, die Ihr virtuelles Gerät (Ziel) von den Initiatoren (Servern) erwartet, die auf die Volumes zugreifen. Die Initiatoren stellen einen CHAP-Benutzernamen und ein CHAP-Kennwort bereit, um sich bei der Authentifizierung bei Ihrem Gerät zu identifizieren. Ausführliche Schritte finden Sie unter [Konfigurieren von CHAP für Ihr Gerät](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### Konfigurieren des CHAP-Ziels

Dieser Parameter enthält die Anmeldeinformationen, die Ihr virtuelles Gerät verwendet, wenn ein CHAP-fähiger Initiator eine wechselseitige oder bidirektionale Authentifizierung anfordert. Ihr virtuelles Gerät verwendet einen Benutzernamen und ein Kennwort für das Zurücksetzen von CHAP, um sich beim Authentifizierungsvorgang beim Initiator zu identifizieren. Beachten Sie, dass es sich bei den CHAP-Zieleinstellungen um globale Einstellungen handelt. Wenn diese angewendet werden, verwenden alle mit dem virtuellen Speichergerät verbundenen Volumes die CHAP-Authentifizierung. Ausführliche Schritte finden Sie unter [Konfigurieren von CHAP für Ihr Gerät](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### Konfigurieren des StorSimple-Momentaufnahme-Managers

Der StorSimple-Momentaufnahme-Manager befindet sich auf dem Windows-Host und ermöglicht Administratoren die Verwaltung der Sicherungen Ihres StorSimple-Geräts in Form von lokalen und Cloudmomentaufnahmen.

>[AZURE.NOTE] Für das virtuelle Gerät handelt es sich beim Windows-Host um einen virtuellen Azure-Computer.

Beim Konfigurieren eines Geräts im StorSimple-Momentaufnahme-Manager werden Sie aufgefordert, zur Authentifizierung des Speichergeräts die IP-Adresse und das Kennwort des StorSimple-Geräts anzugeben. Ausführliche Schritte finden Sie unter [Konfigurieren des StorSimple Snapshot Manager-Kennworts](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### Ändern des Geräteadministratorkennworts 

Wenn Sie über die Windows PowerShell-Benutzeroberfläche auf das virtuelle Gerät zugreifen, müssen Sie ein Kennwort eingeben. Für die Sicherheit Ihrer Daten müssen Sie dieses Kennwort ändern, bevor das virtuelle Gerät verwendet werden kann. Ausführliche Schritte finden Sie unter [Ändern des Geräteadministratorkennworts](storsimple-change-passwords.md#change-the-device-administrator-password).

## Herstellen einer Remoteverbindung mit dem virtuellen Gerät
Der Remotezugriff auf Ihr virtuelles Gerät über die Windows PowerShell-Benutzeroberfläche ist standardmäßig nicht aktiviert. Sie müssen die Remoteverwaltung zunächst auf dem virtuellen Gerät und anschließend auf dem Client aktivieren, der für den Zugriff auf das virtuelle Gerät verwendet wird.

Das aus zwei Schritten bestehende Verfahren zur Remoteverbindung wird nachfolgend beschrieben.

### Schritt 1: Konfigurieren der Remoteverwaltung

Führen Sie die folgenden Schritte durch, um die Remoteverwaltung für das virtuelle StorSimple-Gerät zu konfigurieren.

[AZURE.INCLUDE [Konfigurieren der Remoteverwaltung über HTTP für virtuelle Geräte](../../includes/storsimple-configure-remote-management-http-device.md)]

### Schritt 2: Remotezugriff auf das virtuelle Gerät

Wenn Sie die Windows PowerShell-Remoteverwaltung auf der Konfigurationsseite des StorSimple-Geräts aktiviert haben, können Sie Windows PowerShell-Remoting für Verbindungen mit dem virtuellen Gerät von anderen virtuellen Computern im gleichen virtuellen Netzwerk aus verwenden. So können Sie beispielsweise eine Verbindung von dem virtuellen Hostcomputer herstellen, den Sie für die iSCSI-Verbindung konfiguriert und verwendet haben. Bei den meisten Bereitstellungen ist bereits ein öffentlicher Endpunkt für den Zugriff auf den virtuellen Hostcomputer geöffnet, der für den Zugriff auf das virtuelle Gerät verwendet werden kann.

>[AZURE.WARNING] **Aus Sicherheitsgründen wird dringend empfohlen, für Endpunktverbindungen HTTPS zu verwenden und die Endpunkte nach Abschluss der PowerShell-Remotesitzung zu löschen.**

Beim Einrichten von Remoting für Ihr virtuelles Gerät sollten Sie die Schritte unter [Remoteverbindungen mit dem StorSimple-Gerät](storsimple-remote-connect.md) durchführen.

## Herstellen einer direkten Verbindung mit dem virtuellen Gerät

Sie können auch eine direkte Verbindung mit dem virtuellen Gerät herstellen. Wenn Sie von einem anderen Computer außerhalb des virtuellen Netzwerks oder außerhalb der Microsoft Azure-Umgebung eine Direktverbindung mit dem virtuellen Gerät herstellen möchten, müssen Sie wie im Folgenden beschrieben zusätzliche Endpunkte erstellen.

Führen Sie die folgenden Schritte durch, um auf dem virtuellen Gerät einen öffentlichen Endpunkt zu erstellen:

[AZURE.INCLUDE [Erstellen öffentlicher Endpunkte auf einem virtuellen Gerät](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Es wird empfohlen, Verbindungen von einem anderen virtuellen Computer im gleichen virtuellen Netzwerk herzustellen, da so die Anzahl der öffentlichen Endpunkte im virtuellen Netzwerk minimiert wird. Mithilfe dieser Methode stellen Sie die Verbindung mit einem virtuellen Computer einfach über eine Remotedesktopsitzung her, um anschließend diesen virtuellen Computer wie jeden anderen Windows-Client in einem lokalen Netzwerk hierfür zu konfigurieren. Sie müssen die öffentliche Portnummer nicht anfügen, da der Port bereits bekannt ist.

## Arbeiten mit dem virtuellen StorSimple-Gerät

Nachdem Sie das virtuelle StorSimple-Gerät erstellt und konfiguriert haben, können Sie mit diesem arbeiten. Sie können auf einem virtuellen wie auf einem physischen StorSimple-Gerät mit Volumecontainern, Volumes und Sicherungsrichtlinien arbeiten. Der einzige Unterschied besteht darin, dass das virtuelle Gerät aus der Geräteliste ausgewählt werden muss. Schrittanleitungen für die verschiedenen Verwaltungsaufgaben für das virtuelle Gerät finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md).

In den folgenden Abschnitten werden einige der Unterschiede bei der Arbeit mit virtuellen Geräten erläutert.

### Verwalten virtueller StorSimple-Geräte

Da es sich um ein reines Softwaregerät handelt, ist der Verwaltungsaufwand im Vergleich zu einem physischen Gerät minimal. Folgende Optionen stehen zur Auswahl:

- **Softwareupdates** – Sie können das Datum des letzten Softwareupdates sowie alle Updatestatusmeldungen anzeigen. Mit der Schaltfläche **Updates scannen** unten auf der Seite können Sie manuell nach Updates suchen. Wenn Updates verfügbar sind, klicken Sie zu deren Installation auf **Updates installieren**. Da nur eine einzige Schnittstelle zum virtuellen Gerät vorhanden ist, tritt beim Übernehmen der Updates eine kurze Dienstunterbrechung auf. Das virtuelle Gerät wird heruntergefahren und (ggf.) neu gestartet, um alle veröffentlichten Updates zu übernehmen. Eine Schrittanleitung finden Sie unter [Aktualisieren Ihres Geräts](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
- **Supportpaket** – Sie können ein Supportpaket erstellen und hochladen, um den Microsoft-Support beim Beheben von Problemen mit Ihrem virtuellen Gerät zu unterstützen. Eine Schrittanleitung finden Sie unter [Erstellen und Verwalten eines Unterstützungspakets](storsimple-create-manage-support-package.md).

### Speicherkonten für ein virtuelles Gerät

Die erstellten Speicherkonten werden vom StorSimple-Manager-Dienst, vom virtuellen Gerät und vom physischen Gerät verwendet. Beim Erstellen von Speicherkonten wird empfohlen, in den Anzeigenamen eine Regions-ID einzufügen, um sicherzustellen, dass die Region für alle Systemkomponenten einheitlich ist. Um Leistungsprobleme mit virtuellen Geräten auszuschließen, müssen sich alle Komponenten in derselben Region befinden.

Eine Schrittanleitung finden Sie unter [Hinzufügen eines Speicherkontos](storsimple-manage-storage-accounts.md#add-a-storage-account).

### Deaktivieren von virtuellen StorSimple-Geräten

Beim Deaktivieren eines virtuellen Geräts werden der virtuelle Computer und die bei der Bereitstellung erstellten Ressourcen gelöscht. Nach der Deaktivierung kann das virtuelle Gerät nicht im vorherigen Zustand wiederhergestellt werden. Vor dem Deaktivieren des virtuellen Geräts müssen alle von diesem abhängige Clients und Hosts beendet oder entfernt werden.

Das Deaktivieren eines virtuellen Geräts führt zu den folgenden Aktionen:

- Das virtuelle Gerät wird entfernt.

- Die für das virtuelle Gerät erstellten Betriebssystem-Datenträger und Datenträger für Daten werden entfernt.

- Der bei der Bereitstellung erstellte gehostete Dienst und das virtuelle Netzwerk werden beibehalten. Wenn diese nicht verwendet werden, sollten sie manuell gelöscht werden.

- Für das virtuelle Gerät erstellte Cloudmomentaufnahmen werden beibehalten.

Eine Schritt-für-Schritt-Anleitung finden Sie unter [Deaktivieren und Löschen eines StorSimple-Geräts](storsimple-deactivate-and-delete-device.md).

Sobald das virtuelle Gerät auf der Seite des StorSimple Manager-Diensts als deaktiviert angezeigt wird, können Sie das virtuelle Gerät aus der Geräteliste auf der Seite **Geräte** löschen.


### Starten, Beenden und Neustarten eines virtuellen Geräts
Im Gegensatz zu einem physischen StorSimple-Gerät verfügt das virtuelle StorSimple-Gerät über keine Ein-/Ausschaltschalter. Mitunter kann es jedoch vorkommen, dass Sie das virtuelle Gerät beenden und neu starten müssen. So muss der virtuelle Computer möglicherweise neu gestartet werden, um bestimmte Updatevorgänge abzuschließen. Die einfachste Möglichkeit zum Starten, Beenden und Neustarten eines virtuellen Geräts bietet die Verwaltungskonsole für virtuelle Computer.

In der Verwaltungskonsole wird der Status des virtuellen Geräts als **Wird ausgeführt** angezeigt, da dieses nach dem Erstellen standardmäßig gestartet wird. Sie können einen virtuellen Computer zu einem beliebigen Zeitpunkt starten, beenden und neu starten.

[AZURE.INCLUDE [Beenden und Neustarten eines virtuellen Geräts](../../includes/storsimple-stop-restart-virtual-device.md)]

### Zurücksetzen auf Werkseinstellungen

Wenn Sie Ihr virtuelles Gerät neu einrichten möchten, deaktivieren und löschen Sie dieses einfach, und erstellen Sie ein neues. Genau wie beim Zurücksetzen des physischen Geräts sind für das neue virtuelle Gerät keine Updates installiert. Führen Sie daher vor der Verwendung eine Überprüfung auf Updates durch.


## Ausführen eines Failovers auf das virtuelle Gerät

Die Notfallwiederherstellung ist eines der wichtigsten Szenarios, für die das virtuelle StorSimple-Gerät entwickelt wurde. In diesem Szenario sind das physische StorSimple-Gerät oder das gesamte Datencenter möglicherweise nicht verfügbar. Glücklicherweise können Sie mit einem virtuellen Gerät den Betrieb an einem anderen Speicherort wiederherstellen. Bei der Notfallwiederherstellung wechseln die Volumecontainer vom Quellgerät den Eigentümer und werden auf das virtuelle Gerät übertragen. Als Voraussetzung für die Notfallwiederherstellung müssen ein virtuelles Gerät erstellt und konfiguriert, alle Volumes innerhalb der Volumecontainer offline geschaltet und dem Volumecontainer eine Cloudmomentaufnahme zugeordnet sein.

>[AZURE.NOTE] 
>
> - Wenn Sie ein virtuelles Gerät als sekundäres Gerät für die Notfallwiederherstellung einsetzen, bedenken Sie, dass 8010 über 30 TB Standardspeicher und 8020 über 64 TB Premium-Speicher verfügt. Das virtuelle Gerät mit der höheren Kapazität, 8020, ist möglicherweise für ein DR-Szenario besser geeignet.
> - Ein Failover oder das Klonen eines Geräts mit Update 2 auf ein Gerät mit Software vor Update 1 ist nicht möglich. Sie können jedoch ein Failover für ein Gerät mit Update 2 auf ein Gerät mit Update 1 (1.1 oder 1.2) durchführen.

Eine Schrittanleitung finden Sie unter [Failover auf ein virtuelles Gerät](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).
 

## Herunterfahren oder Löschen virtueller Geräte

Wenn Sie zuvor ein virtuelles StorSimple-Gerät konfiguriert und verwendet haben, jedoch keine weiteren Verwendungsgebühren anfallen sollen, können Sie das virtuelle Gerät herunterfahren. Beim Herunterfahren des virtuellen Geräts werden dessen Betriebssystem oder die Datenträger im Speicher nicht gelöscht. Es fallen keine weiteren Gebühren für Ihr Abonnement an, die Speichergebühren für das Betriebssystem und die Datenträger laufen jedoch weiter.

Wenn Sie das virtuelle Gerät herunterfahren oder löschen, wird es auf der Seite "Geräte" des StorSimple-Manager-Diensts als **Offline** angezeigt. Sie können das Gerät deaktivieren oder löschen, wenn Sie zudem die vom virtuellen Gerät erstellten Sicherungen löschen möchten. Weitere Informationen finden Sie unter [Deaktivieren und Löschen eines StorSimple-Geräts](storsimple-deactivate-and-delete-device.md).

[AZURE.INCLUDE [Herunterfahren eines virtuellen Geräts](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Löschen eines virtuellen Geräts](../../includes/storsimple-delete-virtual-device.md)]

   

## Nächste Schritte

- Erfahren Sie, wie Sie beim [Verwalten Ihres virtuellen Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md) vorgehen.
 
- Erfahren Sie, wie Sie [StorSimple-Volumes aus einem Sicherungssatz wiederherstellen](storsimple-restore-from-backup-set.md).

<!----HONumber=AcomDC_0323_2016-->

<!---HONumber=AcomDC_0323_2016-->