<properties
   pageTitle="Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Azure-Computern | Microsoft Azure"
   description="Wenn Sie wissen, wie Sie AD-Domänendienste und AD-Verbunddienste lokal bereitstellen, können Sie sich darüber informieren, wie diese auf Azure Virtual Machines funktionieren."
   services="active-directory"
   documentationCenter=""
   authors="femila"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/04/2016"
   ms.author="femila"/>

# Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Azure-Computern

In diesem Artikel werden die wichtigen Unterschiede zwischen der lokalen Bereitstellung von Windows Server Active Directory-Domänendiensten (AD DS) und Active Directory-Verbunddiensten (AD FS) und der Bereitstellung auf virtuellen Microsoft Azure-Computern beschrieben.

## Umfang und Zielgruppe

Der Artikel richtet sich an Personen, die bereits Erfahrungen mit der lokalen Bereitstellung von Active Directory gemacht haben. Es werden die Unterschiede zwischen der Bereitstellung von Active Directory auf virtuellen Microsoft Azure-Computern bzw. in Azure Virtual Networks und der herkömmlichen lokalen Bereitstellung von Active Directory aufgezeigt. Virtuelle Azure-Computer und Azure Virtual Networks sind Teil eines IaaS-Angebots (Infrastructure-as-a-Service) für Unternehmen, die Computerressourcen in der Cloud nutzen möchten.

