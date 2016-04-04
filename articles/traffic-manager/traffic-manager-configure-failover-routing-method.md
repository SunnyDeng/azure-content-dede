<properties 
   pageTitle="Konfigurieren der Traffic Manager-Routingmethode für Datenverkehr ";Failover";| Microsoft Azure"
   description="In diesem Artikel finden Sie Informationen zum Konfigurieren der Routingmethode für Datenverkehr ";Failover"; in Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Konfigurieren der Routingmethode "Failover"

Organisationen möchten häufig sicherstellen, dass ihre Dienste zuverlässig funktionieren. Dazu stellen sie Sicherungsprogramme für den Fall bereit, dass der primäre Dienst ausfällt. Failover für Dienste wird häufig implementiert, indem eine identische Sammlung von Diensten bereitgestellt und Datenverkehr an einen primären Dienst gesendet wird. Dabei wird eine konfigurierte Liste mit mindestens einem Sicherungsdienst verwaltet. Sie können diesen Sicherungstyp mit Azure-Cloud-Diensten und -Websites konfigurieren, indem Sie die unten beschriebenen Verfahren verwenden.

Beachten Sie, dass Azure Websites unabhängig vom Websitemodus bereits die Funktionalität der Routingmethode für Datenverkehr "Failover" für Websites in einem Datencenter (auch als "Region" bezeichnet) zur Verfügung stellt. Traffic Manager ermöglicht das Angeben von Routingmethode für Datenverkehr "Failover" für Websites in verschiedenen Datencentern.

## So konfigurieren Sie die Routingmethode für Datenverkehr "Failover"

1. Klicken Sie im klassischen Azure-Portal im linken Bereich auf das Symbol **Traffic Manager**, um den Bereich „Traffic Manager“ zu öffnen. Wenn Sie noch kein Traffic Manager-Profil erstellt haben, finden Sie unter [Verwalten von Traffic Manager-Profilen](traffic-manager-manage-profiles.md) Anweisungen zum Erstellen eines einfachen Traffic Manager-Profils.
2. Suchen Sie im klassischen Azure-Portal im Bereich „Traffic Manager“ das Traffic Manager-Profil mit den Einstellungen, die Sie ändern möchten, und klicken Sie dann auf den Pfeil rechts neben dem Profilnamen. Die Einstellungsseite für das Profil wird geöffnet.
3. Klicken Sie oben auf der Seite für Ihr Profil auf **Endpunkte**, und überprüfen Sie, ob die Cloud-Dienste und Websites (Endpunkte), die Sie in Ihre Konfiguration einschließen möchten, vorhanden sind. Schritte zum Hinzufügen oder Entfernen von Endpunkten finden Sie unter [Verwalten von Endpunkten in Traffic Manager](traffic-manager-endpoints.md).
4. Klicken Sie auf der Seite Ihres Profils oben auf **Konfigurieren**, um die Konfigurationsseite zu öffnen.
5. Überprüfen Sie in den Einstellungen der **Routingmethode für Datenverkehr**, ob die Routingmethode für Datenverkehr **Failover** ist. Klicken Sie andernfalls in der Dropdownliste auf **Failover**.
6. Passen Sie bei **Failoverprioritätsliste** die Failoverreihenfolge für Ihre Endpunkte an. Wenn Sie die Routingmethode für Datenverkehr **Failover** auswählen, ist die Reihenfolge der ausgewählten Endpunkte von Bedeutung. Der primäre Endpunkt steht an erster Stelle. Verwenden Sie die nach oben und unten weisenden Pfeile, um die Reihenfolge nach Bedarf zu ändern. Informationen zum Festlegen der Failoverpriorität mithilfe von Windows PowerShell finden Sie unter [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Stellen Sie sicher, dass die **Überwachungseinstellungen** ordnungsgemäß konfiguriert sind. Durch die Überwachung wird sichergestellt, dass die Endpunkte, die offline sind, keinen Datenverkehr empfangen. Um Endpunkte zu überwachen, müssen Sie einen Pfad und einen Dateinamen angeben. Beachten Sie, dass der Schrägstrich"/"ein gültiger Eintrag für den relativen Pfad ist und bedeutet, dass sich die Datei im Stammverzeichnis (Standardwert) befindet. Weitere Informationen zur Überwachung finden Sie unter Informationen zur [Traffic Manager-Überwachung](traffic-manager-monitoring.md).
8. Klicken Sie nach der Durchführung der Konfigurationsänderungen unten auf der Seite auf **Speichern**.
9. Testen Sie die Änderungen in Ihrer Konfiguration. Weitere Informationen finden Sie unter [Testen der Traffic Manager-Einstellungen](traffic-manager-testing-settings.md).
10. Sobald das Traffic Manager-Profil eingerichtet und funktionsfähig ist, bearbeiten Sie den DNS-Eintrag auf dem autoritativen DNS-Server, damit Ihre Unternehmensdomäne auf den Namen der Traffic Manager-Domäne verweisen kann. Weitere Informationen zur Vorgehensweise finden Sie unter [Verweisen einer Unternehmens-Internetdomäne auf eine Traffic Manager-Domäne](traffic-manager-point-internet-domain.md).

## Nächste Schritte

[Verweisen einer Unternehmens-Internetdomäne auf eine Traffic Manager-Domäne](traffic-manager-point-internet-domain.md)

[Traffic Manager-Routingmethoden](traffic-manager-routing-methods.md)

[Konfigurieren der Routingmethode „Roundrobin“](traffic-manager-configure-round-robin-routing-method.md)

[Konfigurieren der Routingmethode „Performance“](traffic-manager-configure-performance-routing-method.md)

[Problembehandlung beim Status „Heruntergestuft“ in Traffic Manager](traffic-manager-troubleshooting-degraded.md)

[Deaktivieren, Aktivieren oder Löschen eines Traffic Manager-Profils](disable-enable-or-delete-a-profile.md)

[Deaktivieren oder Aktivieren eines Traffic Manager-Endpunkts](disable-or-enable-an-endpoint.md)

 

<!---HONumber=AcomDC_0323_2016-->