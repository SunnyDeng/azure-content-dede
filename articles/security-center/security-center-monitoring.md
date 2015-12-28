<properties
   pageTitle="Überwachen der Sicherheitsintegrität in Azure Security Center | Microsoft Azure"
   description="Dieses Dokument bietet Ihnen einen Einstieg in die Überwachungsfunktionen in Azure Security Center."
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
   ms.date="12/16/2015"
   ms.author="yurid"/>

#Überwachen der Sicherheitsintegrität in Azure Security Center
Dieses Dokument unterstützt Sie bei der Verwendung der Überwachungsfunktionen in Azure Security Center zur Überwachung der Richtliniencompliance.

> [AZURE.NOTE]Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center.

## Was ist Azure Security Center?
Azure Security Center unterstützt Sie beim Verhindern, Erkennen und Beheben von Bedrohungen durch größere Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Sektrum an Sicherheitslösungen verwendet werden.

##Was ist Überwachen der Sicherheitsintegrität?
Überwachung wird häufig als Beobachten und Warten auf ein Eintreten eines Ereignisses aufgefasst, damit auf diese Situation reagiert werden kann. Sicherheitsüberwachung bezieht sich auf eine proaktive Strategie, bei der Ihre Ressourcen überwacht werden, um Systeme zu erkennen, die nicht den Unternehmensstandards oder bewährten Methoden entsprechen.

##Überwachung der Sicherheitsintegrität
Nachdem [Sicherheitsrichtlinien](security-center-policies.md) für die Ressourcen des Abonnements aktiviert wurden, analysiert Azure Security Center die Sicherheit Ihrer Ressourcen, um mögliche Sicherheitsrisiken zu erkennen. Während Informationen zur Konfiguration Ihres Netzwerks sofort verfügbar sind, kann es eine Stunde oder länger dauern, bevor Informationen zur Konfiguration eines virtuellen Computers, z. B. Sicherheitsupdatestatus und Betriebssystemkonfiguration, verfügbar sind. Sie können den Sicherheitsstatus Ihrer Ressourcen zusammen mit allen Problemen auf den Blättern für die Sicherheitsintegrität von Ressourcen anzeigen. Eine Liste dieser Probleme können Sie auch auf den Blättern für Empfehlungen anzeigen.

Weitere Informationen zur Anwendung der Empfehlungen finden Sie unter [Implementieren von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md).

Auf der Kachel **Ressourcenintegrität** können Sie den Sicherheitsstatus Ihrer Ressourcen überwachen. Sie sehen eine Reihe von Problemen mit hohem und mittlerem Schweregrad, die Aufmerksamkeit erfordern (siehe folgende Abbildung):

![Ressourcenintegrität](./media/security-center-monitoring/security-center-monitoring-fig1.png)

Die Sicherheitsrichtlinien, die aktiviert sind, wirken sich auf die Typen von Sicherheitsmechanismen aus, die hier überwacht werden. Erkennt Azure Security Center ein Sicherheitsrisiko, das behoben werden muss, etwa ein virtueller Computer mit fehlenden Sicherheitsupdates oder ein Subnetz ohne [Netzwerksicherheitsgruppe](virtual-networks-nsg.md), wird dies hier angegeben.

###Virtueller Computer
Wenn Sie auf der Kachel **Resources health** auf **Virtuelle Computer** klicken, wird das Blatt **Virtuelle Computer** geöffnet. Dieses Blatt enthält weitere Details zu den Integrations- und Verhinderungsschritten sowie eine Liste aller virtuellen Computer, die von Azure Security Center überwacht werden (siehe folgende Abbildung):

![Fehlendes Systemupdate nach VM](./media/security-center-monitoring/security-center-monitoring-fig2.png)

Nachdem Sie dieses Blatt geöffnet haben, sehen Sie drei Abschnitte. Für jeden Abschnitt können Sie eine einzelne Option auswählen, um weitere Details zu dem empfohlenen Schritt zu sehen, um dieses Problems zu beheben.

Wenn Sie beispielsweise unter **Verhinderungsschritte** auf **Fehlende Systemupdates** klicken, wird das Blatt **Fehlende Systemupdates** mit der Liste der virtuellen Computer, für die Patches fehlen, und dem Schweregrad für jeden virtuellen Computer geöffnet (siehe folgende Abbildung):

![Fehlendes Systemupdate – Verhinderung](./media/security-center-monitoring/security-center-monitoring-fig3.png)

Um die Details zu den empfohlenen Updates anzuzeigen, klicken Sie auf den Namen des jeweiligen virtuellen Computers. Es wird ein neues Blatt für diesen virtuellen Computer mit der Liste der fehlenden Updates geöffnet:

![Empfohlenes Systemupdate nach VM](./media/security-center-monitoring/security-center-monitoring-fig4.png)

> [AZURE.NOTE]Die Sicherheitsempfehlungen hier sind mit denen auf dem Blatt für Empfehlungen identisch. Weitere Informationen zur Umsetzung der Empfehlungen finden Sie unter [Implementieren von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md). Dies gilt nicht nur für virtuelle Computer, sondern auch für alle Ressourcen, die auf der Kachel „Ressourcenintegrität“ verfügbar sind.

