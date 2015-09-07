<properties 
   pageTitle="Installieren des StorSimple 8100-Geräts | Microsoft Azure"
	description="Beschreibt, wie Sie das StorSimple 8100-Gerät auspacken, in ein Rack einbauen und verkabeln., bevor Sie die Software bereitstellen und konfigurieren."
	services="storsimple"
	documentationCenter="NA"
	authors="alkohli"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="TBD"
	ms.date="08/26/2015"
	ms.author="alkohli"/>

# Auspacken, Einbauen und Verkabeln des StorSimple 8100-Geräts

## Übersicht

Microsoft Azure StorSimple 8100 ist ein Gerät mit einem Gehäuse, das in ein Rack eingebaut wird.

In diesem Lernprogramm wird erläutert, wie die StorSimple 8100-Gerätehardware vor dem Konfigurieren der StorSimple-Software ausgepackt, in ein Rack eingebaut und verkabelt wird.

## Auspacken des StorSimple 8100-Geräts

Die folgenden Schritte bieten klare und ausführliche Anweisungen zum Auspacken des StorSimple 8100-Speichergeräts. Dieses Gerät wird in einem einzelnen Karton ausgeliefert.

### Vorbereitungen zum Auspacken des Geräts

Lesen Sie die folgenden Informationen, bevor Sie das Gerät auspacken.

>[AZURE.WARNING]![Symbol für hohes Gewicht](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png)
>
> 1. Stellen Sie aufgrund des Gewichts des Geräts sicher, dass zwei Personen verfügbar sind, wenn Sie den Vorgang manuell durchführen. Ein vollständig konfiguriertes Gerät kann bis zu 32 kg wiegen.
>
> 2. Legen Sie den Karton auf einen flachen, ebenen Untergrund.

Führen Sie dann die folgenden Schritte aus, um das Gerät auszupacken.

#### So packen Sie das Gerät aus

1. Überprüfen Sie den Karton und das Verpackungsmaterial auf Risse, Schnitte, Wasserschäden oder anderweitige offensichtliche Beschädigungen. Wenn der Karton oder die Verpackung stark beschädigt sind, öffnen Sie den Karton nicht. Wenden Sie sich an den Microsoft Support, um zu ermitteln, ob sich das Gerät in funktionsfähigem Zustand befindet. 

2. Packen Sie den Karton aus. Die folgende Abbildung zeigt das ausgepackte Azure StorSimple-Gerät.

     ![Auspacken des Speichergeräts](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)

    **Das ausgepackte Speichergerät**

     Bezeichnung | Beschreibung 
     ----- | -------------
     1 | Karton
     2 | Untere Styroporeinlage
     3 | Gerät
     4 | Obere Styroporeinlage
     5 | Zubehörkarton

3. Stellen Sie nach dem Auspacken des Kartons sicher, dass Folgendes vorhanden ist:

   - Ein Gerät mit einem Gehäuse
   - Zwei Netzkabel
   - Ein Crossover-Ethernet-Kabel
   - Zwei serielle Konsolenkabel
   - Ein Seriell-USB-Konverter für seriellen Zugriff
   - Ein T10-Sicherheitsschraubendreher
   - Vier einzelne QSFP-zu-SFP+-Adapter
   - Ein Rackmontagekit (zwei Seitenschienen mit Befestigungsteilen)
   - Dokumentation "Erste Schritte"

    Wenn Sie eines der oben aufgeführten Teile nicht erhalten haben, wenden Sie sich an den Microsoft Support.

Im nächsten Schritt bauen Sie das Gerät in ein Rack ein.

## Einbauen des StorSimple 8100-Geräts in ein Rack

Führen Sie die folgenden Schritte aus, um das StorSimple 8100-Speichergerät in ein 19-Zoll-Standardrack mit Pfosten auf Vorder- und Rückseite einzubauen. Das StorSimple 8100-Gerät verfügt über ein einziges, primäres Gehäuse.

Die Installation umfasst mehrere Schritte, die im Folgenden detailliert erläutert werden.

### Vorbereiten des Standorts

Das Gerät wird in ein 19-Zoll-Standardrack mit Pfosten an Vorder- und Rückseite eingebaut. Gehen Sie folgendermaßen vor, um die Rackmontage vorzubereiten.

#### So bereiten Sie den Standort für die Rackmontage vor

1. Vergewissern Sie sich, dass das Gerät sicher auf einer flachen, stabilen und ebenen Arbeitsfläche (oder ähnlich) liegt.

