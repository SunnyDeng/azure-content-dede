<properties 
   pageTitle="Konfigurieren der Lastenausgleichsmethode "Failover""
   description="In diesem Artikel finden Sie Informationen zum Konfigurieren der Lastenausgleichsmethode "Failover" in Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Konfigurieren der Lastenausgleichsmethode "Failover""

Organisationen möchten häufig sicherstellen, dass ihre Dienste zuverlässig funktionieren.  Dazu stellen sie Sicherungsprogramme für den Fall bereit, dass der primäre Dienst ausfällt. Failover für Dienste wird häufig implementiert, indem eine identische Sammlung von Diensten bereitgestellt und Datenverkehr an einen primären Dienst gesendet wird. Dabei wird eine konfigurierte Liste mit mindestens einem Sicherungsdienst verwaltet. Sie können diesen Sicherungstyp mit Azure-Cloud-Diensten und -Websites konfigurieren, indem Sie die unten beschriebenen Verfahren verwenden.

Beachten Sie, dass Azure Websites unabhängig vom Websitemodus bereits Failoverlastenausgleichs-Funktionen für Websites in einem Datencenter (auch als "Region" bezeichnet) zur Verfügung stellt. Traffic Manager ermöglicht das Angeben von Failoverlastenausgleich für Websites in verschiedenen Datencentern.

## So konfigurieren Sie die Lastenausgleichsmethode "Failover":

1. Klicken Sie im Verwaltungsportal im linken Bereich auf das Symbol **Traffic Manager**, um den Bereich Traffic Manager zu öffnen. Wenn Sie noch kein Traffic Manager-Profil erstellt haben, finden Sie unter [Verwalten von Traffic Manager-Profilen](traffic-manager-manage-profiles.md) Anweisungen zum Erstellen eines einfachen Traffic Manager-Profils.
2. Suchen Sie im Verwaltungsportal im Traffic Manager-Bereich das Traffic Manager-Profil mit den Einstellungen, die Sie ändern möchten, und klicken Sie dann auf den Pfeil rechts neben dem Profilnamen.  Die Einstellungsseite für das Profil wird geöffnet.
3. Klicken Sie oben auf der Seite für Ihr Profil auf **Endpunkte**, und überprüfen Sie, ob die Cloud-Dienste und Websites (Endpunkte), die Sie in Ihre Konfiguration einschließen möchten, vorhanden sind. Schritte zum Hinzufügen oder Entfernen von Endpunkten finden Sie unter [Verwalten von Endpunkten in Traffic Manager](traffic-manager-endpoints.md)..
4. Klicken Sie auf der Seite Ihres Profils oben auf **Konfigurieren**, um die Konfigurationsseite zu öffnen.
5. Überprüfen Sie in den **Einstellungen der Lastenausgleichsmethode**, ob die Lastenausgleichsmethode **Failover** ist. Klicken Sie andernfalls in der Dropdownliste auf **Failover**.
6. Passen Sie bei **Failoverprioritätsliste** die Failoverreihenfolge für Ihre Endpunkte an. Wenn Sie die Lastenausgleichsmethode **Failover** auswählen, ist die Reihenfolge der ausgewählten Endpunkte von Bedeutung. Der primäre Endpunkt steht an erster Stelle. Verwenden Sie die nach oben und unten weisenden Pfeile, um die Reihenfolge nach Bedarf zu ändern. Informationen zum Festlegen der Failoverpriorität mithilfe von Windows PowerShell finden Sie unter [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Stellen Sie sicher, dass die **Überwachungseinstellungen** ordnungsgemäß konfiguriert sind. Durch die Überwachung wird sichergestellt, dass die Endpunkte, die offline sind, keinen Datenverkehr empfangen.  Um Endpunkte zu überwachen, müssen Sie einen Pfad und einen Dateinamen angeben. Beachten Sie, dass der Schrägstrich"/"ein gültiger Eintrag für den relativen Pfad ist und bedeutet, dass sich die Datei im Stammverzeichnis (Standardwert) befindet. Weitere Informationen zur Überwachung finden Sie unter Informationen zur [Traffic Manager-Überwachung](traffic-manager-monitoring.md)..
8. Klicken Sie nach der Durchführung der Konfigurationsänderungen unten auf der Seite auf **Speichern**.
9. Testen Sie die Änderungen in Ihrer Konfiguration. Weitere Informationen finden Sie unter [Testen der Traffic Manager-Einstellungen](traffic-manager-testing-settings.md).
10. Sobald das Traffic Manager-Profil eingerichtet und funktionsfähig ist, bearbeiten Sie den DNS-Eintrag auf dem autoritativen DNS-Server, damit Ihre Unternehmensdomäne auf den Namen der Traffic Manager-Domäne verweisen kann. Weitere Informationen hierzu finden Sie unter [Verweisen auf eine Traffic Manager-Domäne mit der Internetdomäne eines Unternehmens](traffic-manager-point-internet-domain.md)..

## Siehe auch

[Informationen zu Lastenausgleichsmethoden von Traffic Manager](traffic-manager-load-balancing-methods.md)

[Traffic Manager-Konfigurationsaufgaben](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Traffic Manager - Übersicht](traffic-manmager-overview.md)

[Cloud Services](http://go.microsoft.com/fwlink/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager-Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!--HONumber=49-->