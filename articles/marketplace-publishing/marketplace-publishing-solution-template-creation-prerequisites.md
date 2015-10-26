<properties
   pageTitle="Technische Voraussetzungen für das Erstellen einer Lösungsvorlage für den Marketplace | Microsoft Azure"
   description="Grundlagen zu den Anforderungen zum Erstellen einer Lösungsvorlage für die Bereitstellung und den Verkauf im Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio; v-divte" />

# Technische Voraussetzungen für das Erstellen einer Lösungsvorlage für den Azure Marketplace
Lesen Sie die Informationen zum Prozess vor Beginn sorgfältig durch, um nachvollziehen zu können, wo und warum die einzelnen Schritte ausgeführt werden. Bereiten Sie nach Möglichkeit Ihre Unternehmensinformationen und andere Daten vor, laden Sie die erforderlichen Tools herunter, und/oder erstellen Sie technische Komponenten, bevor Sie mit der Angebotserstellung beginnen. Diese Schritte werden in diesem Artikel erläutert.

## Topologiedefinition und Plattformauswertung
Zu diesem Zeitpunkt kennen Sie möglicherweise bereits das Azure-Compute-Artefakt und überlegen, was Sie genau entwickeln möchten. Images für virtuelle Computer, Erweiterungen, ARM-Vorlagen?

Bevor Sie Ihre Anwendung Azure-Artefakten zuweisen, lesen Sie zunächst mehr zu den unterschiedlichen Topologien, die für Ihre Lösung sinnvoll sind und die Sie wahrscheinlich auch einem Kunden empfehlen würden, der Ihre Anwendung lokal bereitstellen möchte. Die meisten Unternehmen durchlaufen in der Regel etwa folgende vier Phasen: Auswertung, Machbarkeitsstudie, Präproduktion und Produktion. Normalerweise arbeiten Sie mit dem Unternehmen in diesen verschiedenen Phasen zusammen: Sie bewerten die Anforderungen und empfehlen das exakte Layout und die Skalierung für die entsprechenden Szenarien. Kein Kunde ist wie der andere, wir gehen aber gewöhnlich davon aus, dass es allgemeine Topologien und Größen gibt, die Sie basierend auf der Größe der Kundenbereitstellung empfehlen. Bevor Sie mit der Entwicklung einer Lösung beginnen, überlegen Sie, welche Layouts für Ihre Anwendung sinnvoll sind und womit die Anforderungen Ihrer Kunden ohne größere Anpassungen erfüllt werden. Wir stellen hier erneut fest, dass es immer wieder Kunden gibt, die eine ganz spezielle Lösung benötigen, wir suchen jedoch eine allgemeine Basis.

Überlegen Sie sich für jeden Bereitstellungstyp – Auswertung, Machbarkeitsstudie, Präproduktion und Produktion – das genaue Layout und die genaue Topologie für Ihre Anwendung. Worin bestehen die verschiedenen Ebenen? Welche Ressourcen sind in welchem Umfang für jede Ebene erforderlich? Welche Bausteine sind für das Erstellen dieser Ressourcen erforderlich? Eine Produktionsbereitstellung oder sogar eine Test-/Entwicklungsumgebung kann je nach Kunde und seinen Bedürfnissen in der Größe erheblich variieren. Legen Sie fest, wie eine kleine, mittelgroße, große oder sehr große Topologie für Ihre Workload aussehen würde, und durchdenken Sie gründlich, welche Anforderungen an Verfügbarkeit, Skalierbarkeit und Leistung für eine erfolgreiche Implementierung erforderlich sind.

Nachdem Sie sich mit den verschiedenen Topologielayouts und entsprechenden Größen auseinandergesetzt haben (die meisten dieser Informationen liegen Ihnen aufgrund Ihrer Erfahrung mit lokalen Bereitstellungen bei Kunden ggf. bereits vor), widmen Sie sich wieder dem Angebot von Azure.

