<properties
   pageTitle="Installieren von Wartungsmodus-Updates"
   description="Beschreibt die Installation von Wartungsmodus-Updates über Windows PowerShell für StorSimple."
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
   ms.date="04/21/2015"
   ms.author="v-sharos" />

###So installieren Sie Wartungsmodus-Updates über Windows PowerShell für StorSimple

1. Wenn Sie dies noch nicht getan haben, greifen Sie auf die serielle Konsole des Geräts zu, und wählen Sie Option 1, d. h. die **Anmeldung mti Vollzugriff**. 

2. Geben Sie das Kennwort ein. Das Standardkennwort lautet **Password1**.

3. Geben Sie an der Eingabeaufforderung Folgendes ein:

   **Get-HcsUpdateAvailability**
    
    You will be notified if updates are available and whether the updates are disruptive or non-disruptive.

4. Um Updates anzuwenden, durch die der Betrieb unterbrochen wird, müssen Sie das Gerät in den Wartungsmodus versetzen. Anweisungen dazu finden Sie unter [So wechseln Sie in den Wartungsmodus](#to-enter-maintenance-mode).

5. Wenn sich Ihr Gerät im Wartungsmodus befindet, geben Sie an der Eingabeaufforderung folgenden Befehl ein:

    **Start-HcsUpdate**

6. Sie werden aufgefordert, diesen Schritt zu bestätigen. Nachdem Sie die Updates bestätigt haben, werden sie auf dem Controller installiert, auf den Sie gerade zugreifen. Nachdem die Updates installiert wurden, wird der Controller neu gestartet.

7. Nach Abschluss des Neustarts des ersten Controllers, stellen Sie eine Verbindung mit dem anderen Controller her, und führen Sie die Schritte 1 bis 6 für diesen durch.

8. Nachdem beide Controller aktualisiert wurden, beenden Sie den Wartungsmodus. Anweisungen dazu finden Sie unter [So beenden Sie den Wartungsmodus]\](\#to-exit-maintenance-mode\).

<!--HONumber=52-->
