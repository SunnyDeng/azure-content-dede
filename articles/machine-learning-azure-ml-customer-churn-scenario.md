<properties title="Analyzing Customer Churn using Microsoft Azure Machine Learning" pageTitle="Analysieren der Kundenabwanderung mithilfe von Microsoft Azure Machine Learning" description="Case study of developing an integrated model for analyzing and scoring customer churn" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="pamehta" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="barga" />

# Analysieren der Kundenabwanderung mithilfe von Microsoft Azure Machine Learning


Dieses Dokument präsentiert eine Referenzimplementierung eines Analyseprojekts zur Kundenabwanderung, das mithilfe von Azure Machine Learning Studio erstellt wurde, und beschreibt zugehörige generische Modelle zur ganzheitlichen Lösung des Problems der Kundenabwanderung. Außerdem wird die Genauigkeit der mit Azure Machine Learning erstellten Modelle gemessen und die Ausrichtungen für die weitere Entwicklung bewertet.  

##Das Problem der Kundenabwanderung
Firmen müssen sich sowohl auf dem Verbrauchermarkt als auch in allen Unternehmenssektoren mit der Kundenabwanderung auseinandersetzen. Gelegentlich kann es zur übermäßigen Abwanderung kommen, die dann Grundsatzentscheidungen beeinflusst. Die traditionelle Lösung besteht darin, eine starke Abwanderungsbereitschaft von Kunden vorherzusagen und sich mit deren Bedürfnissen über einen Concierge-Dienst, Marketingkampagnen oder Sonderregelungen zu befassen. Diese Ansätze können von Branche zu Branche unterschiedlich sein und auch innerhalb einer Branche (z. B. Telekommunikation) je nach bestimmter Verbrauchergruppe variieren. Die Gemeinsamkeit besteht darin, dass die Unternehmen diese besonderen Bemühungen zur Kundenbindung minimieren müssen. Eine natürliche Methode wäre somit, jeden Kunden mit einer Abwanderungswahrscheinlichkeit zu bewerten und sich mit einer bestimmten Anzahl (N) der Kunden mit den höchsten Werten zu befassen. Die Kunden mit den höchsten Bewertungen sind möglicherweise die einträglichsten Kunden. In komplexeren Szenarien wird während der Auswahl der Anwärter für Sonderregelungen eine Gewinnfunktion verwendet. Diese Überlegungen sind jedoch nur ein Teil der ganzheitlichen Strategie zur Behandlung der Abwanderung. Die Unternehmen müssen auch das Risiko (und die zugehörige Risikobereitschaft), das Interventionsniveau und die zugehörigen Kosten sowie die eingängige Kundensegmentierung berücksichtigen.  

##Branchenprognose und Ansätze
Ein durchdachter Umgang mit der Abwanderung ist ein Zeichen für eine gefestigte Branche. Das klassische Beispiel ist die Telekommunikationsbranche, in der Kunden häufig den Anbieter wechseln. Diese spontane Abwanderung ist ein wesentliches Problem. Darüber hinaus haben die Anbieter wichtige Erfahrungen zu *Abwanderungsargumenten* gesammelt, die für die Kunden die Faktoren für einen Wechsel darstellen. In der Mobilfunkbranche stellt z. B. die Auswahl an Mobiltelefonen oder Geräten ein bekanntes Argument für eine Abwanderung dar. Daher besteht eine beliebte Strategie darin, den Preis für ein Mobiltelefon für Neukunden zu subventionieren, während Bestandskunden bei einem Upgrade der volle Preis berechnet wird. Diese Strategie hat in der Vergangenheit dazu geführt, dass die Kunden von einem Anbieter zum nächsten wechseln, um einen neuen Rabatt zu erhalten. Dies hat die Anbieter wiederum dazu veranlasst, ihre Strategien zu überarbeiten. Die hohe Volatilität bei den Mobiltelefonangeboten ist ein Faktor, der Abwanderungsmodelle sehr schnell entkräftet, die auf aktuellen Mobiltelefonmodellen basieren. Zudem sind Mobiltelefone nicht nur Telekommunikationsgeräte, sondern auch Prestigeobjekte (bedenken Sie das iPhone) und diese gesellschaftlichen Einflusswerte befinden sich außerhalb des Bereichs normaler Telekommunikationsdatenbestände. Das Endergebnis hinsichtlich der Modellerstellung ist, dass Sie keinen vernünftigen Grundsatz formulieren können, indem Sie einfach bekannte Abwanderungsgründe beseitigen. Tatsächlich ist eine durchgängige Modellierungsstrategie **obligatorisch**, einschließlich der klassischen Modelle mit der Quantifizierung bestimmter Variablen (z. B. Entscheidungsbäume).