2. Stellen Sie sicher, dass am vorgesehenen Standort eine Standardstromversorgung von einer unabhängigen Quelle oder eine Rack-PDU (Power Distribution Unit) mit unterbrechungsfreier Stromversorgung (USV) vorhanden ist.

3. Vergewissern Sie sich, dass das Rack, in das Sie das Gerät einbauen möchten, Platz für einen Einschub mit 2 HE bietet.

>[AZURE.WARNING]![Symbol für hohes Gewicht](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png)
> 
> Stellen Sie aufgrund des Gewichts des Geräts sicher, dass zwei Personen verfügbar sind, wenn Sie die Einrichtung des Geräts manuell durchführen. Ein vollständig konfiguriertes Gehäuse kann bis zu 32 kg wiegen.

### Voraussetzungen

Das Gehäuse des 8100-Geräts ist für den Einbau in einen 19-Zoll-Standardrackschrank vorgesehen, wobei Folgendes gilt:

- Mindesttiefe des Racks von 707,14 mm (27,84 Zoll) von Pfosten zu Pfosten
- Maximales Gewicht des Geräts von 32 kg
- Maximaler Gegendruck von 5 Pascal (0,5 mm Wassersäule)

### Schienenkit für die Rackmontage

Im Lieferumfang ist ein Satz Montageschienen für die Verwendung mit einem 19-Zoll-Rackschrank enthalten. Die Schienen wurden für das maximale Gehäusegewicht getestet. Mit den Schienen können auch mehrere Gehäuse in das Rack eingebaut werden, ohne dass dabei Platz verloren geht.

#### So befestigen Sie das Gerät an den Schienen

1. Legen Sie das Gehäuse auf die Arbeitsfläche, und ziehen Sie die Kappen vom rechten und linken vorderen Flansch ab. Die Flanschkappen sind einfach an den Flanschen eingerastet.

