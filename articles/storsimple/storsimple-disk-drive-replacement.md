<properties 
   pageTitle="Austauschen eines Festplattenlaufwerks im StorSimple-Gerät | Microsoft Azure"
   description="Erläutert, wie ein Laufwerk in einem StorSimple-Hauptgerät oder EBOD-Gehäuse ausgetauscht wird."
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
   ms.workload="TBD"
   ms.date="08/05/2015"
   ms.author="alkohli" />


# Austauschen eines Festplattenlaufwerks im StorSimple-Gerät

## Übersicht

In diesem Tutorial wird erläutert, wie Sie ein schlecht funktionierendes oder fehlerhaftes Festplattenlaufwerk eines Microsoft Azure StorSimple-Geräts austauschen. Führen Sie zum Austauschen eines Festplattenlaufwerks die folgenden Schritte aus:

- Entriegeln der Zugangssperre

- Ausbauen des Laufwerks

- Einbauen des Austauschlaufwerks

>[AZURE.IMPORTANT]Überprüfen Sie vor dem Ausbauen und Austauschen eines Laufwerks die Sicherheitsinformationen unter [Austauschen von StorSimple-Hardwarekomponenten](storsimple-hardware-component-replacement.md).

## Entriegeln der Zugangssperre

In dieser Vorgehensweise wird erläutert, wie die Zugangssperren eines StorSimple-Geräts beim Austausch eines Laufwerks entriegelt und wieder verriegelt werden. Die Zugangssperren befinden sich in den Laufwerkstragegriffen und sind durch eine kleine Öffnung im Riegelteil des jeweiligen Griffs zugänglich. Laufwerke werden mit den Sperren in der verriegelten Position ausgeliefert.

#### So entriegeln Sie eine Zugangssperre

1. Führen Sie den Sperrschlüssel (ein "manipulationssicherer" T10-Schraubendreher, der von Microsoft mitgeliefert wurde), vorsichtig in die Öffnung im Griff und in die zugehörige Vertiefung ein. 

    >[AZURE.NOTE]Ist die Zugangssperre aktiviert, ist in der Öffnung das rote Kennzeichen zu sehen.

    ![Verriegeltes Laufwerk](./media/storsimple-disk-drive-replacement/IC741056.png)

    **Abbildung 1** Verriegelte Zugangssperre

    |Bezeichnung|Beschreibung|
    |:----|:----------|
    |1|Öffnung mit Kennzeichen|
    |2|Zugangssperre|

2. Drehen Sie den Schlüssel gegen den Uhrzeigersinn, bis das rote Kennzeichen nicht mehr in der Öffnung über dem Schlüssel zu sehen ist.

3. Entfernen Sie den Schlüssel.

    ![Entriegeltes Laufwerk](./media/storsimple-disk-drive-replacement/IC741057.png)

    **Abbildung 2** Entriegeltes Laufwerk

4. Das Laufwerk kann nun ausgebaut werden.

Führen Sie die Schritte in umgekehrter Reihenfolge aus, um die Zugangssperre zu verriegeln.

## Ausbauen des Laufwerks

Ein StorSimple-Gerät unterstützt eine RAID-10-ähnliche Speicherplatzkonfiguration. Dies bedeutet, dass das Gerät mit einem einzelnen fehlerhaften SSD- (Solid State Drive) oder HDD-Laufwerk (Hard Disk Drive) normal funktionieren kann.

>[AZURE.IMPORTANT]
>
>- Falls es im System mehrere fehlerhafte Datenträger gibt, dürfen Sie immer nur jeweils eine SSD oder HDD aus dem System ausbauen. Andernfalls kann es zu Datenverlusten kommen.
>
>- Stellen Sie sicher, dass Sie eine Austausch-SSD in einem Einschubfach platzieren, das zuvor eine SSD enthalten hat. Auf ähnliche Weise setzen Sie eine Austauschfestplatte in ein Einschubfach ein, das zuvor eine HDD enthalten hat.
>
>- Im Verwaltungsportal sind Einschubfächer von 0 bis 11 nummeriert. Wenn das Portal also anzeigt, dass ein Datenträger in Einschubfach 2 ausgefallen ist, finden Sie diesen Datenträger im Gerät im dritten Einschubfach von links oben.

