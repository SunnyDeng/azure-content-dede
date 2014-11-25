<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Machine Learning Sample: Color quantization using K-Means clustering | Azure" description="A sample Azure Machine Learning experiment that evaluates using different K-Means clustering values for quantizing a color image." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning-Beispiel: Farbquantifizierung mit K-Means-Clustering

*Sie finden das Beispielexperiment für dieses Modell in ML Studio im Bereich **EXPERIMENTE** unter der Registerkarte **BEISPIELE**. Der Name des Experiments lautet:*

	Sample Experiment - Color Based Image Compression using K-Means Clustering - Development

## Problembeschreibung

[Farbquantifizierung][Farbquantifizierung] bezeichnet die Reduzierung der Anzahl unterschiedlicher Farben in einem Bild und somit eine Komprimierung. Normalerweise wird versucht, die Farbzusammensetzung des Bilds so gut wie möglich zu erhalten und gleichzeitig die Anzahl der Farben zu reduzieren, entweder um Speicher zu sparen oder um das Bild zu komprimieren.

## Daten

In diesem Beispielexperiment nehmen wir an, dass jedes beliebige 24 Bit RGB-Bild 256 x 256 x 256 mögliche Farben hat. Wir können problemlos Standard-Farbhistogramme basierend auf diesen Intensitätswerten erstellen. Wir können das Bild jedoch auch quantifizieren und die Anzahl der Farben *reduzieren*, z. B. auf 16 oder 64. Auf diese Weise entsteht ein deutlich kleinerer Farbraum und (idealerweise) weniger Rauschen und Abweichungen. Dazu übergeben wir die Pixeldaten (jedes einzelne Pixel als Zeile im Dataset) an unser K-Means-Clusteringmodul.

## Modell

Das Modell wird wie in der folgenden Abbildung gezeigt erstellt:

![Modell][Modell]

Wir führen K-Means-Clustering mit K=10 bis 500 in 5 unterschiedlichen Verzweigungen aus. Zunächst berechnen wir die Cluster und aggregieren das Clustering auf den Mittelwert der Pixelfarben (mithilfe eines R-Skripts).
Zuletzt ordnen wir jedes Pixel zur aggregierten Clusterfarbe zu und geben das neue Bild im CSV-Format aus. Außerdem berechnen wir die Abweichung des quadratischen Mittelwerts der neuen Pixelfarben zum Originalbild und zeigen diese Abweichung in einem R-Diagramm (wieder mit einem R-Skript).

## Ergebnisse

Wir haben die Ergebnisse mit unterschiedlichen Clusteranzahlen (Farben) getestet, wie Sie im folgenden Experimentmodell sehen. Mehr Cluster führen zu Bildern mit höherer Qualität und niedrigerer Komprimierung:

<table>
<tr><th>Original</th>
<td><img alt="Original" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg"></td>
</tr>
<tr><th>K=10</th>
<td><img alt="K=10" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png"></td>
</tr>
<tr><th>K=20</th>
<td><img alt="K=20" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png"></td>
</tr>
<tr><th>K=50</th>
<td><img alt="K=50" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png"></td>
</tr>
<tr><th>K=100</th>
<td><img alt="K=100" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png"></td>
</tr>
<tr><th>K=500</th>
<td><img alt="K=500" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png"></td>
</tr>
</table>

Außerdem haben wir die Genauigkeit über die Abweichung des quadratischen Mittelwerts zu den Farben des Originalbilds berechnet, wie Sie im zweiten Ausgabeport des Moduls "R-Skript ausführen" sehen können:

![Ausgabe des Moduls "R-Skript ausführen"][Ausgabe des Moduls "R-Skript ausführen"]

Das Ergebnis: Je mehr Farbcluster, desto besser stimmen die Farben mit dem Originalbild überein (bessere Qualität).

## Code zum Konvertieren von Bildern nach CSV und umgekehrt

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



[Farbquantifizierung]: http://en.wikipedia.org/wiki/Color_quantization "Farbquantifizierung"
[Modell]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
[Original]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
[K=10]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
[K=20]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
[K=50]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
[K=100]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
[K=500]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
[Ausgabe des Moduls "R-Skript ausführen"]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png
