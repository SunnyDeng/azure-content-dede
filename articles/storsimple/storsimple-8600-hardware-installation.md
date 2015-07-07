<properties 
   pageTitle="Installieren des StorSimple 8600-Geräts"
   description="Beschreibt, wie Sie das StorSimple 8600-Gerät auspacken, in ein Rack einbauen und verkabeln."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/09/2015"
   ms.author="v-sharos" />

# Installieren des StorSimple 8600-Geräts

## Übersicht
Das Microsoft Azure StorSimple 8600-Gerät umfasst zwei Gehäuse: ein primäres Gehäuse und ein EBOD-Gehäuse. In diesem Lernprogramm wird erläutert, wie die StorSimple 8600-Gerätehardware vor dem Konfigurieren der StorSimple-Software ausgepackt, in ein Rack eingebaut und verkabelt wird.

## Auspacken des StorSimple 8600-Geräts

Die folgenden Schritte bieten klare und ausführliche Anweisungen zum Auspacken des StorSimple 8600-Speichergeräts. Das Gerät wird in zwei separaten Kartons versendet: einem für das primäre Gehäuse und einem für das EBOD-Gehäuse.

### Vorbereitungen zum Auspacken des Geräts

Lesen Sie die folgenden Informationen, bevor Sie das Gerät auspacken.

>[AZURE.WARNING]![Symbol für hohes Gewicht](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png)
>
> 1. Stellen Sie aufgrund des Gewichts des Geräts sicher, dass zwei Personen verfügbar sind, wenn Sie den Vorgang manuell durchführen. Ein vollständig konfiguriertes Gerät kann bis zu 32 kg wiegen.
>
> 2. Legen Sie den Karton auf einen flachen, ebenen Untergrund.

Führen Sie dann die folgenden Schritte aus, um das Gerät auszupacken.

#### So packen Sie das Gerät aus

1. Überprüfen Sie die Kartons und das Verpackungsmaterial auf Risse, Schnitte, Wasserschäden oder anderweitige offensichtliche Beschädigungen. Wenn der Karton oder die Verpackung stark beschädigt sind, öffnen Sie den Karton nicht. Wenden Sie sich an den Microsoft Support, um zu ermitteln, ob sich das Gerät in funktionsfähigem Zustand befindet.

2. Öffnen Sie die beiden Kartons, und packen Sie das primäre Gehäuse und das EBOD-Gehäuse aus. Die folgende Abbildung zeigt das ausgepackte Speichergerät.
 
    **Abbildung 1: Das ausgepackte Speichergerät**

    ![Auspacken des Speichergeräts](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)

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
  - Vier einzelne QSFP-zu-SFP+-Adapter für die Verwendung mit 10-GbE-Netzwerkschnittstellen
  - Zwei Rackmontagekits (vier Seitenschienen mit Befestigungsteilen, davon jeweils zwei für das primäre Gehäuse und das EBOD-Gehäuse), ein Kit pro Karton
  - Dokumentation "Erste Schritte"

    Wenn Sie eines der oben aufgeführten Teile nicht erhalten haben, wenden Sie sich an den Microsoft Support.

Im nächsten Schritt bauen Sie das Gerät in ein Rack ein.

## Einbauen des StorSimple 8600-Geräts in ein Rack

Führen Sie die folgenden Schritte aus, um das StorSimple 8600-Speichergerät in ein 19-Zoll-Standardrack mit Pfosten auf Vorder- und Rückseite einzubauen. Das Gerät wird mit zwei Gehäusen geliefert: einem primären Gehäuse und einem EBOD-Gehäuse. Beide müssen in ein Rack eingebaut werden.

Die Installation umfasst mehrere Schritte, die im Folgenden detailliert erläutert werden.

### Vorbereiten des Standorts

Die Gehäuse werden in ein 19-Zoll-Standardrack mit Pfosten an Vorder- und Rückseite eingebaut. Gehen Sie folgendermaßen vor, um die Rackmontage vorzubereiten.

#### So bereiten Sie den Standort für die Rackmontage vor

1. Vergewissern Sie sich, dass das primäre Gehäuse und das EBOD-Gehäuse sicher auf einer flachen, stabilen und ebenen Arbeitsfläche (oder ähnlich) liegen.

2. Stellen Sie sicher, dass am vorgesehenen Standort eine Standardstromversorgung von einer unabhängigen Quelle oder eine Rack-PDU (Power Distribution Unit) mit unterbrechungsfreier Stromversorgung (USV) vorhanden ist.