Laufwerke können bei laufendem Betrieb des Systems ausgebaut und ausgetauscht werden.

#### So bauen Sie ein Laufwerk aus

1. Navigieren Sie zum Ermitteln des fehlerhaften Datenträgers im Verwaltungsportal zu **Geräte** > **Wartung** > **Hardwarestatus**. Weil ein Datenträger im primären Gehäuse und/oder in einem EBOD-Gehäuse ausfallen kann (wenn Sie ein Modell vom Typ 8600 verwenden), sehen Sie sich die Status der Datenträger unter **Freigegebene Komponenten** und unter **EBOD-Gehäuse – Freigegebene Komponenten** an. Ein fehlerhafter Datenträger in einem der Gehäuse wird mit rotem Status angezeigt.

2. Suchen Sie auf der Vorderseite des primären Gehäuses oder des EBOD-Gehäuses nach den Laufwerken. Für den fehlerhaften Datenträger leuchtet die gelbe LED.

3. Wenn der Datenträger nicht verriegelt ist, fahren Sie mit dem nächsten Schritt fort. Wenn der Datenträger verriegelt ist, entriegeln Sie ihn mithilfe des Verfahrens unter [Entriegeln der Zugangssperre](#disengage-the-antitamper-lock).

4. Drücken Sie den schwarzen Riegel am Laufwerksträgermodul, und ziehen Sie den Laufwerksträgergriff heraus und von der Vorderseite des Gehäuses fort.

    ![Freigeben des Laufwerkgriffs](./media/storsimple-disk-drive-replacement/IC741051.png)

    **Abbildung 3** Entriegeln des Laufwerkgriffs

5. Wenn der Laufwerksträgergriff vollständig geöffnet ist, ziehen Sie den Laufwerksträger aus dem Gehäuse heraus.

    ![Herausziehen des Datenträgers aus dem Laufwerk](./media/storsimple-disk-drive-replacement/IC741052.png)
    
    **Abbildung 4** Herausziehen des Laufwerks

## Einbauen des Austauschlaufwerks

Nachdem ein Laufwerk in einem Microsoft Azure StorSimple-Gerät ausgefallen ist und Sie es ausgebaut haben, gehen Sie wie folgt vor, um ein neues Laufwerk einzubauen.

#### So bauen Sie ein Laufwerk ein

1. Öffnen Sie den Laufwerksträgergriff vollständig, wie dies in der folgenden Abbildung zu sehen ist.

    ![Laufwerk mit ausgefahrenem Griff](./media/storsimple-disk-drive-replacement/IC741044.png)

    **Abbildung 5** Laufwerksträger mit geöffnetem Griff

2. Schieben Sie den Laufwerksträger vollständig in das Gehäuse ein.

    ![Einschieben des Datenträgers in den Laufwerksträger](./media/storsimple-disk-drive-replacement/IC741045.png)

    **Abbildung 6** Einschieben des Laufwerkträgers in das Gehäuse

3. Nachdem der Laufwerksträger eingeschoben wurde, schließen Sie den Laufwerksträgergriff, indem Sie den Laufwerksträger weiter in das Gehäuse drücken, bis der Laufwerksträgergriff in der verriegelten Position einrastet.

4. Sichern Sie den Trägergriff mit dem Sperrschlüssel, der von Microsoft mitgeliefert wurde (manipulationssicherer Torx-Schraubendreher), in seiner Position, indem Sie die Sperrschraube eine Vierteldrehung im Uhrzeigersinn drehen.

5. Vergewissern Sie sich, dass der Austausch erfolgreich war und das Laufwerk betriebsbereit ist. Navigieren Sie dazu im Verwaltungsportal zu **Wartung** > **Hardwarestatus**. Unter **Freigegebene Komponenten** oder **EBOD-Gehäuse – Freigegebene Komponenten** muss der Laufwerksstatus "Grün" angezeigt werden, was heißt, dass das Laufwerk betriebsbereit ist.

    >[AZURE.NOTE]Es kann mehrere Stunden dauern, bis für ein Laufwerk nach einem Austausch der Status "Grün" angezeigt wird.

## Nächste Schritte

Erfahren Sie mehr zum [Austauschen von StorSimple-Hardwarekomponenten](storsimple-hardware-component-replacement.md).

<!---HONumber=August15_HO6-->