Falls Sie mit der AD-Bereitstellung noch nicht vertraut sind, helfen Ihnen die Informationen unter [AD DS Deployment Guide](https://technet.microsoft.com/library/cc753963) (Anleitung zur AD DS-Bereitstellung) bzw. [Planen der AD FS-Bereitstellung](https://technet.microsoft.com/library/dn151324.aspx) weiter.

In diesem Artikel wird davon ausgegangen, dass Leser mit den folgenden Konzepten vertraut sind:

- Windows Server AD DS-Bereitstellung und -Verwaltung
- Bereitstellung und Konfiguration von DNS zur Unterstützung einer Windows Server AD DS-Infrastruktur
- Windows Server AD FS-Bereitstellung und -Verwaltung
- Bereitstellung, Konfiguration und Verwaltung von Anwendungen der vertrauenden Seite (Websites und Webdienste), die Windows Server AD FS-Token nutzen können
- Allgemeine VM-Konzepte, z. B. Konfiguration eines virtuellen Computers, virtueller Datenträger und virtueller Netzwerke

In diesem Artikel werden die Anforderungen für eine Hybridbereitstellung beschrieben, bei der Windows Server AD DS bzw. AD FS teilweise lokal und teilweise auf virtuellen Azure-Computern bereitgestellt werden. Im Dokument geht es zuerst um die wichtigen Unterschiede zwischen der Ausführung von Windows Server AD DS und AD FS auf virtuellen Azure-Computern und lokalen Computern und die wichtigen Entscheidungen, die sich auf den Entwurf und die Bereitstellung auswirken. Im restlichen Teil werden die Richtlinien für die einzelnen Entscheidungspunkte ausführlicher beschrieben, und Sie erfahren, wie die Richtlinien für die unterschiedlichen Bereitstellungsszenarien gelten.

In diesem Artikel wird nicht die Konfiguration von [Azure Active Directory](http://azure.microsoft.com/services/active-directory/) beschrieben. Dabei handelt es sich um einen REST-basierten Dienst mit Funktionen für die Identitätsverwaltung und Zugriffssteuerung für Cloudanwendungen. Azure Active Directory (Azure AD) und Windows Server AD DS sind aber für die Zusammenarbeit ausgelegt, um als Lösung für die Identitäts- und Zugriffsverwaltung in den heutigen IT-Hybridumgebungen und modernen Anwendungen zu dienen. Berücksichtigen Sie Folgendes, um die Unterschiede und Beziehungen zwischen Windows Server AD DS und Azure AD besser zu verstehen:

1. Sie können Windows Server AD DS in der Cloud auf virtuellen Azure-Computern ausführen, wenn Sie Azure verwenden, um Ihr lokales Rechenzentrum in die Cloud zu erweitern.
2. Mit Azure AD können Sie Ihren Benutzern die Möglichkeit einer einmaligen Anmeldung für SaaS-Anwendungen (Software-as-a-Service) eröffnen. So setzt beispielsweise Microsoft Office 365 diese Technologie ein, und auch Anwendungen, die auf Azure oder anderen Cloudplattformen ausgeführt werden, können sie nutzen.
3. Sie können Azure AD (den Access Control Service) verwenden, damit Benutzer sich mit Identitäten von Facebook, Google, Microsoft und anderen Identitätsanbietern an Anwendungen anmelden können, die in der Cloud oder lokal gehostet werden.

Weitere Informationen zu diesen Unterschieden finden Sie unter [Azure-Identität](fundamentals-identity.md).

## Zugehörige Ressourcen

Sie können das Paket [Azure Virtual Machine Readiness Assessment](https://www.microsoft.com/download/details.aspx?id=40898) herunterladen und ausführen. Bei der Bewertung mit diesem Paket wird Ihre lokale Umgebung automatisch untersucht, und es wird basierend auf der Anleitung in diesem Thema ein angepasster Bericht erstellt, der Ihnen als Hilfe beim Migrieren der Umgebung zu Azure dient.

Wir empfehlen Ihnen auch, sich zuerst die Tutorials, Anleitungen und Videos zu den folgenden Themen anzusehen:

- [Konfigurieren eines virtuellen Netzwerks nur für die Cloud im Azure-Portal](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
- [Konfigurieren eines Site-to-Site-VPN im Azure-Portal](../vpn-gateway/vpn-gateway-site-to-site-create.md)
- [Installieren einer neuen Active Directory-Gesamtstruktur auf einem virtuellen Azure-Netzwerk](active-directory-new-forest-virtual-machine.md)
- [Installieren eines Active Directory-Replikatdomänencontrollers in Azure](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md)
- [Microsoft Azure IT Pro IaaS: (01) Grundlagen zu virtuellen Computern](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
- [Microsoft Azure IT Pro IaaS: (05) Erstellen virtueller Netzwerke und Herstellen standortübergreifender Verbindungen](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## Einführung

Die wesentlichen Anforderungen für die Bereitstellung von Windows Server Active Directory auf virtuellen Azure-Computern unterscheiden sich nur wenig von der Bereitstellung auf lokalen virtuellen Computern (und in gewissem Maße auch physischen Computern). Betrachten wir beispielsweise für Windows Server AD DS den Fall, dass es sich bei den Domänencontrollern (DCs), die Sie auf virtuellen Azure-Computern bereitstellen, um Replikate in einer vorhandenen Domäne bzw. Gesamtstruktur des Unternehmens handelt. Die Azure-Bereitstellung kann hierbei größtenteils genauso behandelt werden, wie Sie auch andere zusätzliche Windows Server Active Directory-Standorte behandeln. Dies bedeutet, dass Subnetze in Windows Server AD DS definiert werden müssen, ein Standort erstellt werden muss und die Subnetze mit diesem Standort verknüpft und über entsprechende Standortverknüpfungen mit anderen Standorten verbunden werden müssen. Es bestehen aber einige Unterschiede, die für alle Azure-Bereitstellungen gleich sind, und es gibt andere, die je nach Bereitstellungsszenario variieren. Zwei grundsätzliche Unterschiede sind unten beschrieben:

### Virtuelle Azure-Computer benötigen eine Verbindung mit dem lokalen Unternehmensnetzwerk.

Für das Verbinden von virtuellen Azure-Computern mit einem lokalen Unternehmensnetzwerk ist ein Azure Virtual Network erforderlich. Dieses Netzwerk muss eine Site-to-Site- oder Site-to-Point-VPN-Komponente (Virtual Private Network) umfassen, mit der die Verbindung für virtuelle Azure-Computer und lokale Computer nahtlos hergestellt werden kann. Mit dieser VPN-Komponente kann lokalen Domänenmitgliedscomputern auch der Zugriff auf eine Windows Server Active Directory-Domäne ermöglicht werden, deren Domänencontroller ausschließlich auf virtuellen Azure-Computern gehostet werden. Es ist aber wichtig zu beachten, dass bei einem VPN-Ausfall die Authentifizierung und andere Vorgänge, die von Windows Server Active Directory abhängig sind, ebenfalls ausfallen. Benutzer können sich unter Umständen zwar mit vorhandenen zwischengespeicherten Anmeldeinformationen anmelden, aber alle Peer-to-Peer- oder Client-to-Server-Authentifizierungsversuche, für die noch Tickets ausgestellt werden müssen oder die veraltet sind, sind nicht erfolgreich.

Ein Demovideo und eine Liste mit Schritt-für-Schritt-Tutorials, z. B. [Konfigurieren eines Site-to-Site-VPN im Azure-Portal](../vpn-gateway/vpn-gateway-site-to-site-create.md), finden Sie unter [Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/).

> [AZURE.NOTE] Sie können Windows Server Active Directory auch in einem Azure Virtual Network bereitstellen, das nicht über eine Verbindung mit einem lokalen Netzwerk verfügt. Für die Richtlinien in diesem Thema wird aber vorausgesetzt, dass ein Azure Virtual Network verwendet wird, da es IP-Adressierungsfunktionen umfasst, die für Windows Server wichtig sind.

### Statische IP-Adressen müssen mit Azure PowerShell konfiguriert werden.

Dynamische Adressen werden standardmäßig zugewiesen, aber verwenden Sie das Set-AzureStaticVNetIP-Cmdlet, um stattdessen eine statische IP-Adresse zuzuweisen. Hiermit wird eine statische IP-Adresse festgelegt, die die Dienstreparatur und das Herunterfahren und den Neustart der VM übersteht. Weitere Informationen finden Sie unter [Static Internal IP Address for Virtual Machines](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) (Statische interne IP-Adresse für Virtual Machines).

## <a name="BKMK_Glossary"></a>Begriffe und Definitionen

Unten ist eine nicht erschöpfende Liste mit Begriffen für verschiedene Azure-Technologien angegeben, die in diesem Artikel erwähnt werden.

- **Virtuelle Azure-Computer:** Die ist das IaaS-Angebot von Azure, bei dem Kunden VMs bereitstellen können, um darauf nahezu alle herkömmlichen lokalen Serverworkloads auszuführen.

- **Azure Virtual Network:** Dies ist der Netzwerkdienst in Azure, mit dem Kunden virtuelle Netzwerke in Azure erstellen und verwalten und auf sichere Weise mit ihrer eigenen lokalen Netzwerkinfrastruktur verknüpfen können, indem sie ein Virtual Private Network (VPN) nutzen.

- **Virtuelle IP-Adresse:** Eine IP-Adresse für Internetverbindungen, die nicht an einen bestimmten Computer oder eine Netzwerkschnittstellenkarte gebunden ist. Clouddiensten wird eine virtuelle IP-Adresse zugewiesen, damit sie Netzwerkdatenverkehr empfangen können, der an eine Azure-VM umgeleitet wird. Eine virtuelle IP-Adresse ist eine Eigenschaft eines Clouddiensts, der einen oder mehrere virtuelle Azure-Computer enthalten kann. Beachten Sie außerdem, dass Azure Virtual Network einen oder mehrere Clouddienste enthalten kann. Virtuelle IP-Adressen verfügen über native Funktionen für den Lastenausgleich.

- **Dynamische IP-Adresse:** Dies ist die IP-Adresse, die ausschließlich intern verwendet wird. Sie sollte (mit dem Set-AzureStaticVNetIP-Cmdlet) als statische IP-Adresse für VMs konfiguriert werden, von denen die DC/DNS-Serverrollen gehostet werden.

- **Dienstreparatur:** Dies ist der Prozess, bei dem Azure einen Dienst automatisch wieder in den Ausführungszustand versetzt, nachdem erkannt wurde, dass der Dienst ausgefallen ist. Die Dienstreparatur ist einer der Aspekte von Azure, für den Verfügbarkeit und Resilienz unterstützt wird. Die Wahrscheinlichkeit ist zwar gering, aber das Ergebnis nach einer Dienstreparatur für einen auf einer VM ausgeführten DC kann einem ungeplanten Neustart ähneln. Dies ist mit einigen Nebeneffekten verbunden:

 - Der virtuelle Netzwerkadapter in der VM ändert sich.
 - Die MAC-Adresse des virtuellen Netzwerkadapters ändert sich.
 - Die Prozessor/CPU-ID der VM ändert sich.
 - Die IP-Konfiguration des virtuellen Netzwerkadapters ändert sich nicht, solange die VM mit einem virtuellen Netzwerk verknüpft und die IP-Adresse der VM statisch ist.

 Keines dieser Verhalten wirkt sich auf Windows Server Active Directory aus, da keine Abhängigkeit von der MAC-Adresse oder der Prozessor/CPU-ID besteht. Für alle Windows Server Active Directory-Bereitstellungen unter Azure wird empfohlen, dass sie wie oben beschrieben in einem Azure Virtual Network ausgeführt werden.

## Ist es sicher, Windows Server Active Directory-Domänencontroller zu virtualisieren?

Das Bereitstellen von Windows Server Active Directory-DCs auf virtuellen Azure-Computern unterliegt denselben Richtlinien wie die lokale Ausführung von DCs auf einem virtuellen Computer. Das Ausführen virtualisierter DCs ist ein sicheres Verfahren, solange Sie sich an die Richtlinien zur Sicherung und Wiederherstellung von DCs halten. Weitere Informationen zu Einschränkungen und Richtlinien für die Ausführung virtualisierter DCs finden Sie unter [Ausführen von Domänencontrollern in Hyper-V](https://technet.microsoft.com/library/dd363553).

Hypervisoren stellen Technologien bereit bzw. trivialisieren sie, die Probleme für viele verteilte System verursachen können, darunter auch Windows Server Active Directory. Auf einem physischen Server können Sie beispielsweise einen Datenträger klonen oder nicht unterstützte Methoden verwenden, um für den Zustand eines Servers ein Rollback durchzuführen, z. B. mit Verwendung von SANs usw. Auf einem physischen Server ist dies aber viel schwieriger als die Wiederherstellung einer VM-Momentaufnahme in einem Hypervisor. Azure verfügt über Funktionen, die zu dem gleichen unerwünschten Zustand führen können. Beispielsweise sollten Sie keine VHD-Dateien von DCs kopieren, anstatt regelmäßige Sicherungen zu erstellen, da deren Wiederherstellung zu einer ähnlichen Situation wie bei der Verwendung von Funktionen zum Wiederherstellen von Momentaufnahmen führen kann.

Derartige Rollbacks ergeben USN-Blasen, die dauerhaft divergierende Zustände zwischen DCs nach sich ziehen können. Hierdurch können folgende Probleme verursacht werden:

- Veraltete Objekte
- Inkonsistente Kennwörter
- Inkonsistente Attributwerte
- Schemakonflikte bei einem Rollback des Schemamasters

Weitere Informationen dazu, welche Auswirkungen sich für DCs ergeben, finden Sie unter [USN und USN-Rollback](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

Ab Windows Server 2012 sind in AD DS zusätzliche Sicherungsmechanismen integriert (siehe [Einführung in die Virtualisierung der Active Directory-Domänendienste](https://technet.microsoft.com/library/hh831734.aspx)). Mit diesen Sicherheitsmechanismen sind virtualisierte Domänencontroller besser vor den oben beschriebenen Problemen geschützt, solange die zugrunde liegende Hypervisorplattform „VM-GenerationID“ unterstützt. Azure unterstützt „VM-GenerationID“, sodass Domänencontroller, die Windows Server 2012 oder höher auf virtuellen Azure-Computern ausführen, über diese zusätzlichen Sicherheitsmechanismen verfügen.

> [AZURE.NOTE] Sie sollten eine VM herunterfahren und neu starten, von der die Domänencontroller-Rolle in Azure im Rahmen des Gastbetriebssystems ausgeführt wird, anstatt die Option **Herunterfahren** im klassischen Azure-Portal zu verwenden. Wenn Sie das klassische Portal zum Herunterfahren einer VM verwenden, wird jetzt die Zuordnung der VM aufgehoben. Eine VM mit aufgehobener Zuordnung hat den Vorteil, dass sie keine Kosten verursacht. Es wird aber auch die „VM-GenerationID“ zurückgesetzt, und dies ist für einen DC nicht wünschenswert. Wenn die „VM-GenerationID“ zurückgesetzt wird, wird dies auch für die „invocationID“ der AD DS-Datenbank durchgeführt, der RID-Pool wird verworfen, und SYSVOL wird als nicht autoritativ gekennzeichnet. Weitere Informationen finden Sie unter [Einführung in die Virtualisierung der Active Directory-Domänendienste (Active Directory Domain Services, AD DS) ](https://technet.microsoft.com/library/hh831734.aspx) und [Safely Virtualizing DFSR](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx) (Sichere Virtualisierung von DFSR).

## Gründe für die Bereitstellung von Windows Server AD DS auf Azure Virtual Machines

Viele Windows Server AD DS-Bereitstellungsszenarien eignen sich gut für die Bereitstellung als VMs in Azure. Nehmen wir beispielsweise an, dass Sie ein Unternehmen in Europa besitzen, das Benutzer an einem weit entfernten Standort in Asien authentifizieren muss. Das Unternehmen hat zuvor noch keine Windows Server Active Directory-DCs in Asien bereitgestellt, da die Kosten hoch und nur begrenzte Kenntnisse in Bezug auf die Verwaltung der Server nach der Bereitstellung vorhanden sind. Daher werden Authentifizierungsanforderungen aus Asien mit suboptimalen Ergebnissen von Domänencontrollern in Europa verarbeitet. In diesem Fall können Sie einen Domänencontroller auf einem virtuellen Computer bereitstellen, für den Sie angegeben haben, dass er im Azure-Rechenzentrum in Asien ausgeführt werden muss. Das Anfügen dieses Domänencontrollers an ein Azure Virtual Network, das direkt mit dem Remotestandort verbunden ist, führt zu einer Verbesserung der Authentifizierungsleistung.

Azure eignet sich auch gut als Ersatz für ansonsten teure Standorte für die Notfallwiederherstellung. Die relativ geringen Kosten für das Hosten einer kleinen Anzahl von Domänencontrollern und eines einzelnen virtuellen Netzwerks in Azure stellen eine attraktive Alternative dar.

Es kann auch sein, dass Sie eine Netzwerkanwendung unter Azure bereitstellen möchten, z. B. SharePoint. Unter Umständen ist hierfür Windows Server Active Directory erforderlich, aber es besteht keine Abhängigkeit vom lokalen Netzwerk oder von der Unternehmensinstanz von Windows Server Active Directory. In diesem Fall ist die Bereitstellung einer isolierten Gesamtstruktur unter Azure optimal, um die Anforderungen des SharePoint-Servers zu erfüllen. Außerdem wird auch wieder die Bereitstellung von Netzwerkanwendungen unterstützt, für die eine Verbindung mit dem lokalen Netzwerk und der Unternehmensinstanz von Active Directory erforderlich ist.

> [AZURE.NOTE] Da die VPN-Komponente für die Verbindung zwischen einem Azure Virtual Network und einem lokalen Netzwerk über eine Layer-3-Verbindung verfügt, kann sie für lokal ausgeführte Mitgliedsserver die Nutzung von DCs ermöglichen, die als Azure Virtual Machines in einem Azure Virtual Network ausgeführt werden. Falls das VPN aber nicht verfügbar ist, funktioniert die Kommunikation zwischen lokalen Computern und Azure-basierten Domänencontrollern nicht, sodass sich Authentifizierungsfehler und verschiedene andere Fehler ergeben.

## Unterschiede zwischen der Bereitstellung von Windows Server Active Directory-Domänencontrollern auf Azure Virtual Machines und der lokalen Bereitstellung

- Für jedes Windows Server Active Directory-Bereitstellungsszenario mit mehr als einer einzelnen VM ist die Verwendung eines Azure Virtual Network erforderlich, um die Konsistenz der IP-Adressen sicherzustellen. Beachten Sie, dass in dieser Anleitung davon ausgegangen wird, dass DCs in einem Azure Virtual Network ausgeführt werden.

- Wie bei lokalen Domänencontrollern auch, werden statische IP-Adressen empfohlen. Eine statische IP-Adresse kann nur mit Azure PowerShell konfiguriert werden. Weitere Informationen finden Sie unter [Static Internal IP Address for Virtual Machines](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) (Statische interne IP-Adresse für virtuelle Computer). Wenn Sie Überwachungssysteme oder andere Lösungen nutzen, mit denen die Konfiguration statischer IP-Adressen im Gastbetriebssystem überprüft wird, können Sie die gleiche statische IP-Adresse den Netzwerkadapter-Eigenschaften der VM zuweisen. Beachten Sie aber, dass der Netzwerkadapter verworfen wird, wenn die VM einer Dienstreparatur unterzogen wird oder sie im klassischen Portal heruntergefahren und die Zuordnung der Adresse aufgehoben wird. In diesem Fall muss die statische IP-Adresse im Gastbetriebssystem zurückgesetzt werden.

- Die Bereitstellung von virtuellen Computern in einem virtuellen Netzwerk impliziert nicht, dass eine Verbindung mit einem lokalen Netzwerk besteht (oder dass diese erforderlich ist). Es bedeutet lediglich, dass das virtuelle Netzwerk dies ermöglicht. Sie müssen ein virtuelles Netzwerk für die private Kommunikation zwischen Azure und Ihrem lokalen Netzwerk erstellen. Sie müssen einen VPN-Endpunkt im lokalen Netzwerk bereitstellen. Die VPN-Verbindung wird von Azure zum lokalen Netzwerk eingerichtet. Weitere Informationen finden Sie unter [Virtuelle Netzwerke im Überblick](../virtual-network/virtual-networks-overview.md) und [Konfigurieren eines Site-to-Site-VPN im Azure-Portal](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE] Eine Option zum [Erstellen eines Point-to-Site-VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) ist verfügbar, um einzelne Windows-basierte Computer direkt mit einem Azure Virtual Network zu verbinden.

- Unabhängig davon, ob Sie ein virtuelles Netzwerk erstellen oder nicht, werden unter Azure Kosten für ausgehenden Datenverkehr berechnet, aber nicht für eingehenden Datenverkehr. Unterschiedliche Windows Server Active Directory-Entwurfsentscheidungen können sich darauf auswirken, wie viel ausgehender Datenverkehr von einer Bereitstellung generiert wird. Beispielsweise wird der ausgehende Datenverkehr durch die Bereitstellung eines schreibgeschützten Domänencontrollers (RODC) eingeschränkt, da in ausgehender Richtung keine Replikation durchgeführt wird. Die Entscheidung zur Bereitstellung eines RODC muss gegenüber der Anforderung zur Durchführung von Schreibvorgängen für den DC und die [Kompatibilität](https://technet.microsoft.com/library/cc755190) abgewogen werden, die für Anwendungen und Dienste am Standort in Bezug auf RODCs gilt. Weitere Informationen zu Gebühren für Datenverkehr finden Sie unter [Azure-Preise](http://azure.microsoft.com/pricing/).

- Sie haben zwar die vollständige Kontrolle darüber, welche Serverressourcen für VMs lokal verwendet werden, z. B. RAM, Datenträgergröße usw., aber in Azure müssen Sie die Wahl aus einer Liste mit vorkonfigurierten Servergrößen treffen. Für einen Domänencontroller wird ein Datenträger zusätzlich zum Betriebssystem-Datenträger benötigt, um die Windows Server Active Directory-Datenbank zu speichern.

## Ist es möglich, Windows Server AD FS auf virtuellen Azure-Computern bereitzustellen?

Ja. Sie können Windows Server AD FS auf virtuellen Azure-Computern bereitstellen. Die [bewährten Methoden für die lokale AD FS-Bereitstellung](https://technet.microsoft.com/library/dn151324.aspx) gelten auch für die AD FS-Bereitstellung unter Azure. Für einige bewährte Methoden, z. B. Lastenausgleich und hohe Verfügbarkeit, sind aber Technologien erforderlich, die über das Angebot von AD FS hinausgehen. Diese müssen von der zugrunde liegenden Infrastruktur bereitgestellt werden. Wir sehen uns nun einige dieser bewährten Methoden an, um zu ermitteln, wie sie mit Azure-VMs und einem Azure Virtual Network umgesetzt werden können.

1. **Machen Sie Sicherheitstokendienst-Server (STS) niemals direkt für das Internet verfügbar.**

    Dies ist wichtig, da vom STS Sicherheitstoken ausgegeben werden. Daher sollten STS-Server, z. B. AD FS-Server, mit derselben Schutzebene wie ein Domänencontroller behandelt werden. Wenn ein STS kompromittiert wird, können böswillige Benutzer Zugriffstoken ausgeben, die unter Umständen manipulierte Ansprüche für Anwendungen der vertrauenden Seite und andere STS-Server in vertrauenden Unternehmen enthalten.

2. **Stellen Sie Active Directory-Domänencontroller für alle Benutzerdomänen in einem Netzwerk als AD FS-Server bereit.**

    AD FS-Server verwenden Active Directory-Domänendienste, um Benutzer zu authentifizieren. Es wird empfohlen, Domänencontroller in demselben Netzwerk wie die AD FS-Server bereitzustellen. So ist für Geschäftskontinuität gesorgt, falls die Verknüpfung zwischen dem Azure-Netzwerk und Ihrem lokalen Netzwerk unterbrochen wird, und Anmeldungen können mit einer geringeren Latenz und besseren Leistung durchgeführt werden.

3. **Stellen Sie mehrere AD FS-Knoten bereit, um eine hohe Verfügbarkeit und einen Lastenausgleich zu erzielen.**

    In den meisten Fällen ist der Ausfall einer Anwendung, die von AD FS bereitgestellt wird, nicht akzeptabel. Anwendungen, für die Sicherheitstoken erforderlich sind, sind häufig unternehmenskritische Anwendungen. Aus diesem Grund und aufgrund der Tatsache, dass sich AD FS jetzt im kritischen Pfad für den Zugriff auf unternehmenskritische Anwendungen befindet, muss der AD FS-Dienst über mehrere AD FS-Proxys und AD FS-Server hoch verfügbar sein. Um die Verteilung von Anforderungen zu erreichen, werden Load Balancer meist sowohl vor AD FS-Proxys als auch vor den AD FS-Servern bereitgestellt.

4. **Stellen Sie mindestens einen Webanwendungsproxy-Knoten für den Internetzugriff bereit.**

    Wenn Benutzer auf Anwendungen zugreifen müssen, die durch den AD FS-Dienst geschützt sind, muss der AD FS-Dienst über das Internet verfügbar sein. Dies wird realisiert, indem der Webanwendungsproxy-Dienst bereitgestellt wird. Es wird dringend empfohlen, mehr als einen Knoten bereitzustellen, um für hohe Verfügbarkeit und den Lastenausgleich zu sorgen.

5. **Beschränken Sie den Zugriff von den Webanwendungsproxy-Knoten auf interne Netzwerkressourcen.**

    Damit externe Benutzer über das Internet auf AD FS zugreifen können, müssen Sie Webanwendungsproxy-Knoten bereitstellen (bzw. AD FS-Proxy in früheren Versionen von Windows Server). Die Webanwendungsproxy-Knoten werden direkt im Internet verfügbar gemacht. Sie müssen nicht der Domäne beitreten und benötigen nur Zugriff auf die AD FS-Server über die TCP-Ports 443 und 80. Es wird dringend empfohlen, die Kommunikation mit allen anderen Computern (insbesondere Domänencontrollern) zu blockieren.

    Dies erreichen Sie in der Regel mit einer DMZ. Für Firewalls wird ein Positivlisten-Betriebsmodus verwendet, um den Datenverkehr aus der DMZ in das lokale Netzwerk einzuschränken (es wird also nur Datenverkehr von den angegebenen IP-Adressen und über die angegebenen Ports zugelassen, und der gesamte andere Datenverkehr wird blockiert).

Im folgenden Diagramm ist eine herkömmliche lokale AD FS-Bereitstellung dargestellt.

![Diagramm einer herkömmlichen lokalen Bereitstellung der Active Directory-Verbunddienste](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Da Azure aber nicht über eine native Firewallfunktion mit vollem Funktionsumfang verfügt, müssen zum Beschränken des Datenverkehrs andere Optionen verwendet werden. In der folgenden Tabelle sind die Optionen und die jeweiligen Vor- und Nachteile aufgeführt.

| Option | Vorteil | Nachteil |
| ------ | --------- | ------------ |
| [Azure-Netzwerk-ACLs](virtual-networks-acl.md) | Geringere Kosten und einfachere Erstkonfiguration | Zusätzliche ACL-Netzwerkkonfiguration erforderlich, wenn der Bereitstellung neue VMs hinzugefügt werden |
| [Barracuda NG Firewalls](https://www.barracuda.com/products/ngfirewall) | Positivlisten-Betriebsmodus, keine ACL-Netzwerkkonfiguration erforderlich | Höhere Kosten und Komplexität für Ersteinrichtung |

Die allgemeinen Schritte zum Bereitstellen von AD FS lauten in diesem Fall wie folgt:

1. Erstellen Sie ein [virtuelles Netzwerk mit standortübergreifender Konnektivität](../vpn-gateway/vpn-gateway-cross-premises-options.md), entweder per VPN oder [ExpressRoute](http://azure.microsoft.com/services/expressroute/).

2. Stellen Sie Domänencontroller im virtuellen Netzwerk bereit. Dieser Schritt ist optional, wird aber empfohlen.

3. Stellen Sie der Domäne beigetretene AD FS-Server im virtuellen Netzwerk bereit.

4. Erstellen Sie eine [Gruppe mit internem Lastenausgleich](http://azure.microsoft.com/blog/internal-load-balancing/), die die AD FS-Server enthält und für die im virtuellen Netzwerk eine neue private IP-Adresse verwendet wird (dynamische IP-Adresse).

  1. Aktualisieren Sie DNS, um den FQDN so zu erstellen, dass er auf die private (dynamische) IP-Adresse der Gruppe mit internem Lastenausgleich zeigt.

5. Erstellen Sie einen Clouddienst (oder ein separates virtuelles Netzwerk) für die Webanwendungsproxy-Knoten.

6. Stellen Sie die Webanwendungsproxy-Knoten im Clouddienst oder im virtuellen Netzwerk bereit.

  1. Erstellen Sie eine Gruppe mit externem Lastenausgleich, in der die Webanwendungsproxy-Knoten enthalten sind.

  2. Aktualisieren Sie den externen DNS-Namen (FQDN) so, dass er auf die öffentliche IP-Adresse des Clouddiensts zeigt (virtuelle IP-Adresse).

  3. Konfigurieren Sie die AD FS-Proxys so, dass der FQDN verwendet wird, der der Gruppe mit internem Lastenausgleich für die AD FS-Server entspricht.

  4. Aktualisieren Sie anspruchsbasierte Websites so, dass der externe FQDN für ihren Anspruchsanbieter verwendet wird.

7. Beschränken Sie den Zugriff zwischen dem Webanwendungsproxy und beliebigen Computern im virtuellen AD FS-Netzwerk.

Zur Beschränkung des Datenverkehrs muss die Gruppe mit Lastenausgleich für den internen Azure Load Balancer nur für den Datenverkehr zu den TCP-Ports 80 und 443 konfiguriert werden. Der gesamte Datenverkehr zur internen dynamischen IP-Adresse der Gruppe mit Lastenausgleich wird verworfen.

![Diagramm mit AD FS-Netzwerk-ACLs, TCP 443 und 80 zugelassen](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Datenverkehr zu den AD FS-Servern wird nur aus den folgenden Quellen zugelassen:

- Interner Load Balancer von Azure
- IP-Adresse eines Administrators im lokalen Netzwerk

> [AZURE.WARNING] Beim Entwurf muss verhindert werden, dass Webanwendungsproxy-Knoten andere VMs im Azure Virtual Network oder an anderen Orten im lokalen Netzwerk erreichen. Sie erreichen dies, indem Sie Firewallregeln in der lokalen Appliance für Express Route-Verbindungen oder im VPN-Gerät für Site-to-Site-VPN-Verbindungen konfigurieren.

Ein Nachteil dieser Option ist die Anforderung, dass Sie die Netzwerk-ACLs für mehrere Geräte konfigurieren müssen, z. B. interner Load Balancer, AD FS-Server und alle anderen Server, die dem virtuellen Netzwerk hinzugefügt werden. Wenn ein Gerät der Bereitstellung hinzugefügt wird, ohne Netzwerk-ACLs zur Beschränkung des Datenverkehrs zum Gerät zu konfigurieren, besteht für die gesamte Bereitstellung unter Umständen ein erhöhtes Risiko. Wenn sich die IP-Adressen der Webanwendungsproxy-Knoten jemals ändern, müssen die Netzwerk-ACLs zurückgesetzt werden. (Dies bedeutet, dass die Proxys für die Verwendung von [statischen internen IP-Adressen](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) konfiguriert sein sollten.)

![AD FS in Azure mit Netzwerk-ACLs](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Eine andere Möglichkeit ist die Verwendung der Appliance [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) zum Steuern des Datenverkehrs zwischen AD FS-Proxyservern und AD FS-Servern. Diese Option entspricht den bewährten Methoden für Sicherheit und hohe Verfügbarkeit und benötigt nach der Ersteinrichtung weniger Verwaltungsaufwand, da die Appliance „Barracuda NG Firewall“ über einen Positivlisten-Betriebsmodus für die Firewalladministration verfügt und direkt in einem Azure Virtual Network installiert werden kann. So wird erreicht, dass jedes Mal Netzwerk-ACLs konfiguriert werden, wenn der Bereitstellung ein neuer Server hinzugefügt wird. Diese Option ist bei der anfänglichen Bereitstellung aber mit komplexen Vorgängen und Kosten verbunden.

In diesem Fall werden anstelle von einem Netzwerk zwei virtuelle Netzwerke bereitgestellt. Wir geben ihnen die Namen „VNet1“ und „VNet2“. VNet1 enthält die Proxys, und VNet2 enthält die Sicherheitstokendienste (STS) und die Netzwerkverbindung zurück zum Unternehmensnetzwerk. Daher ist VNet1 physisch (aber trotzdem virtuell) von VNet2 und somit wiederum vom Unternehmensnetzwerk getrennt. Für VNet1 wird dann eine Verbindung mit VNet2 hergestellt, indem eine spezielle Tunneling-Technologie mit der Bezeichnung Transport Independent Network Architecture (TINA) verwendet wird. Der TINA-Tunnel ist per Barracuda NG Firewall mit den einzelnen virtuellen Netzwerken verknüpft. Es wird eine Barracuda NG Firewall für jedes virtuelle Netzwerk verwendet. Zur Erzielung einer hohen Verfügbarkeit wird empfohlen, zwei Barracuda NG Firewalls für jedes virtuelle Netzwerk bereitzustellen: eine aktive und eine passive. Diese verfügen über umfassende Firewallfunktionen, sodass wir den Betrieb einer herkömmlichen lokalen DMZ in Azure nachstellen können.

![AD FS in Azure mit Firewall](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Weitere Informationen finden Sie unter [AD FS: Erweitern einer Ansprüche unterstützenden lokalen Front-End-Anwendung ins Internet](#BKMK_CloudOnlyFed).

### Alternative zur AD FS-Bereitstellung, wenn nur Office 365-SSO erreicht werden soll

Es gibt noch eine ganz andere Alternative zur Bereitstellung von AD FS, wenn das Ziel nur das einmalige Anmelden für Office 365 ist. In diesem Fall können Sie einfach lokal DirSync mit Kennwortsynchronisierung bereitstellen und das gleiche Endergebnis bei minimaler Bereitstellungskomplexität erzielen, weil für diesen Ansatz AD FS oder Azure nicht erforderlich sind.

In der folgenden Tabelle wird verglichen, wie die Anmeldevorgänge mit und ohne Bereitstellung von AD FS funktionieren.

| Einmaliges Anmelden für Office 365 mit AD FS und DirSync | Gleiche Anmeldung für Office 365 mit DirSync und Kennwortsynchronisierung |
| ------------- | ------------- |
| 1\. Der Benutzer meldet sich im Unternehmensnetzwerk an und wird mit Windows Server Active Directory authentifiziert. | 1\. Der Benutzer meldet sich im Unternehmensnetzwerk an und wird mit Windows Server Active Directory authentifiziert. |
| 2\. Der Benutzer versucht, auf Office 365 zuzugreifen (I am @contoso.com). | 2\. Der Benutzer versucht, auf Office 365 zuzugreifen (I am @contoso.com). |
| 3\. Office 365 leitet den Benutzer an Azure AD weiter. | 3\. Office 365 leitet den Benutzer an Azure AD weiter. |
| 4\. Da Azure AD den Benutzer nicht authentifizieren kann und davon ausgeht, dass lokal eine Vertrauensstellung mit AD FS vorhanden ist, wird der Benutzer an AD FS umgeleitet. | 4\. Azure AD kann Kerberos-Tickets nicht direkt akzeptieren, und es besteht keine Vertrauensstellung. Der Benutzer wird also zum Eingeben von Anmeldeinformationen aufgefordert. |
| 5\. Der Benutzer sendet ein Kerberos-Ticket an den AD FS-Sicherheitstokendienst. | 5\. Der Benutzer gibt das gleiche lokale Kennwort ein, und Azure AD gleicht diese Angabe mit dem Benutzernamen und dem Kennwort ab, die per DirSync synchronisiert wurden. |
| 6\. AD FS transformiert das Kerberos-Ticket in das erforderliche Tokenformat bzw. die Ansprüche und leitet den Benutzer an Azure AD um. | 6\. Azure AD leitet den Benutzer zu Office 365 um. |
| 7\. Der Benutzer authentifiziert sich gegenüber Azure AD (eine weitere Transformation wird durchgeführt). | 7\. Der Benutzer kann sich an Office 365 und OWA mit dem Azure AD-Token anmelden. |
| 8\. Azure AD leitet den Benutzer zu Office 365 um. | |
| 9\. Für den Benutzer wird im Hintergrund die Anmeldung für Office 365 durchgeführt. | |

Für Office 365 mit DirSync und Kennwortsynchronisierung (kein AD FS) wird das einmalige Anmelden durch die „gleiche Anmeldung“ (Same Sign-On) ersetzt. Hierbei bedeutet „gleich“ einfach, dass der Benutzer beim Zugreifen auf Office 365 noch einmal seine lokalen Anmeldeinformationen eingeben muss. Beachten Sie, dass diese Daten vom Browser des Benutzers gespeichert werden können, um den Aufwand bei nachfolgenden Zugriffen zu verringern.

### Zusätzlicher Denkanstoß

- Wenn Sie einen AD FS-Proxy auf einem virtuellen Azure-Computer bereitstellen, ist eine Verbindung mit den AD FS-Servern erforderlich. Falls diese sich am lokalen Standort befinden, empfehlen wir Ihnen, die Site-to-Site-VPN-Verbindung des virtuellen Netzwerks zu nutzen. So können die Webanwendungsproxy-Knoten mit ihren AD FS-Servern kommunizieren.

- Wenn Sie einen AD FS-Server auf einem virtuellen Azure-Computer bereitstellen, ist eine Verbindung mit Windows Server Active Directory-Domänencontrollern, Attributspeichern und Konfigurationsdatenbanken erforderlich. Außerdem wird unter Umständen eine ExpressRoute- oder Site-to-Site-VPN-Verbindung zwischen dem Azure Virtual Network und dem lokalen Netzwerk benötigt.

- Die Gebühren werden für den gesamten Datenverkehr von Azure Virtual Machines (ausgehender Datenverkehr) angewendet. Falls die Kosten ein entscheidender Faktor sind, ist es ratsam, die Webanwendungsproxy-Knoten unter Azure bereitzustellen und die AD FS-Server am lokalen Standort zu belassen. Wenn die AD FS-Server auch auf Azure Virtual Machines bereitgestellt werden, fallen zusätzliche Kosten für die Authentifizierung der lokalen Benutzer an. Ausgehender Datenverkehr verursacht unabhängig davon Kosten, ob dafür die ExpressRoute- oder die Site-to-Site-VPN-Verbindung genutzt wird.

- Falls Sie sich für die Verwendung der nativen Azure-Funktionen für den Serverlastenausgleich entscheiden, um die hohe Verfügbarkeit von AD FS-Servern sicherzustellen, sollten Sie Folgendes beachten: Beim Lastenausgleich wird anhand von Überprüfungen die Integrität der virtuellen Computer im Clouddienst ermittelt. Bei Azure Virtual Machines (im Gegensatz zu Web- oder Workerrollen) muss eine benutzerdefinierte Überprüfung verwendet werden, da der Agent, der auf die Standardüberprüfungen reagiert, auf Azure Virtual Machines nicht vorhanden ist. Der Einfachheit halber können Sie eine benutzerdefinierte TCP-Überprüfung verwenden. Hierfür muss lediglich eine TCP-Verbindung (TCP SYN-Segment, das gesendet und auf das mit einem TCP SYN ACK-Segment geantwortet wird) erfolgreich eingerichtet werden, um die Integrität virtueller Computer zu ermitteln. Sie können die benutzerdefinierte Überprüfung so konfigurieren, dass ein beliebiger TCP-Port verwendet wird, über den Ihre virtuellen Computer lauschen.

> [AZURE.NOTE] Computer, die dieselbe Gruppe von Ports direkt für das Internet verfügbar machen müssen (z. B. Port 80 und 443), können nicht gemeinsam denselben Clouddienst nutzen. Daher empfehlen wir Ihnen, einen dedizierten Clouddienst für Ihre Windows Server AD FS-Server zu erstellen, um potenzielle Überlappungen zwischen Portanforderungen für eine Anwendung und Windows Server AD FS zu vermeiden.

## Bereitstellungsszenarien

Im folgenden Abschnitt werden häufig verwendete Bereitstellungsszenarien beschrieben, um auf die wichtigen Aspekte hinzuweisen, die berücksichtigt werden müssen. Jedes Szenario verfügt über Links zu weiteren Details, die die jeweils relevanten Entscheidungen und Faktoren betreffen.

1. [AD DS: Bereitstellen einer AD DS-fähigen Anwendung ohne Erfordernis einer Verbindung mit dem Unternehmensnetzwerk](#BKMK_CloudOnly)

    Ein Beispiel ist, dass ein mit dem Internet verbundener SharePoint-Dienst auf einer Azure Virtual Machine bereitgestellt wird. Die Anwendung weist keine Abhängigkeiten von Ressourcen des Unternehmensnetzwerks auf. Für die Anwendung ist Windows Server AD DS erforderlich, aber NICHT die Windows Server AD DS-Instanz des Unternehmens.

2. [AD FS: Erweitern einer Ansprüche unterstützenden lokalen Front-End-Anwendung ins Internet](#BKMK_CloudOnlyFed)

    Es kann beispielsweise sein, dass eine Ansprüche unterstützende Anwendung, die lokal erfolgreich bereitgestellt wurde und von Unternehmensanwendern genutzt wird, über das Internet erreichbar sein soll. Auf die Anwendung müssen sowohl Geschäftspartner mit ihren eigenen Unternehmensidentitäten als auch vorhandene Unternehmensanwender direkt über das Internet zugreifen können.

3. [AD DS: Bereitstellen einer Windows Server AD DS-fähigen Anwendung mit Erfordernis einer Verbindung mit dem Unternehmensnetzwerk](#BKMK_HybridExt)

    Ein Beispiel hierfür ist eine LDAP-fähige Anwendung, die die in Windows integrierte Authentifizierung unterstützt und Windows Server AD DS als Repository für die Konfiguration und Benutzerprofildaten verwendet und auf einer Azure Virtual Machine bereitgestellt wird. Für die Anwendung ist es wünschenswert, die vorhandene Windows Server AD DS-Instanz des Unternehmens zu nutzen und das einmalige Anmelden bereitzustellen. Die Anwendung unterstützt keine Ansprüche.

### <a name="BKMK_CloudOnly"></a>1. AD DS: Bereitstellen einer AD DS-fähigen Anwendung ohne Erfordernis einer Verbindung mit dem Unternehmensnetzwerk

![Bereitstellung von AD DS nur in der Cloud](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png) **Abbildung 1**

#### Beschreibung

SharePoint wird auf einer Azure Virtual Machine bereitgestellt, und die Anwendung weist keine Abhängigkeiten von Ressourcen des Unternehmensnetzwerks auf. Für die Anwendung ist Windows Server AD DS erforderlich, aber *nicht* die Windows Server AD DS-Instanz des Unternehmens. Weder Kerberos- noch Verbundvertrauensstellungen sind erforderlich, da Benutzer von der Anwendung selbst in der Windows Server AD DS-Domäne bereitgestellt werden, die in der Cloud ebenfalls auf Azure Virtual Machines gehostet wird.

#### Szenarioaspekte und Gültigkeit von Technologiebereichen für das Szenario

- [Netzwerktopologie:](#BKMK_NetworkTopology) Erstellen Sie ein Azure Virtual Network ohne standortübergreifende Konnektivität (auch als Site-to-Site-Konnektivität bezeichnet).

- [DC-Bereitstellungskonfiguration:](#BKMK_DeploymentConfig) Stellen Sie einen neuen Domänencontroller in einer neuen Windows Server Active Directory-Gesamtstruktur mit einer einzelnen Domäne bereit. Dieser sollte zusammen mit dem Windows DNS-Server bereitgestellt werden.

- [Windows Server Active Directory-Standorttopologie:](#BKMK_ADSiteTopology) Verwenden Sie den Windows Server Active Directory-Standardstandort (alle Computer befinden sich unter Default-First-Site-Name).

- [IP-Adressierung und DNS:](#BKMK_IPAddressDNS)

 - Legen Sie eine statische IP-Adresse für den DC fest, indem Sie das Azure PowerShell-Cmdlet Set-AzureStaticVNetIP verwenden.
 - Installieren und konfigurieren Sie Windows Server-DNS auf dem bzw. den Domänencontrollern in Azure.
 - Konfigurieren Sie die Eigenschaften des virtuellen Netzwerks mit dem Namen und der IP-Adresse der VM, von der der Domänencontroller und die DNS-Serverrollen gehostet werden.

- [Globaler Katalog:](#BKMK_GC) Der erste Domänencontroller in der Gesamtstruktur muss ein globaler Katalogserver sein. Weitere Domänencontroller (DCs) sollten auch als globale Kataloge (GCs) konfiguriert werden, da der globale Katalog in einer einzelnen Domänengesamtstruktur keine weiteren Arbeitsschritte vom DC benötigt.

- [Platzierung der Windows Server AD DS-Datenbank und von SYSVOL:](#BKMK_PlaceDB) Fügen Sie DCs, die als Azure VMs ausgeführt werden, einen Datenträger hinzu, um die Windows Server Active Directory-Datenbank, Protokolle und SYSVOL zu speichern.

- [Sichern und Wiederherstellen:](#BKMK_BUR) Bestimmen Sie, wo Sie Sicherungen des Systemstatus speichern möchten. Fügen Sie der DC-VM einen weiteren Datenträger zum Speichern von Sicherungen hinzu, falls dies erforderlich ist.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS: Erweitern einer Ansprüche unterstützenden lokalen Front-End-Anwendung ins Internet

![Verbund mit standortübergreifender Konnektivität](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png) **Abbildung 2**

#### Beschreibung

Eine Ansprüche unterstützende Anwendung, die lokal erfolgreich bereitgestellt wurde und von Unternehmensanwendern genutzt wird, soll direkt über das Internet erreichbar sein. Die Anwendung dient als Web-Front-End für eine SQL-Datenbank, in der die dazugehörigen Daten gespeichert werden. Die von der Anwendung verwendeten SQL-Server befinden sich ebenfalls im Unternehmensnetzwerk. Zwei Windows Server AD FS-Sicherheitstokendienste und ein Lastenausgleichsmodul wurden lokal bereitgestellt, um den Zugriff auf die Unternehmensanwender zu ermöglichen. Auf die Anwendung müssen nun zusätzlich sowohl Geschäftspartner mit ihren eigenen Unternehmensidentitäten als auch vorhandene Unternehmensanwender direkt über das Internet zugreifen können.

Um die Bereitstellungs- und Konfigurationsanforderungen dieser neuen Aufgabenstellung zu vereinfachen und zu erfüllen, wird entschieden, dass zwei zusätzliche Web-Front-Ends und zwei Windows Server AD FS-Proxyserver auf Azure Virtual Machines installiert werden. Alle vier VMs werden direkt über das Internet verfügbar gemacht und mit einer Verbindung mit dem lokalen Netzwerk versehen, indem die Funktion für die Site-to-Site-VPN-Verbindung des Azure Virtual Network verwendet wird.

#### Szenarioaspekte und Gültigkeit von Technologiebereichen für das Szenario

- [Netzwerktopologie:](#BKMK_NetworkTopology) Erstellen Sie ein Azure Virtual Network, und [konfigurieren Sie die standortübergreifende Verbindung](../vpn-gateway/vpn-gateway-site-to-site-create.md).

 > [AZURE.NOTE] Stellen Sie für alle Windows Server AD FS-Zertifikate jeweils sicher, dass die in der Zertifikatvorlage definierte URL und die sich daraus ergebenden Zertifikate von den Windows Server AD FS-Instanzen erreicht werden können, die in Azure ausgeführt werden. Hierfür ist unter Umständen eine standortübergreifende Verbindung mit Teilen Ihrer PKI-Infrastruktur erforderlich. Falls der Endpunkt der Zertifikatsperrliste (CRL) LDAP-basiert ist und ausschließlich lokal gehostet wird, ist eine standortübergreifende Verbindung erforderlich. Wenn dies nicht erwünscht ist, müssen Sie unter Umständen Zertifikate verwenden, die von einer Zertifizierungsstelle (ZS) ausgegeben werden, deren Zertifikatsperrliste über das Internet zugänglich ist.

- [Clouddienstkonfiguration:](#BKMK_CloudSvcConfig) Stellen Sie sicher, dass Sie über zwei Clouddienste verfügen, damit Sie zwei virtuelle IP-Adressen mit Lastenausgleich bereitstellen können. Die virtuelle IP-Adresse des ersten Clouddiensts wird an die beiden Windows Server AD FS-Proxy-VMs an den Ports 80 und 443 geleitet. Die Windows Server AD FS-Proxy-VMs werden so konfiguriert, dass sie auf die IP-Adresse des lokalen Lastenausgleichs zeigen, der den Windows Server AD FS-Sicherheitstokendiensten vorgeschaltet ist. Die virtuelle IP-Adresse des zweiten Clouddiensts wird an die beiden VMs geleitet, und das Web-Front-End wird wieder an den Ports 80 und 443 ausgeführt. Konfigurieren Sie eine benutzerdefinierte Überprüfung, um sicherzustellen, dass der Lastenausgleich Datenverkehr nur an funktionierende Windows Server AD FS-Proxy- und Web-Front-End-VMs weiterleitet.

- [Verbundserverkonfiguration:](#BKMK_FedSrvConfig) Konfigurieren Sie Windows Server AD FS als Verbundserver (Sicherheitstokendienst), um Sicherheitstoken für die in der Cloud erstellte Windows Server Active Directory-Gesamtstruktur zu generieren. Legen Sie Verbundanspruchsanbieter-Vertrauensstellungen für die unterschiedlichen Partner fest, von denen Sie Identitäten akzeptieren möchten, und konfigurieren Sie Vertrauensstellungen der vertrauenden Seite für die unterschiedlichen Anwendungen, für die Token generiert werden sollen.

    In den meisten Fällen werden Windows Server AD FS-Proxyserver aus Sicherheitsgründen als über das Internet erreichbare Einheiten bereitgestellt, während die dazugehörigen Windows Server AD FS-Verbundgegenstücke von der direkten Internetverbindung isoliert bleiben. Unabhängig von Ihrem Bereitstellungsszenario müssen Sie Ihren Clouddienst mit einer virtuellen IP-Adresse konfigurieren, über die eine öffentlich verfügbar gemachte IP-Adresse und ein Port bereitgestellt werden. Mit dem Port muss es möglich sein, den Lastenausgleich über Ihre beiden Windows Server AD FS-STS-Instanzen oder Proxyinstanzen durchzuführen.

- [Windows Server AD FS-Konfiguration für hohe Verfügbarkeit:](#BKMK_ADFSHighAvail) Es ist ratsam, eine Windows Server AD FS-Farm mit mindestens zwei Servern für Failover- und Lastenausgleichszwecke bereitzustellen. Sie können erwägen, die interne Windows-Datenbank (Windows Internal Database, WID) für Windows Server AD FS-Konfigurationsdaten zu verwenden, und die interne Funktion für den Lastenausgleich von Azure nutzen, um eingehende Anforderungen auf die Server der Farm zu verteilen.

Weitere Informationen finden Sie im [AD DS Deployment Guide](https://technet.microsoft.com/library/cc753963) (Leitfaden zur AD DS-Bereitstellung).


### <a name="BKMK_HybridExt"></a>3. AD DS: Bereitstellen einer Windows Server AD DS-fähigen Anwendung mit Erfordernis einer Verbindung mit dem Unternehmensnetzwerk

![Standortübergreifende AD DS-Bereitstellung](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png) **Abbildung 3**

#### Beschreibung

Eine LDAP-fähige Anwendung wird auf einer Azure Virtual Machine bereitgestellt. Sie unterstützt die integrierte Windows-Authentifizierung und nutzt Windows Server AD DS als Repository für Konfigurations- und Benutzerprofildaten. Für die Anwendung soll erreicht werden, dass die vorhandene Windows Server AD DS-Instanz des Unternehmens genutzt und das einmalige Anmelden bereitgestellt wird. Die Anwendung unterstützt keine Ansprüche. Außerdem müssen die Benutzer direkt über das Internet auf die Anwendung zugreifen. Um die Leistung und Kosten zu optimieren, wird entschieden, dass zwei weitere Domänencontroller, die Teil der Unternehmensdomäne sind, zusätzlich zur Anwendung in Azure bereitgestellt werden.

#### Szenarioaspekte und Gültigkeit von Technologiebereichen für das Szenario

- [Netzwerktopologie:](#BKMK_NetworkTopology) Erstellen Sie ein Azure Virtual Network mit [standortübergreifender Verbindung](../vpn-gateway/vpn-gateway-site-to-site-create.md).

- [Installationsmethode:](#BKMK_InstallMethod) Stellen Sie Replikatdomänencontroller aus der Windows Server Active Directory-Domäne des Unternehmens bereit. Für einen Replikatdomänencontroller können Sie Windows Server AD DS auf der VM installieren und bei Bedarf das Feature „Installieren von Medium“ (Install From Media, IFM) nutzen, um die Datenmenge zu reduzieren, die während der Installation für den neuen Domänencontroller repliziert werden muss. Ein Tutorial finden Sie unter [Installieren eines Active Directory-Replikatdomänencontrollers in Azure](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md). Auch bei Verwendung von „Installieren von Medium“ kann es effizienter sein, den virtuellen Domänencontroller lokal zu erstellen und die gesamte virtuelle Festplatte (Virtual Hard Disk, VHD) in die Cloud zu verschieben, anstatt Windows Server AD DS während der Installation zu replizieren. Aus Sicherheitsgründen wird empfohlen, die VHD aus dem lokalen Netzwerk zu löschen, nachdem sie in Azure kopiert wurde.

- [Windows Server Active Directory-Standorttopologie:](#BKMK_ADSiteTopology) Erstellen Sie einen neuen Azure-Standort unter Active Directory-Standorte und -Dienste. Erstellen Sie ein Windows Server Active Directory-Subnetzobjekt, mit dem das Azure Virtual Network dargestellt wird, und fügen Sie das Subnetz dem Standort hinzu. Erstellen Sie eine neue Standortverknüpfung, die den neuen Azure-Standort und den Standort enthält, an dem sich der VPN-Endpunkt des Azure Virtual Network befindet. So können Sie Windows Server Active Directory-Datenverkehr an und von Azure steuern und optimieren.

- [IP-Adressierung und DNS:](#BKMK_IPAddressDNS)

 - Legen Sie eine statische IP-Adresse für den DC fest, indem Sie das Azure PowerShell-Cmdlet Set-AzureStaticVNetIP verwenden.
 - Installieren und konfigurieren Sie Windows Server-DNS auf dem bzw. den Domänencontrollern in Azure.
 - Konfigurieren Sie die Eigenschaften des virtuellen Netzwerks mit dem Namen und der IP-Adresse der VM, von der der Domänencontroller und die DNS-Serverrollen gehostet werden.

- [Geografisch verteilte Domänencontroller:](#BKMK_DistributedDCs) Konfigurieren Sie zusätzliche virtuelle Netzwerke nach Bedarf. Falls für Ihre Active Directory-Standorttopologie Domänencontroller an Standorten in anderen Azure-Regionen benötigt werden, müssen Sie entsprechende Active Directory-Standorte erstellen.

- [Schreibgeschützte Domänencontroller (RODCs):](#BKMK_RODC) Es kann sinnvoll sein, einen RODC am Azure-Standort bereitzustellen. Dies hängt von den Anforderungen in Bezug auf die Durchführung von Schreibvorgängen für den Domänencontroller und der Kompatibilität von Anwendungen und Diensten am Standort mit RODCs ab. Weitere Informationen zur Anwendungskompatibilität finden Sie unter [Anwendungskompatibilität mit Domänencontrollern ohne Schreibzugriff](https://technet.microsoft.com/library/cc755190).

- [Globaler Katalog:](#BKMK_GC) Globale Kataloge (GCs) werden benötigt, um Anmeldeanforderungen in Gesamtstrukturen mit mehreren Domänen zu warten. Wenn Sie für den Azure-Standort keinen GC bereitstellen, fallen Kosten für ausgehenden Datenverkehr an, da Authentifizierungsanforderungen Abfragen von GCs an anderen Standorten verursachen. Zur Reduzierung dieses Datenverkehrs können Sie für den Azure-Standort in Active Directory-Standorte und -Dienste das Caching von universellen Gruppenmitgliedschaften aktivieren.

    Konfigurieren Sie Standortverknüpfungen und die Kosten für Standortverknüpfungen beim Bereitstellen eines GC so, dass der GC an den Azure-Standorten nicht von anderen GCs als Quelldomänencontroller vorgezogen wird, von denen die gleichen Teildomänenpartitionen repliziert werden müssen.

- [Platzierung der Windows Server AD DS-Datenbank und von SYSVOL:](#BKMK_PlaceDB) Fügen Sie DCs, die auf Azure VMs ausgeführt werden, einen Datenträger hinzu, um die Windows Server Active Directory-Datenbank, Protokolle und SYSVOL zu speichern.

- [Sichern und Wiederherstellen:](#BKMK_BUR) Bestimmen Sie, wo Sie Sicherungen des Systemstatus speichern möchten. Fügen Sie der DC-VM einen weiteren Datenträger zum Speichern von Sicherungen hinzu, falls dies erforderlich ist.

## Bereitstellungsentscheidungen und -faktoren

In dieser Tabelle sind die Technologiebereiche von Windows Server Active Directory, die in den obigen Szenarien betroffen sind, und die entsprechenden Entscheidungen zusammengefasst, die berücksichtigt werden müssen. Darunter sind jeweils Links zu ausführlicheren Informationen angegeben. Einige Technologiebereiche gelten ggf. nicht für jedes Bereitstellungsszenario, und einige Bereiche sind für ein Bereitstellungsszenario unter Umständen wichtiger als andere.

Wenn Sie beispielsweise einen Replikatdomänencontroller in einem virtuellen Netzwerk bereitstellen und Ihre Gesamtstruktur nur eine einzelne Domäne hat, ist die Entscheidung für die Bereitstellung eines globalen Katalogservers in diesem Fall nicht von großer Bedeutung für das Bereitstellungsszenario, weil sich keine zusätzlichen Replikationsanforderungen ergeben. Falls die Gesamtstruktur aber über mehrere Domänen verfügt, kann sich die Entscheidung für die Bereitstellung eines globalen Katalogs in einem virtuellen Netzwerk auf die verfügbare Bandbreite, Leistung, Authentifizierung, Verzeichnissuchen usw. auswirken.

| Windows Server Active Directory-Technologiebereich | Entscheidungen | Faktoren |
| ---- | ---- | ---- |
| [Netzwerktopologie](#BKMK_NetworkTopology) | Erstellen Sie ein virtuelles Netzwerk? | <li>Anforderungen für den Zugriff auf Unternehmensressourcen</li> <li>Authentifizierung</li> <li>Kontenverwaltung</li> |
| [DC-Bereitstellungskonfiguration](#BKMK_DeploymentConfig) | <li>Separate Gesamtstruktur ohne Vertrauensstellungen bereitstellen?</li> <li>Neue Gesamtstruktur mit Verbund bereitstellen?</li> <li>Neue Gesamtstruktur mit Windows Server Active Directory-Gesamtstruktur-Vertrauensstellung oder Kerberos bereitstellen?</li> <li>Erweitern der Unternehmensgesamtstruktur per Bereitstellung eines Replikatdomänencontrollers?</li> <li>Erweitern der Unternehmensgesamtstruktur per Bereitstellung einer neuen untergeordneten Domäne oder einer Domänenstruktur?</li> | <li>Sicherheit</li> <li>Compliance</li> <li>Kosten</li> <li>Resilienz und Fehlertoleranz</li> <li>Anwendungskompatibilität</li> |
| [Windows Server Active Directory-Standorttopologie](#BKMK_ADSiteTopology) | Wie können Subnetze, Standorte und Standortverknüpfungen mit Azure Virtual Network konfiguriert werden, um den Datenverkehr zu optimieren und die Kosten zu reduzieren? | <li>Subnetz- und Standortdefinitionen</li> <li>Eigenschaften von Standortverknüpfungen und Änderungsbenachrichtigung</li> <li>Replikationskomprimierung</li> |
| [IP-Adressierung und DNS:](#BKMK_IPAddressDNS) | Wie werden IP-Adressen und die Namensauflösung konfiguriert? | <li>Verwenden des Set-AzureStaticVNetIP-Cmdlets zum Zuweisen einer statischen IP-Adresse</li> <li>Installieren des Windows Server-DNS-Servers und Konfigurieren der Eigenschaften des virtuellen Netzwerks mit dem Namen und der IP-Adresse der VM, auf der der DC und die DNS-Serverrollen gehostet werden</li> |
| [Geografisch verteilte DCs](#BKMK_DistributedDCs) | Wie werden DCs in getrennten virtuellen Netzwerken repliziert? | Falls für Ihre Active Directory-Standorttopologie Domänencontroller an Standorten in anderen Azure-Regionen benötigt werden, müssen Sie entsprechende Active Directory-Standorte erstellen. [Konfigurieren Sie die Verbindung von virtuellem Netzwerk zu virtuellem Netzwerk](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md), um die Replikation zwischen Domänencontrollern in separaten virtuellen Netzwerken durchzuführen. |
| [Schreibgeschützte DCs](#BKMK_RODC) | Sollen schreibgeschützte oder beschreibbare DCs verwendet werden? | <li>Filtern von HBI/PII-Attributen</li> <li>Filtern von geheimen Schlüsseln</li> <li>Beschränken des ausgehenden Datenverkehrs</li> |
| [Globaler Katalog](#BKMK_GC) | Soll der globale Katalog installiert werden? | <li>Für eine Gesamtstruktur mit einer einzelnen Domäne: Alle DCs zu GCs machen</li> <li>Für Gesamtstrukturen mit mehreren Domänen: GCs werden für die Authentifizierung benötigt</li> |
| [Installationsmethode](#BKMK_InstallMethod) | Wie wird ein DC in Azure installiert? | Optionen: <li>Installieren von AD DS mit Windows PowerShell oder Dcpromo</li> <li>Verschieben der VHD eines lokalen virtuellen DC</li> |
| [Platzierung der Windows Server AD DS-Datenbank und von SYSVOL](#BKMK_PlaceDB) | Wo sollen die Windows Server AD DS-Datenbank, Protokolle und SYSVOL gespeichert werden? | Ändern Sie die Standardwerte von „Dcpromo.exe“. Diese wichtigen Active Directory-Dateien *müssen* auf Azure-Datenträgern platziert werden, anstatt auf Betriebssystem-Datenträgern, die den Schreibcache implementieren. |
| [Sichern und Wiederherstellen](#BKMK_BUR) | Wie werden Daten geschützt und wiederhergestellt? | Erstellen von Sicherungen des Systemstatus |
| [Verbundserverkonfiguration](#BKMK_FedSrvConfig) | <li>Neue Gesamtstruktur mit Verbund in der Cloud bereitstellen?</li> <li>AD FS lokal bereitstellen und einen Proxy in der Cloud verfügbar machen?</li> | <li>Sicherheit</li> <li>Compliance</li> <li>Kosten</li> <li>Zugriff auf Anwendungen durch Geschäftspartner</li> |
| [Konfiguration der Clouddienste](#BKMK_CloudSvcConfig) | Ein Clouddienst wird implizit bereitgestellt, wenn Sie zum ersten Mal einen virtuellen Computer erstellen. Müssen Sie zusätzliche Clouddienste bereitstellen? | <li>Müssen eine oder mehrere VMs direkt für den Zugriff über das Internet verfügbar gemacht werden?</li> <li> Ist für den Dienst ein Lastenausgleich erforderlich?</li> |
| [Anforderungen von Verbundservern für die öffentliche und private IP-Adressierung (dynamische IP und virtuelle IP)](#BKMK_FedReqVIPDIP) | <li>Muss die Windows Server AD FS-Instanz direkt über das Internet erreichbar sein?</li> <li>Benötigt die Anwendung, die in der Cloud bereitgestellt wird, eine eigene IP-Adresse und einen Port für den Zugriff über das Internet?</li> | Erstellen eines Clouddiensts für jede virtuelle IP-Adresse, die für Ihre Bereitstellung erforderlich ist |
| [Konfiguration von Windows Server AD FS für hohe Verfügbarkeit](#BKMK_ADFSHighAvail) | <li>Wie viele Knoten sollte meine Windows Server AD FS-Serverfarm umfassen?</li> <li>Wie viele Knoten sollten in meiner Windows Server AD FS-Proxyfarm bereitgestellt werden?</li> | Resilienz und Fehlertoleranz |

### <a name="BKMK_NetworkTopology"></a>Netzwerktopologie

Um die Einheitlichkeit von IP-Adressen und die DNS-Anforderungen von Windows Server AD DS zu erfüllen, müssen Sie zuerst ein [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) erstellen und Ihre virtuellen Computer daran anfügen. Bei der Erstellung müssen Sie entscheiden, ob Sie die Konnektivität optional auf Ihr lokales Unternehmensnetzwerk erweitern, um Azure Virtual Machines auf transparente Weise mit lokalen Computern zu verbinden. Dies wird mit herkömmlichen VPN-Technologien erreicht und ist mit der Anforderung verbunden, dass im Edgebereich des Unternehmensnetzwerks ein VPN-Endpunkt verfügbar gemacht wird. Das VPN wird also von Azure zum Unternehmensnetzwerk initiiert (nicht umgekehrt).

Beachten Sie, dass zusätzliche Gebühren anfallen, wenn Sie ein virtuelles Netzwerk auf Ihre lokales Netzwerk erweitern und dies über die Standardgebühren hinausgeht, die für jeden virtuellen Computer gelten. Es fallen Kosten für die CPU-Zeit des Azure Virtual Network-Gateways und für den ausgehenden Datenverkehr der einzelnen VMs an, die mit lokalen Computern per VPN kommunizieren. Weitere Informationen zu Gebühren für Netzwerk-Datenverkehr finden Sie unter [Azure-Preise](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Konfiguration der DC-Bereitstellung

Die Art und Weise, wie Sie den DC konfigurieren können, hängt von den Anforderungen für den Dienst ab, den Sie unter Azure ausführen möchten. Beispielsweise können Sie eine neue Gesamtstruktur bereitstellen, die von Ihrer eigenen Unternehmensgesamtstruktur isoliert ist, um eine Machbarkeitsstudie, eine neue Anwendung oder ein anderes kurzfristiges Projekt zu testen, für das Verzeichnisdienste benötigt werden, aber kein spezieller Zugriff auf interne Unternehmensressourcen.

Der Vorteil hierbei ist, dass ein isolierter Gesamtstruktur-Domänencontroller keine Replikation mit lokalen DCs durchführt. Dies führt dazu, dass vom System selbst weniger ausgehender Datenverkehr generiert wird und die Kosten direkt reduziert werden. Weitere Informationen zu Gebühren für Netzwerk-Datenverkehr finden Sie unter [Azure-Preise](http://azure.microsoft.com/pricing/).

Ein weiteres Beispiel: Angenommen, bei Ihnen bestehen Datenschutzanforderungen für einen Dienst, aber für den Dienst ist der Zugriff auf Ihre interne Windows Server Active Directory-Instanz erforderlich. Wenn es zulässig ist, dass Sie Daten für den Dienst in der Cloud hosten, können Sie in Azure ggf. eine neue untergeordnete Domäne für Ihre interne Gesamtstruktur bereitstellen. In diesem Fall können Sie einen DC für die neue untergeordnete Domäne bereitstellen (ohne den globalen Katalog als Unterstützung bei der Lösung von Datenschutzproblemen). Für dieses Szenario in Verbindung mit der Bereitstellung eines Replikatdomänencontrollers ist ein virtuelles Netzwerk erforderlich, um die Verbindung mit Ihren lokalen DCs zu realisieren.

Wählen Sie beim Erstellen einer neuen Gesamtstruktur aus, ob [Active Directory-Vertrauensstellungen](https://technet.microsoft.com/library/cc771397) oder [Verbundvertrauensstellungen](https://technet.microsoft.com/library/dd807036) verwendet werden sollen. Streben Sie eine gute Balance der Anforderungen in Bezug auf Kompatibilität, Sicherheit, Compliance, Kosten und Resilienz an. Falls Sie beispielsweise die [selektive Authentifizierung](https://technet.microsoft.com/library/cc755844) nutzen möchten, können Sie sich für die Bereitstellung einer neuen Gesamtstruktur in Azure entscheiden und eine Windows Server Active Directory-Vertrauensstellung zwischen der lokalen Gesamtstruktur und der Cloudgesamtstruktur erstellen. Wenn die Anwendung aber Ansprüche unterstützt, können Sie anstelle von Active Directory-Gesamtstruktur-Vertrauensstellungen auch Verbundvertrauensstellungen bereitstellen. Ein weiterer Faktor sind die Kosten für die Replikation einer größeren Menge von Daten, indem Sie die lokale Windows Server Active Directory-Instanz in die Cloud erweitern oder indem Sie aufgrund der Authentifizierungs- und Abfragelast mehr ausgehenden Datenverkehr generieren.

Die Anforderungen in Bezug auf die Verfügbarkeit und Fehlertoleranz wirken sich ebenfalls auf Ihre Wahl aus. Wenn beispielsweise die Verknüpfung unterbrochen ist, sind alle Anwendungen, für die entweder eine Kerberos-Vertrauensstellung oder eine Verbundvertrauensstellung genutzt wird, mit großer Wahrscheinlichkeit vollständig funktionsunfähig, es sei denn, Sie haben in Azure eine ausreichende Infrastruktur bereitgestellt. Alternative Bereitstellungskonfigurationen, z. B. Replikatdomänencontroller (beschreibbar oder schreibgeschützt), erhöhen die Wahrscheinlichkeit, dass Verknüpfungsausfälle toleriert werden können.

### <a name="BKMK_ADSiteTopology"></a>Windows Server Active Directory-Standorttopologie

Sie müssen Standorte und Standortverknüpfungen richtig definieren, um den Datenverkehr zu optimieren und die Kosten zu reduzieren. Standorte, Standortverknüpfungen und Subnetze wirken sich auf die Replikationstopologie zwischen DCs und dem Fluss des Datenverkehrs für die Authentifizierung aus. Sehen Sie sich die folgenden Kosten an, und stellen Sie DCs dann gemäß den Anforderungen Ihres Bereitstellungsszenarios bereit, und konfigurieren Sie sie:

- Für das eigentliche Gateway wird eine Grundgebühr pro Stunde berechnet:

 - Es kann je nach Bedarf gestartet und beendet werden.

 - Wenn es beendet wird, sind Azure VMs vom Unternehmensnetzwerk isoliert.

- Eingehender Datenverkehr ist kostenfrei.

- Ausgehender Datenverkehr wird gemäß den Angaben unter [Azure-Preise](http://azure.microsoft.com/pricing/) berechnet. Sie können die Eigenschaften von Standortverknüpfungen zwischen lokalen Standorten und Cloudstandorten wie folgt optimieren:

 - Konfigurieren Sie die Standortverknüpfungen und deren Kosten bei Verwendung von mehreren virtuellen Netzwerken entsprechend, um zu verhindern, dass Windows Server AD DS dem Azure-Standort Vorrang gegenüber einem Standort einräumt, der das gleiche Servicelevel kostenfrei bereitstellen kann. Es kann auch ratsam sein, die Option „Bridge all site link“ (BASL) zu deaktivieren (sie ist standardmäßig aktiviert). So wird sichergestellt, dass nur direkt verbundene Standorte miteinander repliziert werden. Für DCs an transitiv verbundenen Standorten ist die direkte Replikation miteinander nicht mehr möglich, sondern nur noch über einen oder mehrere gemeinsame Standorte. Falls die Standorte dazwischen aus irgendeinem Grund nicht mehr verfügbar sind, wird die Replikation zwischen DCs an transitiv verbundenen Standorten auch dann nicht durchgeführt, wenn die Konnektivität zwischen den Standorten vorhanden ist. Wo Abschnitte mit transitivem Replikationsverhalten wünschenswert sind, sollten Sie Brücken für Standortverknüpfungen erstellen, die die entsprechenden Standortverknüpfungen und Standorte enthalten, z. B. lokale Unternehmensnetzwerk-Standorte.

 - [Konfigurieren Sie die Kosten für Standortverknüpfungen](https://technet.microsoft.com/library/cc794882) in geeigneter Weise, um unbeabsichtigten Datenverkehr zu vermeiden. Wenn beispielsweise die Einstellung **Am nächstgelegenen Standort suchen** aktiviert ist, sollten Sie sicherstellen, dass es nicht die Standorte des virtuellen Netzwerks sind, die am nächsten liegen. Erhöhen Sie hierfür die Kosten, die dem Standortverknüpfungsobjekt zugeordnet sind, mit dem die Rückverbindung des Azure-Standorts mit dem Unternehmensnetzwerk realisiert wird.

 - Konfigurieren Sie [Intervalle](https://technet.microsoft.com/library/cc794878) und [Zeitpläne](https://technet.microsoft.com/library/cc816906) von Standortverknüpfungen gemäß den Konsistenzanforderungen und der Rate der Objektänderungen. Richten Sie den Replikationszeitplan an der Latenztoleranz aus. Bei DCs wird nur der letzte Zustand eines Werts repliziert. Das Verringern des Replikationsintervalls kann also Kosten sparen, sofern eine ausreichende Objektänderungsrate vorhanden ist.

- Falls die Reduzierung der Kosten Priorität hat, sollten Sie sicherstellen, dass die Replikation geplant und die Änderungsbenachrichtigung nicht aktiviert ist. Dies ist die Standardkonfiguration für die Replikation zwischen Standorten. Dies ist nicht wichtig, wenn Sie einen schreibgeschützten Domänencontroller (RODC) in einem virtuellen Netzwerk bereitstellen, da der RODC keine Änderungen in ausgehender Richtung repliziert. Falls Sie aber einen beschreibbaren DC bereitstellen, sollten Sie sicherstellen, dass die Standortverknüpfung nicht so konfiguriert ist, dass Updates mit unnötiger Häufigkeit repliziert werden. Stellen Sie beim Bereitstellen eines globalen Katalogservers (GC) Folgendes sicher: Jeder zweite Standort mit einem GC repliziert Domänenpartitionen von einem Quell-DC an einem Standort, der mit einer oder mehreren Standortverknüpfungen verbunden ist, die geringere Kosten als der GC am Azure-Standort verursachen.


- Es ist möglich, den Netzwerk-Datenverkehr noch weiter zu reduzieren, der bei der Replikation zwischen Standorten generiert wird, indem der Komprimierungsalgorithmus für die Replikation geändert wird. Der Komprimierungsalgorithmus wird mit dem REG\_DWORD-Registrierungseintrag „HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\NTDS\\Parameters\\Replicator compression algorithm“ gesteuert. Der Standardwert ist 3, was dem Xpress-Komprimierungsalgorithmus entspricht. Sie können den Wert in 2 ändern, um für den Algorithmus zu MSZip zu wechseln. In den meisten Fällen wird die Komprimierung hierdurch erhöht. Dies geschieht aber auf Kosten der CPU-Auslastung. Weitere Informationen finden Sie unter [How Active Directory Replication Topology Works](https://technet.microsoft.com/library/cc755994) (Funktionsweise der Active Directory-Replikationstopologie).

### <a name="BKMK_IPAddressDNS"></a>IP-Adressierung und DNS

Azure Virtual Machines werden standardmäßig „Adressen mit DHCP-Lease“ zugeordnet. Da dynamische Azure Virtual Network-Adressen einem virtuellen Computer über seine gesamte Lebensdauer hinweg zugeordnet bleiben, werden die Anforderungen von Windows Server AD DS erfüllt.

Wenn Sie in Azure eine dynamische Adresse verwenden, nutzen Sie daher praktisch eine statische IP-Adresse. Dies liegt daran, dass sie für den Lease-Zeitraum geroutet werden kann und der Lease-Zeitraum der Lebensdauer des Clouddiensts entspricht.

Die Zuordnung der dynamischen Adresse wird aber aufgehoben, wenn der virtuelle Computer heruntergefahren wird. Um das Aufheben der Zuordnung für die IP-Adresse zu verhindern, können Sie [Set-AzureStaticVNetIP zum Zuweisen einer statischen IP-Adresse verwenden](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Stellen Sie für die Namensauflösung eine eigene DNS-Serverinfrastruktur bereit (oder nutzen Sie die vorhandene Infrastruktur). Der von Azure bereitgestellte DNS erfüllt die erweiterten Anforderungen an die Namensauflösung von Windows Server AD DS nicht. Beispielsweise werden keine dynamischen SRV-Einträge usw. unterstützt. Die Namensauflösung ist ein wichtiges Konfigurationselement für DCs und für der Domäne beigetretene Clients. DCs müssen dazu in der Lage sein, Ressourceneinträge zu registrieren und andere DC-Ressourceneinträge aufzulösen. Aus Gründen der Fehlertoleranz und aus Leistungsgründen ist es optimal, den Windows Server-DNS-Dienst auf den DCs zu installieren, die unter Azure ausgeführt werden. Konfigurieren Sie anschließend die Azure Virtual Network-Eigenschaften mit dem Namen und der IP-Adresse des DNS-Servers. Wenn andere VMs im virtuellen Netzwerk gestartet werden, werden deren Einstellungen für die DNS-Clientauflösung mit dem DNS-Server als Teil der Zuordnung einer dynamischen IP-Adresse konfiguriert.

> [AZURE.NOTE] Sie können für lokale Computer den Beitritt zu einer Active Directory-Domäne von Windows Server AD DS, die in Azure gehostet wird, nicht direkt über das Internet durchführen. Die Portanforderungen für Active Directory und den Vorgang für den Domänenbeitritt machen es unmöglich, die erforderlichen Ports und somit auch einen gesamten DC direkt über das Internet verfügbar zu machen.

VMs registrieren ihren DNS-Namen automatisch beim Start oder bei einer Namensänderung.

Weitere Informationen zu diesem Beispiel und ein weiteres Beispiel, das veranschaulicht, wie Sie die erste VM bereitstellen und darauf AD DS installieren, finden Sie unter [Installieren einer neuen Active Directory-Gesamtstruktur unter Microsoft Azure](active-directory-new-forest-virtual-machine.md). Weitere Informationen zum Verwenden von Windows PowerShell finden Sie unter [Installieren von Azure PowerShell](../powershell-install-configure.md) und [Azure-Cmdlets für die Verwaltung](https://msdn.microsoft.com/library/azure/jj152841).

### <a name="BKMK_DistributedDCs"></a>Geografisch verteilte DCs

Azure bietet Vorteile, wenn mehrere DCs in unterschiedlichen virtuellen Netzwerken gehostet werden:

- Fehlertoleranz für mehrere Standorte

- Physische Nähe zu Zweigniederlassungen (geringere Latenz)

Informationen zum Konfigurieren der direkten Kommunikation zwischen virtuellen Netzwerken finden Sie unter [Konfigurieren der Verbindung von virtuellem Netzwerk zu virtuellem Netzwerk](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Schreibgeschützte DCs

Sie müssen auswählen, ob Sie schreibgeschützte oder beschreibbare DCs bereitstellen. Unter Umständen neigen Sie zur Bereitstellung von schreibgeschützten Domänencontrollern (RODCs), da Sie keine physische Kontrolle darüber haben. RODCs sind aber für die Bereitstellung an Standorten ausgelegt, an denen ihre physische Sicherheit gefährdet ist, z. B. in Zweigniederlassungen.

Für Azure gilt das Risiko in Bezug auf die physische Sicherheit nicht wie bei einer Zweigniederlassung, aber RODCs sind ggf. trotzdem kostengünstiger, da die enthaltenen Features für diese Umgebungen gut geeignet sind, wenn auch aus anderen Gründen. Beispielsweise verfügen RODCs nicht über eine ausgehende Replikation und können geheime Schlüssel (Kennwörter) selektiv auffüllen. Ein Nachteil ist, dass das Fehlen dieser geheimen Schlüssel unter Umständen dazu führt, dass diese Angaben von bedarfsgesteuertem ausgehendem Datenverkehr überprüft werden müssen, wenn ein Benutzer oder Computer authentifiziert wird. Geheime Schlüssel können aber selektiv vorab aufgefüllt und zwischengespeichert werden.

RODCs haben im Zusammenhang mit HBI- und PII-Aspekten einen weiteren Vorteil, da Sie Attribute, die sensible Daten enthalten, dem Attributsatz mit RODC-Filter (FAS) hinzufügen können. Bei FAS handelt es sich um einen anpassbaren Satz von Attributen, die nicht zu RODCs repliziert werden. Sie können den FAS als Schutzmechanismus verwenden, falls Sie PII oder HBI nicht unter Azure speichern dürfen oder möchten. Weitere Informationen finden Sie unter [RODC Filtered Attribute Set[(https://technet.microsoft.com/library/cc753459)] (Attributsatz mit RODC-Filter).

Stellen Sie sicher, dass Anwendungen mit den RODCs kompatibel sind, die Sie verwenden möchten. Viele für Windows Server Active Directory geeignete Anwendungen funktionieren gut mit RODCs, aber bei einigen Anwendungen kann die Leistung unzureichend sein, oder es können Fehler auftreten, wenn kein Zugriff auf einen beschreibbaren DC besteht. Weitere Informationen finden Sie unter [Anwendungskompatibilität mit Domänencontrollern ohne Schreibzugriff](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Globaler Katalog

Sie müssen wählen, ob Sie einen globalen Katalog (GC) installieren. In einer Gesamtstruktur mit nur einer Domäne sollten Sie alle Domänencontroller als globale Katalogserver konfigurieren. Die Kosten erhöhen sich hierdurch nicht, da kein zusätzlicher Replikationsdatenverkehr anfällt.

In einer Gesamtstruktur mit mehreren Domänen werden GCs benötigt, um universelle Gruppenmitgliedschaften während des Authentifizierungsvorgangs zu erweitern. Wenn Sie keinen GC bereitstellen, generieren Workloads im virtuellen Netzwerk, die gegenüber einem DC unter Azure authentifiziert werden, indirekt ausgehenden Authentifizierungsdatenverkehr, um bei jedem Authentifizierungsversuch lokal GCs abzufragen.

Die Kosten, die mit GCs verbunden sind, sind weniger vorhersagbar, da hierbei jede Domäne (teilweise) gehostet wird. Wenn die Workload einen mit dem Internet verbundenen Dienst hostet und Benutzer gegenüber Windows Server AD DS authentifiziert, können die Kosten vollständig unvorhersagbar sein. Sie können das [Caching für universelle Gruppenmitgliedschaften aktivieren](https://technet.microsoft.com/library/cc816928), um während der Authentifizierung zur Reduzierung von GC-Abfragen außerhalb des Cloudstandorts beizutragen.

### <a name="BKMK_InstallMethod"></a>Installationsmethode

Sie müssen auswählen, wie die DCs im virtuellen Netzwerk installiert werden sollen:

- Stufen Sie neue DCs herauf. Weitere Informationen finden Sie unter [Installieren einer neuen Active Directory-Gesamtstruktur in einem virtuellen Azure-Netzwerk](active-directory-new-forest-virtual-machine.md).

- Verschieben Sie die virtuelle Festplatte eines lokalen virtuellen Domänencontrollers in die Cloud. In diesem Fall müssen Sie sicherstellen, dass der lokale virtuelle Domänencontroller „verschoben“ und nicht „kopiert“ oder „geklont“ wird.

Verwenden Sie nur Azure Virtual Machines für DCs (im Gegensatz zu Azure-Webrollen- oder -Workerrollen-VMs). Sie sind stabil, und ein stabiler Zustand ist für einen DC erforderlich. Azure Virtual Machines sind für Workloads ausgelegt, z. B. DCs.

Verwenden Sie nicht SYSPREP, um DCs bereitzustellen oder zu klonen. Die Möglichkeit zum Klonen von DCs ist erst ab Windows Server 2012 vorhanden. Für die Klonfunktion ist die Unterstützung von VMGenerationID im zugrunde liegenden Hypervisor erforderlich. Hyper-V in Windows Server 2012 und Azure Virtual Networks unterstützen jeweils VMGenerationID, wie dies auch bei Virtualisierungssoftware von Drittanbietern der Fall ist.

### <a name="BKMK_PlaceDB"></a>Platzierung der Windows Server AD DS-Datenbank und von SYSVOL

Wählen Sie aus, wo Sie die Windows Server AD DS-Datenbank, Protokolle und SYSVOL anordnen. Sie müssen auf Azure-Datenträgern bereitgestellt werden.

> [AZURE.NOTE] Die Größe von Azure-Datenträgern ist auf 1 TB beschränkt.

Auf Datenträgern werden Schreibvorgänge standardmäßig nicht zwischengespeichert. Datenträger, die an eine VM angefügt sind, verwenden einen Durchschreibcache (Write-Through Caching). Mit dem Durchschreibcache wird sichergestellt, dass für den Schreibvorgang ein Commit in den dauerhaften Azure-Speicher durchgeführt wird, bevor die Transaktion aus Sicht des VM-Betriebssystems abgeschlossen ist. Diese Dauerhaftigkeit wird auf Kosten von etwas verlangsamten Schreibvorgängen erzielt.

Dies ist für Windows Server AD DS wichtig, da beim verzögerten Datenträger-Caching (Write-Behind Disk-Caching) vom DC getroffene Annahmen ungültig werden. Windows Server AD DS versucht, den Schreibcache zu deaktivieren, aber es ist Aufgabe des Datenträger-E/A-Systems, dies zu regeln. Wenn Sie den Schreibcache deaktivieren, kann dies unter bestimmten Umständen zu einem USN-Rollback und somit zu veralteten Objekten und Problemen führen.

Verwenden Sie für virtuelle DCs die folgende bewährte Methode:

- Legen Sie unter „Hostcacheeinstellungen“ für den Azure-Datenträger KEINE fest. So lassen sich Probleme in Verbindung mit dem Schreibcache für AD DS-Vorgänge verhindern.

- Speichern Sie die Datenbank, Protokolle und das SYSVOL entweder auf demselben oder auf separaten Datenträgern. Dies ist normalerweise ein anderer Datenträger als der Datenträger, der für das eigentliche Betriebssystem verwendet wird. Die wichtigste Erkenntnis ist, dass die Windows Server AD DS-Datenbank und das SYSVOL nicht auf einem Azure-Datenträger für Betriebssysteme gespeichert werden müssen. Standardmäßig werden diese Komponenten vom AD DS-Installationsprozess im Ordner %systemroot% installiert. Dies ist für Azure NICHT zu empfehlen.

### <a name="BKMK_BUR"></a>Sichern und Wiederherstellen

Achten Sie darauf, was für die Sicherung und Wiederherstellung eines DC im Allgemeinen sowie eines in einem VM ausgeführten DC unterstützt bzw. nicht unterstützt wird. Weitere Informationen finden Sie unter [Überlegungen zum Sichern und Wiederherstellen virtualisierter Domänencontroller](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Erstellen Sie Sicherungen des Systemstatus, indem Sie nur Sicherungssoftware verwenden, die speziell für Sicherungsanforderungen für Windows Server AD DS geeignet ist, z. B. Windows Server-Sicherung.

Kopieren oder klonen Sie keine VHD-Dateien von DCs, anstatt regelmäßige Sicherungen durchzuführen. Sollte jemals eine Wiederherstellung erforderlich sein, ergeben sich USN-Blasen, wenn Sie hierfür geklonte oder kopierte VHDs ohne Windows Server 2012 und einen unterstützten Hypervisor verwenden.

### <a name="BKMK_FedSrvConfig"></a>Verbundserverkonfiguration

Die Konfiguration von Windows Server AD FS-Verbundservern (Sicherheitstokendiensten) ist teilweise davon abhängig, ob die Anwendungen, die Sie unter Azure bereitstellen möchten, auf Ressourcen in Ihrem lokalen Netzwerk zugreifen müssen.

Wenn die Anwendungen die folgenden Kriterien erfüllen, können Sie sie isoliert vom lokalen Netzwerk bereitstellen.

- SAML-Sicherheitstoken werden akzeptiert

- Sie können für das Internet verfügbar gemacht werden

- Sie greifen nicht auf lokale Ressourcen zu

Konfigurieren Sie Windows Server AD FS-Sicherheitstokendienste in diesem Fall wie folgt:

1. Konfigurieren Sie in Azure eine isolierte Gesamtstruktur mit nur einer Domäne.

2. Ermöglichen Sie den Verbundzugriff auf die Gesamtstruktur, indem Sie eine Windows Server AD FS-Verbundserverfarm konfigurieren.

3. Konfigurieren Sie Windows Server AD FS (Verbundserverfarm und Verbundserver-Proxyfarm) in der lokalen Gesamtstruktur.

4. Richten Sie eine Verbundvertrauensstellung zwischen der lokalen Instanz und der Azure-Instanz von Windows Server AD FS ein.

Falls für die Anwendungen aber der Zugriff auf lokale Ressourcen erforderlich ist, können Sie Windows Server AD FS mit der Anwendung in Azure wie folgt konfigurieren:

1. Konfigurieren Sie die Verbindung zwischen lokalen Netzwerken und Azure.

2. Konfigurieren Sie eine Windows Server AD FS-Verbundserverfarm in der lokalen Gesamtstruktur.

3. Konfigurieren Sie eine Windows Server AD FS-Verbundserver-Proxyfarm in Azure.

Diese Konfiguration hat den Vorteil, dass die Offenlegung von lokalen Ressourcen reduziert wird. Dies ähnelt der Konfiguration von Windows Server AD FS mit Anwendungen in einem Umkreisnetzwerk.

Beachten Sie, dass Sie bei beiden Szenarien Vertrauensstellungen mit mehr Identitätsanbietern einrichten können, falls die Business-to-Business-Zusammenarbeit erforderlich ist.

### <a name="BKMK_CloudSvcConfig"></a>Konfiguration der Clouddienste

Clouddienste sind erforderlich, wenn Sie eine VM direkt im Internet verfügbar machen oder eine mit dem Internet verbundene Anwendung mit Lastenausgleich verfügbar machen möchten. Dies ist möglich, da jeder Clouddienst nur eine konfigurierbare virtuelle IP-Adresse aufweist.

### <a name="BKMK_FedReqVIPDIP"></a>Anforderungen von Verbundservern für die öffentliche und private IP-Adressierung (dynamische IP und virtuelle IP)

Jede Azure Virtual Machine erhält eine dynamische IP-Adresse. Eine dynamische IP-Adresse ist eine private Adresse, auf die nur in Azure zugegriffen werden kann. In den meisten Fällen ist es aber erforderlich, eine virtuelle IP-Adresse für Ihre Windows Server AD FS-Bereitstellungen zu konfigurieren. Die virtuelle IP-Adresse wird benötigt, um Windows Server AD FS-Endpunkte im Internet verfügbar zu machen. Sie wird außerdem von Verbundpartnern und Clients für die Authentifizierung und fortlaufende Verwaltung verwendet. Eine virtuelle IP-Adresse ist eine Eigenschaft eines Clouddiensts, der einen oder mehrere virtuelle Azure-Computer umfasst. Wenn die beiden Ansprüche unterstützenden Anwendungen, die unter Azure und Windows Server AD FS bereitgestellt werden, mit dem Internet verbunden sind und gemeinsame Ports nutzen, ist jeweils eine eigene virtuelle IP-Adresse erforderlich. Daher müssen Sie einen Clouddienst für die Anwendung und einen zweiten Clouddienst für Windows Server AD FS erstellen.

Definitionen der Begriffe „virtuelle IP-Adresse“ und „dynamische IP-Adresse“ finden Sie unter [Begriffe und Definitionen](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Konfiguration von Windows Server AD FS für hohe Verfügbarkeit

Es ist zwar möglich, eigenständige Windows Server AD FS-Verbunddienste bereitzustellen, aber es wird empfohlen, eine Farm mit mindestens zwei Knoten für AD FS-Sicherheitstokendienste und Proxys für Produktionsumgebungen bereitzustellen.

Informationen zum Treffen der Entscheidung, welche Optionen für die Bereitstellungskonfiguration für Ihre Anforderungen am besten geeignet sind, finden Sie unter [Überlegungen zur AD FS-Bereitstellungstopologie](https://technet.microsoft.com/library/gg982489) im [AD FS 2.0-Entwurfshandbuch](https://technet.microsoft.com/library/dd807036).

> [AZURE.NOTE] Um den Lastenausgleich für Windows Server AD FS-Endpunkte in Azure zu ermöglichen, konfigurieren Sie alle Mitglieder der Windows Server AD FS-Farm in demselben Clouddienst und verwenden die Lastenausgleichsfunktion von Azure für HTTP- (Standard 80) und HTTPS-Ports (Standard 443). Weitere Informationen finden Sie unter [LoadBalancerProbe-Schema](https://msdn.microsoft.com/library/azure/jj151530). Der Windows Server-Netzwerklastenausgleich (Network Load Balancing, NLB) wird in Azure nicht unterstützt.

<!---HONumber=AcomDC_0309_2016-->