Überprüfen Sie, ob Ihre Topologieanforderungen durch den Funktionsumfang des Azure-Ressourcen-Managers und die Compute-Funktion des Azure-Ressourcen-Managers erfüllt werden. Die meisten Kunden beginnen mit kleineren Bereitstellungen. **Es wird jedoch dringend empfohlen, die Leistungs-, Skalierungs- und Funktionsanforderungen Ihrer großen Topologie mit der Compute-Funktion des Azure-Ressourcen-Managers zu bewerten, auch wenn Sie zunächst keine große Topologie integrieren.** Dadurch soll vor allem sichergestellt werden, dass Kunden, die Ihre kleineren Lösungen implementieren, ausreichend Raum für die Skalierung auf größere Bereitstellungen haben. Einschränkungen in Bezug auf Skalierung, Leistung oder Funktionalität sollten Ihrem Ansprechpartner bei Microsoft mitgeteilt werden, damit die von Ihnen, unseren Marketplace-Partnern und Ihren Kunden benötigten Bereiche verbessert werden können.

> [AZURE.TIP]**Empfohlen:** Bewerten Sie eine Vorlage aus, und richten Sie sie auf den größten zu erwartenden Bereitstellungsumfang Ihrer Anwendung aus, um Ihren Endkunden Wachstumsmöglichkeiten zu bieten.

## Azure Compute-Bausteine
Ermitteln wir zunächst, welche Compute-Artefakte zur Verfügung stehen und wie Ihre Anwendung mit diesen Artefakten zusammenhängt.

### 1\. Verwenden vorhandener virtueller Computer
Jeder virtuelle Computer setzt sich in der Regel aus VM-Images zusammen. VM-Images umfassen das komplette Speicherprofil eines virtuellen Computers und enthalten eine Betriebssystem-VHD und null oder mehr Datenträger. Sie können ein VM-Image erstellen, um Ihre Softwareanwendung mit den Betriebssystemkomponenten sowie allen zusätzlichen erforderlichen Daten zu packen. Beispielsweise stehen heute im Azure Marketplace einige VM-Images für SQL Server zur Verfügung, die das Windows-Betriebssystems und die SQL Server-Anwendung in einem Paket zusammenfassen. Dieses Dokument beschreibt das Erstellen und Integrieren von VM-Images in den Azure Marketplace nicht im Detail. Sie sollten jedoch verstehen, worum es sich bei einem VM-Image handelt, da ein solches Image wahrscheinlich ein Baustein für Ihre Anwendungslösung ist. Informationen finden Sie in den Schritten zum [Erstellen eines VM-Images für den Marketplace](marketplace-publishing-vm-image-creation.md).

### 2\. VM-Erweiterung
Manchmal benötigen Sie jedoch vielleicht kein Image, da Sie als Anwendungsanbieter das zugrunde liegende Betriebssystem nicht besitzen und verwalten möchten. Im Azure Marketplace gibt es einige Betriebssystemunternehmen, die regelmäßig Windows-, Linux- und Unix-Betriebssysteme veröffentlichen. Hier finden Sie Images für Windows Server, Ubuntu, CoreOS und Oracle Linux sowie CentOS-basierte Images. Anstatt ein eigenes VM-Image zu veröffentlichen, indem Sie das Betriebssystem und Ihre Anwendung in einem Paket zusammenfassen, können Sie einen Erweiterungs-Handler für virtuelle Computer entwickeln oder einen vorhandenen Handler nutzen. Bei einer Erweiterung für virtuelle Computer handelt es sich um einen Softwaremechanismus, mit dem das Konfigurieren und Verwalten von virtuellen Computern vereinfacht wird. Dieser Mechanismus nutzt den Gast-Agent (ein sicherer, einfacher Prozess) eines virtuellen Azure-Computers. Beispiel: Wie der Name schon sagt, nimmt die benutzerdefinierte Skripterweiterung ein benutzerdefiniertes Skript entgegen und führt es auf dem virtuellen Computer aus. Dadurch wird jede Art von Konfiguration ermöglicht, die ein allgemeines Skript zulassen würde.