Die Unternehmen führen großangelegte Datenanalysen, insbesondere zur Abwanderungserkennung auf Basis großer Datenmengen, mit umfangreichen Datenbeständen zu ihren Kunden als effektiven Lösungsansatz für das Problem durch. Weitere Informationen zum Ansatz mit der Analyse großer Datenmengen für das Abwanderungsproblem finden Sie im Abschnitt mit den Empfehlungen zu ETL.  

##Methode zum Abbilden der Kundenabwanderung
Ein allgemeiner Problemlösungsprozess für die Kundenabwanderung ist in den Diagrammen 1 bis 3 dargestellt:  

1.	Mit einem Risikomodell können Sie berücksichtigen, wie sich Aktionen auf die Wahrscheinlichkeit und das Risiko auswirken.
2.	Mit einem Interventionsmodell können Sie berücksichtigen, wie sich das Interventionsniveau auf die Abwanderungswahrscheinlichkeit und den Langzeitwert von Kundenbeziehungen (Customer Lifetime Value, CLV) auswirken kann.
3.	Diese Analyse führt zu einer qualitativen Analyse, die in eine auf Kundensegmente ausgerichtete proaktive Marketingkampagne ausgeweitet wird, um das optimale Angebot bereitzustellen.  

![][1]

Dieser zukunftsorientierte Ansatz stellt die beste Möglichkeit für den Umgang mit der Abwanderung dar, aber dafür ist er sehr komplex: Wir müssen einen Prototyp mit mehreren Modellen entwickeln und Abhängigkeiten zwischen den Modellen nachverfolgen. Die Interaktion zwischen den Modellen kann gekapselt werden, wie im folgenden Diagramm veranschaulicht:  

![][2]
 
*Abbildung 4: Vereinheitlichter Prototyp mit mehreren Modellen*  

Interaktionen zwischen den verschiedenen Modellen sind entscheidend, wenn wir einen ganzheitlichen Ansatz für die Kundenbindung bereitstellen müssen.  Jedes Modell lässt im Laufe der Zeit zwangsläufig nach. Daher besteht die Architektur aus einer impliziten Schleife (ähnlich dem Prototyp, der vom CRISP-DM Data Mining-Standard, [***3***], festgelegt wurde).  
 
Der Gesamtzyklus von Risiko-Entscheidung-Marketingsegmentierung/-spaltung entspricht weiterhin einer verallgemeinerten Struktur, die auf viele Geschäftsprobleme angewendet werden kann. Die Abwanderungsanalyse ist einfach ein starker Vertreter dieser Gruppe von Problemen, da sie alle Merkmale eines komplexen Geschäftsproblems aufweist, das keine vereinfachte vorhersebare Lösung gestattet. Die gesellschaftlichen Aspekte des modernen Ansatzes für die Abwanderung werden im Ansatz nicht ausdrücklich hervorgehoben, aber die gesellschaftlichen Aspekte sind wie bei anderen Modellen auch im Prototyp für die Modellerstellung gekapselt.  

Eine interessante Ergänzung ist hier die Analyse großer Datenmengen. Die heutigen Telekommunikations- und Einzelhandelsunternehmen erfassen flächendeckend Daten von ihren Kunden und es ist leicht vorherzusehen, dass sich der Bedarf an Verbindungen zwischen den verschiedenen Modellen zu einem allgemeinen Trend entwickelt, wenn man aufkommende Trends wie das "Internet der Dinge" und allgegenwärtige Geräte bedenkt, die es Unternehmen gestatten, intelligente Lösungen auf verschiedenen Ebenen einzusetzen.  

 
#Implementieren des Modellierungsprototyps in Microsoft Azure ML Studio 
Wie können wir für das soeben beschriebene Problem einen integrierten Ansatz für die Modellierung und Bewertung implementieren? In diesem Abschnitt wird veranschaulicht, wie dies mithilfe von Microsoft Azure Cloud ML Studio erreicht wird.  

Der Multi-Modell-Ansatz ist beim Entwerfen eines globalen Prototyps für die Abwanderung zwingend erforderlich. Selbst der (voraussagende) Bewertungsteil des Ansatzes sollte mehrere Modelle einbeziehen.  

Das folgende Diagramm zeigt den erstellten Prototyp, der vier verschiedene Bewertungsalgorithmen in Cloud ML Studio einbezieht, um die Abwanderung vorauszudeuten. Der Grund für die Verwendung eines Multi-Modell-Ansatzes besteht nicht nur darin, einen Gesamtklassifizierer zum Erhöhen der Genauigkeit zu erstellen, sondern er dient auch zum Schutz vor einer Überanpassung sowie zur Verbesserung der vorgeschriebenen Funktionsauswahl.  

