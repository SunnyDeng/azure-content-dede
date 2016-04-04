<properties
   pageTitle="Verwalten von Sicherheitsempfehlungen in Azure Security Center | Microsoft Azure"
   description="In diesem Dokument erfahren Sie, wie Sicherheitsempfehlungen in Azure Security Center Ihnen helfen, Ihre Azure-Ressourcen zu schützen und Ihre Sicherheitsrichtlinien einzuhalten."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/02/2016"
   ms.author="terrylan"/>

# Verwalten von Sicherheitsempfehlungen in Azure Security Center

In diesem Dokument erfahren Sie, wie Sie Sicherheitsempfehlungen in Azure Security Center verwenden, um Ihre Azure-Ressourcen zu schützen.

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center. Der Dienst wird anhand einer Beispielbereitstellung vorgestellt. Es ist keine schrittweise Anleitung.

## Was ist Azure Security Center?
Azure Security Center unterstützt Sie beim Verhindern, Erkennen und Beheben von Bedrohungen durch größere Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Sektrum an Sicherheitslösungen verwendet werden.

## Was sind Sicherheitsempfehlungen?
Security Center analysiert in regelmäßigen Abständen den Sicherheitsstatus der Azure-Ressourcen. Wenn Security Center potenzielle Sicherheitsrisiken identifiziert, werden Empfehlungen erstellt. Entsprechend den Empfehlungen werden Sie durch den Prozess der Konfiguration des erforderlichen Sicherheitsmechanismus geführt.

## Implementieren von Sicherheitsempfehlungen

### Festlegen von Empfehlungen

Unter [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md) wird Folgendes beschrieben:

- Konfigurieren von Sicherheitsrichtlinien
- Aktivieren der Datensammlung
- Auswählen der Empfehlungen, die Sie als Teil Ihrer Sicherheitsrichtlinie verwenden möchten

Aktuelle Richtlinienempfehlungen beziehen sich auf Systemupdates, Grundregeln, Antischadsoftware, [ACLs für Endpunkte](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md), [Netzwerksicherheitsgruppen](../virtual-network/virtual-networks-nsg.md) für Subnetze und Netzwerkschnittstellen, Überwachung der SQL-Datenbank, Transparent Data Encryption für die SQL-Datenbank und Web Application Firewalls. [Einrichten von Sicherheitsrichtlinien](security-center-policies.md) enthält eine Beschreibung der einzelnen Empfehlungsoptionen.

### Überwachen von Empfehlungen
Nach Einstellung einer Sicherheitsrichtlinie analysiert Security Center den Sicherheitsstatus Ihrer Ressourcen, um potenzielle Sicherheitsrisiken zu erkennen. Auf der Kachel **Empfehlungen** des Blatts **Security Center** können Sie die Gesamtzahl der von Security Center identifizierten Empfehlungen sehen.

![][2]

So zeigen Sie die Details jeder Empfehlung an:

1. Klicken Sie auf dem Blatt **Security Center** auf die Kachel **Empfehlungen**. Das Blatt **Empfehlungen** wird geöffnet.

Die Empfehlungen werden in einem Tabellenformat angezeigt, wobei jede Zeile eine bestimmte Empfehlung darstellt. Die Spalten dieser Tabelle sind:

- **BESCHREIBUNG**: Eine Erläuterung der Empfehlung und der erforderlichen Schritte, um sie zu befolgen.
- **RESSOURCE**: Eine Liste der Ressourcen, für die diese Empfehlung gilt.
- **STATUS**: Beschreibt den aktuellen Status der Empfehlung:
    - **Offen**: Die Empfehlung wurde noch nicht verarbeitet.
    - **In Bearbeitung**: Die Empfehlung wird derzeit auf die Ressourcen angewendet, und es ist keine Aktion Ihrerseits erforderlich.
    - **Gelöst**: Die Empfehlung wurde bereits abgeschlossen (in diesem Fall wird die Zeile abgeblendet).