3. Vergewissern Sie sich, dass das Rack, in das Sie die Gehäuse einbauen möchten, Platz für einen Einschub mit 4 HE (2 x 2 HE) bietet.

>[AZURE.WARNING]![Symbol für hohes Gewicht](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png)
> 
> Stellen Sie aufgrund des Gewichts des Geräts sicher, dass zwei Personen verfügbar sind, wenn Sie die Einrichtung des Geräts manuell durchführen. Ein vollständig konfiguriertes Gehäuse kann bis zu 32 kg wiegen.

### Voraussetzungen

Die Gehäuse sind für den Einbau in einen 19-Zoll-Standardrackschrank vorgesehen, wobei Folgendes gilt:

- Mindesttiefe des Racks von 707 mm (27,84 Zoll) von Pfosten zu Pfosten
- Maximales Gewicht des Geräts von 32 kg
- Maximaler Gegendruck von 5 Pascal (0,5 mm Wassersäule)

### Schienenkit für die Rackmontage

Im Lieferumfang ist ein Satz Montageschienen für die Verwendung mit einem 19-Zoll-Rackschrank enthalten. Die Schienen wurden für das maximale Gehäusegewicht getestet. Mit den Schienen können mehrere Gehäuse in das Rack eingebaut werden, ohne dass dabei Platz verloren geht. Beginnen Sie mit dem Einbau des EBOD-Gehäuses.

#### So befestigen Sie das EBOD-Gehäuse an den Schienen

1. Legen Sie das Gehäuse auf die Arbeitsfläche, und ziehen Sie die Kappen vom rechten und linken vorderen Flansch ab. Die Flanschkappen sind einfach an den Flanschen eingerastet.

