<properties 
	pageTitle="Allgemeine Vorgänge in den Machine Learning Empfehlungen API | Azure" 
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
	ms.date="10/14/2014" 
	ms.author="jaymathe"/> 


# Allgemeine Vorgänge in den Machine Learning Empfehlungen-API

##Zweck

Dieses Dokument zeigt die Verwendung einiger API Azure-ML-Empfehlungen über eine Beispielanwendung.

Diese Anwendung ist nicht dazu vorgesehen, die vollständigen Funktionalität zu enthalten oder alle APIs zu verwenden und soll nur einige allgemeinen Anwendungen zeigen, wenn Sie den Azure ML Empfehlung-Service zum ersten Mal verwenden möchten. 

##Einführung in die Empfehlung

Empfehlungen über den Azure-ML Empfehlung- Service sind aktiviert, wenn Sie ein Empfehlungsmodell anhand der folgenden Daten erstellen:

* Ein Repository für das Element, das Sie einem Katalog empfehlen möchten
* Daten, die die Verwendung von Elementen pro Benutzer und Sitzungen anzeigen (dies kann über Datenakquisition erfolgen und ist nicht Teil der Musteranwendung).

Nachdem ein Empfehlungsmodell erstellt ist, können Sie es verwenden, um einem Benutzer basierend auf einer Reihe von Elementen ein von ihm gewähltes Element vorauszusagen (kann ein einzelnes Element sein).

Um dem oben beschriebenen Szenario zu folgen müssen Sie im Azure ML Empfehlung-Service wie folgt vorgehen:

* Erstellen eines Modells - Dies ist ein logischer Container mit den Daten ("Katalog" und "Verwendung") und dem Vorhersage-Modelle. Jeder Modellcontainer wird über eine eindeutige ID identifiziert, die zum Zeitpunkt der Erstellung zugewiesen wird. Diese ID bezeichnet man als Modell-ID und wird in den meisten APIs verwendet 
* Upload Katalog - Sobald ein Modellcontainer erstellt ist, können Sie ihm einen Katalog, zuordnen.

Hinweis: Die oben genannten Schritte ("Modellkatalog erstellen" und "Upload Katalog") werden in der Regel einmal für den Lebenszyklus des Modells ausgeführt.

* Upload-Verwendung - Um Verbraucherdaten dem Modellcontainer hinzuzufügen.
* Ein Empfehlungsmodell erstellen - Wenn Sie ausreichend Daten haben, lösen Sie Empfehlungsmodell erstellen aus. Dieser Vorgang verwendet modernste Algorithmen-Maschinen, um das Empfehlungsmodell zu erstellen. Jedem Build wird eine eindeutige ID zugeordnet und Sie müssen diese ID beibehalten, da Sie für die Funktionalität einiger APIs erforderlich ist.
* Erstellungsprozess überwachen - Ein Empfehlungsmodell ist ein asynchroner Vorgang und kann von einigen Minuten bis zu mehreren Stunden dauern, abhängig von der Datenmenge (Katalog und Gebrauch) und den Parametern. Aus diesem Grund müssen Sie den Build überwachen. Ein Empfehlungsmodell wird nur erstellt, wenn es dem Build erfolgreich zugeordnet werden kann.
* (Optional) Wählen Sie einen aktiven Empfehlungsmodell-Build. Dieser Schritt ist nur erforderlich, wenn Sie in Ihrem Modellcontainer mehr als ein Empfehlungsmodell haben. Jede Anforderung, Empfehlungen zu erhalten, ohne den aktiven Build (aktives Empfehlungsmodell) anzugeben, wird automatisch vom System zum Standard-Build umgeleitet. 

Hinweis: Eine aktive Buildkonfiguration (Empfehlungsmodell) ist einsatzbereit und ist für Produktions-Arbeitsauslastung bestimmt, im Gegensatz zu einem nichtaktiven Empfehlungsmodell, das in einer Testumgebung bleibt (manchmal als Staging bezeichnet).

* Empfehlung erhalten - Wenn Sie ein Empfehlungsmodell haben, können Sie damit Empfehlungen für einen einzelnen Artikel oder eine Liste der Artikel, die Sie auswählen, erstellen. 