![][3]
 
*Abbildung 5: Prototyp eines Multi-Modell-Ansatzes zur Abwanderung*  

Die nächsten Abschnitte bieten weitere Details zum Bewertungsmodell des Prototyps, das wir mithilfe von Cloud ML Studio implementiert haben.  

##Datenauswahl und Vorbereitung
Die zum Erstellen der Modelle und zum Bewerten der Kunden verwendeten Daten wurden aus einer vertikalen CRM-Lösung abgerufen, wobei die Daten verschleiert wurden, um den Datenschutz der Kunden zu gewährleisten. Die Daten enthalten Informationen zu 8.000 Kunden in den USA und sie kombinieren drei Quellen: Bereitstellungsdaten (Abonnementmetadaten), Aktivitätsdaten (Verwendung des Systems) und Kundensupportdaten. Die Daten beziehen keine geschäftsbezogenen Daten der Kunden ein, so sind z. B. keine Treuemetadaten oder Bonitätsbewertungen enthalten.  

Der Einfachheit halber liegen die ETL- und Datenbereinigungsprozesse außerhalb des Anwendungsbereichs, da wir annehmen, dass die Vorbereitung der Daten bereits an anderer Stelle durchgeführt wurde.   

Die Funktionsauswahl für die Modellerstellung basiert auf der Bewertung der vorläufigen Signifikanz für eine Reihe von in den Prozess einbezogenen Einflusswerten, wobei das Random Forest-Modul verwendet wird. Für die Implementierung in Cloud ML Studio haben wir den Mittelwert, Medianwert und Bereiche für repräsentative Funktionen berechnet. Es wurden z. B. Mengen für die qualitativen Daten hinzugefügt, z. B. Mindest- und Höchstwerte für die Benutzeraktivität.    

Zudem wurden temporale Informationen für die letzten sechs Monate erfasst. Es wurden nur die vergangenen sechs Monate verwendet, da wir die Daten eines Jahres analysiert und dabei bewiesen haben, dass die Auswirkung auf die Abwanderung nach sechs Monaten erheblich abnimmt, auch wenn statistisch signifikante Trends vorhanden sind.  

Der wichtigste Punkt dabei ist, dass der gesamte Prozess, einschließlich ETL, Funktionsauswahl und Modellerstellung, mithilfe von Datenquellen in Microsoft Azure in Cloud ML Studio implementiert wurde.   

Die folgenden Diagramme veranschaulichen die verwendeten Daten:  

![][4]
 
*Abbildung 6: Auszug aus der Datenquelle (verschleiert)*  

![][5]

 
*Abbildung 7: Aus der Datenquelle extrahierte Funktionen*
 
##Im Prototyp verwendete Algorithmen

Wir haben die folgenden vier Machine Learning-Algorithmen für die Erstellung des Prototyps verwendet (keine Anpassung):  

1.	Logistische Regression (LR)
2.	Boosted Decision Tree (BT, verstärkter Entscheidungsbaum) 
3.	Averaged Perceptron (AP, gemitteltes Perzeptron) 
4.	Support Vector Machine (SVM, Stützvektormethode)  


Das folgende Diagramm veranschaulicht einen Teil der Experimententwurfsoberfläche, die die Reihenfolge anzeigt, in der die Modelle erstellt wurden:  

![][6]  

 
*Abbildung 8: Erstellen von ML-Modellen in Cloud ML Studio*  

##Bewertungsmethoden
Wir haben die vier Modelle mithilfe eines bezeichneten Trainingsdatasets bewertet.  

Zudem haben wir das Bewertungsdataset an ein vergleichbares Modell übermittelt, das mithilfe der Desktopversion von SAS Enterprise Miner 12 erstellt wurde, und die Genauigkeit des SAS-Modells sowie der vier Azure Cloud ML-Modelle gemessen.  

#Ergebnisse 
In diesem Abschnitt werden unsere Ergebnisse zur Genauigkeit der Modelle auf Basis des Bewertungsdatasets präsentiert.  

##Richtigkeit und Genauigkeit der Bewertung
Im Allgemeinen ist die Implementierung in Azure Cloud ML etwas ungenauer als SAS, d. h. 10-15 % (Area under the curve, AUC).  

Die wichtigste Metrik bei der Abwanderung ist jedoch die Rate der Fehlklassifizierung: Welcher der vom Klassifizierer vorhergesagten wichtigsten N Abwanderer ist tatsächlich **nicht** abgewandert und hat noch keine besondere Behandlung erhalten?  Das folgende Diagramm vergleicht die Fehlklassifizierungsrate für alle Modelle:  

