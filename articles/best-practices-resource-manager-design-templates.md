<properties
	pageTitle="Bewährte Methoden für das Entwerfen von Azure-Ressourcen-Manager-Vorlagen"
	description="Anzeigen von Entwurfsmustern für Azure-Ressourcen-Manager Vorlagen"
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="mmercuri"/>

# Bewährte Methoden für das Entwerfen von Azure-Ressourcen-Manager-Vorlagen

Bei unserer Arbeit mit Unternehmen, Systemintegratoren (SIs), Clouddienstanbietern und Projektteams für die Entwicklung von Open-Source-Software (OSS) ist es häufig erforderlich, Umgebungen oder Workloads schnell bereitzustellen oder Einheiten zu skalieren. Diese Bereitstellungen müssen unterstützt werden, bewährte Methoden befolgen und sich an bestimmte Richtlinien halten. Bei Verwenden eines flexiblen auf Azure-Ressourcen-Manager basierenden Ansatzes können Sie komplexe Topologien rasch und einheitlich bereitstellen und diese Bereitstellungen mühelos anpassen, sobald dies für wichtige Angebote erforderlich ist oder Varianten für besondere Szenarien oder Kunden unterstützt werden müssen.

In Vorlagen werden die Vorteile des zugrunde liegenden Azure-Ressourcen-Managers mit der Flexibilität und besseren Lesbarkeit von JavaScript Object Notation (JSON) kombiniert. Mithilfe von Vorlagen können Sie die folgenden Schritte ausführen:

- Einheitliches Bereitstellen von Topologien und ihrer Workloads
- Gemeinsames Verwalten aller Ressourcen einer Anwendung in Ressourcengruppen
- Anwenden der rollenbasierten Zugriffssteuerung, um Benutzern, Gruppen und Diensten den entsprechenden Zugriff zu gewähren
- Verwenden von Kategoriezuordnungen, um Aufgaben wie z. B. Abrechnungsübersichten zu optimieren

Dieser Artikel bietet Details zu Nutzungsszenarien sowie Architektur- und Implementierungsmustern, die während unserer Entwurfssitzungen und praktischen Vorlagenimplementierungen bei AzureCAT-Kunden (Azure Customer Advisory Team) bestimmt wurden. Diese keineswegs akademischen, bewährten Verfahren wurden beeinflusst von der Entwicklung von Vorlagen für 12 führende Linux-basierte OSS-Technologien einschließlich: Apache Kafka, Apache Spark, Cloudera, Couchbase, Hortonworks HDP, DataStax Enterprise powered by Apache Cassandra, Elasticsearch, Jenkins, MongoDB, Nagios, PostgreSQL, Redis und Nagios. Die meisten dieser Vorlagen wurden mit einem bekannten Hersteller einer bestimmten Distribution entwickelt und von den Anforderungen der Unternehmens- und SI-Kunden von Microsoft im Rahmen der jüngsten Projekte beeinflusst.

In diesem Artikel werden diese bewährten Vorgehensweisen vorgestellt, mit denen Sie erstklassige Azure-Ressourcen-Manager-Vorlagen entwickeln können.

Bei unserer Arbeit mit Kunden haben wir bei Unternehmen, Systemintegratoren (SIs) und Clouddienstanbietern verschiedene Nutzungskonzepte für Ressourcen-Manager-Vorlagen ausgemacht. In den folgenden Abschnitten finden Sie eine allgemeine Übersicht über gängige Szenarien und Muster für verschiedene Kundentypen.

## Unternehmen und Systemintegratoren

In großen Organisationen gibt es meist zwei Nutzer von ARM-Vorlagen: interne Softwareentwicklungsteams und die IT-Abteilung. Die Szenarien bei den SIs, mit denen wir zusammengearbeitet haben, sind mit denen bei Großunternehmen vergleichbar, sodass die gleichen Überlegungen zutreffen.

### Interne Softwareentwicklungsteams

Wenn Ihr Team Software zur Unterstützung Ihrer Geschäftsprozesse entwickelt, bieten Vorlagen eine einfache Möglichkeit, Technologien für den Einsatz in unternehmensspezifischen Lösungen schnell bereitzustellen. Anhand von Vorlagen können auch schnell Schulungsumgebungen erstellt werden, mit deren Hilfe Teammitgliedern erforderliche Kenntnisse vermittelt werden.

Sie können Vorlagen unverändert einsetzen, erweitern oder selbst erstellen, um Ihre Anforderungen zu erfüllen. Mithilfe einer Kategorisierung innerhalb von Vorlagen können Sie eine Abrechnungsübersicht mit verschiedenen Ansichten erstellen, z. B. nach Team, Projekt, Person und Schulung.

Unternehmen möchten, dass Entwicklungsteams eine Vorlage für eine einheitliche Bereitstellung einer Lösung erstellen und zugleich Einschränkungen definieren, sodass bestimmte Elemente fixiert bleiben und nicht überschrieben werden können. Beispielsweise kann eine Bank anfordern, dass für eine Vorlage die rollenbasierte Zugriffssteuerung gilt, damit ein Programmierer eine Bankgeschäftslösung nicht so ändern kann, dass Daten an ein privates Speicherkonto gesendet werden.

### IT-Abteilungen von Unternehmen

IT-Abteilungen von Unternehmen nutzen Vorlagen zumeist für die Bereitstellung von Cloudkapazität und in der Cloud gehostete Funktionen.

#### Cloudkapazität

Es kommt häufig vor, dass IT-Abteilungen Teams in ihrem Unternehmen Cloudkapazität in bekannten T-Shirt-Größen zur Verfügung stellen, z. B. als "S", "M" oder "L". Bei den angebotenen T-Shirt-Größen können verschiedene Ressourcentypen und -mengen kombiniert werden. Zugleich wird ein Grad an Standardisierung geboten, der die Verwendung von Vorlagen möglich macht. Die Vorlagen bieten Kapazität auf eine einheitliche Weise, die Unternehmensrichtlinien erzwingt, und arbeiten mit einer Kategorisierung, um nutzenden Abteilungen die Kostenverrechnung zu ermöglichen.

