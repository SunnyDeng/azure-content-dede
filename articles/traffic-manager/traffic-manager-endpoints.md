<properties
   pageTitle="Verwalten von Endpunkten in Traffic Manager | Microsoft Azure"
   description="Dieser Artikel bietet Informationen zum Hinzufügen, Entfernen, Aktivieren und Deaktivieren von Endpunkten in Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />

# Hinzufügen, Deaktivieren, Aktivieren oder Löschen von Endpunkten

Azure Websites stellt bereits unabhängig vom Websitemodus Failover- und Roundrobin-Funktionen für das Routing von Datenverkehr für Websites in einem Datencenter zur Verfügung. Traffic Manager ermöglicht das Angeben des Failover- und Roundrobin-Routings von Datenverkehr für Websites und Clouddienste in verschiedenen Datencentern. Der erste erforderliche Schritt zum Bereitstellen dieser Funktionalität ist das Hinzufügen die Funktionen des Cloud-Dienst- oder Website-Endpunkts zu Traffic Manager.

>[AZURE.NOTE]Sie können im Verwaltungsportal keine externe Speicherorte oder Traffic Manager-Profile als Endpunkte hinzufügen. Sie müssen die REST-API [Definition erstellen](http://go.microsoft.com/fwlink/p/?LinkId=400772) oder das Windows PowerShell-Cmdlet [Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774) verwenden.

Sie können auch einzelne Endpunkte deaktivieren, die Teil eines Traffic Manager-Profils sind. Endpunkte umfassen Cloud-Dienste und Websites. Bei Deaktivieren eines Endpunkts bleibt er Teil des Profils, doch das Profil verhält sich so, als wäre der Endpunkt nicht vorhanden. Diese Aktion ist äußerst nützlich zum zeitweiligen Entfernen eines Endpunkts, der sich im Wartungsmodus befindet oder erneut bereitgestellt werden soll. Sobald der Endpunkt wieder betriebsbereit ist, kann er aktiviert werden.

>[AZURE.NOTE]Das Deaktivieren eines gehosteten Diensts hat nichts mit dessen Bereitstellungsstatus in Azure zu tun. Ein fehlerfrei funktionierender Endpunkt bleibt in Betrieb und kann Datenverkehr empfangen, selbst wenn er in Traffic Manager deaktiviert ist. Das Deaktivieren eines Endpunkts in einem Profil wirkt sich darüber hinaus nicht auf seinen Status in einem anderen Profil aus.

## So fügen Sie einen Cloud-Dienst oder Website-Endpunkt hinzu


1. Suchen Sie im Verwaltungsportal im Bereich "Traffic Manager" das Traffic Manager-Profil, das die Endpunkteinstellungen enthält, die Sie ändern möchten, und klicken Sie dann auf den Pfeil rechts neben dem Profilnamen. Die Einstellungsseite für das Profil wird geöffnet.
2. Klicken Sie oben auf der Seite auf **Endpunkte**, um die Endpunkte anzuzeigen, die schon in der Konfiguration enthalten sind.
3. Klicken Sie unten auf der Seite auf **Hinzufügen**, um auf die Seite **Dienstendpunkte hinzufügen** zuzugreifen. In der Standardeinstellung werden auf dieser Seite unter **Dienstendpunkte** die Cloud-Dienste aufgelistet.
4. Wählen Sie für Cloud-Dienste die Cloud-Dienste in der Liste aus, die als Endpunkte für dieses Profil aktiviert werden sollen. Durch Löschen des Namen des Cloud-Diensts wird dieser aus der Liste der Endpunkte entfernt.
5. Klicken Sie für Websites auf die Dropdownliste **Diensttyp**, und wählen Sie dann **Website** aus.
6. Wählen Sie die Websites in der Liste aus, um sie als Endpunkte für dieses Profil hinzuzufügen. Durch Löschen des Namen der Website wird diese aus der Liste der Endpunkte entfernt. Beachten Sie, dass Sie pro Azure-Datencenter (auch "Region" genannt) nur eine einzelne Website auswählen können. Wenn Sie eine Website in einem Datencenter auswählen, das mehrere Websites hostet, stehen nach Wahl der ersten Website die anderen im selben Datencenter nicht mehr zur Auswahl. Beachten Sie außerdem, dass nur Standardwebsites aufgelistet werden.
7. Nachdem Sie die Endpunkte für dieses Profil ausgewählt haben, klicken Sie auf das Häkchen in der unteren rechten Ecke, um die Änderungen zu speichern.

>[AZURE.NOTE]Stellen Sie bei Verwendung der Methode *Failover* für das Routing von Datenverkehr nach dem Hinzufügen oder Entfernen eines Endpunkts sicher, dass Sie die Failoverprioritätsliste auf der Seite "Konfiguration" entsprechend der für Ihre Konfiguration gewünschten Failoverreihenfolge anpassen. Weitere Informationen finden Sie unter [Konfigurieren des Routings für Failoverdatenverkehr](traffic-manager-configure-failover-load-balancing.md).

## So deaktivieren Sie einen Endpunkt

1. Suchen Sie im Verwaltungsportal im Bereich "Traffic Manager" das Traffic Manager-Profil, das die Endpunkteinstellungen enthält, die Sie ändern möchten, und klicken Sie dann auf den Pfeil rechts neben dem Profilnamen. Die Einstellungsseite für das Profil wird geöffnet.
2. Klicken Sie oben auf der Seite auf **Endpunkte**, um die Endpunkte anzuzeigen, die in der Konfiguration enthalten sind.
3. Klicken Sie auf den Endpunkt, den Sie deaktivieren möchten, und dann am unteren Rand der Seite auf **Deaktivieren**.
4. Datenverkehr wird basierend auf der DNS-Gültigkeitsdauer, die für den Traffic Manager-Domänennamen konfiguriert ist, nicht mehr zum Endpunkt geleitet. Sie können die Gültigkeitsdauer auf der Seite "Konfiguration" des Traffic Manager-Profils ändern.

## So aktivieren Sie einen Endpunkt

1. Suchen Sie im Verwaltungsportal im Bereich "Traffic Manager" das Traffic Manager-Profil, das die Endpunkteinstellungen enthält, die Sie ändern möchten, und klicken Sie dann auf den Pfeil rechts neben dem Profilnamen. Die Einstellungsseite für das Profil wird geöffnet.
2. Klicken Sie oben auf der Seite auf **Endpunkte**, um die Endpunkte anzuzeigen, die in der Konfiguration enthalten sind.
3. Klicken Sie auf den Endpunkt, den Sie aktivieren möchten, und dann am unteren Rand der Seite auf **Aktivieren**.
4. Datenverkehr wird wieder entsprechend dem Profil zum Dienst geleitet.

## So löschen Sie einen Cloud-Dienst oder Website-Endpunkt


1. Suchen Sie im Verwaltungsportal im Bereich "Traffic Manager" das Traffic Manager-Profil, das die Endpunkteinstellungen enthält, die Sie ändern möchten, und klicken Sie dann auf den Pfeil rechts neben dem Profilnamen. Die Einstellungsseite für das Profil wird geöffnet.
2. Klicken Sie oben auf der Seite auf **Endpunkte**, um die Endpunkte anzuzeigen, die schon in der Konfiguration enthalten sind.
3. Klicken Sie auf der Seite "Endpunkte" auf den Namen des Endpunkts, den Sie aus dem Profil löschen möchten.
4. Klicken Sie unten auf der Seite auf **Löschen**.

>[AZURE.NOTE]Sie können im Verwaltungsportal keine externen Speicherorte oder Traffic Manager-Profile als Endpunkte löschen. Sie müssen Windows PowerShell verwenden. Weitere Informationen finden Sie unter [Remove-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## Siehe auch

[Traffic Manager – Übersicht](traffic-manager-overview.md)

[Traffic Manager-Überwachung](traffic-manager-monitoring.md)

[Deaktivieren, Aktivieren oder Löschen eines Traffic Manager-Profils](disable-enable-or-delete-a-profile.md)

[Deaktivieren oder Aktivieren eines Traffic Manager-Endpunkts](disable-or-enable-an-endpoint.md)

[Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Cloud Services](http://go.microsoft.com/fwlink/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)
 

<!---HONumber=August15_HO8-->