![][7]

 
*Abbildung 9: Area under the curve (Fläche unter der Kurve) für den Passauer Prototyp * 

###Verwenden von AUC zum Vergleichen von Ergebnissen
"Area Under Curve" (AUC) ist eine Metrik, die eine globale Maßeinheit für die *Trennbarkeit* zwischen den Verteilungen von Punktzahlen für positive und negative Bestände darstellt. Sie ähnelt dem traditionellen ROC-Diagramm (Receiver Operator Characteristic), aber ein wesentlicher Unterschied ist, dass die AUC-Metrik von Ihnen keinen Schwellenwert erfordert. Stattdessen fasst sie die Ergebnisse über **alle** möglichen Optionen zusammen. Im Gegensatz dazu zeigt das traditionelle ROC-Diagramm den positiven Wert an der vertikalen und den falsch positiven Wert an der horizontalen Achse an, wobei der Schwellenwert für die Klassifizierung variiert.   

AUC wird im Allgemeinen als Maßstab für den Wert verschiedener Algorithmen (oder verschiedener Systeme) verwendet, da sie den Vergleich von Modellen anhand ihrer AUC-Werte gestattet. Dies ist ein beliebter Ansatz in der Meteorologie, Biowissenschaft sowie in vielen anderen Branchen. Somit stellt AUC ein beliebtes Werkzeug zur Bewertung der Leistung von Klassifizierern dar.  

###Vergleichen der Fehlklassifikationsraten
Wir haben die Fehlklassifikationsraten für das fragliche Dataset mithilfe der CRM-Daten von ungefähr 8.000 Kunden verglichen.  

-	Die SAS-Fehlklassifizierungsrate lag bei 10-15 %.
-	Die Fehlklassifizierungsrate von Azure ML lag für die obersten 200-300 Abwanderer bei 15-20 %.  

In der Telekommunikationsbranche ist es wichtig, sich nur mit den Kunden mit dem höchsten Abwanderungsrisiko zu befassen, indem ihnen ein Concierge-Dienst oder andere Sonderbehandlungen angeboten werden. In dieser Hinsicht erzielt die Azure Cloud ML-Implementierung Ergebnisse, die mit dem SAS-Modell vergleichbar sind.  

Ebenso ist die Richtigkeit wichtiger als die Genauigkeit, da wir am stärksten an der korrekten Klassifizierung potenzieller Abwanderer interessiert sind.  

Das folgende Diagramm aus Wikipedia veranschaulicht die Beziehung in einer einfach zu verstehenden Grafik:  

![][8]
 
*Abbildung 10: Kompromiss zwischen Richtigkeit und Genauigkeit*

###Ergebnisse für Richtigkeit und Genauigkeit für das Boosted Tree-Modell  

Das folgende Diagramm zeigt die Originalergebnisse der Bewertung mithilfe des Azure Cloud ML-Prototyps für das zwei Klassen umfassende Boosted Decision Tree-Modell, bei dem es sich um das genaueste von den vier Modellen handelt:  

![][9]

*Abbildung 11: Boosted Tree-Merkmale*

##Leistungsvergleich 
Wir haben die Geschwindigkeit, mit der Daten mithilfe des Azure Cloud ML-Modells bewertet wurden, mit einem vergleichbaren Modell verglichen, das mithilfe der Desktopversion von SAS Enterprise Miner 12.1 erstellt wurde.  

Die folgende Tabelle fasst die Leistung der Algorithmen zusammen:  

*Tabelle 1. Allgemeine Leistung (Richtigkeit) der Algorithmen * 

**LR**|	**BT**|	**AP**|	**SVM**|
--|--|--|--|
Durchschnittliches Modell|	Das beste Modell|	Unterdurchschnittlich|	Durchschnittliches Modell

Die in Azure Cloud ML gehosteten Modelle haben SAS hinsichtlich der Ausführungsgeschwindigkeit um 15-25 % übertroffen, aber die Richtigkeit war größtenteils vergleichbar.  

#Diskussion und Empfehlungen
In der Telekommunikationsbranche haben sich verschiedene Methoden zur Analyse der Abwanderung entwickelt:  