- **SCHWEREGRAD**: Beschreibt den Schweregrad der jeweiligen Empfehlung:
    - **Hoch**: Ein Sicherheitsrisiko betrifft eine bedeutsame Ressource (z. B. eine Anwendung, VM oder Netzwerksicherheitsgruppe) und erfordert Aufmerksamkeit.
    - **Mittel**: Es besteht ein Sicherheitsrisiko, und es sind nicht kritische bzw. weitere Schritte erforderlich, um es zu beseitigen oder einen Prozess abzuschließen.
    - **Niedrig**: Es besteht ein Sicherheitsrisiko, das behandelt werden sollte, aber es ist keine unmittelbare Aufmerksamkeit erforderlich. (Standardmäßig werden Empfehlungen mit dem Status „Niedrig“ nicht angezeigt, aber Sie können bei Bedarf nach diesen Empfehlungen filtern.)

Der folgenden Tabelle können Sie entnehmen, welche Empfehlungen verfügbar sind und welche Aktionen jeweils ausgeführt werden, wenn Sie sie anwenden:

> [AZURE.NOTE] Erfahren Sie mehr über das [klassische und das Resource Manager-Bereitstellungsmodell](../azure-classic-rm.md) für Azure-Ressourcen.

|Empfehlungen|Beschreibung|
|-----|-----|
|Sammlung von Daten für Abonnements/virtuelle Computer aktivieren|Empfiehlt Ihnen, in der Sicherheitsrichtlinie die Datensammlung für alle Abonnements oder für ausgewählte virtuelle Maschinen zu aktivieren.|
|Grundregelkonflikte lösen|Empfiehlt Ihnen, OS-Konfigurationen an den empfohlenen Grundregeln zu orientieren, z. B. nicht zuzulassen, dass Kennwörter gespeichert werden.|
|Systemupdates anwenden|Empfiehlt Ihnen, fehlende Systemupdates und kritische Updates für virtuelle Maschinen (nur Windows-VMs) bereitzustellen.|
|ACLs für Endpunkte konfigurieren|Empfiehlt Ihnen, Zugriffssteuerungslisten zu konfigurieren, um den eingehenden Zugriff auf virtuelle Maschinen (nur klassische VMs) einzuschränken.|
|[Web Application Firewall hinzufügen](security-center-add-web-application-firewall.md)|Empfiehlt Ihnen, eine Web Application Firewall (WAF) für Webendpunkte bereitzustellen. Sie können mehrere Webanwendungen in Security Center schützen, indem Sie diese Anwendungen Ihren vorhandenen WAF-Bereitstellungen hinzufügen. WAF-Geräte (erstellt mit dem Ressourcen-Manager-Bereitstellungsmodell) müssen in einem separaten virtuellen Netzwerk bereitgestellt werden. WAF-Geräte (erstellt mit dem klassischen Bereitstellungsmodell) sind auf die Verwendung einer Netzwerksicherheitsgruppe beschränkt. Diese Unterstützung wird in Zukunft auf eine vollständig angepasste Bereitstellung eines WAF-Geräts (klassisch) erweitert.|
|Web Application Firewall-Setup abschließen|Um die Konfiguration einer WAF abzuschließen, muss Datenverkehr an das WAF-Gerät umgeleitet werden. Nach dieser Empfehlung werden die erforderlichen Setupänderungen vorgenommen.|
|[Antischadsoftware aktivieren](security-center-enable-antimalware.md)|Empfiehlt Ihnen, Antischadsoftware für virtuelle Maschinen bereitzustellen (nur Windows-VMs).|
|Netzwerksicherheitsgruppen für Subnetze/Netzwerkschnittstellen aktivieren|Empfiehlt Ihnen, Netzwerksicherheitsgruppen (NSGs) für Subnetze und Netzwerkschnittstellen zu aktivieren (nur Ressourcen-Manager-VMs).|
|Zugriff über öffentliche externe Endpunkte einschränken|Empfiehlt Ihnen, Regeln für eingehenden Datenverkehr für NSGs zu konfigurieren.|
|Überwachung von SQL-Server aktivieren|Empfiehlt Ihnen, die Überwachung für Azure SQL-Server zu aktivieren (nur Azure SQL-Dienst, keine Ausführung von SQL auf Ihren virtuellen Maschinen).|
|Überwachung der SQL-Datenbank aktivieren|Empfiehlt Ihnen, die Überwachung für Azure SQL-Datenbanken zu aktivieren (nur Azure SQL-Dienst, keine Ausführung von SQL auf Ihren virtuellen Maschinen).|
|Transparent Data Encryption für SQL-Datenbanken aktivieren|Empfiehlt Ihnen, die Verschlüsselung für SQL-Datenbanken zu aktivieren (nur Azure SQL-Dienst).|
|VM-Agent bereitstellen|Ermöglicht Ihnen, anzuzeigen, welche VMs den VM-Agent benötigen. Der VM-Agent muss auf virtuellen Maschinen installiert werden, um das Überprüfen von Patches, Überprüfen von Grundregeln und Antischadsoftware bereitzustellen. Der VM-Agent wird standardmäßig für virtuelle Maschinen installiert, die vom Azure Marketplace bereitgestellt werden. Der Artikel [VM-Agent und Erweiterungen – Teil 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) enthält Informationen zum Installieren des VM-Agents.|
| [Datenträgerverschlüsselung anwenden](security-center-apply-disk-encryption.md) |Empfiehlt Ihnen, dass Sie Ihre VM-Datenträger per Azure Disk Encryption (Windows- und Linux-VMs) verschlüsseln. Die Verschlüsselung wird sowohl für die Betriebssystem- als auch für die Datenvolumes auf Ihrer VM empfohlen.|