###Netzwerk
Der Verhinderungsstatus für Netzwerke zeigt die virtuellen Netzwerke, die von Azure Security Center überwacht werden. Wenn Sie auf der Kachel „Ressourcenintegrität“ auf **Netzwerk** klicken, wird das Blatt **Netzwerk** mit weiteren Details geöffnet (siehe folgende Abbildung):

![Empfohlenes Systemupdate nach VM](./media/security-center-monitoring/security-center-monitoring-fig5.png)

Ähnlich wie für die Ressourcenintegritätsinformationen für virtuelle Computer enthält dieses Blatt im oberen Bereich eine zusammengefasste Liste der Probleme und im unteren Bereich eine Liste der überwachten Netzwerke.

Im Abschnitt für die Statusanalyse der Netzwerke werden Verhinderungsschritte wie nicht aktivierte [ACLs on endpoints](virtual-machines-set-up-endpoints.md) und [Netzwerksicherheitsgruppen](virtual-networks-nsg.md) sowie fehlerfreie Subnetze aufgelistet. Sie können auf jeden dieser Einträge klicken, um weitere Details zu erhalten und Aktionen auszuführen, mit denen das jeweilige Problem behoben wird.

Ein Beispiel hierfür ist die Warnung **NSGs on subnets not enabled**. Wenn Sie auf diese Warnung klicken, wird das Blatt **Configure Missing Network Security Groups for Subnets** geöffnet. Auf diesem Blatt sehen Sie eine Beschreibung des Subnetzes, für das keine Netzwerksicherheitsgruppe aktiviert ist, und die Liste der Netzwerkkarten, die zu diesem Subnetz gehören. Wählen Sie das Subnetz aus, in dem Sie die Sicherheitsgruppe anwenden möchten, und klicken Sie auf die Option **Configure NSG**. Das Blatt **Choose network security group** wird geöffnet (siehe folgende Abbildung):

![Empfohlenes Systemupdate nach VM](./media/security-center-monitoring/security-center-monitoring-fig6.png)

###SQL
Wenn Sie in **SQL** auf die Kachel **Resources health** klicken, wird das Blatt **SQL** mit Empfehlungen geöffnet, die sich auf Probleme wie eine nicht aktivierte Überwachung oder eine nicht aktivierte Transparent Data Encryption sowie auf den allgemeinen Integritätsstatus der Datenbank beziehen. Der untere Teil des Blatts enthält die SQL Server-Analyse und für jeden Server die Datenbanken, die zu ihm gehören (siehe folgende Abbildung):

![Empfohlenes Systemupdate nach VM](./media/security-center-monitoring/security-center-monitoring-fig7.png)

Sie können auf eine dieser Empfehlungen klicken, um weitere Details zu erhalten und Aktionen auszuführen, mit denen das jeweilige Problem behoben wird. Das nachstehend dargestellte Beispiel zeigt eine erweiterte Ansicht für Aktivieren von Überwachung auf SQL-Servern.

![Empfohlenes Systemupdate nach VM](./media/security-center-monitoring/security-center-monitoring-fig8.png)

###Anwendungen
Umfasst Ihre Azure-Workload Anwendungen, die sich auf [virtuellen Ressourcen-Manager-Computern](resource-manager-deployment-model.md) mit verfügbar gemachten Webports (TCP-Ports 80 und 443) befinden, kann Azure Security Center diese Ports überwachen, um potenzielle Sicherheitsprobleme zu erkennen und Wartungsschritte zu empfehlen. Wenn Sie auf die Kachel **Anwendungen** klicken, wird das Blatt **Anwendungen** geöffnet. Es enthält im Abschnitt für die Verhinderungsschritte eine Reihe von Empfehlungen und zeigt die Anwendungsaufschlüsselung pro Host/virtuelle IP-Adresse an (siehe folgende Abbildung):

![Empfohlenes Systemupdate nach VM](./media/security-center-monitoring/security-center-monitoring-fig9.png)

So wie bei den anderen Empfehlungen für die vorherigen Ressourcen können Sie auf eine Empfehlung klicken, um weitere Details zu dem Problem sowie Informationen zu dessen Beseitigung anzuzeigen. Das in der nächsten Abbildung gezeigte Beispiel ist eine Anwendung, die als **unsichere Webanwendung** identifiziert wurde. Wenn Sie die Anwendung, die nicht als sicher angesehen wurde, auswählen, wird ein weiteres Blatt mit der verfügbaren Option geöffnet, die in diesem Fall darin besteht, die Web Application Firewall zu aktivieren.

![Empfohlenes Systemupdate nach VM](./media/security-center-monitoring/security-center-monitoring-fig10.png)

## Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie die Überwachungsfunktionen in Azure Security Center verwenden können. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

- [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md) – erfahren Sie, wie Sie Sicherheitseinstellungen in Azure Security Center konfigurieren.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) – erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
- [Azure Security Center – häufig gestellte Fragen](security-center-faq.md) zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) – suchen Sie nach Blogbeiträgen über Azure-Sicherheit und -Compliance.

<!---HONumber=AcomDC_1217_2015-->