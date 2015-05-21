<properties
   pageTitle="Aktualisieren von StorSimple-Geräten"
   description="Erläutert, wie die Updatefunktion von StorSimple für das Installieren regelmäßiger und Wartungsmodus-Updates und -Hotfixes verwendet wird."
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

#Aktualisieren von StorSimple-Geräten

##Übersicht

Mit den Updatefunktionen von StorSimple können Sie Ihr StorSimple-Gerät ganz einfach auf dem neuesten Stand halten. Je nach Updatetyp können Sie Updates über das Microsoft Azure-Verwaltungsportal oder über die Windows PowerShell-Benutzeroberfläche auf dem Gerät anwenden. In diesem Lernprogramm werden die Updatetypen und deren Installation beschrieben.

Sie können zwei Arten von Geräteupdates anwenden:

- Regelmäßige Updates \(der normale Modus\)
- Wartungsmodus-Updates

Sie können regelmäßige Updates über das Verwaltungsportal oder über Windows PowerShell installieren. Sie müssen jedoch Windows PowerShell verwenden, um Wartungsmodus-Updates zu installieren.

Sämtliche Updatetypen werden im Folgenden einzeln beschrieben.

###Regelmäßige Updates

Regelmäßige Updates können ohne Unterbrechung des normalen Betriebs auf dem Gerät installiert werden, wenn das Gerät im normalen Modus ausgeführt wird. Diese Updates werden über die Microsoft Update-Website auf jedem Gerätecontroller angewendet.

> [AZURE.IMPORTANT]Während des Updatevorgangs kann ein Controllerfailover auftreten. Dies wirkt sich jedoch nicht auf die Systemverfügbarkeit oder den Betrieb aus.

