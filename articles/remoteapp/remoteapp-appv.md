<properties
    pageTitle="Verwenden von App-V-Apps mit Azure RemoteApp| Microsoft Azure"
    description="Erfahren Sie, wie Sie App-V-Apps unter Azure RemoteApp verwenden."
    services="remoteapp"
	documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/21/2016" 
    ms.author="elizapo" />



# Verwenden von App-V-Apps in Azure RemoteApp

Sie können App-V-Anwendungen in einer Azure RemoteApp-Hybridsammlung verwenden. Hierfür ist ein Domänenbeitritt erforderlich.

Stellen Sie sicher, dass Sie den App-V 5.1-Client mit den neuesten Updates installieren, bevor Sie beginnen. Sie müssen ein [benutzerdefiniertes Image](remoteapp-create-custom-image.md) erstellen, in dem der App-V-Client enthalten ist.

Es ist einfach, Ihre vorhandene App-V-Infrastruktur mit Azure RemoteApp zu verwenden. Da eine Hybridsammlung in einem Azure VNET bereitgestellt wird, für das Zugriff auf Ihren Domänencontroller besteht, und die virtuellen Computer der Domäne der Domäne beigetreten sind, können Sie Ihre vorhandene App-V-Infrastruktur und die Bereitstellungsmethoden nutzen, um die App-V-Anwendung ohne Probleme in Azure RemoteApp zu hosten. Hier sind einige Aspekte aufgeführt, die Sie je nach Typ Ihrer derzeitigen App-V-Bereitstellung berücksichtigen sollten:

| Konfigurationsoptionen | | Positiv | Negativ |
|-----------------------|-----------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Bereitstellungsmethode | Streaming (bedarfsgesteuert) | App ist immer aktuell | Wartezeit bei erster Nutzung |
| | Bereitgestellt | Am schnellsten; App ist auf virtuellem Computer bereits vorhanden | Bloat – Belegung von Imagespeicher (Obergrenze von 127 GB) |
| App-Ortsspeicher | Freigegebener Inhalt | App wird im Arbeitsspeicher der Azure RemoteApp-Instanz ausgeführt | Verbraucht Arbeitsspeicher und benötigt gute Verbindung zum Streaming(datei)server, auf dem sich die App befindet |
| | Datenträger (zwischengespeichert) | Schnelle Ausführung; App nicht abhängig von Verfügbarkeit der Inhaltsquelle | Bloat – Belegung von Imagespeicher (Obergrenze von 127 GB) |
| Ziel | Benutzer | Erfordert vollständige eigenständige App-V-Infrastruktur | |
| | Global (Computer) | Vorabveröffentlichung oder Adressierung per Veröffentlichungsserver | Aktualisierung des Azure-Images erforderlich, wenn die App aktualisiert werden soll (umfangreich). Verbraucht relativ viel Platz im Image. |

 Nachdem Sie das benutzerdefinierte Image und die Hybridsammlung erstellt haben, können Sie Ihre Anwendung veröffentlichen, Benutzer zuweisen und Ihre vorhandenen App-V-Anwendungen nutzen, die unter Azure RemoteApp gehostet und auf allen Geräten an allen Orten bereitgestellt werden.

<!---HONumber=AcomDC_0128_2016-->