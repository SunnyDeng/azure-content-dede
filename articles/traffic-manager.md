<properties
   pageTitle="Traffic Manager - Übersicht"
   description="Der vorliegende Artikel bietet eine technische Übersicht über Traffic Manager."
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/23/2015"
   ms.author="joaoma" />

# Traffic Manager - Übersicht

Mit Microsoft Azure Traffic Manager können Sie die Verteilung des Benutzerdatenverkehrs an Ihre angegebenen Endpunkte steuern, darunter z. B. Azure Cloud Services, Websites und andere Endpunkte. Die Funktionsweise von Traffic Manager basiert darauf, dass Sie ein intelligentes Richtlinienmodul auf DNS-Abfragen (Domain Name System) von Domänennamen Ihrer Internetressourcen anwenden. Die Azure Cloud Services oder Websites können in verschiedenen Datencentern auf der ganzen Welt ausgeführt werden.

Traffic Manager unterstützt Sie bei folgenden Aufgaben:

-**Verbessern der Verfügbarkeit kritischer Anwendungen:** Mit Traffic Manager erhöhen Sie die Verfügbarkeit kritischer Anwendungen, da Ihre Endpunkte in Azure überwacht werden und automatische Failoverfunktionen beim Ausfall eines Azure Cloud Services, einer Azure-Website oder eines anderen Standorts sofort greifen.

-**Verbessern der Reaktionsfähigkeit hochleistungsfähiger Anwendungen:** Azure ermöglicht die Ausführung von Cloud-Diensten oder Websites in Datencentern auf der ganzen Welt. Traffic Manager sorgt für reaktionsschnellere Anwendungen und verbessert die Inhaltsübermittlung, indem Endbenutzer vom Client an den Endpunkt mit der niedrigsten Netzwerklatenz weitergeleitet werden.

-**Ausführen von Upgrades oder Dienstwartungsarbeiten ohne Ausfallzeiten:** Manager unterstützt erweiterte Szenarien für Hybridcloud und lokale Bereitstellungen, einschließlich der Szenarien "Erweiterung in die Cloud", "Migration in die Cloud" und "Failover in die Cloud". Für eine geplante Wartung deaktivieren Sie den Endpunkt in Traffic Manager und warten dann, bis der Endpunkt das Bedienen der vorhandenen Verbindungen abgeschlossen hat. Sobald der Endpunkt keinen weiteren Datenverkehr empfängt, können Sie den Dienst auf diesem Endpunkt aktualisieren, testen und den Dienst anschließend in Traffic Manager erneut aktivieren. Auf diese Weise können Sie ohne Ausfallzeiten für Clients Wartungsmaßnahmen oder Upgrades für die Dienste ausführen.

