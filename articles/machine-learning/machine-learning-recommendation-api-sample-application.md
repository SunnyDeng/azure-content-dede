<properties 
	pageTitle="Allgemeine Vorgänge in der Machine Learning-Empfehlungen-API | Microsoft Azure" 
	description="Azure ML Recommendation-Beispielanwendung" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="luiscabrer"/>


# Allgemeine Vorgänge in den Machine Learning Empfehlungen-API

##Zweck

Dieses Dokument zeigt die Verwendung der Azure Machine Learning RECOMMENDATIONS-API anhand einer [Beispielanwendung](http://1drv.ms/1xeO2F3).

Diese Anwendung ist nicht dazu vorgesehen, vollständige Funktionen zu enthalten oder alle APIs zu verwenden. Sie soll nur einige allgemeine Anwendungen zeigen, wenn Sie den Azure Machine Learning-Empfehlungsdienst zum ersten Mal verwenden möchten.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

##Einführung zum Machine Learning-Empfehlungsdienst

Die Empfehlungen über den Azure Machine Learning-Empfehlungsdienst sind aktiviert, wenn Sie ein Empfehlungsmodell anhand der folgenden Daten erstellen:

* Ein Repository für die zu empfehlenden Elemente, auch als Katalog bezeichnet
* Daten, die der Nutzung der Elemente pro Benutzer oder Sitzungen entsprechen (diese können im Lauf der Zeit und nicht als Teil der Beispiel-App gesammelt werden)

Mit dem erstellten Empfehlungsmodell können Sie Elemente vorhersagen, für die sich ein Benutzer basierend auf einer Reihe der von ihm ausgewählten Elemente (oder einem einzelnen Element) möglicherweise interessiert.

Um dieses Szenario zu aktivieren, gehen Sie im Machine Learning-Empfehlungsdienst folgendermaßen vor:

* Erstellen eines Modells: Dies ist ein logischer Container mit den Daten (Katalog und Nutzung) und den Vorhersagemodellen. Jeder Modellcontainer wird über eine eindeutige ID identifiziert, die beim Erstellen zugewiesen wird. Diese ID wird als Modell-ID bezeichnet und für die meisten APIs verwendet. 
* Hochladen in den Katalog: Nachdem ein Modellcontainer erstellt wurde, kann diesem ein Katalog zugeordnet werden.

**Hinweis**: Das Erstellen eines Modells und das Hochladen des Katalogs erfolgt in der Regel einmal pro Lebenszyklus eines Modells.

* Hochladen der Nutzung: Hiermit werden dem Modellcontainer Nutzungsdaten hinzugefügt.
* Erstellen eines Empfehlungsmodells: Wenn Sie über ausreichend Daten verfügen, können Sie das Empfehlungsmodell erstellen. Für diesen Vorgang werden modernste Algorithmen für maschinelles Lernen verwendet, um das Empfehlungsmodell zu erstellen. Jedem Build ist eine eindeutige ID zugeordnet Sie müssen diese ID aufzeichnen, da sie für die Funktionen einiger APIs erforderlich ist.
* Überwachen des Erstellungsvorgangs: Das Erstellen eines Empfehlungsmodells ist ein asynchroner Vorgang, der einige Minuten oder bis zu mehrere Stunden dauern kann. Dies ist abhängig von der Datenmenge (Katalog und Nutzung) und den Buildparametern. Aus diesem Grund müssen Sie den Build überwachen. Ein Empfehlungsmodell wird nur dann erstellt, wenn der zugeordnete Build erfolgreich abgeschlossen wurde.
* (Optional:) Wählen Sie einen aktiven Empfehlungsmodell-Build aus. Dieser Schritt ist nur erforderlich, wenn Ihr Modellcontainer mehr als ein Empfehlungsmodell umfasst. Alle Empfehlungsanforderungen ohne Angabe des aktiven Empfehlungsmodells werden automatisch zum aktiven Standardbuild umgeleitet. 

**Hinweis:** Ein aktives Empfehlungsmodell ist einsatzbereit und für die Produktionslast ausgelegt. Dies ist ein Unterschied zu einem inaktiven Empfehlungsmodell, das in einer Art Testumgebung verbleibt (mitunter als "Staging" bezeichnet).

* Abrufen von Empfehlungen: Wenn Sie über ein Empfehlungsmodell verfügen, können Sie Empfehlungen für einzelne ausgewählte Elemente oder eine Elementliste erstellen. 

In der Regel rufen Sie "Get recommendation" über einen bestimmten Zeitraum auf. Während dieses Zeitraums können Sie die Nutzungsdaten an das Machine Learning-Empfehlungssystem umleiten, damit diese Daten dem angegebenen Modellcontainer hinzugefügt werden. Wenn Sie über ausreichend Nutzungsdaten verfügen, können Sie ein neues Empfehlungsmodell erstellen, das die zusätzlichen Nutzungsdaten integriert.

##Voraussetzungen

* Visual Studio 2013
* Zugriff auf das Internet 

##Beispielanwendungslösung für Azure Machine Learning

Die Lösung enthält den Quellcode, eine Beispielnutzung, eine Katalogdatei und Anweisungen zum Herunterladen der für die Kompilierung erforderlichen Nuget-Pakete.

##Die verwendeten APIs

Die Anwendung verwendet die Machine Learning-Empfehlungsfunktionen über eine Teilmenge der verfügbaren APIs. Die folgenden APIs werden in der Anwendung veranschaulicht:

* Create model: Erstellen Sie einen logischen Container für Daten und Empfehlungsmodelle. Ein Modell wird anhand eines Namens identifiziert. Sie können jeweils nur ein Modell mit demselben Namen erstellen.
* Upload catalog file: wird zum Hochladen von Katalogdaten verwendet.
* Upload usage file: wird zum Hochladen der Nutzungsdaten verwendet.
* Auslösen des Builds: wird zum Erstellen eines Empfehlungsmodells verwendet.
* Trigger build: wird zum Überwachen des Status eines Empfehlungsmodell-Builds verwendet.
* Choose a built model for recommendation: wird verwendet, um anzugeben, welches Empfehlungsmodell standardmäßig für einen bestimmten Modellcontainer verwendet werden soll. Dieser Schritt ist nur dann erforderlich, wenn Sie über mehrere Empfehlungsmodelle verfügen und einen inaktiven Build als aktives Empfehlungsmodell aktivieren möchten.
* Get recommendation: wird zum Abrufen empfohlener Elemente anhand eines bestimmten Einzelelements oder eines Elementsatzes verwendet. 

Eine vollständige Beschreibung der APIs finden Sie in der Dokumentation zum Microsoft Azure Marketplace.

**Hinweis:** Ein Modell kann im Lauf der Zeit über mehrere Builds verfügen (nicht jedoch gleichzeitig). Jeder Build wird mit demselben oder einem aktualisierten Katalog und zusätzlichen Nutzungsdaten erstellt.

## Häufige Probleme

* Sie müssen Ihren Benutzernamen und den Microsoft Azure Marketplace-Hauptkontoschlüssel angeben, um die Beispiel-App auszuführen.
* Die Beispiel-App kann nicht mehrfach hintereinander ausgeführt werden. Der Anwendungsfluss umfasst das Erstellen, das Hochladen, das Aufbauen des Bildschirms sowie das Abrufen von Empfehlungen von einem vordefinierten Modell. Daher schlägt eine fortlaufende Ausführung fehl, wenn Sie zwischen den Aufrufen nicht den Modellnamen ändern.
* Empfehlungen werden möglicherweise ohne Daten zurückgegeben. Die Beispiel-App verwendet einen sehr kleinen Katalog. Dies gilt auch für die Nutzungsdatei. Daher verfügen einige Elemente des Katalogs nicht über empfohlene Elemente.

## Haftungsausschluss
Die Beispiel-App ist nicht für die Ausführung in einer Produktionsumgebung gedacht. Der Katalog umfasst nur sehr wenige Daten, die kein sinnvolles Empfehlungsmodell ergeben. Die Daten werden als Beispiel bereitgestellt.
 

<!---HONumber=July15_HO4-->