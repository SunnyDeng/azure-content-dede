<properties
   pageTitle="Implementieren von Sicherheitsempfehlungen in Azure Security Center | Microsoft Azure"
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
   ms.date="12/02/2015"
   ms.author="terrylan"/>

# Implementieren von Sicherheitsempfehlungen in Azure Security Center

In diesem Dokument erfahren Sie, wie Sicherheitsempfehlungen in Azure Security Center Ihnen helfen, Ihre Azure-Ressourcen zu schützen.

> [AZURE.NOTE]Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center. Dies ist eine Einführung in den Dienst anhand einer Beispielbereitstellung. Es ist keine schrittweise Anleitung.

## Was ist Azure Security Center?
Azure Security Center unterstützt Sie beim Verhindern, Erkennen und Beheben von Bedrohungen durch größere Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Spektrum an Sicherheitslösungen verwendet werden.

##Was ist eine Sicherheitsempfehlung?
Azure Security Center analysiert in regelmäßigen Abständen den Sicherheitsstatus der Azure-Ressourcen. Werden mögliche Sicherheitsrisiken festgestellt, werden Empfehlungen erstellt. Gemäß der Empfehlungen werden Sie durch den Prozess der Konfiguration des erforderlichen Sicherheitsmechanismus geführt.

## Einstellung von Empfehlungen

In [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md) lernen Sie:

- Konfigurieren von Sicherheitsrichtlinien
- Aktivieren der Datensammlung
- Auswählen der Empfehlungen, die Sie als Teil Ihrer Sicherheitsrichtlinie verwenden möchten

Aktuelle Richtlinienempfehlungen beziehen sich auf Systemupdates, Grundregeln, Antischadsoftware, [ACLs für Endpunkte](virtual-machines-set-up-endpoints.md), [Netzwerksicherheitsgruppen](virtual-networks-nsg.md) für Subnetze und Netzwerkschnittstellen, Überwachung der SQL-Datenbank, Transparent Data Encryption für die SQL-Datenbank und Web Application Firewall. Die [Einstellung von Sicherheitsrichtlinien](security-center-policies.md) enthält eine Beschreibung der einzelnen Empfehlungsoptionen.

Der Abschnitt **Empfehlungen anzeigen für** des Blatts **Sicherheitsrichtlinie** enthält eine Liste von Empfehlungen, die Sie für die Ressourcen im angegebenen Abonnement aktivieren können.

![][1]

## Überwachen der Empfehlungen

Nach Einstellung einer Sicherheitsrichtlinie analysiert Azure Security Center den Sicherheitsstatus Ihrer Ressourcen, um potenzielle Sicherheitsrisiken zu erkennen. Auf der Kachel **Empfehlungen** des Blatts **Security Center** können Sie die Gesamtmenge der von Azure Security Center identifizierten Empfehlungen sehen.

![][2]

So zeigen Sie die Details jeder Empfehlung an:

1. Klicken Sie auf dem Blatt **Security Center** auf die Kachel **Empfehlungen**. Das Blatt **Empfehlungen** wird geöffnet.
2. Sie können nach Status und Schweregrad filtern, welche Empfehlungen Ihnen vorgeschlagen werden. Klicken Sie auf dem Blatt **Empfehlungen** auf **Filter**. Das Blatt "Filter" wird geöffnet, und Sie wählen die Schweregrad- und Statuswerte aus, die Sie anzeigen möchten. ![][3]

3. Wenn Sie feststellen, dass eine Empfehlung nicht anwendbar ist, können Sie die Empfehlung verwerfen und dann aus der Ansicht filtern. Es gibt zwei Möglichkeiten, eine Empfehlung zu verwerfen. Klicken Sie mit der rechten Maustaste auf ein Element, und wählen Sie **Verwerfen**, oder zeigen Sie auf ein Element, klicken Sie auf die drei Punkte, die rechts angezeigt werden, und wählen Sie **Verwerfen**. Sie können verworfene Empfehlungen anzeigen, indem Sie auf **Filter** klicken und **Verworfen** auswählen. ![][4]

Die Empfehlungen werden in einem Tabellenformat angezeigt, wobei jede Zeile eine bestimmte Empfehlung darstellt. Die Spalten dieser Tabelle sind:

- **BESCHREIBUNG**: Eine Erläuterung der Empfehlung, und was getan werden muss, um sie zu befolgen.
- **RESSOURCE**: Für welche Ressource(n) diese Empfehlung gilt.
- **STATUS**: der aktuelle Status der Empfehlung
  - **Offen**: noch nicht behandelt
  - **In Bearbeitung**: Empfehlung wird derzeit auf diese Ressourcen angewendet, es ist keine Aktion Ihrerseits erforderlich.
  - **Gelöst**: Empfehlung wurde bereits abgeschlossen (im Status "Gelöst" ist die Farbe der Linie abgeblendet).
