<properties 
   pageTitle="Problembehandlung bei einem betriebsbereiten StorSimple-Gerät"
   description="Beschreibt die Diagnose und Behebung von Fehlern, die auf einem betriebsbereiten StorSimple-Gerät auftreten."
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
   ms.date="04/09/2015"
   ms.author="v-sharos" />

# Problembehandlung bei einem betriebsbereiten StorSimple-Gerät

## Übersicht

Dieser Artikel enthält Anleitungen zum Beheben von Konfigurationsproblemen, die bei einem bereitgestellten und betriebsbereiten StorSimple-Gerät auftreten können. Es werden allgemeine Probleme, mögliche Ursachen und empfohlene Schritte zum Beheben von Problemen bei der StorSimple-Konfiguration beschrieben. Diese Informationen gelten sowohl für lokale physische als auch für virtuelle StorSimple-Geräte.

## Verfahren des Setup-Assistenten für betriebsbereite Geräte

Mithilfe des Setup-Assistenten (Invoke-HcsSetupWizard) überprüfen Sie die Gerätekonfiguration und nehmen bei Bedarf Korrekturmaßnahmen vor.

Die Verfahren im Setup-Assistenten unterscheiden sich bei einem bereits konfigurierten und betriebsbereiten Gerät. Sie können nur die folgenden Einträge ändern:

- IP-Adresse, Subnetzmaske und Gateway
- Primärer DNS-Server
- Primärer NTP-Server
- Optionale Webproxy-Konfiguration

Der Setup-Assistent führt keine Vorgänge im Zusammenhang mit der Kennworterfassung und Geräteregistrierung durch.

## Fehler, die bei nachfolgenden Ausführungen des Setup-Assistenten auftreten

In der folgenden Tabelle sind die Fehler, die beim Ausführen des Setup-Assistenten auf einem betriebsbereiten Gerät auftreten können, mögliche Ursachen für diese Fehler sowie empfohlene Maßnahmen zur Problembehebung beschrieben.

| Nr. | Fehlermeldung oder -bedingung | Mögliche Ursachen | Empfohlene Maßnahme |
| --- | -------------------------- | --------------- | ------------------ |
| 1 | Fehler 350032: Dieses Gerät wurde bereits deaktiviert. | Dieser Fehler wird angezeigt, wenn Sie den Setup-Assistenten auf einem deaktivierten Gerät ausführen. | [Wenden Sie sich an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx) für weitere Schritte. Ein deaktiviertes Gerät kann nicht in Betrieb genommen werden. Sie müssen das Gerät möglicherweise auf die Werkseinstellungen zurücksetzen, bevor es erneut aktiviert werden kann. |
| 2 | Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION(Ausnahme von HRESULT: 0x80070001) | Das Update des DNS-Servers ist fehlgeschlagen. DNS-Einstellungen sind globale Einstellungen und gelten für alle aktivierten Netzwerkschnittstellen. | Aktivieren Sie die Schnittstelle, und wenden Sie die DNS-Einstellungen erneut an. Dies kann das Netzwerk für andere aktivierten Schnittstellen beeinträchtigen, da es sich um globale Einstellungen handelt. |
| 3 | Das Gerät wird im StorSimple-Manager-Dienstportal als online aufgeführt. Wenn Sie jedoch versuchen, die Mindestinstallation abzuschließen und die Konfiguration speichern, schlägt der Vorgang fehl. | Der Webproxy wurde während der ersten Installation nicht konfiguriert, obwohl ein Proxyserver tatsächlich vorhanden war. | Verwenden Sie das Cmdlet [Test-HcsmConnection](https://msdn.microsoft.com/library/azure/eedae62d-0957-4005-b346-9248724f90e0#sec05), um den Fehler zu suchen. [Wenden Sie sich an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx), wenn Sie das Problem nicht beheben können. |
| 4 | Invoke-HcsSetupWizard: Der Wert liegt nicht innerhalb des erwarteten Bereichs. | Dieser Fehler wird durch eine falsche Subnetzmaske erzeugt. Mögliche Ursachen sind: <ul><li> Subnetzmaske fehlt oder ist leer.</li><li>Das IPv6-Präfix-Format ist ungültig.</li><li>Die Schnittstelle bietet Cloud-Unterstützung, aber das Gateway ist nicht vorhanden oder ungültig.</li></ul>Beachten Sie, dass die DATA 0-Schnittstelle automatisch über Cloud-Funktionalität verfügt, wenn sie über den Setup-Assistenten konfiguriert wird. | Um das Problem zu bestimmen, verwenden Sie die Subnetzmaske 0.0.0.0 oder 256.256.256.256 und überprüfen die Ausgabe. Geben Sie ggf. die korrekten Werte für Subnetzmaske, Gateway und IPv6-Präfix ein. |
 
## Nächste Schritte
Wenn das Problem durch die vorhergehenden Schritte nicht behoben wird, [wenden Sie sich für Hilfe an den Microsoft Support](https://msdn.microsoft.com/library/azure/dn757750.aspx).

<!---HONumber=July15_HO2-->