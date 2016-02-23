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
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="terrylan"/>

# Azure Security Center – Häufig gestellte Fragen

In diesem Artikel zu häufig gestellten Fragen werden Fragen zu Azure Security Center beantwortet.

> [AZURE.NOTE] Die Informationen in diesem Dokument gelten für die Vorschauversion von Azure Security Center.

## Allgemeine Fragen

### Was ist Security Center?
 Security Center unterstützt Sie beim Vorbeugen, Erkennen und Beheben von Bedrohungen. Mit dieser Cloudlösung gewinnen Sie mehr Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Sektrum an Sicherheitslösungen verwendet werden.

### Wie erhalte Security Center?
 Security Center wird mit Ihrem Microsoft Azure-Abonnement aktiviert und über das [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) aufgerufen. ([Melden Sie sich beim Portal an](https://portal.azure.com), wählen Sie **Durchsuchen** aus, und führen Sie dann einen Bildlauf zum **Security Center** aus.) Möglicherweise Sehen im Dashboard Sie sofort einige Sicherheitsempfehlungen. Dies liegt daran, dass der Dienst den Sicherheitsstatus einiger Sicherheitsmechanismen anhand ihrer Konfiguration in Azure bewerten kann. Damit Sie auf den vollständigen Satz von Funktionen zur Sicherheitsüberwachung, zu Empfehlungen und zu Warnmeldungen zugreifen können, müssen Sie die [Datensammlung aktivieren](#data-collection).

## Abrechnung

### Wie funktioniert die Abrechnung für Security Center?
Informationen hierzu finden Sie unter [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/).

## Datensammlung

### Wie aktiviere ich Datensammlung?<a name=data-collection></a>
Sie können Datensammlung für Ihre Azure-Abonnements in der Sicherheitsrichtlinie aktivieren. Zum Aktivieren der Datensammlung [melden Sie sich beim Azure-Portal an](https://portal.azure.com). Wählen Sie **Durchsuchen**, **Security Center** und dann **Sicherheitsrichtlinie** aus. Legen Sie **Datensammlung** auf **Ein** fest, und konfigurieren Sie die Speicherkonten, in denen Sie die gesammelten Daten anzeigen möchten (siehe die Frage „[Wo werden meine Daten gespeichert?](#where-is-my-data-stored)“). Wenn **Datensammlung** aktiviert ist, werden automatisch Sicherheitskonfigurations- und Ereignisinformationen von allen virtuellen Computern gesammelt, die im Abonnement unterstützt werden.

### Was geschieht, wenn ich Datensammlung aktiviere?
Datensammlung wird über den Azure-Überwachungs-Agent und die Azure-Erweiterung für Sicherheitsüberwachung aktiviert. Die Azure-Erweiterung für Sicherheitsüberwachung sucht in ETW-Ablaufverfolgungen (Event Tracing for Windows, [Ereignisablaufverfolgung für Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)) nach verschiedenen relevanten Sicherheitskonfigurationen und Ereignissen.

Zusätzlich löst das Betriebssystem Ereignisprotokollereignisse zu allen Aktivitäten aus. Der Azure-Agent für Sicherheitsüberwachung liest Ereignisprotokolleinträge und ETW-Ablaufverfolgungen und kopiert diese dann zur Analyse in Ihr Speicherkonto. Dies ist das Speicherkonto, das Sie in der Sicherheitsrichtlinie konfiguriert haben. Weitere Informationen über das Speicherkonto finden Sie unter der Frage „[Wo werden meine Daten gespeichert?](#where-is-my-data-stored)“

### Wirkt sich der Überwachungs-Agent oder die Erweiterung für Sicherheitsüberwachung auf die Leistung meiner Server aus?
Der Agent und die Erweiterung beanspruchen eine äußerst geringe Menge von Systemressourcen und sollten nur eine geringe Auswirkung auf die Leistung haben.

### Wie gehe ich vor, wenn ich die Datensammlung deaktivieren möchte?
Sie können die Datensammlung für ein Abonnement in der Sicherheitsrichtlinie deaktivieren. ([Melden Sie sich beim Azure-Portal an](https://portal.azure.com). Wählen Sie **Durchsuchen**, **Security Center** und dann **Sicherheitsrichtlinie** aus.) Wenn Sie auf ein Abonnement klicken, wird ein neues Blatt geöffnet, das Ihnen die Option bietet, die Datensammlung zu deaktivieren. Wählen Sie im oberen Menüband die Option **Agents löschen** aus, um die Agents von vorhandenen virtuellen Computern zu entfernen.

### Wo werden meine Daten gespeichert?<a name=where-is-my-data-stored></a>
Wählen Sie für jede Region, in der Sie virtuelle Computer ausführen, ein Speicherkonto, in dem Daten dieser virtuellen Computer gespeichert werden. Dies macht es einfach für Sie, Daten aus Datenschutz- und Datenhoheitszwecken im selben geografischen Gebiet zu speichern.

Das Speicherkonto für ein Abonnement wählen Sie in der Sicherheitsrichtlinie aus. ([Melden Sie sich beim Azure-Portal an](https://portal.azure.com). Wählen Sie **Durchsuchen**, **Security Center** und dann **Sicherheitsrichtlinie** aus.) Wenn Sie auf ein Abonnement klicken, wird ein neues Blatt geöffnet. Klicken Sie auf **Speicherkonten wählen**, um eine Region auszuwählen. Gesammelte Daten werden aus Sicherheitsgründen logisch von anderen Kundendaten getrennt.

Weitere Informationen zu Azure Storage und zu Speicherkonten finden Sie unter [Speicherdokumentation](https://azure.microsoft.com/documentation/services/storage/) und [Informationen zu Azure-Speicherkonten](../storage/storage-create-storage-account.md).

## Verwendung von Security Center

### Was ist eine Sicherheitsrichtlinie?
In einer Sicherheitsrichtlinie wird der Satz von Sicherheitsmechanismen definiert, die für Ressourcen in dem angegebenen Abonnement zu empfehlen sind. In Security Center definieren Sie Richtlinien für Ihre Azure-Abonnements entsprechend den Sicherheitserfordernissenen Ihres Unternehmens sowie entsprechend dem Typ der Anwendungen oder der Vertraulichkeit der Daten in jedem Abonnement.

Beispielsweise haben Ressourcen, die für Entwicklungs- oder Testzwecke verwendet werden, unter Umständen Sicherheitsanforderungen, die sich von denen für Produktionsanwendungen unterscheiden. Ähnlich kann für Anwendungen mit reglementierten Daten, etwa personenbezogene Informationen (Personally Identifiable Information, PII), eine höhere Sicherheitsstufe erforderlich sein. Die Sicherheitsempfehlungen und -überwachung werden entsprechend den in Security Center aktivierten Sicherheitsrichtlinien umgesetzt. Weitere Informationen zu Sicherheitsrichtlinien finden Sie unter [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md).

### Wie kann ich eine Sicherheitsrichtlinie ändern?
Sicherheitsrichtlinien sind für jedes Abonnement konfiguriert. Zum Ändern einer Sicherheitsrichtlinie müssen Sie entweder der Besitzer oder ein Mitwirkender dieses Abonnements sein.

Weitere Informationen dazu, wie eine Sicherheitsrichtlinie konfiguriert wird, finden Sie unter [Festlegen von Sicherheitsrichtlinien in Azure Security Center](security-center-policies.md).

### Was ist eine Sicherheitsempfehlung?
 Security Center analysiert den Sicherheitsstatus Ihrer Azure-Ressourcen. Werden potenzielle Sicherheitsrisiken identifiziert, erstellt Security Center Empfehlungen. Entsprechend den Empfehlungen werden Sie durch den Prozess des Konfigurierens des erforderlichen Sicherheitsmechanismus geführt. Beispiele sind:

- Bereitstellung von Antischadsoftwareprogrammen, um Schadsoftware zu erkennen und zu entfernen
- Konfigurieren von [Netzwerksicherheitsgruppen](../virtual-network/virtual-networks-nsg.md) und Regeln, um Datenverkehr zu virtuellen Maschinen zu steuern
- Bereitstellung von Web Application Firewalls als Schutz vor Angriffen, die auf Ihre Webanwendungen abzielen
- Bereitstellen von fehlenden Systemupdates
- Behandeln von Betriebssystemkonfigurationen, die nicht den empfohlenen Basisregeln entsprechen

Hier sind nur Empfehlungen aufgeführt, die in den Sicherheitsrichtlinien aktiviert sind.

### Wie kann ich den aktuellen Sicherheitsstatus meiner Azure-Ressourcen anzeigen?
Auf dem Blatt **Security Center** wird auf der Kachel **Ressourcenintegrität** der Gesamtsicherheitsstatus Ihrer Umgebung aufgegliedert nach virtuellen Computern, Webanwendungen und weiteren Ressourcen gezeigt. Jede Ressource hat einen Indikator, der kennzeichnet, ob irgendwelche möglichen Sicherheitsrisiken festgestellt wurden. Wenn Sie auf die Kachel **Ressourcenintegrität** klicken, werden Ihre Ressourcen angezeigt, und es wird gekennzeichnet, wo Aufmerksamkeit erforderlich ist oder Probleme vorhanden sein können.

### Wodurch wird eine Sicherheitswarnung ausgelöst?
 Security Center erfasst, analysiert und kombiniert automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von Partnerlösungen wie Antischadsoftware und Firewalls. Wenn Security Center Bedrohungen erkennt, wird eine Sicherheitswarnung erstellt. Beispiele für eine Erkennung sind:

- Gefährdete virtuelle Maschinen, die mit bekannten bösartigen IP-Adressen kommunizieren
- Erweiterte Schadsoftware, die mithilfe der Windows-Fehlerberichterstattung erkannt wurde
- Brute-Force-Angriffe gegen virtuelle Maschinen
- Sicherheitswarnungen von integrierten Partnersicherheitslösungen wie Antischadsoftwareprogrammen oder Web Application Firewalls

### Wie werden Berechtigungen in Azure Center behandelt?
 Security Center unterstützt rollenbasierten Zugriff. Weitere Informationen zur rollenbasierten Zugriffssteuerung (RBAC) in Azure finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure Active Directory](../active-directory/role-based-access-control-configure.md).

Wenn ein Benutzer Security Center öffnet, werden nur Empfehlungen und Warnungen angezeigt, die sich auf die Ressourcen beziehen, auf die der Benutzer Zugriff hat. Dies bedeutet, dass ein Benutzer nur Elemente sieht, die sich auf Ressourcen beziehen, für die ihm für das Abonnement oder die Ressourcengruppe, zu der eine Ressource gehört, die Rolle „Besitzer“, „Mitwirkender“ oder „Leser“ zugeordnet ist.

Zum Bearbeiten einer Sicherheitsrichtlinie müssen Sie entweder der Besitzer oder ein Mitwirkender des Abonnements sein.

### Welche Typen von virtuellen Maschinen werden unterstützt?
Sowohl [klassische virtuelle Computer und virtuelle Ressourcen-Manager-Computer](../azure-classic-rm.md) werden unterstützt, einschließlich virtueller Computer, die zu Azure Service Fabric-Clustern gehören.

Zugriffssteuerungslisten-Empfehlungen gelten derzeit für klassische virtuelle Computer. Regeln für Netzwerksicherheitsgruppen und Empfehlungen für die Installation einer von Web Application Firewalls gelten derzeit nur für virtuelle Ressourcen-Manager-Maschinen.

### Werden virtuelle Linux-Computer unterstützt?
Azure Security Center bietet eine Basisüberwachung für virtuelle Linux-Computer (nur für die Ubuntu-Versionen 12.04, 14.04, 14.10 und 15.04). Zukünftig werden weitere Sicherheitintegritätsüberwachung und Datensammlung/-analysen verfügbar sein, und es sollen weitere Linux-Distributionen unterstützt werden.

<!---HONumber=AcomDC_0218_2016-->