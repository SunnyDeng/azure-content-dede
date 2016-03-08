<properties
    pageTitle="Verwenden von Outlook in Azure RemoteApp | Microsoft Azure" 
    description="Informationen zum Konfigurieren und Verwenden von Outlook in Azure RemoteApp | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="02/25/2016"
    ms.author="elizapo" />

# Verwenden Microsoft Outlook in Azure RemoteApp

Azure RemoteApp unterstützt Microsoft Outlook O365. Erfahren Sie mehr über das Verwenden von [Office in Azure RemoteApp](remoteapp-officesubscription.md). Für die Verwendung von Outlook in Azure RemoteApp werden einige Einstellungen empfohlen.

## Cache-Modus
Der Cache-Modus ist eine empfohlene Konfiguration für die Verwendung von Outlook in Azure RemoteApp. Wenn Sie für ein Outlook 2013-Konto den Exchange-Cache-Modus konfigurieren, arbeitet Outlook 2013 mit einer lokalen Kopie des Microsoft Exchange-Postfachs des Benutzers, die mit dem Offline Offlineadressbuch (OAB) in einer Offline-Datendatei (OST-Datei) auf dem Computer des Benutzers gespeichert ist. Das zwischengespeicherte Postfach und OAB werden in regelmäßigen Abständen vom O365-Dienst aktualisiert. Erfahren Sie mehr über [die Unterschiede zwischen Cache- und Onlinemodus](https://technet.microsoft.com/library/jj683103.aspx).

Der Benutzer kann bei der Kontoeinrichtung zwischen **Exchange-Cachemodus** oder **im Onlinemodus** wählen oder die Kontoeinstellungen ändern. Sie können auch mit dem Office-Anpassungstool (OAT) oder der Gruppenrichtlinie einen der Modi bereitstellen.

Informationen dazu finden Sie unter [Schrittweise Anweisungen zum Aktivieren des Cache-Modus](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## Suchen
In Azure RemoteApp ist die Suche in Outlook eingeschränkt. Azure RemoteApp verwendet VM-Pools, um Benutzersitzungen zu ermöglichen. Die Suchindizierung hängt von der VM-ID ab, die sich je nach virtueller Maschine unterscheidet. So ist es möglich, dass ein Benutzer bei jeder Anmeldung bei der Azure RemoteApp an eine neue virtuelle Maschine weitergeleitet wird. Bei Aktivierung der lokalen Suche würde bei jeder Änderung der VM-ID (wenn der Benutzer an eine andere virtuelle Maschine geleitet wird) der Indexer ausgeführt werden. Je nach Größe der OST-Datei könnte die Ausführung des Indexers sehr lange dauern und die erforderlichen Ressourcen für andere Apps belegen. Die Suche wäre nicht nur langsam, sondern ergäbe möglicherweise keine Ergebnisse. Dieses Problem kann durch standardmäßige Verwendung der Onlinesuche umgangen werden. Leider kann die indizierte/lokale Suche nicht deaktiviert werden, und die Online-Suche kann in Outlook 2013 nicht standardmäßig aktiviert werden.

Outlook 2016 bietet hierfür eine Lösung durch die Bereitstellung einer neuen Online-Suchfunktion für Postfächer, die auf Exchange 2016 gehostet (oder in Office 365 gehostet) werden. Hierbei werden die Suchergebnisse des Servers für den lokalen Cache (OST) verwendet. Outlook greift in gewissen Fällen möglicherweise auf den Search-Indexer zurück, aber die meisten Suchvorgänge erfolgen im Onlinemodus. Wenn die Mail-Suche ein wichtiges Szenario darstellt, wird von Azure Remote-App die Verwendung von Outlook 2016 empfohlen.

<!---HONumber=AcomDC_0302_2016-->