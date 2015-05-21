<properties
   pageTitle="Installieren von regelmäßigen Updates über Windows PowerShell für StorSimple"
   description="Beschreibt die Updatefunktion für StorSimple und Windows PowerShell für StorSimple, um regelmäßige Updates zu installieren."
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
   ms.date="04/27/2015"
   ms.author="v-sharos" />

###So installieren Sie regelmäßige Updates über Windows PowerShell für StorSimple

1. Öffnen Sie die serielle Konsole des Geräts, und wählen Sie Option 1, **Anmeldung mit Vollzugriff**, aus. Geben Sie das Kennwort ein. Das Standardkennwort lautet *Password1*. 

2. Geben Sie an der Eingabeaufforderung Folgendes ein:

    **Get-HcsUpdateAvailability**

    Sie werden benachrichtigt, wenn Updates verfügbar sind und ob diese Updates mit oder ohne Unterbrechungen installiert werden können.

3. Geben Sie an der Eingabeaufforderung Folgendes ein:

    **Start-HcsUpdate**

    Der Updatevorgang wird gestartet.

> [AZURE.IMPORTANT]
>
> - Dieser Befehl gilt nur für regelmäßige Updates. Dieser Befehl muss nur auf einem Controller ausgeführt werden, es werden jedoch beide Controller aktualisiert. 
> - Möglicherweise werden Sie während des Updatevorgangs ein Failover des Controllers bemerken. Dieses Failover wirkt sich jedoch nicht auf die Systemverfügbarkeit oder den Betrieb aus.

<!--HONumber=52-->
