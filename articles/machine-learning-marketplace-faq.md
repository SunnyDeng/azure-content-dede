<properties title="FAQ for publishing and using Machine Learning apps in the Azure Marketplace" pageTitle="Häufig gestellte Fragen zur Veröffentlichung und Verwendung von Machine Learning-apps im Azure Marketplace | Azure" description="Häufig gestellte Fragen" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/13/2014" ms.author="jaymathe" /> 

#Häufig gestellte Fragen zur Veröffentlichung und Verwendung von Machine Learning-apps im Azure Marketplace | Azure

##Für die Nutzung von Marketplace

###Frage 1: Nach der Eingabe für den Webdienst wird die folgende Fehlermeldung angezeigt. Warum bekomme ich diesen Fehler?
Die Anforderung führte zu einem Back-End-Timeout oder Back-End-Fehler. Das Team untersucht das Problem. Wir entschuldigen uns für eventuelle Unannehmlichkeiten. (500)
Antwort: Ihr/e Eingabeparameter entspricht/entsprechen möglicherweise nicht dem erforderlichen Format für den bestimmten Webdienst. Das richtige Format für Eingabeparameter und die Einschränkungen für diesen Webdienst finden Sie im entsprechenden Dokumentationslink.

###Frage 2: Mit welchen Anmeldeinformationen sollte ich auf die Ergebnisse zugreifen und wie kann ich diese anzeigen, wenn ich den API-Link für den Webdienst, der in "Dieses Dataset durchsuchen" angezeigt wird, kopiere und in einem anderen Browserfenster einfüge?
Antwort: Sie sollten Ihr Marketplace-Konto als den Benutzernamen und den primären Kontoschlüssel als Kennwort verwenden. Den Hauptkontoschlüssel finden Sie auf der Seite "Dieses Dataset durchsuchen" unter der Beschreibung des Webdiensts (klicken Sie auf die Schaltfläche "Show"). Das Ergebnis kann im Browser angezeigt oder heruntergeladen werden, je nachdem welchen Browser Sie verwenden.

###Frage 3: Nach der Eingabe für den Webdienst in "Dieses Dataset durchsuchen" wird die folgende Fehlermeldung angezeigt. Warum bekomme ich diesen Fehler?
Unerwarteter Fehler beim Verarbeiten Ihrer Anforderung. Bitte versuchen Sie es später noch einmal.
Antwort: Eine oder mehrere Eingabeparameter Ihres Webdiensts haben möglicherweise die maximale Länge bei der Nutzung des Webdiensts auf der Marketplace-Seite "Dieses Dataset durchsuchen" überschritten. Die Dienste können möglicherweise mit einer höheren Eingabelänge mit HTTP POST-Methoden aufgerufen werden. Beispielcode wird unter den hier beschriebenen Beispieldiensten bereitgestellt: http://azure.microsoft.com/de-de/documentation/articles/machine-learning-r-csharp-web-service-examples/.

###

##Für die Veröffentlichung von Azure ML auf Marketplace

###Frage 1: Warum werden meine Logos/Bilder/Anzahl der Transaktionen nicht für meinen Webdienst aktualisiert? 
Antwort: Es erfolgt eine Zwischenspeicherung der Logos/Bilder im Veröffentlichungsportal und eine Aktualisierung des neuen Logos/Bildes im Portal kann bis zu 10 Tage dauern.

###Frage 2: Warum wird auf der "Registerkarte Details" meines Webdiensts auf dem Marketplace-Angebot ein Fehler angezeigt?
Antwort: Es gibt ein bekanntes Problem von Marketplace bei der Verbindung mit Azure ML für Dienstdetails. Das Team arbeitet an einer Lösung zur Behebung dieses Problems.

###Frage 3: Warum funktioniert der R-Beispielcode im Azure-ML-Webdienste nicht für die Nutzung der Webdienste im Marketplace?
Antwort: Die Authentifizierungssysteme unterscheiden sich bei der Verbindung mit Azure ML Webdiensten im direkten Vergleich mit der Verbindung mit diesen Webdiensten über den Marketplace. Die Dienste im Marketplace sind OData-Dienste und können mit GET- oder POST-Methoden aufgerufen werden. 

###Frage 4: Warum werden die Supportlinks meiner Webdienstangebote nicht korrekt für einige meiner Angebote aktualisiert?
Antwort: Support-Links sind global pro Herausgeber und nicht pro Angebot. 

###Frage 5: Wie veröffentliche ich einen Webdienst mit Batcheingabemodus im Marketplace?
Antwort: Der Batcheingabemodus wird zurzeit nicht von Marketplace-Webdiensten unterstützt.

###Frage 6: An wen kann ich mich wenden bei Fragen zur Datenherausgabe oder wenn ich Probleme bei der Veröffentlichung habe?
Antwort: Weitere Informationen erhalten Sie vom Marketplace-Team unter datamarketbd@microsoft.com.






