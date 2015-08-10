<properties
	pageTitle="Erweitern Ihres Experiments mit R | Microsoft Azure"
	description="In diesem Artikel erfahren Sie, wie Sie die Funktionalität von Azure Machine Learning Studio über die R-Sprache mithilfe des Moduls &quot;Execute R Script&quot; erweitern."
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


#Erweitern Ihres Experiments mit R 

Sie können die Funktionalität von ML Studio mithilfe der Programmiersprache R hinaus erweitern, indem Sie das [Execute R Script][execute-r-script] verwenden.

Dieses Modul akzeptiert mehrere Eingabedatasets, als Ergebnis wird ein einzelnes Dataset ausgegeben. Sie können ein R-Skript für den Parameter **R Script** des Moduls [Execute R Script][execute-r-script] eingeben.

Sie können auf jeden Eingangsport des Moduls mithilfe von Code wie folgt zugreifen:

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Auflisten aller derzeit installierten Pakete

Die Liste der installierten Pakete kann sich ändern. Um die vollständige Liste zu erhalten, fügen Sie die folgenden Zeilen in das Modul [Execute R Script][execute-r-script] ein, um die Liste an das Ausgabedataset zu senden:

    out <- data.frame(installed.packages())
    maml.mapOutputPort("out")

Um die Liste der Pakete anzuzeigen, verbinden Sie ein Modul für die Konvertierung wie [Convert to CSV][convert-to-csv] mit der Ausgabe des Moduls [Execute R Script][execute-r-script]. Führen Sie das Experiment aus, klicken Sie dann auf die Ausgabe des Konvertierungsmoduls, und wählen Sie **Download** aus.

##Importieren von Paketen

Sie können auch Pakete importieren, die noch nicht aus einem bereitgestellten ML Studio Repository installiert wurden. Hierzu verwenden Sie die folgenden Befehle im Modul [Execute R Script][execute-r-script] und einem gezippten Paketarchiv:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

Hierbei enthält `my_favorite_package.zip` die ZIP-Datei des Pakets.

##Liste der installierten Pakete

Zur besseren Übersicht wird die Liste der Pakete in der aktuellen Version in der folgenden Tabelle bereitgestellt.

