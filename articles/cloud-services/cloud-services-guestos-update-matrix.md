<properties 
   pageTitle="Erfahren Sie mehr über die neuesten Azure-Gastbetriebssystemversionen | Azure" 
   description="Die neueste Releaseneuigkeiten und SDK-Kompatibilität für das Azure Cloud Services-Gastbetriebssystem." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="Thraka" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="04/17/2015"
   ms.author="adegeo"/>

# Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix
Bietet Ihnen aktuelle Informationen zu den neuesten Azure-Gastbetriebssystemreleases für Cloud Services. Anhand dieser Informationen können Sie Ihren Upgradepfad planen, bevor ein Gastbetriebssystem wird.

> [AZURE.IMPORTANT] Diese Seite bezieht sich auf die Cloud Services-Web- und Workerrollen, die zusätzlich zu einem Gastbetriebssystem ausgeführt werden. Sie gilt nicht für IaaS Virtual Machines. Wenn Sie die Rollen so konfigurieren, dass die automatischen Gastbetriebssystemupdates, wie unter [Updateeinstellungen für Azure-Gastbetriebssysteme][] beschrieben, verwendet werden, müssen Sie diese Seite nicht unbedingt lesen.

<!-- -->
<br />

> [AZURE.TIP] Abonnieren Sie den [RSS-Feed zu Gastbetriebssystemupdates][rss], um immer sofort über alle Gastbetriebssystemänderungen benachrichtigt zu werden. Die in diesem Feed genannten Änderungen werden etwa einmal pro Woche in diese Seite integriert.


## Neuigkeiten

###### **17. April 2015**
Gastbetriebssystemversionen 4.19, 3.26, 2.38 wurden heute veröffentlicht.

