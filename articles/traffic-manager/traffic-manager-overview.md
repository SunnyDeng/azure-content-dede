<properties 
   pageTitle="Traffic Manager - Übersicht"
   description="Dieser Artikel hilft Ihnen zu verstehen, was Traffic Manager ist und wie er funktioniert."
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

# Traffic Manager - Übersicht

Mit Microsoft Azure Traffic Manager können Sie die Verteilung des Benutzerdatenverkehrs an Ihre angegebenen Endpunkte steuern, z. B. Azure-Cloud-Dienste, Websites und andere Endpunkte. Die Funktionsweise von Traffic Manager basiert darauf, dass Sie ein intelligentes Richtlinienmodul auf DNS-Abfragen (Domain Name System) von Domänennamen Ihrer Internetressourcen anwenden. Die Cloud-Dienste oder Websites können in verschiedenen Rechenzentren auf der ganzen Welt ausgeführt werden.

Traffic Manager ermöglicht Folgendes:

- **Verbessern der Verfügbarkeit wichtiger Anwendungen**: Mit Traffic Manager können Sie die Verfügbarkeit wichtiger Anwendungen zu verbessern, indem Sie Ihre Endpunkte in Azure überwachen und automatische Failoverfunktionen bereitstellen, wenn ein Azure-Cloud-Dienst, eine Azure-Website oder ein anderer Standort ausfällt.
- **Verbessern der Reaktionsschnelligkeit hochleistungsfähiger Anwendungen**: Azure ermöglicht die Ausführung von Cloud-Diensten oder Websites in Rechenzentren auf der ganzen Welt. Sie können Traffic Manager die Reaktionsfähigkeit von Anwendungen und die Zeit für die Inhaltsübermittlung beschleunigen, indem Sie die Endbenutzer vom Client aus an den Endpunkt mit der geringsten Netzwerklatenz umleiten.
- **Aktualisieren und Warten ohne Ausfallzeiten**: Traffic Manager unterstützt erweiterte Szenarios für die Hybrid Cloud und lokale Bereitstellungen, einschließlich "Burst-to-Cloud", "Migrieren in die Cloud" und "Failover-in-Cloud". Für geplante Wartungen deaktivieren Sie die Endpunkte in Traffic Manager und warten dann, bis der Endpunkt die Verarbeitung der vorhandenen Verbindungen beendet hat. Wenn kein weiterer Datenverkehr an den Endpunkt geleitet wird, aktualisieren Sie den Dienst am Endpunkt und testen ihn. Anschließend aktivieren Sie ihn wieder in Traffic Manager. Dies hilft Ihnen, Ihre Dienste ohne Ausfallzeiten für die Clients zu warten und zu aktualisieren.
- **Verteilung des Datenverkehrs für große, komplexe Bereitstellungen**: Mit geschachtelten Traffic Manager-Profilen, in denen ein Traffic Manager-Profil über ein anderes Traffic Manager-Profil als einen Endpunkt verfügen kann, können Sie Konfigurationen zur Optimierung der Leistung und der Verteilung für größere und komplexere Bereitstellungen erstellen. Weitere Informationen finden Sie unter [Geschachtelt Profile](#nested-profiles).

## Funktionsweise von Traffic Manager

Wenn Sie ein Traffic Manager-Profil konfigurieren, stellen die von Ihnen angegebenen Einstellungen Traffic Manager die erforderlichen Informationen bereit, um zu bestimmen, welcher Endpunkt die Anforderung basierend auf einer DNS-Abfrage verarbeiten soll. Es wird kein tatsächlicher Endpunkt-Datenverkehr über Traffic Manager weitergeleitet.

*Abbildung 1* zeigt, wie Traffic Manager Benutzer zu einem Satz von Endpunkten weiterleitet. Die Zahlen in Abbildung 1 entsprechen den nummerierten Beschreibungen unten:

![Funktionsweise von Traffic Manager](./media/traffic-manager-overview/IC740854.jpg)

**Abbildung 1**

1. **Benutzerdatenverkehr an Unternehmensdomäne**: Der Client fordert Informationen mithilfe des Domänennamens des Unternehmens an. Das Ziel ist, einen DNS-Namen in eine IP-Adresse aufzulösen. Unternehmensdomänen müssen über normale Internetdomänennamen reserviert werden, die außerhalb von Traffic Manager verwaltet werden. In Abbildung 1 lautet die Unternehmensdomäne *www.contoso.com*.
2. **Unternehmensdomäne an Traffic Manager-Domänennamen**: Der DNS-Ressourceneintrag für die Unternehmensdomäne verweist auf einen Traffic Manager-Domänennamen, der in Azure Traffic Manager verwaltet wird. Dies wird mit einem CNAME-Ressourceneintrag erreicht, der den Domänennamen des Unternehmens dem Traffic Manager-Domänennamen zuordnet. Im Beispiel lautet der Traffic Manager-Domänenname *contoso.trafficmanager.net*.
3. **Traffic Manager-Domänenname und -Profil**: Der Traffic Manager-Domänenname ist Bestandteil des Traffic Manager-Profils. Der DNS-Server des Benutzers sendet eine neue DNS-Abfrage für den Traffic Manager-Domänennamen (im Beispiel *contoso.trafficmanager.net*), die von den Traffic Manager-DNS-Namensservern empfangen wird.
4. **Verarbeitung der Traffic Manager-Profilregeln**: Traffic Manager verwendet die ausgewählte Lastenausgleichsmethode sowie den zugehörigen Überwachungsstatus, um zu bestimmen, welcher Azure- oder andere Endpunkt die Abfrage verarbeiten soll.
5. **An Benutzer gesendeter Endpunkt**: Traffic Manager gibt einen CNAME-Datensatz zurück, der den Traffic Manager-Domänennamen dem Domänennamen des Endpunkts zuordnet. Der DNS-Server des Benutzers löst den Domänennamen des Endpunkts in seine IP-Adresse auf und sendet diese an den Benutzer.
6. **Benutzeraufruf des Endpunkts**: Der Benutzer ruft den zurückgegebenen Endpunkt direkt mithilfe seiner IP-Adresse auf.

Da die Unternehmensdomäne und die aufgelöste IP-Adresse auf dem Clientcomputer zwischengespeichert werden, interagiert der Benutzer weiterhin mit dem ausgewählten Endpunkt, bis der lokale DNS-Cacheeintrag abgelaufen ist. Es ist wichtig zu beachten, dass der DNS-Client die DNS-Hosteinträge für die Gültigkeitsdauer (Time-to-Live, TTL) zwischenspeichert. Das Abrufen von Hosteinträgen aus dem DNS-Clientcache umgeht das Traffic Manager-Profil. Dabei können Verbindungsverzögerungen auftreten, wenn der Endpunkt nicht mehr verfügbar ist, bevor die Gültigkeitsdauer abläuft. Wenn die Gültigkeitsdauer eines DNS-Hosteintrags im Cache abläuft und der Clientcomputer den Domänennamen des Unternehmens erneut auflösen muss, sendet er eine neue DNS-Abfrage. Der Clientcomputer empfängt möglicherweise die IP-Adresse eines anderen Endpunkts. Dies ist von der angewendeten Lastenausgleichsmethode und der Integrität der Endpunkte zum Zeitpunkt der Anforderung abhängig.

## Implementieren von Traffic Manager

*Abbildung 2* zeigt die Schritte (in der vorgegebenen Reihenfolge), die zur Implementierung von Traffic Manager erforderlich sind. Diese Schritte können in einer etwas anderen Reihenfolge ausgeführt werden, sofern Sie über gute Kenntnisse der Traffic Manager-Konfiguration und der bewährten Methoden verfügen. Die Zahlen in Abbildung 2 entsprechen den nummerierten Beschreibungen unten:

![Konfigurieren von Traffic Manager](./media/traffic-manager-overview/IC740855.jpg)

**Abbildung 2**

1. **Stellen Sie Ihre Azure-Cloud-Dienste, Azure-Websites oder anderen Endpunkte in Ihrer Produktionsumgebung bereit**. Wenn Sie ein Traffic Manager-Profil erstellen, muss es einem Abonnement zugeordnet sein. Anschließend fügen Sie Endpunkte für Cloud-Dienste und Websites der Standardebene in der Produktionsumgebung hinzu, die Teil des gleichen Abonnements sind. Wenn ein Endpunkt Teil einer Stagingumgebung ist und sich nicht in einer Azure-Produktionsumgebung befindet oder zum gleichen Abonnement gehört, kann er als externer Endpunkt hinzugefügt werden. Weitere Informationen über Cloud-Dienste finden Sie unter [Cloud-Dienste](http://go.microsoft.com/fwlink/p/?LinkId=314074). Weitere Informationen zu Websites finden Sie unter [Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327).
2. **Legen Sie einen Namen für Ihre Traffic Manager-Domäne fest**. Verwenden Sie für Ihre Domäne einen Namen mit einem eindeutigen Präfix. Der letzte Teil der Domäne ist dabei immer trafficmanager.net. Weitere Informationen finden Sie unter [Bewährte Methoden](#best-practices).
3. **Legen Sie die zu verwendende Überwachungskonfiguration fest**. Traffic Manager überwacht die Endpunkte, um sicherzustellen, dass sie unabhängig von der Lastenausgleichsmethode online sind. Nachdem Sie die Überwachungseinstellungen konfiguriert haben, leitet Traffic Manager Datenverkehr nicht an Endpunkte, die laut Überwachungssystem offline sind. Dies gilt nicht, wenn alle Endpunkte offline sind oder der Status der im Profil enthaltenen Endpunkte nicht erkannt wurde. Weitere Informationen zur Überwachung finden Sie unter [Traffic Manager-Überwachung](traffic-manager-monitoring.md).
4. **Legen Sie die zu verwendende Lastenausgleichsmethode fest**. Es sind drei verschiedene Lastenausgleichsmethoden verfügbar. Nehmen Sie sich Zeit, um zu verstehen, welche bewährte Methode Ihren Anforderungen entspricht. Wenn Sie die Methode zu einem späteren Zeitpunkt ändern müssen, können Sie dies jederzeit tun. Beachten Sie außerdem, dass für jede Methode etwas andere Konfigurationsschritte erforderlich sind. Informationen zu den Lastenausgleichsmethoden finden Sie unter [Informationen zu Lastenausgleichsmethoden von Traffic Manager](traffic-manager-load-balancing-methods.md).
5. **Erstellen Sie Ihr Profil und konfigurieren Sie die Einstellungen**. Sie können die REST-APIs, Windows PowerShell oder das Verwaltungsportal verwenden, um Ihr Traffic Manager-Profil zu erstellen und die Einstellungen zu konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren der Traffic Manager-Einstellungen](#how-to-configure-traffic-manager-settings). Bei den folgenden Schritte wird davon ausgegangen, dass Sie im Verwaltungsportal die **Schnellerfassung** verwenden. 
   - **Erstellen Sie Ihr Traffic Manager-Profil**: Informationen zum Erstellen eines Profils mithilfe der Schnellerfassung im Verwaltungsportal finden Sie unter [Verwalten von Traffic Manager-Profilen]traffic-manager-manage-profiles.md).
   - **Konfigurieren Sie die Einstellungen der Lastenausgleichsmethode**: Bei der Schnellerfassung müssen Sie die Lastenausgleichsmethode für Ihr Profil auswählen. Diese Einstellung kann zu einem beliebigen Zeitpunkt nach Abschluss der Schritte zur Schnellerfassung geändert werden. Die Konfigurationsschritte finden Sie im Thema zu Ihrer Lastenausgleichsmethode: [Konfigurieren des Lastenausgleichs](traffic-manager-configure-performance-load-balancing.md),[Konfigurieren des Failover-Lastenausgleichs](traffic-manager-configure-failover-load-balancing.md), [Konfigurieren des Roundrobin-Lastenausgleichs](traffic-manager-configure-round-robin-load-balancing.md).
   >[AZURE.NOTE] Die Roundrobin-Lastenausgleichsmethode unterstützt nun die gewichtete Verteilung des Netzwerkdatenverkehrs. Allerdings müssen Sie zu diesem Zeitpunkt entweder REST-APIs oder Windows PowerShell verwenden, um die Gewichtung zu konfigurieren. Weitere Informationen und eine Beispielkonfiguration finden Sie im Azure-Blog unter [Azure Traffic Manager External Endpoints and Weighted Round Robin via PowerShell](http://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) (in englischer Sprache).

   - **Konfigurieren von Endpunkten**: Endpunkte werden nicht während der Schnellerfassung konfiguriert. Nach dem Erstellen Ihres Profils und dem Festlegen der Lastenausgleichsmethode müssen Sie in Traffic Manager die Endpunkte angeben. Die Schritte zum Konfigurieren von Endpunkten finden Sie unter [Verwalten von Endpunkten in Traffic Manager](traffic-manager-endpoints.md).

   - **Konfigurieren von Überwachungseinstellungen**: Überwachungseinstellungen werden nicht während der Schnellerfassung konfiguriert. Nach dem Erstellen Ihres Profils und dem Festlegen der Lastenausgleichsmethode müssen Sie in Traffic Manager die zu überwachenden Objekte angeben. Die Schritte zum Konfigurieren der Überwachung finden Sie unter [Traffic Manager-Überwachung](traffic-manager-monitoring.md).
6. **Testen Sie Ihr Traffic Manager-Profil**. Testen Sie, ob das Profil und die Domäne ordnungsgemäß funktionieren. Informationen hierzu finden Sie unter [Testen von Traffic Manager-Einstellungen](traffic-manager-testing-settings.md).
7. **Verweisen Sie mit dem DNS-Ressourceneintrag des Domänennamens Ihres Unternehmens auf das Profil, damit sie aktiviert wird**. Weitere Informationen dazu finden Sie unter [Verweisen einer Unternehmens-Internetdomäne auf eine Traffic Manager-Domäne](traffic-manager-point-internet-domain.md.

Beim Beispiel in Abbildung 1 ändern Sie den DNS-Ressourceneintrag auf Ihren Servern für den folgenden Endpunkt der Unternehmensdomäne in den Traffic Manager-Domänennamen:
    www.contoso.com IN CNAME contoso.trafficmanager.net

## Konfigurieren der Traffic Manager-Einstellungen

Sie können zum Konfigurieren der Traffic Manager-Einstellungen das Verwaltungsportal, REST-APIs und Windows PowerShell-Cmdlets  verwenden.

Obwohl nicht jedes REST-API-Element im Verwaltungsportal sichtbar ist, sind viele Einstellungen mit allen Methoden verfügbar. Weitere Informationen zur Verwendung von REST-APIs finden Sie unter [Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Weitere Informationen zu Windows PowerShell-Cmdlets für Traffic Manager finden Sie unter [Azure Traffic Manager-Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769).

>[AZURE.NOTE] Es gibt zurzeit keine Unterstützung für das Konfigurieren externer Endpunkte (Typ = 'Any'), von Gewichtungen für die Roundrobin-Lastenausgleichsmethode und von geschachtelten Profilen über das Verwaltungsportal. Verwenden Sie entweder REST (siehe [Definition erstellen](http://go.microsoft.com/fwlink/p/?LinkId=400772)) oder Windows PowerShell (siehe [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)).

### Konfigurieren von Einstellungen im Verwaltungsportal

Im Verwaltungsportal können Sie Ihr Traffic Manager-Profil mithilfe der Schnellerfassung erstellen. Mit der Schnellerfassung können Sie ein Basisprofil erstellen. Nachdem Sie Ihr Profil erstellt haben, können Sie zusätzliche Einstellungen konfigurieren oder die zuvor konfigurierten Einstellungen bearbeiten. Weitere Informationen zum Erstellen des Traffic Manager-Profils mithilfe der Schnellerfassung finden Sie unter [Verwalten von Traffic Manager-Profilen](traffic-manager-manage-profiles.md).

Sie können im Verwaltungsportal die folgenden Einstellungen konfigurieren:

- **DNS-Präfix** - Ein eindeutiges, von Ihnen erstelltes Präfix. Profile werden im Verwaltungsportal nach Präfix angezeigt.
- **DNS-Gültigkeitsdauer (TTL)** - Der Wert für die DNS-Gültigkeitsdauer bestimmt, wie häufig der lokale Cachenamenserver des Clients aktualisierte DNS-Einträge aus dem DNS-System von Azure Traffic Manager abfragt.
- **Abonnement** - Wählen Sie das Abonnement aus, das Ihrem Profil zugeordnet ist. Beachten Sie, dass diese Option nur angezeigt wird, wenn Sie über mehrere Abonnements verfügen.
- **Lastenausgleichsmethode** - Die Methode, die Traffic Manager für den Lastenausgleich verwendet.
- **Failoverreihenfolge** - Wenn Sie die Failover-Lastenausgleichsmethode verwenden, ist dies die Reihenfolge der Endpunkte.
- **Überwachung** - Zu den Überwachungseinstellungen gehören das Protokoll (HTTP oder HTTPS), der Port und der relative Pfad sowie der Dateiname.

### Konfigurieren von Einstellungen mithilfe von REST-APIs

Sie können das Traffic Manager-Profil mithilfe von REST-APIs erstellen und konfigurieren. Weitere Informationen finden Sie unter [Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/?LinkId=313584).

- **Profil** - Ein Profil enthält ein Domänennamenpräfix, das Sie erstellen. Jedes Profil entspricht Ihrem Abonnement. Sie können mehrere Profile pro Abonnement erstellen. Der Profilname ist im Verwaltungsportal sichtbar. Der von Ihnen im Profil erstellte Name wird als Ihre Traffic Manager-Domäne bezeichnet.
- **Definition** - Eine Definition umfasst Richtlinien- und Überwachungseinstellungen. Eine Definition entspricht einem Profil. Es kann nur eine Definition pro Profil vorhanden sein. Die Definition selbst ist nicht im Verwaltungsportal sichtbar, obwohl viele der in der Definition enthaltenen Einstellungen sichtbar sind und im Verwaltungsportal konfiguriert werden können.
- **DNS-Optionen** - Innerhalb jeder Definition stehen DNS-Optionen zur Verfügung. Mit diesen wird die DNS-Gültigkeitsdauer konfiguriert.
- **Überwachung** - Jede Definition verfügt über Überwachungseinstellungen. Über diese Einstellungen werden das Protokoll, der Port, der relative Pfad und der Dateiname konfiguriert. Die Überwachungseinstellungen sind sichtbar und können im Verwaltungsportal konfiguriert werden. Weitere Informationen finden Sie unter [Traffic Manager-Überwachung](traffic-manager-monitoring.md).
- **Richtlinie** - Jede Definition verfügt über Richtlinieneinstellungen. In der Richtlinie werden Lastenausgleichsmethoden und Endpunkte angegeben. Die Richtlinie selbst ist nicht im Verwaltungsportal sichtbar, obwohl einige der Einstellungen für die Richtlinie sichtbar sind und im Verwaltungsportal konfiguriert werden können. Weitere Informationen finden Sie unter [Informationen zu Lastenausgleichsmethoden von Traffic Manager](traffic-manager-load-balancing-methods.md).

## Konfigurieren von Einstellungen mithilfe von Windows PowerShell

Sie können das Traffic Manager-Profil mithilfe von Windows PowerShell erstellen und konfigurieren. Weitere Informationen finden Sie unter [Azure Traffic Manager Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## Bewährte Methoden

- **Verwenden Sie eindeutige und leicht verständliche Präfixe** - Der DNS-Name Ihres Traffic Manager-Profils muss eindeutig sein. Sie können nur den ersten Teil des DNS-Namens ändern. Der Traffic Manager-Domänenname wird lediglich zur Identifizierung und Weiterleitung von Clientanforderungen verwendet. Auf Clientcomputern sind die Namen für Endbenutzer nicht sichtbar. Allerdings werden Profile anhand dieses Domänennamens identifiziert. Daher ist es wichtig, dass Sie diesen Domänennamen problemlos von anderen im Verwaltungsportal aufgelisteten Domänennamen unterscheiden können.
- **Verwenden Sie Punkte für mehr Eindeutigkeit oder verständliche Domänennamen** - Sie können auch Teile Ihres Domänennamenpräfix durch Punkte trennen.  Wenn Sie planen, mehrere Richtlinien in Traffic Manager zu erstellen, erleichtert eine einheitliche Hierarchie die Unterscheidung der einzelnen Dienste voneinander. Beispielsweise verfügt Contoso über globale Dienste für die Web-, Abrechnungs- und Hilfsprogrammverwaltung. Die drei zugehörigen Richtlinien sind *web.contoso.trafficmanager.net*, *bill.contoso.trafficmanager.net* und *util.contoso.trafficmanager.net*. Verwenden Sie beim Einrichten von Cloud-Diensten oder Websites Namen, die den Speicherort umfassen. Zum Beispiel  *web-us-contoso.cloudapp.net* und *web-asia-contoso.cloudapp.net*. Ihre Einschränkungen werden von DNS vorgegeben. Angenommen, ein Domänenname ist eine Sequenz von Bezeichnungen, die durch Punkte getrennt sind (label.label.label.label.etc.).. Zum Zeitpunkt der Erstellung dieser Dokumentation waren folgende Einschränkungen für Domänennamen in Traffic Manager gültig:
   - Jede Bezeichnung kann maximal 63 Zeichen sein.
   - Sie dürfen insgesamt nicht mehr als 40 Bezeichnungen verwenden. Da trafficmanager.net bereits zwei Bezeichnungen beansprucht, bleiben noch 38 für Ihr Präfix übrig.
   - Der gesamte Domänenname darf maximal 253 Zeichen umfassen. Beachten Sie, dass trafficmanager.net bereits 19 dieser Zeichen beansprucht.
- **DNS-Gültigkeitsdauer (TTL)** - Der Wert für die DNS-Gültigkeitsdauer bestimmt, wie häufig der lokale Cachenamenserver des Clients aktualisierte DNS-Einträge aus dem DNS-System von Azure Traffic Manager abfragt. Für jede Änderung in Traffic Manager, beispielsweise eine Profiländerung oder Änderungen bezüglich der Verfügbarkeit des Endpunkts, wird dieser Zeitraum benötigt, bis die Aktualisierung im globalen System der DNS-Server ausgeführt wurde. Es wird empfohlen, den Standardwert für diese Einstellung von 300 Sekunden (5 Minuten) nicht zu ändern Ein höherer Wert verlängert die Zeit, die Traffic Manager-DNS-Antworten von DNS-Resolvern und -Clients zwischengespeichert werden, und verringert so die Gesamtlatenz von DNS-Abfragen. Wenn ein Failover sehr schnell erfolgen muss, empfiehlt sich die Konfiguration eines niedrigeren Werts.
- **Endpunkte sollten sich in einem einzigen Abonnement befinden** - Alle Endpunkte sollten beim Erstellen des Profils ein und demselben Abonnement angehören. Sie können einem Profil Endpunkte aus verschiedenen Abonnements als externe Endpunkte hinzufügen. Azure entfernt diese jedoch nicht automatisch, wenn Sie den zugehörigen Dienst deaktivieren oder löschen. Externe Endpunkte verbleiben daher im Traffic Manager-Profil, und es fallen weiterhin Gebühren an, wenn Sie diese Endpunkte nicht manuell entfernen.
- **Nur Produktionsdienste** - Es sind nur Endpunkte in einer Produktionsumgebung verfügbar. Eine Weiterleitung an Endpunkte in einer Stagingumgebung ist nicht möglich.  Beachten Sie, dass wenn Sie einen virtuellen IP-Adressaustausch (VIP-Austausch) ausführen, während Datenverkehr von einem Profil weitergeleitet wird, der Endpunkt für den Datenverkehr verwendet wird, der gerade in die Produktionsumgebung verlagert wurde.
- **Benennen Sie Ihre Endpunkte so, dass diese leicht identifiziert werden können** - Verwenden Sie ein geeignetes DNS-Präfix. Der DNS-Name wird verwendet, da dieser in einem Abonnement in jedem Falle eindeutig ist, während dies beim Namen des Cloud-Diensts oder der Website möglicherweise nicht der Fall ist. Um Verwechslungen zu vermieden, weisen Sie einem Cloud-Dienst oder einer Website einen Namen und ein DNS-Präfix zu, die identisch oder ähnlich sind. Wenn Sie über mehr als 20 Cloud-Dienste und Websites verfügen, können nicht eindeutige Benennungen die Suche nach dem richtigen Endpunkt erschweren. Außerdem lassen sich durch nicht eindeutige Endpunktbenennungen Profile nur schwer verwalten.
- **Alle Endpunkte in einem Profil sollten für die gleichen Vorgänge und Ports gelten** - Wenn Sie Ihre Endpunkte kombinieren, steigt die Wahrscheinlichkeit, dass ein Client einen Endpunkt aufruft, der die Anforderung nicht verarbeiten kann.
- **Alle Cloud-Dienste in einem Profil müssen die gleichen Überwachungseinstellungen verwenden** - Sie können nur einen einzelnen Pfad und eine einzelne Datei zur Überwachung aller Endpunkte in einer bestimmten Definition auswählen. Sie können "/" im Textfeld **Relativer Pfad und Dateiname** eingeben, damit die Überwachung auf den Standardpfad und den Standarddateinamen zugreifen kann.
- **Endpunkte für temporäre Änderungen deaktivieren, anstatt Ihre Konfiguration zu ändern** - In vielen Fällen ist es ggf. erforderlich, einen Endpunkt offline zu schalten Anstatt den Endpunkt aus dem Profil zu entfernen, deaktivieren Sie einfach den einzelnen Endpunkt in Ihrem Profil. Auf diese Weise bleibt der Endpunkt zwar Bestandteil des Profils, die Richtlinie verhält sich aber so, als ob der Endpunkt nicht in ihr enthalten wäre. Dies ist sehr hilfreich, um einen Endpunkt vorübergehend zu entfernen, der gewartet oder erneut bereitgestellt wird. Sobald der Endpunkt wieder verfügbar ist und ausgeführt wird, kann er aktiviert werden. Weitere Informationen finden Sie unter [Verwalten von Endpunkten in Traffic Manager](traffic-manager-endpoints.md).
- **Deaktivieren Sie ein Profil für temporäre Änderungen, anstatt es zu löschen** - Sie können ein vollständiges Profil offline schalten, und nicht nur einzelne darin angegebene Endpunkte. Deaktivieren Sie dazu das Profil. Wenn Sie ein Profil deaktivieren, können Sie dennoch alle Einstellungen im Verwaltungsportal bearbeiten. Wenn Sie das Profil wieder verwenden möchten, können Sie es schnell und einfach online schalten. Weitere Informationen finden Sie unter [Verwalten von Endpunkten in Traffic Manager](traffic-manager-endpoints.md).
- **Speicher** - Die Standortgestaltung und die Verteilung des Speichers sind wichtige Überlegungen für die Verwendung von Traffic Manager. Denken Sie beim Entwerfen und Bereitstellen Ihrer Anwendungen für Traffic Manager an eine End-to-End-Transaktion, und berücksichtigen Sie den Datenfluss.
- **SQL Azure** - Analog zur Gestaltung des Speichers ist eine Analyse des Anwendungszustands und der Datenanforderungen bei der Erweiterung Ihrer Endpunkte auf mehrere geografische Regionen wichtig.

## Geschachtelte Profile

Sie können den Namen eines anderen Traffic Manager-Profils als Endpunkt angeben. Daraus ergibt sich ein sogenanntes geschachteltes Profil. Der Name des Traffic Manager-Profils ist dessen DNS-Name, beispielsweise "contoso-europe.trafficmanager.net".

Dies ermöglicht es Ihnen, Traffic Manager so zu konfigurieren, dass eingehende DNS-Namensabfragen auf mehreren Ebenen analysiert werden, sodass sichergestellt werden kann, dass der anfordernde Client zur richtigen Gruppe von Endpunkten geleitet wird.  *Figure 3* zeigt ein Beispiel.

![Beispiel für geschachtelte Traffic Manager-Profile](./media/traffic-manager-overview/IC751072.png)

**Abbildung 3**

Sie können bis zu 10 Ebenen schachteln, und jedes Profil kann mit einer anderen Lastenausgleichsmethode konfiguriert werden.

Beispielsweise könnten Sie eine Konfiguration für Folgendes erstellen:

- Auf der obersten Ebene (das Traffic Manager-Profil, das Ihrem externen DNS-Namen zugeordnet ist) konfigurieren Sie das Profil mit einer leistungsbezogenen Lastenausgleichsmethode.
- Auf der mittleren Ebene entsprechen mehrere Traffic Manager-Profile unterschiedlichen Rechenzentren. Für diese Profile wird die Roundrobin-Lastenausgleichsmethode verwendet.
- Auf der untersten Ebene werden die Datenverkehrsanforderungen des Benutzers von den Cloud-Dienst-Endpunkten des jeweiligen Rechenzentrums verarbeitet.

Dies führt dazu, dass Benutzer anhand der Leistung zu einem regional geeigneten Rechenzentrum und in diesem Rechenzentrum anhand gleicher oder gewichteter Lastenverteilung zu einem Cloud-Dienst geleitet werden. Gewichtung könnte beispielsweise verwendet werden, um einen kleinen Prozentsatz des Datenverkehrs zu Testzwecken oder für Kundenfeedback auf eine neue oder eine Testbereitstellung zu verteilen.

*Abbildung 4* zeigt die Konfiguration.

![Beispiel für mehrschichtige Traffic Manager-Profile](./media/traffic-manager-overview/IC751073.png)

**Abbildung 4**

In  *Figure 4* ist das Traffic Manager-Profil auf der obersten Ebene ein übergeordnetes Profil, und die Traffic Manager-Profile auf der mittleren Ebene sind untergeordnete Profile.

Wenn Traffic Manager Benutzer zu einem untergeordneten Profil leitet, das nur über wenige einsatzbereite (integere) Endpunkte verfügt, werden diese Endpunkte möglicherweise überlastet, sodass Leistungsprobleme auftreten können. Um diese Situation zu vermeiden, können Sie das übergeordnete Traffic Manager-Profil mit einem Schwellenwert für einsatzbereite Endpunkte konfigurieren, über den ermittelt wird, ob einer der Endpunkte in den zugehörigen untergeordneten Profilen Datenverkehr empfangen kann. Wenn Sie beispielsweise sicherstellen möchten, dass es in den untergeordneten Profilen mindestens drei einsatzbereite Endpunkte gibt, legen Sie diesen Schwellenwert auf 3 fest. Im Beispiel in Abbildung 4 würden Sie das auf der obersten Ebene befindliche Traffic Manager-Profil mit diesem Schwellenwert konfigurieren.

Wenn Sie ein Traffic Manager-Profil als Endpunkt hinzufügen und die Mindestanzahl von einsatzbereiten (integeren) Endpunkten konfigurieren möchten, müssen Sie REST (siehe [Definition erstellen](http://go.microsoft.com/fwlink/p/?LinkId=400772)) oder Windows PowerShell (siehe [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)) verwenden. Das Verwaltungsportal können Sie dazu nicht verwenden.

## Traffic Manager-Abbildungen

Wenn Sie die Abbildungen in diesem Thema als PowerPoint-Folien in Ihrer eigenen Präsentation zu Traffic Manager verwenden oder für Ihre eigenen Zwecke ändern möchten, lesen Sie [Traffic Manager-Abbildungen in der MSDN-Dokumentation](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Siehe auch

[Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/p/?LinkId=313584)

[Azure Traffic Manager-Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!--HONumber=49--> 