Beispielsweise müssen Sie möglicherweise Entwicklungs-, Test- oder Produktionsumgebungen einrichten, in denen die Softwareentwicklungsteams ihre Lösungen bereitstellen können. Die Umgebung weist eine vordefinierte Netzwerktopologie und Elemente auf, die die Softwareentwicklungsteams nicht ändern können, wie z. B. Regeln für den Zugriff auf das öffentliche Internet und die Paketuntersuchung. Möglicherweise gibt es auch organisationsspezifische Rollen für diese Umgebungen mit unterschiedlichen Zugriffsrechten für die Umgebung.

#### In der Cloud gehostete Funktionen

Vorlagen können zur Unterstützung in der Cloud gehosteter Funktionen eingesetzt werden, einschließlich einzelner Softwarepakete oder zusammengesetzter Angebote, die internen Geschäftsbereichen angeboten werden. Ein Beispiel einer zusammengesetzten Lösung ist Analytics-as-a-Service (Analyse, Visualisierung und anderen Technologien), die in einer optimierten, vernetzten Konfiguration in einer vordefinierten Netzwerktopologie bereitgestellt wird.

In der Cloud gehostete Funktionen unterliegen, wie zuvor beschrieben, den Sicherheits- und Rollenvorgaben, die für das Cloudkapazitätsangebot gelten, in dem sie erstellt werden. Diese Funktionen werden entweder in Standardform oder als verwalteter Dienst angeboten. Für letzteres sind Rollen mit eingeschränktem Zugriff erforderlich, um zu Verwaltungszwecken den Zugriff auf die Umgebung zuzulassen.

## Clouddienstanbieter

In vielen Gesprächen mit Clouddienstanbietern haben wir mehrere Ansätze ausgemacht, die Sie befolgen können, um Dienste für Ihre Kunden bereitzustellen und die entsprechenden Anforderungen zu erfüllen.

### Von Clouddienstanbietern gehostetes Angebot

Wenn Sie Ihr Angebot in Ihrem eigenen Azure-Abonnement hosten, sind zwei Hostingverfahren gängig: das Einrichten einer eigenen Bereitstellung für jeden Kunden oder das Bereitstellen von Skalierungseinheiten in einer gemeinsam genutzten Infrastruktur, die von allen Kunden verwendet wird.

- **Eigene Bereitstellungen für jeden Kunden.** Eigene Bereitstellungen pro Kunde erfordern feste Topologien verschiedener bekannter Konfigurationen. Diese weisen möglicherweise unterschiedliche Größen virtueller Computer (VMs), Anzahlen von Knoten und Mengen an zugeordnetem Speicher auf. Eine Kategorisierung von Bereitstellungen dient zum Erstellen der Abrechnungsübersicht für jeden Kunden. Die rollenbasierte Zugriffssteuerung kann aktiviert werden, um Kunden Zugriff auf bestimmte Bereiche ihrer Cloudumgebung zu gewähren.
- **Skalierungseinheiten in von mehreren Mandanten gemeinsam genutzten Umgebungen.** Eine Vorlage kann eine Skalierungseinheit für Umgebungen mit mehreren Mandanten darstellen. In diesem Fall wird die gleiche Infrastruktur zur Unterstützung aller Kunden verwendet. Die Bereitstellungen stellen eine Gruppe von Ressourcen dar, die dem gehosteten Angebot einen bestimmten Grad an Kapazität zur Verfügung stellen, der z. B. als Anzahl von Benutzern oder Transaktionen ausgedrückt wird. Diese Skalierungseinheiten werden je nach Bedarf vergrößert oder verkleinert.

### In Kundenabonnement einfließendes Angebot eines Clouddienstanbieters

Möglicherweise möchten Ihre Software in Abonnements bereitstellen, die im Besitz von Endkunden sind. Mithilfe von Vorlagen können Sie verschiedene Bereitstellungen im Azure-Konto eines Kunden einrichten.

Diese Bereitstellungen unterliegen der rollenbasierten Zugriffsteuerung, damit Sie die Bereitstellung im Konto des Kunden aktualisieren und verwalten können.

### Azure Marketplace

Wenn Sie Ihre Angebote über eine Onlineplattform, wie z. B. Azure Marketplace bewerben und verkaufen möchten, können Sie Vorlagen entwickeln, um unterschiedliche Arten von Bereitstellungen zu bieten, die im Azure-Konto eines Kunden ausgeführt werden. Diese unterschiedlichen Bereitstellungen werden in der Regel als T-Shirt-Größen (S, M, L), Produkt-/Zielgruppentyp (Community, Developer, Enterprise) oder Featuretyp (Basis, hohe Verfügbarkeit) beschrieben. In einigen Fällen erlauben Ihnen diese Typen das Angeben bestimmter Attribute der Bereitstellung, wie z. B. VM-Typ oder Anzahl der Datenträger.

## Open-Source-Software-Projekte (OSS)

Innerhalb von Open-Source-Projekten ermöglichen Ressourcen-Manager-Vorlagen einer Community die schnelle Bereitstellung einer Lösung anhand bewährter Methoden. Sie können Vorlagen in einem GitHub-Repository speichern, damit die Community diese mit der Zeit überarbeiten kann. Endbenutzer können dann diese Vorlagen in ihren eigenen Azure-Abonnements bereitstellen.

In den folgenden Abschnitten werden die Punkte beschrieben, die vor dem Entwurf Ihrer Lösung berücksichtigt werden müssen.

## Bestimmen, was sich außerhalb und innerhalb einer VM befindet

Wie Sie Ihre Vorlage entwerfen, ist es hilfreich, sich die Anforderungen dahingehend anzusehen, was sich außerhalb und innerhalb der virtuellen Computer (VMs) befindet:

