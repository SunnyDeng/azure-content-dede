<properties 
   pageTitle="Installieren des StorSimple 8600-Geräts | Microsoft Azure"
   description="Beschreibt, wie Sie das StorSimple 8600-Gerät auspacken, in ein Rack einbauen und verkabeln., bevor Sie die Software bereitstellen und konfigurieren."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/01/2015"
   ms.author="alkohli" />

# Auspacken, Einbauen und Verkabeln des StorSimple 8600-Geräts

## Übersicht
Das Microsoft Azure StorSimple 8600-Gerät umfasst zwei Gehäuse: ein primäres Gehäuse und ein EBOD-Gehäuse. In diesem Lernprogramm wird erläutert, wie die StorSimple 8600-Gerätehardware vor dem Konfigurieren der StorSimple-Software ausgepackt, in ein Rack eingebaut und verkabelt wird.

## Auspacken des StorSimple 8600-Geräts

Die folgenden Schritte bieten klare und ausführliche Anweisungen zum Auspacken des StorSimple 8600-Speichergeräts. Das Gerät wird in zwei Kartons versendet: einem für das primäre Gehäuse und einem für das EBOD-Gehäuse. Diese beiden Kartons werden dann in einem Karton verpackt.

### Vorbereitungen zum Auspacken des Geräts

Lesen Sie die folgenden Informationen, bevor Sie das Gerät auspacken.