2. In der Regel sind die Schienen bereits werkseitig installiert. Ist dies nicht der Fall, bringen Sie die linke und die rechte Gleitschiene an den Seiten des Gehäuses an. Sie werden auf jeder Seite mit sechs metrischen Schrauben befestigt. Als Hilfe bei der Ausrichtung sind die Gleitschienen mit **LH – Front** (Vorne links) und **RH – Front** (Vorne rechts) gekennzeichnet. Darüber hinaus weisen die Schienen jeweils an dem Ende, das in Richtung der Rückseite des Gehäuses befestigt wird, eine Verjüngung auf.

    **Abbildung 2: Befestigen der Gleitschienen an den Seiten des Gehäuses**

    ![Befestigen von Gleitschienen am Gehäuse](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    Bezeichnung | Beschreibung
    ----- | -----------
    1 | Rundkopfschrauben M3 x 4
    2 | Gehäusegleitschiene
 
3. Befestigen Sie die linke und die rechte Schienenbaugruppe an den vertikalen Blechwinkeln des Rackschranks. Die Halterungen sind mit **LH** (Links), **RH** (Rechts) und **This side up** (Diese Seite nach oben) gekennzeichnet, um Sie bei der richtigen Ausrichtung zu unterstützen.

4. Suchen Sie die Fixierungsstifte, die sich vorne und hinten an der Schienenbaugruppe befinden. Verlängern Sie die Schiene, sodass sie zwischen die Rackpfosten passt, und führen Sie die Stifte in die Bohrungen der vertikalen Blechwinkel am vorderen und hinteren Pfosten ein. Achten Sie darauf, dass die Schienenbaugruppe waagerecht ausgerichtet ist.

5. Sichern Sie die Schienenbaugruppe mit zwei der mitgelieferten metrischen Schrauben an den vertikalen Blechwinkeln des Racks. Verwenden Sie eine Schraube an der Vorderseite und eine Schraube an der Rückseite.

6. Wiederholen Sie diese Schritte für die andere Schienenbaugruppe.

    **Abbildung 3: Befestigen der Schienenbaugruppen am Rack**

     ![Befestigen von Gleitschienen am Rackschrank](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

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

    **Abbildung 4: Einbauen des Gehäuses in das Rack**

    ![Einführen des Geräts in das Rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

3. Sichern Sie das Gehäuse im Rack am linken und am rechten Flansch mit je einer der mitgelieferten Kreuzschlitzschrauben.

4. Bringen Sie die Flanschkappen an, indem Sie diese an der vorgesehenen Position andrücken und einrasten lassen.

    **Abbildung 5: Anbringen der Flanschkappen**

     ![Anbringen der Flanschkappen](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
 
     Bezeichnung | Beschreibung
     ----- | -----------
     1 | Befestigungsschraube für Gehäuse


### Einbauen des primären Gehäuses in das Rack

Bauen Sie nach dem Einbau des EBOD-Gehäuses das primäre Gehäuse ein, indem Sie die gleichen Schritte ausführen.

> [AZURE.NOTE]
> 
> - Zwischen dem primären Gehäuse und dem EBOD-Gehäuse können sich leere Einschubfächer im Rack befinden. 
> - Verwenden Sie das mitgelieferte 2 m lange SAS-Kabel, um das primäre Gehäuse mit dem EBOD-Gehäuse zu verbinden.
> - Es gibt keine Einschränkungen hinsichtlich der relativen Position der primären Einheit zur EBOD-Einheit. Daher kann das primäre Gehäuse im obersten Einschubfach und das EBOD-Gehäuse darunter platziert werden – oder umgekehrt.

Im nächsten Schritt verkabeln Sie das Gerät für die Stromversorgung, die Netzwerkverbindung und den seriellen Zugriff.

## Verkabeln des StorSimple 8600-Geräts

Im Folgenden wird erläutert, wie Sie das StorSimple 8600-Gerät für die Stromversorgung, die Netzwerkverbindung und serielle Verbindungen verkabeln.

### Voraussetzungen

Bevor Sie mit dem Verkabeln des Geräts beginnen können, benötigen Sie Folgendes:

- Das vollständig ausgepackte primäre Gehäuse und das vollständig ausgepackte EBOD-Gehäuse
- Vier Netzkabel (jeweils zwei für das primäre Gehäuse und das EBOD-Gehäuse), die zum Lieferumfang des Geräts gehören
- Zwei SAS-Kabel, die mit dem Gerät geliefert wurden, um das EBOD-Gehäuse mit dem primären Gehäuse zu verbinden
- Zugang zu zwei PDUs (Power Distribution Units) (empfohlen)
- Netzwerkkabel
- Mitgelieferte serielle Kabel
- Seriell-USB-Konverter, für den der entsprechende Treiber auf dem PC installiert ist (sofern erforderlich)
- Mitgelieferte einzelne QSFP-zu-SFP+-Adapter für die Verwendung mit 10-GbE-Netzwerkschnittstellen
- [Unterstützte Transceiver, Kabel und Switches für 10-GbE-Netzwerkschnittstellen](https://msdn.microsoft.com/library/azure/dn891474.aspx) 

### SAS-Verkabelung

Das Gerät umfasst ein primäres Gehäuse und ein EBOD-Gehäuse. Diese Gehäuse müssen über die SAS-Kabel miteinander verbunden werden.

Im nächsten Schritt identifizieren Sie das primäre Gehäuse und das EBOD-Gehäuse.

#### So identifizieren Sie das primäre Gehäuse und das EBOD-Gehäuse

1. Identifizieren Sie das primäre Gehäuse und das EBOD-Gehäuse. Sehen Sie sich dazu die Rückwand des jeweiligen Gehäuses an. Die folgende Abbildung bietet eine Orientierung.

    **Abbildung 6: Rückseite des primären Gehäuses und des EBOD-Gehäuses**

    ![Primäres Gehäuse und EBOD-Gehäuse](./media/storsimple-8600-hardware-installation/HCSBackplaneofprimaryandEBODenclosure.png)

     Bezeichnung | Beschreibung
     ----- | -----------
     1 | Primäres Gehäuse
     2 | EBOD-Gehäuse


2. Suchen Sie am primären Gehäuse und am EBOD-Gehäuse nach der jeweiligen Seriennumer. Der Aufkleber mit der Seriennummer befindet sich jeweils an der hinteren Lasche des Gehäuses. Die Seriennummern beider Gehäuse müssen identisch sein. Wenn die Seriennummern nicht übereinstimmen, wenden Sie sich umgehend an den Microsoft Support. Die folgende Abbildung zeigt die Position der Seriennummern.

    **Abbildung 7: Rückansicht des Gehäuses mit Position der Seriennummer**

    ![Position der Seriennummer](./media/storsimple-8600-hardware-installation/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)

     Bezeichnung | Beschreibung
     ----- | -----------
     1 | Lasche des Gehäuses


Im nächsten Schritt verwenden Sie die mitgelieferten SAS-Kabel, um das EBOD-Gehäuse mit dem primären Gehäuse zu verbinden.

#### So verbinden Sie das primäre Gehäuse mit dem EBOD-Gehäuse

1. Identifizieren Sie die vier SAS (Serial Attached SCSI)-Anschlüsse am primären Gehäuse und am EBOD-Gehäuse. Die SAS-Anschlüsse sind am primären Gehäuse mit "EBOD" und am EBOD-Gehäuse mit "CTRL" beschriftet, wie in Abbildung 8 dargestellt.

    **Abbildung 8: Position der SAS-Anschlüsse am primären Gehäuse und am EBOD-Gehäuse**

    ![Position der SAS-Anschlüsse](./media/storsimple-8600-hardware-installation/HCSSAScablingforyourdevice.png)

     Bezeichnung | Beschreibung
     ----- | -----------
     Eine Datei | Primäres Gehäuse
     B | EBOD-Gehäuse
     1 | Controller 0
     2 | Controller 1
     3 | EBOD-Controller 0
     4 | EBOD-Controller 1
     5,6 | SAS-Anschlüsse am primären Gehäuse (mit der Beschriftung "EBOD")
     7,8 | SAS-Anschlüsse am EBOD-Gehäuse (mit der Beschriftung "CTRL")

2. Verwenden Sie die mitgelieferten SAS-Kabel, um die EBOD-Anschlüsse mit den CTRL-Anschlüssen zu verbinden.

    Der EBOD-Anschluss am Controller 0 sollte mit dem CTRL-Anschluss am EBOD-Controller 0 verbunden sein. Der EBOD-Anschluss am Controller 1 sollte mit dem CTRL-Anschluss am EBOD-Controller 1 verbunden sein. Orientieren Sie sich dazu an Abbildung 8.

### Stromverkabelung

Sowohl das primäre Gehäuse als auch das EBOD-Gehäuse enthalten redundante Stromversorgungs- und Kühleinheiten (Power and Cooling Modules, PCMs). Die PCMs in beiden Gehäusen müssen installiert und mit unterschiedlichen Stromquellen verbunden sein, um eine hohe Verfügbarkeit sicherzustellen.

Führen Sie die folgenden Schritte aus, um das Gerät für die Stromversorgung zu verkabeln.

#### So verkabeln Sie das Gerät für die Stromversorgung

1. Stellen Sie sicher, dass sich die Netzschalter aller PCMs in ausgeschalteter Position befinden.

2. Schließen Sie an beide PCMs des primären Gehäuses die Netzkabel an. Die Netzkabel sind in der folgenden Abbildung rot dargestellt.

3. Stellen Sie sicher, dass die beiden PCMs des primären Gehäuses separate Stromquellen verwenden.

4. Schließen Sie die Netzkabel an die Rack-PDUs an, wie in der folgenden Abbildung dargestellt.

5. Wiederholen Sie die Schritte 2 bis 4 für das EBOD-Gehäuse.

6. Schalten Sie das EBOD-Gehäuse ein, indem Sie die Netzschalter der PCMs in die eingeschaltete Position bringen.

7. Stellen Sie sicher, dass das EBOD-Gehäuse eingeschaltet ist. Überprüfen Sie hierzu, ob die EBOD-Controller-LEDs (grüne LEDs an der Rückseite des Gehäuses) leuchten.

8. Schalten Sie nun das primäre Gehäuse ein, indem Sie die PCM-Schalter in die eingeschaltete Position bringen.

9. Stellen Sie sicher, dass das System eingeschaltet ist. Überprüfen Sie hierzu, ob die Controller-LEDs leuchten.

10. Vergewissern Sie sich, dass die Verbindung zwischen dem EBOD-Controller und dem Gerätecontroller aktiv ist, indem Sie die SAS-LEDs (vier LEDs neben dem SAS-Anschluss) am EBOD-Controller überprüfen. Die SAS-LEDs sollten grün leuchten.

> [AZURE.IMPORTANT]Damit eine hohe Verfügbarkeit des Systems sichergestellt ist, sollten Sie sich unbedingt an das im folgenden Diagramm dargestellte Stromverkabelungsschema halten.

   **Abbildung 9: Stromverkabelung des Geräts**

   ![Diagramm für die Stromverkabelung](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforPower.png)

     Label | Description
     ----- | -----------
     1     | Primary enclosure
     2     | PCM 0
     3     | PCM 1
     4     | Controller 0
     5     | Controller 1
     6     | EBOD controller 0
     7     | EBOD controller 1
     8     | EBOD enclosure
     9     | PDUs
 

### Netzwerkverkabelung

Das Gerät besitzt eine Konfiguration mit aktivem Standbymodus: Zu jedem Zeitpunkt ist ein Controllermodul aktiv und verarbeitet alle Datenträger- und Netzwerkvorgänge, während sich das andere Controllermodul im Standbymodus befindet. Wenn ein Controllerfehler auftritt, wird der Standbycontroller sofort aktiviert und setzt alle Datenträger- und Netzwerkvorgänge fort.

Verkabeln Sie Ihr Gerätenetzwerk wie in den folgenden Schritten beschrieben, um dieses redundante Controllerfailover zu unterstützen.

#### So verkabeln Sie das Gerät für die Netzwerkverbindung

1. Das Gerät verfügt über sechs Netzwerkschnittstellen an jedem Controller: vier Ethernet-Anschlüsse mit 1 GBit/s und zwei Ethernet-Anschlüsse mit 10 GBit/s. Mithilfe der folgenden Abbildung können Sie die Datenanschlüsse an der Rückwand des Geräts identifizieren.

    **Abbildung 10: Rückseite des Geräts mit Datenanschlüssen**

     ![Rückwand des 8600-Geräts](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
 
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

**Abbildung 11: Netzwerkverkabelung des Geräts**

![Netzwerkverkabelung des 4 HE-Geräts](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

Bezeichnung | Beschreibung
----- | -----------
Eine Datei | LAN mit Internetzugriff
B | Controller 0
C | PCM 0
D | Controller 1
E | PCM 1
F | EBOD-Controller 0
G | EBOD-Controller 1
H, I | Hosts (z. B. Dateiserver)
0-5 | Netzwerkschnittstellen
6 | Primäres Gehäuse
7 | EBOD-Gehäuse

### Verkabelung des seriellen Anschlusses

Führen Sie die folgenden Schritte aus, um den seriellen Anschluss zu verkabeln.

#### So verkabeln Sie das Gerät für serielle Verbindungen

1. Das Gerät verfügt über einen seriellen Anschluss an jedem Controller, der durch ein Schraubenschlüsselsymbol gekennzeichnet ist. Die Position der seriellen Anschlüsse an der Rückwand des Geräts ist in Abbildung 10 dargestellt.

2. Identifizieren Sie den aktiven Controller anhand der Rückwand des Geräts. Eine blinkende blaue LED zeigt an, dass der Controller aktiv ist.

3. Verwenden Sie das mitgelieferte serielle Kabel (bei Bedarf den USB-Seriell-Konverter für Ihren Laptop), und verbinden Sie die Konsole oder den Computer (mit Terminalemulation zum Gerät) mit dem seriellen Anschluss des aktiven Controllers.

4. Installieren Sie die Seriell-USB-Treiber (im Lieferumfang des Geräts enthalten) auf Ihrem Computer.

5. Richten Sie die serielle Verbindung wie folgt ein:
   - 115.200 Baud
   - 8 Datenbits
   - 1 Stoppbit
   - Keine Parität
   - **Keine** Flusssteuerung

6. Stellen Sie sicher, dass die Verbindung funktioniert, indem Sie auf der Konsole die EINGABETASTE drücken. Ein Menü der seriellen Konsole sollte angezeigt werden.

> [AZURE.NOTE]**Lights-Out-Management:** Wenn das Gerät in einem Remoterechenzentrum oder in einem Computerraum mit beschränktem Zugriff installiert ist, stellen Sie sicher, dass die seriellen Verbindungen zu beiden Controllern immer mit einem Switch einer seriellen Konsole oder einem ähnlichen Gerät verbunden sind. Dies ermöglicht bei einer Netzwerkunterbrechung oder unerwarteten Fehlern Out-of-Band-Remotesteuerungs- und -Supportvorgänge.

Das Gerät ist nun für Stromversorgung, Netzwerkzugriff und serielle Verbindungen verkabelt. Im nächsten Schritt konfigurieren Sie die Software auf Ihrem Gerät.

## Nächste Schritte

Sie können nun [Ihr lokales StorSimple-Gerät bereitstellen und konfigurieren](storsimple-deployment-walkthrough.md).
 

<!---HONumber=62-->