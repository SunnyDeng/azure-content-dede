<properties linkid="web-sites-traffic-manager" urlDisplayName="Controlling Windows Azure Websites Traffic with Azure Traffic Manager" pageTitle="Controlling Azure Websites Traffic with Azure Traffic Manager" metaKeywords="Azure Web Sites, Traffic Manager, request routing, round robin, failover, performance" description="This article provides summary information for  Azure Traffic Manager as it relates to Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Controlling Azure Websites Traffic with Azure Traffic Manager" authors="cephalin"  solutions="" writer="cephalin" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Steuern des Azure-Websites-Datenverkehrs mit Azure Traffic Manager

> [WACOM.NOTE] Dieser Artikel bietet zusammenfassende Informationen zu Microsoft Azure Traffic Manager im Hinblick auf Azure-Websites. Weitere Informationen über Azure Traffic Manager selbst finden Sie unter den Links am Ende dieses Artikels.

## Einführung

Mit Azure Traffic Manager können Sie steuern, wie Anforderungen von Webclients auf Azure-Websites verteilt werden. Wenn einem Azure Traffic Manager-Profil Azure-Website-Endpunkte hinzugefügt werden, verfolgt Azure Traffic Manager den Status Ihrer Websites (aktiv, angehalten oder gelöscht), sodass der gewünschte Endpunkt als Empfänger des Datenverkehrs gewählt werden kann.

## Lastenausgleichsmethoden

Azure Traffic Manager verwendet drei verschiedene Lastenausgleichsmethoden. Diese werden in der folgenden Liste beschrieben, da Sie Azure-Websites betreffen.

-   **Failover**: Wenn Sie in verschiedenen Regionen über Websiteklone verfügen, können Sie mit dieser Methode eine Website konfigurieren, um den gesamten Webclient-Datenverkehr zu bedienen. Dann können Sie eine andere Website in einer anderen Region konfigurieren, um den Datenverkehr zu verarbeiten, falls die erste Website nicht verfügbar sein sollte.

-   **Roundrobin**: Wenn Sie über Websiteklone in verschiedenen Regionen verfügen, können Sie mit dieser Methode den Datenverkehr gleichmäßig auf die Websites in verschiedenen Regionen verteilen.

-   **Leistung**: Mithilfe der Leistungsmethode wird der Verkehr anhand der kürzesten Roundtripzeit an Clients verteilt. Die Leistungsmethode kann für Websites innerhalb derselben Region oder in verschiedenen Regionen verwendet werden.

Detaillierte Informationen zum Lastenausgleich in Azure Traffic Manager finden Sie unter [Traffic Manager-Lastenausgleichsmethoden][Traffic Manager-Lastenausgleichsmethoden].

## Azure-Websites und Traffic Manager-Profile

Für die Konfiguration zur Steuerung des Website-Datenverkehrs erstellen Sie ein Profil in Azure Traffic Manager, das eine der drei zuvor beschriebenen Lastenausgleichsmethoden verwendet. Fügen Sie dann dem Profil die Endpunkte (in diesem Fall Websites) hinzu, für die Sie den Datenverkehr steuern möchten. Der Websitestatus (aktiv, angehalten oder gelöscht) wird regelmäßig an das Profil übermittelt, sodass Azure Traffic Manager den Datenverkehr entsprechend leiten kann.

Beachten Sie die folgenden Aspekte, wenn Sie Azure Traffic Manager mit Azure verwenden:

-   Bei reinen Websitebereitstellungen innerhalb derselben Region bietet Azure-Websites eine Failover- und Roundrobin-Funktion unabhängig vom Websitemodus.

-   Bei Bereitstellungen in derselben Region, die Azure-Websites zusammen mit anderen Azure-Cloud-Diensten verwenden, können Sie beide Endpunkttypen kombinieren, um Hybridszenarios zu ermöglichen.

-   Sie können in einem Profil nur einen Website-Endpunkt pro Region angeben. Wenn Sie eine Website als Endpunkt für eine Region auswählen, stehen die verbleibenden Websites in dieser Region nicht mehr für dieses Profil zur Auswahl zur Verfügung.

-   Die Website-Endpunkte, die Sie in einem Azure Traffic Manager-Profil angeben, werden auf der Seite "Konfigurieren" im Abschnitt **Domänennamen** für die Websites im Profil angezeigt, werden dort jedoch nicht konfiguriert.

-   Nachdem Sie einem Profil eine Website hinzugefügt haben, wird in der **Site-URL** im Dashboard der Portalseite der Website die benutzerdefinierte Domänen-URL der Website angezeigt, wenn Sie eine eingerichtet haben. Anderenfalls wird die URL des Traffic Manager-Profils angezeigt (z. B. `contoso.trafficmgr.com`). Sowohl der direkte Domänenname der Website als auch die Traffic Manager-URL werden auf der Seite "Konfigurieren" der Website im Abschnitt **Domänennamen** angezeigt.

-   Ihre benutzerdefinierten Domänennamen funktionieren wie erwartet. Nach dem Hinzufügen der Namen zu Ihren Websites müssen Sie allerdings die DNS-Zuordnung noch so konfigurieren, dass auf die Traffic Manager-URL verwiesen wird. Informationen zum Einrichten einer benutzerdefinierten Domäne für eine Azure-Website finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website][Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website].

-   Sie können einem Azure Traffic Manager-Profil nur Websites hinzufügen, die sich im Modus "Standard" befinden.

## Nächste Schritte

Einen Überblick über die Konzepte und technischen Aspekte von Azure Traffic Manager finden Sie unter [Traffic Manager-Übersicht][Traffic Manager-Übersicht].

Informationen zum Konfigurieren von Azure Traffic Manager, darunter auch für den Einsatz auf Azure-Websites, finden Sie unter [Traffic Manager-Konfigurationsaufgaben][Traffic Manager-Konfigurationsaufgaben].

Detaillierte Informationen zum Lastenausgleich in Azure Traffic Manager finden Sie unter [Traffic Manager-Lastenausgleichsmethoden][Traffic Manager-Lastenausgleichsmethoden].

Weitere Informationen zur Verwendung von Traffic Manager mit Azure-Websites finden Sie in den Blogbeiträgen
[Using Windows Azure Traffic Manager with WAWS][Using Windows Azure Traffic Manager with WAWS] (Verwenden von Windows Azure Traffic Manager mit WAWS, in englischer Sprache) und [Azure Traffic Manager can now integrate with Azure Web sites][Azure Traffic Manager can now integrate with Azure Web sites] (Azure Traffic Manager kann jetzt in Azure-Websites integriert werden, in englischer Sprache).

  [Traffic Manager-Lastenausgleichsmethoden]: http://msdn.microsoft.com/de-de/library/windowsazure/dn339010.aspx
  [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website]: https://www.windowsazure.com/de-de/documentation/articles/web-sites-custom-domain-name/
  [Traffic Manager-Übersicht]: http://msdn.microsoft.com/de-de/library/windowsazure/hh744833.aspx
  [Traffic Manager-Konfigurationsaufgaben]: http://msdn.microsoft.com/de-de/library/windowsazure/hh744830.aspx
  [Using Windows Azure Traffic Manager with WAWS]: http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx
  [Azure Traffic Manager can now integrate with Azure Web sites]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
