
<properties 
    pageTitle="Problembehandlung bei Hybrid-Sammlungen - Erstellen"
    description="Erfahren Sie, wie Sie Probleme beim Erstellen von RemoteApp-Hybrid-Sammlungen beheben." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/20/2015" 
    ms.author="vikbucha" />



# Problembehandlung bei Hybrid-Sammlungen - Erstellen

Bevor Sie eine Problemhandlung bei Fehlern mit der Erstellung von Hybrid-Sammlungen durchführen können, sollten Sie verstehen, wie Hybrid-Sammlungen erstellt werden. Für Hybrid-Sammlungen müssen die RemoteApp-Instanzen einer Domäne angehören - dies erfolgt beim Erstellen der Sammlung.  Zu Beginn der Sammlungserstellung werden Kopien der hochgeladenen Vorlagenimages im VNET erstellt und über den Standort-zu-Standort-VPN-Tunnel mit der Domäne verbunden, die über den bei der VNET-Erstellung angegebenen DNS-IP-Eintrag aufgelöst wird.

Folgende Fehler treten im Azure-Verwaltungsportal häufig auf:

	DNS server could not be reached

	Could not join the domain. Unable to reach the domain.

Wenn einer der oben genannten Fehler angezeigt wird, überprüfen Sie Folgendes:

- Vergewissern Sie sich, dass die DNS-IP-Konfigurationen gültig sind.
- Stellen Sie sicher, dass die DNS-IP-Einträge öffentliche IP-Einträge oder ein Teil des bei der VNET-Erstellung angegebenen "lokalen Adressraums" sind.
- Überprüfen Sie den VNET-Tunnel, um sicherzustellen, dass er aktiv bzw. verbunden ist. 
- Stellen Sie sicher, dass die Standortseite der VPN-Verbindung nicht den Netzwerkverkehr blockiert. Dies können Sie in den Protokollen Ihres lokalen VPN-Geräts oder Ihrer VPN-Software in Erfahrung bringen.
- Stellen Sie sicher, dass die beim Erstellen der Sammlung angegebene Domäne fehlerfrei ausgeführt wird.

	ProvisioningTimeout


Wenn dieser Fehler angezeigt wird, überprüfen Sie Folgendes:

- Stellen Sie sicher, dass die Standortseite der VPN-Verbindung nicht den Netzwerkverkehr blockiert. Dies können Sie in den Protokollen Ihres lokalen VPN-Geräts oder Ihrer VPN-Software in Erfahrung bringen.
- Stellen Sie sicher, dass das hochgeladene RemoteApp-Vorlagenpaket ordnungsgemäß mit Sysprep verarbeitet wurde. Sie können die Anforderungen an RemoteApp-Images hier überprüfen.: http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/ 
- Versuchen Sie, mit dem hochgeladenen Vorlagenimage eine VM zu erstellen, und stellen Sie sicher, dass sie gestartet werden kann und entweder (a) auf einem lokalen Hyper-V-Server oder (b) durch Erstellen einer Azure IAAS-VM in Ihrem Azure-Abonnement ausgeführt werden kann. Wenn die VM nicht erstellt oder nicht gestartet werden kann, bedeutet das meist, dass das Vorlagenimage nicht richtig vorbereitet wurde. Dieses Problem muss behoben werden.

	DomainJoinFailed_InvalidUserNameOrPassword

	DomainJoinFailed_InvalidParameter

Wenn einer der oben genannten Fehler angezeigt wird, überprüfen Sie Folgendes:

- Vergewissern Sie sich, dass die für die Domäne eingegebenen Anmeldeinformationen gültig sind.
- Vergewissern Sie sich, dass die Anmeldeinformationen zum Verbinden der Domäne richtig sind und dass die entsprechenden Berechtigungen vorliegen.
- Vergewissern Sie sich, dass die Organisationseinheit (OU) ordnungsgemäß formatiert und in Active Directory vorhanden ist.


<!--HONumber=52--> 