- **SCHWEREGRAD**: Beschreibt den Schweregrad dieser bestimmten Empfehlung:
  - **Hoch**: Ein Sicherheitsrisiko betrifft eine bedeutsame Ressource (Anwendung, VM, Netzwerksicherheitsgruppe) und erfordert Aufmerksamkeit.
  - **Mittel**: Unkritische oder zusätzliche Schritte sind erforderlich, um einen Prozess abzuschließen oder eine Sicherheitslücke zu beseitigen.
  - **Niedrig**: Ein Sicherheitsrisiko, das behandelt werden sollte, jedoch nicht unmittelbare Aufmerksamkeit erfordert. (Standardmäßig wird eine Empfehlung mit Schweregrad "Niedrig" nicht angezeigt, jedoch können Sie einen entsprechenden Filter für solche Empfehlungen einstellen, wenn Sie sie anzeigen möchten.)

Der folgenden Tabelle können Sie entnehmen, welche Empfehlungen verfügbar sind, und welche Aktionen sie jeweils ausführen, wenn Sie sie anwenden:

| Empfehlung | Beschreibung |
|----- |-----|
| Sammlung von Daten für Abonnements/virtuelle Computer aktivieren | Empfiehlt Ihnen, in der Sicherheitsrichtlinie die Datensammlung für ausgewählte virtuelle Computer zu aktivieren. |
| Grundregelkonflikte lösen | Empfiehlt Ihnen, OS-Konfigurationen an den empfohlenen Grundregeln zu orientieren, z. B. nicht zuzulassen, dass Kennwörter gespeichert werden. |
| Systemupdates anwenden | Empfiehlt Ihnen, fehlende Systemupdates für virtuelle Computer (nur Windows-VMs) bereitzustellen. |
| ACLs für Endpunkte konfigurieren | Sie sollten eine Zugriffssteuerungsliste konfigurieren, um den eingehenden Zugriff auf virtuelle Computer (nur für klassische VMs) einzuschränken. |
| Web Application Firewall hinzufügen | Empfiehlt Ihnen, eine Web Application Firewall (WAF) für Webendpunkte bereitzustellen (nur Ressourcen-Manager-VMs). |
| Web Application Firewall-Setup abschließen | Um die Konfiguration einer WAF abzuschließen, muss Datenverkehr an das WAF-Gerät umgeleitet werden. Diese Empfehlung schließt die erforderlichen Setupänderungen ab. |
| Antischadsoftware aktivieren | Empfiehlt Ihnen, Antischadsoftware für virtuelle Computer bereitzustellen (nur Windows-VMs). |
| Netzwerksicherheitsgruppen für Subnetze/Netzwerkschnittstellen aktivieren | Empfiehlt Ihnen, Netzwerksicherheitsgruppen (NSGs) für Subnetze und Netzwerkschnittstellen zu aktivieren(nur Ressourcen-Manager-VMs). |
| Zugriff über öffentliche externe Endpunkte einschränken | Empfiehlt Ihnen, Regeln für eingehenden Datenverkehr für NSGs zu konfigurieren. |
| Überwachung von SQL-Server aktivieren | Empfiehlt Ihnen, die Überwachung für Azure SQL-Server zu aktivieren (nur Azure SQL-Dienst, auf Ihren virtuellen Computern ausgeführtes SQL nicht inbegriffen). |
| Überwachung der SQL-Datenbank aktivieren | Empfiehlt Ihnen, die Überwachung für Azure SQL-Datenbanken zu aktivieren (nur Azure SQL-Dienst, auf Ihren virtuellen Computern ausgeführtes SQL nicht inbegriffen). |
| Transparent Data Encryption für SQL-Datenbanken aktivieren | Empfiehlt Ihnen, die Verschlüsselung für SQL-Datenbanken zu aktivieren (nur Azure SQL-Dienst). |
| VM-Agent bereitstellen | Ermöglicht Ihnen, anzuzeigen, welche VMs den VM-Agent benötigen. Der VM-Agent muss auf virtuellen Computern installiert werden, um das Überprüfen von Patches, Überprüfen von Grundregeln und Antischadsoftware bereitzustellen. Der VM-Agent wird standardmäßig für virtuelle Computer installiert, die vom Azure Marketplace bereitgestellt werden. Der Artikel [VM-Agent und Erweiterungen – Teil 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) enthält Informationen zum Installieren des VM-Agents. |

## Anwenden von Empfehlungen
Nach Auswertung aller Empfehlungen können Sie entscheiden, welche zuerst angewendet werden soll. Verwenden Sie den Schweregrad als wichtigsten Parameter bei der Entscheidung, welche Empfehlungen zuerst angewendet werden sollen. Die Antischadsoftware-Empfehlung dient uns als Beispiel zum Anwenden einer Empfehlung:

1. Wählen Sie im Blatt **Empfehlungen** die Option **Antischadsoftware aktivieren**. ![][5]