![Symbol "Warnung"](./media/storsimple-safety/IC740879.png)![Symbol für hohes Gewicht](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **WARNUNG!**

1. Stellen Sie aufgrund des Gewichts des Geräts sicher, dass zwei Personen verfügbar sind, wenn Sie den Vorgang manuell durchführen. Ein vollständig konfiguriertes Gehäuse kann bis zu 32 kg wiegen.
1. Legen Sie den Karton auf einen flachen, ebenen Untergrund.

Führen Sie dann die folgenden Schritte aus, um das Gerät auszupacken.

#### So packen Sie das Gerät aus

1. Überprüfen Sie den Karton und das Verpackungsmaterial auf Risse, Schnitte, Wasserschäden oder anderweitige offensichtliche Beschädigungen. Wenn der Karton oder die Verpackung stark beschädigt sind, öffnen Sie den Karton nicht. Wenden Sie sich an den [Microsoft Support](storsimple-contact-microsoft-support.md), um zu ermitteln, ob sich das Gerät in funktionsfähigem Zustand befindet.

2. Öffnen Sie den äußeren Karton, und nehmen Sie dann die beiden Kartons mit dem primären Gehäuse und dem EBOD-Gehäuse aus diesem Karton. Nun können Sie das primäre Gehäuse und das EBOD-Gehäuse auspacken. Die folgende Abbildung zeigt eines der ausgepackten Gehäuse.

    ![Auspacken des Speichergeräts](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
 
    **Das ausgepackte Speichergerät**

     Bezeichnung | Beschreibung 
     ----- | -------------
     1 | Karton
     2 | SAS-Kabel (in der Verpackung für Zubehör und Kabel)
     3 | Untere Styroporeinlage
     4 | Gerät
     5 | Obere Styroporeinlage
     6 | Zubehörkarton

3. Stellen Sie nach dem Auspacken der beiden Kartons sicher, dass Folgendes vorhanden ist:

  - Ein primäres Gehäuse (primäres Gehäuse und EBOD-Gehäuse befinden sich in zwei separaten Kartons) 
  - Ein EBOD-Gehäuse
  - Vier Netzkabel (zwei pro Karton)
  - Zwei SAS-Kabel (um das primäre Gehäuse mit dem EBOD-Gehäuse zu verbinden)
  - Ein Crossover-Ethernet-Kabel
  - Zwei serielle Konsolenkabel
  - Ein Seriell-USB-Konverter für seriellen Zugriff
  - Vier QSFP-zu-SFP+-Adapter für die Verwendung mit 10-GbE-Netzwerkschnittstellen
  - Zwei Rackmontagekits (vier Seitenschienen mit Befestigungsteilen, davon jeweils zwei für das primäre Gehäuse und das EBOD-Gehäuse), ein Kit pro Karton
  - Dokumentation "Erste Schritte"

    Wenn Sie eines der oben aufgeführten Teile nicht erhalten haben, wenden Sie sich an den [Microsoft Support](storsimple-contact-microsoft-support.md).

Im nächsten Schritt bauen Sie das Gerät in ein Rack ein.

## Einbauen des StorSimple 8600-Geräts in ein Rack

Führen Sie die folgenden Schritte aus, um das StorSimple 8600-Speichergerät in ein 19-Zoll-Standardrack mit Pfosten auf Vorder- und Rückseite einzubauen. Das Gerät wird mit zwei Gehäusen geliefert: einem primären Gehäuse und einem EBOD-Gehäuse. Beide müssen in ein Rack eingebaut werden.

Die Installation umfasst mehrere Schritte, die im Folgenden detailliert erläutert werden.

### Vorbereiten des Standorts

Die Gehäuse werden in ein 19-Zoll-Standardrack mit Pfosten an Vorder- und Rückseite eingebaut. Gehen Sie folgendermaßen vor, um die Rackmontage vorzubereiten.

#### So bereiten Sie den Standort für die Rackmontage vor

1. Vergewissern Sie sich, dass das primäre Gehäuse und das EBOD-Gehäuse sicher auf einer flachen, stabilen und ebenen Arbeitsfläche (oder ähnlich) liegen.

2. Stellen Sie sicher, dass am vorgesehenen Standort eine Standardstromversorgung von einer unabhängigen Quelle oder eine Rack-PDU (Power Distribution Unit) mit unterbrechungsfreier Stromversorgung (USV) vorhanden ist.

3. Vergewissern Sie sich, dass das Rack, in das Sie die Gehäuse einbauen möchten, Platz für einen Einschub mit 4 HE (2 x 2 HE) bietet.

![Symbol "Warnung"](./media/storsimple-safety/IC740879.png)![Symbol für hohes Gewicht](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **WARNUNG!**

 Stellen Sie aufgrund des Gewichts des Geräts sicher, dass zwei Personen verfügbar sind, wenn Sie die Einrichtung des Geräts manuell durchführen. Ein vollständig konfiguriertes Gehäuse kann bis zu 32 kg wiegen.

### Voraussetzungen

Die Gehäuse sind für den Einbau in einen 19-Zoll-Standardrackschrank vorgesehen, wobei Folgendes gilt:

- Mindesttiefe des Racks von 707 mm (27,84 Zoll) von Pfosten zu Pfosten
- Maximales Gewicht des Geräts von 32 kg
- Maximaler Gegendruck von 5 Pascal (0,5 mm Wassersäule)

### Schienenkit für die Rackmontage

Im Lieferumfang ist ein Satz Montageschienen für die Verwendung mit einem 19-Zoll-Rackschrank enthalten. Die Schienen wurden für das maximale Gehäusegewicht getestet. Mit den Schienen können mehrere Gehäuse in das Rack eingebaut werden, ohne dass dabei Platz verloren geht. Beginnen Sie mit dem Einbau des EBOD-Gehäuses.

#### So befestigen Sie das EBOD-Gehäuse an den Schienen

2. Führen Sie diesen Schritt nur aus, wenn in Ihrem Gerät keine inneren Schienen installiert sind. In der Regel sind die inneren Schienen bereits werkseitig installiert. Ist dies nicht der Fall, bringen Sie die linke und die rechte Gleitschiene an den Seiten des Gehäuses an. Sie werden auf jeder Seite mit sechs metrischen Schrauben befestigt. Als Hilfe bei der Ausrichtung sind die Gleitschienen mit **LH – Front** (Vorne links) und **RH – Front** (Vorne rechts) gekennzeichnet. Darüber hinaus weisen die Schienen jeweils an dem Ende, das in Richtung der Rückseite des Gehäuses befestigt wird, eine Verjüngung auf.

    ![Befestigen von Gleitschienen am Gehäuse](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Befestigen der Gleitschienen an den Seiten des Gehäuses**

    Bezeichnung | Beschreibung
    ----- | -----------
    1 | Rundkopfschrauben M3 x 4
    2 | Gehäusegleitschiene
 
3. Befestigen Sie die linke und die rechte Schienenbaugruppe an den vertikalen Blechwinkeln des Rackschranks. Die Halterungen sind mit **LH** (Links), **RH** (Rechts) und **This side up** (Diese Seite nach oben) gekennzeichnet, um Sie bei der richtigen Ausrichtung zu unterstützen.

4. Suchen Sie die Fixierungsstifte, die sich vorne und hinten an der Schienenbaugruppe befinden. Verlängern Sie die Schiene, sodass sie zwischen die Rackpfosten passt, und führen Sie die Stifte in die Bohrungen der vertikalen Blechwinkel am vorderen und hinteren Pfosten ein. Achten Sie darauf, dass die Schienenbaugruppe waagerecht ausgerichtet ist.

5. Sichern Sie die Schienenbaugruppe mit zwei der mitgelieferten metrischen Schrauben an den vertikalen Blechwinkeln des Racks. Verwenden Sie eine Schraube an der Vorderseite und eine Schraube an der Rückseite.

6. Wiederholen Sie diese Schritte für die andere Schienenbaugruppe.

     ![Befestigen von Gleitschienen am Rackschrank](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Befestigen der Schienenbaugruppen am Rack**

     Bezeichnung | Beschreibung
     ----- | -----------
     1 | Klemmschraube
     2 | Vierkantloch-Schraube für vorderen Rackpfosten
     3 | Vordere Fixierungsstifte der linken Schienenbaugruppe
     4 | Klemmschraube
     5 | Hintere Fixierungsstifte der linken Schienenbaugruppe

### Einbauen des EBOD-Gehäuses in das Rack 

Führen Sie die folgenden Schritte aus, um das EBOD-Gehäuse unter Verwendung der soeben installierten Schienen in das Rack einzubauen.

#### So bauen Sie das EBOD-Gehäuse ein

1. Heben Sie das Gehäuse zusammen mit einem Helfer an, und richten sie es an den Rackschienen aus. 

2. Führen Sie das Gehäuse sorgfältig in die Schienen ein, und schieben Sie es dann vollständig in den Rackschrank hinein.

    ![Einführen des Geräts in das Rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Einbauen des Gehäuses in das Rack**

3. Entfernen Sie links und rechts die vorderen Flanschkappen, indem Sie sie abziehen. Die Flanschkappen sind einfach an den Flanschen eingerastet.

4. Sichern Sie das Gehäuse im Rack am linken und am rechten Flansch mit je einer der mitgelieferten Kreuzschlitzschrauben.

4. Bringen Sie die Flanschkappen an, indem Sie diese an der vorgesehenen Position andrücken und einrasten lassen.

     ![Anbringen der Flanschkappen](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **Anbringen der Flanschkappen**
 
     Bezeichnung | Beschreibung
     ----- | -----------
     1 | Befestigungsschraube für Gehäuse


### Einbauen des primären Gehäuses in das Rack

Bauen Sie nach dem Einbau des EBOD-Gehäuses das primäre Gehäuse ein, indem Sie die gleichen Schritte ausführen.

> [AZURE.NOTE]
> 
> - Zwischen dem primären Gehäuse und dem EBOD-Gehäuse können sich leere Einschubfächer im Rack befinden. 
> - Verwenden Sie das mitgelieferte 2 m lange SAS-Kabel, um das primäre Gehäuse mit dem EBOD-Gehäuse zu verbinden.
> - Es gibt keine Einschränkungen hinsichtlich der relativen Position der primären Einheit zur EBOD-Einheit. Daher kann das primäre Gehäuse im obersten Einschubfach und das EBOD-Gehäuse darunter platziert werden – oder umgekehrt.

Im nächsten Schritt verkabeln Sie das Gerät für die Stromversorgung, die Netzwerkverbindung und den seriellen Zugriff.

## Verkabeln des StorSimple 8600-Geräts

Im Folgenden wird erläutert, wie Sie das StorSimple 8600-Gerät für die Stromversorgung, die Netzwerkverbindung und serielle Verbindungen verkabeln.

### Voraussetzungen

Bevor Sie mit dem Verkabeln des Geräts beginnen können, benötigen Sie Folgendes:

- Das vollständig ausgepackte primäre Gehäuse und das vollständig ausgepackte EBOD-Gehäuse
- Vier Netzkabel (jeweils zwei für das primäre Gehäuse und das EBOD-Gehäuse), die zum Lieferumfang des Geräts gehören
- Zwei SAS-Kabel, die mit dem Gerät geliefert wurden, um das EBOD-Gehäuse mit dem primären Gehäuse zu verbinden
- Zugang zu zwei PDUs (Power Distribution Units) (empfohlen)
- Netzwerkkabel
- Mitgelieferte serielle Kabel
- Seriell-USB-Konverter, für den der entsprechende Treiber auf dem PC installiert ist (sofern erforderlich)
- Vier beigefügte QSFP-zu-SFP+-Adapter für die Verwendung mit 10-GbE-Netzwerkschnittstellen
- [Unterstützte Hardware für 10-GbE-Netzwerkschnittstellen auf Ihrem StorSimple-Gerät](storsimple-supported-hardware-for-10-gbe-network-interfaces.md) 

### SAS- und Stromverkabelung

Das Gerät verfügt sowohl über ein primäres als auch ein EBOD-Gehäuse. Daher müssen die Einheiten für SAS-Konnektivität (Serial Attached SCSI) und Stromversorgung miteinander verbunden werden.

Wenn Sie dieses Gerät zum ersten Mal einrichten, führen Sie zuerst die Schritte für die SAS-Verkabelung und danach die Schritte für die Stromverkabelung aus.

[AZURE.INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### Netzwerkverkabelung

Das Gerät besitzt eine Konfiguration mit aktivem Standbymodus: Zu jedem Zeitpunkt ist ein Controllermodul aktiv und verarbeitet alle Datenträger- und Netzwerkvorgänge, während sich das andere Controllermodul im Standbymodus befindet. Wenn ein Controllerfehler auftritt, wird der Standbycontroller sofort aktiviert und setzt alle Datenträger- und Netzwerkvorgänge fort.

Verkabeln Sie Ihr Gerätenetzwerk wie in den folgenden Schritten beschrieben, um dieses redundante Controllerfailover zu unterstützen.

#### So verkabeln Sie das Gerät für die Netzwerkverbindung

1. Das Gerät verfügt über sechs Netzwerkschnittstellen an jedem Controller: vier Ethernet-Anschlüsse mit 1 GBit/s und zwei Ethernet-Anschlüsse mit 10 GBit/s. Mithilfe der folgenden Abbildung können Sie die Datenanschlüsse an der Rückwand des Geräts identifizieren.

     ![Rückwand des 8600-Geräts](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Rückseite des Geräts mit Datenanschlüssen**
 
     Bezeichnung | Beschreibung
     ------- | -----------
     0,1,4,5 | 1-GbE-Netzwerkschnittstellen
     2,3 | 10-GbE-Netzwerkschnittstellen
     6 | Serielle Anschlüsse



1. Die Netzwerkverkabelung ist im folgenden Diagramm dargestellt. (Die Mindestkonfiguration des Netzwerks ist durch durchgängige blaue Linien gekennzeichnet. Die für hohe Verfügbarkeit und Leistung zusätzlich erforderliche Konfiguration wird durch die gepunkteten Linien dargestellt.)

![Netzwerkverkabelung des 4 HE-Geräts](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Netzwerkverkabelung des Geräts**

Bezeichnung | Beschreibung
----- | -----------
Eine Datei | LAN mit Internetzugriff
B | Controller 0
C | PCM 0
D | Controller 1
E | PCM 1
F | EBOD-Controller 0
G | EBOD-Controller 1
H, I | Hosts (z. B. Dateiserver)
0-5 | Netzwerkschnittstellen
6 | Primäres Gehäuse
7 | EBOD-Gehäuse

Beim Verkabeln des Geräts ist die folgende Mindestkonfiguration erforderlich:


- Mindestens zwei Netzwerkschnittstellen pro Controller – eine für den Cloudzugriff und eine für iSCSI. Der DATA 0-Anschluss wird automatisch über die serielle Konsole des Geräts aktiviert und konfiguriert. Zusätzlich zu DATA 0 muss ein weiterer Datenanschluss über das klassische Azure-Portal konfiguriert werden. Verbinden Sie den DATA 0-Anschluss in diesem Fall mit dem primären LAN (Netzwerk mit Internetzugriff). Die anderen Datenanschlüsse können in Abhängigkeit von der vorgesehenen Rolle mit dem SAN/iSCSI-LAN (VLAN)-Segment des Netzwerks verbunden werden.

- Verbinden Sie identische Schnittstellen an jedem Controller mit demselben Netzwerk, um die Verfügbarkeit bei einem Controllerfailover sicherzustellen. Wenn Sie z. B. DATA 0 und DATA 3 bei einem der Controller verbinden, müssen Sie DATA 0 und DATA 3 auch am anderen Controller verbinden.
	
Beachten Sie zur Sicherstellung der hohen Verfügbarkeit und Leistung Folgendes:


- Konfigurieren Sie auf jedem Controller nach Möglichkeit ein Netzwerkschnittstellenpaar für den Cloudzugriff (1 GbE) und ein weiteres Paar für iSCSI (10 GbE empfohlen). 

- Verbinden Sie die Netzwerkschnittstellen jedes Controllers nach Möglichkeit mit zwei unterschiedlichen Switches, um sicherzustellen, dass die Verfügbarkeit auch bei einem Switchausfall gewährleistet ist. Die Abbildung zeigt die beiden 10 GbE-Netzwerkschnittstellen, DATA 2 und DATA 3, jedes Controllers, die mit zwei unterschiedlichen Switches verbunden sind. Weitere Informationen finden Sie im Abschnitt **Netzwerkschnittstellen** unter [Anforderungen an die hohe Verfügbarkeit für Ihr StorSimple-Gerät](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Verwenden Sie die beigefügten QSFP-SFP+-Adapter, wenn Sie SFP+-Transceiver mit Ihren 10 GbE-Netzwerkschnittstellen nutzen. Weitere Informationen finden Sie unter [Unterstützte Hardware für 10-GbE-Netzwerkschnittstellen auf Ihrem StorSimple-Gerät](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

### Verkabelung des seriellen Anschlusses

Führen Sie die folgenden Schritte aus, um den seriellen Anschluss zu verkabeln.

#### So verkabeln Sie das Gerät für serielle Verbindungen

1. Das Gerät verfügt über einen seriellen Anschluss an jedem Controller, der durch ein Schraubenschlüsselsymbol gekennzeichnet ist. Sehen Sie sich die Abbildung der Datenanschlüsse an der Rückseite des Geräts an, um die seriellen Anschlüsse zu identifizieren.

2. Identifizieren Sie den aktiven Controller anhand der Rückwand des Geräts. Eine blinkende blaue LED zeigt an, dass der Controller aktiv ist.

3. Verwenden Sie das mitgelieferte serielle Kabel (bei Bedarf den USB-Seriell-Konverter für Ihren Laptop), und verbinden Sie die Konsole oder den Computer (mit Terminalemulation zum Gerät) mit dem seriellen Anschluss des aktiven Controllers.

4. Installieren Sie die Seriell-USB-Treiber (im Lieferumfang des Geräts enthalten) auf Ihrem Computer.

5. Richten Sie die serielle Verbindung wie folgt ein:
   - 115\.200 Baud
   - 8 Datenbits
   - 1 Stoppbit
   - Keine Parität
   - **Keine** Flusssteuerung

6. Stellen Sie sicher, dass die Verbindung funktioniert, indem Sie auf der Konsole die EINGABETASTE drücken. Ein Menü der seriellen Konsole sollte angezeigt werden.

> [AZURE.NOTE] **Lights-Out-Management:** Wenn das Gerät in einem Remoterechenzentrum oder in einem Computerraum mit beschränktem Zugriff installiert ist, stellen Sie sicher, dass die seriellen Verbindungen zu beiden Controllern immer mit einem Switch einer seriellen Konsole oder einem ähnlichen Gerät verbunden sind. Dies ermöglicht bei einer Netzwerkunterbrechung oder unerwarteten Fehlern Out-of-Band-Remotesteuerungs- und -Supportvorgänge.

Das Gerät ist nun für Stromversorgung, Netzwerkzugriff und serielle Verbindungen verkabelt. Im nächsten Schritt konfigurieren Sie die Software auf Ihrem Gerät.

## Nächste Schritte

Sie können nun [Ihr lokales StorSimple-Gerät bereitstellen und konfigurieren](storsimple-deployment-walkthrough.md).
 

<!---HONumber=AcomDC_0224_2016-->