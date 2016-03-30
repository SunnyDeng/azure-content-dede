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
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="yurid"/>

#Überwachen der Sicherheitsintegrität in Azure Security Center
Dieses Dokument unterstützt Sie bei der Verwendung der Überwachungsfunktionen in Azure Security Center zur Überwachung der Richtliniencompliance.

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center.

## Was ist Azure Security Center?
Security Center unterstützt Sie beim Vorbeugen, Erkennen und Beheben von Bedrohungen. Mit dieser Cloudlösung gewinnen Sie mehr Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Sie bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Spektrum an Sicherheitslösungen verwendet werden.

##Was ist Überwachen der Sicherheitsintegrität?
Überwachung wird häufig als Beobachten und Warten auf ein Eintreten eines Ereignisses aufgefasst, damit auf diese Situation reagiert werden kann. Sicherheitsüberwachung bezieht sich auf eine proaktive Strategie, bei der Ihre Ressourcen überwacht werden, um Systeme zu erkennen, die nicht den Unternehmensstandards oder bewährten Methoden entsprechen.

##Überwachung der Sicherheitsintegrität
Nachdem Sie die [Sicherheitsrichtlinien](security-center-policies.md) für die Ressourcen des Abonnements aktiviert haben, analysiert Security Center die Sicherheit Ihrer Ressourcen, um mögliche Sicherheitsrisiken zu erkennen. Informationen zur Konfiguration Ihres Netzwerks sind sofort verfügbar, aber kann es eine Stunde oder länger dauern, bevor Informationen zur Konfiguration einer virtuellen Maschine – z. B. zu Sicherheitsupdatestatus und Betriebssystemkonfiguration – verfügbar sind. Sie können den Sicherheitsstatus Ihrer Ressourcen zusammen mit allen Problemen auf dem Blatt **Sicherheitsintegrität von Ressourcen** anzeigen. Eine Liste dieser Probleme können Sie auch auf dem Blatt **Empfehlungen** anzeigen.

Weitere Informationen zur Anwendung der Empfehlungen finden Sie unter [Implementieren von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md).

Auf der Kachel **Ressourcensicherheitsintegrität** können Sie den Sicherheitsstatus Ihrer Ressourcen überwachen. Im Beispiel unten sehen Sie eine Reihe von Problemen mit hohem und mittlerem Schweregrad, die Aufmerksamkeit erfordern. Die Sicherheitsrichtlinien, die aktiviert sind, wirken sich auf die Arten von Sicherheitsmechanismen aus, die überwacht werden.

![Ressourcenintegrität](./media/security-center-monitoring/security-center-monitoring-fig1-new.png)

Erkennt Security Center ein Sicherheitsrisiko, das behoben werden muss – etwa ein virtueller Computer mit fehlenden Sicherheitsupdates oder ein Subnetz ohne [Netzwerksicherheitsgruppe](../virtual-network/virtual-networks-nsg.md) –, wird dies hier angegeben.

###Überwachen virtueller Maschinen
Wenn Sie auf der Kachel **Ressourcensicherheitsintegrität** auf **Virtuelle Computer** klicken, wird das Blatt **Virtuelle Computer** geöffnet. Dieses Blatt enthält weitere Details zu den Integrations- und Vorbeugungsschritten sowie eine Liste aller virtuellen Maschinen, die von Security Center überwacht werden (siehe folgende Abbildung).

![Fehlendes Systemupdate nach VM](./media/security-center-monitoring/security-center-monitoring-fig2-2-new.png)

- Schritte zur Integration
- Empfehlungen für virtuelle Computer
- Virtuelle Computer

In jedem Abschnitt können Sie eine einzelne Option auswählen, um weitere Details zum empfohlenen Schritt für die Lösung des Problems anzuzeigen. In den folgenden Abschnitten werden diese Bereiche ausführlicher behandelt.

####Schritte zur Integration
In diesem Abschnitt wird die Gesamtzahl der virtuellen Maschinen angezeigt, die für die Datensammlung initialisiert wurden, sowie deren aktueller Status. Sobald für alle virtuellen Maschinen die Datensammlung initialisiert wurde, sind sie bereit für den Empfang der Security Center-Sicherheitsrichtlinien. Wenn Sie auf diesen Eintrag klicken, wird das Blatt **Datensammlung wird initialisiert** angezeigt, und Sie sehen die Namen der virtuellen Computer und den aktuellen Status der Datensammlung in der Spalte **Installationsstatus**. Dies ist unten dargestellt.

