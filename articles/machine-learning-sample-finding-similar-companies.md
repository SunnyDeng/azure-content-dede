<properties title="Azure Machine Learning Sample: Finding similar companies" pageTitle="Machine Learning Sample: Finding similar companies | Azure" description="A sample Azure Machine Learning experiment that uses a clustering model to find similar companies." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning-Beispiel: Suche nach ähnlichen Firmen

*Sie finden das Beispielexperiment für dieses Modell in ML Studio im Bereich **EXPERIMENTE** unter der Registerkarte **BEISPIELE**. Der Name des Experiments lautet:*

	Sample Experiment - S & P 500 Company Clustering - Development

## Problembeschreibung
Clustering von Firmen aus dem S&P 500 anhand der Wikipedia-Einträge über die Firmen. Dieses Modell könnte z. B. verwendet werden, um aufstrebende Firmen danach zu kategorisieren, wie ähnlich sie existierenden S&P 500-Unternehmen sind.

## Daten
Die Daten stammen aus dem Wikipedia XML-Dump. Die Daten wurden außerhalb von ML Studio vorverarbeitet, um Textinhalte für die einzelnen Firmen zu extrahieren, Wiki-Formatierungen und nicht-alphanumerische Zeichen zu entfernen und den gesamten Text in Kleinbuchstaben zu konvertieren. Außerdem wurden bekannte Firmenkategorien hinzugefügt. Beachten Sie, dass für manche Firmen kein Artikel gefunden wurde. Daher enthält der Datensatz weniger als 500 Einträge.

## Modell
Zunächst werden die Artikelinhalte an das "Merkmal-Hashing"-Modul übergeben, das die Textdaten basierend auf dem Hashwert der einzelnen Token in ein numerisches Format transformiert. Die Daten sind zu hochdimensional und dürftig, um direkt im K-Means-Clustering verwendet zu werden. Daher wird PCA im Modul "R-Skript ausführen" verwendet, um die Dimensionalität auf 10 Variablen zu reduzieren. Sie können die PCA-Ergebnisse visualisieren, indem Sie die rechte Ausgabe des R-Moduls öffnen.

Durch systematisches Ausprobieren wurde ermittelt, dass die erste Variable - diejenige mit der höchsten Abweichung - der PCA-transformierten Daten negative Auswirkungen auf das Clustering hat. Daher wird die Variable mithilfe des "Projektspalten"-Moduls aus der Liste der Merkmale entfernt.

Anschließend werden die Daten mit dem K-Means-Algorithmus in 3 Cluster geclustert. Durch systematisches Ausprobieren wurde ermittelt, dass diese Anzahl sinnvolle Ergebnisse generiert. 4 und 5 Cluster wurden ebenfalls ausprobiert.

Zuletzt wird die Clusterbezeichnungsvariable im Metadaten-Editor in eine kategorische Form konvertiert. Außerdem werden die Ergebnisse für den Download in eine CSV-Datei konvertiert.

## Ergebnisse
Sie können die Ergebnisse anzeigen, indem Sie die Ausgabe des Metadaten-Editors visualisieren und die Kategoriespalte (bekannt aus Wikipedia-Daten) gegen die Zuweisungsspalte grafisch darstellen. Die 3 Cluster entsprechen grob den folgenden bekannten Kategorien.

Cluster 0: Nicht-Basiskonsumgüter, Basiskonsumgüter, Finanzen, Gesundheitswesen

Cluster 1: Informationstechnologie

Cluster 2: Energie, Haushaltsgeräte, Telekommunikationsdienste

**Hinweis** Das Clustering ist nicht eindeutig abgegrenzt. Cluster 0 enthält mehrere IT- und Energieunternehmen, und Industrie und Materialien sind auf die Cluster 0 und 1 verteilt

**Hinweis** Das Clustering kann sich von Durchlauf zu Durchlauf unterscheiden.

