<properties 
   pageTitle="StorSimple-SAS-Verkabelung für 8600 | Microsoft Azure"
   description="Erläutert, wie Sie die SAS-Kabel an das StorSimple 8600-Gerät anschließen."
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
   ms.date="08/06/2015"
   ms.author="alkohli" />

#### So schließen Sie die SAS-Kabel an

1. Identifizieren Sie das primäre Gehäuse und das EBOD-Gehäuse. Die beiden Gehäuse können anhand ihrer Rückwände identifiziert werden. Die folgende Abbildung bietet eine Orientierung. 

    ![Rückseite des primären Gehäuses und des EBOD-Gehäuses](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)

    **Rückseite des primären Gehäuses und des EBOD-Gehäuses**

    |Bezeichnung|Beschreibung|
    |:----|:----------|
    |1|Primäres Gehäuse|
    |2|EBOD-Gehäuse|

2. Suchen Sie am primären und am EBOD-Gehäuse nach der jeweiligen Seriennummer. Der Aufkleber mit der Seriennummer befindet sich jeweils an der hinteren Lasche des Gehäuses. Die Seriennummern beider Gehäuse müssen identisch sein. Wenn die Seriennummern nicht übereinstimmen, [kontaktieren Sie umgehend den Microsoft-Support](storsimple-contact-microsoft-support.md). Die folgende Abbildung zeigt die Position der Seriennummern.

    ![Rückansicht des Gehäuses mit Seriennummer](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)

    **Position des Aufklebers mit der Seriennummer**

    |Bezeichnung|Beschreibung|
    |:----|:----------|
    |1|Lasche des Gehäuses|

3. Im nächsten Schritt verwenden Sie die mitgelieferten SAS-Kabel, um das EBOD-Gehäuse folgendermaßen mit dem primären Gehäuse zu verbinden:

    1. Im nächsten Schritt identifizieren Sie die vier SAS-Anschlüsse am primären und am EBOD-Gehäuse. Die SAS-Anschlüsse sind am primären Gehäuse mit "EBOD" und am EBOD-Gehäuse mit "CTRL" beschriftet, wie unten in der Abbildung zur SAS-Verkabelung dargestellt.

    2. Verwenden Sie die mitgelieferten SAS-Kabel, um die EBOD-Anschlüsse mit den CTRL-Anschlüssen zu verbinden.

    3. Der EBOD-Anschluss am Controller 0 sollte mit dem CTRL-Anschluss am EBOD-Controller 0 verbunden sein. Der EBOD-Anschluss am Controller 1 sollte mit dem CTRL-Anschluss am EBOD-Controller 1 verbunden sein. Der Vorgang wird in der folgenden Abbildung veranschaulicht.
																	
     ![SAS-Verkabelung des Geräts](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)

     **SAS-Verkabelung**

    |Bezeichnung|Beschreibung|
    |:----|:----------|
    |Eine Datei|Primäres Gehäuse|
    |B|EBOD-Gehäuse|
    |1|Controller 0|
    |2|Controller 1|
    |3|EBOD-Controller 0|
    |4|EBOD-Controller 1|
    |5, 6|SAS-Anschlüsse am primären Gehäuse (mit der Beschriftung "EBOD")|
    |7, 8|SAS-Anschlüsse am EBOD-Gehäuse (mit der Beschriftung "CTRL")|

<!---HONumber=August15_HO7-->