<properties
	pageTitle="Erweitern Sie Ihr Experiment mit R | Azure"
	description="Gewusst wie: Erweitern Sie die Funktionalität von Azure Machine Learning Studio über die Programmiersprache R mithilfe des Moduls R-Skript ausführen."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="garye" />


#Erweitern Sie Ihr Experiment mit R 

Sie können die Funktionalität von ML Studio über die Programmiersprache R erweitern, indem Sie das Modul [R-Skript ausführen][execute-r-script] verwenden. 

Dieses Modul akzeptiert mehrere Eingabedatasets, und das Ergebnis ist die Ausgabe eines einzelnen Datasets. Sie können ein R-Skript für den Parameter **R-Skript** , des Moduls [R-Skript ausführen][execute-r-script] eingeben. 

Sie können auf jeden Eingangsport des Moduls mithilfe von Code etwa wie folgt zugreifen: 

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Auflisten aller aktuell installierten Pakete

Die Liste der installierten Pakete kann sich ändern. Um die vollständige Liste zu erhalten, schreiben Sie die folgenden Zeilen in das Modul [R-Skript ausführen][execute-r-script], um in die Liste an das Ausgabedataset zu senden:

    out <- data.frame(installed.packages())
    maml.mapOutputPort("out")

Führen Sie zum Anzeigen der Liste der Pakete Folgendes aus: Stellen Sie eine Verbindung zwischen einem Modul für die Konvertierung wie [In CSV konvertieren 
][convert-to-csv] und den Ausgabedaten von [R-Skript ausführen][execute-r-script] her. Führen Sie das Experiment aus, klicken Sie dann auf die Ausgabedaten des Konversionsmoduls, und wählen Sie **Herunterladen** aus.

##Importieren von Paketen

Sie können auch Pakete importieren, die noch nicht von einem gestaffelten ML Studio Repository installiert wurden. Dies funktioniert mithilfe der folgenden Befehle im Modul [R-Skript ausführen][execute-r-script] und einem gepackten Paketarchiv: 

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

Wobei  `my_favorite_package.zip` die ZIP-Datei mit dem Paket enthält.

##Liste der installierten Pakete

Zur besseren Übersicht wird die Liste der Pakete in der aktuellen Version in der folgenden Tabelle bereitgestellt.