- Außerhalb bedeutet die VMs und andere Ressourcen Ihrer Bereitstellung, wie z. B. Netzwerktopologie, Kategorisierung, Verweise auf Zertifikate/geheime Schlüssel und rollenbasierte Zugriffssteuerung. Alle diese Ressourcen sind Teil Ihrer Vorlage.
- Innerhalb bedeutet die installierte Software und der allgemein gewünschte Zustand der Konfiguration. Andere Mechanismen, wie z. B. VM-Erweiterungen oder Skripts, werden vollständig oder teilweise verwendet. Diese Mechanismen werden von der Vorlage bestimmt und ausgeführt, ohne sich in ihr zu befinden.

Zu den gängigen Beispielen von Aktivitäten, die innerhalb der VM anzusiedeln sind, zählen z. B.:

- Installieren oder Entfernen von Serverrollen und -features
- Installieren und Konfigurieren von Software auf Knoten- oder Clusterebene
- Bereitstellen von Websites auf einem Webserver
- Bereitstellen von Datenbankschemas
- Verwalten der Registrierung oder anderer Arten von Konfigurationseinstellungen
- Verwalten von Dateien und Verzeichnissen
- Starten, Beenden und Verwalten von Prozessen und Diensten
- Verwalten lokaler Gruppen und Benutzerkonten
- Installieren und Verwalten von Paketen (.msi, .exe, yum usw.)
- Verwalten von Umgebungsvariablen
- Ausführen systemeigener Skripts (Windows PowerShell, Bash usw.)

### Konfigurieren des gewünschten Zustands (Desired State Configuration, DSC)

Mit Blick auf den internen Zustand Ihrer VMs über die Bereitstellung hinaus wollen Sie sicherstellen, dass sich diese Bereitstellung nicht von der Konfiguration "entfernt", die Sie definiert und in der Quellcodeverwaltung eingecheckt haben. Dadurch wird gewährleistet, dass Ihre Entwickler oder das Betriebspersonal keine manuellen Ad-hoc-Änderungen an einer Umgebung vornehmen, die nicht überprüft, getestet oder in der Quellcodeverwaltung gespeichert wurden. Dies ist wichtig, da die manuellen Änderungen nicht nur nicht der Quellcodeverwaltung unterliegen, sondern auch nicht Teil der Standardbereitstellung sind, was sich negativ auf künftige automatisierte Bereitstellungen der Software auswirkt.

Die Konfiguration des gewünschten Zustands ist nicht nur mit Blick auf die internen Mitarbeiter, sondern auch aus Gründen der Sicherheit wichtig. Hacker versuchen regelmäßig, Softwaresysteme zu manipulieren und auszunutzen. Wenn sie Erfolg haben, werden zumeist Dateien installiert und sonstige Änderungen am Zustand eines betroffenen Systems vorgenommen. Bei Verwenden der Konfiguration des gewünschten Zustands können Sie Unterschiede zwischen dem gewünschten und dem tatsächlichen Zustand erkennen und eine bekannte Konfiguration wiederherstellen.

Es gibt Ressourcenerweiterungen für die am häufigsten verwendeten DSC-Mechanismen: PowerShell DSC, Chef und Puppet. Jede davon kann den ursprünglichen Zustand Ihrer VM bereitstellen und auch verwendet werden, um sicherzustellen, dass der gewünschte Zustand beibehalten wird.

## Allgemeine Vorlagenbereiche

Laut unserer Erfahrung gibt es in Lösungsvorlagen drei wesentliche Bereiche. Diese drei Bereiche – Kapazität, Funktionalität und Komplettlösung – werden nachstehend ausführlich beschrieben.

### Der Bereich "Kapazität"

Eine Lösung aus dem Bereich "Kapazität" bietet eine Reihe von Ressourcen in einer Standardtopologie, die so vorkonfiguriert ist, dass Vorschriften und Richtlinien eingehalten werden. Das gängigste Beispiel ist die Bereitstellung einer Standardentwicklungsumgebung in einem IT-Abteilungs- oder SI-Szenario.

### Der Bereich "Funktionalität"

Eine Lösung aus dem Bereich "Funktionalität" konzentriert sich auf das Bereitstellen und Konfigurieren einer Topologie für eine bestimmte Technologie. Gängige Szenarien schließen Technologien wie z. B. SQL Server, Cassandra, Hadoop usw. ein.

### Der Bereich "Komplettlösung"

Der Bereich "Komplettlösung" geht über eine einzelne Funktion hinaus und dient stattdessen zum Bereitstellen einer Komplettlösung, die mehrere Funktionen umfasst.

Eine Vorlage aus dem Bereich "Komplettlösung" besteht aus einer oder mehreren Vorlagen aus dem Bereich "Funktionalität" mit lösungsspezifischen Ressourcen, Logik und gewünschtem Zustand. Ein Beispiel einer Vorlage aus dem Bereich "Komplettlösung" ist eine Lösungsvorlage für eine Datenpipeline, in der lösungsspezifische Topologie und ein entsprechender Zustand mit mehreren Lösungsvorlagen aus dem Bereich "Funktionalität" kombiniert werden, wie z. B. Kafka, Storm und Hadoop.

## Auswählen von Freiform- oder bekannten Konfigurationen

Es könnte anfangs angenommen werden, dass eine Vorlage den Nutzern die größtmögliche Flexibilität bieten sollte. Doch viele Faktoren beeinflussen die Entscheidung, ob Konfigurationen in freier Form oder aber bekannte Konfigurationen verwendet werden sollten. In diesem Abschnitt werden die wichtigsten Kundenanforderungen und technische Aspekte vorgestellt, die den in diesem Dokument vorgestellten Ansatz beeinflusst haben.

### Konfigurationen in freier Form

