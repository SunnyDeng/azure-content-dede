<properties
   pageTitle="Erstellen und Bereitstellen eines JMeter-JUnit-Samplers zum Testen der Leistung von Elasticsearch | Microsoft Azure"
   description="Generieren und Hochladen von Daten in einen Elasticsearch-Cluster mit einem JUnit-Sampler."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="mabsimms"/>
   
# Erstellen und Bereitstellen eines JMeter-JUnit-Samplers zum Testen der Leistung von Elasticsearch

Dieser Artikel ist [Teil einer Serie](guidance-elasticsearch-introduction.md).

Dieses Dokument beschreibt das Erstellen und Verwenden eines JUnit-Samplers, der im Zuge eines JMeter-Testplans Daten generieren und in ein Elasticsearch-Cluster hochladen kann. Dieser Ansatz ist sehr flexibel für Auslastungstests, da große Mengen an Testdaten generiert werden können, ohne dass dafür externe Datendateien erforderlich sind.

> [AZURE.NOTE] Mit diesem Ansatz wurden auch die Auslastungstests erstellt, die zum Bewerten der Datenerfassungsleistung verwendet wurden, die im Dokument „Maximieren der Datenerfassungsleistung mit Elasticsearch in Azure“ beschrieben wird. Die Details des JUnit-Codes werden in dem Dokument erläutert.

Zum Testen der Datenerfassungsleistung wurde der JUnit-Code mithilfe von Eclipse (Mars) entwickelt, und Abhängigkeiten wurden mithilfe von Maven aufgelöst. Die folgenden Verfahren beschreiben Schritt für Schritt das Installieren von Eclipse, das Konfigurieren von Maven, das Erstellen eines JUnit-Tests und das Bereitstellen dieses Tests als Sampler für JUnit-Anforderungen in einem JMeter-Test.

> [AZURE.NOTE] Ausführliche Informationen zur Struktur und Konfiguration der Testumgebung finden Sie im Dokument „Gewusst wie: Erstellen einer Leistungstestumgebung für Elasticsearch“.

## Installieren der erforderlichen Komponenten

