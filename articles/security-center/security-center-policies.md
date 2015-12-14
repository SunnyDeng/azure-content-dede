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
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/03/2015"
   ms.author="yurid"/>

#Festlegen von Sicherheitsrichtlinien in Azure Security Center
In dieser exemplarischen Vorgehensweise erfahren Sie, wie Sie Sicherheitsrichtlinien in Azure Security Center konfigurieren können.

> [AZURE.NOTE]Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center.

##Wann sollte ich Sicherheitsrichtlinien verwenden?
In einer Sicherheitsrichtlinie wird der Satz von Sicherheitsmechanismen definiert, die für Ressourcen in dem angegebenen Abonnement zu empfehlen sind. In Azure Security Center definieren Sie Richtlinien für Ihre Azure-Abonnements entsprechend den Sicherheitsanforderungen Ihres Unternehmens sowie entsprechend dem Typ der Anwendungen oder der Vertraulichkeit der Daten in jedem Abonnement.
 
Beispielsweise haben Ressourcen, die für Entwicklungs- oder Testzwecke verwendet werden, möglicherweise Sicherheitsanforderungen, die sich von denen unterscheiden, die für Produktionsanwendungen verwendet werden. Ähnlich kann für Anwendungen mit reglementierten Daten, etwa personenbezogene Informationen (Personally Identifiable Information), eine höhere Sicherheitsstufe erforderlich sein. Die in Azure Security Center aktivierten Sicherheitsrichtlinien werden dazu verwendet, Sicherheitsempfehlungen und -überwachung zu steuern, damit Sie potenzielle Sicherheitsrisiken identifizieren und Bedrohungen entschärfen können.

####Konfigurieren von Sicherheitsrichtlinien

Sicherheitsrichtlinien sind für jedes Abonnement konfiguriert. Damit Sie eine Sicherheitsrichtlinie ändern können, müssen Sie ein Besitzer oder Mitwirkender dieses Abonnements sein. Führen Sie die nachstehenden Schritte aus, um Sicherheitsrichtlinien in Azure Security Center zu konfigurieren:

1. Klicken Sie im Azure Security Center-Dashboard auf die Kachel **Sicherheitsrichtlinie**.
 
2. Wählen Sie auf dem Blatt **Sicherheitsrichtlinie - Richtlinie pro Abonnement definieren**, das auf der rechten Seite geöffnet wurde, das Abonnement aus, für das Sie die Sicherheitsrichtlinie aktivieren möchten.

    ![Aktivieren von Datensammlung](./media/security-center-policies/security-center-policies-fig0.png)

3. Das **Sicherheitsrichtlinie**-Blatt für dieses Abonnement wird geöffnet. Das Blatt enthält eine Reihe von Optionen, die grundsätzlich wie folgt aussehen:

    ![Aktivieren von Datensammlung](./media/security-center-policies/security-center-policies-fig1.png)

    
4. Stellen Sie sicher, dass die Option **Daten von virtuellen Computern sammeln** auf **Ein** festgelegt ist. Diese Option aktiviert automatische Protokollsammlung für vorhandene und neue Ressourcen.

5. Wenn Ihr Speicherkonto noch nicht konfiguriert ist, sehen Sie, wenn Sie die **Sicherheitsrichtlinie** öffnen, möglicherweise eine Warnung, die so ähnlich aussieht wie in der folgenden Abbildung:

    ![Speicherauswahl](./media/security-center-policies/security-center-policies-fig2.png)

6. Wenn diese Warnung angezeigt wird, klicken Sie auf diese Option, und wählen Sie die Region aus, wie in der folgenden Abbildung gezeigt:

    ![Speicherauswahl](./media/security-center-policies/security-center-policies-fig3.png)

7. Wählen Sie für jede Region, in der Sie virtuelle Computer ausführen, ein Speicherkonto, in dem Daten gespeichert werden, die von diesen virtuellen Computer gesammelt wurden. Dies macht es einfach für Sie, Daten aus Datenschutz- und Datenhoheitszwecken im selben geografischen Gebiet zu speichern. Nachdem Sie entschieden haben, welche Region verwendet werden soll, wählen Sie die Region dann das Speicherkonto aus.

8. Klicken Sie auf dem Blatt **Speicherkonten wählen** auf **OK**.

    > [AZURE.NOTE]Bei Bedarf können Sie Daten von virtuellen Computern in verschiedenen Regionen in einem zentralen Speicherkonto aggregieren. Weitere Informationen hierzu finden Sie unter [Azure Security Center – häufig gestellte Fragen](security-center-faq.md).

9. Klicken Sie auf dem Blatt **Sicherheitsrichtlinie** auf **Ein**, um die Sicherheitsempfehlungen zu aktivieren, die Sie für dieses Abonnement verwenden möchten. Verwenden Sie die folgende Tabelle als Referenz, um zu verstehen, was jede Option bewirkt:

    | Richtlinie | Zustand Ein |
