<properties
   pageTitle="Erste Schritte mit Azure Security Center | Microsoft Azure"
   description="Dieses Dokument enthält Informationen dazu, wie Sie Sicherheitsrichtlinien in Azure Security Center konfigurieren können."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/08/2016"
   ms.author="yurid"/>

# Festlegen von Sicherheitsrichtlinien in Azure Security Center
Dieses Dokument unterstützt Sie dabei, Sicherheitsrichtlinien in Azure Security Center zu konfigurieren. Sie werden durch die erforderlichen Schritte zum Ausführen dieser Aufgabe begleitet.

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center.

## Was ist Azure Security Center?
Azure Security Center unterstützt Sie beim Verhindern, Erkennen und Beheben von Bedrohungen durch größere Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Sektrum an Sicherheitslösungen verwendet werden.

## Was sind Sicherheitsrichtlinien?
In einer Sicherheitsrichtlinie wird der Satz von Sicherheitsmechanismen definiert, die für Ressourcen in dem angegebenen Abonnement zu empfehlen sind. In Azure Security Center definieren Sie Richtlinien für Ihre Azure-Abonnements entsprechend den Sicherheitsanforderungen Ihres Unternehmens sowie entsprechend dem Typ der Anwendungen oder der Vertraulichkeit der Daten in jedem Abonnement.

Beispielsweise haben Ressourcen, die für Entwicklungs- oder Testzwecke verwendet werden, möglicherweise Sicherheitsanforderungen, die sich von denen unterscheiden, die für Produktionsanwendungen verwendet werden. Ähnlich kann für Anwendungen mit reglementierten Daten, etwa personenbezogene Informationen (Personally Identifiable Information), eine höhere Sicherheitsstufe erforderlich sein. Die in Azure Security Center aktivierten Sicherheitsrichtlinien werden dazu verwendet, Sicherheitsempfehlungen und -überwachung zu steuern, damit Sie potenzielle Sicherheitsrisiken identifizieren und Bedrohungen entschärfen können.

## Einrichten von Sicherheitsrichtlinien

Sicherheitsrichtlinien sind für jedes Abonnement konfiguriert. Damit Sie eine Sicherheitsrichtlinie ändern können, müssen Sie ein Besitzer oder Mitwirkender dieses Abonnements sein. Führen Sie die nachstehenden Schritte aus, um Sicherheitsrichtlinien in Azure Security Center zu konfigurieren:

1. Klicken Sie im Azure Security Center-Dashboard auf die Kachel **Sicherheitsrichtlinie**.

2. Wählen Sie auf dem Blatt **Security Policy – Define policy per subscription**, das auf der rechten Seite geöffnet wird, das Abonnement aus, für das Sie die Sicherheitsrichtlinie aktivieren möchten.

    ![Aktivieren von Datensammlung](./media/security-center-policies/security-center-policies-fig0.png)

3. Das Blatt **Sicherheitsrichtlinie** für dieses Abonnement wird geöffnet. Es enthält eine Reihe von Optionen, die grundsätzlich wie folgt aussehen:

    ![Aktivieren von Datensammlung](./media/security-center-policies/security-center-policies-fig1.png)

4. Stellen Sie sicher, dass die Option **Collect data from virtual machines** auf **Ein** festgelegt ist. Diese Option aktiviert automatische Protokollsammlung für vorhandene und neue Ressourcen.

    >[AZURE.NOTE] Es wird dringend empfohlen, die Datensammlung für alle Ihre Abonnements zu aktivieren, da so sichergestellt wird, dass die Sicherheitsüberwachung für alle vorhandenen und neuen VMs verfügbar ist. Wird Datensammlung aktiviert, wird der Überwachungs-Agent installiert. Wenn Sie Datensammlung momentan von diesem Standort nicht aktivieren möchten, können Sie dies später über die Integritäts- und Empfehlungsansichten tun. Sie können die Datensammlung auch nur für das Abonnement oder für ausgewählte VMs aktivieren. Weitere Informationen zu den unterstützten virtuellen Computern finden Sie unter [Azure Security Center – häufig gestellte Fragen](security-center-faq.md).

5. Wenn Ihr Speicherkonto noch nicht konfiguriert ist, wird beim Öffnen der **Sicherheitsrichtlinie** möglicherweise eine Warnung angezeigt, die so ähnlich aussieht wie in der folgenden Abbildung:

    ![Speicherauswahl](./media/security-center-policies/security-center-policies-fig2.png)

6. Wenn diese Warnung angezeigt wird, klicken Sie auf diese Option, und wählen Sie die Region aus, wie in der folgenden Abbildung gezeigt:

    ![Speicherauswahl](./media/security-center-policies/security-center-policies-fig3.png)

7. Wählen Sie für jede Region, in der Sie virtuelle Computer ausführen, ein Speicherkonto, in dem Daten gespeichert werden, die von diesen virtuellen Computer gesammelt wurden. Dies macht es einfach für Sie, Daten aus Datenschutz- und Datenhoheitszwecken im selben geografischen Gebiet zu speichern. Nachdem Sie entschieden haben, welche Region verwendet werden soll, wählen Sie die Region dann das Speicherkonto aus.