Dieses Release enthält [MS15-034](https://technet.microsoft.com/library/security/MS15-034), einen **kritischen** Patch für Windows HTTP-Server.

Gastbetriebssystemversionen 4.17, 3.24, 2.36 werden am 17. Mai 2015 deaktiviert.

###### **6. April 2015**
Gastbetriebssystemversionen 4.18, 3.25, 2.37 wurden am 2. April 2015 veröffentlicht.

Gastbetriebssystemversionen 4.16, 3.23, 2.35 werden am 2. Mai 2015 deaktiviert.


###### **11. März 2015**
Gastbetriebssystemversionen 4.17, 3.24 und 2.36 wurden am 9. März 2015 veröffentlicht.

Für die Gastbetriebssystemversionen 4.15, 3.22 und 2.34 wurde das Deaktivierungsdatum auf den 9. April 2015 festgelegt.


###### **29. Januar 2015**
Für die Gastbetriebssystemversionen 4.14, 4.13, 3.21, 3.20, 2.33, 2.32 (im November veröffentlicht) wurde das Deaktivierungsdatum verschoben. Die unten stehende Tabelle mit Gastbetriebssystemreleases wurde aktualisiert.


###### **13. Januar 2015, aktualisiert am 15. Januar 2015**
Das Gastbetriebssystem vom Dezember wurde am 14. Januar 2015 veröffentlicht.


###### **13. Januar 2015**
Die Bereitstellung des Gastbetriebssystems vom Januar beginnt voraussichtlich am oder nach dem 19. Januar 2015 durch Aktualisieren der Clouddienste, für die automatische Updates aktiviert sind. Das Gastbetriebssystem vom Januar wird zur Bereitstellung irgendwann im Februar veröffentlicht. SSL, Version 3.0, wird standardmäßig deaktiviert, wie im Sicherheitsupdate vom Januar empfohlen. Dieser Artikel wird aktualisiert, wenn weitere Informationen verfügbar sind.

Wie [zuvor angekündigt][ssl3 announcement], wird durch das Januar-Sicherheitsupdate für das PaaS-Gastbetriebssystem die Unterstützung für SSL, Version 3.0, standardmäßig deaktiviert, wie in der [Microsoft-Sicherheitsempfehlung 3009008][] vorgeschlagen wird. Diese Änderung wird zusätzlich zu allen übrigen monatlichen Sicherheitsupdates vorgenommen. PaaS-Kunden, die automatische Updates aktiviert haben, können überprüfen, ob sich die Deaktivierung von SSL, Version 3.0, auf die Anwendungskompatibilität auswirkt, indem sie dieses [Korrekturskript][ssl3-fixit] ausführen. Dieses Skript deaktiviert proaktiv die Unterstützung für SSL, Version 3.0.

###### **16. Dezember 2014. Aktualisiert am 7. Januar 2015**
Die Gastbetriebssystemversion vom Dezember startet voraussichtlich am oder nach dem 9. Januar 2015.


###### **11. November 2014**

Das November-Release (4.14, 3.21 und 2.33) wurde am 11. November eingeführt. Dieses Update wurde früher herausgegeben, da es das MSRC-Update [Microsoft Security Bulletin MS14-066 - Kritisch][MS14-066] enthält. Ihre Web- und Workerrollen, für die automatische Updates aktiviert sind, sollten in den folgenden Tagen einmal neu gestartet werden, um diese Korrektur zu erhalten.

###### **10. November 2014**
Das Deaktivierungsdatum des Oktober-Release (4.13, 3.20 und 2.32) wurde aufgrund von Kundenfeedback aktualisiert. Das Deaktivierungsdatum liegt immer mindestens zwei Monate nach dem Veröffentlichungsdatum.

###### **4. November 2014**
Das Oktober-Release (4.13, 3.20 und 2.32) wurde am 4. November 2014 eingeführt. Sie enthält den MSRC-Patch, der Probleme mit den August- und September-Releases verursacht hat. Um dieses Problem zu umgehen, sind im Oktober-Release .NET 3.5 und 3.5.1 vorinstalliert, aber deaktiviert. Skripts, die eine Installation von .NET 3.5 oder 3.5.1 versuchen, aktivieren dieses effektiv erneut und geben für die .NET-Installation "Erfolgreich" zurück, vermeiden aber auch das Problem der vollständigen Installation, das durch den MSRC-Patch hervorgerufen wurde.



## Informationen zu den Gastbetriebssystemreleases

In diesem Abschnitt werden die derzeit unterstützten Versionen des Gastbetriebssystems aufgelistet. Gastbetriebssystemfamilien und -versionen besitzen ein Veröffentlichungsdatum, ein Deaktivierungsdatum und ein Ablaufdatum. Ab dem Zeitpunkt der Veröffentlichung kann eine Gastbetriebssystemversion manuell im Verwaltungsportal ausgewählt werden. Ein Gastbetriebssystem wird am oder nach dem Zeitpunkt des Deaktivierungsdatums aus dem Verwaltungsportal entfernt. Es befindet sich dann "im Übergang", wird jedoch mit eingeschränkter Funktion zum Aktualisieren einer Bereitstellung unterstützt. Am Ablaufdatum wird eine Version oder Familie planmäßig vollständig aus dem Azure-System entfernt. Clouddienste, die noch unter einer abgelaufenen Version ausgeführt werden, werden beendet, gelöscht oder zwangsweise auf eine neuere Version aktualisiert, wie unter [Richtlinie zur Unterstützung und Deaktivierung von Azure-Gastbetriebssystemen][retirepolicy] im Detail beschrieben wird.

Microsoft unterstützt mindestens zwei aktuelle Versionen jeder unterstützten Gastbetriebssystemfamilie. Das Deaktivierungsdatum einer vorhandenen Gastbetriebssystemversion kann auf ein späteres Datum verschoben werden, um sicherzustellen, dass mindestens zwei Versionen für die Bereitstellung aktiviert bleiben.

> [AZURE.WARNING] Die Deaktivierung der Gastbetriebssystemfamilie 1 begann am 1. Juni 2013 und wird voraussichtlich in Kürze abgeschlossen sein. Erstellen Sie mit dieser Gastbetriebssystemfamilie keine neuen Installationen, und führen Sie für ältere Systeme, die diese Gastbetriebssystemfamilie verwenden, ein Upgrade durch. Weitere Informationen finden Sie in den [Deaktivierungsinformationen zur Azure-Gastbetriebssystemfamilie 1][fam1retire].

Das Gastbetriebssystem umfasst eine Konfiguration, die sich von den Standardeinstellungen von Windows Server unterscheidet. Weitere Informationen finden Sie unter [Unterschiede zwischen dem Azure-Gastbetriebssystem und Windows Server (Standardinstallation)][server and gos].

### Erläuterung zu Gastbetriebssystemfamilie, -version und -release
Die Gastbetriebssystemfamilien basieren auf veröffentlichten Versionen von Microsoft Windows Server. Das Gastbetriebssystem ist das zugrunde liegende Betriebssystem, auf dem Azure Cloud Services ausgeführt werden. Jedes Gastbetriebssystem verfügt über eine Familien-, eine Versions- und eine Releasenummer.

Die **Gastbetriebssystemfamilie** entspricht einem Windows Server-Betriebssystemrelease, auf dem ein Gastbetriebssystem basiert. Beispielsweise basiert Familie 3 auf Windows Server 2012.

Eine **"Gastbetriebssystemversion"** ist das Familienbetriebssystem-Image zuzüglich relevanter Patches aus dem [Microsoft Security Response Center (MSRC)][msrc], die zum Zeitpunkt der Herstellung der neuen Gastbetriebssystemversion verfügbar sind. Möglicherweise sind nicht alle Patches enthalten. Die Zahlen beginnen bei 0 und werden jedes Mal, wenn ein neuer Satz von Updates hinzugefügt wird, um 1 erhöht. Nachfolgende Nullen werden nur angezeigt, wenn sie von Bedeutung sind. Version 2.10 ist beispielsweise eine andere, viel höhere Version als Version 2.1.

Ein **"Gastbetriebssystemrelease"** bezieht sich auf eine erneute Veröffentlichung einer Gastbetriebssystemversion. Eine erneute Veröffentlichung kommt vor, wenn Microsoft beim Testen Probleme feststellt, die Änderungen erfordern. Das neueste Release ersetzt immer alle vorherigen Releases, unabhängig davon, ob diese veröffentlicht wurden oder nicht. Das Verwaltungsportal lässt Benutzer nur das neueste Release für eine bestimmte Version auswählen. Bereitstellungen, die auf einem früheren Release ausgeführt werden, werden normalerweise nicht zwangsweise aktualisiert. Dies ist abhängig vom Schweregrad des Fehlers.

Im folgenden Beispiel steht 2 für die Familie, 12 für die Version, und "rel2" für das Release.

**Gastbetriebssystemrelease** – 2.12 rel2

**Konfigurationszeichenfolge für dieses Release** - WA-GUEST-OS-2.12_201208-02

In der Konfigurationszeichenfolge für ein Gastbetriebssystem sind dieselben Informationen eingebettet sowie ein Datum, das zeigt, welche MSRC-Patches in diesem Release berücksichtigt wurden. In diesem Beispiel wurden MSRC-Patches für Windows Server 2008 R2 bis einschließlich August 2012 integriert. Es werden nur Patches einbezogen, die ausdrücklich für diese Version von Windows Server gelten. Wenn beispielsweise ein MSRC-Patch für Microsoft Office gilt, wird er nicht berücksichtigt, da dieses Produkt kein Bestandteil des Windows Server-Basisimage ist.

## Releases

## Releases von Familie 4
**Windows Server 2012 R2**

Unterstützt .NET 4.0, 4.5, 4.5.1, 4.5.2 (Hinweis 2)

| Gastbetriebssystemversion | Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Ablaufdatum |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 4.19 | WA-GUEST-OS-4.19_201504-01 | 17. April 2015 | Wird bei Veröffentlichung von 4.21 aktualisiert | TBD |
| 4.18 | WA-GUEST-OS-4.18_201503-01 | 2. April 2015 | Wird bei Veröffentlichung von 4.20 aktualisiert | TBD |
| 4.17 | WA-GUEST-OS-4.17_201502-01 | 9. März 2015 | 17. Mai 2015 | TBD |
| 4.16 | WA-GUEST-OS-4.16_201501-01 | 29. Januar 2015 | 2. Mai 2015 | TBD |
| 4.15 | WA-GUEST-OS-4.15_201412-01 | 14. Januar 2015 | 9. April 2015 | TBD |
| 4.14 | WA-GUEST-OS-4.14_201411-01 | 11. November 2014 | 28. Februar 2015 | TBD |
| 4.13 | WA-GUEST-OS-4.13_201410-01 | 3. November 2014 | 14. Februar 2015 | TBD |
| 4.12 (Hinweis 1) | WA-GUEST-OS-4.12_201409-02 | 6. Oktober 2014 | 12. Oktober 2014 | 23. März 2015 |
| 4.11 (Hinweis 1) | WA-GUEST-OS-4.11_201408-02 | 25. August 2014 | 11. September 2014 | 23. März 2015 |
| 4.10 | WA-GUEST-OS-4.10_201407-01 | 18. Juli 2014 | 1. Dezember 2014 | 23. März 2015 |
| 4.9 | WA-GUEST-OS-4.9_201406-01 | 16. Juni 2014 | 10. November 2014 | 23. März 2015 |
| 4.8 | WA-GUEST-OS-4.8_201405-01 | 1. Juni 2014 | 1. August 2014 | 23. März 2015 |

## Releases von Familie 3

**Windows Server 2012**

Unterstützt .NET 4.0, 4.5

| Gastbetriebssystemversion | Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Ablaufdatum |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 3.26 | WA-GUEST-OS-3.26_201504-01 | 17. April 2015 | Wird bei Veröffentlichung von 3.28 aktualisiert | TBD |
| 3.25 | WA-GUEST-OS-3.25_201503-01 | 2. April 2015 | Wird bei Veröffentlichung von 3.27 aktualisiert | TBD |
| 3.24 | WA-GUEST-OS-3.24_201502-01 | 9. März 2015 | 17. Mai 2015 | TBD |
| 3.23 | WA-GUEST-OS-3.23_201501-01 | 29. Januar 2015 | 2. Mai 2015 | TBD |
| 3.22 | WA-GUEST-OS-3.22_201412-01 | 14. Januar 2015 | 9. April 2015 | TBD |
| 3.21 | WA-GUEST-OS-3.21_201411-01 | 11. November 2014 | 28. Februar 2015 | TBD |
| 3.20 | WA-GUEST-OS-3.20_201410-01 | 3. November 2014 | 14. Februar 2015 | TBD |
| 3.19 (Hinweis 1) | WA-GUEST-OS-3.19_201409-02 | 6. Oktober 2014 | 12. Oktober 2014 | 23. März 2015 |
| 3.18 (Hinweis 1) | WA-GUEST-OS-3.18_201408-02 | 25. August 2014 | 11. September 2014 | 23. März 2015 |
| 3.17 | WA-GUEST-OS-3.17_201407-01 | 18. Juli 2014 | 1. Dezember 2014 | 23. März 2015 |
| 3.16 | WA-GUEST-OS-3.16_201406-01 | 16. Juni 2014 | 10. November 2014 | 23. März 2015 |
| 3.15 | WA-GUEST-OS-3.15_201405-01 | 1. Juni 2014 | 1. August 2014 | 23. März 2015 |


## Releases von Familie 2

**Windows Server 2008 R2 SP1**

Unterstützt .NET 3.5, 4.0

| Gastbetriebssystemversion | Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Ablaufdatum |
| ---------------- | -------------------------- | ---------------------- | ------------ | --- |
| 2.38 | WA-GUEST-OS-2.38_201504-01 | 17. April 2015 | Wird bei Veröffentlichung von 2.40 aktualisiert | TBD |
| 2.37 | WA-GUEST-OS-2.37_201503-01 | 2. April 2015 | Wird bei Veröffentlichung von 2.39 aktualisiert | TBD |
| 2.36 | WA-GUEST-OS-2.36_201502-01 | 9. März 2015 | 17. Mai 2015 | TBD |
| 2.35 | WA-GUEST-OS-2.35_201501-01 | 29. Januar 2015 | 2. Mai 2015 | TBD |
| 2.34 | WA-GUEST-OS-2.34_201412-01 | 14. Januar 2015 | 9. April 2015 | TBD |
| 2.33 | WA-GUEST-OS-2.33_201411-01 | 11. November 2014 | 28. Februar 2015 | TBD |
| 2.32 | WA-GUEST-OS-2.32_201410-01 | 3. November 2014 | 14. Februar 2015 | TBD |
| 2.31 (Hinweis 1) | WA-GUEST-OS-2.31_201409-02 | 6. Oktober 2014 | 12. Oktober 2014 | 23. März 2015 |
| 2.30 (Hinweis 1) | WA-GUEST-OS-2.30_201408-02 | 25. August 2014 | 11. September 2014 | 23. März 2015 |
| 2.29 | WA-GUEST-OS-2.29_201407-01 | 18. Juli 2014 | 1. Dezember 2014 | 23. März 2015 |
| 2.28 | WA-GUEST-OS-2.28_201406-01 | 16. Juni 2014 | 10. November 2014 | 23. März 2015 |
| 2.27 | WA-GUEST-OS-2.27_201405-01 | 1. Juni 2014 | 1. August 2014 | 23. März 2015 |




### Releases von Familie 1
**FAMILIE 1** wurde [deaktiviert][fam1retire].

#### Hinweis 1
Releases von August und September 2014 wurden nur teilweise eingeführt aufgrund von Problemen mit einem [MSRC-Patch][MS14-046], die während des Release festgestellt wurden. Durch das Problem ist die manuelle Installation von .NET 3.5.1 in den Familien 3 und 4 nicht möglich. Das Release wurde als Vorsichtsmaßnahme deaktiviert, damit Kunden es nicht manuell auswählen können.

#### Hinweis 2
Ab dem 19. September 2014 wurde .NET 4.5.2 nicht speziell auf dem Azure-Gastbetriebssystem getestet. Das Gastbetriebssystem ist jedoch im Wesentlichen identisch mit Windows Server. Die gleichen Regeln für die Anwendungskompatibilität, die für das Windows Server-Produkt gelten, gelten daher auch für die entsprechenden Gastbetriebssystemfamilien. Wenn Sie eine Ausnahme von dieser Richtlinie feststellen, wenden Sie sich an den [Azure-Support][azuresupport]. Microsoft betreibt einen angemessenen Aufwand zur Lösung des Problems. [Manuelles Installationspaket für .NET 4.5.2][net install pkg].

### Im Gastbetriebssystem enthaltene MSRC-Updates
Die Liste der Patches, die in den einzelnen monatlichen Gastbetriebssystemreleases enthalten sind, steht [hier][patches] zur Verfügung.

## SDK-Unterstützung

Diese Tabelle zeigt, welche Gastbetriebssystemfamilie mit welchen Azure SDK-Versionen kompatibel ist. Weitere Informationen, die über diese Tabelle hinausgehen, finden Sie unter [Unterstützungs- und Deaktivierungsinformationen zum Azure SDK für .NET und APIs][retire policy sdk]. Alle Informationen in dieser Liste haben Vorrang vor den unten aufgeführten Informationen.

> [AZURE.IMPORTANT]Um sicherzustellen, dass Ihr Dienst erwartungsgemäß funktioniert, müssen Sie ihn auf dem Gastbetriebssystemrelease bereitstellen, das kompatibel mit der Version des Azure SDK ist, mit der der Dienst entwickelt wurde. Andernfalls kann der bereitgestellte Dienst Fehler in der Cloud aufweisen, die in der Entwicklungsumgebung nicht ersichtlich waren.

| Gastbetriebssystemfamilie | Unterstützte SDK-Versionen |
| --------------- | ---------------------- |
| 4 | Version 2.1 und höher |
| 3 | Version 1.8 und höher |
| 2 | Version 1.3 und höher |
| 1 | Version 1.0 und höher |


## Updateprozess des Gastbetriebssystems
Diese Seite enthält Informationen zu anstehenden Gastbetriebssystemreleases. Kunden haben uns mitgeteilt, dass sie wissen möchten, wann ein Release stattfindet, da ihre Clouddienstrollen neu gestartet werden, wenn sie auf "Automatisches Update" festgelegt sind. Gastbetriebssystemreleases werden in der Regel mindestens 5 Tage nach dem MSRC-Updaterelease veröffentlicht, das am zweiten Dienstag jedes Monats auftritt. Neue Releases enthalten alle relevanten MSRC-Patches für jede Gastbetriebssystemfamilie.

Für Microsoft Azure werden ständig Updates veröffentlicht. Das Gastbetriebssystem ist nur ein solches Update in der Pipeline. Ein Release kann durch eine Reihe von Faktoren beeinflusst werden, die zu zahlreich sind, um hier aufgeführt zu werden. Darüber hinaus wird Azure auf Hunderttausenden von Computern ausgeführt. Daher ist es unmöglich, ein genaues Datum und eine Uhrzeit anzugeben, zu der Ihre Rollen neu gestartet werden. Wir aktualisieren den [RSS-Feed zu Gastbetriebssystemupdates][rss] mit den neuesten Informationen, wir uns vorliegen. Dabei handelt es sich jedoch um ungefähre Zeitfenster. Uns ist bekannt, dass dies für Kunden problematisch ist, und arbeiten an einem Plan zur Begrenzung oder zeitlichen Planung von Neustarts.

Wenn eine neue Version des Gastbetriebssystems veröffentlicht wird, kann die vollständige Verteilung über Azure einige Zeit in Anspruch nehmen. Während Dienste auf das neue Gastbetriebssystem aktualisiert werden, werden sie den Updatedomänen entsprechend neu gestartet. Dienste, für die automatische Updates festgelegt wurden, erhalten zuerst ein Release. Nach dem Update wird die neue Gastbetriebssystemversion für Ihren Dienst im Azure-Verwaltungsportal aufgeführt. Erneute Releases können während dieses Zeitraums auftreten. Einige Versionen können über einen längeren Zeitraum bereitgestellt werden, und es finden möglicherweise für viele Wochen nach dem offiziellen Veröffentlichungsdatum keine automatischen Upgradeneustarts statt. Sobald ein Gastbetriebssystem verfügbar ist, können Sie diese Version explizit im Portal oder in der Konfigurationsdatei auswählen. Weitere Informationen finden Sie unter [Aktualisieren des Azure-Gastbetriebssystems über das Verwaltungsportal][update guest os portal] und unter [Aktualisieren des Azure-Gastbetriebssystems durch Ändern der Dienstkonfigurationsdatei][update guest os svc].

Viele wertvolle Informationen zu Neustarts und Verweise auf weitere technische Informationen zu Gast- und Hostbetriebssystemupdates finden Sie im MSDN-Blogbeitrag [Role Instance Restarts Due to OS Upgrades][restarts].

Wenn Sie Ihr Gastbetriebssystem manuell aktualisieren, lesen Sie bitte die [Deaktivierungsrichtlinie für Gastbetriebssysteme][retirepolicy].


## Unterstützungs- und Deaktivierungsrichtlinie für Gastbetriebssysteme
Die Unterstützungs- und Deaktivierungsrichtlinie für Gastbetriebssysteme wird [hier][retirepolicy] erläutert.
 
## Nachrichtenarchiv

**20. Oktober 2014 Aktualisiert am 4. November 2014** - Das September-Release (4.12, 3.19, 2.31 und 1.39) wurde aufgrund von[MSRC-Patch MS14-046][MS14-046] teilweise eingeführt. Dieser Patch führte bei Benutzern zu Fehlern, die versucht haben, .NET 3.5 oder 3.5.1 in Familie 3 oder 4 zu installieren. .NET 3.5.x wird in keiner der beiden Familie offiziell unterstützt. Microsoft reagiert jedoch auf diese Verhaltensänderung, da Installationen für bestimmte Kunden darauf basieren und die Änderung nicht angekündigt wurde. Die Datumsangaben für die Deaktivierung vorheriger Gastbetriebssysteme (Juni und Juli) werden entsprechend verzögert, damit mindestens zwei vollständig veröffentlichte Gastbetriebssysteme unterstützt werden und verfügbar sind. Eine Lösung für das .NET-Installationsproblem wurde im Release vom Oktober 2014 veröffentlicht.

Im Oktober-Release ist .NET 3.5 und 3.5.1 vorinstalliert (aber deaktiviert). Zudem enthält es den zuvor aufgeführten MSRC-Patch. Skripts, die eine Installation von .NET 3.5 oder 3.5.1 versuchen, aktivieren dieses effektiv erneut und geben für die .NET-Installation "Erfolgreich" zurück, vermeiden aber auch das Installationsproblem, das durch den MSRC-Patch hervorgerufen wurde.

Aufgrund der partiellen Einführung der letzten beiden Releases können Personen, die automatische Updates festgelegt oder neue Installationen eingeführt haben, alle diese Gastbetriebsreleases ausführen. Die folgende Tabelle enthält die Gastbetriebssystemreleases, welche die Installation von .NET 3.5 oder 3.5.1 in den Familien 3 und 4 zulassen. Derzeit gilt: Wenn ein Release die Installation zulässt, ist der MSRC-Patch MS14-046 NICHT installiert.

| Betriebssystemversion | .NET 3.5 kann installiert werden | Umfasst MSRC-Patch [MS14-046][] |
| --- | --- | --- |
| Alle späteren Gastbetriebssystemversionen | Ja | Ja |
| WA-GUEST-OS-4.13_201410-01 | Ja | Ja |
| WA-GUEST-OS-4.12_201409-02 | Nein | Ja |
| WA-GUEST-OS-4.12_201409-01 | Nein | Ja |
| WA-GUEST-OS-4.11_201408-02 | Ja | Nein |
| WA-GUEST-OS-4.11_201408-01 | Nein | Ja |
| WA-GUEST-OS-4.10_201407-01 | Ja | Nein |

**20. Oktober 2014** – Da das August- und das September-Release nur teilweise eingeführt wurden, sollten Sie Folgendes beachten:

1. Die Verschlüsselungsänderungen, die unter "Unterschiede zwischen dem Azure-Gastbetriebssystem und Windows Server (Standardinstallation)" beschrieben werden, wurden nicht über das gesamte Azure eingeführt. Kunden, die nicht das August- oder September-Release ausführen, erhalten diese Änderungen im Oktober-Release. 

2. Die Gastbetriebssysteme von August und September wurden im Verwaltungsportal deaktiviert. Sie können sie nicht manuell auswählen. Dies geschieht zum Schutz vor Problemen, die auftreten können, wenn Sie diese Gastbetriebssystemversion auswählen.

3. Die Datumsangaben zur Deaktivierung früherer Releases wurden verschoben. So wird eine ständige Verfügbarkeit im Portal sowie die Unterstützung für mindestens zwei freigegebene Gastbetriebssystemversionen in jeder Familie sichergestellt.

## Releasearchiv

### Familie 4

| Gastbetriebssystemversion | Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Ablaufdatum |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 4.7 | WA-GUEST-OS-4.7_201404-01 | 2. Mai 2014 | 2. Juli 2014 | 18. August 2014 |
| 4.6 | WA-GUEST-OS-4.6_201403-01 | 28. März 2014 | 9. Juni 2014 | 18. August 2014 |
| 4.5 | WA-GUEST-OS-4.5_201402-01 | 21. März 2014 | 21. Mai 2014 | 18. August 2014 |
| 4.4 | WA-GUEST-OS-4.4_201401-01 | 8. Februar 2014 | 8. April 2014 | 14. Mai 2014 |
| 4.3 | WA-GUEST-OS-4.3_201312-01 | 6. Januar 2014 | 6. März 2014 | 14. Mai 2014 |
| 4.2 | WA-GUEST-OS-4.2_201311-01 | 12. Dezember 2013 | 12. Februar 2014 | 14. Mai 2014 |
| 4.1 | WA-GUEST-OS-4.1_201310-01 | 29. Oktober 2013 | N/V | 14. Mai 2014 |
| 4.0 rel3 | WA-GUEST-OS-4.0_201309-03 | 9. Oktober 2013. Am 18. Oktober veröffentlicht. | N/V | 14. Mai 2014 |
 


### Familie 3

| Gastbetriebssystemversion | Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Ablaufdatum |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 3.14 | WA-GUEST-OS-3.14_201404-01 | 2. Mai 2014 | 2. Juli 2014 | 18. August 2014 |
| 3.13 | WA-GUEST-OS-3.13_201403-01 | 28. März 2014 | 9. Juni 2014 | 18. August 2014 |
| 3.12 | WA-GUEST-OS-3.12_201402-01 | 21. März 2014 | 21. Mai 2014 | 18. August 2014 |
| 3.11 | WA-GUEST-OS-3.11_201401-01 | 8. Februar 2014 | 8. April 2014 | 14. Mai 2014 |
| 3.10 | WA-GUEST-OS-3.10_201312-01 | 6. Januar 2014 | 6. März 2014 | 14. Mai 2014 |
| 3.9 | WA-GUEST-OS-3. 9_201311-01 | 12. Dezember 2013 | 12. Februar 2014 | 14. Mai 2014 |
| 3.8 | WA-GUEST-OS-3.8_201310-01 | 29. Oktober 2013 | N/V | 14. Mai 2014 |
| 3.7 rel3 | WA-GUEST-OS-3.7_201309-03 | 9. Oktober 2013 | N/V | 14. Mai 2014 |
| 3.7 rel1 | WA-GUEST-OS-3.7_201309-01 | 23. September 2013 | N/V | 14. Mai 2014 |

### Familie 2

| Gastbetriebssystemversion | Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Ablaufdatum |
| ---------------- | -------------------------- | ------------ | ------------ | --- |
| 2.26 | WA-GUEST-OS-2.26_201404-01 | 2. Mai 2014 | 2. Juli 2014 | 18. August 2014 |
| 2.25 | WA-GUEST-OS-2.25_201403-01 | 28. März 2014 | 9. Juni 2014 | 18. August 2014 |
| 2.24 | WA-GUEST-OS-2.24_201402-01 | 21. März 2014 | 21. Mai 2014 | 18. August 2014 |
| 2.23 | WA-GUEST-OS-2.23_201401-01 | 8. Februar 2014 | 8. April 2014 | 14. Mai 2014 |
| 2.22 | WA-GUEST-OS-2.22_201312-01 | 6. Januar 2014 | 6. März 2014 | 14. Mai 2014 |
| 2.21 | WA-GUEST-OS-2.21_201311-01 | 12. Dezember 2013 | 12. Februar 2014 | 14. Mai 2014 |
| 2.20 | WA-GUEST-OS-2.20_201310-01 | 29. Oktober 2013 | N/V | 14. Mai 2014 |
| 2.19 rel3 | WA-GUEST-OS-2.19_201309-03 | 9. Oktober 2013 | N/V | 14. Mai 2014 |
| 2.19 rel1 | WA-GUEST-OS-2.19_201309-01 | 23. September 2013 | N/V | 14. Mai 2014 |
[Updateeinstellungen für Azure-Gastbetriebssysteme]: https://msdn.microsoft.com/library/azure/ff729420.aspx
[rss]: http://sxp.microsoft.com/feeds/3.0/msdntn/WindowsAzureOSUpdates
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft-Sicherheitsempfehlung 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md

<!--HONumber=54--> 