2. Treffen Sie im Blatt **Antischadsoftware installieren** Ihre Auswahl in der Liste virtueller Computer, auf denen keine Antischadsoftware aktiviert ist, und klicken Sie auf **Antischadsoftware installieren**.
3. Das Blatt **Neue Ressource** wird geöffnet, in dem Sie die Antischadsoftware-Lösung auswählen können, Sie verwenden möchten. Wählen Sie **Microsoft Antimalware**.
4. Weitere Informationen zur Antischadsoftware-Lösung werden angezeigt. Klicken Sie auf **Erstellen**.
5. Geben Sie die erforderlichen Konfigurationseinstellungen auf dem Blatt **Erweiterung hinzufügen** ein, und wählen Sie **OK**. ![][6]

[Microsoft Antimalware](azure-security-antimalware.md) ist jetzt auf dem ausgewählten virtuellen Computer aktiv.

## Bereitstellen integrierter Partnerlösungen

Eine Empfehlung könnte lauten, eine integrierte Sicherheitslösung von einem Microsoft-Partner bereitzustellen. Betrachten wir ein Beispiel zu dieser Vorgehensweise:

1. Kehren Sie zum Blatt **Empfehlungen** zurück.
2.	Wählen Sie die Empfehlung **Webanwendung mit Web Application Firewall sichern**. Daraufhin wird das Blatt **Ungeschützte Webanwendungen** geöffnet. ![][7]
3. Wählen Sie eine Webanwendung aus, worauf das Blatt **Web Application Firewall hinzufügen** geöffnet wird.
4. Wählen Sie **Barracuda Web Application Firewall** aus. Ein Blatt wird geöffnet, das Ihnen Informationen zur **Barracuda Web Application Firewall** liefert.
5. Klicken Sie im Informationsblatt auf **Erstellen**. Das Blatt **Neue Web Application Firewall** wird geöffnet, wo Sie Schritte zur **VM-Konfiguration** ausführen und **WAF-Informationen** bereitstellen können.
6. Wählen Sie **VM-Konfiguration**. Im Blatt **VM-Konfiguration** können Sie Informationen eingeben, die erforderlich sind, um den virtuellen Computer einzurichten, auf dem die WAF ausgeführt wird. ![][8]
7. Kehren Sie zum Blatt **Neue Web Application Firewall** zurück, und wählen Sie **WAF-Informationen**. Im Blatt **WAF-Informationen** können Sie die WAF selbst konfigurieren. In Schritt 6 können Sie den virtuellen Computer konfigurieren, auf dem die WAF ausgeführt wird, und in Schritt 7 können Sie die WAF selbst bereitstellen.

8. Kehren Sie zum Blatt **Empfehlungen** zurück. Ein neuer Eintrag wurde generiert, nachdem Sie die WAF erstellt haben: **Web Application Firewall-Setup abschließen**. So wissen Sie, dass Sie den Prozess der tatsächlichen Verdrahtung der WAF in Azure Virtual Network abschließen müssen, damit sie die Anwendung schützen kann. ![][9]

9. Wählen Sie **Web Application Firewall-Setup abschließen**, und ein neues Blatt wird geöffnet. Sie sehen, dass Sie über eine Webanwendung verfügen, für die der Datenverkehr umgeleitet werden muss.
10. Wählen Sie die Webanwendung aus, und ein Blatt wird geöffnet, in dem Sie Schritte zum Abschließen des Web Application Firewall-Setups ausführen können. Führen Sie die Schritte aus, klicken Sie auf **Datenverkehr einschränken**, und Azure Security Center übernimmt die Verdrahtung für Sie. ![][10]

Die Protokolle dieser WAF sind nun vollständig integriert. Azure Security Center kann automatisch beginnen, die Protokolle zu sammeln und zu analysieren, damit Sie wichtige Sicherheitswarnungen erhalten können.

## Nächste Schritte
In diesem Dokument wurden Ihnen Sicherheitsempfehlungen in Azure Security Center vorgestellt. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

- [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md) – Erfahren Sie, wie Sie Sicherheitsrichtlinien konfigurieren.
- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md) – Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) – Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
- [Häufig gestellte Fragen (FAQ) zu Security Center](security-center-faq.md) – Häufig gestellte Fragen zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) – Suchen Sie nach Blogbeiträgen über Azure-Sicherheit und -Compliance.

<!--Image references-->
[1]: ./media/security-center-recommendations/show-recommendations-for.png
[2]: ./media/security-center-recommendations/recommendations-tile.png
[3]: ./media/security-center-recommendations/filter-recommendations.png
[4]: ./media/security-center-recommendations/dismiss-recommendations.png
[5]: ./media/security-center-recommendations/select-enable-antimalware.png
[6]: ./media/security-center-recommendations/install-antimalware.png
[7]: ./media/security-center-recommendations/secure-web-application.png
[8]: ./media/security-center-recommendations/vm-configuration.png
[9]: ./media/security-center-recommendations/finalize-waf.png
[10]: ./media/security-center-recommendations/restrict-traffic.png

<!---HONumber=AcomDC_1210_2015-->