8. Klicken Sie auf dem Blatt **Choose storage accounts** auf **OK**.

    > [AZURE.NOTE] Bei Bedarf können Sie Daten von virtuellen Computern in verschiedenen Regionen in einem zentralen Speicherkonto aggregieren. Weitere Informationen hierzu finden Sie unter [Azure Security Center – häufig gestellte Fragen](security-center-faq.md).

9. Klicken Sie auf dem Blatt **Sicherheitsrichtlinie** auf **Ein**, um die Sicherheitsempfehlungen zu aktivieren, die Sie für dieses Abonnement verwenden möchten. Verwenden Sie die folgende Tabelle als Referenz, um zu verstehen, was jede Option bewirkt:

| Richtlinie | Zustand Ein |
|----- |-----|
| System Updates | Ruft alle 12 Stunden und abhängig davon, welcher Dienst für diesen virtuellen Computer konfiguriert ist, eine Liste der verfügbaren Updates von Windows Update oder WSUS ab und empfiehlt, dass die fehlenden Updates auf Ihren virtuellen Windows-Computern installiert werden sollen. |
| Basisregeln | Analysiert alle 12 Stunden alle unterstützten virtuellen Computer, um jegliche Betriebssystemkonfigurationen zu ermitteln, die bewirken können, dass die virtuellen Computer anfälliger für Angriffe werden, und empfiehlt Konfigurationsänderungen, um auf diese Sicherheitsrisiken zu reagieren. Weitere Informationen zu den speziellen Konfigurationen, die überwacht werden, finden Sie in der [Liste der empfohlenen Basisregeln](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) (in englischer Sprache). |
| Antimalware | Empfiehlt, dass Antimalware für alle virtuellen Windows-Computer bereitgestellt wird, um Viren, Spyware und andere Schadsoftware möglichst zu erkennen und zu entfernen. |
| Zugriffssteuerungsliste auf Endpunkten | Empfiehlt, dass eine [Zugriffssteuerungsliste](virtual-machines-set-up-endpoints.md) (Access Controls List, ACL) konfiguriert wird, um den Zugriff auf Endpunkte von klassischen virtuellen Computern zu beschränken. Dies wird normalerweise verwendet, um sicherzustellen, dass nur Benutzer, die mit dem Unternehmensnetzwerk verbunden sind, auf die virtuellen Computer zugreifen können. |
| Netzwerksicherheitsgruppen | Empfiehlt, dass [Netzwerksicherheitsgruppen](virtual-networks-nsg.md) (NSGs) konfiguriert werden sollten, um den eingehenden und ausgehenden Datenverkehr für Subnetze und Netzwerkschnittstellen für virtuelle Ressourcen-Manager-Computer zu steuern. NSGs, die für ein Subnetz konfiguriert sind, werden für alle Netzwerkschnittstellen der virtuellen Computer übernommen, sofern nichts anderes angegeben ist. Zusätzlich zur Überprüfung, ob eine NSG konfiguriert wurde, bewertet diese Option Sicherheitsregeln für eingehende Daten, um Regeln zu identifizieren, die jeglichen eingehenden Datenverkehr zulassen. |
| Web Application Firewall | Empfiehlt, dass eine Web Application Firewall auf virtuellen Ressourcen-Manager-Computern bereitgestellt wird, wenn Folgendes zutrifft: [Öffentliche IP auf Instanzebene](virtual-networks-instance-level-public-ip.md) (ILPIP) wird verwendet, und die zugehörigen NSG-Sicherheitsregeln für eingehenden Datenverkehr sind so konfiguriert, dass Zugriff auf den Port 80/443 zugelassen wird. Es wird eine IP-Adresse mit Lastenausgleich (VIP) verwendet, und die zugehörigen Lastenausgleichs- und eingehenden NAT-Regeln sind so konfiguriert, dass Zugriff auf den Port 80/443 zugelassen wird (weitere Informationen finden Sie unter [Unterstützung des Azure-Ressourcen-Managers für Load Balancer](load-balancer-arm.md)). |
| SQL-Überwachung | Empfiehlt, dass Überwachung des Zugriffs auf Azure SQL-Server und -Datenbanken zu Compliance-, erweiterten Erkennungs- und Untersuchungszwecken aktiviert wird. |
| Transparent Data Encryption für SQL | Empfiehlt, dass Verschlüsselung im Ruhezustand für Ihre Azure SQL-Datenbanken, die zugehörigen Sicherungen und die Transaktionsprotokolldateien aktiviert wird, sodass Ihre Daten selbst dann, wenn unberechtigt auf sie zugegriffen wird, nicht gelesen werden können. |

Sobald Sie alle Optionen konfiguriert haben, klicken Sie auf **Speichern**, um diese Änderungen zu übernehmen.

## Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Sicherheitsrichtlinien in Azure Security Center konfigurieren können. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md) – erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) – Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
- [Azure Security Center – häufig gestellte Fragen](security-center-faq.md) zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) – suchen Sie nach Blogbeiträgen über Azure-Sicherheit und -Compliance.

<!---HONumber=AcomDC_0211_2016-->