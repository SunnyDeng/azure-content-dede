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
   ms.date="01/29/2016"
   ms.author="yurid"/>

#Überwachen der Sicherheitsintegrität in Azure Security Center
Dieses Dokument unterstützt Sie bei der Verwendung der Überwachungsfunktionen in Azure Security Center zur Überwachung der Richtliniencompliance.

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center.

## Was ist Azure Security Center?
Azure Security Center unterstützt Sie beim Vorbeugen, Erkennen und Beheben von Bedrohungen. Mit dieser Cloudlösung gewinnen Sie mehr Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Sie bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Spektrum an Sicherheitslösungen verwendet werden.

##Was ist Überwachen der Sicherheitsintegrität?
Überwachung wird häufig als Beobachten und Warten auf ein Eintreten eines Ereignisses aufgefasst, damit auf diese Situation reagiert werden kann. Sicherheitsüberwachung bezieht sich auf eine proaktive Strategie, bei der Ihre Ressourcen überwacht werden, um Systeme zu erkennen, die nicht den Unternehmensstandards oder bewährten Methoden entsprechen.

##Überwachung der Sicherheitsintegrität
Nachdem [Sicherheitsrichtlinien](security-center-policies.md) für die Ressourcen des Abonnements aktiviert wurden, analysiert Azure Security Center die Sicherheit Ihrer Ressourcen, um mögliche Sicherheitsrisiken zu erkennen. Während Informationen zur Konfiguration Ihres Netzwerks sofort verfügbar sind, kann es eine Stunde oder länger dauern, bevor Informationen zur Konfiguration eines virtuellen Computers, z. B. zu Sicherheitsupdatestatus und Betriebssystemkonfiguration, verfügbar sind. Sie können den Sicherheitsstatus Ihrer Ressourcen zusammen mit allen Problemen auf dem Blatt **Sicherheitsintegrität von Ressourcen** anzeigen. Eine Liste dieser Probleme können Sie auch auf dem Blatt **Empfehlungen** anzeigen.

Weitere Informationen zur Anwendung der Empfehlungen finden Sie unter [Implementieren von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md).

Auf der Kachel **Ressourcenintegrität** können Sie den Sicherheitsstatus Ihrer Ressourcen überwachen. Sie sehen eine Reihe von Problemen mit hohem und mittlerem Schweregrad, die Aufmerksamkeit erfordern (siehe folgende Abbildung):

![Ressourcenintegrität](./media/security-center-monitoring/security-center-monitoring-fig1-new.png)

Die Sicherheitsrichtlinien, die aktiviert sind, wirken sich auf die Typen von Sicherheitsmechanismen aus, die hier überwacht werden. Erkennt Azure Security Center ein Sicherheitsrisiko, das behoben werden muss, etwa ein virtueller Computer mit fehlenden Sicherheitsupdates oder ein Subnetz ohne [Netzwerksicherheitsgruppe](virtual-networks-nsg.md), wird dies hier angegeben.

###Virtueller Computer
Wenn Sie auf der Kachel **Ressourcenintegrität** auf **Virtuelle Computer** klicken, wird das Blatt **Virtuelle Computer** geöffnet. Dieses Blatt enthält weitere Details zu den Integrations- und Vorbeugungsschritten sowie eine Liste aller virtuellen Computer, die von Azure Security Center überwacht werden (siehe folgende Abbildung):

![Fehlendes Systemupdate nach VM](./media/security-center-monitoring/security-center-monitoring-fig2-2-new.png)

Nachdem Sie dieses Blatt geöffnet haben, sehen Sie drei Abschnitte:

- Schritte zur Integration
- Empfehlungen für virtuelle Computer
- Virtuelle Computer

Für jeden Abschnitt können Sie eine einzelne Option auswählen, um weitere Details zu dem empfohlenen Schritt zu sehen und dieses Problems zu beheben. In den folgenden Abschnitten werden diese Bereiche ausführlicher behandelt.

####Schritte zur Integration
In diesem Abschnitt wird die Gesamtanzahl von virtuellen Computern angezeigt, die für die Datensammlung initialisiert wurden, sowie deren aktueller Status. Sobald für die einzelnen virtuellen Computer die Datensammlung initialisiert wurde, sind sie bereit zum Empfang der Azure Security Center-Sicherheitsrichtlinien. Wenn Sie auf diesen Eintrag klicken, wird das Blatt **Initialisieren der Datensammlung** angezeigt, und Sie sehen die Namen der virtuellen Computer und den aktuellen Status der Datensammlung wie in der Spalte **Installationsstatus** unten angezeigt:

![Initialisierungsstatus](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####Empfehlungen für virtuelle Computer
Dieser Abschnitt enthält eine Reihe von Empfehlungen für jeden virtuellen Computer, der von Azure Security Center überwacht wird. Die erste Spalte enthält die Beschreibung der Empfehlung, die zweite Spalte enthält die Gesamtanzahl der von der Empfehlung betroffenen virtuellen Computer, und in der dritten Spalte finden Sie den Schweregrad (siehe folgende Abbildung):

![Empfehlungen für virtuelle Computer](./media/security-center-monitoring/security-center-monitoring-fig4-2-new.png)

Jeder Empfehlung ist eine Reihe von Aktionen zugeordnet, die ausgeführt werden können, wenn Sie darauf klicken. Wenn Sie beispielsweise auf **Fehlende Systemupdates** klicken, wird das Blatt **Fehlende Systemupdates** mit der Liste der virtuellen Computer, für die Patches fehlen, und dem Schweregrad für jeden virtuellen Computer angezeigt (siehe folgende Abbildung):

![Fehlende Systemupdates](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

Auf dem Blatt „Fehlende Systemupdates“ wird eine Tabelle mit folgenden Informationen angezeigt:

- **VIRTUELLER COMPUTER**: Der Name des virtuellen Computers, bei dem Updates fehlen.
- **SYSTEMUPDATES**: Die Anzahl der fehlenden Systemupdates.
- **LETZTE ÜBERPRÜFUNGSZEIT**: Der Zeitpunkt, zu dem der virtuelle Computer zuletzt von Azure Security Center auf Updates überprüft wurde.
- **STATUS**: der aktuelle Status der Empfehlung 
	- **Offen**: noch nicht behandelt
	- **In Bearbeitung**: Empfehlung wird derzeit auf diese Ressourcen angewendet, es ist keine Aktion Ihrerseits erforderlich.
	- **Gelöst**: Empfehlung wurde bereits abgeschlossen (im Status „Gelöst“ ist die Farbe der Linie abgeblendet).
- **SCHWEREGRAD**: Beschreibt den Schweregrad dieser bestimmten Empfehlung: 
	- **Hoch**: Ein Sicherheitsrisiko betrifft eine bedeutsame Ressource (Anwendung, VM, Netzwerksicherheitsgruppe) und erfordert Aufmerksamkeit.
	- **Mittel**: Unkritische oder zusätzliche Schritte sind erforderlich, um einen Prozess abzuschließen oder eine Sicherheitslücke zu beseitigen.
	- **Niedrig**: Ein Sicherheitsrisiko, das behandelt werden sollte, jedoch keine unmittelbare Aufmerksamkeit erfordert. (Standardmäßig wird eine Empfehlung mit Schweregrad "Niedrig" nicht angezeigt, jedoch können Sie einen entsprechenden Filter für solche Empfehlungen einstellen, wenn Sie sie anzeigen möchten.)

Um die Details zu den empfohlenen Updates anzuzeigen, klicken Sie auf den Namen des jeweiligen virtuellen Computers. Es wird ein neues Blatt für diesen virtuellen Computer mit der Liste der fehlenden Updates geöffnet (siehe folgende Abbildung):

![Fehlende Systemupdates nach virtuellem Computer](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] Die Sicherheitsempfehlungen hier sind mit denen auf dem Blatt für Empfehlungen identisch. Weitere Informationen zur Umsetzung der Empfehlungen finden Sie unter [Implementieren von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md). Dies gilt nicht nur für virtuelle Computer, sondern auch für alle Ressourcen, die auf der Kachel „Ressourcenintegrität“ verfügbar sind.

####Virtuelle Computer
Der Abschnitt „Virtuelle Computer“ enthält eine Übersicht über alle virtuellen Computer und Empfehlungen. Jede Empfehlungsspalte steht für eine Gruppe von Empfehlungen (siehe folgende Abbildung):

![VMs](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

Anhand der Symbole, die unter jeder Empfehlung angezeigt werden, können Sie schnell erkennen, bei welchen virtuellen Computern für welche Art von Empfehlungen Aufmerksamkeit nötig ist.

Im obigen Beispiel wird für einen virtuellen Computer eine dringende Empfehlung in Bezug auf Antischadsoftware angezeigt. Um weitere Informationen über diesen virtuellen Computer zu erhalten, klicken Sie darauf. Es wird ein neues Blatt für diesen virtuellen Computer geöffnet (siehe folgende Abbildung):

![Sicherheitsdetails für virtuelle Computer](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

Dieses Blatt enthält die Sicherheitsdetails für den virtuellen Computer. Unten auf dem Blatt werden die empfohlenen Aktionen sowie der Schweregrad der einzelnen Empfehlungen angezeigt.

###Netzwerk
Der Vorbeugungsstatus für Netzwerke zeigt die virtuellen Netzwerke, die von Azure Security Center überwacht werden. Wenn Sie auf der Kachel „Ressourcenintegrität“ auf **Netzwerk** klicken, wird das Blatt **Netzwerk** mit weiteren Details geöffnet (siehe folgende Abbildung):

![Netzwerk](./media/security-center-monitoring/security-center-monitoring-fig9-new.png)

Wenn Sie dieses Blatt öffnen, sehen Sie zwei Abschnitte: – Netzwerkempfehlungen – Netzwerk
 
Für jeden Abschnitt können Sie eine einzelne Option auswählen, um weitere Details zu dem empfohlenen Schritt zu sehen, um dieses Problems zu beheben. In den folgenden Abschnitten werden diese Bereiche ausführlicher behandelt.

####Netzwerkempfehlungen

Ähnlich wie für die Ressourcenintegritätsinformationen für virtuelle Computer enthält dieses Blatt im oberen Bereich eine zusammengefasste Liste der Probleme und im unteren Bereich eine Liste der überwachten Netzwerke.

Im Abschnitt für die Statusanalyse von Netzwerken werden Vorbeugungsschritte wie [ACLs auf Endpunkten](virtual-machines-set-up-endpoints.md) nicht aktiviert, [Netzwerksicherheitsgruppen](virtual-networks-nsg.md) nicht aktiviert, **Zugriff in Netzwerksicherheitsgruppen (NSGs)** nicht eingeschränkt sowie die fehlerfreien Subnetze aufgelistet. Wenn Sie auf eine dieser Empfehlungen klicken, wird ein neues Blatt mit weiteren Details zu der Empfehlung angezeigt (siehe folgendes Beispiel):

![Endpunkt](./media/security-center-monitoring/security-center-monitoring-fig10-new.png)

In diesem Beispiel enthält das Blatt **Zugriff über öffentlichen externen Endpunkt einschränken** eine Liste der von dieser Warnung betroffenen Netzwerksicherheitsgruppen (NSGs) sowie das Subnetz und das Netzwerk, in denen diese NSGs zugeordnet wurden, den aktuellen Status dieser Empfehlung und ihren Schweregrad. Wenn Sie auf die Netzwerksicherheitsgruppe klicken, wird ein weiteres Blatt angezeigt (siehe folgende Abbildung):

![Endpunkt einschränken](./media/security-center-monitoring/security-center-monitoring-fig11-new.png)

Dieses Blatt enthält Informationen zur Netzwerksicherheitsgruppe, zum Standort und zu den derzeit aktivierten Eingangsregeln. Im unteren Teil dieses Blatts wird der virtuelle Computer aufgeführt, der dieser Netzwerksicherheitsgruppe zugeordnet ist. Wenn Sie die Eingangsregeln zum Sperren eines unerwünschten, aber derzeit geöffneten Ports aktivieren oder die Quelle der aktuellen Eingangsregel ändern möchten, können Sie oben auf dem Blatt auf **Eingangsregel bearbeiten** klicken.

####Netzwerk
Im Abschnitt **Netzwerk** finden Sie eine hierarchische Ansicht der Ressourcengruppen, Subnetze und Netzwerkschnittstellen, die den virtuellen Computern zugeordnet sind (siehe folgende Abbildung):

![Netzwerkstruktur](./media/security-center-monitoring/security-center-monitoring-fig121-new.png)

In diesem Abschnitt werden [die von Ressourcen-Manager verwalteten virtuellen Computer von den über das klassische Azure-Portal verwalteten virtuellen Computern getrennt aufgeführt](resource-manager-deployment-model.md). So können Sie schneller erkennen, ob die für einen virtuellen Computer verfügbaren Netzwerkfunktionen über die Azure-Dienstverwaltung (ASM/klassisches Azure-Portal) oder über den Azure-Ressourcen-Manager bereitgestellt wurden. Wenn Sie von dieser Stelle aus auf die Eigenschaften einer Netzwerkschnittstellenkarte zugreifen möchten, müssen Sie das Subnetz erweitern und auf den Namen des virtuellen Computers klicken. Wenn Sie diese Aktion für einen Ressourcen-Manager-basierten virtuellen Computer durchführen, wird ein neues Blatt ähnlich dem Folgenden geöffnet:

![Netzwerkstruktur](./media/security-center-monitoring/security-center-monitoring-fig13-new.png)

Auf diesem Blatt sind die Informationen zur Netzwerkschnittstellenkarte und die aktuellen Empfehlungen dafür zusammengefasst. Wenn es sich bei dem ausgewählten virtuellen Computer um einen über das klassische Azure-Portal bereitgestellten virtuellen Computer handelt, wird ein neues Blatt mit anderen Optionen angezeigt (siehe folgende Abbildung):

![ACL (Zugriffssteuerungsliste)](./media/security-center-monitoring/security-center-monitoring-fig14-new.png)

Auf diesem Blatt können Sie den öffentlichen Port und den privaten Port ändern sowie Zugriffssteuerungslisten (ACLs, Access Control Lists) für den virtuellen Computer erstellen.

###SQL
Wenn Sie auf der Kachel **Ressourcenintegrität** auf **SQL** klicken, wird das Blatt „SQL“ mit Empfehlungen geöffnet, die sich auf Probleme wie eine nicht aktivierte Überwachung oder eine nicht aktivierte Transparent Data Encryption sowie auf den allgemeinen Integritätsstatus der Datenbank beziehen.

![Ressourcenintegrität für SQL](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

Sie können auf eine dieser Empfehlungen klicken, um weitere Details zu erhalten und Aktionen auszuführen, mit denen das jeweilige Problem behoben wird. Im folgenden Beispiel wird die Erweiterung der Empfehlung **Datenbanküberwachung nicht aktiviert** angezeigt.

![Ressourcenintegrität für SQL](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

Auf dem Blatt **Überwachung für SQL-Datenbanken aktivieren** finden Sie eine Liste der SQL-Datenbanken, den Server, auf dem sie gespeichert sind, Angaben darüber, ob diese Einstellung vom Server geerbt wurde oder ob sie nur für diese Datenbank gilt, den aktuellen Problemstatus und den Schweregrad. Wenn Sie auf die Datenbank klicken, um die Empfehlung umzusetzen, wird das **Blatt Überwachung und Bedrohungserkennung** wie folgt angezeigt:

![Ressourcenintegrität für SQL](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

Um diese Funktion zu aktivieren, klicken Sie unter der Option **Überwachung** einfach auf **EIN** und dann auf **Speichern**.

###Anwendungen
Umfasst Ihre Azure-Workload Anwendungen, die sich auf [virtuellen Ressourcen-Manager-Computern](resource-manager-deployment-model.md) mit verfügbar gemachten Webports (TCP-Ports 80 und 443) befinden, kann Azure Security Center diese Ports überwachen, um potenzielle Sicherheitsprobleme zu erkennen und Wartungsschritte zu empfehlen. Wenn Sie auf die Kachel **Anwendungen** klicken, wird das Blatt **Anwendungen** geöffnet. Es enthält im Abschnitt für die Vorbeugungsschritte eine Reihe von Empfehlungen und zeigt die Anwendungsaufschlüsselung pro Host/virtuelle IP-Adresse an (siehe folgende Abbildung):

![Sicherheitsintegrität für Anwendungen](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

So wie bei den anderen Empfehlungen für die vorherigen Ressourcen können Sie auf eine Empfehlung klicken, um weitere Details zu dem Problem sowie Informationen zu dessen Beseitigung anzuzeigen. Das in der nächsten Abbildung gezeigte Beispiel ist eine Anwendung, die als **unsichere Webanwendung** identifiziert wurde. Wenn Sie die als unsicher angesehene Anwendung auswählen, wird ein weiteres Blatt mit der folgenden verfügbaren Option geöffnet:

![Apps](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

Auf dem Blatt **Ungesicherte Webanwendungen** wird eine Liste aller virtuellen Computer angezeigt, die als unsicher betrachtete Anwendungen enthalten. In der Liste werden der Name des virtuellen Computers, der aktuelle Problemstatus und die Sicherheit angezeigt. Wenn Sie auf diese Webanwendung klicken, wird das Blatt **Web Application Firewall hinzufügen** mit Optionen zum Installieren einer Web Application Firewall (WAF) von Drittanbietern angezeigt (siehe folgende Abbildung):

![Web Application Firewall (WAF) hinzufügen](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie die Überwachungsfunktionen in Azure Security Center verwenden können. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

- [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md) – erfahren Sie, wie Sie Sicherheitseinstellungen in Azure Security Center konfigurieren.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) – erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
- [Azure Security Center – häufig gestellte Fragen](security-center-faq.md) zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) – suchen Sie nach Blogbeiträgen über Azure-Sicherheit und -Compliance.

<!---HONumber=AcomDC_0204_2016-->