Eine vollständige Liste der aktuell verfügbare Pakete finden Sie im Abschnitt **Auflisten aller derzeit installierten Pakete** oben. Die aktuelle Dokumentation zu R finden Sie [hier](http://cran.r-project.org/manuals.html).

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
| abc | Tools für die ungefähre Bayessche Berechnung \(ABC\) |
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
| AtelieR | Eine GTK-GUI für das Lehren grundlegender Konzepte zum statistischen Rückschluss und zur Ausführung elementarer Bayes-Tests |
| BaBooN | Bayesian Bootstrap Predictive Mean Matching – mehrfache und einzelne Imputationen für diskrete Daten |
| BACCO | Bayessche Analyse der Ausgabe von Computercode |
| BaM | Funktionen und Datasets für Bücher, von Jeff Gill |
| bark | Bayessche additive Regressionskernel |
| BAS | Bayessche Modellmittelung mit bayesschem Adaptive Sampling |
| base | Das R-Basispaket |
| BayesDA | Funktionen und Datasets für das Buch "Bayessche Datenanalyse" |
| bayesGARCH | Bayessche Schätzung des Modells GARCH\(1,1\) mit Student-t-Innovationen |
| bayesm | Bayessche Inferenz für Marketing und Mikroökonometrie |
| bayesmix | Bayessche Vermischungsmodelle mit JAGS |
| bayesQR | Bayessche Quantilsregression |
| bayesSurv | Bayessche Fortbestandsregression mit flexiblen Fehler- und zufälligen Effektverteilungen |
| Bayesthresh | Bayessche Modelle mit Schwellenwerten und gemischten Effekten für kategorische Daten |
| BayesTree | Bayessche Methoden für baumstrukturbasierte Modelle |
| BayesValidate | BayesValidate-Paket |
| BayesX | R-Hilfsprogramme für das Softwarepaket BayesX |
| BayHaz | R-Funktionen für die Bayessche Schätzung der Gefahrenrate |
| bbemkr | Bayessche Schätzung der Bandbreite für multivariate Kernelregression mit gaußschen Fehlern |
| BCBCSF | Tendenzkorrigierte bayessche Klassifizierung mit ausgewählten Features |
| BCE | Bayesscher Kompositionsschätzer: Schätzen der \(taxonomischen\) Beispielzusammensetzung von Biomarkerdaten |
| bclust | Bayessches Clustering mit hierarchischem Spike-and-Slab-Modell, für das Clustering von hochdimensionalen Daten geeignet |
| bcp | Ein Paket für die Ausführung einer bayesschen Analyse der mit Änderungspunkten verbundenen Probleme |
| BenfordTests | Statistische Tests für die Bewertung der Übereinstimmung mit dem Benford'schen Gesetz |
| bfp | Bayessche gebrochene Polynome |
| BH | C++-Headerdateien verbessern |
| bisoreg | Bayessche isotonische Regression mit Bernstein-Polynomen |
| Bit | Eine Klasse für Vektoren von booleschen 1-Bit-Werten |
| bitops | Bitweise Vorgänge |
| BLR | Bayessche lineare Regression |
| BMA | Bayessche Modellmittelung |
| Bmix | Bayessches Sampling für Stick-Breaking-Mischungen |
| BMS | Bayessche Modellmittelungsbibliothek |
| bnlearn | Bayessches Netzwerkstrukturlernen, Parameterlernen und Inferenz |
| boa | Bayessches Ausgabeanalyseprogramm \(BOA\) für MCMC |
| Bolstad | Bolstad-Funktionen |
| boot | Bootstrap-Funktionen \(ursprünglich von Angelo Canty für S\) |
| bootstrap | Funktionen für das Buch "Eine Einführung in Bootstrap" |
| bqtl | Bayessche QTL-Zuordnung \(Toolkit\) |
| BradleyTerry2 | Bradley-Terry-Modelle |
| brew | Vorlagenframework für die Berichterstellung |
| brglm | Tendenzreduzierung in generalisierten linearen Modellen mit binomialen Antworten |
| bspec | Bayessche Spektralinferenz |
| bspmma | bspmma: Semiparametrische bayessche Modelle für die Meta-Analyse |
| BVS | Bayessche Variantenauswahl: Bayessche Modellunsicherheitstechniken für genetische Zuordnungsstudien |
| cairoDevice | Cairo-basierter, plattformübergreifender Grafikgerätetreiber mit Antialiasing |
| calibrator | Bayessche Kalibrierung komplexer Computercodes |
| car | Begleitung zu angewandter Regression |
| caret | Klassifizierungs- und Regressionstraining |
| catnet | Kategorische bayessche Netzwerkinferenz |
| caTools | Tools: Statistik zum Verschieben von Fenstern, GIF, Base64, ROC AUC usw. |
| chron | Chronologische Objekte, die Datumsangaben und Uhrzeiten verarbeiten können |
| class | Funktionen für die Klassifizierung |
| cluster | Cluster Analysis Extended Rousseeuw et al. |
| clusterSim | Suche nach optimalen Clusteringverfahren für ein Dataset |
| coda | Ausgabe der Analyse und Diagnose für MCMC |
| codetools | Codeanalysetools für R |
| coin | Bedingte Inferenzverfahren in einem Permutationstestframework |
| colorspace | Farbraummanipulation |
| combinat | Kombinatorische Dienstprogramme |
| compiler | Das R-Compiler-Paket |
| corpcor | Effiziente Schätzung von Kovarianz und \(teilweiser\) Korrelation |
| cslogistic | Bedingt festgelegte logistische Regression |
| ctv | CRAN-Aufgabenansichten |
| cubature | Adaptive multivariate Integration über Hypercubes |
| data.table | Erweiterung von data.frame |
| datasets | Das R-Datasets-Paket |
| date | Funktionen zum Verarbeiten von Datumsangaben |
| dclone | Klonen von Daten und MCMC-Tools für Methoden zur maximalen Wahrscheinlichkeit |
| deal | Bayessche Lernnetzwerke mit gemischten Variablen |
| Deducer | Deducer: Eine Datenanalyse-GUI für R |
| DeducerExtras | Zusätzliche Dialogfelder und Funktionen für Deducer |
| deldir | Delaunay-Triangulation und Dirichlet \(Voronoi\)-Mosaik |
| DEoptimR | Differenzielle Evolutionsoptimierung in reinem R |
| deSolve | Allgemeine Solver für Anfangswertprobleme bei gewöhnlichen Differenzialgleichungen \(ODE\), teilweisen Differenzialgleichungen \(PDE\), differenzial-algebraischen Gleichungen \(DAE\) und verzögerten Differenzialgleichungen \(DDE\) |
| devtools | Tools für die einfachere Entwicklung von R-Code |
| dichromat | Farbschemata für Dichromaten |
| digest | Erstellen kryptografischer Hash-Digests von R-Objekten |
| distrom | Verteilte multinomiale Regression |
| dlm | Bayessche und Wahrscheinlichkeitsanalyse dynamischer linearer Modelle |
| doSNOW | Paralleler Foreach-Adapter für das snow-Paket |
| dplyr | dplyr: Eine Grammatik der Datenbearbeitung |
| DPpackage | Bayessche nichtparametrische Modellierung in R |
| dse | Schätzung dynamischer Systeme \(Zeitreihenpaket\) |
| e1071 | Sonstige Funktionen der Abteilung des Statistischen Amts \(e1071\), TU Wien |
| EbayesThresh | Empirische bayessche Schwellenwertbestimmung und die zugehörigen Methoden |
| ebdbNet | Empirische bayessche Schätzung von dynamischen bayesschen Netzwerken |
| effects | Auswirkungsanzeigen für lineare, generalisierte Multinomial-Logit-, Proportional-Odds-Logit-Modelle und Modelle für gemischte Effekte |
| emulator | Bayessche Emulation von Computerprogrammen |
| ensembleBMA | Probabilistische Prognose mit Ensembles und bayesscher Modellmittelung |
| entropy | Abschätzung von Entropie, gegenseitigen Information und verwandten Mengen |
| EvalEst | Schätzung dynamischer Systeme - Erweiterungen |
| evaluate | Analyse- und Auswertungstools, die mehr Details als der Standard bieten |
| evdbayes | Bayessche Analyse in der Extremwerttheorie |
| evora | Ausreißer bei epigenetischen Variablen für die Risikovorhersageanalyse |
| exactLoglinTest | Genaue Monte Carlo-Tests für Log-lineare Modelle |
| expm | Matrix exponential |
| extremevalues | Univariate Ausreißererkennung |


###R-Module von F bis L

| Paketname | Paketbeschreibung |
| ------------ | ------------------- |
| factorQR | Faktormodelle für bayessche Quantilsregression |
| faoutlier | Erkennungsmethoden für einflussreiche Fälle für Faktoranalyse und SEM |
| fitdistrplus | Hilfe zum Anpassen einer parametrischen Verteilung auf nicht zensierte oder zensierte Daten |
| FME | Eine flexible Modellierungsumgebung für inverse Modellierung, Empfindlichkeit, Identifizierbarkeit, Monte Carlo-Analyse |
| foreach | Foreach-Schleifenkonstrukt für R |
| forecast | Vorhersagefunktionen für Zeitreihen und lineare Modelle |
| foreign | Lesen von Daten, gespeichert von Minitab, S SAS, SPSS, Stata, Systat, Weka, dBase... |
| formatR | R-Code automatisch formatieren |
| Formula | Erweiterte Modellformeln |
| fracdiff | Stückweise differenzierte ARIMA-Modelle, genannt ARFIMA\(p,d,q\)-Modelle |
| gam | Verallgemeinerte additive Modelle |
| gamlr | Gamma-Lasso-Regression |
| gbm | Verallgemeinerte verstärkte Regressionsmodelle |
| gclus | Clusteringgrafiken |
| gdata | Verschiedene R-Programmiertools für die Datenbearbeitung |
| gee | Solver für verallgemeinerte Schätzungsgleichungen |
| genetics | Populationsgenetik |
| geoR | Analyse von geostatistischen Daten |
| geoRglm | geoRglm – ein Paket für verallgemeinerte lineare räumliche Modelle |
| geosphere | Sphärische Trigonometrie |
| ggmcmc | Grafische Tools zum Analysieren von Markov-Ketten-Monte Carlo-Simulationen von bayesscher Inferenz |
| ggplot2 | Eine Implementierung der Grammatik der Grafik |
| glmmBUGS | Verallgemeinerte lineare gemischte Modelle und räumliche Modelle mit WinBUGS, BRugs oder OpenBUGS |
| glmnet | Lasso- und Flexibles-Netz-Modelle, regularisiert, verallgemeinert, linear |
| gmodels | Verschiedene R-Programmiertools für Modellanpassung |
| gmp | Mehrfache Präzisionsarithmetik |
| gnm | Verallgemeinerte nicht lineare Modelle |
| googlePublicData | Eine R-Bibliothek zum Erstellen von Google Public Data Explorer DSPL-Metadatendateien |
| googleVis | Schnittstelle zwischen R und Google-Diagrammen |
| GPArotation | GPA-Faktorrotation |
| gplots | Verschiedene R-Programmiertools zum Darstellen von Daten |
| graphics | Das R-Grafikpaket |
| grDevices | Die R-Grafikgeräte und Unterstützung für Farben und Schriftarten |
| gregmisc | Sonstige Funktionen von Greg |
| grid | Das Grid-Grafikpaket |
| gridExtra | Funktionen in Grid-Grafiken |
| growcurves | Bayessche halb- und nichtparametrische Wachstumskurvenmodelle, die zusätzlich Mehrfachmitgliedschafts-Zufallseffekte enthalten |
| grpreg | Regulierungspfade für Regressionsmodelle mit gruppierten Kovariaten |
| gsubfn | Hilfsprogramme für Zeichenfolgen und Funktionsargumente |
| gtable | Anordnen von Grobs in Tabellen |
| gtools | Verschiedene R-Programmiertools |
| gWidgets | gWidgets-API zum Erstellen von Toolkit-unabhängigen, interaktiven Benutzeroberflächen |
| gWidgetsRGtk2 | Toolkit-Implementierung von gWidgets für RGtk2 |
| haplo.stats | Statistische Analyse der Haplotypen mit Merkmalen und Kovariaten bei mehrdeutiger Bindungsphase |
| hbsae | Hierarchische bayessche Schätzung im kleinen Bereich |
| hdrcde | Höchste-Dichte-Regionen und bedingte Dichteschätzung |
| heavy | Paket für Ausreißereinbeziehung mit einseitigen Distributionen |
| hflights | Flüge, die Houston 2011 verlassen haben |
| HH | Statistische Analyse und Datenanzeige: Heiberger und Holland |
| HI | Simulation von Verteilungen, von geschachtelten Hyperebenen unterstützt |
| highr | Syntaxhervorhebung für R |
| Hmisc | Sonstiges, Harrell |
| htmltools | Tools für HTML |
| httpuv | HTTP- und WebSocket-Serverbibliothek |
| httr | Tools für die Arbeit mit URLs und HTTP |
| IBrokers | R-API zur Interactive Brokers Trader Workstation |
| igraph | Netzwerkanalyse und -Visualisierung |
| intervals | Tools zum Arbeiten mit Punkten und Intervallen |
| iplots | iPlots – interaktive Grafiken für R |
| ipred | Verbesserte Prädiktoren |
| irr | Verschiedene Koeffizienten von Interrater-Reliabilität und Vereinbarung |
| iterators | Iterator-Konstrukt für R |
| JavaGD | Java-Grafikgerät |
| JGR | JGR – Java-GUI für R |
| kernlab | Kernel-basiertes Machine Learning-Lab |
| KernSmooth | Funktionen für die Kernelglättung für Wand und Jones \(1995\) |
| KFKSDS | Kalman-Filter, Glätter und Störungsglätter |
| kinship2 | Stammbaumfunktionen |
| kknn | Gewichtete k-nächste Nachbarn |
| klaR | Klassifizierung und Visualisierung |
| knitr | Ein allgemeines Paket für die dynamische Berichterstellung in R |
| ks | Kernelglättung |
| labeling | Achsenbeschriftung |
| Lahman | Sean Lahmans Baseballdatenbank |
| lars | Least-Angle-Regression, Lasso und Forward Stagewise |
| lattice | Gittergrafik |
| latticeExtra | Zusätzliche Grafikhilfsprogramme auf Gitterbasis |
| lava | Lineare Latentvariablenmodelle |
| lavaan | Latentvariablenanalyse |
| leaps | Regressionsteilmengenauswahl |
| LearnBayes | Funktionen zum Erlernen von Bayes-Inferenz |
| limSolve | Lösen von linearen inversen Modellen |
| lme4 | Lineare Modelle mit gemischten Effekten mit Eigen und S4 |
| lmm | Lineare gemischte Modelle |
| lmPerm | Permutationstests für lineare Modelle |
| lmtest | Testen von linearen Regressionsmodellen |
| locfit | Lokale Regression, Wahrscheinlichkeits- und Dichteschätzung |
| lpSolve | Schnittstelle zu Lp\_solve v. 5.5 zum Lösen von linearen/Ganzzahlenprogrammen |


###R-Module von M bis R

| Paketname | Paketbeschreibung |
| ------------ | ------------------- |
| magic | Erstellen und Untersuchen von magischen Quadraten |
| magrittr | magrittr – ein Forward-Pipe-Operator für R |
| mapdata | Zusätzliche Mapping-Datenbanken |
| mapproj | Mapping-Projektionen |
| maps | Zeichnen von geografischen Karten |
| maptools | Tools zum Lesen und Verarbeiten räumlicher Objekte |
| maptree | Zuordnen, Kürzen und Darstellen von Baumstrukturmodellen |
| markdown | Markdown-Rendering für R |
| MASS | Unterstützungsfunktionen und Datasets für Venables und Ripleys MASS |
| MasterBayes | ML- und MCMC-Methoden für Stammbaumrekonstruktion und -Analyse |
| Matrix | Klassen und Methoden für Matrizen mit hoher und geringer Dichte |
| matrixcalc | Auflistung von Funktionen für Matrixberechnungen |
| MatrixModels | Modellierung mit Matrizen mit hoher und geringer Dichte |
| maxent | Multinomiale logistische Regression mit geringem Arbeitsspeicher und Unterstützung für die Klassifizierung von Texten |
| maxLik | Einschätzung der maximalen Wahrscheinlichkeit |
| mcmc | Markov-Kette Monte Carlo |
| MCMCglmm | MCMC: verallgemeinerte lineare gemischte Modelle |
| MCMCpack | Markov-Kette Monte Carlo \(MCMC\), Paket |
| memoise | Memoise-Funktionen |
| methods | Formale Methoden und Klassen |
| mgcv | GAM Computation Vehicle gemischt mit GCV/AIC/REML-Glättungsschätzung |
| mice | Multivariate Imputation durch verkettete Gleichungen |
| microbenchmark | Timingfunktionen mit Genauigkeit unter einer Mikrosekunde |
| mime | Zuordnen von Dateinamen zu MIME-Typen |
| minpack.lm | R-Schnittstelle für den Algorithmus der nichtlinearen kleinsten Quadrate in MINPACK \(Levenberg-Marquardt\) und Unterstützung für Grenzen |
| minqa | Ableitungsfreie Optimierungsalgorithmen durch quadratische Angleichung |
| misc3d | Sonstige 3D-Plots |
| miscF | Sonstige Funktionen |
| miscTools | Verschiedene Tools und Hilfsprogramme |
| mixtools | Tools zum Analysieren von begrenzten Mischmodellen |
| mlbench | Machine Learning-Benchmark-Probleme |
| mlogitBMA | Bayessches Modell für die Mittelung von multinomialen Logit-Modellen |
| mnormt | Der multivariate Normalwert und t-Verteilungen |
| MNP | R-Paket zum Anpassen für das multinomiale Probit-Modell |
| modeltools | Tools und Klassen für statistische Modelle |
| mombf | Bayessche Moment- und Inverse Moment-Faktoren |
| monomvn | Schätzung für normale multivariate und Student-t-Daten mit monotonen Fehlmustern |
| monreg | Nichtparametrische monotone Regression |
| mosaic | Projekt MOSAIC \(mosaic-web.org\): Statistiken und Hilfsprogramme für die Mathematiklehre |
| MSBVAR | Markov-Switching-, bayessche, Vektorautoregressionsmodelle |
| msm | Markov-Modelle mit mehreren Zuständen und ausgeblendete Markov-Modelle in fortlaufender Zeit |
| multcomp | Gleichzeitige Inferenz in allgemeinen parametrischen Modellen |
| multicool | Permutationen von Multimenge in cool-lex-Reihenfolge. |
| munsell | Munsell-Farbsystem |
| mvoutlier | Multivariate Ausreißererkennung auf Basis zuverlässiger Methoden |
| mvtnorm | Multivariate Normal- und t-Verteilungen |
| ncvreg | Regulierungspfade für Regressionsmodelle mit SCAD- und MCP-Abzug |
| nlme | Modelle mit gemischten Effekten, linear und nicht linear |
| NLP | Verarbeitungsinfrastruktur für Natürliche Sprache |
| nnet | Feed-Forward für neuronale Netzwerke und multinomiale Protokoll-Linear-Modelle |
| numbers | Zahlentheoretische Funktionen |
| numDeriv | Genaue numerische Ableitungen |
| openNLP | Apache OpenNLP-Tool-Schnittstelle |
| openNLPdata | Apache OpenNLP Jars- und Basic English-Sprachmodelle |
| OutlierDC | Ausreißererkennung mit Quantilsregression für zensierte Daten |
| OutlierDM | Ausreißererkennung für replizierte Daten mit hohem Durchsatz |
| outliers | Tests auf Ausreißer |
| pacbpred | PAC-bayessche Schätzung und Vorhersage für Sparse Additive-Modelle |
| parallel | Unterstützung für parallele Berechnung in R |
| partitions | Additive Partitionen von Ganzzahlen |
| party | Ein Labor für rekursive Partitionierung |
| PAWL | Implementierung des PAWL-Algorithmus |
| pbivnorm | Bivariates Normal-CDF, vektorisiert |
| pcaPP | Stabile PCA durch Projection Pursuit |
| permute | Funktionen zum Generieren von eingeschränkten Permutationen von Daten |
| pls | Regression: Partielle Methode der kleinsten Quadrate und Hauptkomponenten |
| plyr | Tools zum Aufteilen, Anwenden und Kombinieren von Daten |
| png | Lesen und Schreiben von PNG-Bildern |
| polynom | Eine Auflistung von Funktionen zum Implementieren einer Klasse für univariate polynomiale Manipulationen |
| PottsUtils | Hilfsfunktionen der Potts-Modelle |
| predmixcor | Klassifizierungsregel basierend auf bayesschen Mischmodellen mit tendenzkorrigierter Featureauswahl |
| PresenceAbsence | Auswerten des Vorhandensein/Abwesenheit-Modells |
| prodlim | Abschätzung des Produktlimits. Kaplan-Meier- und Aalen-Johansson-Methode für die zensierte Ereignisverlaufsanalyse \(Fortbestand\) |
| profdpm | Profil der Dirichlet-Prozessmischungen |
| profileModel | Tools zur Profilerstellung bei Inferenzfunktionen für verschiedene Modellklassen |
| proto | Prototyp: objektbasierte Programmierung |
| pscl | Political Science Computational Laboratory, Stanford University |
| psych | Verfahren zur psychologischen, psychometrischen und Persönlichkeitsforschung |
| quadprog | Funktionen zum Lösen von Problemen mit quadratischen Programmen |
| quantreg | Quantilsregression |
| qvcalc | Quasi-Abweichungen für Faktoreffekte statistischer Modelle |
| R.matlab | Lesen und Schreiben von MAT-Dateien zusammen mit R-zu-MATLAB-Konnektivität |
| R.methodsS3 | Hilfsfunktion zum Definieren von S3-Methoden |
| R.oo | objektorientierte R-Programmierung mit oder ohne Verweise |
| R.utils | Verschiedene Hilfsprogramme zur Programmierung |
| R2HTML | HTML-Export für R-Objekte |
| R2jags | Ein Paket für die Ausführung von Jags aus R |
| R2OpenBUGS | Ausführen von OpenBUGS aus R |
| R2WinBUGS | Ausführen von WinBUGS und OpenBUGS aus R/S-PLUS |
| ramps | Bayessches geostatisches Modellieren mit RAMPS |
| RandomFields | Simulation und Analyse von zufälligen Feldern |
| randomForest | Breiman und Cutler: Zufällige Gesamtstrukturen für Klassifizierung und Regression |
| RArcInfo | Funktionen zum Importieren von Daten aus binären Abdeckungen von Arc/Info V7.x |
| raster | Raster: Analyse der geografischen Daten und Modellierung |
| rbugs | Zusammenführen von R und OpenBugs und mehr |
| RColorBrewer | ColorBrewer-Paletten |
| Rcpp | Nahtlose Integration von R und C++ |
| RcppArmadillo | RCPP-Integration für auf Armadillo-Vorlagen basierende Lineare-Algebra-Bibliothek |
| rcppbugs | R-Bindung für cppbugs |
| RcppEigen | RCPP-Integration für auf Eigen-Vorlagen basierende Lineare-Algebra-Bibliothek |
| RcppExamples | Beispiele zur Verwendung von R- und C++-Schnittstellen mit Rcpp |
| RCurl | Allgemeine Netzwerk-Clientschnittstelle \(HTTP/FTP/...\) für R |
| relimp | Relativer Anteil der Effekte in einem Regressionsmodell |
| reshape | Flexibles Umformen von Daten |
| reshape2 | Flexibles Umformen von Daten: eine Neuauflage des Pakets zum Umformen |
| rgdal | Bindungen für die Geospatialdatenabstraktions-Bibliothek |
| rgeos | Schnittstelle zu Geometrieengine – Open-Source-\(GEOS\) |
| rgl | 3D-Visualisierungsgerätesystem \(OpenGL\) |
| RGraphics | Daten und Funktionen aus dem Buch R Graphics, zweite Auflage |
| RGtk2 | R-Bindungen für Gtk 2.8.0 und höher |
| RJaCGH | Umkehrbare Jump-MCMC für die Analyse von CGH-Arrays |
| rjags | Grafische bayessche Modelle mit MCMC |
| rJava | Low-Level-Schnittstelle R zu Java |
| RJSONIO | Serialisieren von R-Objekten in JavaScript Object Notation \(JSON\) |
| robCompositions | Stabile Schätzung für zusammengesetzte Daten |
| robustbase | Grundlegende stabile Statistiken |
| RODBC | Zugriff auf die ODBC-Datenbank |
| rootSolve | Nichtlineare Stammsuche, Gleichgewichts- und Steady-State-Analyse normaler Differenzialgleichungen |
| roxygen | Literate Programming in R |
| roxygen2 | In-Source-Dokumentation für R |
| rpart | Rekursive Partitionierung und Regressionsstrukturen |
| rrcov | Skalierbare, stabile Schätzer mit hohem Bruchpunkt |
| rscproxy | Statconn: bietet portable C-Stil-Schnittstellen zu R \(StatConnector\) |
| RSGHB | Funktionen für hierarchische bayessche Schätzung: Ein flexibler Ansatz |
| RSNNS | Neuronale Netzwerke in R mit dem Stuttgarter Simulator für neuronale Netzwerke \(SNNS\) |
| RTextTools | Automatische Textklassifizierung über beaufsichtigtes Lernen |
| RUnit | R-Komponententest-Framework |
| runjags | Schnittstellenhilfsprogramme, parallele Verarbeitungsmethoden und zusätzliche Verteilungen für MCMC-Modelle in JAGS |
| Runuran | R-Schnittstelle zu UNU.RAN-Zufallsgeneratoren |
| rworldmap | Zuordnen von globalen Daten, Vektor und Raster |
| rworldxtra | Ländergrenzen mit hoher Auflösung |


###R-Module von S bis Z

| Paketname | Paketbeschreibung |
| ------------ | ------------------- |
| SampleSizeMeans | Beispielgrößenberechnungen für normale Mittelwerte |
| SampleSizeProportions | Berechnen von Beispielgrößenanforderungen, wenn die Differenz zwischen zwei binomialen Proportionen geschätzt wird |
| sandwich | Stabile Kovarianzmatrixschätzer |
| sbgcop | Semiparametrische bayessche/gaußsche Copulasschätzung und Imputation |
| scales | Skalierungsfunktionen für Grafiken |
| scapeMCMC | Diagnoseplots für MCMC |
| scatterplot3d | 3D-Punktwolke |
| sciplot | Wissenschaftliche Graphfunktionen für faktorielle Versuchspläne |
| segmented | Segmentierte Beziehungen in Regressionsmodellen mit Bruchpunkt/Umwandlungspunkt-Schätzung |
| sem | Strukturgleichungsmodelle |
| seriation | Infrastruktur für Reihung |
| setRNG | Festlegen von \(normalem\) Zufallszahlengenerator und Seed |
| sgeostat | Ein objektorientiertes Framework für die geostatistische Modellierung in S+ |
| shapefiles | Lesen und Schreiben von ESRI-Shape-Dateien |
| shiny | Webanwendungsframework für R |
| SimpleTable | Bayessche Inferenz- und Empfindlichkeitsanalyse für kausale Effekte aus 2 x 2- und 2 x 2 x K-Tabellen bei nicht gemessener Uneindeutigkeit |
| slam | Einfache Arrays und Matrizen mit geringer Dichte |
| smoothSurv | Fortbestandsregression mit geglätteter Fehlerverteilung |
| sna | Tools für die Analyse sozialer Netzwerke |
| snow | Einfaches Netzwerk von Arbeitsstationen |
| SnowballC | Snowball-Stemmer basierend auf der C-Libstemmer-UTF-8-Bibliothek |
| snowFT | Fehlertolerantes einfaches Netzwerk von Arbeitsstationen |
| sp | Klassen und Methoden für räumliche Daten |
| spacetime | Klassen und Methoden für räumlich-zeitliche Daten |
| SparseM | Einfache lineare Algebra |
| spatial | Funktionen für Kriging- und Punktmuster-Analyse |
| spBayes | Univariate und multivariate räumlich-zeitliche Modellierung |
| spdep | Räumliche Abhängigkeit: Gewichtungsschemas, Statistiken und Modelle |
| spikeslab | Vorhersage und Variablenauswahl mit der Spike-and-Slab-Regression |
| splancs | Räumliche und Raum-Zeit-Punktmuster-Analyse |
| splines | Regression-Spline: Funktionen und Klassen |
| spTimer | Räumlich-zeitliche bayessche Modellierung mit R |
| stats | R-Stats-Paket |
| stats4 | Statistische Funktionen mit S4-Klassen |
| stochvol | Effiziente bayessche Inferenz für stochastische Volatilitätsmodelle \(SV-Modelle\) |
| stringr | Einfacheres Arbeiten mit Zeichenfolgen |
| strucchange | Testen, Überwachen und Datieren struktureller Änderungen |
| stsm | Strukturelle Zeitreihenmodelle |
| stsm.class | Klasse und Methoden für strukturelle Zeitreihenmodelle |
| SuppDists | Zusätzliche Verteilungen |
| survival | Fortbestandsanalyse |
| svmpath | svmpath: der SVM-Pfad-Algorithmus |
| tau | Hilfsprogramme zur Textanalyse |
| tcltk | Tcl/Tk-Schnittstelle |
| tcltk2 | Tcl/Tk-Erweiterungen |
| TeachingDemos | Demos zum Lehren und Lernen |
| tensorA | Erweiterte Tensorarithmetik mit benannten Indizes |
| testthat | Testthat-Code. Tools, mit denen Testen Spaß macht |
| textcat | N-Gramm-basierte Textkategorisierung |
| textir | Inverse Regression für die Textanalyse |
| tfplot | Zeitrahmen-Benutzer-Hilfsprogramme |
| tframe | Zeitrahmen-Codierungskernel |
| tgp | Gaußsche Prozessmodelle mit bayesscher Struktur |
| TH.data | TH-Datenarchiv |
| timeDate | Rmetrics – chronologische und Kalenderobjekte |
| tm | Text Mining-Paket |
| tools | Tools für die Paketentwicklung |
| translations | Das R-Übersetzungspaket |
| tree | Klassifizierungs- und Regressionsstrukturen |
| tseries | Zeitreihenanalysen und Computational Finance |
| tsfa | Zeitreihen-Faktorenanalyse |
| tsoutliers | Automatische Erkennung von Ausreißern in Zeitreihen |
| TSP | Rundreiseproblem \(TSP\) |
| UsingR | Datasets für den Text, wobei R für die Einführung in die Statistik verwendet wird |
| utils | Das R-Utils-Paket |
| varSelectIP | Objektive bayessche Modellauswahl |
| vcd | Visualisieren von kategorischen Daten |
| vegan | Ökologiepaket der Community |
| VGAM | Vektorgeneralisierte lineare und additive Modelle |
| VIF | VIF-Regression: Ein schneller Regressionsalgorithmus für große Datenmengen |
| whisker | {{mustache}} für R, Vorlagen ohne Logik |
| wordcloud | Word-Clouds |
| XLConnect | Excel-Connector für R |
| XML | Tools zum Analysieren und Generieren von XML in R und S-Plus |
| xtable | Exportieren von Tabellen in LaTeX oder HTML |
| xts | eXtensible-Zeitreihen |
| yaml | Methoden für die Konvertierung von R-Daten in YAML und zurück |
| zic | Bayessche Inferenz für Zero-Inflated-Count-Modelle |
| zipfR | Statistische Modelle für Worthäufigkeitsverteilungen |
| zoo | S3-Infrastruktur für reguläre und unregelmäßige Zeitreihen \(sortierte Z-Beobachtungen\) |


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
 

<!---HONumber=July15_HO5-->