In einem herkömmlichen Imageszenario hätten Sie alle Komponenten des Images von Anfang an installiert und alle erforderlichen Konfigurationsschritte vor oder sogar erst nach dem Starten des virtuellen Computers (mit einer Startaufgabe) ausgeführt. Dieses Image kann stark angepasst werden und wäre daher nicht so einfach wiederverwendbar. In einigen Fällen ist dies wirklich sinnvoll. In anderen Fällen, insbesondere bei einfachen Anwendungen oder komplexen Konfigurationen, die nach der Bereitstellung des virtuellen Computers erfolgen müssen, kann das Erweiterungsmodell hilfreich sein. Manchmal stellen Kunden eine Kombination aus Erweiterungen und VM-Images bereit, damit beide vorinstallierten Lösungen zur Verfügung stehen, aber auch gleichzeitig komplexe Konfigurationen nach dem Start möglich sind.

Wie Sie sicher wissen, sind Images und Erweiterungen sehr nützlich für das Konfigurieren eines einzelnen virtuellen Computers. Ein virtueller Computer ist jedoch nicht ausreichend, um Kunden hohe Verfügbarkeit in der Cloud und Skalierbarkeit zu bieten und damit die Anforderungen bei zunehmendem Datenverkehr zu erfüllen. Aus diesem Grund wird für alle Produktionsworkloads empfohlen, eine hochverfügbare Lösung mit mehreren virtuellen Computern zu planen und dabei Verfügbarkeitsgruppen und Load Balancer zu verwenden.

>[AZURE.TIP]**Empfohlen:** Alle Produktionsworkloads im Marketplace sollten mehrere virtuelle Computer verwenden, die den Datenverkehr im Rahmen einer Verfügbarkeitsgruppe (mit drei Fehlerdomänen) und mit einem Lastenausgleich abwickeln.

Um diese zahlreichen von Ihrer Anwendung unterstützten und empfohlenen Bereitstellungstopologien zu unterstützen, bietet der Azure Marketplace den Azure-Ressourcen-Manager (ARM) und ARM-Vorlagen. Der Azure-Ressourcen-Manager stellt den Lebenszyklus einer Ressourcensammlung über eine deklarative, modellbasierte Vorlagensprache bereit und verwaltet ihn. Diese Vorlage ist einfach eine parametrisierte JSON-Datei, die die für Bereitstellungen zu verwendende Ressourcengruppe und ihre Beziehung angibt. Jede Ressource befindet sich in einer Ressourcengruppe. Dabei handelt es sich lediglich um einen Container für Ressourcen. Der Ressourcen-Manager bietet Endkunden darüber hinaus eine Reihe zusätzlicher nützlicher Funktionen, u. a. zentralisierte Vorgangsüberwachung, Ressourcenmarkierung über die Kundenrechnung, ressourcenbasierte Zugriffssteuerung für präzise Sicherheit und Unterstützung für drei Compute-Fehlerdomänen für höchste Verfügbarkeit. Alle Vorgänge des Ressourcen-Managers sind idempotent.

> [AZURE.IMPORTANT]**Obligatorisch:** Angesichts der Vorteile für Endkunden sollten alle Marketplace-Inhalte mithilfe von Azure-Ressourcen-Manager-Vorlagen bereitgestellt werden.

Mit ARM und ARM-Vorlagen können Sie komplexere Bereitstellungen von Azure-Ressourcen wie beispielsweise virtuelle Computer, Speicherkonten und virtuelle Netzwerke implementieren, die auf Marketplace-Inhalten wie VM-Images und -Erweiterungen aufbauen.