In der Regel rufen Sie erste Empfehlung für einen bestimmten Zeitraum, in der Zwischenzeit können Sie Nutzungsdaten umleiten, wieder in das System der Azure-ML-Empfehlung dazu Modell angegebenen Container hinzugefügt wird. Sobald Sie genügend Daten haben, können Sie ein  neues Empfehlungsmodell auslösen, um aktuellere Daten zu verwenden. 

##Voraussetzungen

* Visual Studio 2013
* Zugriff auf das Internet 

##Azure ML Beispiel-App-Lösung

Die Lösung enthält den Quellcode, Nutzung und Katalog-Beispieldatei und Anweisungen zum Herunterladen der Nuget-Pakete für die Kompilierung erforderlich.

##Die API verwendet

Die Anwendung verwenden, nur eine kleine Teilmenge der Azure-ML Empfehlung Funktionalität über eine Teilmenge der verfügbaren-API. Die folgenden APIs werden in der Anwendung veranschaulicht:

* Modell erstellen - Den logischen Container für Daten und Empfehlungsmodelle erstellen. Ein Modell wird durch einen Namen identifiziert. Ein Benutzer kann nicht zweimal ein Modell mit demselben Namen erstellen.
* Hochladen Sie Katalogdatei - zum Hochladen von Katalogdaten
* Hochladen Sie Verwendung-Datei - zum Hochladen von Daten zur Speicherauslastung
* Build - Erstellen eines Modells Empfehlung ausgelöst
* Überwachen der Buildausführung - Überwachen des Status der Modellerstellung Empfehlung
* Wählen Sie ein Build-Modell für Empfehlungen - um anzugeben, welches Empfehlungsmodell standardmäßig für einen bestimmten Modellcontainer verwendet werden soll. Dieser Schritt ist nur dann notwendig, wenn Sie mehr als ein Empfehlungsmodell haben und Sie einen nichtaktiven Build als aktiven Build verwenden möchten.
* Erhalten Sie Empfehlungen - empfohlene Artikel mit einer bestimmten einzelnen oder Element abrufen. 

Eine vollständige Beschreibung der API-Dokumentation Microsoft Azure Marketplace. 

Hinweis: Ein Modell kann mit der Zeit mehrere Builds haben (nicht gleichzeitig). Jeder Build wurde mit dem gleichen oder aktualisierten Katalog und zusätzlichen Verwendungsdaten erstellt.

## Häufige Probleme

* Sie müssen Ihren Benutzernamen und Ihr Microsoft Azure Marketplace Hauptkonto-Schlüssel als Befehlszeile zum Ausführen des Beispiels angeben
* Die Beispiel-App fortlaufend ausgeführt fehlschlagen - den Fluss Handle Anwendungserstellung, hochladen, Monitor erstellen und Abrufen des Namens eines vordefinierten Modells Empfehlung, daher schlägt für aufeinanderfolgende Ausführung, wenn Sie nicht den Modellnamen zwischen Aufrufen ändern
* Empfehlung ist möglicherweise ohne Daten - die Beispielanwendung verwendet einen sehr kleinen Katalog und die Datei, auf der das Empfehlungsmodell die Daten sammelte ohne Bedeutung, so dass keine Elemente empfohlen werden können.

## Haftungsausschluss
Die Beispiel-App ist nicht für die Produktion bestimmt. Die Daten, die  im Katalog aufgeführt werden und die Verwendung ist sehr klein und stellt kein sinnvolles Empfehlungsmodell dar und dient nur Demonstrationszwecken. 

## Rechtliches
Dieses Dokument wird bereitgestellt "wie-es- ist". Informationen und Stellungnahmen, die in diesem Dokument enthalten sind, einschließlich URLs und andere Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. 
Einige der in diesem Dokument dargestellten Beispiele dienen nur zu Illustrationszwecken und sind frei erfunden. Keine Ähnlichkeit oder Verbindung ist beabsichtigt und ist rein zufällig. 
Dieses Dokument gibt keine Rechte an geistigem Eigentum an irgendeinem Microsoft-Produkt. Sie können dieses Dokument für interne Zwecke verwenden. 
(c) 2014 Microsoft. Alle Rechte vorbehalten. 


<!--HONumber=46--> 