-	Ableitung der Metriken für vier grundlegende Kategorien:
	-	**Entität (z. B. Abonnement)**. Bereitstellung grundlegender Informationen zum Abonnement und/oder Kunden, das/der Gegenstand der Abwanderung ist.
	-	**Aktivität**. Abrufen aller möglichen Nutzungsinformationen, die sich auf die Entität beziehen. Beispielsweise die Anzahl der Anmeldungen usw.
	-	**Kundensupport**. Sammeln von Informationen aus Kundensupportprotokollen, um anzuzeigen, ob es für das Abonnement Probleme oder Interaktionen mit dem Kundensupport gab. 
	-	**Wettbewerbs- und Geschäftsdaten**. Abrufen aller möglichen Informationen zum Kunden (können nicht verfügbar oder schwer nachzuverfolgen sein).
-	Verwenden der Gewichtung zur Steuerung der Funktionsauswahl. Das bedeutet, dass die verstärkte/zufällige Gesamtstruktur immer einen vielversprechenden Ansatz darstellt.  

Die Verwendung der vier aufgeführten Kategorien erzeugt den Eindruck, dass ein einfacher *deterministischer* Ansatz, der auf Indizes basiert, die für geeignete Faktoren pro Kategorie erzeugt werden, ausreichen sollte, um das Abwanderungsrisiko für einen Kunden zu erkennen. Obwohl dieser Gedanke plausibel scheint, beruht er leider auf einem falschen Verständnis. Der Grund hierfür ist, dass die Abwanderung ein temporaler Effekt ist, während die zur Abwanderung führenden Faktoren normalerweise schwankende Zustände aufweisen. Das führt einen Kunden dazu zu glauben, dass es einen Unterschied macht, ob er heute oder morgen oder in sechs Monaten abwandert. Daher ist ein *Wahrscheinlichkeitsmodell* erforderlich.  
 
Diese wichtige Beobachtung wird in Unternehmen häufig übersehen, die im Allgemeinen einen BI-orientierten Ansatz für die Analyse bevorzugen (hauptsächlich, da er besser zu verkaufen ist und eine unkomplizierte Automatisierung gestattet).  

Die Aussicht der Self-Service-Analyse mithilfe von Azure Cloud ML ist jedoch, dass die vier Informationskategorien, die nach Geschäftsbereich oder Abteilung bewertet werden, für das maschinelle Lernen hinsichtlich der Abwanderung zu einer wichtigen Quelle werden.  
 
Eine weitere interessante Funktion von Azure Cloud ML ist die Möglichkeit, ein benutzerdefiniertes Modul zum Repository der vordefinierten Module hinzuzufügen, die bereits verfügbar sind. Diese Funktion bietet im Wesentlichen eine Möglichkeit, um Bibliotheken auszuwählen und Vorlagen für vertikale Märkte zu erstellen. Sie ist am Markt ein wichtiges Unterscheidungsmerkmal von Azure Cloud ML.  

Wir hoffen, dieses Thema in Zukunft behandeln zu können, insbesondere in Bezug auf die Analyse großer Datenmengen.
  
#Zusammenfassung
In diesem Dokument wird ein sinnvoller Ansatz zur Bewältigung eines allgemeinen Problems, der Kundenabwanderung, mithilfe einer allgemeinen Struktur beschrieben. Wir haben einen Prototyp zur Bewertung von Modellen betrachtet und ihn mithilfe von Microsoft Azure Cloud ML implementiert. Abschließend wird die Genauigkeit und Leistung der Prototyplösung hinsichtlich vergleichbarer Algorithmen in SAS bewertet.   

**Weitere Informationen finden Sie unter:**  

Konnte Ihnen dieses Dokument helfen? Bitte senden Sie uns Ihr Feedback. Teilen Sie uns auf einer Skala von 1 (schlecht) bis 5 (ausgezeichnet) mit, wie Sie dieses Dokument bewerten, und nennen Sie uns einen Grund für diese Bewertung. Beispiel:  

-	Erfolgt die positive Bewertung aufgrund geeigneter Beispiele, ausgezeichneter Screenshots, anschaulicher Beschreibungen oder aus anderen Gründen? 
-	Erfolgt die negative Bewertung aufgrund schlechter Beispiele, ungenauer Screenshots oder missverständlicher Beschreibungen?  

Dieses Feedback hilft uns dabei, die Qualität der von uns veröffentlichten Whitepaper zu verbessern.   

[Feedback senden](mailto:sqlfback@microsoft.com).
 
#Referenzen
[1] Predictive analytics: beyond the predictions, W. McKnight, Information Management, Juli/August 2011, S.18-20.  

[2] http://en.wikipedia.org/wiki/Accuracy_and_precision   
 
[3] http://www.the-modeling-agency.com/crisp-dm.pdf   

[4] Big Data Marketing  

[5] http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn
 
#Anhang

![][10]
 
*Abbildung 12: Momentaufnahme einer Darstellung zum Abwanderungsprototyp*
  

[1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png