## Entwickeln von Bausteinen
Nachdem Sie Ihre Anwendungstopologien festgelegt und entschieden haben, welche Topologien Sie in der ersten Phase entwickeln und im Marketplace bereitstellen, müssen Sie die Bausteine virtueller Computer bestimmen und entwickeln. Wie bereits zuvor erwähnt, müssen Sie festlegen, ob Sie die Inhalte des virtuellen Computers als eigenes veröffentlichtes VM-Image packen oder ob Sie ein bereits vorhandenes VM-Image im Azure Marketplace nutzen und mit einem veröffentlichten Erweiterungs-Handler oder Ihrem eigenen Handler konfigurieren möchten. Es gibt keine einzig richtige Lösung. Die Entscheidung hängt stark davon ab, in welchem Maße Sie das zugrunde liegende Betriebssystem und die Anforderungen in Bezug auf Installation, Konfiguration und Verwaltung Ihrer Anwendung steuern möchten. Auf MSDN und in den Azure-Blogs finden Sie umfangreiche Informationen zu Images und Erweiterungen. Eine Liste der möglicherweise nützlichen Artikel finden Sie weiter unten im Abschnitt **Weitere Informationen**.

Beachten Sie, dass ein Image- oder Erweiterungs-Handler erst über den Marketplace veröffentlicht werden muss, damit er verwendet werden kann.

## Entwickeln von ARM-Vorlagen
Wenn Sie mit dem Azure-Ressourcen-Manager und mit ARM-Vorlagen noch nicht vertraut sind, lohnt es sich, die Dokumentation zum Azure-Ressourcen-Manager und zur Compute-Funktion des Azure-Ressourcen-Managers zu lesen. Bei den nachfolgend aufgeführten Hinweisen und Beispielen wird davon ausgegangen, dass Sie grundlegende Kenntnisse der ARM-Vorlagensprache sowie Kenntnisse zur Verwendung von Compute mit ARM besitzen. In diesem Abschnitt werden hauptsächlich Best Practices und Anforderungen behandelt, die Sie beim Entwickeln nützlicher Vorlagen speziell für den Azure Marketplace unterstützen. Dabei werden insbesondere die Anforderungen berücksichtigt, die erfüllt sein müssen, um den Kunden sowohl im Ibiza-Portal als auch innerhalb des Programms eine einheitliche, benutzerfreundliche und relevante Umgebung zu bieten.

Ihre Lösung wird wahrscheinlich durch eine Vorlagengruppe ausgedrückt. Diese Vorlagen sind ein logischer Teil Ihrer Topologie. Für das Aufteilen der Topologie in mehrere Dateien gibt es mehrere Gründe: einfache Entwicklung und Verwaltbarkeit, Wiederverwendbarkeit bestimmter Teilmengen, modulare Tests und Erweiterung von Vorlagenlösungen. Infolge der Zusammenarbeit mit zahlreichen Partnern schlägt das Azure-Team folgende Aufschlüsselung der Topologie vor: eine Vorlage für allgemeine Ressourcen wie etwa VNets, Speicherkonten usw. (die sogenannte Vorlage für freigegebene Ressourcen), null oder mehr Vorlagen für optionale Ressourcen und mindestens eine Vorlage für die topologiespezifischen Ressourcen (die sogenannten Ressourcenvorlagen für eine bekannte Konfiguration).

Eine vollständige Vorlagenlösung im Marketplace besteht aus folgenden Elementen:

1. **Einer Hauptvorlage (mainTemplate.json)** – Dies ist der Einstiegspunkt in die allgemeine Bereitstellung, die in der ARM-JSON-Vorlagendatei angegeben ist. Dies ist die erste Datei, die für die Vorlagenlösung ausgewertet und zum Zusammenfügen (Verknüpfen) der übrigen Vorlagen verwendet wird. Die Datei muss zudem alle gewünschten Eingaben (Parameter) des Endbenutzers enthalten.
2. **Null oder mehr verknüpfte Vorlagen** – Die übrigen Vorlagen, die über die Hauptvorlage verknüpft werden, um die vollständige Lösungstopologie darzustellen.
3. **Null oder mehr Skriptdateien ** – Alle relevanten Skriptdateien, die als Eingabe für Erweiterungen erwartet werden, die zum Konfigurieren eines virtuellen Computers verwendet werden.
4. **Einer Definitionsdatei zum Erstellen der Benutzeroberfläche (uiDefinition.json createUiDefinition.json)** – Hierbei handelt es sich um die Datei, die die Parameter in der ARM-Vorlage Elementen auf der Benutzeroberfläche zuordnet. Mit dieser Datei können Partner die Benutzeroberfläche steuern, die ihren Endkunden in Ibiza angezeigt wird. Alle vom Endbenutzer erforderlichen Eingaben sind in dieser Datei angegeben. Jeder Eintrag ermöglicht Ihnen die Steuerung aller den einzelnen Eingaben zugeordneten Benutzeroberflächenaspekte, beispielsweise Benutzeroberflächenwidgets, Standardwerte, Gültigkeitsregeln, Fehlermeldungen usw. Beachten Sie beim Entwickeln dieser Dateien für Ihre vollständige Topologie die folgenden Kriterien, um die beste Vorlage für Ihr Marketplace-Angebot sicherzustellen.

