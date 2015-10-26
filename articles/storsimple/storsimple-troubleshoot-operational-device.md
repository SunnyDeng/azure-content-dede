<properties 
   pageTitle="Problembehandlung bei einem bereitgestellten StorSimple-Gerät | Microsoft Azure"
   description="Beschreibt die Diagnose und Behebung von Fehlern auf einem StorSimple-Gerät, das bereitgestellt und betriebsbereit ist."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/31/2015"
   ms.author="v-sharos" />

# Problembehandlung bei einem betriebsbereiten StorSimple-Gerät

## Übersicht

Dieser Artikel enthält Anleitungen zum Beheben von Konfigurationsproblemen, die bei einem bereitgestellten und betriebsbereiten StorSimple-Gerät auftreten können. Es werden allgemeine Probleme, mögliche Ursachen und empfohlene Schritte zum Beheben von Problemen bei der Ausführung von Microsoft Azure StorSimple beschrieben. Diese Informationen gelten sowohl für lokale physische als auch für virtuelle StorSimple-Geräte.

Am Ende dieses Artikels finden Sie eine Liste der Fehlercodes, die bei einem Microsoft Azure StorSimple-Vorgang möglicherweise ausgegeben werden. Zudem enthält der Artikel Informationen zum Beheben dieser Fehler.

## Verfahren des Setup-Assistenten für betriebsbereite Geräte

Mithilfe des Setup-Assistenten ([Invoke-HcsSetupWizard][1]) überprüfen Sie die Gerätekonfiguration und nehmen bei Bedarf Korrekturmaßnahmen vor.

Die Verfahren im Setup-Assistenten unterscheiden sich bei einem bereits konfigurierten und betriebsbereiten Gerät. Sie können nur die folgenden Einträge ändern:

- IP-Adresse, Subnetzmaske und Gateway
- Primärer DNS-Server
- Primärer NTP-Server
- Optionale Webproxy-Konfiguration

Der Setup-Assistent führt keine Vorgänge im Zusammenhang mit der Kennworterfassung und Geräteregistrierung durch.

## Fehler, die bei nachfolgenden Ausführungen des Setup-Assistenten auftreten

In der folgenden Tabelle sind die Fehler, die beim Ausführen des Setup-Assistenten auf einem betriebsbereiten Gerät auftreten können, mögliche Ursachen für diese Fehler sowie empfohlene Maßnahmen zur Problembehebung beschrieben.

| Nr. | Fehlermeldung oder -bedingung | Mögliche Ursachen | Empfohlene Maßnahme |
|:--- |:-------------------------- |:--------------- |:------------------ |
| 1 | Fehler 350032: Dieses Gerät wurde bereits deaktiviert. | Dieser Fehler wird angezeigt, wenn Sie den Setup-Assistenten auf einem deaktivierten Gerät ausführen. | [Wenden Sie sich an den Microsoft Support](storsimple-contact-microsoft-support.md) für weitere Schritte. Ein deaktiviertes Gerät kann nicht in Betrieb genommen werden. Sie müssen das Gerät möglicherweise auf die Werkseinstellungen zurücksetzen, bevor es erneut aktiviert werden kann. |
| 2 | Invoke-HcsSetupWizard : ERROR\_INVALID\_FUNCTION(Ausnahme von HRESULT: 0x80070001) | Das Update des DNS-Servers ist fehlgeschlagen. DNS-Einstellungen sind globale Einstellungen und gelten für alle aktivierten Netzwerkschnittstellen. | Aktivieren Sie die Schnittstelle, und wenden Sie die DNS-Einstellungen erneut an. Dies kann das Netzwerk für andere aktivierten Schnittstellen beeinträchtigen, da es sich um globale Einstellungen handelt. |
| 3 | Das Gerät wird im StorSimple-Manager-Dienstportal als online aufgeführt. Wenn Sie jedoch versuchen, die Mindestinstallation abzuschließen und die Konfiguration speichern, schlägt der Vorgang fehl. | Der Webproxy wurde während der ersten Installation nicht konfiguriert, obwohl ein Proxyserver tatsächlich vorhanden war. | Verwenden Sie das Cmdlet [Test-HcsmConnection][2], um den Fehler zu suchen. [Wenden Sie sich an den Microsoft Support](storsimple-contact-microsoft-support.md), wenn Sie das Problem nicht beheben können. |
| 4 | Invoke-HcsSetupWizard: Der Wert liegt nicht innerhalb des erwarteten Bereichs. | Dieser Fehler wird durch eine falsche Subnetzmaske erzeugt. Mögliche Ursachen sind: <ul><li> Subnetzmaske fehlt oder ist leer.</li><li>Das IPv6-Präfix-Format ist ungültig.</li><li>Die Schnittstelle bietet Cloudunterstützung, aber das Gateway ist nicht vorhanden oder ungültig.</li></ul>Beachten Sie, dass die DATA 0-Schnittstelle automatisch über Cloudfunktionalität verfügt, wenn sie über den Setup-Assistenten konfiguriert wird. | Um das Problem zu bestimmen, verwenden Sie die Subnetzmaske 0.0.0.0 oder 256.256.256.256 und überprüfen die Ausgabe. Geben Sie ggf. die korrekten Werte für Subnetzmaske, Gateway und IPv6-Präfix ein. |
 
## Fehlercodes

Die Fehler sind in numerischer Reihenfolge aufgeführt.

|Fehlernummer|Fehlertext oder Beschreibung|Empfohlene Benutzeraktion|
|:---|:---|:---|
|10502|Fehler beim Zugriff auf Ihr Speicherkonto.|Warten Sie einige Minuten, und versuchen Sie es dann erneut. Wenn das Problem weiterhin auftritt, erkundigen Sie sich beim Support von Microsoft nach den weiteren Schritten.|
|40017|Der Datenträger in einem Sicherungssatz konnte nicht aufgelöst werden.|Wenn das Problem weiterhin auftritt, erkundigen Sie sich beim Support von Microsoft nach den weiteren Schritten.|
|40018|Die Datenträger in einem Sicherungssatz konnten nicht aufgelöst werden.|Wenn das Problem weiterhin auftritt, erkundigen Sie sich beim Support von Microsoft nach den weiteren Schritten.|
|390061|Das System ist ausgelastet oder nicht verfügbar.|Warten Sie einige Minuten, und versuchen Sie es dann erneut. Wenn das Problem weiterhin auftritt, erkundigen Sie sich beim Support von Microsoft nach den weiteren Schritten.|
|390143|Ein Fehler mit dem Fehlercode 390143 ist aufgetreten. (Unbekannter Fehler)|Wenn das Problem weiterhin auftritt, erkundigen Sie sich beim Support von Microsoft nach den weiteren Schritten.|

## Nächste Schritte

Wenn das Problem nicht behoben werden kann, [wenden Sie sich an den Support von Microsoft](storsimple-contact-microsoft-support.md), um Unterstützung zu erhalten.


[1]: https://technet.microsoft.com/de-DE/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/de-DE/%5Clibrary/Dn715782(v=WPS.630).aspx

<!---HONumber=Oct15_HO3-->