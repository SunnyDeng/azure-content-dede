<properties 
   pageTitle="Erfahren Sie mehr über die neuesten Azure-Gastbetriebssystemversionen | Microsoft Azure" 
   description="Die neueste Releaseneuigkeiten und SDK-Kompatibilität für das Azure Cloud Services-Gastbetriebssystem." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="yuemlu" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="03/11/2016"
   ms.author="yuemlu"/>

# Azure-Gastbetriebssystemversionen und SDK-Kompatibilitätsmatrix
Bietet Ihnen aktuelle Informationen zu den neuesten Azure-Gastbetriebssystemreleases für Cloud Services. Anhand dieser Informationen können Sie Ihren Upgradepfad planen, bevor ein Gastbetriebssystem wird. Wenn Sie die Rollen so konfigurieren, dass die *automatischen* Gast-BS-Updates, wie unter [Updateeinstellungen für Azure-Gast-BS][] beschrieben, verwendet werden, müssen Sie diese Seite nicht unbedingt lesen.

> [AZURE.IMPORTANT] Diese Seite bezieht sich auf die Cloud Services-Web- und Workerrollen, die zusätzlich zu einem Gastbetriebssystem ausgeführt werden. Sie **gilt nicht** für IaaS Virtual Machines.

<!-- -->

> [AZURE.TIP] Abonnieren Sie den [RSS-Feed zu Gastbetriebssystemupdates][rss], um immer sofort über alle Gastbetriebssystemänderungen benachrichtigt zu werden.