## Zusätzliche Anforderungen und Empfehlungen

### Standardisierter Mindestsatz an Benutzereingaben
Mit Parametern können Sie Werte ausdrücken, die vom Benutzer zum Zeitpunkt der Bereitstellung in der Vorlage bereitgestellt werden müssen. Benutzereingaben können von Anwendung zu Anwendung und sogar von Topologieebene zu Topologieebene variieren. Mit der Parameterliste muss ein optimales Gleichgewicht geschaffen werden: Einerseits sollen weniger erforderliche Fragen gestellt und anderseits dem Benutzer die richtigen Steuerelemente zum Steuern der Bereitstellung zur Verfügung gestellt werden. Es gibt zwar keine Anforderungen an die vollständige Liste der Benutzereingaben, es soll jedoch sichergestellt werden, dass alle Marketplace-Vorlagenlösungen eine Reihe allgemeiner Eingaben enthalten, die zahlreiche Kunden steuern möchten. Stellen Sie sicher, dass Sie Parameter für die folgenden Werte definiert haben:

1. **Speicherort** – Die Azure-Region, in der Ressourcen bereitgestellt werden
2.	**Name des virtuellen Netzwerks** – Bei Bereitstellungen, die ein neues virtuelles Netzwerk erstellen, ist dies der Name zum Erstellen der Ressource. Bei Bereitstellung, die ein vorhandenes virtuelles Netzwerk verwenden, ist das der Name des bereitzustellenden VNets.
3.	**Speicherkonto** – Bei Bereitstellungen, die ein neues Speicherkonto erstellen, ist dies der zu erstellende Name des Speicherkontos. Bei Bereitstellungen, die ein vorhandenes virtuelles Netzwerk verwenden, ist das der Name des zu verwendenden Speicherkontos.
4.	**DNS-Name** – Der Domänenname des virtuellen Computers oder des Load Balancers, mit dem ein Endbenutzer eine Verbindung herstellt.
5.	**Benutzername** – Der Benutzername für die virtuellen Computer und möglicherweise die Anwendung(en). Vom Endbenutzer muss mindestens ein Benutzername angefordert werden.
6.	**Kennwort** – Das Kennwort für die virtuellen Computer und möglicherweise die Anwendung(en). Vom Endbenutzer muss mindestens ein Kennwort für verschiedene virtuelle Computer oder Anwendungen angefordert werden.
7.	**Öffentlicher SSH-Schlüssel (nur für virtuelle Linux-Computer)** – Der SSH-Schlüssel für die virtuellen Computer. Vom Endbenutzer muss mindestens ein Schlüssel für verschiedene virtuelle Computer angefordert werden.

## Nächste Schritte
Sie haben die Voraussetzungen überprüft und die erforderlichen Aufgaben ausgeführt und können nun mit dem Erstellen des Angebots für Ihre Lösungsvorlage fortfahren. Ausführliche Informationen finden Sie unter [Anleitung zum Erstellen einer Lösungsvorlage](marketplace-publishing-solution-template-creation.md)

## Weitere Informationen
- [Erste Schritte: Veröffentlichen eines Angebots im Azure Marketplace](marketplace-publishing-getting-started.md)
- [Bewährte Methoden zum Erstellen einer Lösungsvorlage ](marketplace-publishing-solution-template-best-practices.md)

[link-acct]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=Oct15_HO3-->