![Initialisierungsstatus](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####Empfehlungen für virtuelle Computer
Dieser Abschnitt enthält eine Reihe von Empfehlungen für jeden virtuellen Computer, der von Azure Security Center überwacht wird. Die erste Spalte enthält die Empfehlung, und in der zweiten Spalte wird die Gesamtzahl der VMs angegeben, die von der Empfehlung betroffen sind. Die dritte Spalte enthält den Schweregrad des Problems, wie in der folgenden Abbildung dargestellt.

![Empfehlungen für virtuelle Computer](./media/security-center-monitoring/security-center-monitoring-fig4-2-new.png)

Jeder Empfehlung ist eine Reihe von Aktionen zugeordnet, die ausgeführt werden können, wenn Sie darauf klicken. Wenn Sie beispielsweise auf **Fehlende Systemupdates** klicken, wird das Blatt **Fehlende Systemupdates** geöffnet. Auf dem Blatt sind die VMs, für die Patches fehlen, und der Schweregrad des fehlendes Updates wie unten dargestellt aufgeführt.

![Fehlende Systemupdates](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

Auf dem Blatt **Fehlende Systemupdates** wird eine Tabelle mit folgenden Informationen angezeigt:

- **VIRTUELLER COMPUTER**: Der Name der virtuellen Maschine, für die Updates fehlen.
- **SYSTEMUPDATES**: Die Anzahl der fehlenden Systemupdates.
- **LETZTE ÜBERPRÜFUNGSZEIT**: Der Zeitpunkt, zu dem die virtuelle Maschine zuletzt von Security Center auf Updates überprüft wurde.
- **STATUS**: Der aktuelle Status der Empfehlung: 
	- **Offen**: Die Empfehlung wurde noch nicht verarbeitet.
	- **In Bearbeitung**: Die Empfehlung wird derzeit auf die Ressourcen angewendet, und es ist keine Aktion Ihrerseits erforderlich.
	- **Gelöst**: Die Empfehlung wurde bereits abgeschlossen. (Wenn das Problem behoben ist, wird der Eintrag abgeblendet.)
- **SCHWEREGRAD**: Beschreibt den Schweregrad der jeweiligen Empfehlung: 
	- **Hoch**: Ein Sicherheitsrisiko betrifft eine bedeutsame Ressource (Anwendung, VM, Netzwerksicherheitsgruppe) und erfordert Aufmerksamkeit.
	- **Mittel**: Nicht kritische oder zusätzliche Schritte sind erforderlich, um einen Prozess abzuschließen oder eine Sicherheitslücke zu beseitigen.
	- **Niedrig**: Ein Sicherheitsrisiko, das behandelt werden sollte, jedoch keine unmittelbare Aufmerksamkeit erfordert. (Standardmäßig werden Empfehlungen mit dem Status „Niedrig“ nicht angezeigt, aber Sie können bei Bedarf nach diesen Empfehlungen filtern.)

Um die Details zur Empfehlung anzuzeigen, klicken Sie auf den Namen der jeweiligen virtuellen Maschine. Es wird ein neues Blatt für die virtuelle Maschine mit der Liste geöffnet. Dies ist hier dargestellt:

![Fehlende Systemupdates nach virtuellem Computer](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] Die Sicherheitsempfehlungen hier sind mit denen auf dem Blatt für Empfehlungen identisch. Weitere Informationen zur Umsetzung der Empfehlungen finden Sie unter [Implementieren von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md). Dies gilt nicht nur für virtuelle Computer, sondern auch für alle Ressourcen, die auf der Kachel „Ressourcenintegrität“ verfügbar sind.

####Abschnitt „Virtuelle Computer“
Der Abschnitt „Virtuelle Computer“ enthält eine Übersicht über alle virtuellen Computer und Empfehlungen. Jede Spalte steht für eine Gruppe von Empfehlungen (siehe folgende Abbildung):

![VMs](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

Anhand der Symbole, die unter jeder Empfehlung angezeigt werden, können Sie schnell erkennen, bei welchen virtuellen Maschinen Aufmerksamkeit nötig ist und welche Arten von Empfehlungen vorhanden sind.

Im obigen Beispiel wird für eine virtuelle Maschine eine dringende Empfehlung in Bezug auf Antischadsoftware angezeigt. Klicken Sie auf die VM, um weitere Informationen anzuzeigen. Es wird ein neues Blatt geöffnet, auf dem die Informationen zur VM wie unten dargestellt angegeben werden.

![Sicherheitsdetails für virtuelle Computer](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

Dieses Blatt enthält die Sicherheitsdetails für den virtuellen Computer. Unten auf dem Blatt werden die empfohlenen Aktionen sowie der Schweregrad der einzelnen Probleme angezeigt.

###Überwachen virtueller Netzwerke
Im Abschnitt mit dem Vorbeugungsstatus für das Netzwerk sind die virtuellen Netzwerke aufgeführt, die von Security Center überwacht werden. Wenn Sie auf der Kachel **Ressourcensicherheitsintegrität** auf **Netzwerk** klicken, wird das Blatt **Netzwerk** mit weiteren Details geöffnet (siehe folgende Abbildung):

![Netzwerk](./media/security-center-monitoring/security-center-monitoring-fig9-new.png)

Nachdem Sie dieses Blatt geöffnet haben, sehen Sie zwei Abschnitte:
- Netzwerkempfehlungen
- Netzwerk
 
In jedem Abschnitt können Sie eine individuelle Option wählen, um weitere Details zur Empfehlung zu erhalten. In den folgenden Abschnitten werden diese Bereiche ausführlicher behandelt.

####Netzwerkempfehlungen

Ähnlich wie für die Ressourcenintegritätsinformationen für virtuelle Maschinen enthält dieses Blatt im oberen Bereich eine zusammengefasste Liste der Probleme und im unteren Bereich eine Liste der überwachten Netzwerke.

![Endpunkt](./media/security-center-monitoring/security-center-monitoring-fig10-new.png)

Im Abschnitt für die Statusanalyse der Netzwerke sind potenzielle Sicherheitsprobleme und entsprechende Empfehlungen aufgeführt. Beispiele für Probleme:

- [ACLs für Endpunkte](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) sind nicht aktiviert
- [Netzwerksicherheitsgruppen](../virtual-network/virtual-networks-nsg.md) sind nicht aktiviert
- Fehlerfreie Subnetze und „Zugriff in Netzwerksicherheitsgruppen (NSGs) nicht eingeschränkt“ werden aufgeführt. 
 
Wenn Sie auf eine dieser Empfehlungen klicken, wird ein neues Blatt mit weiteren Details zur Empfehlung angezeigt (siehe folgendes Beispiel).

![Endpunkt einschränken](./media/security-center-monitoring/security-center-monitoring-fig11-new.png)

In diesem Beispiel enthält das Blatt **Zugriff über öffentlichen externen Endpunkt einschränken** eine Liste der von dieser Warnung betroffenen Netzwerksicherheitsgruppen (NSGs) sowie das Subnetz und das Netzwerk, denen diese NSGs zugeordnet wurden, den aktuellen Status dieser Empfehlung und den Schweregrad des Problems. Wenn Sie auf die Netzwerksicherheitsgruppe klicken, wird ein weiteres Blatt angezeigt (siehe folgende Abbildung).

Dieses Blatt enthält die Informationen und den Speicherort der Netzwerksicherheitsgruppe. Außerdem ist hier die Liste mit den eingehenden Regeln angegeben, die derzeit aktiviert sind. Im unteren Teil dieses Blatts wird der virtuelle Computer aufgeführt, der dieser Netzwerksicherheitsgruppe zugeordnet ist. Klicken Sie oben im Blatt auf **Eingangsregel bearbeiten**, wenn Sie die Eingangsregeln zum Sperren eines unerwünschten, aber derzeit geöffneten Ports aktivieren oder die Quelle der aktuellen Eingangsregel ändern möchten.

####Abschnitt „Netzwerk“

Der Abschnitt **Netzwerk** enthält eine hierarchische Ansicht der Ressourcengruppen, Subnetze und Netzwerkschnittstellen, die Ihrem virtuellen Computer zugeordnet sind (siehe folgende Abbildung).

![Netzwerkstruktur](./media/security-center-monitoring/security-center-monitoring-fig121-new.png)

In diesem Abschnitt werden [auf dem Resource Manager basierende VMs von klassischen VMs abgegrenzt](../resource-manager-deployment-model.md). So können Sie schnell erkennen, ob für die virtuelle Maschine Azure Service Management- oder Azure Resource Management-Netzwerkfunktionen verfügbar sind. Wenn Sie von dieser Stelle aus auf die Eigenschaften einer Netzwerkschnittstellenkarte zugreifen möchten, müssen Sie das Subnetz erweitern und auf den Namen des virtuellen Computers klicken. Wenn Sie diese Aktion für eine Ressourcen-Manager-basierte virtuelle Maschine durchführen, wird ein neues Blatt geöffnet, das etwa wie folgt aussieht:

![Netzwerkstruktur](./media/security-center-monitoring/security-center-monitoring-fig13-new.png)

Auf diesem Blatt sind die Informationen zur Netzwerkschnittstellenkarte und die aktuellen Empfehlungen dafür zusammengefasst. Wenn es sich bei der ausgewählten virtuellen Maschine um eine über das klassische Azure-Portal bereitgestellte virtuelle Maschine handelt, wird ein neues Blatt mit anderen Optionen angezeigt (siehe folgende Abbildung).

![ACL (Zugriffssteuerungsliste)](./media/security-center-monitoring/security-center-monitoring-fig14-new.png)

Auf diesem Blatt können Sie Änderungen an den öffentlichen und privaten Ports vornehmen und eine ACL für diese VM erstellen.

###Überwachen von SQL-Ressourcen
Wenn Sie auf der Kachel **Ressourcensicherheitsintegrität** auf **SQL** klicken, wird das Blatt „SQL“ geöffnet. Es enthält Empfehlungen zu Problemen, die mit der Überwachung oder der nicht aktivierten Transparent Data Encryption zusammenhängen. Außerdem werden Empfehlungen zum allgemeinen Integritätsstatus der Datenbank angegeben.

![Ressourcenintegrität für SQL](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

Sie können auf eine dieser Empfehlungen klicken, um weitere Details zu Aktionen zu erhalten, mit denen das jeweilige Problem behoben werden kann. Im folgenden Beispiel wird die Erweiterung der Empfehlung **Datenbanküberwachung nicht aktiviert** gezeigt.

![Ressourcenintegrität für SQL](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

Das Blatt **Überwachung für SQL-Datenbanken aktivieren** enthält die folgenden Informationen:

- Liste der SQL-Datenbanken
- Server, auf dem sich eine Datenbank befindet
- Informationen dazu, ob die Einstellung vom Server geerbt wurde oder in der Datenbank eindeutig ist
- Aktueller Status
- Schweregrad des Problems

Wenn Sie auf die Datenbank klicken, um die Empfehlung umzusetzen, wird das Blatt **Überwachung und Bedrohungserkennung** wie folgt angezeigt.

![Ressourcenintegrität für SQL](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

Um die Überwachung zu aktivieren, klicken Sie unter der Option **Überwachung** einfach auf **EIN** und dann auf **Speichern**.

###Überwachen von Anwendungen
Wenn Ihre Azure-Workload Anwendungen umfasst, die sich auf [Resource Manager-VMs](../resource-manager-deployment-model.md) mit verfügbar gemachten Webports (TCP-Ports 80 und 443) befinden, kann Security Center diese Ports überwachen, um potenzielle Sicherheitsprobleme zu erkennen und Wartungsschritte zu empfehlen. Wenn Sie auf die Kachel **Anwendungen** klicken, wird das Blatt **Anwendungen** mit einer Reihe von Empfehlungen im Abschnitt „Vorbeugungsschritte“ geöffnet. Außerdem werden die Anwendungen nach Host/virtueller IP aufgeschlüsselt. Die ist unten dargestellt.

![Sicherheitsintegrität für Anwendungen](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

Sie können wie bei den anderen Empfehlungen auch auf eine Empfehlung klicken, um weitere Details zum Problem sowie Informationen zu dessen Beseitigung anzuzeigen. Das in der nächsten Abbildung gezeigte Beispiel ist eine Anwendung, die als unsichere Webanwendung identifiziert wurde. Wenn Sie die als unsicher angesehene Anwendung auswählen, wird ein weiteres Blatt mit der folgenden verfügbaren Option geöffnet:

![Apps](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

Auf dem Blatt **Ungesicherte Webanwendungen** wird eine Liste aller virtuellen Computer angezeigt, die als unsicher betrachtete Anwendungen enthalten. In der Liste werden der Name der virtuellen Maschine, der aktuelle Problemstatus und der Schweregrad des Problems angezeigt. Wenn Sie auf diese Webanwendung klicken, wird das Blatt **Web Application Firewall hinzufügen** mit Optionen zum Installieren einer Web Application Firewall (WAF) von Drittanbietern angezeigt (siehe folgende Abbildung).

![Web Application Firewall (WAF) hinzufügen](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie die Überwachungsfunktionen in Azure Security Center verwenden können. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

- [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md) – erfahren Sie, wie Sie Sicherheitseinstellungen in Azure Security Center konfigurieren.
- [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) – erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
- [Azure Security Center – häufig gestellte Fragen](security-center-faq.md) zur Verwendung des Diensts.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) – suchen Sie nach Blogbeiträgen über Azure-Sicherheit und -Compliance.

<!---HONumber=AcomDC_0323_2016-->