-**Verteilen des Datenverkehrs für große, komplexe Bereitstellungen:** Bei geschachtelten Traffic Manager-Profilen, die ein anderes Traffic Manager-Profil als Endpunkt umfassen können, können Sie Konfigurationen zur Optimierung von Leistung und Verteilung für größere, komplexere Bereitstellungen erstellen. Weitere Informationen finden Sie unter [Geschachtelte Profile](#Nested).

## Funktionsweise von Traffic Manager

Beim Konfigurieren eines Traffic Manager-Profils legen Sie verschiedene Einstellungen fest. Anhand dieser Einstellungen kann Traffic Manager entscheiden, welcher Endpunkt basierend auf einer DNS-Abfrage zur Verarbeitung einer Anforderung ausgewählt werden soll. Der eigentliche Endpunktdatenverkehr für den Dienst wird nicht über Traffic Manager geroutet.

*Abbildung 1* zeigt, wie Traffic Manager Benutzer an einen Endpunkt aus einem Satz von Endpunkten weiterleitet. Die Zahlen in Abbildung 1 entsprechen den nummerierten Beschreibungen unten:

![Funktionsweise von Traffic Manager](./media/traffic-manager/IC740854.jpg)

Abbildung 1

1. **Benutzerdatenverkehr an den Domänennamen des Unternehmens:** Der Client fordert Informationen mithilfe des Domänennamens des Unternehmens an. Das Ziel besteht darin, einen DNS-Namen in eine IP-Adresse aufzulösen. Unternehmensdomänen müssen über die normale Registrierung für Internetdomänennamen reserviert werden, die außerhalb von Traffic Manager verwaltet werden. In Abbildung 1 lautet die Domäne des Beispielunternehmens "www.contoso.com".
1. **Zuordnen des Namens der Unternehmensdomäne zum Traffic Manager-Domänennamen:** Der DNS-Ressourceneintrag für die Unternehmensdomäne verweist auf einen Traffic Manager-Domänennamen, der in Azure Traffic Manager verwaltet wird. Dies wird über einen CNAME-Ressourceneintrag erreicht, mit dem der Namen der Unternehmensdomäne dem Traffic Manager-Domänennamen zugeordnet wird. Im vorliegenden Beispiel lautet der Traffic Manager-Domänenname "contoso.trafficmanager.net".
1. **Traffic Manager-Domänenname und -Profil:** Der Traffic Manager-Domänenname ist Bestandteil des Traffic Manager-Profils. Der DNS-Server des Benutzers sendet eine neue DNS-Abfrage für den Traffic Manager-Domänennamen (in diesem Beispiel "contoso.trafficmanager.net"), die von den DNS-Namenservern von Traffic Manager empfangen wird.
1. **Verarbeiten der Traffic Manager-Profilregeln:** Traffic Manager verwendet die ausgewählte Lastenausgleichsmethode sowie den zugehörigen Überwachungsstatus, um zu bestimmen, welcher Azure- oder anderer Endpunkt die Anforderung verarbeiten soll.
1. **Senden des Endpunktdomänennamens an den Benutzer:** Traffic Manager gibt einen CNAME-Eintrag zurück, der den Traffic Manager-Domänennamen dem Domänennamen des Endpunkts zuordnet. Der DNS-Server des Benutzers löst dann den Domänennamen des Endpunkts in die zugehörige IP-Adresse auf und sendet diese an den Benutzer.
1. **Aufrufen des Endpunkts durch den Benutzer:** Der Benutzer ruft den zurückgegebenen Endpunkt mithilfe der zugehörigen IP-Adresse direkt auf.

Da der Domänenname des Unternehmens und die aufgelöste IP-Adresse auf dem Clientcomputer zwischengespeichert werden, kann der Benutzer so lange mit dem ausgewählten Endpunkt interagieren, bis der lokale DNS-Cacheeintrag abläuft. Beachten Sie, dass die DNS-Hosteinträge bis zum Ablauf der Gültigkeitsdauer vom DNS-Client zwischengespeichert werden. Wenn Hosteinträge aus dem DNS-Clientcache abgerufen werden, wird das Traffic Manager-Profil umgangen. Es können Verbindungsverzögerungen auftreten, wenn der Endpunkt vor Ablauf der Gültigkeitsdauer nicht mehr verfügbar ist. Wenn die Gültigkeitsdauer eines DNS-Hosteintrags im Cache abläuft und der Clientcomputer den Domänennamen des Unternehmens erneut auflösen muss, sendet er eine neue DNS-Abfrage. Der Clientcomputer empfängt möglicherweise die IP-Adresse eines anderen Endpunkts. Dies hängt davon ab, welche Lastenausgleichsmethode verwendet wird und welche Integrität die Endpunkte zum Zeitpunkt der Anforderung aufweisen.

## Implementieren von Traffic Manager


*Abbildung 2* zeigt die zur Implementierung von Traffic Manager erforderlichen Schritte in der Reihenfolge ihrer Ausführung an. Diese Schritte können in leicht abweichender Reihenfolge ausgeführt werden, wenn Sie mit der Traffic Manager-Konfiguration und den bewährten Methoden umfassend vertraut sind. Die Zahlen in Abbildung 2 entsprechen den nummerierten Beschreibungen unten:

![Konfigurieren von Traffic Manager](./media/traffic-manager/IC740855.jpg)

Abbildung 2

1. **Stellen Sie Ihre Azure Cloud Services, Azure-Websites oder anderen Endpunkte in der Produktionsumgebung bereit.** Wenn Sie ein Traffic Manager-Profil erstellen, muss dieses einem Abonnement zugeordnet werden. Anschließend fügen Sie Endpunkte für Cloud-Dienste und Websites der Standardebene in der Produktion hinzu, die zum selben Abonnement gehören. Befindet sich ein Endpunkt im Stagingmodus und gehört er weder zu einer Azure-Produktionsumgebung noch zum selben Abonnement, kann er als externer Endpunkt hinzugefügt werden. Weitere Informationen zu Cloud Services finden Sie unter [Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074). Weitere Informationen zu Websites finden Sie unter [Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327).
1. **Legen Sie einen Namen für die Traffic Manager-Domäne fest.** Weisen Sie der Domäne einen Namen mit einem eindeutigen Präfix zu. Der abschließende Teil des Domänennamens,"trafficmanager.net", kann nicht geändert werden. Weitere Informationen finden Sie unter [Empfohlene Methoden](#bkmk_TrafficManagerBestPracticesProfile).
1. **Wählen Sie die Überwachungskonfiguration, die Sie verwenden möchten.** Traffic Manager überwacht Endpunkte, um sicherzustellen, dass diese unabhängig von der Lastenausgleichsmethode online sind. Nachdem Sie die Überwachungseinstellungen konfiguriert haben, sendet Traffic Manager keinen Datenverkehr an Endpunkte, die laut Überwachungssystem offline sind. Dies gilt nicht, wenn erkannt wird, dass alle Endpunkte offline sind, oder wenn der Status eines der im Profil enthaltenen Endpunkte nicht erkannt wird. Weitere Informationen zur Überwachung finden Sie unter [Informationen zur Traffic Manager-Überwachung](https://msdn.microsoft.com/library/azure/dn339013.aspx).
1. **Entscheiden Sie, welche Lastenausgleichsmethode Sie verwenden möchten.** Es stehen drei verschiedene Lastenausgleichsmethoden zur Verfügung. Nehmen Sie sich genügend Zeit, um zu verstehen, welche Methode Ihren Anforderungen am besten gerecht wird. Die Methode kann später jederzeit geändert werden. Beachten Sie außerdem, dass sich die Konfigurationsschritte für die drei Methoden geringfügig unterscheiden. Weitere Informationen zu Lastenausgleichsmethoden finden Sie unter [Traffic Manager-Lastenausgleichsmethoden](https://msdn.microsoft.com/library/azure/dn339010.aspx).
1. **Erstellen Sie Ihr Profil, und konfigurieren Sie die Einstellungen.** Sie können zum Erstellen Ihres Traffic Manager-Profils oder zum Konfigurieren der Einstellungen REST-APIs, Windows PowerShell oder das Verwaltungsportal verwenden. Weitere Informationen finden Sie unter [Konfigurieren von Traffic Manager-Einstellungen](#Configure). In den folgenden Schritten wird davon ausgegangen, dass Sie die Schnellerfassung im Verwaltungsportal verwenden.
	- 	**Erstellen des Traffic Manager-Profils -** Weitere Informationen darüber, wie Sie ein Profil mithilfe der Schnellerfassung im Verwaltungsportal erstellen, finden Sie unter [Erstellen eines Traffic Manager-Profils mithilfe von "Schnellerfassung"](https://msdn.microsoft.com/library/azure/dn339012.aspx).
	- 	**Konfigurieren von Einstellungen für die Lastenausgleichsmethode -** Bei der Schnellerfassung müssen Sie die Lastenausgleichsmethode für das Profil auswählen. Diese Einstellung kann jederzeit geändert werden, nachdem Sie die Schritte für die Schnellerfassung ausgeführt haben. Die Konfigurationsschritte können Sie dem Thema entnehmen, in dem die von Ihnen gewählte Lastenausgleichsmethode beschrieben wird: [Konfigurieren der Lastenausgleichsmethode "Leistung"](https://msdn.microsoft.com/library/azure/hh744835.aspx)
	
	[AZURE.NOTE]**Die Lastenausgleichsmethode "Roundrobin" unterstützt jetzt auch die gewichtete Verteilung von Netzwerkdatenverkehr. Zurzeit müssen Sie zum Konfigurieren von Gewichtung jedoch die REST-APIs oder Windows PowerShell verwenden. Weitere Informationen und ein Konfigurationsbeispiel finden Sie unter [Azure Traffic Manager: Externe Endpunkte und gewichtetes Roundrobin über PowerShell](https://msdn.microsoft.com/library/azure/hh744829.aspx) (in englischer Sprache) im Azure-Blog.**
	- 	**Konfigurieren von Endpunkten -** Endpunkte werden während der Schnellerfassung nicht konfiguriert. Nachdem Sie das Profil erstellt und die Lastenausgleichsmethode angegeben haben, müssen Sie dem Traffic Manager Informationen zu den Endpunkten bereitstellen.
	- 	**Konfigurieren von Überwachungseinstellungen -** Überwachungseinstellungen werden während der Schnellerfassung nicht konfiguriert. Nachdem Sie das Profil erstellt und die Lastenausgleichsmethode angegeben haben, müssen Sie dem Traffic Manager mitteilen, welche Objekte überwacht werden sollen. Die zur Konfiguration der Überwachung erforderlichen Schritte finden Sie unter [Konfigurieren der Traffic Manager-Überwachung](configure-traffic-manager-monitoring.md).
1. **Testen Sie Ihr Traffic Manager-Profil.** Testen Sie Profil und Domäne, um sicherzustellen, dass diese wie erwartet funktionieren. Weitere Informationen zu diesem Thema finden Sie unter [Testen der Traffic Manager-Einstellungen](testing-traffic-manager-settings.md).
1. **Verweisen Sie im DNS-Ressourceneintrag für den Domänennamen Ihres Unternehmens auf das Profil, um es zu aktivieren.** Weitere Informationen finden Sie unter [Verweisen auf eine Traffic Manager-Domäne mit der Internetdomäne eines Unternehmens](point-a-company-internet-domain-to-a-traffic-manager-domain.md).

Mithilfe des Beispiels in Abbildung 1 können Sie z. B. den DNS-Ressourceneintrag auf Ihren Servern ändern, damit der Domänenname des Unternehmens auf den Traffic Manager-Domänennamen verweist:

    www.contoso.com IN CNAME contoso.trafficmanager.net


## Konfigurieren von Traffic Manager-Einstellungen


Sie können Traffic Manager-Einstellungen im Verwaltungsportal, mithilfe der REST-APIs und mit Windows PowerShell-Cmdlets konfigurieren.

Obwohl nicht jedes REST-API-Element im Verwaltungsportal sichtbar ist, können viele Einstellungen mit beiden Methoden konfiguriert werden. Weitere Informationen zur Verwendung von REST-APIs finden Sie unter [Vorgänge in Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/p/?LinkId=313584).


Weitere Informationen zu Windows PowerShell-Cmdlets für Traffic Manager finden Sie unter [Azure Traffic Manager-Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769).


[AZURE.NOTE] **Zurzeit wird das Konfigurieren von externen Endpunkten (Typ = "Any"), von Gewichtungen für die Lastenausgleichsmethode "Roundrobin" sowie von geschachtelten Profilen mit dem Verwaltungsportal nicht unterstützt. Hierzu müssen Sie entweder REST (siehe "Definition erstellen") oder Windows PowerShell verwenden (siehe "Add-AzureTrafficManagerEndpoint").**

### Konfigurieren von Einstellungen im Verwaltungsportal


Im Verwaltungsportal können Sie Ihr Traffic Manager-Profil mithilfe der Schnellerfassung erstellen. Über die Schnellerfassung erstellen Sie ein Basisprofil. Nachdem Sie Ihr Profil erstellt haben, können Sie zusätzliche Einstellungen konfigurieren oder die zuvor konfigurierten Einstellungen bearbeiten. Weitere Informationen zum Erstellen des Traffic Manager-Profils mithilfe der Schnellerfassung finden Sie unter "Erstellen eines Traffic Manager-Profils mithilfe von 'Schnellerfassung'".

[Erstellen eines Traffic Manager-Profils mithilfe von "Schnellerfassung"](https://msdn.microsoft.com/library/azure/dn339012.aspx)

Sie können die folgenden Einstellungen im Verwaltungsportal konfigurieren:

-**DNS-Präfix -** Ein eindeutiges, von Ihnen erstelltes Präfix. Profile werden im Verwaltungsportal nach Präfix angezeigt.

-**DNS-Gültigkeitsdauer (TTL) -** Der Wert für die DNS-Gültigkeitsdauer bestimmt, wie häufig der lokale Cachenamenserver des Clients aktualisierte DNS-Einträge aus dem DNS-System von Azure Traffic Manager abfragt.

-**Abonnement -** Wählen Sie das Abonnement aus, das Ihrem Profil zugeordnet ist. Beachten Sie, dass diese Option nur angezeigt wird, wenn Sie über mehrere Abonnements verfügen.

-**Lastenausgleichsmethode -** Die Methode, die Traffic Manager für den Lastenausgleich verwendet.

-**Failoverreihenfolge -** Wenn Sie die Lastenausgleichsmethode "Failover" verwenden, ist dies die Reihenfolge der Endpunkte.

-**Überwachung -** Die Einstellungen geben das Protokoll (HTTP oder HTTPS), den Port sowie den relativen Pfad und Dateinamen an.

### Konfigurieren von Einstellungen mithilfe von REST-APIs


Sie können das Traffic Manager-Profil mithilfe von REST-APIs erstellen und konfigurieren. Weitere Informationen finden Sie unter [Vorgänge in Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/?LinkId=313584).

-**Profil -** Ein Profil enthält ein von Ihnen erstelltes Domänennamenpräfix. Jedes Profil entspricht Ihrem Abonnement. Sie können mehrere Profile pro Abonnement erstellen. Der Profilname ist im Verwaltungsportal sichtbar. Der von Ihnen erstellte und im Profil enthaltene Name ist der Name Ihrer Traffic Manager-Domäne.

-**Definition -** Eine Definition enthält Richtlinien- und Überwachungseinstellungen. Eine Definition gehört zu einem Profil. Es kann nur eine Definition pro Profil vorhanden sein. Die Definition selbst ist nicht im Verwaltungsportal sichtbar, obwohl viele der in der Definition enthaltenen Einstellungen angezeigt und im Verwaltungsportal konfiguriert werden können.

-**DNS-Optionen -** Jede Definition umfasst DNS-Optionen. Mit diesen Optionen wird die DNS-Gültigkeitsdauer konfiguriert.

-**Überwachung -** Jede Definition umfasst Überwachungseinstellungen. Mit diesen Einstellungen werden Protokoll, Port, relativer Pfad und Dateiname konfiguriert. Überwachungseinstellungen sind sichtbar und können im Verwaltungsportal konfiguriert werden. Weitere Informationen finden Sie unter [Informationen zur Traffic Manager-Überwachung](https://msdn.microsoft.com/library/azure/dn339013.aspx).

-**Richtlinie -** Jede Definition umfasst Richtlinieneinstellungen. Mithilfe der Richtlinie werden Lastenausgleichsmethoden und Endpunkte definiert. Die Richtlinie selbst ist nicht im Verwaltungsportal sichtbar, obwohl viele der in der Richtlinie enthaltenen Einstellungen angezeigt und im Verwaltungsportal konfiguriert werden können. Weitere Informationen finden Sie unter [Traffic Manager-Lastenausgleichsmethoden](about-traffic-manager-balancing-methods.md).

## Konfigurieren von Einstellungen mithilfe von Windows PowerShell

Sie können Ihr Traffic Manager-Profil mithilfe von Windows PowerShell erstellen und konfigurieren. Weitere Informationen finden Sie unter [Azure Traffic Manager-Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769).

## <a name=bkmk_TrafficManagerBestPracticesProfile>Best practices</a>

- **Verwenden Sie eindeutige und leicht verständliche Präfixe -** Der DNS-Name Ihres Traffic Manager-Profils muss eindeutig sein. Sie können nur den ersten Teil des DNS-Namens selbst festlegen. Der Traffic Manager-Domänenname wird ausschließlich zur Identifizierung und Weiterleitung von Clientanforderungen verwendet. Auf Clientcomputern sind die Namen für Endbenutzer nicht sichtbar. Allerdings werden Profile anhand dieses Domänennamens identifiziert. Deshalb ist es wichtig, dass Sie diesen Domänennamen problemlos von anderen im Verwaltungsportal aufgelisteten Domänennamen unterscheiden können.
- **Verwenden Sie Punkte, um eindeutige und leicht lesbare Domänennamen zu erstellen -** Sie können auch Teile Ihres Domänennamenpräfix mithilfe von Punkten abtrennen.  Wenn Sie planen, mehrere Richtlinien in Traffic Manager zu erstellen, erleichtert eine konsistente Hierarchie die Unterscheidung der einzelnen Dienste voneinander. Beispielsweise verfügt Contoso über globale Dienste für die Web-, Abrechnungs- und Hilfsprogrammverwaltung. Die drei zugehörigen Richtlinien würden "web.contoso.trafficmanager.net", "bill.contoso.trafficmanager.net" und "util.contoso.trafficmanager.net" lauten. Verwenden Sie beim Einrichten von Cloud-Diensten oder Websites Namen, die den Standort enthalten. Verwenden Sie beispielsweise "web-us-contoso.cloudapp.net" und "web-asia-contoso.cloudapp.net". Es gelten Einschränkungen aufgrund von DNS. Ein Domänenname ist im Prinzip eine Sequenz von Bezeichnungen, die durch Punkte voneinander getrennt sind (Bezeichnung.Bezeichnung.Bezeichnung.Bezeichnung.usw.). Zum Zeitpunkt der Erstellung dieser Dokumentation gab es folgende Einschränkungen für Domänennamen in Traffic Manager:
	- Jede Bezeichnung kann maximal 63 Zeichen umfassen.
	- Es können insgesamt nicht mehr als 40 Bezeichnungen verwendet werden. Da zwei Bezeichnungen durch "trafficmanager.net" belegt sind, können Sie 38 Bezeichnungen für Ihr Präfix verwenden.
	- Der gesamte Domänenname kann maximal 253 Zeichen umfassen. Beachten Sie, dass hiervon 19 Zeichen durch "trafficmanager.net" beansprucht werden.
- **DNS-Gültigkeitsdauer -** Der Wert für die DNS-Gültigkeitsdauer bestimmt, wie häufig der lokale Cachenamenserver des Clients aktualisierte DNS-Einträge aus dem DNS-System von Azure Traffic Manager abfragt. Für jede Änderung in Traffic Manager, beispielsweise eine Profiländerung oder Änderungen bezüglich der Verfügbarkeit von Endpunkten, wird dieser Zeitraum zur Aktualisierung im globalen System der DNS-Server benötigt. Es wird empfohlen, den Standardwert von 300 Sekunden (5 Minuten) für diese Einstellung beizubehalten. Ein höherer Wert verlängert den Zeitraum, für den Traffic Manager-DNS-Antworten von DNS-Resolvern und -Clients zwischengespeichert werden, und verringert so die Latenz von DNS-Abfragen insgesamt. Wenn aber ein Failover sehr schnell erfolgen muss, empfiehlt sich die Konfiguration eines niedrigeren Werts.
- **Endpunkte sollten sich in einem einzigen Abonnement befinden -** Alle Endpunkte sollten zu dem Abonnement gehören, für das Sie das Profil erstellen. Sie können einem Profil Endpunkte aus verschiedenen Abonnements als externe Endpunkte hinzufügen. Azure entfernt diese jedoch nicht automatisch, wenn Sie den zugehörigen Dienst deaktivieren oder löschen. Externe Endpunkte verbleiben daher im Traffic Manager-Profil, und es fallen weiterhin Gebühren an, wenn Sie diese Endpunkte nicht manuell entfernen.

-**Nur Produktionsdienste -** Es stehen nur Endpunkte zur Verfügung, die sich in einer Produktionsumgebung befinden. Eine Weiterleitung an Endpunkte in einer Stagingumgebung ist nicht möglich. Wenn Sie einen virtuellen IP-Adressaustausch (VIP-Austausch) durchführen, während Datenverkehr von einem Profil weitergeleitet wird, wird für den Datenverkehr der getauschte Endpunkt in der Produktionsumgebung verwendet.

-**Geben Sie Ihren Endpunkten leicht identifizierbare Namen -** Verwenden Sie ein geeignetes DNS-Präfix. Der DNS-Name wird verwendet, da dieser in einem Abonnement in jedem Fall eindeutig ist, während dies beim Namen des Cloud-Diensts oder der Website möglicherweise nicht der Fall ist. Um Verwechslungen zu vermeiden, sollten Sie einem Cloud-Dienst oder einer Website einen Namen und ein DNS-Präfix geben, das identisch oder ähnlich ist. Wenn Sie über mehr als 20 Cloud-Dienste und Websites verfügen, kann eine nicht eindeutige Benennung zu Problemen beim Identifizieren des richtigen Endpunkts führen. Darüber hinaus wird durch nicht eindeutig benannte Endpunkte das Verwalten von Profilen erschwert.

-**Alle Endpunkte in einem Profil sollten dieselben Vorgänge und Ports verwenden -** Wenn Sie Ihre Endpunkte mischen, steigt das Risiko, dass ein Client einen Endpunkt aufruft, der die Anforderung nicht verarbeiten kann.

-**Alle Cloud-Dienste in einem Profil müssen dieselben Überwachungseinstellungen verwenden -** Sie können nur einen einzelnen Pfad und eine einzelne Datei zur Überwachung aller Endpunkte in einer bestimmten Definition auswählen. Sie können im Textfeld für den relativen Pfad und Dateinamen den Wert "/" eingeben, damit die Überwachung auf den Standardpfad und -dateinamen zugreift.

-**Deaktivieren Sie Endpunkte für temporäre Änderungen, anstatt die Konfiguration zu ändern -** Es kann häufiger vorkommen, dass Sie einen Endpunkt offline schalten möchten. Statt den Endpunkt aus dem Profil zu entfernen, deaktivieren Sie einfach den betreffenden Endpunkt in Ihrem Profil. Auf diese Weise bleibt der Endpunkt Bestandteil des Profils, aber das Profil verhält sich, als wäre der Endpunkt nicht darin enthalten. Dies ist nützlich, wenn Sie einen Endpunkt temporär entfernen möchten, beispielsweise, weil dieser sich im Wartungsmodus befindet oder erneut bereitgestellt wird. Sobald der Endpunkt wieder betriebsbereit ist, können Sie ihn wieder aktivieren. Weitere Informationen finden Sie unter [Deaktivieren oder Aktivieren von Endpunkten](disable-or-enable-an-endpoint.md).

-**Speicher -** Standort und Verteilung des Speichers sind wichtige Überlegungen bei der Verwendung von Traffic Manager. Berücksichtigen Sie bei Entwurf und Bereitstellung von Anwendungen für Traffic Manager die End-to-End-Transaktion und den Datenfluss.

-**SQL Azure -** Analog zum Entwurf des Speichers ist eine Analyse von Anwendungszustand und Datenanforderungen erforderlich, wenn Sie Ihre Endpunkte auf mehrere geografische Regionen ausweiten.

## <a name="Nested">Geschachtelte Profile</a>

Sie können den Namen eines anderen Traffic Manager-Profils als Endpunkt angeben, sodass ein so genanntes geschachteltes Profil entsteht. Der Name des Traffic Manager-Profils entspricht dem zugehörigen DNS-Namen, beispielsweise "contoso-europe.trafficmanager.net".

Dies ermöglicht es Ihnen, Traffic Manager so zu konfigurieren, dass eingehende DNS-Namensabfragen auf mehreren Ebenen analysiert werden. Auf diese Weise wird sichergestellt, dass der anfordernde Client zur richtigen Gruppe von Endpunkten weitergeleitet wird. Abbildung 3 zeigt ein Beispiel.

![Beispiel für geschachtelte Traffic Manager-Profile](./media/traffic-manager/IC751072.png)

**Abbildung 3**

Sie können bis zu 10 Ebenen schachteln, und jedes Profil kann mit einer anderen Lastenausgleichsmethode konfiguriert werden.

Beispielsweise könnten Sie eine Konfiguration für das folgende Szenario erstellen:

- Auf der obersten Ebene (Traffic Manager-Profil, das Ihrem externen DNS-Namen zugeordnet ist) konfigurieren Sie das Profil mit der Lastenausgleichsmethode "Leistung".

- Auf der mittleren Ebene repräsentieren mehrere Traffic Manager-Profile unterschiedlichen Datencenter; für diese Profile wird die Lastenausgleichsmethode "Roundrobin" verwendet.

- Auf der untersten Ebene werden die Datenverkehrsanforderungen des Benutzers von den Cloud-Dienst-Endpunkten des jeweiligen Datencenters verarbeitet.

Dies führt zu dem Ergebnis, dass Benutzer basierend auf der Leistung zu einem regional geeigneten Rechenzentrum und in diesem Rechenzentrum basierend auf einer gleichen oder gewichteten Lastenverteilung an einen Cloud-Dienst weitergeleitet werden. Mithilfe der Gewichtung könnte beispielsweise ein kleiner Prozentsatz des Datenverkehrs zu Testzwecken oder für Kundenfeedback an eine neue oder an eine Testbereitstellung weitergeleitet werden.

Diese Konfiguration wird in Abbildung 4 gezeigt.

![Beispiel für mehrschichtige Traffic Manager-Profile](./media/traffic-manager/IC751073.png)

**Abbildung 4**

In Abbildung 4 ist das Traffic Manager-Profil der ersten Ebene ein übergeordnetes Profil, und die Traffic Manager-Profile der mittleren sind untergeordnete Profile.

Wenn Traffic Manager Benutzer an ein untergeordnetes Profil weiterleitet, das nur wenige einsatzbereite Endpunkte umfasst, werden diese Endpunkte möglicherweise überlastet, sodass Leistungsprobleme auftreten können. Um diese Situation zu verhindern, können Sie das übergeordnete Traffic Manager-Profil mit einem Schwellenwert für einsatzbereite Endpunkte konfigurieren, der festlegt, dass eine bestimmte Anzahl von Endpunkten in den untergeordneten Profilen Datenverkehr empfangen können. Wenn Sie beispielsweise sicherstellen möchten, dass mindestens drei einsatzbereite Endpunkte in den untergeordneten Profilen vorhanden sind, legen Sie diesen Schwellenwert auf 3 fest. Im Beispiel aus Abbildung 4 würden Sie das Traffic Manager-Profil der obersten Ebene mit diesem Schwellenwert konfigurieren.

Wenn Sie ein Traffic Manager-Profil als Endpunkt hinzufügen und die Mindestanzahl von einsatzbereiten Endpunkten konfigurieren möchten, müssen Sie entweder REST (siehe [Definition erstellen](http://go.microsoft.com/fwlink/p/?LinkId=400772)) oder Windows PowerShell (siehe [Add-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/azure/dn690257.aspx)) verwenden. Diese Aufgabe kann nicht im Verwaltungsportal ausgeführt werden.

## Traffic Manager-Abbildungen


Wenn Sie die Abbildungen in diesem Thema als PowerPoint-Folien für Ihre eigene Präsentation zu Traffic Manager verwenden oder zu eigenen Zwecken ändern möchten, finden Sie diese unter [Traffic Manager-Abbildungen in der MSDN-Dokumentation](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99).

## Siehe auch

[Traffic Manager-Konfigurationsaufgaben](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Cloud Services](http://go.microsoft.com/fwlink/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Vorgänge in Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/?LinkId=313584)

<!--HONumber=49-->