Oberflächlich betrachtet erscheinen Konfigurationen in freier Form ideal. Sie ermöglichen Ihnen, einen VM-Typ auszuwählen und eine beliebige Anzahl von Knoten und an diese Knoten angeschlossene Datenträger anzugeben, und zwar als Parameter für eine Vorlage. Wenn Sie jedoch bei Vorlagen genauer hinsehen, mit denen mehrere virtuelle Computer mit verschiedenen Größen bereitgestellt werden, sind zusätzliche Überlegungen anzustellen, die diese Wahl in einer Vielzahl von Szenarien weniger geeignet erscheinen lassen.

Im Artikel [VM- Clouddienstgrößen für Azure](http://msdn.microsoft.com/library/azure/dn641267.aspx) auf der Azure-Website sind die verschiedenen VM-Typen und verfügbaren Größen sowie jeweils die Anzahl langlebiger Datenträger (2, 4, 8, 16 oder 32) angegeben, die angefügt werden können. Jeder angeschlossene Datenträger bietet 500 IOPS (E/A-Vorgänge pro Sekunde), und Vielfache dieser Laufwerke können über einen Multiplikator dieser Anzahl von IOPS in einem Pool zusammengefasst werden. Beispielsweise können 16 Datenträger in einem Pool zusammengefasst werden, um 8.000 IOPS bereitzustellen. Das Bilden von Pools erfolgt über eine Konfiguration im Betriebssystem unter Verwendung von Microsoft Windows-Speicherplätzen oder RAID (Redundant Array of Inexpensive Disks) unter Linux.

Eine Konfiguration in freier Form ermöglicht die Auswahl einer Reihe von VM-Instanzen, von verschiedenen VM-Typen und -Größen für diese Instanzen, einer Anzahl von Datenträgern, die basierend auf dem VM-Typ variieren kann, sowie von einem oder mehreren Skripts zum Konfigurieren des VM-Inhalts.

Es ist üblich, dass eine Bereitstellung ggf. mehrere Typen von Knoten, z. B. Master- und Datenknoten, aufweist, sodass diese Flexibilität oft für jeden Knotentyp geboten wird.

Sobald Sie mit dem Bereitstellen von Clustern einer gewissen Größe anfangen, beginnen Sie mit Vielfachen all dieser Werte zu arbeiten. Wenn Sie z. B. einen Hadoop-Cluster mit 8 Masterknoten und 200 Datenknoten bereitstellen möchten und mit Pools von 4 angefügten Datenträgern für jeden Masterknoten und 16 angefügten Datenträgern für jeden Datenknoten arbeiten, müssen Sie 208 VMs und 3.232 Datenträger verwalten.

Bei Speicherkonten werden Anforderungen über dem definierten Grenzwert von 20.000 Transaktionen pro Sekunde eingeschränkt. Deshalb müssen Sie die Speicherkontenpartitionierung erwägen und Berechnungen anstellen, um die geeignete Anzahl von Speicherkonten zur Unterstützung dieser Topologie zu bestimmen. Angesichts der Vielzahl von Kombinationen, die vom Ansatz mit freier Form unterstützt werden, sind dynamische Berechnungen erforderlich, um die entsprechende Partitionierung zu bestimmen. Der Vorlagensprache von Azure-Ressourcen-Manager bietet derzeit keine mathematische Funktionen. Deshalb müssen Sie diese Berechnungen im Code ausführen, um eine eindeutige, hartcodierte Vorlage mit den entsprechenden Details zu erstellen.

In IT-Abteilungs- und SI-Szenarien muss jemand die Vorlagen verwalten und für die Topologien Unterstützung bieten, die einer oder mehreren Organisationen bereitgestellt wurden. Dieser zusätzliche Aufwand aufgrund verschiedener Konfigurationen und Vorlagen für jeden Kunden ist alles andere als wünschenswert.

Sie können diese Vorlagen zum Bereitstellen von Umgebungen im Azure-Abonnement Ihres Kunden verwenden. Doch sowohl IT-Abteilungsteams als auch Clouddienstanbieter stellen diese meist in ihren eigenen Abonnements bereit, wobei eine Kostenverrechnungsfunktion für die Abrechnung mit den Kunden verwendet wird. In diesen Szenarien ist das Ziel die Bereitstellung von Kapazität für mehrere Kunden in einem Pool von Abonnements und eine dichte Anzahl von Bereitstellungen in den Abonnements, um das Ausufern von Abonnements, d. h. die Anzahl zu verwaltender Abonnements, zu minimieren. Bei wirklich dynamischen Bereitstellungsgrößen erfordert das Erreichen dieser Art von Dichte eine sorgfältige Planung und zusätzliche "Gerüstbauarbeiten" im Auftrag der Organisation.

Darüber hinaus können Sie Abonnements nicht über einen API-Aufruf erstellen, sondern müssen dies manuell über das Portal tun. Mit zunehmender Anzahl von Abonnements erfordert das etwaige resultierende Ausufern von Abonnements administrative Eingriffe, sodass keine Automatisierung möglich ist. Bei einem solchen Maß an Variabilität bei den Größen von Bereitstellungen müssten Sie eine Anzahl von Abonnements manuell vorab bereitstellen, um sicherzustellen, dass Abonnements verfügbar sind.

Unter Berücksichtigung aller dieser Faktoren ist eine Konfiguration in wirklich freier Form weniger attraktiv, als es auf den ersten Blick scheinen mag.

### Bekannte Konfigurationen – Der an T-Shirt-Größen angelehnte Ansatz

Statt eine Vorlage zu bieten, die eine umfassende Flexibilität und unzählige Variationen zulässt, ist es unserer Erfahrung empfehlenswert, bekannte Konfigurationen, die an T-Shirt-Größen wie S, M und L angelehnt sind, sowie eine Experimentierumgebung (auch Sandkasten genannt) zur Auswahl zu stellen. Weitere Beispiele für T-Shirt-Größen sind Produktangebote, wie z. B. Community Edition oder Enterprise Edition. In anderen Fällen gibt es möglicherweise workloadspezifische Konfigurationen einer Technologie wie z. B. MapReduce oder NoSQL.

Viele IT-Abteilungen von Unternehmen, Anbieter von Open-Source-Software und SIs stellen ihre Angebote derzeit auf diese Weise in lokalen, virtualisierten Umgebungen (Unternehmen) oder als Software-as-a-Service-Angebote (Clientdienst- und Betriebssystemanbieter) zur Verfügung.

Dieser Ansatz bietet geeignete, bekannte Konfigurationen mit unterschiedlichen Größen, die für Kunden vorkonfiguriert sind. Ohne bekannte Konfigurationen müssen Endkunden Clustergrößen selbst bestimmen, Ressourcenbeschränkungen der Plattform berücksichtigen und Berechnungen ausführen, um die resultierende Partitionierung von Speicherkonten und anderen Ressourcen (aufgrund von Clustergrößen- und Ressourcenbeschränkungen) zu bestimmen. Bekannte Konfigurationen ermöglichen Kunden das einfache Auswählen der richtigen Größe für eine bestimmte Bereitstellung. Zusätzlich zum Verbessern der Erfahrung für den Kunden ist eine kleine Anzahl bekannter Konfigurationen einfacher zu unterstützen und ermöglicht Ihnen das Erreichen einer höheren Dichte.

Ein Ansatz bekannter Konfigurationen mit Schwerpunkt auf T-Shirt-Größen kann möglicherweise auch eine variierende Anzahl von Knoten innerhalb einer Größe aufweisen. Beispielsweise kann die T-Shirt-Größe S 3 bis 10 Knoten aufweisen. Die T-Shirt-Größe wird dann für eine Unterstützung von bis zu 10 Knoten gestaltet und ermöglicht dem Nutzer eine Auswahl in freier Form bis zur angegebenen Maximalgröße.

Eine auf dem Workloadtyp basierende Größe kann hinsichtlich der Anzahl der Knoten, die bereitgestellt werden kann, eine freiere Form haben, wird aber abhängig vom Workload eine unterschiedliche Knotengröße und Konfiguration der Software auf dem Knoten aufweisen.

Auf Produktangeboten basierende Größen wie Community oder Enterprise verfügen ggf. über unterschiedliche Ressourcentypen und eine maximale Anzahl von Knoten, die bereitgestellt werden kann, was üblicherweise an Lizenzaspekte oder die Verfügbarkeit von Features in den verschiedenen Angeboten gebunden ist.

Sie können mithilfe der JSON-basierten Vorlagen auch Kunden mit besonderen Varianten gerecht werden. Beim Umgang mit Sonderfällen können Sie die entsprechende Planung und Überlegungen zu Entwicklung, Unterstützung und Kosten integrieren.

Basierend auf den Nutzungsszenarien von Kundenvorlagen, den zu Beginn dieses Dokuments bestimmten Anforderungen und unserer praktischen Erfahrung bei der Erstellung zahlreicher Vorlagen haben wir ein Muster zur Aufgliederung von Vorlagen ausgemacht.

## Lösungsvorlagen aus den Bereichen "Kapazität" und "Funktionalität"

Die Aufgliederung bietet einem modularen Ansatz für die Entwicklung von Vorlagen, der die Wiederverwendung, Erweiterbarkeit, Tests und Tools unterstützt. Dieser Abschnitt bietet Details dazu, wie ein Aufgliederungsansatz auf Vorlagen aus den Bereichen "Kapazität" und "Funktionalität" angewendet werden kann.

Bei diesem Ansatz empfängt eine Hauptvorlage Parameterwerte von einem Vorlagennutzer und erstellt anschließend, wie nachstehend gezeigt, Verknüpfungen mit mehreren nachgelagerten Typen von Vorlagen und Skripts. Parameter, statische Variablen und generierte Variablen werden zum Bereitstellen von Werten in den und von den verknüpften Vorlagen verwendet.

![Vorlagenparameter](./media/best-practices-resource-manager-design-templates/template-parameters.png)

**Parameter werden an eine Hauptvorlage und anschließend an verknüpfte Vorlagen übergeben**

Die folgenden Abschnitte konzentrieren sich auf die Typen von Vorlagen und Skripts, in die eine einzelne Vorlage aufgegliedert wird. Anschließend werden Ansätze zum Übergeben von Zustandsinformationen unter den Vorlagen untersucht. Alle Vorlagen- und Skripttypen in der Abbildung werden mit Beispielen beschrieben. Ein kontextbezogenes Beispiel finden Sie weiter unten in diesem Dokument unter "Zusammenfassung: Beispielimplementierung".

### Metadaten für die Vorlage

Die Metadatendatei für die Vorlage (metadata.json) enthält die Schlüssel-/Wert-Paare, die eine Vorlage in JSON beschreiben, die von Menschen und Softwaresystemen gelesen werden kann.

![Metadaten für die Vorlage](./media/best-practices-resource-manager-design-templates/template-metadata.png)

**Die Metadaten der Vorlage sind in der Datei "metadata.json" beschrieben**

Software-Agents können die Datei "metadata.json" abrufen und die Informationen und einen Link zur Vorlage auf einer Webseite oder in einem Verzeichnis veröffentlichen. Elemente sind u. a. *itemDisplayName*, *description*, *summary*, *githubUsername* und *dateUpdated*.

Nachstehend wird eine vollständige Beispieldatei gezeigt.

    {
        "itemDisplayName": "PostgreSQL 9.3 on Ubuntu VMs",
        "description": "This template creates a PostgreSQL streaming-replication between a master and one or more slave servers each with 2 striped data disks. The database servers are deployed into a private-only subnet with one publicly accessible jumpbox VM in a DMZ subnet with public IP.",
        "summary": "PostgreSQL stream-replication with multiple slave servers and a publicly accessible jumpbox VM",
        "githubUsername": "arsenvlad",
        "dateUpdated": "2015-04-24"
    }

### Hauptvorlage

Die Hauptvorlage (die Datei "azuredeploy.json") wird von einem Endbenutzer aufgerufen und ist die Vorlage, über die eine Reihe benutzerdefinierter Parameter präsentiert wird.

![Hauptvorlage](./media/best-practices-resource-manager-design-templates/main-template.png)

**Die Hauptvorlage empfängt Parameter von einem Benutzer**

Aufgabe dieser Vorlage ist das Empfangen von Parametern von einem Benutzer, das Verwenden dieser Informationen, um eine Gruppe komplexer Objektvariablen aufzufüllen, und das anschließende Ausführen der entsprechenden Gruppe zusammengehöriger Vorlagen mithilfe von Vorlagenlinks.

Ein Parameter, der bereitgestellt wird, ist ein bekannter Konfigurationstyp, der auch aufgrund seiner standardisierten Werte S, M und L als T-Shirt-Größenparameter bekannt ist. In der Praxis können Sie diesen Parameter auf mehrere Weisen nutzen. Weitere Informationen finden Sie weiter unten in diesem Dokument unter "Ressourcenvorlage für bekannte Konfiguration".

Einige Ressourcen werden unabhängig von der bekannten Konfiguration bereitgestellt, die von einem Benutzerparameter angegeben wird. Diese Ressourcen werden mithilfe einer einzelnen freigegebenen Ressourcenvorlage bereitgestellt und von anderen Vorlagen gemeinsam genutzt, damit die Vorlage für die freigegebene Ressource zuerst ausgeführt wird.

Einige Ressourcen werden unabhängig von der angegebenen bekannten Konfiguration optional bereitgestellt.

### Vorlage für freigegebene Ressourcen

Diese Vorlage bietet Ressourcen, die alle bekannten Konfigurationen gemeinsam haben. Sie enthält das virtuelle Netzwerk, Verfügbarkeitsgruppen und anderen Ressourcen, die unabhängig von der Vorlage der bekannten Konfiguration erforderlich sind, die bereitgestellt wird.

![Vorlagenressourcen](./media/best-practices-resource-manager-design-templates/template-resources.png)

**Vorlage für freigegebene Ressourcen**

Ressourcennamen, wie z. B. der Namen des virtuellen Netzwerks, basieren auf der Hauptvorlage. Sie können sie gemäß der Vorgabe Ihrer Organisation als Variable in der jeweiligen Vorlage angeben oder als Parameter vom Benutzer erhalten.

### Vorlage für optionale Ressourcen

Die Vorlage für optionale Ressourcen enthält Ressourcen, die basierend auf dem Wert eines Parameters oder einer Variablen programmgesteuert bereitgestellt werden.

![Optionale Ressourcen](./media/best-practices-resource-manager-design-templates/optional-resources.png)

**Vorlage für optionale Ressourcen**

Eine Vorlage für optionale Ressourcen können Sie z. B. zum Konfigurieren einer Jumpbox verwenden, die über das öffentliche Internet einen indirekten Zugriff auf eine bereitgestellte Umgebung ermöglicht. Sie müssen einen Parameter oder eine Variable verwenden, um festzustellen, ob die Jumpbox aktiviert werden soll, und die *Concat*-Funktion verwenden, um den Zielnamen für die Vorlage zu erstellen, wie z. B.*jumpbox_enabled.json*. Vorlagenlinks nutzen anschließend die resultierende Variable, um die Jumpbox zu installieren.

Sie können die Vorlage für optionale Ressourcen von mehreren Stellen aus verknüpfen:

-	Falls für jede Bereitstellung zutreffend, erstellen Sie einen parametergesteuerter Link über die Vorlage für freigegebene Ressourcen.
-	Falls für ausgewählte bekannte Konfigurationen zutreffend, z. B. ausschließliche Installation in großen Bereitstellungen, erstellen Sie einen parameter- oder variablengesteuerten Link über die Vorlage für eine bekannte Konfiguration.

Ob eine bestimmte Ressource optional ist, hängt möglicherweise nicht vom Vorlagennutzer, sondern vom Vorlagenanbieter ab. Angenommen, Sie müssen die Anforderung eines bestimmten Produkts oder eines Produkt-Add-Ons (gängig bei Clouddienstanbietern) erfüllen oder Richtlinien erzwingen (gängig bei SIs und IT-Abteilungen von Unternehmen). In diesen Fällen können Sie eine Variable verwenden, um zu ermitteln, ob die Ressource bereitgestellt werden soll.

### Ressourcenvorlage für eine bekannte Konfiguration

In der Hauptvorlage kann ein Parameter verfügbar gemacht werden, um dem Vorlagennutzer das Angeben einer gewünschter bekannten Konfiguration zu ermöglichen, die bereitgestellt werden soll. In vielen Fällen verwendet diese bekannten Konfiguration einen T-Shirt-Größenansatz mit einer Reihe fester Konfigurationsgrößen wie z. B. Sandkasten, S, M und L.

![Ressourcen für bekannte Konfiguration](./media/best-practices-resource-manager-design-templates/known-config.png)

**Ressourcenvorlage für eine bekannte Konfiguration**

Der T-Shirt-Größenansatz wird häufig verwendet, aber die Parameter können eine beliebige Gruppe bekannter Konfigurationen darstellen. Beispielsweise können Sie eine Gruppe von Umgebungen für eine Unternehmensanwendung angeben, wie z. B. Entwicklung, Test und Produkt. Oder Sie können den Ansatz für einen Clouddienst zum Darstellen von verschiedenen Skalierungseinheiten, Produktversionen oder Produktkonfigurationen wie z. B. Community, Developer oder Enterprise verwenden.

Wie bei der Vorlage für freigegebene Ressourcen werden Variablen an die Vorlage für eine bekannte Konfiguration von Folgendem übergeben:

-	Einem Endbenutzer – d. h. die an die Hauptvorlage übergebenen Parameter
-	Einer Organisation – d. h. die Variablen in der Hauptvorlage, die interne Anforderungen oder Richtlinien darstellen

### Vorlage für Memberknotenressourcen

In einer bekannten Konfiguration sind häufig ein oder mehrere Memberknotentypen enthalten. Bei Hadoop gibt es z. B. Master- und Datenknoten. Wenn Sie MongoDB installieren, gibt es Datenknoten und eine Vermittlung. Wenn Sie DataStax bereitstellen, verfügen Sie über Datenknoten sowie über eine VM mit installiertem OpsCenter.

![Memberknotenressourcen](./media/best-practices-resource-manager-design-templates/member-resources.png)

**Vorlage für Memberknotenressourcen**

Jeder Knotentyp kann verschiedene Größen von VMs, Anzahlen angefügter Datenträger, Skripts zum Installieren und Einrichten der Knoten, Portkonfigurationen für die VMs, Anzahlen von Instanzen und andere Details aufweisen. Deshalb erhält jeder Knotentyp eine eigene Vorlage für Memberknotenressourcen, die die Details für die Bereitstellung und Konfiguration einer Infrastruktur sowie Ausführungsskripts zum Bereitstellen und Konfigurieren von Software innerhalb der VM enthält.

Für VMs werden zumeist zwei Typen von Skripts verwendet: umfassend wiederverwendbare und benutzerdefinierte Skripts.

### Umfassend wiederverwendbare Skripts

Umfassend wiederverwendbare Skripts können für mehrere Typen von Vorlagen verwendet werden. Eines der besseren Beispiele dieser umfassend wiederverwendbaren Skripts richtet RAID unter Linux für das Hinzufügen von Datenträgern zu einem Pool und zum Erzielen einer größeren Anzahl von IOPS ein. Unabhängig von der in der VM installierten Software ermöglicht dieses Skript die Wiederverwendung bewährter Vorgehensweisen für gängige Szenarien.

![Wiederverwendbare Skripts](./media/best-practices-resource-manager-design-templates/reusable-scripts.png)

**Vorlagen für Memberknotenressourcen können umfassend wiederverwendbare Skripts aufrufen**

### Benutzerdefinierte Skripts

Vorlagen rufen häufig ein oder mehrere Skripts auf, die Software innerhalb von VMs installieren und konfigurieren. Ein gängiges Muster ist in großen Topologien erkennbar, bei denen mehrere Instanzen von einem oder mehreren Memberknotentypen bereitgestellt werden. Ein Installationsskript wird für alle VMs ausgelöst, das parallel ausgeführt werden kann. Dann folgt ein Setupskript, das aufgerufen wird, nachdem alle VMs (oder alle VMs eines bestimmten Memberknotentyps) bereitgestellt wurden.

![Benutzerdefinierte Skripts](./media/best-practices-resource-manager-design-templates/custom-scripts.png)

**Vorlagen für Memberknotenressourcen können Skripts für einen bestimmten Zweck aufrufen, wie z. B. die VM-Konfiguration**

## Beispiel einer Lösungsvorlage aus dem Bereich "Funktionalität" – Redis

Um zu veranschaulichen, wie eine Implementierung funktionieren kann, sehen wir uns ein praktisches Beispiel der Erstellung einer Vorlage an, die die Bereitstellung und Konfiguration von Redis mittels standardmäßiger T-Shirt-Größen vereinfacht.

Für die Bereitstellung gibt es eine Gruppe gemeinsam genutzter Ressourcen (virtuelles Netzwerk, Speicherkonto, Verfügbarkeitsgruppen) und eine optionale Ressource (Jumpbox). Es gibt mehrere bekannte Konfigurationen, die als T-Shirt-Größen (S, M, L) dargestellt werden, von denen jede jedoch einen einzelnen Knotentyp hat. Es gibt auch zwei zweckbezogene Skripts (für Installation und Konfiguration).

### Erstellen der Vorlagendateien

Sie erstellen eine Hauptvorlage mit dem Namen "azuredeploy.json".

Sie erstellen eine Vorlage für freigegebene Ressourcen mit dem Namen "shared-resources.json".

Sie erstellen eine Vorlage für optionale Ressourcen, um die Bereitstellung einer Jumpbox mit dem Namen "jumpbox_enabled.json" zu ermöglichen.

Redis verwendet nur einen einzelnen Knotentyp, weshalb Sie eine einzelne Vorlage für Memberknotenressourcen mit dem Namen "node-resources.json" erstellen.

Mithilfe von Redis möchten Sie jeden einzelnen Knoten installieren und anschließend nach der Installation aller Knoten den Cluster einrichten. Sie verfügen über Skripts für diese beiden Aufgaben: "redis-cluster-install.sh" und "redis-cluster-setup.sh".

### Verknüpfen der Vorlagen

Unter Verwendung von Vorlagenlinks werden die Hauptvorlagenlinks mit der Vorlage für freigegebene Ressourcen verknüpft, wodurch das virtuelle Netzwerk eingerichtet wird.

Logik wird der Hauptvorlage hinzugefügt, um Nutzern der Vorlage die Angabe zu ermöglichen, ob eine Jumpbox bereitgestellt werden soll. Der Wert *enabled* für den *EnableJumpbox*-Parameter gibt an, dass der Kunde eine Jumpbox bereitstellen möchte. Wenn dieser Wert angegeben ist, verkettet die Vorlage *enabled* als Suffix mit dem Basisvorlagennamen der Jumpbox-Funktion.

Die Hauptvorlage wendet den Wert des Parameters *Large* als Suffix an den Basisvorlagenamen für T-Shirt-Größen an und verwendet dann diesen Wert in einem Vorlagenlink, um einen Link mit *technology_on_os_large.json* herzustellen.

Die Topologie ähnelt dieser Abbildung.

![Redis-Vorlage](./media/best-practices-resource-manager-design-templates/redis-template.png)

**Vorlagenstruktur einer Redis-Vorlage**

### Konfigurieren des Status

Für den Knoten im Cluster gibt es zwei Schritte zum Konfigurieren des Status, die mithilfe zweckbezogener Skripts erfolgen. "redis-cluster-install.sh" dient zur Installation von Redis und "redis-cluster-setup.sh" zum Einrichten des Clusters.

### Unterstützung von Bereitstellungen unterschiedlicher Größe

In den Variablen gibt die Vorlage für die T-Shirt-Größen die Anzahl der Knoten für jeden Typ an, der für die angegebene Größe bereitgestellt werden soll (*large*). Dann stellt die Vorlage diese Anzahl von VM-Instanzen mithilfe von Ressourcenschleifen bereit und fügt Ressourcen eindeutige Namen hinzu, indem ab *copyIndex()* ein Knotenname mit einer numerischen Sequenznummer angefügt wird. Dies erfolgt für sowohl VMs in aktiven als auch nicht aktiven Zonen gemäß der Definition in der Vorlage mit der T-Shirt-Namensvorlage.

## Aufgliederung und Vorlagen aus dem Bereich "Komplettlösung"

Eine Lösungsvorlage aus dem Bereich "Komplettlösung" legt den Schwerpunkt auf die Bereitstellung einer solchen Lösung. Diese basiert in der Regel auf einer Kombination mehrerer Vorlagen aus dem Bereich "Funktionalität", wobei zusätzliche Ressourcen, Logik und Statusinformationen hinzukommen.

Wie in der folgenden Abbildung hervorgehoben wird das für Vorlagen aus dem Bereich "Funktionalität" verwendete Modell mit Vorlagen aus dem Bereich "Komplettlösung" erweitert.

Eine Vorlage für freigegebene Ressourcen und Vorlagen für optionale Ressourcen erfüllen denselben Zweck wie beim Ansatz mit Vorlagen aus den Bereichen "Kapazität" und "Funktionalität", sind jedoch auf die Komplettlösung ausgelegt.

Da Vorlagen aus dem Bereich "Komplettlösung" zumeist auch T-Shirt-Größen aufweisen, gibt die Vorlage "Ressourcen für bekannte Konfiguration" wieder, was für eine bestimmte bekannte Konfiguration der Lösung erforderlich ist.

Die Vorlage "Ressourcen für bekannte Konfiguration" wird mit einer oder mehreren Vorlagen aus dem Bereich "Funktionalität" verknüpft, die für die Komplettlösung relevant sind, und auch mit Vorlagen für Memberknotenressourcen verknüpft, die für die Komplettlösung erforderlich sind.

Da die T-Shirt-Größe der Lösung sich von der jeweiligen Vorlage aus dem Bereich "Funktionalität" unterscheiden kann, werden Variablen in der Vorlage "Ressourcen für bekannte Konfiguration" verwendet, um zum Bereitstellen der gewünschten T-Shirt-Größe die entsprechenden Werte für nachgelagerte Lösungsvorlagen aus dem Bereich "Funktionalität" zur Verfügung zu stellen.

![Komplettlösung](./media/best-practices-resource-manager-design-templates/end-to-end.png)

**Das für Lösungsvorlagen aus den Bereichen "Kapazität" oder "Funktionalität" verwendete Modell kann für Vorlagen aus dem Bereich "Komplettlösung" einfach erweitert werden**

## Vorbereiten von Vorlagen für den Marketplace

Der zuvor beschriebene Ansatz unterstützt Szenarien, bei denen Unternehmen, SIs und Clouddienstanbieter entweder die Vorlagen selbst bereitstellen oder ihren Kunden ein selbständiges Bereitstellen ermöglichen möchten.

Eine weiteres gewünschtes Szenario ist die Bereitstellung einer Vorlage über den Marketplace. Dieser Aufgliederungsansatz funktioniert bei geringfügigen Änderungen auch für den Marketplace.

Wie bereits erwähnt, können Vorlagen verwendet werden, um unterschiedliche Bereitstellungstypen im Marketplace zum Verkauf anzubieten. Unterschiedliche Bereitstellungen lassen sich als T-Shirt-Größe (Small, Medium, Large), Produkt-/Zielgruppentyp (Community, Developer, Enterprise) oder Featuretyp (Basis, hohe Verfügbarkeit) beschreiben.

Wie unten gezeigt, können vorhandene Vorlagen aus den Bereichen "Komplettlösung" oder "Funktionalität" zum Auflisten der unterschiedlichen bekannten Konfigurationen im Marketplace genutzt werden.

Die Parameter für die Hauptvorlage werden zunächst so geändert, dass der Eingangsparameter mit dem Namen "tshirtSize" entfernt wird.

Obgleich die unterschiedlichen Bereitstellungstypen der Vorlage "Ressourcen für bekannte Konfiguration" zugeordnet sind, benötigen sie auch die allgemeinen Ressourcen und Konfigurationen in der Vorlage für freigegebene Ressourcen und möglicherweise diejenigen in den Vorlagen für optionale Ressourcen.

Wenn Sie Ihre Vorlage im Marketplace veröffentlichen möchten, richten Sie einfach unterschiedliche Kopien Ihrer Hauptvorlage ein, in denen der zuvor verfügbare Eingangsparameter "tshirtSize" in eine in diese Vorlage eingebettete Variable geändert wird.

![Marketplace](./media/best-practices-resource-manager-design-templates/marketplace.png)

**Anpassen einer Lösungsvorlage für den Marketplace**

## Nächste Schritte

- Kontextbezogene Beispiele zum Umsetzen der in diesem Thema vorgestellten Entwurfsgrundsätze finden Sie unter [Kontextbezogene Beispiele bewährter Methoden für die Implementierung von Vorlagen](best-practices-resource-manager-examples.md).
- Empfehlungen für die Sicherheitseinstellungen in Azure-Ressourcen-Manager finden Sie unter [Sicherheitsaspekte für Azure-Ressourcen-Manager](best-practices-resource-manager-security.md).
- Informationen zur Freigabe des Status in Vorlagen finden Sie unter [Freigeben des Status in Azure-Ressourcen-Manager-Vorlagen](best-practices-resource-manager-state.md).

<!---HONumber=July15_HO4-->