<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Machine Learning-Beispiel: Farbquantifizierung mit K-Means-Clustering | Azure" description="Azure Machine Learning-Beispielexperiment, das die Verwendung verschiedener K-Means-Clusterwerte für die Quantifizierung eines Farbbilds bewertet" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning-Beispiel: Farbquantifizierung mit K-Means-Clustering

>[AZURE.HINWEIS]
>Das [Beispielexperiment] und das [Beispieldataset] im Zusammenhang mit diesem Modell sind in ML Studio verfügbar. Weitere Details erhalten Sie unten.
[Beispielexperiment]: #sample-experiment
[Beispieldataset]: #sample-dataset


##Problembeschreibung

[Farbquantifizierung](http://en.wikipedia.org/wiki/Color_quantization "Color quantization") bezeichnet die Reduzierung der Anzahl unterschiedlicher Farben in einem Bild und somit eine Komprimierung. Normalerweise wird versucht, die Farbzusammensetzung des Bilds so gut wie möglich zu erhalten und gleichzeitig die Anzahl der Farben zu reduzieren, entweder um Speicher zu sparen oder um das Bild zu komprimieren. 

##Daten

In diesem Beispielexperiment nehmen wir an, dass jedes beliebige 24 Bit RGB-Bild 256 x 256 x 256 mögliche Farben hat. Wir können problemlos Standard-Farbhistogramme basierend auf diesen Intensitätswerten erstellen. Wir können das Bild jedoch auch quantifizieren und die Anzahl der Farben *reduzieren*, z. B. auf 16 oder 64. Auf diese Weise entsteht ein deutlich kleinerer Farbraum und (idealerweise) weniger Rauschen und Abweichungen. Dazu übergeben wir die Pixeldaten (jedes einzelne Pixel als Zeile im Dataset) an unser K-Means-Clusteringmodul. 

##Modell

Das Modell wird wie in der folgenden Abbildung gezeigt erstellt:

![Model][image1]

Wir führen K-Means-Clustering mit K=10 bis 500 in 5 unterschiedlichen Verzweigungen aus. Zunächst haben wir Cluster berechnet und dann aggregiert das clustering dem Mittelwert von deren Pixel Farben (mit einem Skript R). 
Schließlich haben wir die aggregierten Cluster-Farben jedes Pixels zugeordnet und das neue Abbild im CSV-Format gesendet. Außerdem berechnen wir die Abweichung des quadratischen Mittelwerts der neuen Pixelfarben zum Originalbild und zeigen diese Abweichung in einem R-Diagramm (wieder mit einem R-Skript). 

##Ergebnisse

Wir haben die Ergebnisse mit unterschiedlichen Clusteranzahlen (Farben) getestet, wie Sie im folgenden Experimentmodell sehen. Wie im Folgenden sehen ist, sorgt mehr Clustering für bessere Qualität der Bilder mit weniger Komprimierung:

||
------------ | ---------
**Original** | ![Original][image2a]
**K=10**     | ![K=10][image2b]
**K=20**     | ![K=20][image2c]
**K=50**     | ![K=50][image2d]
**K=100**    | ![K=100][image2e]
**K=500**    | ![K=500][image2f]


Außerdem haben wir die Genauigkeit über die Abweichung des quadratischen Mittelwerts zu den Farben des Originalbilds berechnet, wie Sie im zweiten Ausgabeport des Moduls "R-Skript ausführen" sehen können:

![Output of Execute R Script module][image3]

Das Ergebnis: Je mehr Farbcluster, desto besser stimmen die Farben mit dem Originalbild überein (bessere Qualität). 

##Code zum Konvertieren von Bildern nach CSV und umgekehrt

Um die Bilder in ML Studio verwenden zu können, haben wir einen einfachen Konvertierungscode geschrieben, der Bilddateien in ein für ML Studio lesbares CSV-Format konvertiert und umgekehrt. Sie können den folgenden Code gerne verwenden. Für zukünftige Versionen ist ein Modul zum Einlesen von Bildern geplant. 

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Drawing;
	using System.Drawing.Imaging;
	using System.IO;
	 
	namespace Text2Image
	{
	    class Program
	    {
	        static void img2csv(string img_path)
	        {
	            FileInfo img_info = new FileInfo(img_path);
	            string destination_file_directory = img_info.DirectoryName + "\\";
	            string destination_file_name = img_info.Name.Remove(img_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".csv";
	 
	            // Read the image
	            Bitmap img = new Bitmap(img_path);
	 
	            // Create the CSV File and write the header values
	            System.IO.StreamWriter file = new System.IO.StreamWriter(destination_file_path);
	            file.WriteLine("X,Y,R,G,B");
	 
	            // Write the Pixel values
	            for (int x = 0; x < img.Width; x++)
	                for (int y = 0; y < img.Height; y++)
	                {
	                    string line = x + "," + y + "," + img.GetPixel(x, y).R + "," + img.GetPixel(x, y).G + "," + img.GetPixel(x, y).B ;
	                    file.WriteLine(line);
	                }
	 
	            file.Close();
	        }
	 
	        static void csv2img(string csv_path)
	        {
	            FileInfo csv_info = new FileInfo(csv_path);
	            string destination_file_directory = csv_info.DirectoryName + "\\";
	            string destination_file_name = csv_info.Name.Remove(csv_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".png";
	            
	            // Read all the lines in the CSV file
	            string[] lines = System.IO.File.ReadAllLines(csv_path);
	 
	            // set a new bitmap image with the provided width and height in the header
	            string[] wh = lines.Last().Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	            int img_width = Convert.ToInt32(wh[0])+1;
	            int img_height = Convert.ToInt32(wh[1])+1;
	 
	            Bitmap bmp_img = new Bitmap(img_width, img_height);
	 
	            for (int i = 1; i < lines.Length ;i++ )
	            {
	                string[] values = lines[i].Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	                if (values.Length < 3)
	                    continue;
	 
	                int x = Convert.ToInt16(values[0]);
	                int y = Convert.ToInt32(values[1]);
	                int r = Convert.ToInt32(values[2]);
	                int g = Convert.ToInt32(values[3]);
	                int b = Convert.ToInt32(values[4]);
	 
	                bmp_img.SetPixel(x, y, Color.FromArgb(r, g, b));
	            }
	 
	            bmp_img.Save(destination_file_path);
	        }
	 
	        static void Main(string[] args)
	        {
	            string source_path = args[1];
	            FileInfo source_info = new FileInfo(source_path);
	 
	            if (source_info.Extension == ".csv")
	                csv2img(source_path);
	            else
	                img2csv(source_path);
	        }
	    }
	}


## Beispielexperiment

Das nachfolgende Beispielexperiment im Zusammenhang mit diesem Modellist in ML Studio im Abschnitt **EXPERIMENTE** auf der Registerkarte **BEISPIELE** verfügbar.

> **Beispielexperiment -  Auf Farbe basierte Bildkomprimierung mithilfe von K-Means-Clustering - Entwicklung**


## Beispieldataset

Das folgende Beispieldataset, das von diesem Experiment verwendet wird, finden Sie in der Modulpalette in ML Studio unter **Gespeicherte Datasets**.

###Bil Gates RGB-Bild
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]



[image1]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
[image2a]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
[image2b]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
[image2c]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
[image2d]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
[image2e]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
[image2f]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
[image3]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png