Um die vollständige Liste der Pakete zu erhalten, die derzeit verfügbar sind, siehe Abschnitt **Auflisten aller installierten Pakete** oben. Aktuelle Dokumentation zu R finden Sie [hier](http://cran.r-project.org/manuals.html).

- [R-Module von A bis E]
- [R-Module von F bis L]
- [R-Module von M bis R]
- [R-Module von S bis Z]

[R-Module von A bis E]: #r-modules-beginning-with-a-through-e
[R-Module von F bis L]: #r-modules-beginning-with-f-through-l
[R-Module von M bis R]: #r-modules-beginning-with-m-through-r
[R-Module von S bis Z]: #r-modules-beginning-with-s-through-z


###R-Module von A bis E

| Paketname | Paketbeschreibung |
| ------------ | ------------------- |
| abc | Tools für die ungefähre Bayessche Berechnung (ABC) |
| abind | Kombinieren von mehrdimensionalen Arrays |
| actuar | Versicherungsmathematische Funktionen |
| ade4 | Analyse ökologischer Daten: Explorative und euklidische Methoden in Umweltwissenschaften |
| AdMit | Adaptive Mischung von Student-t-Verteilungen |
| aod | Analyse überverteilter Daten |
| ape | Analysen von Phylogenetik und Evolution |
| approximator | Bayessche Vorhersage für komplexe Computercodes |
| arm | Datenanalyse mit Regression und Modellen mit mehreren Ebenen/hierarchischen Modellen |
| arules | Mining-Zuordnungsregeln und häufige Itemsets |
| arulesViz | Visualisieren von Zuordnungsregeln und häufigen Itemsets |
| ash | David Scotts ash-Routinen |
| assertthat | Einfache Prä- und Post-Aussagen |
| AtelieR | Eine GTK-GUI für das Lehren grundlegender Konzepte zum statistischen Rückschluss und elementarer Bayes-Tests |
| BaBooN | Bayesian Bootstrap Predictive Mean Matching - mehrfache und einzelne Imputationen für diskrete Daten |
| BACCO | Bayessche Analyse der Ausgabe von Computercode |
| BaM | Funktionen und Datasets für Bücher, von Jeff Gill |
| bark | Bayessche sdditive Regressionskernel |
| BAS | Bayes-Modell zur Mittelwertbildung mit Bayesschem Adaptive Sampling |
| base | Das Basispaket von R |
| BayesDA | Funktionen und Datasets für das Buch Bayesian Data Analysis |
| BayesGARCH | Bayes-Schätzung des Modells GARCH(1,1) mit Student-t-Innovationen |
| bayesm | Bayes-Inferenz für Marketing und Mikroökonometrie |
| bayesmix | Bayessche Vermischungsmodelle mit JAGS |
| bayesQR | Bayessche Quantilsregression |
| bayesSurv | Bayessche Überlebensregression mit flexiblen Fehler- und zufälligen Effektverteilungen |
| Bayesthresh | Bayessche Modelle mit Schwellenwerten und gemischten Effekten für kategorische Daten |
| BayesTree | Bayes-Methoden für baumstrukturbasierte Modelle |
| BayesValidate | BayesValidate-Paket |
| BayesX | R-Hilfsprogramme für das Softwarepaket BayesX |
| BayHaz | R-Funktionen für die Bayessche Schätzung der Gefahrenrate|
| bbemkr | Bayessche Schätzung der Bandbreite für multivariate Kernelregression mit Gaußschen Fehlern |
| BCBCSF | Bayes-Klassifikation mit ausgewählten Mermalen und Bias-Korrektur |
| BCE | Bayessche Kompositionsschätzung: Schätzen der (taxonomischen )Beispielzusammensetzung von Biomarkern |
| bclust | Bayes-Clustering in der Sammlung mit hierarchischem Spike-and-Slab-Modell, für das Clustering von hochdimensionalen Daten geeignet |
| bcp | Ein Paket für die Durchführung einer Bayes-Analyse der Probleme im Zusammenhang mit Änderungspunkten |
| BenfordTests | Statistische Tests für die Evaluierung der Übereinstimmung mit dem Benford'schen Gesetz |
| bfp | Bayessche gebrochene Polynome |
| BH | Boost C++-Headerdateien |
| bisoreg | Bayessche isotonische Regression mit Bernstein-Polynomen |
| bit | Eine Klasse für Vektoren von 1-Bit-booleschen-Werten |
| bitops | Bitweise Operationen |
| BLR | Bayessche lineare Regression |
| BMA | Bayes-Modellmittelwerterstellung |
| Bmix | Bayes-Sampling für Stick-Breaking-Mischungen |
| BMS | Bibliothek für Bayes-Modellmittelwerterstellung |
| bnlearn | Bayes-Netzwerkstrukturlernen, -Parameterlernen und Inferenz |
| boa | Bayes-Ausgabeanalyseprogramm (BOA) für MCMC |
| Bolstad | Bolstad-Funktionen |
| boot | Bootstrap-Funktionen (ursprünglich von Angelo Canty für S) |
| bootstrap | Funktionen für das Buch eine Einführung in die Bootstrap |
| bqtl | Bayessche QTL-Zuordnung (Toolkit) |
| BradleyTerry2 | Bradley-Terry-Modelle |
| brew | Vorlagenframework für die Berichterstellung |
| brglm | Biasreduktion in generalisierten linearen Modellen mit binomialen Antworten |
| bspec | Bayessche Spektralinferenz |
| bspmma | bspmma: Semiparametrische Bayes-Modelle für die Meta-Analyse |
| BVS | Bayes-Variantenauswahl: Bayes-Modellunsicherheitstechniken für genetische Zuordnungsstudien |
| CairoDevice | Cairo-basierter, plattformübergreifender Grafikgerätetreiber mit Antialiasing |
| calibrator | Bayes-Kalibrierung komplexer Computercodes |
| car | Begleitung zu angewandter Regression |
| caret | Klassifizierungs- und Regressionstraining |
| catnet | Kategorische Bayes-Netzwerkinterferenz |
| caTools | Tools: Moving Window Wtatistics, GIF, Base64, ROC AUC usw. |
| chron | Chronologische Objekte, die Datumsangaben und Uhrzeiten verarbeiten können |
| class | Funktionen für die Klassifizierung |
| cluster | Cluster Analysis Extended Rousseeuw et al.  |
| clusterSim | Suche nach optimalen Clusteringverfahren für ein Dataset |
| coda | Ausgabe der Analyse und Diagnose für MCMC |
| codetools | Codeanalysetools für R |
| coin | Bedingte Interferenzverfahren in einer Variation des Testframeworks |
| colorspace | Farbraummanipulation |
| combinat | kombinatorische Dienstprogramme |
| Compiler | R-Compilerpaket |
| corpcor | Effiziente Schätzung von Kovarianz und (teilweiser) Korrelation |
| cslogistic | Bedingt festgelegte logistische Regression |
| ctv | CRA-Aufgabenansichten |
| cubature | Adaptive multivariate Integration über Hypercubes |
| data.table | Erweiterung von data.frame |
| datasets | Das R-Datasetspaket |
| date | Funktionen zum Verarbeiten von Datumsangaben |
| dclone | Klonen von Daten und MCMC-Tools für maximale Wahrscheinlichkeitsmethoden |
| deal | Bayes-Lernnetzwerke mit gemischten Variablen |
| Deducer | Deducer: Eine Datenanalyse-GUI für R |
| DeducerExtras | Zusätzliche Dialogfelder und Funktionen für Deducer |
| deldir | Delaunay Triangulation und Dirichlet (Voronoi) Tesselierung  |
| DEoptimR | Differenzielle Evolutionsoptimierung in reinem R |
| deSolve | Allgemeine Löser für Anfangswertproblemen mit gewöhnlichen Differentialgleichungen,  partiellen Differentialgleichungen, differential-algebraischen Gleichungen, und verzögerten Differentialgleichungen
| devtools | Tools für die einfachere Entwicklung von R-Code |
| dichromat | Farbschemata für Dichromaten |
| digest | Erstellen kryptografischer Hash-Digests von R-Objekten |
| distrom | Verteilte multinomiale Regression |
| dlm | Bayes- und Wahrscheinlichkeitsanalyse dynamischer linearer Modelle |
| doSNOW | Parallele Foreach-Adapter für das snow-Paket |
| dplyr | dplyr: Eine Grammatik der Datenbearbeitung |
| DPpackage | Bayessche nichtparametrische Modellierung in R |
| dse | Schätzung dynamischer Systeme (Zeitreihenpaket) |
| e1071 | Sonstige Funktionen der Abteilung des Statistischen Amts (e1071), TU Wien |
| EbayesThresh | Empirische Bayes-Schwellenwertbestimmung und die zugehörigen Methoden |
| ebdbNet | Empirische Bayes-Schätzung von dynamischen Bayes-Netzwerken |
| effects | Auswirkungensanzeigen für lineare, generalisierte Multinomial-Logit-, Proportional-Odds Logit-Modelle und gemischte Effektmodelle |
| emulator | Bayes-Emulation von Computerprogrammen |
| ensembleBMA | Probabilistische Prognose mit Ensembles und Bayes-Modellmittelwerterstellung |
| entropy | Abschätzung von Entropie, gegenseitigen Information und verwandten Mengen |
| EvalEst | Schätzung dynamischer Systeme - Erweiterungen |
| evaluate | Analyse- und Auswertungstools, die mehr Details als den Standardwert bereitstellen |
| evdbayes | Bayes-Analyse in der Extremwerttheorie |
| evora | Sonderfälle bei epigenetischen Variablen für die Risikovorhersageanalyse |
| exactLoglinTest | Monte Carlo, genaue Tests für Log-lineare Modelle |
| expm | Matrixexponential |
| extremevalues | Univariate Sonderfallerkennung|


###R-Module von F bis L

| Paketname | Paketbeschreibung |
| ------------ | ------------------- |
| factorQR | Faktormodelle für Bayessche Quantilsregression |
| faoutlier | Erkennungsmethoden für einflussreiche Fälle für Faktoranalyse und SEM |
| fitdistrplus | Hilfe zum Anpassen einer parametrischen Verteilung auf nicht zensierte oder zensierte Daten |
| FME | Eine flexible Modellierungsumgebung für Modellen inverse Modellierung, Vertraulichkeit Identifizierbarkeit, Monte Carlo-Analyse |
| Foreach | Foreach-Schleifenkonstrukt für R |
| forecast | Vorhersagefunktionen für Zeitreihen und lineare Modelle |
| foreign | Lesen von Daten, gespeichert von Minitab, S SAS, SPSS, Stata, Systat, Weka, dBase... |
| FormatR | R-Code automatisch formatieren |
| Formula | Erweiterte Modellformeln |
| fracdiff | Stückweise differenzierte ARIMA-Modelle, genannt ARFIMA(p,d,q)-Modelle |
| gam | Generalisierte additive Modelle |
| gamlr | Gamma-Lasso-Regression |
| gbm | Generalisierte Boosted-Regressionsmodelle |
| gclus | Clusteringgrafiken |
| gdata | Verschiedene R-Programmiertools für die Datenbearbeitung |
| gee | Löser für allgemeine Schätzunggleichungen |
| genetics | Populationsgenetik |
| geoR | Analyse von geostatistischen Daten |
| geoRglm | geoRglm - ein Paket für allgemeine lineare räumliche Modelle |
| geosphere | Sphärische Trigonometrie |
| ggmcmc | Grafische Tools zum Analysieren von Markov-Ketten-Monte Carlo-Simulationen von Bayes-Inferenz |
| ggplot2 | Eine Implementierung der Grammatik der Grafik |
| glmmBUGS | Allgemeine lineare gemischte Modelle und räumliche Modelle mit WinBUGS, BRugs oder OpenBUGS |
| glmnet | Lasso- und flexibles Netz- Modelle, regulaisiert, generalisiert, linear |
| gmodels | Verschiedene R-Programmiertools für Modellanpassung |
| gmp | Mehrfache Präzisionsarithmetik |
| gnm | Nicht lineare Modelle, generalisiert |
| googlePublicData | Eine R-Bibliothek zum Erstellen von Google Public Data Explorer DSPL-Metadatendateien |
| googleVis | Die Schnittstelle zwischen R und Google-Diagrammen |
| GPArotation | GPA-Faktorrotation |
| gplots | Verschiedene R-Programmiertools zum Darstellen von Daten |
| graphics | Das R-Grafikpaket |
| grDevices | Die R-Grafikgeräte und Unterstützung für Farben und Schriftarten |
| gregmisc | Sonstige Funktionen von Greg |
| grid | Das Grid-Grafikpaket |
| gridExtra | Funktionen in Grid-Grafiken |
| growcurves | Bayes-Semi- und nichtparametrische Wachstumskurvenmodelle, die zusätzlich mehrere zufällige Membership-Effekte enthalten |
| grpreg | Regulierungspfade für Regressionsmodelle mit gruppierten Kovariaten |
| gsubfn | Dienstprogramme für Zeichenfolgen und Funktionsargumente |
| gtable | Grobs in Tabellen anordnen |
| gtools | Verschiedene R-Programmiertools |
| gWidgets | gWidgets-API zum Erstellen von Toolkit-unabhängigen, interaktiven Benutzeroberflächen |
| gWidgetsRGtk2 | Toolkit-Implementierung von gWidgets für RGtk2 |
| haplo.stats | Statistische Analyse der Haplotypen mit Merkmalen und Kovariaten bei mehrdeutiger Bindungsphase |
| hbsae | Hierarchische Bayes-Schätzung im kleinen Bereich |
| hdrcde | Höchste-Dichte-Regionen und bedingte Dichte-Schätzung |
| heavy | Paket für Ausreißereinbeziehung mit einseitigen Distributionen |
| hflights | Flüge, die Houston 2011 verlassen haben|
| HH | Statistische Analyse und Datenanzeige: Heiberger und Holland |
| HI | Simulation von Verteilungen, von geschachtelten Hyperplanes unterstützt |
| highr | Syntaxhervorhebung für R |
| Hmisc | Sonstige, Harrell |
| htmltools | Tools für HTML |
| httpuv | Bibliothek für HTTP und WebSocket-Server |
| httr | Tools für die Arbeit mit URLs und HTTP |
| IBrokers | R-API zur Interactive Brokers Trader Workstation |
| igraph | Netzwerkanalyse und Visualisierung |
| intervals | Tools zum Arbeiten mit Punkten und Intervallen |
| iplots | iPlots - interaktive Grafiken für R |
| ipred | Verbesserte Prädiktoren |
| irr | Verschiedene Koeffizienten von Interrater-Reliabilität und Vereinbarung |
| iterators | Iterator-Konstrukt für R |
| JavaGD | Java-Grafikgerät |
| JGR | JGR - Java-GUI für R |
| kernlab | Kernel-basiertes Machine Learning-Lab|
| KernSmooth | Funktionen für die Kernel-Glättung für Wand and Jones(1995) |
| KFKSDS | Kalman-Filter, Glätter und Störungsgglätter |
| kinship2 | Pedigree-Funktionen |
| kknn | Gewichtete k-nächste Nachbarn |
| klaR | Klassifizierung und Visualisierung |
| knitr | Ein allgemeines Paket für die dynamische Berichterstellung in R |
| ks | Kernel-Glättung |
| labeling | Beschriften von Achsen |
| Lahman | Sean Lahmans Baseball-Datenbank |
| lars | Geringste Winkelregression, Lasso und Forward Stagewise |
| lattice | Lattice Graphics |
| latticeExtra | Zusätzliche grafische Dienstprogramme auf Grundlage von Lattice |
| lava | Lineare latente Variablenmodelle |
| lavaan | Latente Variablenanalysen |
| leaps | Regressions-Teilmengenauswahl |
| LearnBayes | Funktionen zum Erlernen von Bayes-Inferenz |
| limSolve | Lösen von linearen inversen Modellen |
| lme4 | Lineare gemischten Effektmodelle mit Eigen und S4 |
| lmm | Lineare gemischte Modelle |
| lmPerm | Zeichenfolgepermutations-Tests für lineare Modelle |
| lmtest | Testen von linearen Regressionsmodellen |
| locfit | Lokale Regression, Wahrscheinlichkeits- und Dichteschätzung |
| lpSolve | Schnittstelle zum Lp_solve v. 5.5 zum lösen linearer/ganzzahliger Programme |


###R-Module von M bis R

| Paketname | Paketbeschreibung |
| ------------ | ------------------- |
| magic | Erstellen Und untersuchen von Magic Squares |
| magrittr | magrittr - ein Forward-Pipe-Operator für R |
| mapdata | Zusätzliche Mapping-Datenbanken |
| mapproj | Mapping-Projektionen |
| maps | Zeichnen von geografischen Karten |
| maptools | Tools zum Lesen und Verarbeiten räumlicher Objekte |
| maptree | Zuordnung, Kürzen und Darstellen von Baumstrukturmodellen |
| markdown | Markdown-Rendering für R |
| MASS | Unterstützung: Funktionen und Datasets für Venables und der Ripleys MASS |
| MasterBayes | ML- und MCMC-Methoden für Pedigree-Rekonstruktion und -Analyse |
| Matrix | Klassen und Methoden für Matrizen mit hoher und geringer Dichte |
| matrixcalc | Auflistung von Funktionen für Matrixberechnungen |
| MatrixModels | MModellierung von Matrizen mit hoher und geringer Dichte |
| maxent | Multinomiale logistische Regression mit geringem Arbeitsspeicher und Unterstützung für die Klassifizierung von Texten |
| maxLik | Maximale Wahrscheinlichkeitsschätzung |
| mcmc | Markov-Kette Monte Carlo |
| MCMCglmm | MCMC: allgemeine lineare gemischte Modelle |
| MCMCpack | Markov-Kette Monte Carlo (MCMC), Paket |
| memoise | Memoise-Funktionen |
| methods | Formale Methoden und Klassen |
| mgcv | Gemischtes GAM Computation Vehicle mit GCV/AIC/REML Glättunsschätzung |
| mice | Multivariate Imputation bei verketteten Gleichungen |
| microbenchmark | Unter Mikrosekunden genaue Timingfunktionen |
| mime | MIME-Typen Dateinamen zuordnen |
| minpack.lm | R-Schnittstelle für den Algorithmus der nichtlinearen kleinsten Quadrate in MINPACK (Levenberg-Marquardt) und Unterstützung für Grenzen |
| minqa | Ableitungsfreie Optimierungsalgorithmen durch quadratische Angleichung |
| misc3d | Sonstige 3D-Plots |
| miscF | Sonstige Funktionen |
| miscTools | Verschiedene Tools und Dienstprogramme |
| mixtools | Tools zum Analysieren von begrenzten Mischmodellen |
| mlbench | Machine Learning Benchmark-Probleme |
| mlogitBMA | Bayes-Modell für Polynomiale Logit-Modell-Mittelwerterstellung |
| mnormt | Der multivariate Normalwert und t-Verteilungen |
| MNP | R-Paket zum Anpassen für das Polynomiale Probit-Modell |
| modeltools | Tools und Klassen für statistische Modelle |
| mombf | Zeitpunkt- und Inverse Moment-Bayes-Faktoren |
| monomvn | Schätzung für normale multivariate und Student-t-Daten mit monotonen Fehlmustern |
| monreg | Nichtparametrische monotone Regression |
| mosaic | Projekt MOSAIC (mosaic-web.org): Statistiken und Dienstprogramme für die Mathematiklehre |
| MSBVAR | Markov-Switching, Bayes, Vektorgrafiken Autoregressionsmodelle |
| msm | Markov-Modelle mit mehreren Zuständen und ausgeblendete Markov-Modelle in fortlaufende Zeit |
| multcomp | Gleichzeitige Inferenz in allgemeinen parametrischen Modellen |
| multicool | Permutationen vonMultisets in cool-lex-Reihenfolge. |
| munsell | Munsell-Farbsystem |
| mvoutlier | Multivariate Ausreißererkennung auf Basis zuverlässiger Methoden |
| mvtnorm | Multivariate Normal- und t-Verteilungen |
| ncvreg | Regulierungspfade für Regressionsmodelle mit SCAD- und MCP-Abzug |
| nlme | Gemischte Effektmodelle, linear und nicht linear |
| NLP | Verarbeitungsinfrastruktur für Natürliche Sprache |
| nnet | Feed-Forward für neuronale Netzwerke und multinomiale Protokoll Log-Linear-Modelle |
| numbers | Zahlentheoretische Funktionen |
| numDeriv | Genaue numerische Ableitungen |
| openNLP | Apache OpenNLP-Tool-Schnittstelle |
| openNLPdata | Apache OpenNLP Jars- und Basic English-Sprachmodelle |
| OutlierDC | Ausreßererkennung mit Quantilsregression für zensiertn Daten |
| OutlierDM | Ausreißererkennung für replizierte Daten mit hohem Durchsatz |
| outliers | Tests auf Ausreißer |
| pacbpred | PAC-Bayes-Schätzung und -Vorhersage für Sparse Additive-Modelle |
| parallel | Unterstützung für parallele Berechnung in R |
| partitions | Additive Partitionen von Ganzzahlen |
| party | Ein Labor für rekursive Partitionierung |
| PAWL | Implementierung des Algorithmus PAWL |
| pbivnorm | Bivariates Normal-CDF, vektorisiert |
| pcaPP | Stabile PCA durch Projektion|
| permute | Funktionen zum Generieren von eingeschränkten Permutationen von Daten |
| pls | Partielle Methode der kleinsten Quadrate und Prinzipalkomponentenregression |
| plyr | Tools zum Aufteilen, Anwenden und Kombinieren von Daten |
| png | Lesen und Schreiben von PNG-Bildern |
| polynom | Eine Auflistung von Funktionen zum Implementieren einer Klasse für univariate polynomische Manipulationen |
| PottsUtils | Hilfsfunktionen der Potts-Modelle |
| predmixcor | Klassifizierungsregel basierend auf Bayesschen Mischmodellen mit biaskorrigierter Merkmalsauswahl |
| PresenceAbsence | Auswerten des Vorhandensein/Abwesenheit-Modells |
| prodlim | Abschätzung des Produktlimits; Kaplan-Meier und Aalen-Johansson-Methode für die zensierten Ereigniverlaufsanalyse (Survival) |
| profdpm | Profil der Dirichlet-Prozessmischungen |
| profileModel | Tools zur Profilerstellung von Inferenzfunktionen für verschiedene Modellklassen |
| proto | Prototyp objektbasierter Programmierung |
| pscl | Political Science Computational Laboratory, Stanford University |
| psych | Verfahren zur psychologische, psychometrischen und die Persönlichkeitsforschung |
| quadprog | Funktionen zum Lösen quadratischr Programmierprobleme |
| quantreg | Quantilsregression |
| qvcalc | Quasi-Abweichungen für Faktoreffekte statistischer Modelle |
| R.matlab | Lesen und Schreiben von MAT-Dateien zusammen mit R-zu-MATLAB-Konnektivität |
| R.methodsS3 | Utility-Funktion zum Definieren von S3-Methoden |
| R.oo | objektorientierte R-Programmierung mit oder ohne Verweise |
| R.utils | Verschiedene Dienstprogramme für Programmiersprachen |
| R2HTML | HTML-Export für R-Objekte |
| R2jags | Ein Paket für die Ausführung von Jags aus R |
| R2OpenBUGS | Ausführen von OpenBUGS aus R |
| R2WinBUGS | Ausführen von WinBUGS und OpenBUGS aus R / S PLUS |
| ramps | Baessches geostatisches Modellieren mit RAMPS |
| RandomFields | Simulation und Analyse von zufälligen Feldern |
| randomForest | Breiman und Cutler: Zufällige Gesamtstrukturen für Klassifizierung und Regression |
| RArcInfo | Funktionen zum Importieren von Daten aus binären Coverages von Arc/Info V7.x |
| raster | Raster: Analyse der geografischen Daten und Modellierung |
| Rrbugs bugs | Zusammenführen von R und OpenBugs und mehr |
| RColorBrewer | ColorBrewer-Paletten |
| Rcpp | Nahtlose R- und C++-Integration |
| RcppArmadillo | Auf Vorlagen basierende lineare Algebra-Bibliothek für RCPP-Integration für Armadillo |
| rcppbugs | R-Bindung für cppbugs |
| RcppEigen | RCPP-Integration für die Eigen-vorlagenbasierten lineare Algebra-Bibliothek |
| RcppExamples | Beispiele zur Verwendung von R und C++-Schnittstellen mit Rcpp |
| RCurl | Allgemeine Netzwerk-Clientschnittstelle (HTTP/FTP /...) für R |
| relimp | Der relative Anteil der Effekte in einem Regressionsmodell |
| reshape | Flexibl Daten umformen |
| reshape2 | Flexible Daten umformen: eine Neuauflage des Pakets zum Umformen |
| rgdal | Bindungen für die Geodatenabstraktionabibliothek |
| rgeos | Schnittstelle zu Geometrieengine - Open-Source-(GEOS) |
| rgl | Dreidimensionales Visualisierungsgerätesystem (OpenGL) |
| RGraphics | Daten und Funktionen aus dem Buch R rgdal , zweite Ausgabe |
| RGtk2 | R-Bindungen für Gtk 2.8.0 und höher |
| RJaCGH | Umkehrbare Jump MCMC  für die Analyse von CGH Arrays |
| rjags | Grafische Bayes-Modelle mit MCMC |
| rJava | Java-Schnittstelle auf niedriger R-Ebene |
| RJSONIO | R-Objekte in JavaScript Object Notation (JSON) serialisieren |
| robCompositions | Stabile Schätzung für zusammengesetzte Daten |
| robustbase | Grundlegende stabile Statistiken |
| RODBC | ODBC-Datenbankzugriff |
| rootSolve | Nichtlineare Stammsuche, Gleichgewichts- und stationäre Analyse normaler Differentialgleichungen |
| roxygen | Literate Programming in R |
| roxygen2 | In-source-Dokumentation für R |
| rpart | Rekursive Partitionierung und Regressionsstrukturen |
| rrcov | Skalierbare, stabile Schätzungen mit hoher Aufschlüsselung |
| rscproxy | Statconn: bietet portable C-Stil-Schnittstellen zu R (StatConnector) |
| RSGHB | Funktionen für hierarchische Bayes-Schätzung: Einen flexibler Ansatz |
| RSNNS | Neuronale Netzwerke in R mit dem Stuttgarter  Simulator für neuronale Netzwerke (SNNS) |
| RTextTools | Automatische Textklassifizierung über beaufsichtigtes Lernen |
| RUnit | R-Komponententest-Framework |
| runjags | Schnittstellen-Dienstprogramme, parallele Verarbeitungsmethoden und zusätzliche Verteilungen für MCMC-Modelle in JAGS |
| Runuran | R-Schnittstellezu zufällige Variate-Generatoren (UNU.RAN) |
| rworldmap | Zuordnen von globalen Daten, Vektoren und Raster |
| rworldxtra | Ländergrenzen mit hoher Auflösung |


###R-Module von S bis Z

| Paketname | Paketbeschreibung |
| ------------ | ------------------- |
| SampleSizeMeans | Beispielgrößenberechnungen, normal |
| SampleSizeProportions | Berechnen von Beispielgrößenanforderungen, wenn die Differenz zwischen zwei binomialen Proportionen geschätzt wird |
| sandwich | Stabile Kovarianzmatrixschätzung |
| sbgcop | Imputation und semiparametrische Bayes/Gaußsche Copula-Schätzung |
| scales | Skalierungsfunktionen für Grafiken |
| scapeMCMC | Diagnoseplots für MCMC |
| scatterplot3d | 3D Scatter Plot|
| sciplot | Wissenschaftliche Graphfunktionen für  faktorielle Designs |
| segmented | Segmentierte Beziehungen in Regressionsmodellen mit Breakpoints/Changepoints-Schätzung |
| sem | Strukturelle Gleichungsmodelle |
| seriation | Infrastruktur für Reihung |
| setRNG | Festgelegt (Normal) Zufallszahlen-Generator und Seed |
| sgeostat | Ein objektorientiertes Framework für die geostatistische Modellierung in S+ |
| shapefiles | Lesen und Schreiben von ESRI-Shape-Dateien |
| shiny | Web Application Framework für R |
| SimpleTable | Bayes-Inferenz und Sensitivitätsanalyse für kausale Effekte von 2 x 2 und 2 x 2 x K Tabellen bei nicht gemessener Uneindeutigkeit |
| slam | Einfache Arrays und Matrizen mit geringer Dichte |
| smoothSurv | Survival-Regression mit geglätteter Fehlerverteilung |
| sna | Tools für Soziale Netzwerkanalyse |
| snow | Simple Network of Workstations |
| SnowballC | Snowball-Stemmer basierend auf der C-Libstemmer-UTF-8-Bibliothek |
| snowFT | Fault Tolerant Simple Network of Workstations |
| sp | Klassen und Methoden für räumliche Daten |
| spacetime | Klassen und Methoden für räumlich-zeitbezogene Daten |
| SparseM | Sparse Linear Algebra |
| spatial | Funktionen für Kriging und Punkt-Muster Analysen |
| spBayes | Univariate und ultivariate räumlich-zeitliche Modellierung |
| spdep | Räumliche Abhängigkeit: Gewichtungsschemas, Statistiken und Modelle |
| spikeslab | Vorhersage und Variablenauswahl von Projekten anhand der Slab-Regression |
| splancs | Räumliche und Raum/Zeit-Punkt-Musteranalyse |
| splines | Regression: Spline-Funktionen und -Klassen |
| spTimer | Raum-zeitliche Bayes-Modellierung mit R |
| stats | R-Stats-Paket |
| stats4 | Statistische Funktionen mit S4-Klassen |
| stochvol | Effiziente Bayes-Inferenz für stochastische Volatilitätsmodelle (SV-Modelle) |
| stringr | Einfacheres Arbeiten mit Zeichenfolgen |
| strucchange | Testen, Überwachen und Datieren struktureller Änderungen |
| stsm | Strukturelle Zeitreihenmodelle |
| stsm.Class | Klasse und Methoden für die strukturelle Zeitreihenmodelle |
| SuppDists | Zusätzliche Verteilungen |
| survival | Survival-Analysen |
| svmpath | svmpath: der SVM-Pfad-Algorithmus |
| tau | Dienstprogramme zur Textanalyse |
| tcltk | Tcl/Tk-Schnittstelle |
| tcltk2 | Erweiterungen für Tcl/Tk |
| TeachingDemos | Demos zum Lehren und Lernen |
| tensorA | Erweiterte Tensorarithmetik mit benannten Indizes |
| testthat | Testthat-Code. Tools, mit denen Testen Spaß macht |
| textcat | N-Gramm-basierte Textkategorisierung |
| textir | Inverse Regression für die Textanalyse |
| tfplot | Zeitrahmen-Benutzer-Dienstprogramme |
| tframe | Zeitrahmen-Codierungskernel |
| Tgp | Gaußsche Prozessmodelle mit Bayes-Struktur |
| TH.data | THs Datenarchiv |
| timeDate | Rmetrics - Chronologische und Kalender Objekte |
| tm | Text Mining-Paket |
| tools | Tools für die Paketentwicklung |
| translations | Das R-Übersetzungspaket |
| translations | Klassifizierungs- und Regressionsalgorithmus-Strukturen |
| tseries | Zeitreihenanalysen und Finanzinformatik |
| tsfa | Zeitreihen-Faktorenanalyse |
| tsoutliers | Automatische Erkennung von Ausreißern in Zeitreihen |
| TSP | Problem mit reisendem Vertriebsmitarbeiter |
| UsingR | Datasets für den Text Using R für Einführung in die Statistik |
| utils | Das R-Utils-Paket |
| VarSelectIP | Objektive Bayes-Modellauswahl |
| VCD | Visualisieren von kategorischen Daten |
| vegan | Ökologiepaket der Community |
| VGAM | Vektorgeneralisierte lineare und additive Modelle |
| VIF | VIF-Regression: Ein schneller Regressionsalgorithmus für große Datenmengen |
| whisker | {{mustache}} für R, Vorlagen ohne Logik |
| wordcloud | Word-Clouds |
| XLConnect | Excel-Connector für R |
| XML | Tools zum Analysieren und Generieren von XML in R und S-Plus |
| xtable | Tabellen in LaTeX oder HTML exportieren |
| xts | Erweiterbare Zeitreihen |
| yaml | Methoden für die Konvertierung von R-Daten in YAML und zurück |
| zic | Bayes-Inferenz für Zero-Inflated-Count-Modelle |
| zipfR | Statistische Modelle für Worthäufigkeitsverteilungen |
| zoo | S3-Infrastruktur für reguläre und unregelmäßige Zeitreihen (Zs sortierte Beobachtungen) |


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

<!--HONumber=54--> 