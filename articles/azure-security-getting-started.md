<properties
   pageTitle="Erste Schritte mit Microsoft Azure-Sicherheit | Microsoft Azure"
   description="Dieser Artikel bietet einen Überblick über die Sicherheitsfunktionen von Microsoft Azure sowie allgemeine Informationen für Organisationen, die ihre Ressourcen zu einem Cloudanbieter migrieren."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="yuridio"/>

#Erste Schritte mit Microsoft Azure-Sicherheit

Beim Erstellen oder Migrieren von IT-Ressourcen zu einem Cloudanbieter verlassen Sie sich darauf, dass die jeweilige Organisation die ihren Diensten anvertrauten Anwendungen und Daten optimal schützt. Außerdem verlassen Sie sich auf die Sicherheitskontrollfunktionen, die Ihnen zum Steuern der Sicherheit Ihrer cloudbasierten Ressourcen bereitgestellt werden.

Die Infrastruktur von Azure ist von den Hardwareressourcen bis hin zu den Anwendungen vollständig auf das gleichzeitige Hosten von Millionen von Kunden ausgelegt und stellt für Unternehmen eine vertrauenswürdige Grundlage zur Erfüllung ihrer Sicherheitsanforderungen dar. Darüber hinaus bietet Azure Ihnen eine breite Auswahl von konfigurierbaren Sicherheitsoptionen, die Sie selbst steuern können, sodass Sie die Sicherheit an die individuellen Anforderungen Ihrer Bereitstellungen anpassen können.

In diesem allgemeinen Artikel zur Sicherheit von Azure werden folgende Themen behandelt:

-   Azure-Dienste und -Features, die Sie beim Sichern Ihrer Dienste und Daten innerhalb von Azure unterstützen

-   Die Absicherung der Azure-Infrastruktur durch Microsoft zum Schutz Ihrer Daten und Anwendungen

##Identitäts- und Zugriffsverwaltung


Das Steuern des Zugriffs auf die IT-Infrastruktur, Daten und Anwendungen ist von größter Bedeutung. In Microsoft Azure werden diese Funktionen von Diensten wie Azure Active Directory und Azure Storage sowie durch die Unterstützung für zahlreiche Standards und APIs bereitgestellt.

