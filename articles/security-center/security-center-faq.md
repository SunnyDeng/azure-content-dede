<properties
   pageTitle="Häufig gestellte Fragen (FAQ) zu Azure Security Center | Microsoft Azure"
   description="Dieses FAQ-Dokument beantwortet Fragen zu Azure Security Center."
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
   ms.date="12/10/2015"
   ms.author="terrylan"/>

# Azure Security Center – Häufig gestellte Fragen

Hier werden häufig gestellte Fragen zu Azure Security Center beantwortet. Azure Security Center ist ein Dienst, der Sie durch größere Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen dabei unterstützt, Bedrohungen zu verhindern, zu erkennen und darauf zu reagieren.

> [AZURE.NOTE]Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center.

## Allgemeine Fragen

### Was ist Azure Security Center?
Azure Security Center unterstützt Sie beim Verhindern, Erkennen und Beheben von Bedrohungen durch größere Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Sektrum an Sicherheitslösungen verwendet werden.

### Wie erhalte ich Azure Security Center?
Azure Security Center wird mit Ihrem Microsoft Azure-Abonnement aktiviert und über das [Azure-Portal](http://azure.microsoft.com/features/azure-portal/) aufgerufen. ([Melden Sie sich beim Portal an](https://portal.azure.com), wählen Sie **Durchsuchen** aus, und führen Sie einen Bildlauf zum **Security Center** aus.) Möglicherweise Sehen im Dashboard Sie sofort einige Sicherheitsempfehlungen. Dies liegt daran, dass der Dienst den Sicherheitsstatus einiger Sicherheitsmechanismen anhand ihrer Konfiguration in Azure bewerten kann. Damit Sie den vollständigen Satz von Funktionen zur Sicherheitsüberwachung, zu Empfehlungen und zu Warnmeldungen ausprobieren können, müssen Sie [Datensammlung aktivieren](#data-collection).

## Abrechnung

### Wie funktioniert die Abrechnung für Azure Security Center?
Informationen hierzu finden Sie unter [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/).

## Datensammlung

### Wie aktiviere ich Datensammlung?<a name=data-collection></a>
Sie können Datensammlung für Ihre Azure-Abonnements in der Sicherheitsrichtlinie aktivieren. Um die Datensammlung zu aktivieren, [melden Sie sich beim Azure-Portal an](https://portal.azure.com). Wählen Sie **Durchsuchen**, **Security Center** und dann **Sicherheitsrichtlinie** aus. Legen Sie **Datensammlung** auf **Ein** fest, und konfigurieren Sie die Speicherkonten, in denen Sie die gesammelten Daten anzeigen möchten (siehe die Frage [Wo werden meine Daten gespeichert?](#where-is-my-data-stored)). Wenn **Datensammlung** aktiviert ist, werden automatisch Sicherheitskonfigurations- und Ereignisinformationen von allen virtuellen Computern gesammelt, die im Abonnement unterstützt werden.

### Was geschieht, wenn ich Datensammlung aktiviere?
Datensammlung wird über den Azure-Überwachungs-Agent und die Azure-Erweiterung für Sicherheitsüberwachung aktiviert. Die Azure-Erweiterung für Sicherheitsüberwachung sucht in ETW-Ablaufverfolgungen (Event Tracing for Windows, [Ereignisablaufverfolgung für Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)) nach verschiedenen sicherheitsrelevanten Konfigurationen und Ereignissen. Zusätzlich löst das Betriebssystem Ereignisprotokollereignisse zu jeder Aktivität aus. Der Azure-Überwachungs-Agent liest Ereignisprotokolleinträge und ETW-Ablaufverfolgungen und kopiert diese zur Analyse in Ihr Speicherkonto. Dies ist das Speicherkonto, das Sie in der Sicherheitsrichtlinie konfiguriert haben. Weitere Informationen über das Speicherkonto finden Sie in der Farge [Wo werden meine Daten gespeichert?](#where-is-my-data-stored)

### Wirkt sich der Überwachungs-Agent oder die Erweiterung für Sicherheitsüberwachung auf die Leistung meiner Server aus?
Der Agent und die Erweiterung beanspruchen eine äußerst geringe Menge von Systemressourcen und sollten nur eine geringe Auswirkung auf die Leistung haben.

### Wie gehe ich vor, wenn ich Datensammlung deaktivieren möchte?
Sie können **Datensammlung** für ein Abonnement in der Sicherheitsrichtlinie deaktivieren. ([Melden Sie sich beim Azure-Portal an](https://portal.azure.com). Wählen Sie **Durchsuchen**, **Security Center** und dann **Sicherheitsrichtlinie** aus.) Wenn Sie auf ein Abonnement klicken, wird ein neues Blatt geöffnet, das Ihnen die Option bietet, Datensammlung zu deaktivieren. Wählen Sie im oberen Menüband die Option **Agents löschen** aus, um die Agents von vorhandenen virtuellen Computern zu entfernen.

### Wo werden meine Daten gespeichert?<a name=where-is-my-data-stored></a>
Wählen Sie für jede Region, in der Sie virtuelle Computer ausführen, ein Speicherkonto, in dem Daten dieser virtuellen Computer gespeichert werden. Dies macht es einfach für Sie, Daten aus Datenschutz- und Datenhoheitszwecken im selben geografischen Gebiet zu speichern. Das Speicherkonto für ein Abonnement wählen Sie in der Sicherheitsrichtlinie aus. ([Melden Sie sich beim Azure-Portal an](https://portal.azure.com). Wählen Sie **Durchsuchen**, **Security Center** und dann **Sicherheitsrichtlinie** aus.) Wenn Sie auf ein Abonnement klicken, wird ein neues Blatt geöffnet. Klicken Sie auf **Speicherkonten wählen**, um eine Region auszuwählen. Die gesammelten Daten werden aus Sicherheitsgründen logisch von anderen Kundendaten getrennt.

Weitere Informationen zu Azure-Speicher und zu Speicherkonten finden Sie unter [Speicherdokumentation](https://azure.microsoft.com/documentation/services/storage/) und [Informationen zu Azure-Speicherkonten](storage-create-storage-account.md).

## Verwenden von Azure Security Center

### Was ist eine Sicherheitsrichtlinie?
In einer Sicherheitsrichtlinie wird der Satz von Sicherheitsmechanismen definiert, die für Ressourcen in dem angegebenen Abonnement zu empfehlen sind. In Azure Security Center definieren Sie Richtlinien für Ihre Azure-Abonnements entsprechend den Sicherheitserfordernissenen Ihres Unternehmens sowie entsprechend dem Typ der Anwendungen oder der Vertraulichkeit der Daten in jedem Abonnement.

Beispielsweise haben Ressourcen, die für Entwicklungs- oder Testzwecke verwendet werden, möglicherweise Sicherheitsanforderungen, die sich von denen unterscheiden, die für Produktionsanwendungen verwendet werden. Ähnlich kann für Anwendungen mit reglementierten Daten, etwa personenbezogene Informationen (Personally Identifiable Information), eine höhere Sicherheitsstufe erforderlich sein. Die Sicherheitsempfehlungen und -überwachung werden entsprechend den in Azure Security Center aktivierten Sicherheitsrichtlinien umgesetzt. Weitere Informationen zu Sicherheitsrichtlinien finden Sie unter [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md).

### Wie kann ich eine Sicherheitsrichtlinie ändern?
Sicherheitsrichtlinien sind für jedes Abonnement konfiguriert. Damit Sie eine Sicherheitsrichtlinie ändern können, müssen Sie ein Besitzer oder Mitwirkender dieses Abonnements sein.

Weitere Informationen dazu, wie eine Sicherheitsrichtlinie konfiguriert wird, finden Sie unter [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md).

### Was ist eine Sicherheitsempfehlung?
Azure Security Center analysiert den Sicherheitsstatus Ihrer Azure-Ressourcen. Werden mögliche Sicherheitsrisiken festgestellt, werden Empfehlungen erstellt. Entsprechend den Empfehlungen werden Sie durch den Prozess des Konfigurierens des erforderlichen Sicherheitsmechanismus geführt. Beispiele sind:

- Bereitstellung von Antischadsoftware, um bösartige Software zu erkennen und zu entfernen
- Konfigurieren von [Netzwerksicherheitsgruppen](virtual-networks-nsg.md) und Regeln, um Datenverkehr zu virtuellen Computern zu steuern
- Bereitstellung einer Web Application Firewall als Schutz vor Angriffen, die auf Ihre Webanwendungen abzielen
- Bereitstellen von fehlenden Systemupdates
- Behandeln von Betriebssystemkonfigurationen, die nicht den empfohlenen Basisregeln entsprechen

Hier sind nur Empfehlungen aufgeführt, die in den Sicherheitsrichtlinien aktiviert sind.

### Wie kann ich den aktuellen Sicherheitsstatus meiner Azure-Ressourcen anzeigen?
Auf dem Blatt **Security Center** wird auf der Kachel **Ressourcenintegrität** der Gesamtsicherheitsstatus Ihrer Umgebung aufgegliedert nach virtuellen Computern, Webanwendungen und weiteren Ressourcen gezeigt. Jede Ressource hat einen Indikator, der kennzeichnet, ob irgendwelche möglichen Sicherheitsrisiken festgestellt wurden. Wenn Sie auf die Kachel „Ressourcenintegrität“ klicken, werden Ihre Ressourcen angezeigt, und es wird gekennzeichnet, wo Aufmerksamkeit erforderlich ist oder Probleme vorhanden sein können.

### Wodurch wird eine Sicherheitswarnung ausgelöst?
Azure Security Center erfasst, analysiert und kombiniert automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von Partnerlösungen wie Antischadsoftware und Firewalls. Wurden Bedrohungen erkannt, wird eine Sicherheitswarnung erstellt. Beispiele für eine Erkennung sind:

- Gefährdete virtuelle Computer, die mit bekannten bösartigen IP-Adressen kommunizieren
- Erweiterte Schadsoftware, die mithilfe der Windows-Fehlerberichterstattung erkannt wurde
- Brute-Force-Angriffe gegen virtuelle Computer
- Sicherheitswarnungen von integrierten Partnersicherheitslösungen wie Antischadsoftware oder Web Application Firewalls

### Wie werden Berechtigungen in Azure Security Center behandelt?
Azure Security Center unterstützt rollenbasierten Zugriff. Weitere Informationen zur rollenbasierten Zugriffssteuerung (RBAC) in Azure finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure Active Directory](role-based-access-control-configure.md).

Wenn ein Benutzer Azure Security Center öffnet, werden nur Empfehlungen und Warnungen angezeigt, die sich auf die Ressourcen beziehen, auf die der Benutzer Zugriff hat. Dies bedeutet, dass ein Benutzer nur Elemente sieht, die sich auf Ressourcen beziehen, für die ihm für das Abonnement oder die Ressourcengruppe, zu der eine Ressource gehört, die Rolle „Besitzer“, „Mitwirkender“ oder „Leser“ zugeordnet ist.

Damit Sie eine Sicherheitsrichtlinie bearbeiten können, müssen Sie ein Besitzer oder Mitwirkender des Abonnements sein.

### Welche Typen von virtuellen Maschinen werden unterstützt?
Es werden [klassische virtuelle Computer und virtuelle Ressourcen-Manager-Computer](azure-classic-rm.md) unterstützt, einschließlich virtueller Computer, die zu Azure Service Fabric-Clustern gehören.

Zugriffssteuerungslisten-Empfehlungen gelten derzeit für klassische virtuelle Computer. Netzwerksicherheitsgruppen und Empfehlungen für die Installation einer Web Application Firewall gelten derzeit nur für virtuelle Ressourcen-Manager-Computer.

### Werden virtuelle Linux-Computer unterstützt?
Azure Security Center bietet eine Basisüberwachung für virtuelle Linux-Computer (nur für die Ubuntu-Versionen 12.04, 14.04, 14.10 und 15.04). Zukünftig werden weitere Sicherheitintegritätsüberwachung und Datensammlung/-analysen verfügbar sein, und es sollen weitere Linux-Distributionen unterstützt werden.

<!---HONumber=AcomDC_1217_2015-->