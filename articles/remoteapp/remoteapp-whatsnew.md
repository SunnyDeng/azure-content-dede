
<properties 
    pageTitle="Neuerungen in Azure RemoteApp"
    description="Erfahren Sie, welche Änderungen und Verbesserungen an Azure RemoteApp vorgenommen wurden." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/30/2015" 
    ms.author="elizapo" />



# Neuigkeiten in RemoteApp

Einer der Vorteile von RemoteApp ist, dass wir ständig an Verbesserungen arbeiten. Hier werden Sie über solche Änderungen informiert.


## Juni 2015

Es gibt so viele Änderungen! Das Team war im Juni sehr aktiv:

- Die [Angebotsseite](https://www.remoteapp.windowsazure.com/) von Azure RemoteApp wurde überarbeitet - sehen sie selbst! 
- Die Software wurde in allen Images aktualisiert, die Ihnen im Rahmen Ihres Abonnements zur Verfügung stehen.
- An Hybridsammlungen wurden Verbesserungen vorgenommen, einschließlich der Unterstützung von erzwungener Tunnelung und der Überprüfung der Größe von IP-Subnetzen vor dem Erstellen der Sammlung.
- Es wurde festgestellt, dass der Platzhalter * für Webcams nicht funktioniert. Stattdessen müssen Sie die Instanz-ID oder GUID angeben. Die Umleitungsinformationen werden entsprechend aktualisiert.
- Sie können dem Image benutzerdefinierte Antivirensoftware hinzufügen, wenn Sie ein Vorlagenimage aus dem Azure-Katalog erstellen.

Weitere Änderungen sind für Juli geplant, sodass es bald ein neues Update gibt.

## Mai 2015

Die Erstellung dieses Themas ist bereits einige Erweiterungen (und Monate) her, sodass diese Liste eigentlich die Änderungen von Anfang März bis Mai enthält. Sehen Sie sich diese neuen Features an:

- Automatisieren Sie alles: Azure RemoteApp verfügt jetzt über [Cmdlets im Azure PowerShell-Modul](remoteapp-tutorial-arawithpowershell.md). 
- [Erstellen Sie ein Azure RemoteApp-Image von einem virtuellen Azure-Computer](remoteapp-image-on-azurevm.md). Dadurch wird das Hochladen Ihres benutzerdefinierten Images beschleunigt.
- Verwenden Sie ein Azure-VNET statt eines RemoteApp-VNET, um die Ressourcen Ihres Unternehmensnetzwerks mit Azure zu verbinden. Die [Anweisungen für Hybridsammlungen](remoteapp-create-hybrid-deployment.md) wurden aktualisiert, um Ihnen die Erstellung eines Azure-VNET zu erläutern (Schritt 1).
- Apropos VNETs, sehen Sie sich die [neuen Anleitungen](remoteapp-vnetsizing.md) im Zusammenhang mit VNET-Größenlimits und -Einschränkungen an.
- Und bezüglich Einschränkungen: Was sind die [Diensteinschränkungen und Standardwerte](remoteapp-servicelimits.md)?

Sie möchten mehr über Azure RemoteApp erfahren? Das RemoteApp-Team war vor einigen Wochen auf der Ignite. Sehen Sie sich das Video von Eric an: [The Fundamentals of Microsoft Azure RemoteApp Management and Administration](http://channel9.msdn.com/Events/Ignite/2015/BRK3868) (in englischer Sprache).

Möchten Sie Azure RemoteApp in der Praxis testen? Sehen Sie sich das Lernprogramm [Mit RemoteApp jede Anwendung auf jedem Gerät ausführen](remoteapp-anyapp.md) an. Es zeigt, wie Access für die Benutzer freigegeben wird, einschließlich der Freigabe von Datenbankdateien. Es gibt auch ein Lernprogramm, das zeigt, wie [Office 365](remoteapp-tutorial-o365anywhere.md) auf jedem Gerät gleich ausgeführt wird.

Vielen Dank, dass Sie uns treu bleiben. Im nächsten Monat gibt es weitere Updates.

<!---HONumber=July15_HO4-->