|----- |-----|
| System Updates | Ruft alle 12 Stunden und abhängig davon, welcher Dienst für diesen virtuellen Computer konfiguriert ist, eine Liste der verfügbaren Updates von Windows Update oder WSUS ab und empfiehlt, dass die fehlenden Updates auf Ihren virtuellen Windows-Computern installiert werden sollen. |
| Basisregeln | Analysiert alle 12 Stunden alle unterstützten virtuellen Computer, um jegliche Betriebssystemkonfigurationen zu ermitteln, die bewirken können, dass die virtuellen Computer anfälliger für Angriffe werden, und empfiehlt Konfigurationsänderungen, um auf diese Sicherheitsrisiken zu reagieren. Weitere Informationen zu den speziellen Konfigurationen, die überwacht werden, finden Sie in der [Liste der empfohlenen Basisregeln](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). |
| Antimalware | Empfiehlt, dass Antimalware für alle virtuellen Windows-Computer bereitgestellt wird, um Viren, Spyware und andere Schadsoftware möglichst zu erkennen und zu entfernen. |
| Zugriffssteuerungsliste auf Endpunkten | Empfiehlt, dass eine [Zugriffssteuerungsliste](virtual-machines-set-up-endpoints.md) (Access Controls List, ACL) konfiguriert wird, um den Zugriff auf Endpunkte von klassischen virtuellen Computern zu beschränken. Dies wird normalerweise verwendet, um sicherzustellen, dass nur Benutzer, die mit dem Unternehmensnetzwerk verbunden sind, auf die virtuellen Computer zugreifen können. |
| Netzwerksicherheitsgruppen für Subnetze und Netzwerkschnittstellen | Empfiehlt, dass [Netzwerksicherheitsgruppen](virtual-networks-nsg.md) (NSGs) konfiguriert werden sollten, um den eingehenden und ausgehenden Datenverkehr für Subnetze und Netzwerkschnittstellen für virtuelle Ressourcen-Manager-Computer zu steuern. NSGs, die für ein Subnetz konfiguriert sind, werden für alle Netzwerkschnittstellen der virtuellen Computer übernommen, sofern nichts anderes angegeben ist. Zusätzlich zur Überprüfung, ob eine NSG konfiguriert wurde, werden Sicherheitsregeln für eingehende Daten bewertet, um Regeln zu identifizieren, die jeglichen eingehenden Datenverkehr zulassen. |
| Web Application Firewall | Empfiehlt, dass eine Web Application Firewall auf virtuellen Ressourcen-Manager-Computern bereitgestellt wird, wenn Folgendes zutrifft: [Öffentliche IP auf Instanzebene](virtual-networks-instance-level-public-ip.md) (ILPIP) wird verwendet, und die zugehörigen NSG-Sicherheitsregeln für eingehenden Datenverkehr sind so konfiguriert, dass Zugriff auf den Port 80/443 zugelassen wird. Es wird eine IP-Adresse mit Lastenausgleich (virtuelle IP) verwendet, und die zugehörigen Lastenausgleichs- und eingehenden NAT-Regeln sind so konfiguriert, dass Zugriff auf den Port 80/443 zugelassen wird (weitere Informationen finden Sie unter [Unterstützung des Azure-Ressourcen-Managers für Load Balancer](load-balancer-arm.md)). |
| SQL-Überwachung | Empfiehlt, dass Überwachung des Zugriffs auf Azure SQL-Server und -Datenbanken zu Compliance-, erweiterten Erkennungs- und Untersuchungszwecken aktiviert wird. |
| Transparent Data Encryption für SQL | Empfiehlt, dass Verschlüsselung im Ruhezustand für Ihre Azure SQL-Datenbanken, die zugehörigen Sicherungen und die Transaktionsprotokolldateien aktiviert wird, sodass Ihre Daten selbst dann, wenn unberechtigt auf sie zugegriffen wird, nicht gelesen werden können. |

10. Sobald Sie alle Optionen konfiguriert haben, klicken Sie auf **Speichern**, um diese Änderungen zu übernehmen.

> [AZURE.NOTE]Wird Datensammlung aktiviert, wird der Überwachungs-Agent installiert. Wenn Sie Datensammlung momentan von diesem Standort nicht aktivieren möchten, können Sie dies später über die Integritäts- und Empfehlungsansichten tun. Sie können Datensammlung auch für das Abonnement oder nur für einen ausgewählten virtuellen Computer aktivieren. Weitere Informationen zu den unterstützten virtuellen Computern finden Sie unter [Häufig gestellte Fragen (FAQ) zu Azure Security Center](security-center-faq.md).

## Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Sicherheitsrichtlinien in Azure Security Center konfigurieren können. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

- [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md) – Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) – Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
- [Häufig gestellte Fragen (FAQ) zu Security Center](security-center-faq.md) – Häufig gestellte Fragen zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) – Suchen Sie nach Blogbeiträgen über Azure-Sicherheit und -Compliance.

<!---HONumber=AcomDC_1203_2015-->