[Azure Active Directory](active-directory-whatis.md) (Azure AD) ist ein Identitätsrepository und Modul, das Authentifizierung, Autorisierung und Zugriffssteuerung für die Benutzer, Gruppen und Objekte einer Organisation bietet. Azure AD stellt Entwicklern außerdem eine effektive Methode zur Verfügung, um die Identitätsverwaltung in ihre Anwendungen zu integrieren. Standardprotokolle wie [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx) und [OpenID Connect](http://openid.net/connect/) ermöglichen die Anmeldung an vielerlei Plattformen wie .NET, Java, Node.js und PHP.

Die REST-basierte Diagramm-API ermöglicht Entwicklern von jeder Plattform aus das Lesen und Schreiben im Verzeichnis. Durch die Unterstützung für [OAuth 2.0](http://oauth.net/2/) können Entwickler mobile und Webanwendungen, die in Web-APIs von Microsoft und Drittanbietern integriert sind, und eigene sichere Web-APIs erstellen. Open Source Client-Bibliotheken sind für .Net, Windows Store, iOS und Android verfügbar, weitere Bibliotheken werden aktuell entwickelt.

### Umsetzung der Identitäts- und Zugriffsverwaltung in Azure

Sie können Azure AD als eigenständiges Cloudverzeichnis für Ihre Organisation verwenden oder in Ihr vorhandenes lokales Active Directory integrieren. Einige Integrationsfeatures umfassen die Verzeichnissynchronisierung und einmaliges Anmelden (SSO). Diese Funktionen erweitern die Reichweite Ihrer vorhandenen lokalen Identitäten in die Cloud und verbessern die Benutzerfreundlichkeit für Administratoren und Benutzer.

Im Folgenden sind einige weitere Möglichkeiten zur Identitäts- und Zugriffsverwaltung aufgeführt:

-   Azure AD ermöglicht [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) für SaaS-Anwendungen, unabhängig davon, wo sie gehostet werden. Einige Anwendungen bilden einen Verbund mit Azure AD, andere verwenden Kennwort-SSO. Verbundanwendungen können auch die Benutzerbereitstellung und Kennworttresore unterstützen.

-   Zugriff auf Daten in [Azure Storage](https://azure.microsoft.com/services/storage/) wird über die Authentifizierung gesteuert. Jedes Storage-Konto verfügt über einen Primärschlüssel ([Speicherkontoschlüssel](https://msdn.microsoft.com/library/azure/ee460785.aspx)) und einen sekundären geheimen Schlüssel (die [Shared Access Signature](storage-dotnet-shared-access-signature-part-1.md) oder SAS).

-   Azure AD bietet Identity-as-a-Service durch Verbund (mit den [Active Directory-Verbunddiensten](fundamentals-identity.md), Synchronisierung und Replikation mit lokalen Verzeichnissen.

-   [Azure Multi-Factor Authentication (MFA)](multi-factor-authentication.md) ist der Dienst zur mehrstufigen Authentifizierung, der Benutzer zur Verifizierung der Anmeldung über eine mobile App, einen Telefonanruf oder eine Textnachricht auffordert. Er kann mit Azure AD verwendet werden, um lokale Ressourcen mit dem Azure MFA-Server zu sichern, sowie für benutzerdefinierte Anwendungen und Verzeichnisse, die das SDK verwenden.

-   Mit [Azure AD-Domänendiensten](https://azure.microsoft.com/services/active-directory-ds/) können Sie virtuelle Azure-Computer in eine Domäne einbinden, ohne Domänencontroller bereitstellen zu müssen. Benutzer können sich bei diesen virtuellen Computern mithilfe ihrer Active Directory-Unternehmensanmeldeinformationen anmelden und in die Domäne eingebundene virtuelle Computer anhand von Gruppenrichtlinien verwalten, um Sicherheitsbaselines für alle virtuellen Azure-Computer durchzusetzen.

-   [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) bietet Ihnen einen hoch verfügbaren, globalen Identitätsverwaltungsdienst für kundenorientierte Anwendungen, der für Hunderte Millionen von Identitäten skaliert werden kann. Er kann über mobile und Webplattformen integriert werden. Ihre Kunden können sich über eine anpassbare Oberfläche bei all Ihren Anwendungen anmelden und zu diesem Zweck entweder vorhandene Konten aus sozialen Netzwerken nutzen oder neue Anmeldeinformationen festlegen.

##Zugriffssteuerung und Verschlüsselung von Daten

Microsoft wendet die Grundsätze der Aufgabentrennung und der [geringstmöglichen Berechtigungen](https://en.wikipedia.org/wiki/Principle_of_least_privilege) in allen Azure-Vorgängen an. Für den Zugriff auf Daten benötigen Azure-Supportmitarbeiter Ihre explizite Erlaubnis, die auf „Just-in-Time“-Basis gewährt, protokolliert und überwacht und nach Abschluss der Aufgabe wieder entzogen wird.

Darüber hinaus bietet Azure mehrere Funktionen zum Schutz von Daten während der Übertragung und im Ruhezustand, einschließlich der Verschlüsselung für Daten, Dateien, Anwendungen, Dienste, Kommunikation und Laufwerke. Sie haben die Option zum Verschlüsseln von Informationen, bevor sie in Azure abgelegt werden, sowie zum Speichern von Schlüsseln in Ihren lokalen Datencentern.

![Microsoft-Antischadsoftware in Azure](./media/azure-security-getting-started\sec-azgsfig1.PNG)

### Azure-Verschlüsselungstechniken

Mit der [Azure AD-Berichterstellung](active-directory-reporting-audit-events.md) können Sie Details zum Administratorzugriff auf Ihre Abonnementumgebung sammeln. Sie haben die Möglichkeit zum Konfigurieren der [BitLocker-Laufwerkverschlüsselung](https://technet.microsoft.com/library/cc732774.aspx) auf virtuellen Festplatten mit vertraulichen Informationen in Azure.

Weitere Möglichkeiten zum Schutz Ihrer Daten in Azure:

-   Anwendungsentwickler können die Verschlüsselung mithilfe von Windows [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) und .NET-Framework in die Anwendungen integrieren, die sie in Azure bereitstellen.

- Die clientseitige Verschlüsselung für Microsoft-Blobspeicher ermöglicht Ihnen eine vollständige Kontrolle der Schlüssel. Dem Speicherdienst werden die Schlüssel niemals offengelegt, sodass er die Daten nicht entschlüsseln kann.

-   [Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) (mit dem [RMS SDK](https://msdn.microsoft.com/library/dn758244.aspx)) bietet Verschlüsselung auf Datei- und Datenebene sowie Schutz vor Datenverlust durch die Zugriffsverwaltung anhand von Richtlinien.

-   Azure unterstützt die [Verschlüsselung auf Tabellen- und auf Spaltenebene (TDE/CLE)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) in SQL Server Virtual Machines und unterstützt lokale Drittanbieterserver zur Schlüsselverwaltung in den Datencentern der Kunden.

-   Speicherkontoschlüssel, Shared Access Signatures (SAS), Verwaltungszertifikate und andere Schlüssel sind für die einzelnen Azure-Mandanten eindeutig.

-   Der Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx)-Hybridspeicher verschlüsselt Daten über ein 128-Bit-Schlüsselpaar aus öffentlichem / privatem Schlüssel vor dem Hochladen in Azure Storage.

-   Azure unterstützt und verwendet zahlreiche Verschlüsselungsmechanismen, z. B. SSL/TLS, IPsec und AES, abhängig von den Datentypen, Containern und Transporten.

##Virtualisierung

Die Azure-Plattform verwendet eine virtualisierte Umgebung. Benutzerinstanzen arbeiten als eigenständige virtuelle Computer, die keinen Zugriff auf einen physischen Hostserver haben, und diese Isolierung wird anhand physischer [Prozessorberechtigungsebenen (Ring 0/Ring 3)](https://en.wikipedia.org/wiki/Protection_ring) erzwungen.

Ring 0 entspricht den höchsten Berechtigungen und Ring 3 den geringsten. Das Gastbetriebssystem wird in einem weniger privilegierten Ring 1 und Anwendungen im am wenigsten privilegierten Ring 3 ausgeführt. Die Virtualisierung physischer Ressourcen führt zu einer klaren Abgrenzung zwischen Gastbetriebssystem und Hypervisor, was eine zusätzliche Sicherheitstrennung zwischen den beiden bewirkt.

Der Hypervisor von Azure verhält sich wie ein Microkernel und leitet alle Hardwarezugriffsanforderungen von virtuellen Gastcomputern an den Host weiter, damit sie über eine Schnittstelle mit gemeinsam genutztem Speicherbereich namens VMBus verarbeitet werden. Dies verhindert, dass Benutzer RAW-Lese-, -Schreib- und -Ausführungszugriff auf das System erhalten, und verringert das Risiko der Freigabe von Systemressourcen.

![Microsoft-Antischadsoftware in Azure](./media/azure-security-getting-started\sec-azgsfig2.PNG)

### Implementierung der Virtualisierung in Azure

Azure verwendet eine Hypervisorfirewall (Paketfilter), die im Hypervisor implementiert und von einem Fabric Controller-Agent konfiguriert wird. So können Mandanten vor nicht autorisiertem Zugriff geschützt werden. Standardmäßig wird beim Erstellen eines virtuellen Computers sämtlicher Datenverkehr blockiert, und der Fabric Controller-Agent konfiguriert anschließend den Paketfilter, um *Regeln und Ausnahmen* für das Zulassen des autorisierten Datenverkehrs hinzuzufügen.

Es gibt zwei Kategorien von Regeln, die hier programmiert werden:

-   **Computerkonfiguration oder Infrastrukturregeln**: In der Standardeinstellung wird die gesamte Kommunikation blockiert. Es gibt Ausnahmen, um einem virtuellen Computer das Senden und Empfangen von DHCP- und DNS-Datenverkehr zu gestatten. Virtuelle Computer können auch Datenverkehr an das „öffentliche“ Internet und an andere virtuelle Computer im Cluster und auf dem Betriebssystem-Aktivierungsserver senden. Die zulässige Liste ausgehender Ziele der virtuellen Computer enthält weder Azure-Routersubnetze noch das Azure-Verwaltungs-Back-End und andere Microsoft-Eigenschaften.

-   **Rollenkonfigurationsdatei**: Diese definiert die eingehenden ACLs basierend auf dem Dienstmodell des Mandanten. Wenn ein Mandant beispielsweise über ein Web-Front-End an Port 80 auf einem bestimmten virtuellen Computer verfügt, öffnet Azure TCP-Port 80 für alle IP-Adressen, wenn Sie einen Endpunkt im [Azure Service Management](resource-manager-deployment-model.md)-Modell konfigurieren. Wenn der virtuelle Computer eine Back-End- oder Workerrolle ausführt, öffnen wir die Workerrolle nur für den virtuellen Computer innerhalb desselben Mandanten.

##Isolation

Um die nicht autorisierte und unbeabsichtigte Übertragung von Informationen zwischen Bereitstellungen in einer freigegebenen mehrinstanzenfähigen Architektur zu verhindern, ist die Trennung eine weitere wichtige Anforderung an die Cloudsicherheit.

Azure implementiert die [Netzwerkzugriffssteuerung](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) und die Trennung durch VLAN-Isolierung, ACLs, Lastenausgleichsmodule und IP-Filter. Externer, bei Ihren virtuellen Computern eingehender Datenverkehr wird auf Ports und Protokolle eingeschränkt, die Sie definieren. Die Netzwerkfilterung wird implementiert, um gefälschten Datenverkehr zu verhindern, und beschränkt den eingehenden und ausgehenden Datenverkehr auf vertrauenswürdige Plattformkomponenten. Richtlinien zum Datenverkehrsfluss werden auf Geräten zum Schutz der Netzwerkgrenzen implementiert, die den Datenverkehr standardmäßig verweigern.

![Microsoft-Antischadsoftware in Azure](./media/azure-security-getting-started\sec-azgsfig3.PNG)

Die Netzwerkadressenübersetzung wird zum Trennen des internen Netzwerkdatenverkehrs vom externen Datenverkehr eingesetzt. Der interne Datenverkehr kann nicht extern geroutet werden. [Virtuelle IP-Adressen](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx), die extern geroutet werden können, werden in [interne dynamische IP-Adressen](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) übersetzt, die nur innerhalb von Azure geroutet werden können.

Externer Datenverkehr an virtuelle Azure-Computer wird auf Routern, Lastenausgleichsmodulen und Schicht 3-Switches über die Zugriffssteuerungslisten (ACLs) durch eine Firewall geleitet. Nur bestimmte bekannte Protokolle sind zulässig. ACLs begrenzen den Datenverkehr von virtuellen Gastcomputern an andere VLANs, die zur Verwaltung verwendet werden. Darüber hinaus wird der Datenverkehr sowohl auf der Sicherungsschicht als auch auf der Vermittlungsschicht über IP-Filter auf dem Hostbetriebssystem weiter begrenzt.

### Implementierung der Isolation in Azure

Der Azure Fabric Controller ist verantwortlich für die Zuweisung von Infrastrukturressourcen zu Mandantenworkloads und verwaltet die unidirektionale Kommunikation vom Host zu den virtuellen Computern. Der Azure-Hypervisor erzwingt eine Speicher- und Prozesstrennung zwischen virtuellen Computern und routet Netzwerkdatenverkehr sicher zu den Gastbetriebssystem-Mandanten. Azure implementiert die Isolation auch für Mandanten, Speicher und virtuelle Netzwerke:

-   Jeder Azure AD-Mandant ist logisch über Sicherheitsgrenzen isoliert.

-   Azure-Speicherkonten sind für jedes Abonnement eindeutig, und der Zugriff muss mithilfe eines Speicherkontoschlüssels authentifiziert werden.

-   Virtual Networks sind logisch isoliert durch eine Kombination von eindeutigen privaten IP-Adressen, Firewalls und IP-ACLs. Lastenausgleichsmodule leiten Datenverkehr basierend auf Endpunktdefinitionen an die entsprechenden Mandanten weiter.

##Virtual Network und Firewall

Anhand der [verteilten und virtuellen Netzwerke](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) in Azure können Sie sicherstellen, dass Ihr privater Netzwerkdatenverkehr logisch vom Datenverkehr in anderen Azure Virtual Networks isoliert wird.

![Microsoft-Antischadsoftware in Azure](./media/azure-security-getting-started\sec-azgsfig4.PNG)

Ihr Abonnement kann mehrere isolierte private Netzwerke enthalten (und Firewall, Lastenausgleich und Netzwerkadressenübersetzung umfassen).

Azure bietet in jedem Azure-Cluster drei primäre Ebenen der Netzwerktrennung, um den Datenverkehr logisch aufzuteilen. [Virtuelle lokale Netzwerke](https://azure.microsoft.com/services/virtual-network/) (VLANs) werden verwendet, um Kundendatenverkehr vom übrigen Azure-Netzwerk zu trennen. Der Zugriff auf das Azure-Netzwerk von außerhalb des Clusters wird durch Lastenausgleichsmodule eingeschränkt.

Netzwerkdatenverkehr an und von virtuellen Computern muss den virtuellen Switch des Hypervisors durchlaufen. Die IP-Filterkomponente im Stammbetriebssystem isoliert den virtuellen Stammcomputer von den virtuellen Gastcomputern sowie die virtuellen Gastcomputern voneinander. Sie führt die Filterung des Datenverkehrs durch, um die Kommunikation zwischen den Knoten des Mandanten und dem öffentlichen Internet (basierend auf der Dienstkonfiguration des Kunden) einzuschränken, indem sie sie von anderen Mandanten trennt.

Der IP-Filter verhindert folgende Aktionen von virtuellen Gastcomputern:
 
- Generieren von gefälschtem Datenverkehr
 
- Empfangen von Datenverkehr, der nicht an sie adressiert ist

- Weiterleiten des Datenverkehrs an geschützte Infrastrukturendpunkte

- Senden oder Empfangen von unerwünschtem Broadcastdatenverkehr

Sie können Ihre virtuellen Computer in [Azure Virtual Networks](https://azure.microsoft.com/documentation/services/virtual-network/) platzieren. Diese virtuellen Netzwerke sind vergleichbar mit den Netzwerken, die Sie in einer lokalen Umgebung konfigurieren, in der sie normalerweise einem virtuellen Switch zugeordnet sind. Mit dem gleichen Azure Virtual Network verbundene virtuelle Computer können ohne zusätzliche Konfiguration miteinander kommunizieren. Sie haben auch die Möglichkeit, verschiedene Subnetze in Ihrem Azure Virtual Network zu konfigurieren.

Die folgenden Azure Virtual Network-Technologien können Sie einsetzen, um die Kommunikation in Ihrem Azure Virtual Network zu sichern:

-   [**Netzwerksicherheitsgruppen**](virtual-networks-nsg.md). Mit einer NSG können Sie eingehenden Datenverkehr für Instanzen virtueller Computer in Ihrem virtuellen Netzwerk steuern. Eine NSG enthält Regeln zur Zugriffssteuerung, die den Datenverkehr auf Grundlage der Richtung des Datenverkehrs, des Protokolls, der Quelladresse und des Quellports, und der Zieladresse und des Zielports zulässt oder verweigert.

-   [**Benutzerdefiniertes Routing**](virtual-networks-udr-overview.md). Sie können das Routing von Paketen über ein virtuelles Gerät steuern, indem Sie benutzerdefinierte Routen erstellen, die festlegen, dass der nächste Hop für in ein bestimmtes Subnetz gesendete Pakete an Ihr virtuelles Netzwerksicherheitsgerät erfolgen soll.

-   [**IP-Weiterleitung**](virtual-networks-udr-overview.md). Ein virtuelles Netzwerksicherheitsgerät muss eingehenden Datenverkehr empfangen können, der nicht an das Gerät selbst adressiert ist. Damit ein virtueller Computer an andere Ziele gerichteten Datenverkehr empfangen kann, aktivieren Sie für den virtuellen Computer die IP-Weiterleitung.

-   [**Tunnelerzwingung**](vpn-gateway-about-forced-tunneling.md). Über die Tunnelerzwingung können Sie die Umleitung des gesamten Internetdatenverkehrs, der von Ihren virtuellen Computern in einem Azure Virtual Network generiert wird, an Ihren lokalen Standort "erzwingen". Sie verwenden dazu einen Standort-zu-Standort-VPN-Tunnel für die Kontrolle und Überwachung.

-   [**Endpunkt**-ACLs](virtual-machines-set-up-endpoints.md). Sie können steuern, welchen Computern eingehende Verbindungen aus dem Internet an einen virtuellen Computer in Ihrem Azure Virtual Network gestattet werden, indem Sie Endpunkt-ACLs definieren.

-   [**Partnerlösungen zur Netzwerksicherheit**](https://azure.microsoft.com/marketplace/). Es gibt eine Reihe von Partnerlösungen zur Netzwerksicherheit, auf die Sie über den Azure Marketplace zugreifen können.

### Implementierung virtueller Netzwerke und der Firewall in Azure

Azure implementiert standardmäßig Firewalls zur Paketfilterung auf allen virtuellen Host- und Gastcomputern. Auch in Images des Windows-Betriebssystems aus dem Azure-Katalog ist die Windows-Firewall standardmäßig aktiviert. Lastenausgleichsmodule im Umkreis von öffentlich zugänglichen Azure-Netzwerken steuern die Kommunikation auf der Grundlage von IP-ACLs, die von Kundenadministratoren verwaltet werden.

Wenn Azure die Daten eines Kunden im Zuge des normalen Betriebs oder bei einem Notfall verschiebt, geschieht dies über private, verschlüsselte Kommunikationskanäle. Weitere von Azure genutzte Funktionen in virtuellen Netzwerken und Firewalls:

-   **Systemeigene Hostfirewall**: Azure Fabric und Storage werden in einem systemeigenen Betriebssystem ohne Hypervisor ausgeführt. Daher wird die Windows-Firewall mit den beiden oben genannten Regelsätzen konfiguriert. Storage wird zum Optimieren der Leistung systemeigen ausgeführt.

-   **Hostfirewall**: Die Hostfirewall dient zum Schutz des Hostbetriebssystems, auf dem der Hypervisor ausgeführt wird. Die Regeln werden so programmiert, dass nur dem Fabric Controller und Jumpboxes die Kommunikation mit dem Hostbetriebssystem über einen bestimmten Port gestattet wird. Die anderen Ausnahmen dienen zum Zulassen von DHCP-Antworten und DNS-Antworten. Azure verwendet eine Computerkonfigurationsdatei mit der Vorlage von Firewallregeln für das Hostbetriebssystem. Der Host selbst wird vor Angriffen von außen durch eine Windows-Firewall geschützt, die so konfiguriert ist, dass nur die Kommunikation von bekannten, authentifizierten Quellen zugelassen wird.

-   **Gastfirewall**: Repliziert die Regeln im VM-Switchpaketfilter, die jedoch in anderer Software programmiert sind (d. h. der Teil der Windows-Firewall im Gastbetriebssystem). Die Gast-VM-Firewall kann so konfiguriert werden, dass die Kommunikation mit dem virtuellen Gastcomputer auch dann eingeschränkt wird, wenn die Kommunikation durch Konfigurationen des Host-IP-Filters zulässig ist. Beispielsweise können Sie die Gast-VM-Firewall zum Einschränken der Kommunikation zwischen zwei VNets verwenden, die für das gegenseitige Herstellen einer Verbindung konfiguriert wurden.

-   **Speicherfirewall (FW)**: Die Firewall im Speicher-Front-End filtert den Datenverkehr, sodass er nur die Ports 80/443 und andere benötigte Hilfsprogrammports passieren kann. Die Firewall im Speicher-Back-End beschränkt die Kommunikation auf Datenverkehr von den Speicher-Front-End-Servern.

-   **Gateway von Virtual Network**: [Azure Virtual Network-Gateways](virtual-networks-configure-vnet-to-vnet-connection.md) dienen als standortübergreifende Gateways, die Ihre Workloads in Azure Virtual Network mit Ihren lokalen Standorten verbinden. Die Verbindung mit lokalen Standorten muss über [IPSec-Standort-zu-Standort-VPN-Tunnel](vpn-gateway-create-site-to-site-rm-powershell.md) oder über [ExpressRoute](expressroute-introduction.md)-Schaltkreise erfolgen. Für IPsec/IKE-VPN-Tunnel führen die Gateways IKE-Handshakes durch und richten die IPsec-S2S-VPN-Tunnel zwischen den Virtual Networks und lokalen Standorten ein. Virtual Network-Gateways beenden außerdem [Punkt-zu-Standort-VPNs](vpn-gateway-point-to-site-create.md).

##Sicherer Remotezugriff

In der Cloud gespeicherte Daten müssen ausreichende Schutzvorrichtungen aktiviert haben, um Exploits zu verhindern und bei der Übertragung die Vertraulichkeit und Integrität zu wahren. Dies schließt die Netzwerksteuerfunktionen ein, die mit den richtlinienbasierten, überprüfbaren Mechanismen zur Identitäts- und Zugriffsverwaltung einer Organisation einhergehen.

Die integrierte Kryptografietechnologie ermöglicht Ihnen das Verschlüsseln der Kommunikation innerhalb von und zwischen Bereitstellungen, zwischen Azure-Regionen und von Azure zu lokalen Datencentern. Der Administratorzugriff auf virtuelle Computer über [Remotedesktopsitzungen](virtual-machines-log-on-windows-server.md), [Remote-Windows PowerShell](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx) und das [Azure-Verwaltungsportal](https://azure.microsoft.com/overview/preview-portal/) wird immer verschlüsselt.

Um Ihr lokales Datencenter sicher auf die Cloud zu erweitern, bietet Azure sowohl [Standort-zu-Standort-VPN](vpn-gateway-create-site-to-site-rm-powershell.md) als auch [Punkt-zu-Standort-VPN](vpn-gateway-point-to-site-create.md) sowie dedizierte Verknüpfungen über [ExpressRoute](expressroute-introduction.md) (Verbindungen mit Azure Virtual Networks über VPN sind verschlüsselt).

### Implementierung des sicheren Remotezugriffs in Azure

Für Verbindungen mit dem Azure-Portal sind immer eine Authentifizierung und SSL/TLS erforderlich. Sie können Verwaltungszertifikate konfigurieren, um eine sichere Verwaltung zu ermöglichen. Sichere Protokolle nach Industriestandard, wie z. B. [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) und [IPsec](https://en.wikipedia.org/wiki/IPsec), werden vollständig unterstützt.

[Azure ExpressRoute](expressroute-introduction.md) ermöglicht Ihnen das Herstellen privater Verbindungen zwischen Azure-Datencentern und einer Infrastruktur, die sich bei Ihnen vor Ort oder in einer Kollokationsumgebung befindet. ExpressRoute-Verbindungen verlaufen nicht über das öffentliche Internet. Sie bieten mehr Zuverlässigkeit, schnellere Geschwindigkeiten, geringere Wartezeiten und größere Sicherheit als normale internetbasierte Links. In einigen Fällen können durch die Verwendung von ExpressRoute-Verbindungen zum Übertragen von Daten zwischen lokalen Standorten und Azure auch drastische Kosteneinsparungen erzielt werden.

##Protokollierung und Überwachung

Azure bietet eine authentifizierte Protokollierung sicherheitsrelevanter Ereignisse, die einen Audit-Trail generieren, und wurde so entwickelt, dass es gegen Manipulationen geschützt ist. Dies schließt die Systeminformationen ein, wie z. B. Sicherheitsereignisprotokolle in virtuellen Computern der Azure-Infrastruktur und in Azure AD. Die Überwachung von Sicherheitsereignissen umfasst das Sammeln von Ereignissen wie Änderungen an den IP-Adressen von DHCP- oder DNS-Servern, Zugriffsversuche auf Ports, per Voreinstellung blockierte Protokolle oder IP-Adressen, Änderungen an Sicherheitsrichtlinien oder Firewalleinstellungen, Konten- oder Gruppenerstellung, unerwartete Prozesse oder Treiberinstallationen.

![Microsoft-Antischadsoftware in Azure](./media/azure-security-getting-started\sec-azgsfig5.PNG)

Überwachungsprotokolle, in denen Zugriffe und Aktivitäten privilegierter Benutzer, autorisierte und nicht autorisierte Zugriffsversuche, Systemausnahmen und Informationsereignisse aufgezeichnet werden, werden für einen festgelegten Zeitraum aufbewahrt. Die Aufbewahrung von Protokollen liegt in Ihrem eigenen Ermessen, da Sie die Protokollerfassung und -aufbewahrung Ihren eigenen Anforderungen entsprechend konfigurieren.

### Implementierung der Protokollierung und Überwachung in Azure

Azure stellt Verwaltungs-Agents und ASM-Agents (Azure Security Monitor) für jeden verwalteten Compute-, Speicher- oder Fabric-Knoten bereit, unabhängig davon, ob es sich um systemeigene oder virtuelle Knoten handelt. Jeder Verwaltungs-Agent ist so konfiguriert, dass er sich mit einem Zertifikat aus dem Azure-Zertifikatspeicher bei einem Dienstteam-Speicherkonto authentifiziert und vorkonfigurierte Diagnose- und Ereignisdaten an das Speicherkonto weiterleitet. Diese Agents werden nicht auf virtuellen Kundencomputern bereitgestellt.

Azure-Administratoren greifen über ein Webportal auf Protokolle zu, um einen authentifizierten und kontrollierten Zugriff auf die Protokolle zu gewährleisten. Ein Administrator kann Protokolle filtern, korrelieren und analysieren. Die Azure-Dienstteam-Speicherkonten für Protokolle werden vor direktem Administratorzugriff geschützt, um Manipulationen des Protokolls zu verhindern.

Microsoft sammelt Protokolle von Netzwerkgeräten mithilfe des Syslog-Protokolls und von Hostservern mithilfe der Microsoft-Überwachungssammeldienste. Diese Protokolle werden in einer Protokolldatenbank abgelegt, von der aus Warnungen zu verdächtigen Ereignissen für einen Microsoft-Administrator generiert werden. Der Administrator kann auf diese Protokolle zugreifen und sie analysieren.

Die [Azure-Diagnose](https://msdn.microsoft.com/library/azure/gg433048.aspx) ist ein Azure-Feature, das Ihnen das Sammeln von Diagnosedaten aus einer in Azure ausgeführten Anwendung ermöglicht. Hierbei handelt es sich um Diagnosedaten zum Debuggen und Behandeln von Problemen, zum Messen der Leistung, zum Überwachen der Ressourcenauslastung, zum Analysieren des Datenverkehrs, zum Planen der Kapazität und zum Durchführen der Überwachung. Nach dem Erfassen der Diagnosedaten können diese zur dauerhaften Speicherung an ein Azure-Speicherkonto übertragen werden. Datenübertragungen können entweder geplant oder bedarfsgesteuert erfolgen. Der Artikel [Microsoft Azure-Sicherheits- und Überwachungsprotokollverwaltung](azure-security-audit-log-management.md) enthält Details zum Sammeln dieser Informationen und zum Ausführen von Analysen.

##Bedrohungsabwehr

Zusätzlich zur Isolierung, Verschlüsselung und Filterung setzt Azure eine Anzahl von Mechanismen und Verfahren zur Bedrohungsabwehr ein, um Infrastruktur und Dienste zu schützen. Dazu zählen interne Kontrollen und Technologien, die zum Erkennen und Beheben erweiterter Bedrohungen dienen, darunter DDoS, Berechtigungsausweitung und die [OWASP Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

Die Sicherheitskontrollen und Verfahren zur Risikoverwaltung, die Microsoft zur Sicherung der Cloudinfrastruktur einsetzt, verringern das Risiko von Sicherheitsvorfällen. Falls jedoch ein Vorfall stattfindet, steht das SIM-Team (Security Incident Management) innerhalb des OSSC-Teams (Microsoft Online Security Services & Compliance) rund um die Uhr bereit.

### Implementierung der Bedrohungsabwehr in Azure

Azure verfügt über Sicherheitskontrollen, um die Bedrohungsabwehr zu implementieren und um Kunden beim Abwehren potenzieller Sicherheitsrisiken in ihrer Umgebung zu unterstützen. In der folgenden Liste werden die von Azure angebotenen Funktionen zur Bedrohungsabwehr zusammengefasst:

-   [Azure-Antischadsoftware](azure-security-antimalware.md) ist standardmäßig auf allen Infrastrukturservern aktiviert. Sie können sie optional auf Ihren eigenen virtuellen Computern aktivieren.

-   Microsoft bietet eine kontinuierliche Überwachung über Server, Netzwerke und Anwendungen hinweg, um Gefahren zu erkennen und Exploits zu verhindern. Administratoren werden durch automatisierte Warnungen über anomale Verhalten benachrichtigt, sodass sie sowohl bei internen als auch bei externen Gefahren korrigierende Maßnahmen ergreifen können.

-   Sie haben die Möglichkeit, innerhalb Ihrer Abonnements Sicherheitslösungen von Drittanbietern bereitzustellen, wie z. B. Webanwendungsfirewalls von [Barracuda](https://techlib.barracuda.com/ng54/deployonazure).

-   Der Microsoft-Ansatz für Penetrationstests umfasst „[Red-Teaming](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)“. Dabei greifen Microsoft-Sicherheitsexperten Live-Produktionssysteme in Azure an (nicht die von Kunden), um Schutzmaßnahmen gegen reale, fortgeschrittene und dauerhafte Bedrohungen zu testen.

-   Integrierte Bereitstellungssysteme verwalten die Verteilung und Installation von Sicherheitspatches über die gesamte Azure-Plattform.

##Nächste Schritte

[Azure Trust Center](https://azure.microsoft.com/support/trust-center/)

[Blog des Azure-Sicherheitsteams](http://blogs.msdn.com/b/azuresecurity/)

[Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)

[Active Directory-Blog](http://blogs.technet.com/b/ad/)

<!---HONumber=AcomDC_1217_2015-->