2. In der Regel sind die Schienen bereits werkseitig installiert. Ist dies nicht der Fall, bringen Sie die linke und die rechte Gleitschiene an den Seiten des Gehäuses an. Sie werden auf jeder Seite mit sechs metrischen Schrauben befestigt. Als Hilfe bei der Ausrichtung sind die Gleitschienen mit **LH – Front** (Vorne links) und **RH – Front** (Vorne rechts) gekennzeichnet. Darüber hinaus weisen die Schienen jeweils an dem Ende, das in Richtung der Rückseite des Gehäuses befestigt wird, eine Verjüngung auf.<br/>

    ![Befestigen von Gleitschienen am Gehäuse](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

   **Befestigen der Gleitschienen an den Seiten des Gehäuses**

    Label | Description
    ----- | -----------
    1     | M 3x4 button-head screws
    2     | Chassis slides

3. Befestigen Sie die linke und die rechte Schienenbaugruppe an den vertikalen Blechwinkeln des Rackschranks. Die Halterungen sind mit **LH** (Links), **RH** (Rechts) und **This side up** (Diese Seite nach oben) gekennzeichnet, um Sie bei der richtigen Ausrichtung zu unterstützen.

4. Suchen Sie die Fixierungsstifte, die sich vorne und hinten an der Schienenbaugruppe befinden. Verlängern Sie die Schiene, sodass sie zwischen die Rackpfosten passt, und führen Sie die Stifte in die Bohrungen der vertikalen Blechwinkel am vorderen und hinteren Pfosten ein. Achten Sie darauf, dass die Schienenbaugruppe waagerecht ausgerichtet ist.

5. Sichern Sie die Schienenbaugruppe mit zwei der mitgelieferten metrischen Schrauben an den vertikalen Blechwinkeln des Racks. Verwenden Sie eine Schraube an der Vorderseite und eine Schraube an der Rückseite.

6. Wiederholen Sie diese Schritte für die andere Schienenbaugruppe.<br/>

     ![Befestigen von Gleitschienen am Rackschrank](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Befestigen der Schienenbaugruppen am Rack**

     Bezeichnung | Beschreibung
     ----- | -----------
     1 | Klemmschraube
     2 | Vierkantloch-Schraube für vorderen Rackpfosten
     3 | Vordere Fixierungsstifte der linken Schienenbaugruppe
     4 | Klemmschraube
     5 | Hintere Fixierungsstifte der linken Schienenbaugruppe

### Einbauen des Geräts in das Rack

Führen Sie die folgenden Schritte aus, um das Gerät unter Verwendung der soeben installierten Schienen in das Rack einzubauen.

#### So bauen Sie das Gerät ein

1. Heben Sie das Gehäuse zusammen mit einem Helfer an, und richten sie es an den Rackschienen aus. 

2. Führen Sie das Gerät sorgfältig in die Schienen ein, und schieben Sie es dann vollständig in den Rackschrank hinein.<br/>

    ![Einführen des Geräts in das Rack](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Einbauen des Geräts in das Rack**

3. Sichern Sie das Gehäuse im Rack am linken und am rechten Flansch mit je einer der mitgelieferten Kreuzschlitzschrauben.

4. Bringen Sie die Flanschkappen an, indem Sie diese an der vorgesehenen Position andrücken und einrasten lassen.<br/>

     ![Anbringen der Flanschkappen](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
 
    **Anbringen der Flanschkappen**

     Bezeichnung | Beschreibung
     ----- | -----------
     1 | Befestigungsschraube für Gehäuse

Im nächsten Schritt verkabeln Sie das Gerät für die Stromversorgung, die Netzwerkverbindung und den seriellen Zugriff.

## Verkabeln des StorSimple 8100-Geräts

Im Folgenden wird erläutert, wie Sie das StorSimple 8100-Gerät für die Stromversorgung, die Netzwerkverbindung und serielle Verbindungen verkabeln.

### Voraussetzungen

Bevor Sie mit dem Verkabeln des Geräts beginnen können, benötigen Sie Folgendes:

- Vollständig ausgepacktes Speichergerät

- Zwei Netzkabel, die zum Lieferumfang des Geräts gehören

- Zugang zu zwei PDUs (Power Distribution Units) (empfohlen)

- Netzwerkkabel

- Mitgelieferte serielle Kabel

- Seriell-USB-Konverter, für den der entsprechende Treiber auf dem PC installiert ist (sofern erforderlich)

- Mitgelieferte einzelne QSFP-zu-SFP+-Adapter für die Verwendung mit 10-GbE-Netzwerkschnittstellen

- [Unterstützte Hardware für 10-GbE-Netzwerkschnittstellen auf Ihrem StorSimple-Gerät](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)


### Stromverkabelung

Das Gerät enthält redundante Stromversorgungs- und Kühleinheiten (Power and Cooling Modules, PCMs). Beide PCMs müssen installiert und mit unterschiedlichen Stromquellen verbunden sein, um eine hohe Verfügbarkeit sicherzustellen.

Führen Sie die folgenden Schritte aus, um das Gerät für die Stromversorgung zu verkabeln.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### Netzwerkverkabelung

Das Gerät besitzt eine Konfiguration mit aktivem Standbymodus: Zu jedem Zeitpunkt ist ein Controllermodul aktiv und verarbeitet alle Datenträger- und Netzwerkvorgänge, während sich das andere Controllermodul im Standbymodus befindet. Wenn ein Controller ausfällt, wird der Standbycontroller sofort aktiviert und setzt alle Datenträger- und Netzwerkvorgänge fort.

Verkabeln Sie Ihr Gerätenetzwerk wie in den folgenden Schritten beschrieben, um dieses redundante Controllerfailover zu unterstützen.

#### So verkabeln Sie das Gerät für die Netzwerkverbindung

1. Das Gerät verfügt über sechs Netzwerkschnittstellen an jedem Controller: vier Ethernet-Anschlüsse mit 1 GBit/s und zwei Ethernet-Anschlüsse mit 10 GBit/s. Identifizieren Sie die verschiedenen Datenanschlüsse an der Rückwand des Geräts.

    ![Rückwand des 8100-Geräts](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Rückseite des Geräts mit Datenanschlüssen**
 
     Bezeichnung | Beschreibung
     ------- | -----------
     0,1,4,5 | 1-GbE-Netzwerkschnittstellen
     2,3 | 10-GbE-Netzwerkschnittstellen
     6 | Serielle Anschlüsse

2. Um eine hohe Verfügbarkeit sicherzustellen, sind für das Gerät mindestens zwei Verbindungen pro Controller erforderlich.
    1. Der DATA 0-Anschluss wird automatisch über die serielle Konsole des Geräts aktiviert und konfiguriert. Zusätzlich zu DATA 0 muss ein weiterer Datenanschluss über das Verwaltungsportal konfiguriert werden. 
    2. Identifizieren Sie identische Netzwerkschnittstellen auf jedem Controller. Wenn Sie z. B. DATA 0 und DATA 3 bei einem der Controller verbinden, müssen Sie DATA 0 und DATA 3 auch am anderen Controller verbinden. 

3. Stellen Sie folgende Verbindungen her, um eine hohe Verfügbarkeit sicherzustellen:
    1. Verbinden Sie identische Schnittstellen an jedem Controller mit dem relevanten Netzwerk, um die Verfügbarkeit bei einem Controllerfehler sicherzustellen.
    2. Verbinden Sie die Schnittstellen an jedem Controller mit mindestens zwei unterschiedlichen Switches, um die Verfügbarkeit bei einem Switchfehler sicherzustellen.
    3. Verbinden Sie den DATA 0-Anschluss mit dem primären LAN (Netzwerk mit Internetzugriff). Die anderen Datenanschlüsse können in Abhängigkeit von der vorgesehenen Rolle mit dem SAN/iSCSI-LAN (VLAN)-Segment des Netzwerks verbunden werden.

    Konfigurieren Sie mindestens eine Netzwerkschnittstelle für den Cloudzugriff und eine für iSCSI. Konfigurieren Sie für hohe Verfügbarkeit und Leistung zwei Paare von Netzwerkschnittstellen auf jedem Controller. Die Netzwerkverkabelung ist im folgenden Diagramm dargestellt. (Die Mindestkonfiguration des Netzwerks ist durch durchgängige blaue Linien gekennzeichnet. Die für hohe Verfügbarkeit und Leistung zusätzlich erforderliche Konfiguration wird durch die gepunkteten Linien dargestellt.)

    ![Netzwerkverkabelung des 2 HE-Geräts](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Netzwerkverkabelung des Geräts**

    Bezeichnung | Beschreibung
    ----- | -----------
     Eine Datei | LAN mit Internetzugriff
     B | Controller 0
     C | PCM 0
     D | Controller 1
     E | PCM 1
     F, G | Host
     0-5 | Netzwerkschnittstellen
   
### Verkabelung des seriellen Anschlusses

Führen Sie die folgenden Schritte aus, um den seriellen Anschluss zu verkabeln.

#### So verkabeln Sie das Gerät für serielle Verbindungen

1. Das Gerät verfügt über einen seriellen Anschluss an jedem Controller, der durch ein Schraubenschlüsselsymbol gekennzeichnet ist. Sehen Sie sich die Abbildung im Abschnitt [Netzwerkverkabelung](#network-cabling) an, um die seriellen Anschlüsse an der Rückwand des Geräts zu identifizieren. 

2. Identifizieren Sie den aktiven Controller anhand der Rückwand des Geräts. Eine blinkende blaue LED zeigt an, dass der Controller aktiv ist.

3. Verwenden Sie die mitgelieferten seriellen Kabel (bei Bedarf den USB-Seriell-Konverter für Ihren Laptop), und verbinden Sie die Konsole oder den Computer (mit Terminalemulation zum Gerät) mit dem seriellen Anschluss des aktiven Controllers.

4. Installieren Sie die Seriell-USB-Treiber (im Lieferumfang des Geräts enthalten) auf Ihrem Computer.

5. Richten Sie die serielle Verbindung wie folgt ein: 115.200 Baud, 8 Datenbits, 1 Stoppbit, keine Parität und keine Flusssteuerung.

6. Stellen Sie sicher, dass die Verbindung funktioniert, indem Sie auf der Konsole die EINGABETASTE drücken. Ein Menü der seriellen Konsole sollte angezeigt werden.

>[AZURE.NOTE]**Lights-Out-Management:** Wenn das Gerät in einem Remoterechenzentrum oder in einem Computerraum mit beschränktem Zugriff installiert ist, stellen Sie sicher, dass die seriellen Verbindungen zu beiden Controllern immer mit einem Switch einer seriellen Konsole oder einem ähnlichen Gerät verbunden sind. Dies ermöglicht bei Netzwerkunterbrechungen oder unerwarteten Fehlern Out-of-Band-Remotesteuerungs- und -Supportvorgänge.

Das Gerät ist jetzt für Stromversorgung, Netzwerkzugriff und serielle Konnektivität verkabelt. Im nächsten Schritt konfigurieren Sie die Software auf Ihrem Gerät.

## Nächste Schritte

Sie können nun [Ihr lokales StorSimple-Gerät bereitstellen und konfigurieren](storsimple-deployment-walkthrough.md).
 

<!---HONumber=August15_HO9-->