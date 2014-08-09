<properties linkid="web-sites-traffic-manager" urlDisplayName="Controlling Windows Azure Web Sites Traffic with Azure Traffic Manager" pageTitle="Controlling Azure Web Sites Traffic with Azure Traffic Manager" metaKeywords="Azure Web Sites, Traffic Manager, request routing, round robin, failover, performance" description="This article provides summary information for  Azure Traffic Manager as it relates to Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="Controlling Azure Web Sites Traffic with Azure Traffic Manager" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Steuern des Azure-Websites-Verkehrs mit Azure Traffic Manager
=============================================================

> [WACOM.NOTE] Dieser Artikel bietet zusammenfassende Informationen zu Microsoft Azure Traffic Manager im Hinblick auf Azure-Websites. Weitere Informationen über Azure Traffic Manager selbst finden Sie unter den Links am Ende dieses Artikels.

Einführung
----------

Mit Azure Traffic Manager können Sie steuern, wie Anfragen von Webclients auf Azure-Websites verteilt werden. Wenn einem Azure Traffic Manager-Profil Azure-Website-Endpunkte hinzugefügt werden, verfolgt Azure Traffic Manager den Status Ihrer Websites (aktiv, angehalten oder gelöscht), sodass der gewünschte Endpunkt als Empfänger des Verkehrs gewählt werden kann.

Lastenausgleichsmethoden
------------------------

Azure Traffic Manager verwendet drei verschiedene Lastenausgleichsmethoden. Diese werden in der folgenden Liste beschrieben, da Sie Azure-Websites betreffen.

-   **Failover**: Wenn Sie in verschiedenen Regionen über Website-Klone verfügen, können Sie mit dieser Methode eine Website konfigurieren, um den gesamten Webclient-Verkehr zu bedienen. Dann können Sie eine andere Website in einer anderen Region konfigurieren, um den Verkehr zu verarbeiten, falls die erste Website nicht verfügbar sein sollte.

-   **Roundrobin**: Wenn Sie über Website-Klone in verschiedenen Regionen verfügen, können Sie mit dieser Methode den Verkehr gleichmäßig auf die Websites in anderen Regionen verteilen.

-   **Leistung**: Mithilfe der Leistungsmethode wird der Verkehr anhand der kürzesten Roundtripzeit an Clients verteilt. Die Leistungsmethode kann für Websites innerhalb derselben Region oder in verschiedenen Regionen verwendet werden.

Detaillierte Informationen zum Lastenausgleich in Azure Traffic Manager finden Sie unter [Traffic Manager-Lastenausgleichsmethoden](http://msdn.microsoft.com/de-de/library/windowsazure/dn339010.aspx).

Azure-Websites und Traffic Manager-Profile
------------------------------------------

Für die Konfiguration zur Steuerung des Website-Verkehrs erstellen Sie ein Profil in Azure Traffic Manager, das eine der drei zuvor beschriebenen Lastenausgleichsmethoden verwendet. Fügen Sie dann dem Profil die Endpunkte (in diesem Fall Websites) hinzu, für die Sie den Verkehr steuern möchten. Der Website-Status (aktiv, angehalten oder gelöscht) wird regelmäßig an das Profil übermittelt, sodass Azure Traffic Manager den Verkehr entsprechend leiten kann.

Beachten Sie die folgenden Aspekte, wenn Sie Azure Traffic Manager mit Azure verwenden:

-   Bei reinen Website-Bereitstellungen innerhalb derselben Region bietet Azure-Websites eine Failover- und Roundrobin-Funktion, unabhängig vom Website-Modus.

-   Bei Bereitstellungen in derselben Region, die Azure-Websites zusammen mit anderen Azure-Cloud-Diensten verwenden, können Sie beide Endpunkttypen kombinieren, um Hybridszenarios zu aktivieren.

-   Sie können in einem Profil nur einen Website-Endpunkt pro Region angeben. Wenn Sie eine Website als Endpunkt für eine Region auswählen, stehen die verbleibenden Websites in dieser Region nicht mehr für dieses Profil zur Auswahl zur Verfügung.

-   Die Website-Endpunkte, die Sie in einem Azure Traffic Manager-Profil festlegen, werden auf der Konfigurationsseite im Abschnitt **Domänennamen** für die Websites im Profil angezeigt, werden dort jedoch nicht konfiguriert.

-   Nachdem Sie einem Profil eine Website hinzugefügt haben, wird in der **Site-URL** im Dashboard der Portalseite der Website die benutzerdefinierte Domänen-URL der Website angezeigt, wenn Sie eine eingerichtet haben. Anderenfalls wird die URL des Traffic Manager-Profils angezeigt (z. B. `contoso.trafficmgr.com`). Sowohl der direkte Domänenname der Website als auch die Traffic Manager-URL werden auf der Konfigurationsseite der Website im Abschnitt **Domänennamen** angezeigt.

-   Ihre benutzerdefinierten Domänennamen funktionieren wie erwartet. Sie fügen sie Ihren Websites hinzu, müssen jedoch auch die DNS-Zuordnung konfigurieren, um auf die Traffic Manager-URL zu verweisen. Informationen zum Einrichten einer benutzerdefinierten Domäne für eine Azure-Website finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website](https://www.windowsazure.com/de-de/documentation/articles/web-sites-custom-domain-name/).

-   Sie können einem Azure Traffic Manager-Profil nur Websites hinzufügen, die sich im Standardmodus befinden.

Nächste Schritte
----------------

Einen Überblick über die Konzepte und technischen Aspekte von Azure Traffic Manager finden Sie unter [Traffic Manager-Übersicht](http://msdn.microsoft.com/de-de/library/windowsazure/hh744833.aspx).

Informationen über das Konfigurieren von Azure Traffic Manager, darunter auch für den Einsatz von Azure-Websites, finden Sie unter [Traffic Manager-Konfigurationsaufgaben](http://msdn.microsoft.com/de-de/library/windowsazure/hh744830.aspx).

Detaillierte Informationen zum Lastenausgleich in Azure Traffic Manager finden Sie unter [Traffic Manager-Lastenausgleichsmethoden](http://msdn.microsoft.com/de-de/library/windowsazure/dn339010.aspx).