Sind Sie unsicher, was das Gast-BS ist oder wie Gast-BS-Releases funktionieren? Lesen Sie [diesen](#how-it-works) Abschnitt.

## Neuigkeiten

###### **14. März 2016**
Der Rollout des Gast-BS März beginnt am 14. März 2016 und sollte voraussichtlich am 8. April 2016 freigegeben werden.

###### **22. Februar 2016**
Die Bereitstellung des Gastbetriebssystems Februar beginnt am 22. Februar 2016 und soll voraussichtlich am 9. März 2016 freigegeben werden.

###### **18. Januar 2016**
Die Bereitstellung des Gast-BS Januar beginnt am 18. Januar 2016 und soll voraussichtlich am 12. Februar 2016 freigegeben werden.

###### **4. Januar 2016**
Das Gastbetriebssystem November 201511-02 wurde am 4. Januar 2016 für die Bereitstellung veröffentlicht. Diese Version des Betriebssystems wurde nicht als Standardbetriebssystem für automatische Updates festgelegt. Daher nimmt die Bereitstellung des Gastbetriebssystems für die Version November 201511-02 etwas mehr Zeit in Anspruch.

## Releases

## Releases von Familie 4
**Windows Server 2012 R2**

Unterstützt .NET 4.0, 4.5, 4.5.1, 4.5.2 (Hinweis 2)

>[AZURE.NOTE] Änderungen bei Datumsangaben mit einem * bleiben vorbehalten

| Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Abgelaufenes Datum |
| ------------------------------ | --------------- | ------------- | ---- |
| WA-GUEST-OS-4.30\_201603-01 | * 8.4.2016 | Post 4.32 | TBD |
| WA-GUEST-OS-4.29\_201602-01 | 12.3.2016 | Post 4.31 | TBD |
| WA-GUEST-OS-4.28\_201601-01 | 12.2.2016 | Post 4.30 | TBD |
| WA-GUEST-OS-4.27\_201512-01 | 12.1.2016 | 12.4.2016 | TBD |
| ~~WA-GUEST-OS-4.26\_201511-02~~ | 4.1.2016 | 12.3.2016 | TBD |
| ~~WA-GUEST-OS-4.26\_201511-01~~ | 10.12.2015 | 12.3.2016 | TBD |
| ~~WA-GUEST-OS-4.25\_201510-01~~ | 6.3.2015 | 12.2.2016 | TBD |
| ~~WA-GUEST-OS-4.24\_201509-01~~ | 1.10.2015 | 10.1.2016 | TBD |
| ~~WA-GUEST-OS-4.23\_201508-02~~ | 9.9.2015 | 6.12.2015 | TBD |
| ~~WA-GUEST-OS-4.22\_201507-02~~ | Am 7.8.2015 | 1.11.2015 | TBD |
| ~~WA-GUEST-OS-4.21\_201506-01~~ | 9.7.2015 | 9.10.2015 | TBD |
| ~~WA-GUEST-OS-4.20\_201505-02~~ | 12.6.2015 | 7.9.2015 | TBD |
| ~~WA-GUEST-OS-4.19\_201504-01~~ | 17.4.2015 | 9.8.2015 | TBD |

## Releases von Familie 3

**Windows Server 2012**

Unterstützt .NET 4.0, 4.5

>[AZURE.NOTE] Änderungen bei Datumsangaben mit einem * bleiben vorbehalten

| Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Abgelaufenes Datum |
| ------------------------------ | -------------- | ------------- | --- |
| WA-GUEST-OS-3.37\_201603-01 | *8.4.2016 | Post 3.39 | TBD |
| WA-GUEST-OS-3.36\_201602-01 | 12.3.2016 | Post 3.38 | TBD |
| WA-GUEST-OS-3.35\_201601-01 | 12.2.2016 | Post 3.37 | TBD |
| WA-GUEST-OS-3.34\_201512-01 | 12.1.2016 | 12.4.2016 | TBD |
| ~~WA-GUEST-OS-3.33\_201511-02~~ | 4.1.2016 | 12.3.2016 | TBD |
| ~~WA-GUEST-OS-3.33\_201511-01~~ | 10.12.2015 | 12.3.2016 | TBD |
| ~~WA-GUEST-OS-3.32\_201510-01~~ | 6.11.2015 | 12.2.2016 | TBD |
| ~~WA-GUEST-OS-3.31\_201509-01~~ | 1.10.2015 | 10.1.2016 | TBD |
| ~~WA-GUEST-OS-3.30\_201508-02~~ | 9.9.2015 | 6.12.2015 | TBD |
| ~~WA-GUEST-OS-3.29\_201507-02~~ | 7.8.2015 | 1.11.2015 | TBD |
| ~~WA-GUEST-OS-3.28\_201506-01~~ | 9.7.2015 | 9.10.2015 | TBD |
| ~~WA-GUEST-OS-3.27\_201505-02~~ | 12.6.2015 | 7.9.2015 | TBD |
| ~~WA-GUEST-OS-3.26\_201504-01~~ | 17.4.2015 | 9.8.2015 | TBD |


## Releases von Familie 2

**Windows Server 2008 R2 SP1**

Unterstützt .NET 3.5, 4.0

>[AZURE.NOTE] Änderungen bei Datumsangaben mit einem * bleiben vorbehalten

| Konfigurationszeichenfolge | Herausgabedatum | Deaktivierungsdatum | Abgelaufenes Datum |
| ------------------------------ | ------------- | ------------  | --- |
| WA-GUEST-OS-2.49\_201603-01 | *8.4.2016 | Post 2.51 | TBD |
| WA-GUEST-OS-2.48\_201602-01 | 12.3.2016 | Post 2.50 | TBD |
| WA-GUEST-OS-2.47\_201601-01 | 12.2.2016 | Post 2.49 | TBD |
| WA-GUEST-OS-2.46\_201512-01 | 12.1.2016 | 12.4.2016 | TBD |
| ~~WA-GUEST-OS-2.45\_201511-02~~ | 4.1.2016 | 12.3.2016 | TBD |
| ~~WA-GUEST-OS-2.45\_201511-01~~ | 10.12.2015 | 12.3.2016 | TBD |
| ~~WA-GUEST-OS-2.44\_201510-01~~ | 6.11.2015 | 12.2.2016 | TBD |
| ~~WA-GUEST-OS-2.43\_201509-01~~ | 1.10.2015 | 10.1.2016 | TBD |
| ~~WA-GUEST-OS-2.42\_201508-02~~ | 9.9.2015 | 6.12.2015 | TBD |
| ~~WA-GUEST-OS-2.41\_201507-02~~ | 7.8.2015 | 1.11.2015 | TBD |
| ~~WA-GUEST-OS-2.40\_201506-01~~ | 9.7.2015 | 9.10.2015 | TBD |
| ~~WA-GUEST-OS-2.39\_201505-02~~ | 12.6.2015 | 7.9.2015 | TBD |
| ~~WA-GUEST-OS-2.38\_201504-01~~ | 17.4.2015 | 9.8.2015 | TBD |

## MSRC-Patch-Updates
Die Liste der Patches, die in den einzelnen monatlichen Gastbetriebssystemreleases enthalten sind, steht [hier][patches] zur Verfügung.

## SDK-Unterstützung

Obwohl die [Deaktivierungsrichtlinien für Azure SDK][retire policy sdk] angeben, dass nur Versionen über 2.2 unterstützt werden, erlauben Ihnen bestimmte Gast-BS-Familien, frühere Versionen zu verwenden. Sie sollten immer das neueste unterstützte SDK verwenden.

| Gastbetriebssystemfamilie | Kompatible SDK-Versionen |
| --------------- | ----------------------- |
| 4 | Version 2.1+ |
| 3 | Version 1.8+ |
| 2 | Version 1.3+ |
| 1 | Version 1.0+ |

## Informationen zu den Gastbetriebssystemreleases
Es gibt drei Datumsangaben, die für Gast-BS-Releases wichtig sind: **Freigabe**datum **Deaktivierungs**datum, und **Ablauf**datum. Ein Gast-BS wird als verfügbar betrachtet, wenn es im Portal verfügbar ist und als Zielgastbetriebssystem ausgewählt werden kann. Wenn ein Gast-BS das **Deaktivierungs**datum erreicht, wird es aus Azure entfernt. Jedoch funktioniert jeder Clouddienst, der auf dieses Gast-BS abzielt, wie gewohnt.

Das Fenster zwischen dem **Deaktivierungs**datum und dem **Ablauf**datum bietet Ihnen einen Puffer, um einfach von einem Gast-BS auf ein neueres zu wechseln. Wenn Sie *automatisch* als Ihr Gast-BS verwenden, verwenden Sie immer die neueste Version, und müssen sich keine Sorgen um dessen Ablauf machen.

Wenn das **Ablauf**datum überschritten ist, und ein Clouddienst noch immer dieses Gast-BS verwendet, wird er angehalten, gelöscht oder zu einem Upgrade gezwungen. Erfahren Sie [hier][retirepolicy] mehr über die Deaktivierungsrichtlinie.

## Erläuterung zu den Versionen der Gast-BS-Familie
Die Gastbetriebssystemfamilien basieren auf veröffentlichten Versionen von Microsoft Windows Server. Das Gastbetriebssystem ist das zugrunde liegende Betriebssystem, auf dem Azure Cloud Services ausgeführt werden. Jedes Gastbetriebssystem verfügt über eine Familien-, eine Versions- und eine Releasenummer.

- Die **Gast-BS-Familie** entspricht einem Windows Server-Betriebssystemrelease, auf dem ein Gast-BS basiert. Beispielsweise basiert *Familie 3* auf Windows Server 2012.

- Eine **Gast-BS-Version** ist das Familienbetriebssystem-Image zuzüglich relevanter Patches aus dem [Microsoft Security Response Center (MSRC)][msrc], die zum Zeitpunkt der Herstellung der neuen Gast-BS-Version verfügbar sind. Möglicherweise sind nicht alle Patches enthalten.

    Die Zahlen beginnen bei 0 und werden jedes Mal, wenn ein neuer Satz von Updates hinzugefügt wird, um 1 erhöht. Nachfolgende Nullen werden nur angezeigt, wenn sie von Bedeutung sind. Version 2.10 ist beispielsweise eine andere, viel höhere Version als Version 2.1.

- Ein ** Gast-BS-Release** bezeichnet eine Neuveröffentlichung einer Gast-BS-Version. Eine Neuveröffentlichung kommt vor, wenn Microsoft beim Testen Probleme feststellt, die Änderungen erfordern. Das neueste Release ersetzt immer alle vorherigen Releases, unabhängig davon, ob diese veröffentlicht wurden oder nicht. Das klassischen Azure-Portal lässt Benutzer nur das neueste Release für eine bestimmte Version auswählen. Bereitstellungen, die auf einem früheren Release ausgeführt werden, werden normalerweise nicht zwangsweise aktualisiert. Dies ist abhängig vom Schweregrad des Fehlers.

Im folgenden Beispiel steht 2 für die Familie, 12 für die Version, und "rel2" für das Release.

**Gastbetriebssystemrelease** – 2.12 rel2

**Konfigurationszeichenfolge für dieses Release** - WA-GUEST-OS-2.12\_201208-02

In der Konfigurationszeichenfolge für ein Gastbetriebssystem sind dieselben Informationen eingebettet sowie ein Datum, das zeigt, welche MSRC-Patches in diesem Release berücksichtigt wurden. In diesem Beispiel wurden MSRC-Patches für Windows Server 2008 R2 bis einschließlich August 2012 integriert. Es werden nur Patches einbezogen, die ausdrücklich für diese Version von Windows Server gelten. Wenn beispielsweise ein MSRC-Patch für Microsoft Office gilt, wird er nicht berücksichtigt, da dieses Produkt kein Bestandteil des Windows Server-Basisimage ist.

## Updateprozess des Gastbetriebssystems
Diese Seite enthält Informationen zu anstehenden Gastbetriebssystemreleases. Kunden haben uns mitgeteilt, dass sie wissen möchten, wann ein Release stattfindet, da ihre Clouddienstrollen neu gestartet werden, wenn sie auf "Automatisches Update" festgelegt sind. Gastbetriebssystemreleases werden in der Regel mindestens 5 Tage nach dem MSRC-Updaterelease veröffentlicht, das am zweiten Dienstag jedes Monats auftritt. Neue Releases enthalten alle relevanten MSRC-Patches für jede Gastbetriebssystemfamilie.

Für Microsoft Azure werden ständig Updates veröffentlicht. Das Gastbetriebssystem ist nur ein solches Update in der Pipeline. Ein Release kann durch eine Reihe von Faktoren beeinflusst werden, die zu zahlreich sind, um hier aufgeführt zu werden. Darüber hinaus wird Azure auf Hunderttausenden von Computern ausgeführt. Daher ist es unmöglich, ein genaues Datum und eine Uhrzeit anzugeben, zu der Ihre Rollen neu gestartet werden. Wir aktualisieren den [RSS-Feed zu Gastbetriebssystemupdates][rss] mit den neuesten Informationen, wir uns vorliegen. Dabei handelt es sich jedoch um ungefähre Zeitfenster. Uns ist bekannt, dass dies für Kunden problematisch ist, und arbeiten an einem Plan zur Begrenzung oder zeitlichen Planung von Neustarts.

Wenn eine neue Version des Gastbetriebssystems veröffentlicht wird, kann die vollständige Verteilung über Azure einige Zeit in Anspruch nehmen. Während Dienste auf das neue Gastbetriebssystem aktualisiert werden, werden sie den Updatedomänen entsprechend neu gestartet. Dienste, für die automatische Updates festgelegt wurden, erhalten zuerst ein Release. Nach dem Update wird die neue Gastbetriebssystemversion für Ihren Dienst im klassischen Azure-Portal aufgeführt. Erneute Releases können während dieses Zeitraums auftreten. Einige Versionen können über einen längeren Zeitraum bereitgestellt werden, und es finden möglicherweise für viele Wochen nach dem offiziellen Veröffentlichungsdatum keine automatischen Upgradeneustarts statt. Sobald ein Gastbetriebssystem verfügbar ist, können Sie diese Version explizit im Portal oder in der Konfigurationsdatei auswählen.

Viele wertvolle Informationen zu Neustarts und Verweise auf weitere technische Informationen zu Gast- und Hostbetriebssystemupdates finden Sie im MSDN-Blogbeitrag [Role Instance Restarts Due to OS Upgrades][restarts].

Wenn Sie Ihr Gastbetriebssystem manuell aktualisieren, lesen Sie bitte die [Deaktivierungsrichtlinie für Gastbetriebssysteme][retirepolicy].


## Unterstützungs- und Deaktivierungsrichtlinie für Gastbetriebssysteme
Die Unterstützungs- und Deaktivierungsrichtlinie für Gastbetriebssysteme wird [hier][retirepolicy] erläutert.

[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/de-DE/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Updateeinstellungen für Azure-Gast-BS]: cloud-services-how-to-configure.md
[rss]: http://sxp.microsoft.com/feeds/3.0/msdntn/WindowsAzureOSUpdates
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
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
 

<!---HONumber=AcomDC_0316_2016-->
