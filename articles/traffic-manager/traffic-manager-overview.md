<properties 
   pageTitle="Traffic Manager – Übersicht"
   description="Dieser Artikel hilft Ihnen zu verstehen, was Traffic Manager ist und wie er funktioniert."
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
   ms.date="07/10/2015"
   ms.author="joaoma" />

# Was ist Traffic Manager?

Mit Microsoft Azure Traffic Manager können Sie die Verteilung des Benutzerdatenverkehrs an Ihre angegebenen Endpunkte steuern, darunter z. B. Azure Cloud Services, Websites und andere Endpunkte. Die Funktionsweise von Traffic Manager basiert darauf, dass Sie ein intelligentes Richtlinienmodul auf DNS-Abfragen (Domain Name System) von Domänennamen Ihrer Internetressourcen anwenden. Die Azure Cloud Services oder Websites können in verschiedenen Datencentern auf der ganzen Welt ausgeführt werden.

Traffic Manager unterstützt Sie bei folgenden Aufgaben:

- **Verbessern der Verfügbarkeit wichtiger Anwendungen**: Mit Traffic Manager können Sie die Verfügbarkeit wichtiger Anwendungen verbessern, indem Sie Ihre Endpunkte in Azure überwachen und automatische Failoverfunktionen bereitstellen, wenn ein Azure-Clouddienst, eine Azure-Website oder ein anderer Standort ausfällt.
- **Verbessern der Reaktionsschnelligkeit hochleistungsfähiger Anwendungen**: Azure ermöglicht die Ausführung von Clouddiensten oder Websites in Rechenzentren auf der ganzen Welt. Traffic Manager sorgt für reaktionsschnellere Anwendungen und verbessert die Inhaltsübermittlung, indem Endbenutzer vom Client an den Endpunkt mit der niedrigsten Netzwerklatenz weitergeleitet werden.
- **Aktualisieren und Warten ohne Ausfallzeiten**: Traffic Manager unterstützt erweiterte Szenarien für Hybridcloud und lokale Bereitstellungen, einschließlich der Szenarien "Erweiterung in die Cloud", "Migration in die Cloud" und "Failover in die Cloud". Für eine geplante Wartung deaktivieren Sie den Endpunkt in Traffic Manager und warten dann, bis der Endpunkt das Bedienen der vorhandenen Verbindungen abgeschlossen hat. Sobald der Endpunkt keinen weiteren Datenverkehr empfängt, können Sie den Dienst auf diesem Endpunkt aktualisieren, testen und den Dienst anschließend in Traffic Manager erneut aktivieren. Auf diese Weise können Sie ohne Ausfallzeiten für Clients Wartungsmaßnahmen oder Upgrades für die Dienste ausführen.
- **Verteilung des Datenverkehrs für große, komplexe Bereitstellungen**: Mit geschachtelten Traffic Manager-Profilen, in denen ein Traffic Manager-Profil über ein anderes Traffic Manager-Profil als einen Endpunkt verfügen kann, können Sie Konfigurationen zur Optimierung der Leistung und der Verteilung für größere und komplexere Bereitstellungen erstellen. Weitere Informationen finden Sie unter [Geschachtelte Profile](#nested-profiles).

## Funktionsweise von Traffic Manager

Beim Konfigurieren eines Traffic Manager-Profils legen Sie verschiedene Einstellungen fest. Anhand dieser Einstellungen kann Traffic Manager entscheiden, welcher Endpunkt basierend auf einer DNS-Abfrage zur Verarbeitung einer Anforderung ausgewählt werden soll. Der eigentliche Endpunktdatenverkehr für den Dienst wird nicht über Traffic Manager geroutet.

*Abbildung 1* zeigt, wie Traffic Manager Benutzer an einen Endpunkt aus einem Satz von Endpunkten weiterleitet. Die Zahlen in Abbildung 1 entsprechen den nummerierten Beschreibungen unten.

![Funktionsweise von Traffic Manager](./media/traffic-manager-overview/IC740854.jpg)

**Abbildung 1**

1. **Benutzerdatenverkehr an den Domänennamen des Unternehmens**: Der Client fordert Informationen mithilfe des Domänennamens des Unternehmens an. Das Ziel ist, einen DNS-Namen in eine IP-Adresse aufzulösen. Unternehmensdomänen müssen über normale Internetdomänennamen reserviert werden, die außerhalb von Traffic Manager verwaltet werden. In Abbildung 1 lautet die Unternehmensdomäne *www.contoso.com*.
2. **Zuordnen des Namens der Unternehmensdomäne zum Traffic Manager-Domänennamen**: Der DNS-Ressourceneintrag für die Unternehmensdomäne verweist auf einen Traffic Manager-Domänennamen, der in Azure Traffic Manager verwaltet wird. Dies wird mit einem CNAME-Ressourceneintrag erreicht, der den Domänennamen des Unternehmens dem Traffic Manager-Domänennamen zuordnet. Im Beispiel lautet der Traffic Manager-Domänenname *contoso.trafficmanager.net*.
3. **Traffic Manager-Domänenname und -Profil**: Der Traffic Manager-Domänenname ist Bestandteil des Traffic Manager-Profils. Der DNS-Server des Benutzers sendet eine neue DNS-Abfrage für den Traffic Manager-Domänennamen (im Beispiel *contoso.trafficmanager.net*), die von den Traffic Manager-DNS-Namensservern empfangen wird.
4. **Verarbeiten der Traffic Manager-Profilregeln**: Traffic Manager verwendet die ausgewählte Lastenausgleichsmethode sowie den zugehörigen Überwachungsstatus, um zu bestimmen, welcher Azure- oder anderer Endpunkt die Anforderung verarbeiten soll.
5. **Senden des Endpunktdomänennamens an den Benutzer**: Traffic Manager gibt einen CNAME-Eintrag zurück, der den Traffic Manager-Domänennamen dem Domänennamen des Endpunkts zuordnet. Der DNS-Server des Benutzers löst den Domänennamen des Endpunkts in seine IP-Adresse auf und sendet diese an den Benutzer.
6. **Aufrufen des Endpunkts durch den Benutzer**: Der Benutzer ruft den zurückgegebenen Endpunkt mithilfe der zugehörigen IP-Adresse direkt auf.

Da der Domänenname des Unternehmens und die aufgelöste IP-Adresse auf dem Clientcomputer zwischengespeichert werden, kann der Benutzer so lange mit dem ausgewählten Endpunkt interagieren, bis der lokale DNS-Cacheeintrag abläuft. Beachten Sie, dass die DNS-Hosteinträge bis zum Ablauf der Gültigkeitsdauer vom DNS-Client zwischengespeichert werden. Wenn Hosteinträge aus dem DNS-Clientcache abgerufen werden, wird das Traffic Manager-Profil umgangen. Es können Verbindungsverzögerungen auftreten, wenn der Endpunkt vor Ablauf der Gültigkeitsdauer nicht mehr verfügbar ist. Wenn die Gültigkeitsdauer eines DNS-Hosteintrags im Cache abläuft und der Clientcomputer den Domänennamen des Unternehmens erneut auflösen muss, sendet er eine neue DNS-Abfrage. Der Clientcomputer empfängt möglicherweise die IP-Adresse eines anderen Endpunkts. Dies hängt davon ab, welche Lastenausgleichsmethode verwendet wird und welche Integrität die Endpunkte zum Zeitpunkt der Anforderung aufweisen.

## Implementieren von Traffic Manager

*Abbildung 2* zeigt die zur Implementierung von Traffic Manager erforderlichen Schritte in der Reihenfolge ihrer Ausführung an. Diese Schritte können in leicht abweichender Reihenfolge ausgeführt werden, wenn Sie mit der Traffic Manager-Konfiguration und den bewährten Methoden umfassend vertraut sind. Die Zahlen in Abbildung 2 entsprechen den nummerierten Beschreibungen unten.

![Konfigurieren von Traffic Manager](./media/traffic-manager-overview/IC740855.jpg)

**Abbildung 2**

1. **Stellen Sie Ihre Azure-Clouddienste, Azure-Websites oder anderen Endpunkte in Ihrer Produktionsumgebung bereit**. Wenn Sie ein Traffic Manager-Profil erstellen, muss es einem Abonnement zugeordnet sein. Anschließend fügen Sie Endpunkte für Clouddienste und Websites der Standardebene in der Produktionsumgebung hinzu, die Teil des gleichen Abonnements sind. Wenn ein Endpunkt Teil einer Stagingumgebung ist und sich nicht in einer Azure-Produktionsumgebung befindet oder zum gleichen Abonnement gehört, kann er als externer Endpunkt hinzugefügt werden. Weitere Informationen zu Clouddiensten finden Sie unter [Clouddienste](http://go.microsoft.com/fwlink/p/?LinkId=314074). Weitere Informationen zu Websites finden Sie unter [Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327).
2. **Legen Sie einen Namen für Ihre Traffic Manager-Domäne fest**. Verwenden Sie für Ihre Domäne einen Namen mit einem eindeutigen Präfix. Der letzte Teil der Domäne ist dabei immer trafficmanager.net. Weitere Informationen finden Sie unter [Bewährte Methoden](#best-practices).
3. **Legen Sie die zu verwendende Überwachungskonfiguration fest**. Traffic Manager überwacht die Endpunkte, um sicherzustellen, dass sie unabhängig von der Lastenausgleichsmethode online sind. Nachdem Sie die Überwachungseinstellungen konfiguriert haben, leitet Traffic Manager Datenverkehr nicht an Endpunkte, die laut Überwachungssystem offline sind. Dies gilt nicht, wenn alle Endpunkte offline sind oder der Status der im Profil enthaltenen Endpunkte nicht erkannt wurde. Weitere Informationen zur Überwachung finden Sie unter [Traffic Manager-Überwachung](traffic-manager-monitoring.md).
4. **Legen Sie die zu verwendende Lastenausgleichsmethode fest**. Es sind drei verschiedene Lastenausgleichsmethoden verfügbar. Nehmen Sie sich Zeit, um zu verstehen, welche bewährte Methode Ihren Anforderungen entspricht. Wenn Sie die Methode zu einem späteren Zeitpunkt ändern müssen, können Sie dies jederzeit tun. Beachten Sie außerdem, dass für jede Methode etwas andere Konfigurationsschritte erforderlich sind. Informationen zu den Lastenausgleichsmethoden finden Sie unter [Informationen zu Lastenausgleichsmethoden von Traffic Manager](traffic-manager-load-balancing-methods.md).
5. **Erstellen Sie Ihr Profil, und konfigurieren Sie die Einstellungen**. Sie können die REST-APIs, Windows PowerShell oder das Verwaltungsportal verwenden, um Ihr Traffic Manager-Profil zu erstellen und die Einstellungen zu konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren der Traffic Manager-Einstellungen](#how-to-configure-traffic-manager-settings). Bei den folgenden Schritten wird davon ausgegangen, dass Sie die **Schnellerfassung** im Verwaltungsportal verwenden. 
   - **Erstellen Sie Ihr Traffic Manager-Profil**: Informationen zum Erstellen eines Profils mithilfe der Schnellerfassung im Verwaltungsportal finden Sie unter [Verwalten von Traffic Manager-Profilen](traffic-manager-manage-profiles.md).
   - **Konfigurieren Sie die Einstellungen der Lastenausgleichsmethode**: Bei der Schnellerfassung müssen Sie die Lastenausgleichsmethode für das Profil auswählen. Diese Einstellung kann jederzeit geändert werden, nachdem Sie die Schritte für die Schnellerfassung ausgeführt haben. Die Konfigurationsschritte finden Sie im Thema zu Ihrer Lastenausgleichsmethode: [Konfigurieren der Lastenausgleichsmethode "Leistung"](traffic-manager-configure-performance-load-balancing.md), [Konfigurieren der Lastenausgleichsmethode "Failover"](traffic-manager-configure-failover-load-balancing.md), [Konfigurieren der Lastenausgleichsmethode "Roundrobin"](traffic-manager-configure-round-robin-load-balancing.md). >[AZURE.NOTE]Die Lastenausgleichsmethode "Roundrobin" unterstützt jetzt auch die gewichtete Verteilung von Netzwerkdatenverkehr. Zurzeit müssen Sie zum Konfigurieren von Gewichtung jedoch die REST-APIs oder Windows PowerShell verwenden. Weitere Informationen und ein Konfigurationsbeispiel finden Sie unter [Azure Traffic Manager: Externe Endpunkte und gewichtetes Roundrobin über PowerShell](http://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) (in englischer Sprache) im Azure-Blog.

   - **Konfigurieren von Endpunkten**: Endpunkte werden während der Schnellerfassung nicht konfiguriert. Nachdem Sie das Profil erstellt und die Lastenausgleichsmethode angegeben haben, müssen Sie dem Traffic Manager Informationen zu den Endpunkten bereitstellen. Die Schritte zum Konfigurieren von Endpunkten finden Sie unter [Verwalten von Endpunkten in Traffic Manager](traffic-manager-endpoints.md).

   - **Konfigurieren von Überwachungseinstellungen**: Überwachungseinstellungen werden während der Schnellerfassung nicht konfiguriert. Nachdem Sie das Profil erstellt und die Lastenausgleichsmethode angegeben haben, müssen Sie dem Traffic Manager mitteilen, welche Objekte überwacht werden sollen. Die Schritte zum Konfigurieren der Überwachung finden Sie unter [Traffic Manager-Überwachung](traffic-manager-monitoring.md).
6. **Testen Sie Ihr Traffic Manager-Profil**. Testen Sie, ob das Profil und die Domäne ordnungsgemäß funktionieren. Informationen hierzu finden Sie unter [Testen der Traffic Manager-Einstellungen](traffic-manager-testing-settings.md).
7. **Verweisen Sie mit dem DNS-Ressourceneintrag des Domänennamens Ihres Unternehmens auf das Profil, damit es aktiviert wird**. Weitere Informationen dazu finden Sie unter [Verweisen einer Unternehmens-Internetdomäne auf eine Traffic Manager-Domäne](traffic-manager-point-internet-domain.md).

Beim Beispiel in Abbildung 1 können Sie den DNS-Ressourceneintrag auf Ihren Servern folgendermaßen ändern, damit der Domänenname des Unternehmens auf den Traffic Manager-Domänennamen verweist: www.contoso.com IN CNAME contoso.trafficmanager.net

## Konfigurieren von Traffic Manager-Einstellungen

Sie können Traffic Manager-Einstellungen im Verwaltungsportal, mithilfe der REST-APIs und mit Windows PowerShell-Cmdlets konfigurieren.

Obwohl nicht jedes REST-API-Element im Verwaltungsportal sichtbar ist, können viele Einstellungen mit beiden Methoden konfiguriert werden. Weitere Informationen zur Verwendung von REST-APIs finden Sie unter [Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Weitere Informationen zu Windows PowerShell-Cmdlets für Traffic Manager finden Sie unter [Azure Traffic Manager-Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769).

>[AZURE.NOTE]Zurzeit wird das Konfigurieren von externen Endpunkten (Typ = "Any"), von Gewichtungen für die Lastenausgleichsmethode "Roundrobin" sowie von geschachtelten Profilen mit dem Verwaltungsportal nicht unterstützt. Hierzu müssen Sie entweder REST (siehe [Definition erstellen](http://go.microsoft.com/fwlink/p/?LinkId=400772)) oder Windows PowerShell (siehe [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)) verwenden.

### Konfigurieren von Einstellungen im Verwaltungsportal

Im Verwaltungsportal können Sie Ihr Traffic Manager-Profil mithilfe der Schnellerfassung erstellen. Über die Schnellerfassung erstellen Sie ein Basisprofil. Nachdem Sie Ihr Profil erstellt haben, können Sie zusätzliche Einstellungen konfigurieren oder die zuvor konfigurierten Einstellungen bearbeiten. Weitere Informationen zum Erstellen des Traffic Manager-Profils mithilfe der Schnellerfassung finden Sie unter [Verwalten von Traffic Manager-Profilen](traffic-manager-manage-profiles.md).

Sie können die folgenden Einstellungen im Verwaltungsportal konfigurieren:

- **DNS-Präfix** – Ein eindeutiges, von Ihnen erstelltes Präfix. Profile werden im Verwaltungsportal nach Präfix angezeigt.
- **DNS-Gültigkeitsdauer (TTL)** – Der Wert für die DNS-Gültigkeitsdauer bestimmt, wie häufig der lokale Cachenamenserver des Clients aktualisierte DNS-Einträge aus dem DNS-System von Azure Traffic Manager abfragt.
- **Abonnement** – Wählen Sie das Abonnement aus, das Ihrem Profil zugeordnet ist. Beachten Sie, dass diese Option nur angezeigt wird, wenn Sie über mehrere Abonnements verfügen.
- **Lastenausgleichsmethode** – Die Methode, die Traffic Manager für den Lastenausgleich verwendet.
- **Failoverreihenfolge** – Wenn Sie die Lastenausgleichsmethode "Failover" verwenden, ist dies die Reihenfolge der Endpunkte.
- **Überwachung** – Die Einstellungen geben das Protokoll (HTTP oder HTTPS), den Port sowie den relativen Pfad und Dateinamen an.

### Konfigurieren von Einstellungen mithilfe von REST-APIs

Sie können das Traffic Manager-Profil mithilfe von REST-APIs erstellen und konfigurieren. Weitere Informationen finden Sie unter [Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/?LinkId=313584).

- **Profil** – Ein Profil enthält ein von Ihnen erstelltes Domänennamenpräfix. Jedes Profil entspricht Ihrem Abonnement. Sie können mehrere Profile pro Abonnement erstellen. Der Profilname ist im Verwaltungsportal sichtbar. Der von Ihnen erstellte und im Profil enthaltene Name ist der Name Ihrer Traffic Manager-Domäne.
- **Definition** – Eine Definition enthält Richtlinien- und Überwachungseinstellungen. Eine Definition gehört zu einem Profil. Es kann nur eine Definition pro Profil vorhanden sein. Die Definition selbst ist nicht im Verwaltungsportal sichtbar, obwohl viele der in der Definition enthaltenen Einstellungen angezeigt und im Verwaltungsportal konfiguriert werden können.
- **DNS-Optionen** – Jede Definition umfasst DNS-Optionen. Mit diesen Optionen wird die DNS-Gültigkeitsdauer konfiguriert.
- **Überwachung** – Jede Definition umfasst Überwachungseinstellungen. Mit diesen Einstellungen werden Protokoll, Port, relativer Pfad und Dateiname konfiguriert. Überwachungseinstellungen sind sichtbar und können im Verwaltungsportal konfiguriert werden. Weitere Informationen finden Sie unter [Traffic Manager-Überwachung](traffic-manager-monitoring.md).
- **Richtlinie** – Jede Definition umfasst Richtlinieneinstellungen. Mithilfe der Richtlinie werden Lastenausgleichsmethoden und Endpunkte definiert. Die Richtlinie selbst ist nicht im Verwaltungsportal sichtbar, obwohl viele der in der Richtlinie enthaltenen Einstellungen angezeigt und im Verwaltungsportal konfiguriert werden können. Weitere Informationen finden Sie unter [Traffic Manager-Lastenausgleichsmethoden](traffic-manager-load-balancing-methods.md).

## Konfigurieren von Einstellungen mithilfe von Windows PowerShell

Sie können Ihr Traffic Manager-Profil mithilfe von Windows PowerShell erstellen und konfigurieren. Weitere Informationen finden Sie unter [Azure Traffic Manager-Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## Bewährte Methoden

- **Verwenden Sie eindeutige und leicht verständliche Präfixe** – Der DNS-Name Ihres Traffic Manager-Profils muss eindeutig sein. Sie können nur den ersten Teil des DNS-Namens selbst festlegen. Der Traffic Manager-Domänenname wird ausschließlich zur Identifizierung und Weiterleitung von Clientanforderungen verwendet. Auf Clientcomputern sind die Namen für Endbenutzer nicht sichtbar. Allerdings werden Profile anhand dieses Domänennamens identifiziert. Deshalb ist es wichtig, dass Sie diesen Domänennamen problemlos von anderen im Verwaltungsportal aufgelisteten Domänennamen unterscheiden können.
- **Verwenden Sie Punkte, um eindeutige und leicht lesbare Domänennamen zu erstellen** – Sie können auch Teile Ihres Domänennamenpräfix durch Punkte trennen. Wenn Sie planen, mehrere Richtlinien in Traffic Manager zu erstellen, erleichtert eine konsistente Hierarchie die Unterscheidung der einzelnen Dienste voneinander. Beispielsweise verfügt Contoso über globale Dienste für die Web-, Abrechnungs- und Hilfsprogrammverwaltung. Die drei zugehörigen Richtlinien würden *web.contoso.trafficmanager.net*, *bill.contoso.trafficmanager.net* und *util.contoso.trafficmanager.net* lauten. Verwenden Sie beim Einrichten von Clouddiensten oder Websites Namen, die den Standort enthalten. Verwenden Sie beispielsweise *web-us-contoso.cloudapp.net* und *web-asia-contoso.cloudapp.net*. Es gelten Einschränkungen aufgrund von DNS. Ein Domänenname ist im Prinzip eine Sequenz von Bezeichnungen, die durch Punkte voneinander getrennt sind (Bezeichnung.Bezeichnung.Bezeichnung.Bezeichnung.usw.). Zum Zeitpunkt der Erstellung dieser Dokumentation gab es folgende Einschränkungen für Domänennamen in Traffic Manager:
   - Jede Bezeichnung kann maximal 63 Zeichen umfassen.
   - Es können insgesamt nicht mehr als 40 Bezeichnungen verwendet werden. Da zwei Bezeichnungen durch "trafficmanager.net" belegt sind, können Sie 38 Bezeichnungen für Ihr Präfix verwenden.
   - Der gesamte Domänenname kann maximal 253 Zeichen umfassen. Beachten Sie, dass hiervon 19 Zeichen durch "trafficmanager.net" beansprucht werden.
- **DNS-Gültigkeitsdauer** – Der Wert für die DNS-Gültigkeitsdauer bestimmt, wie häufig der lokale Cachenamenserver des Clients aktualisierte DNS-Einträge aus dem DNS-System von Azure Traffic Manager abfragt. Für jede Änderung in Traffic Manager, beispielsweise eine Profiländerung oder Änderungen bezüglich der Verfügbarkeit von Endpunkten, wird dieser Zeitraum zur Aktualisierung im globalen System der DNS-Server benötigt. Es wird empfohlen, den Standardwert von 300 Sekunden (5 Minuten) für diese Einstellung beizubehalten. Ein höherer Wert verlängert den Zeitraum, für den Traffic Manager-DNS-Antworten von DNS-Resolvern und -Clients zwischengespeichert werden, und verringert so die Latenz von DNS-Abfragen insgesamt. Wenn aber ein Failover sehr schnell erfolgen muss, empfiehlt sich die Konfiguration eines niedrigeren Werts.
- **Endpunkte sollten sich in einem einzigen Abonnement befinden** – Alle Endpunkte sollten zu dem Abonnement gehören, für das Sie das Profil erstellen. Sie können einem Profil Endpunkte aus verschiedenen Abonnements als externe Endpunkte hinzufügen. Azure entfernt diese jedoch nicht automatisch, wenn Sie den zugehörigen Dienst deaktivieren oder löschen. Externe Endpunkte verbleiben daher im Traffic Manager-Profil, und es fallen weiterhin Gebühren an, wenn Sie diese Endpunkte nicht manuell entfernen.
- **Nur Produktionsdienste** – Es stehen nur Endpunkte zur Verfügung, die sich in einer Produktionsumgebung befinden. Eine Weiterleitung an Endpunkte in einer Stagingumgebung ist nicht möglich. Wenn Sie einen virtuellen IP-Adressaustausch (VIP-Austausch) durchführen, während Datenverkehr von einem Profil weitergeleitet wird, wird für den Datenverkehr der getauschte Endpunkt in der Produktionsumgebung verwendet.
- **Geben Sie Ihren Endpunkten leicht identifizierbare Namen** – Verwenden Sie ein geeignetes DNS-Präfix. Der DNS-Name wird verwendet, da dieser in einem Abonnement in jedem Fall eindeutig ist, während dies beim Namen des Clouddiensts oder der Website möglicherweise nicht der Fall ist. Um Verwechslungen zu vermeiden, sollten Sie einem Clouddienst oder einer Website einen Namen und ein DNS-Präfix geben, das identisch oder ähnlich ist. Wenn Sie über mehr als 20 Cloudddienste und Websites verfügen, kann eine nicht eindeutige Benennung zu Problemen beim Identifizieren des richtigen Endpunkts führen. Darüber hinaus wird durch nicht eindeutig benannte Endpunkte das Verwalten von Profilen erschwert.
- **Alle Endpunkte in einem Profil sollten dieselben Vorgänge und Ports verwenden** – Wenn Sie Ihre Endpunkte mischen, steigt das Risiko, dass ein Client einen Endpunkt aufruft, der die Anforderung nicht verarbeiten kann.
- **Alle Clouddienste in einem Profil müssen dieselben Überwachungseinstellungen verwenden** – Sie können nur einen einzelnen Pfad und eine einzelne Datei zur Überwachung aller Endpunkte in einer bestimmten Definition auswählen. Sie können im Textfeld **Relativer Pfad und Dateiname** den Wert "/" eingeben, damit die Überwachung auf den Standardpfad und -dateinamen zugreift.
- **Deaktivieren Sie Endpunkte für temporäre Änderungen, anstatt die Konfiguration zu ändern** – Es kann häufiger vorkommen, dass Sie einen Endpunkt offline schalten möchten. Statt den Endpunkt aus dem Profil zu entfernen, deaktivieren Sie einfach den betreffenden Endpunkt in Ihrem Profil. Auf diese Weise bleibt der Endpunkt Bestandteil des Profils, aber das Profil verhält sich, als wäre der Endpunkt nicht darin enthalten. Dies ist nützlich, wenn Sie einen Endpunkt temporär entfernen möchten, beispielsweise, weil dieser sich im Wartungsmodus befindet oder erneut bereitgestellt wird. Sobald der Endpunkt wieder betriebsbereit ist, können Sie ihn wieder aktivieren. Weitere Informationen finden Sie unter [Verwalten von Endpunkten in Traffic Manager](traffic-manager-endpoints.md).
- **Deaktivieren Sie ein Profil für temporäre Änderungen, anstatt es zu löschen** – Sie können ein vollständiges Profil offline schalten, und nicht nur einzelne darin angegebene Endpunkte. Deaktivieren Sie dazu das Profil. Wenn Sie ein Profil deaktivieren, können Sie dennoch alle Einstellungen im Verwaltungsportal bearbeiten. Wenn Sie das Profil wieder verwenden möchten, können Sie es schnell und einfach online schalten. Weitere Informationen finden Sie unter [Verwalten von Endpunkten in Traffic Manager](traffic-manager-endpoints.md).
- **Speicher** – Die Standortgestaltung und die Verteilung des Speichers sind wichtige Überlegungen für die Verwendung von Traffic Manager. Berücksichtigen Sie bei Entwurf und Bereitstellung von Anwendungen für Traffic Manager die End-to-End-Transaktion und den Datenfluss.
- **SQL Azure** – Analog zur Gestaltung des Speichers ist eine Analyse des Anwendungszustands und der Datenanforderungen bei der Erweiterung Ihrer Endpunkte auf mehrere geografische Regionen wichtig.

## Geschachtelte Profile

Sie können den Namen eines anderen Traffic Manager-Profils als Endpunkt angeben, sodass ein so genanntes geschachteltes Profil entsteht. Der Name des Traffic Manager-Profils entspricht dem zugehörigen DNS-Namen, beispielsweise "contoso-europe.trafficmanager.net".

Dies ermöglicht es Ihnen, Traffic Manager so zu konfigurieren, dass eingehende DNS-Namensabfragen auf mehreren Ebenen analysiert werden. Auf diese Weise wird sichergestellt, dass der anfordernde Client zur richtigen Gruppe von Endpunkten weitergeleitet wird. *Abbildung 3* zeigt ein Beispiel.

![Beispiel für geschachtelte Traffic Manager-Profile](./media/traffic-manager-overview/IC751072.png)

**Abbildung 3**

Sie können bis zu 10 Ebenen schachteln, und jedes Profil kann mit einer anderen Lastenausgleichsmethode konfiguriert werden.

Beispielsweise könnten Sie eine Konfiguration für das folgende Szenario erstellen:

- Auf der obersten Ebene (das Traffic Manager-Profil, das Ihrem externen DNS-Namen zugeordnet ist) konfigurieren Sie das Profil mit einer leistungsbezogenen Lastenausgleichsmethode.
- Auf der mittleren Ebene entsprechen mehrere Traffic Manager-Profile unterschiedlichen Rechenzentren. Für diese Profile wird die Roundrobin-Lastenausgleichsmethode verwendet.
- Auf der untersten Ebene werden die Datenverkehrsanforderungen des Benutzers von den Clouddienst-Endpunkten des jeweiligen Rechenzentrums verarbeitet.

Dies führt zu dem Ergebnis, dass Benutzer basierend auf der Leistung zu einem regional geeigneten Rechenzentrum und in diesem Rechenzentrum basierend auf einer gleichen oder gewichteten Lastenverteilung an einen Clouddienst weitergeleitet werden. Mithilfe der Gewichtung könnte beispielsweise ein kleiner Prozentsatz des Datenverkehrs zu Testzwecken oder für Kundenfeedback an eine neue oder an eine Testbereitstellung weitergeleitet werden.

Diese Konfiguration wird in *Abbildung 4* gezeigt.

![Beispiel für mehrschichtige Traffic Manager-Profile](./media/traffic-manager-overview/IC751073.png)

**Abbildung 4**

In *Abbildung 4* ist das Traffic Manager-Profil der obersten Ebene ein übergeordnetes Profil, und die Traffic Manager-Profile der mittleren Ebene sind untergeordnete Profile.

Wenn Traffic Manager Benutzer an ein untergeordnetes Profil weiterleitet, das nur wenige einsatzbereite Endpunkte umfasst, werden diese Endpunkte möglicherweise überlastet, sodass Leistungsprobleme auftreten können. Um diese Situation zu verhindern, können Sie das übergeordnete Traffic Manager-Profil mit einem Schwellenwert für einsatzbereite Endpunkte konfigurieren, der festlegt, dass eine bestimmte Anzahl von Endpunkten in den untergeordneten Profilen Datenverkehr empfangen können. Wenn Sie beispielsweise sicherstellen möchten, dass mindestens drei einsatzbereite Endpunkte in den untergeordneten Profilen vorhanden sind, legen Sie diesen Schwellenwert auf 3 fest. Im Beispiel aus Abbildung 4 würden Sie das Traffic Manager-Profil der obersten Ebene mit diesem Schwellenwert konfigurieren.

Wenn Sie ein Traffic Manager-Profil als Endpunkt hinzufügen und die Mindestanzahl von einsatzbereiten Endpunkten konfigurieren möchten, müssen Sie entweder REST (siehe [Definition erstellen](http://go.microsoft.com/fwlink/p/?LinkId=400772)) oder Windows PowerShell (siehe [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)) verwenden. Diese Aufgabe kann nicht im Verwaltungsportal ausgeführt werden.

## Traffic Manager-Abbildungen

Wenn Sie die Abbildungen in diesem Thema als PowerPoint-Folien in Ihrer eigenen Präsentation zu Traffic Manager verwenden oder für Ihre eigenen Zwecke ändern möchten, finden Sie diese unter [Traffic Manager-Abbildungen in der MSDN-Dokumentation](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99) (in englischer Sprache).

## Nächste Schritte

[Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/p/?LinkId=313584)

[Azure Traffic Manager-Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769)

<!---HONumber=July15_HO4-->