- Ausführliche Informationen zum Installieren von regelmäßigen Updates im Verwaltungsportal finden Sie unter [Installieren regelmäßiger Updates im Verwaltungsportal](#install-regular-updates-via-the-management-portal).

- Sie können normale Updates über Windows PowerShell für StorSimple installieren. Ausführliche Informationen finden Sie unter [Installieren von regelmäßigen Updates über Windows PowerShell für StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

###Wartungsmodus-Updates

Für Wartungsmodus-Updates muss der Betrieb unterbrochen werden. Dazu gehören z. B. Firmwareupgrades für Datenträger oder USM-Firmwareupgrades. Für diese Updates muss das Gerät in den Wartungsmodus versetzt werden. Einzelheiten finden Sie unter [Ändern des Gerätemodus](#change-device-modes). Sie können Wartungsmodus-Updates nicht über das Verwaltungsportal installieren. Sie müssen stattdessen Windows PowerShell für StorSimple verwenden.

Weitere Informationen zum Installieren von Wartungsmodus-Updates finden Sie unter [Installieren von Wartungsmodus-Updates über Windows PowerShell für StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [AZURE.IMPORTANT]Wartungsmodus-Updates müssen separat auf jedem Controller angewendet werden.

##Installieren regelmäßiger Updates im Verwaltungsportal

Sie können Updates über das Verwaltungsportal auf Ihr StorSimple-Gerät anwenden.

[AZURE.INCLUDE [storsimple-install-updates-manually](../includes/storsimple-install-updates-manually.md)]

##Installieren von regelmäßigen Updates über Windows PowerShell für StorSimple

Sie können aber auch Windows PowerShell für StorSimple für das Anwenden regelmäßiger Updates \(normaler Modus\) verwenden.

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../includes/storsimple-install-regular-updates-powershell.md)]

##Installieren von Wartungsmodus-Updates über Windows PowerShell für StorSimple

Sie verwenden Windows PowerShell für StorSimple, um Wartungsmodus-Updates auf Ihr StorSimple-Gerät anzuwenden. Alle E/A-Anforderungen werden in diesem Modus angehalten. Auch werden die Dienste wie der NVRAM- \(Non-Volatile Random Access Memory\) oder der Clusterdienst beendet. Beide Controller werden neu gestartet, wenn Sie diesen Modus starten oder beenden. Wenn Sie diesen Modus beenden, werden alle Dienste fortgesetzt und sollten fehlerfrei funktionieren. \(Dies kann einige Minuten dauern.\)

Wenn Sie Wartungsmodus-Updates anwenden müssen, erhalten Sie über das Verwaltungsportal eine Warnung, dass Updates vorliegen, die installiert werden müssen. Diese Warnung enthält Anweisungen zur Verwendung von Windows PowerShell für StorSimple für die Installation dieser Updates. Nachdem Sie Ihr Gerät aktualisiert haben, versetzen Sie das Gerät auf dieselbe Weise wieder in den normalen Modus. Schrittweise Anweisungen finden Sie unter [So beenden Sie den Wartungsmodus](#to-exit-maintenance-mode).

> [AZURE.IMPORTANT] 
> 
> - Vergewissern Sie sich vor dem Starten des Wartungsmodus, dass beide Gerätecontroller fehlerfrei funktionieren, indem Sie den **Hardwarestatus** auf der Seite **Wartung** im Verwaltungsportal überprüfen. Wenn der Controller nicht fehlerfrei funktioniert, wenden Sie sich für die nächsten Schritte an Microsoft-Support. Für weitere Informationen kontaktieren Sie den Microsoft-Support. 
> - Im Wartungsmodus müssen Sie das Update zunächst auf einem und dann auf dem anderen Controller anwenden.

Verwenden Sie eine Anwendung wie PuTTY, um auf die serielle Konsole zuzugreifen. Die folgende Vorgehensweise erläutert, wie Sie PuTTY für die Verbindung mit der seriellen Konsole verwenden.

[AZURE.INCLUDE [storsimple-use-putty](../includes/storsimple-use-putty.md)]

Nachdem Sie eine Verbindung mit der Konsole hergestellt haben, bestimmen Sie, ob Updates installiert werden müssen. Wechseln Sie dann in den Wartungsmodus, um sie zu installieren.

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../includes/storsimple-enter-maintenance-mode.md)]

Installieren Sie anschließend die Updates.

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../includes/storsimple-install-maintenance-mode-updates.md)]

Beenden Sie zum Schluss den Wartungsmodus.

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../includes/storsimple-exit-maintenance-mode.md)]

##Installieren von Hotfixes über Windows PowerShell für StorSimple

Im Gegensatz zu Updates für Microsoft Azure StorSimple werden die Hotfixes aus einem freigegebenen Ordner installiert. Wie bei Updates gibt es auch zwei Arten von Hotfixes:

- Regelmäßige Hotfixes 
- Wartungsmodus-Hotfixes  

Die folgenden Vorgehensweisen erläutern, wie Sie mit Windows PowerShell für StorSimple regelmäßige und Wartungsmodus-Hotfixes installieren.

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../includes/storsimple-install-maintenance-mode-hotfixes.md)]

##Was geschieht mit Updates, wenn Sie das Gerät auf die Werkseinstellungen zurücksetzen?

Wenn ein Gerät auf die Werkseinstellungen zurückgesetzt wird, gehen alle Updates verloren. Nach dem Registrieren und Konfigurieren des auf Werkseinstellungen zurückgesetzten Geräts müssen Sie die Updates manuell über das Verwaltungsportal und/oder Windows PowerShell für StorSimple installieren. Weitere Informationen zum Zurücksetzen auf Werkseinstellungen finden Sie unter [Zurücksetzen des Geräts auf die Standardwerkseinstellungen](https://msdn.microsoft.com/library/azure/dn772373.aspx).

##Nächste Schritte

Erfahren Sie mehr über [Windows PowerShell für StorSimple](https://https://msdn.microsoft.com/library/azure/dn772425.aspx).

<!--HONumber=52-->