Sie benötigen die [Java Runtime Environment](http://www.java.com/en/download/ie_manual.jsp) auf Ihrem Entwicklungscomputer.
Sie müssen außerdem die [Eclipse-IDE für Java-Entwickler](https://www.eclipse.org/downloads/index.php?show_instructions=TRUE) installieren.

> [AZURE.NOTE] Wenn Sie die im Abschnitt [Konfigurieren der Leistungstestumgebung](#overview) beschriebene JMeter-Master-VM als Entwicklungsumgebung verwenden, laden Sie die Windows-32-Bit-Version des Installationsprogramms für Eclipse herunter.

## Erstellen eines JUnit-Testprojekts für Auslastungstests für Elasticsearch

1.  Starten Sie die Eclipse-IDE, falls sie nicht bereits ausgeführt wird, und schließen Sie dann die *Startseite*.

2.  Klicken Sie im Menü *File* auf *New*, und klicken Sie dann auf *Java Project*.

![](./media/guidance-elasticsearch-jmeter-deploy7.png)

3.  Geben Sie im Fenster *New Java Project* einen Projektnamen ein, wählen Sie *Use default JRE*, und klicken Sie dann auf *Finish*.

![](./media/guidance-elasticsearch-jmeter-deploy8.png)

4.  Erweitern Sie im Fenster *Package Explorer* den Knoten, der nach Ihrem Projekt benannt ist. Stellen Sie sicher, dass er einen Ordner namens *Src* und einem Verweis auf die angegebene JRE enthält.

![](./media/guidance-elasticsearch-jmeter-deploy9.png)

5.  Klicken Sie mit der rechten Maustaste auf den Ordner *Src*, klicken Sie auf *New*, und klicken Sie dann auf *JUnit Test Case*.

![](./media/guidance-elasticsearch-jmeter-deploy10.png)

6.  Wählen Sie im Fenster *New JUnit Test Case* die Option *New JUnit 4 Test* aus, geben Sie einen Namen für das Paket ein (der mit dem Namen des Projekts übereinstimmen kann, jedoch nach Konvention mit Kleinbuchstaben beginnen sollte), geben Sie einen Namen für die Testklasse ein, und wählen Sie die Optionen zum Generieren der Methodenstubs aus, die für Ihren Test erforderlich sind. Lassen Sie das Feld *Class under test* leer, und klicken Sie dann auf *Finish*.

![](./media/guidance-elasticsearch-jmeter-deploy11.png)

7.  Wenn das folgende Dialogfeld *New JUnit Test Case* angezeigt wird, geben Sie die Option zum Hinzufügen der JUnit 4-Bibliothek zum Buildpfad an, und klicken Sie dann auf *OK*.

![](./media/guidance-elasticsearch-jmeter-deploy12.png)

8.  Stellen Sie sicher, dass das Codegerüst für den JUnit-Test generiert und im Fenster des Java-Editors angezeigt wird.

![](./media/guidance-elasticsearch-jmeter-deploy13.png)

9.  Klicken Sie im *Package Explorer* mit der rechten Maustaste auf den Knoten für das Projekt, klicken Sie auf *Configure*, und klicken Sie dann auf *Convert to Maven Project*.

> [AZURE.NOTE] Mit Maven können Sie externe Abhängigkeiten (z. B. die Elasticsearch-Java-Clientbibliotheken) leichter verwalten, von denen ein Projekt abhängig ist.

![](./media/guidance-elasticsearch-jmeter-deploy14.png)

10.  Wählen Sie im Dialogfeld *Create new POM* im Dropdown-Listenfeld *Packaging* den Eintrag *jar* aus, und klicken Sie dann auf *Finish*.

![](./media/guidance-elasticsearch-jmeter-deploy15.png)

11.  In dem Bereich, der unterhalb des POM-Editors angezeigt wird, wird möglicherweise folgende Warnung angezeigt: *Buildpfad gibt Ausführungsumgebung J2SE-1.5 an. Es sind keine JREs im Arbeitsbereich installiert, die vollständig mit dieser Umgebung kompatibel sind*. Dies hängt von der Version von Java ab, die Sie auf Ihrem Entwicklungscomputer installiert haben. Wenn Sie eine Java-Version verwenden, die neuer ist als Version 1.5, können Sie diese Warnung gefahrlos ignorieren.

![](./media/guidance-elasticsearch-jmeter-deploy16.png)

12.  Erweitern Sie im POM-Editor den Eintrag *Eigenschaften*, und klicken Sie dann auf *Create*.

![](./media/guidance-elasticsearch-jmeter-deploy17.png)

13.  Geben Sie im Dialogfeld *Add Property* im Feld *Name* den Text *es.version* und im Feld *Value* den Text *1.7.2* ein, und klicken Sie dann auf *OK*. Dies ist die Version der Elasticsearch-Java-Clientbibliothek, die verwendet werden soll. (Diese Version kann in Zukunft abgelöst werden, und durch das Definieren der Version als POM-Eigenschaft und das Referenzieren dieser Eigenschaft von anderer Stelle innerhalb des Projekts kann die Version schnell geändert werden.)

![](./media/guidance-elasticsearch-jmeter-deploy18.png)

14.  Klicken Sie am unteren Rand des POM-Editors auf die Registerkarte *Dependencies*, und klicken Sie dann neben dem Listenfeld *Dependencies* auf *Add*.

![](./media/guidance-elasticsearch-jmeter-deploy19.png)

15.  Geben Sie im Dialogfeld *Select Dependency* im Feld *Group Id* den Text *org.elasticsearch*, im Feld *Artifact Id* den Text *elasticsearch* und im Feld *Version* den Text *\\${es.version}* ein, und klicken Sie dann auf *OK*. Informationen über die Java-Elasticsearch-Clientbibliothek werden im zentralen Maven-Onlinerepository gespeichert, und diese Konfiguration lädt bei der Erstellung des Projekts automatisch die Bibliothek und ihre Abhängigkeiten herunter.

![](./media/guidance-elasticsearch-jmeter-deploy20.png)

16.  Klicken Sie im Menü *File* auf *Save All*. Durch diese Aktion speichern und erstellen Sie das Projekt, und die von Maven angegebenen Abhängigkeiten werden heruntergeladen. Überprüfen Sie, ob der Ordner *Maven Dependencies* im Package Explorer angezeigt wird. Erweitern Sie diesen Ordner, um die JAR-Dateien anzuzeigen, die zur Unterstützung der Elasticsearch-Java-Clientbibliothek heruntergeladen wurden.

![](./media/guidance-elasticsearch-jmeter-deploy21.png)


## Importieren eines vorhandenen JUnit-Testprojekts in Eclipse

> [AZURE.NOTE] Dieses Verfahren setzt voraus, dass Sie ein Maven-Projekt heruntergeladen haben, das zuvor mithilfe von Eclipse erstellt wurde.

1.  Starten Sie die Eclipse-IDE.

2.  Klicken im Menü *File* auf *Import*.

![](./media/guidance-elasticsearch-jmeter-deploy22.png)

3.  Erweitern Sie im Fenster *Select* den Ordner *Maven*, klicken Sie auf *Existing Maven Projects*, und klicken Sie dann auf *Next*.

![](./media/guidance-elasticsearch-jmeter-deploy23.png)

4.  Geben Sie im Fenster *Maven Projects* den Ordner an, in dem das Projekt enthalten ist (der Ordner mit der Datei „pom.xml“), klicken Sie auf *Select All*, und klicken Sie dann auf *Finish*.

![](./media/guidance-elasticsearch-jmeter-deploy24.png)

5.  Erweitern Sie im Fenster *Package Explorer* den Knoten, der Ihrem Projekt entspricht. Stellen Sie sicher, dass das Projekt einen Ordner namens *src* enthält. Dieser Ordner enthält den Quellcode für den JUnit-Test. Das Projekt kann gemäß den Anweisungen im Verfahren [Bereitstellen eines JUnit-Tests für JMeter](#deploying-a-junit-test-to-jmeter) kompiliert und bereitgestellt werden.

![](./media/guidance-elasticsearch-jmeter-deploy25.png)

## Bereitstellen eines JUnit-Tests für JMeter

> [AZURE.NOTE] In diesem Projekt wird davon ausgegangen, dass Sie ein Projekt namens „LoadTest“ erstellt haben. Dieses enthält eine JUnit-Testklasse namens „BulkLoadTest.java“, die Konfigurationsparameter akzeptiert, die als eine einzelne Zeichenfolge an einen Konstruktor übergeben werden. (Dieser Mechanismus wir von JMeter erwartet.)

1.  Klicken Sie in der Eclipse-IDE im Package Explorer mit der rechten Maustaste auf den Projektknoten, und klicken Sie dann auf *Export*.

![](./media/guidance-elasticsearch-jmeter-deploy26.png)

2.  Erweitern Sie im *Export-Assistenten* auf der Seite *Select* den Knoten *Java*, klicken Sie auf *JAR file*, und klicken Sie dann auf *Next*.

![](./media/guidance-elasticsearch-jmeter-deploy27.png)

3.  Erweitern Sie auf der Seite *JAR File Specification* im Feld *Select the resources to export* das Projekt, das exportiert wird, und deaktivieren Sie alle Optionen außer dem Ordner *src*, deaktivieren Sie *.classpath*, *.project* und *pom.xml*. Geben Sie im Feld *JAR file* einen Dateinamen und einen Speicherort für die JAR-Datei an (einschließlich der JAR-Dateierweiterung), und klicken Sie dann auf *Finish*.

![](./media/guidance-elasticsearch-jmeter-deploy28.png)

4.  Kopieren Sie mithilfe von Windows-Explorer die soeben erstellte JAR-Datei in die JMeter-Master-VM, und speichern Sie sie im Ordner *apache-jmeter-2.13\\lib\\junit* unterhalb des Ordners, in dem Sie JMeter installiert haben. (Informationen hierzu finden Sie im Verfahren [Installieren und Konfigurieren von JMeter auf der JMeter-Master-VM](#_Installing_and_Configuring).)

5.  Kehren Sie zu Eclipse zurück, erweitern Sie das Package Explorer-Fenster, und notieren Sie sich alle JAR-Dateien und deren Speicherorte, die im Ordner *Maven Dependencies* für das Projekt aufgelistet sind. Beachten Sie, dass die in der folgenden Abbildung gezeigten Dateien je nach verwendeter Version von Elasticsearch abweichen können:

![](./media/guidance-elasticsearch-jmeter-deploy29.png)

6.  Kopieren Sie mithilfe des Windows-Explorers jede JAR-Datei, auf die im Ordner mit den Maven-Abhängigkeiten verwiesen wird, in den Ordner *apache-jmeter-2.13\\lib\\junit* auf der JMeter-Master-VM.

> [AZURE.NOTE] Wenn der Ordner „ l*Ib\\junit* “ bereits ältere Versionen dieser JAR-Dateien enthält, entfernen Sie sie. Wenn Sie diese am selben Ort belassen, funktioniert der JUnit-Test möglicherweise nicht, weil Verweise in die falschen JARs aufgelöst werden könnten.

7.  Beenden Sie JMeter auf der JMeter-Master-VM, wenn es derzeit ausgeführt wird.

8.  Starten Sie JMeter.

9.  Klicken Sie in JMeter mit der rechten Maustaste auf *Test Plan*, klicken Sie auf *Add*, klicken Sie auf *Threads (Users)*, und klicken Sie dann auf *Thread Group*.

![](./media/guidance-elasticsearch-jmeter-deploy30.png)

10.  Klicken Sie mit der rechten Maustaste im Knoten *Test Plan* auf *Thread-Group*, klicken Sie auf *Add*, klicken Sie auf *Sampler*, und klicken Sie dann auf *JUnit Request*.

![](./media/guidance-elasticsearch-jmeter-deploy31.png)

11.  Wählen Sie auf der Seite *JUnit Request* die Option *Search for JUnit4 annotations (instead of JUnit 3)* aus. Wählen Sie im Dropdown-Listenfeld *Classname* Ihre JUnit-Belastungstestklasse aus (die im Format *&lt;Paket&gt;.&lt;Klasse&gt;* aufgeführt ist), wählen Sie im Dropdown-Listenfeld *Test Method* die JUnit-Testmethode aus (dies ist die Methode, die die dem Test zugeordnete Arbeit tatsächlich durchführt und die im Eclipse-Projekt mit der Anmerkung *@test* markiert sein sollte), und geben Sie Werte ein, die an den Konstruktor im Feld *Constructor String Label* übergeben werden sollen. (Die in der folgenden Abbildung gezeigten Details sind nur Beispiele; Ihre Angaben für *Classname*, *Test Method* und *Constructor String Label* unterscheiden sich wahrscheinlich von den gezeigten Werten).

![](./media/guidance-elasticsearch-jmeter-deploy32.png)

> [AZURE.NOTE]  Wenn Ihre Klasse nicht im Dropdown-Listenfeld *Classname* angezeigt wird, bedeutet dies wahrscheinlich, dass die JAR-Datei nicht ordnungsgemäß exportiert wurde oder sich nicht im Ordner *lib\\junit* befindet oder dass einige der abhängigen JAR-Dateien im Ordner *lib\\junit* fehlen. In diesem Fall exportieren Sie das Projekt erneut aus Eclipse, und stellen Sie sicher, dass Sie die Ressource *src* ausgewählt haben. Kopieren Sie die JAR-Datei in den Ordner *lib\\junit*, und stellen Sie sicher, dass Sie alle von Maven aufgeführten abhängigen JAR-Dateien in den Ordner *lib* kopiert haben.

12.  Schließen Sie JMeter. Es ist nicht erforderlich, den Testplan zu speichern.

13.  Kopieren Sie die JAR-Datei mit der JUnit-Testklasse in den Ordner */home/&lt;Benutzername&gt;/apache-jmeter-2.13/lib/junit* auf jeder der untergeordneten JMeter-VMs. *&lt;Benutzername&gt;* ist der Name des Administrators, den Sie beim Erstellen des virtuellen Computers angegeben haben. Weitere Informationen finden Sie unter [Erstellen der untergeordneten virtuellen JMeter-Computer](#_Creating_the_JMeter_2).

14.  Kopieren Sie die abhängigen JAR-Dateien, die für die JUnit-Testklasse erforderlich sind, in den Ordner */home/&lt;Benutzername&gt;/apache-jmeter-2.13/lib/junit* auf jedem untergeordneten virtuellen JMeter-Computer. Entfernen Sie zunächst unbedingt alle älteren Versionen der JAR-Dateien aus diesem Ordner.

> [AZURE.NOTE] Mit dem Dienstprogramm pscp können Sie Dateien von einem Windows-Computer nach Ubuntu kopieren.

<!----HONumber=AcomDC_0211_2016-->
