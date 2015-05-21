<properties
   pageTitle="Wechseln in den Wartungsmodus"
   description="Erläutert, wie das StorSimple-Gerät in den Wartungsmodus versetzt wird."
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

###So wechseln Sie in den Wartungsmodus

1. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d. h. die** Anmeldung mit Vollzugriff**.

2. Geben Sie das Kennwort ein. Das Standardkennwort lautet **Password1**.

3. Geben Sie an der Eingabeaufforderung Folgendes ein:

    **Enter-HcsMaintenanceMode**

4. Es wird eine Warnmeldung angezeigt, die Sie darüber informiert, dass durch den Wartungsmodus alle E/A-Anforderungen unterbrochen werden und die Verbindung mit dem Verwaltungsportal getrennt wird. Sie werden aufgefordert, dies zu bestätigen. Geben Sie **J** ein, um den Wartungsmodus zu beginnen.

    Beide Controller werden neu gestartet. Wenn der Neustart abgeschlossen ist, wird eine andere Meldung angezeigt, die besagt, dass das Gerät in den Wartungsmodus versetzt wird.

<!--HONumber=52-->