Sie können Empfehlungen filtern und verwerfen.

1. Klicken Sie auf dem Blatt **Empfehlungen** auf **Filter**. Das Blatt **Filter** wird geöffnet. Sie können Werte für Schweregrad und Status auswählen, die Sie anzeigen möchten.

    ![][3]

2. Wenn Sie feststellen, dass eine Empfehlung nicht anwendbar ist, können Sie die Empfehlung verwerfen und dann aus der Ansicht filtern. Es gibt zwei Möglichkeiten, eine Empfehlung zu verwerfen. Klicken Sie mit der rechten Maustaste auf ein Element, und wählen Sie die Option **Verwerfen**. Oder bewegen Sie den Mauszeiger auf ein Element, klicken Sie auf die drei rechts angezeigten Punkte, und wählen Sie **Verwerfen**. Sie können verworfene Empfehlungen anzeigen, indem Sie auf **Filter** klicken und dann **Verworfen** auswählen.

    ![][4]

### Anwenden von Empfehlungen
Nach Auswertung aller Empfehlungen entscheiden Sie, welche zuerst angewendet werden soll. Es ist ratsam, den Schweregrad als wichtigsten Parameter bei der Entscheidung heranzuziehen, welche Empfehlungen zuerst angewendet werden sollen. Die Empfehlung **Antischadsoftware aktivieren** dient uns als Beispiel zum Anwenden einer Empfehlung.

1. Wählen Sie auf dem Blatt **Empfehlungen** die Option **Enable Antimalware**. ![][5]

2. Treffen Sie auf dem Blatt **Install Antimalware** Ihre Auswahl in der Liste virtueller Computer, auf denen keine Antischadsoftware aktiviert ist, und klicken Sie auf **Install Antimalware**.
3. Das Blatt **Neue Ressource** wird geöffnet, auf dem Sie die Antischadsoftware-Lösung auswählen können, Sie verwenden möchten. Wählen Sie **Microsoft Antimalware**.
4. Weitere Informationen zur Antischadsoftware-Lösung werden angezeigt. Klicken Sie auf **Erstellen**.
5. Geben Sie die erforderlichen Konfigurationseinstellungen auf dem Blatt **Erweiterung hinzufügen** ein, und wählen Sie anschließend **OK**. ![][6]

[Microsoft Antimalware](../azure-security-antimalware.md) ist jetzt auf dem ausgewählten virtuellen Computer aktiv.


## Nächste Schritte
In diesem Dokument wurden Ihnen die Sicherheitsempfehlungen in Security Center vorgestellt. Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

- [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md): Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md): Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
- [Azure Security Center – häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/): Hier finden Sie Blogbeiträge über Azure-Sicherheit und -Compliance.

<!--Image references-->
[2]: ./media/security-center-recommendations/recommendations-tile.png
[3]: ./media/security-center-recommendations/filter-recommendations.png
[4]: ./media/security-center-recommendations/dismiss-recommendations.png
[5]: ./media/security-center-recommendations/select-enable-antimalware.png
[6]: ./media/security-center-recommendations/install-antimalware.png

<!---HONumber=AcomDC_0323_2016-->