# Einführung in Azure

Azure ist die Anwendungsplattform von Microsoft für die öffentliche Cloud. Sie können diese Plattform auf vielfältige Weise nutzen.

-   Sie können eine Anwendung erstellen, die ihre Daten in Azure-Datencentern speichert.
-   Sie können ausschließlich Daten speichern, wobei die Anwendungen, die diese Daten nutzen, lokal ausgeführt werden (d. h. außerhalb der öffentlichen Cloud).
-   Mit Azure können Sie auch virtuelle Computer zu Entwicklungs- und Testzwecken erstellen oder SharePoint und andere Anwendungen ausführen.
-   Sie können Azure zum Erstellen von hochgradig skalierbaren Anwendungen mit unzähligen Benutzern verwenden.

All dies und noch viel mehr ist möglich, da die Plattform eine Vielzahl an unterschiedlichen Diensten bietet. Für alle diese Aufgaben benötigen Sie jedoch Grundkenntnisse. Auch wenn Sie noch gar nichts über das Cloud Computing wissen, so erhalten Sie mit diesem Artikel einen Einblick in die Grundlagen von Azure. Ziel ist es, Ihnen Grundkenntnisse über die Funktionsweise und die Arbeit mit dieser Cloud-Plattform zu vermitteln.

**Tipps zum Lesen dieses Artikels**

Es gibt sehr viele Dienste in Azure, und es werden ständig mehr, sodass Sie leicht den Überblick verlieren können. Die grundlegenden Dienste sind weiter oben in diesem Artikel aufgeführt. Beginnen Sie zunächst damit. Schauen Sie sich danach die zusätzlichen Dienste an. Das bedeutet nicht, dass Sie diese zusätzlichen Dienste nicht unabhängig verwenden können. Die grundlegenden Dienste bilden jedoch den Kern einer Anwendung, die in Azure ausgeführt wird.

**Geben Sie Feedback**

Dieser Artikel soll Ihnen einen optimalen Überblick über Azure verschaffen. Falls dies Ihrer Meinung nach nicht der Fall ist, teilen Sie uns dies im Kommentarabschnitt unten auf der Seite mit. Erläutern Sie, was Sie erwartet haben und wie wir den Artikel verbessern können. Ihr Feedback ist sehr wichtig.

## Inhaltsverzeichnis

Grundlegende Dienste

-   [Die Komponenten von Azure][]
-   [Verwaltungsportal][]
-   [Compute][]
-   [Datenverwaltung][]
-   [Netzwerk][]
-   [Entwicklerdienste][]

Zusätzliche Dienste

-   [Identität und Zugriff][]
-   [Mobile][]
-   [Sicherung][]
-   [Messaging und Integration][]
-   [Compute-Unterstützung][]
-   [Leistung][]
-   [Big Compute und Big Data][]
-   [Medien][]
-   [Commerce][]
-   [Erste Schritte][]

## <span id="components"></span></a>Die Komponenten von Azure

Azure gruppiert Dienste im Verwaltungsportal unter verschiedenen Kategorien und auf verschiedenen visuellen Hilfsmitteln wie der [Was ist Windows Azure-Infografik][]. Im Verwaltungsportal verwalten Sie die meisten (aber nicht alle) Dienste in Azure. Dieser Artikel **geht anders vor**, denn die Dienste werden nach Funktionsverwandtschaft dargestellt, und es werden wichtige Teildienste hervorgehoben, die zu größeren Diensten gehören.

![Azure components][]
 **Abbildung 1: Azure bietet über das Internet zugängige Anwendungsdienste, die in Azure-Datencentern ausgeführt werden.**

## <span id="portal"></span></a>Verwaltungsportal

Azure verfügt über eine Webschnittstelle, das so genannte Verwaltungsportal, über das Administratoren auf die meisten Azure-Funktionen zugreifen und die meisten Azure-Funktionen verwalten können. Microsoft gibt das Portal mit der neueren Benutzeroberfläche in der Regel als Beta-Version heraus, bevor eine ältere Version zurückgezogen wird. Die neuere Version heißt "Vorschauportal". Die beiden Portale weisen im Allgemeinen einige Überschneidungen auf.

## <span id="compute"></span></a>Compute

Eine der grundlegendsten Aufgaben einer Cloud-Plattform ist die Ausführung von Anwendungen. Azure bietet die folgenden Optionen:

1.  Mit Virtual Machines haben Sie Kontrolle über Ihren eigenen virtuellen Computer einschließlich des Betriebssystems.
2.  Websites bietet verschiedene Anwendungen, Frameworks und Vorlagen, mit deren Hilfe Sie große, skalierbare Webanwendungen und Präsenzwebsites schnell erstellen können, deren Entwicklung, Test und Betrieb dann effizient verwaltet werden kann.
3.  Cloud Services ist eine Platform-as-a-Service (PaaS)-Option, die für das Erstellen hoch skalierbarer und fehlerresistenter Anwendungen optimiert ist, jedoch flexibler ist als Websites.

Jedes Azure-Ausführungsmodell erfüllt seinen eigenen Zweck.

Sie können diese Technologien separat verwenden oder sie wie benötigt miteinander kombinieren, um die richtige Grundlage für Ihre Anwendung zu erstellen. Bei der Wahl des richtigen Ansatzes ist das Problem, das Sie lösen möchten, ausschlaggebend.

### Azure Virtual Machines

![Azure Virtual Machines][]
**Abbildung 2: Azure Virtual Machines bietet Ihnen volle Kontrolle über Instanzen virtueller Computer in der Cloud.**

Die Möglichkeit, bei Bedarf einen virtuellen Computer zu erstellen, sei es anhand eines Standard-Images oder anhand eines von Ihnen bereitgestellten Images, kann sehr nützlich sein. Genau diesen Ansatz, im Allgemeinen als Infrastructure-as-a-Services (IaaS) bezeichnet, bietet Ihnen Azure Virtual Machines. Abbildung 2 zeigt, wie ein virtueller Computer (VM) ausgeführt wird und wie ein solcher anhand einer VHD erstellt wird.

Zum Erstellen eines virtuellen Computers legen Sie die VHD, die verwendet werden soll, sowie die Größe des virtuellen Computers fest. Dann zahlen Sie für die Zeit, die der virtuelle Computer ausgeführt wird. Sie zahlen pro Minute und nur, wenn der virtuelle Computer aktiv ist. Allerdings fällt dafür, dass die VHD verfügbar bleibt, eine minimale Speichergebühr an. Azure bietet eine Galerie an Bestands-VHDs (so genannte "Images"), die ein bootfähiges Betriebssystem enthalten. Hierzu gehören Microsoft- und Partneroptionen wie Windows Server und Linux, SQL Server, Oracle und vieles mehr. Sie haben die Möglichkeit, VHDs und Images zu erstellen und selbst hochzuladen. Sie können sogar VHDs hochladen, die nur Daten enthalten, und über Ihre aktiven virtuellen Computer darauf zugreifen.

Unabhängig vom Ursprung der VHD können Sie alle Änderungen, die beim Ausführen eines virtuellen Computers vorgenommen wurden, dauerhaft speichern. Wenn Sie das nächste Mal einen virtuellen Computer von dieser VHD erstellen, werden die zuletzt verwendeten Einstellungen übernommen. Die VHDs, welche die virtuellen Computer unterstützen, werden in Azure Storage-Blobs gespeichert, über die wir später sprechen werden. Dank dieser Redundanz ist sichergestellt, dass Ihre VMs nicht aufgrund von Hardware- und Datenträgerausfällen verloren gehen. Sie können auch die geänderte VHD aus Azure kopieren und sie dann lokal ausführen.

Ihre Anwendung wird auf einem oder mehreren virtuellen Computern ausgeführt, je nachdem, wie Sie sie erstellt haben bzw. wie Sie sie jetzt neu erstellen möchten.

Dieser eher allgemeine Cloud-Computing-Ansatz kann zur Lösung verschiedener Probleme herangezogen werden.

**Szenarien für virtuelle Computer**

1.  **Dev/Test** – Damit können Sie eine günstige Entwicklungs- und Testplattform erstellen, die Sie nach Abschluss Ihrer Arbeiten wieder außer Betrieb nehmen. Sie können auch Anwendungen erstellen und ausführen, die genau die Sprachen und Bibliotheken nutzen, die Sie benötigen. Diese Anwendungen können jede Datenverwaltungsoption nutzen, die in Azure zur Verfügung steht. Sie können auch SQL Server oder ein anderes DBMS verwenden, das auf einem oder mehreren virtuellen Computern ausgeführt wird.
2.  **Anwendungen nach Azure verschieben (anheben und verschieben)** - Bei "anheben und verschieben" geht es um das Verschieben Ihrer Anwendung. Vergleichen Sie dies mit dem Anheben eines großen Objekts mithilfe eines Gabelstaplers. Sie "heben" die VHD aus dem lokalen Datencenter, "verschieben" sie nach Azure und führen sie dort aus. In der Regel müssen Sie anschließend noch Abhängigkeiten von anderen Systemen entfernen. Sind dies zu viele, können Sie stattdessen Option 3 verwenden.
3.  **Datencenter erweitern** – Verwenden Sie Azure-VMs, auf denen SharePoint oder andere Anwendungen ausgeführt werden, als Erweiterung Ihres lokalen Datencenters. Zur Unterstützung dieser Option können Sie Windows-Domänen in der Cloud erstellen, indem Sie Active Directory auf Azure-VMs ausführen. Sie können Azure Virtual Network (Erläuterungen weiter unten) verwenden, um Ihr lokales Netzwerk und Ihr Netzwerk in Azure zu verbinden.

### Websites

![Azure Websites][]
 **Azure Websites führt eine Website-Anwendung in der Cloud aus, ohne dass der zugrunde liegende Webserver verwaltet werden muss.**

Die Cloud wird am häufigsten dazu verwendet, Websites und Webanwendungen auszuführen. Azure Virtual Machines macht dies möglich; Sie sind jedoch für die Verwaltung der virtuellen Computer und der zugrunde liegenden Betriebssysteme verantwortlich. Die kann zwar mit Cloud Services-Webrollen geschehen, deren Bereitstellung und Verwaltung bedeutet dennoch einen gewissen Aufwand. Was, wenn Sie nur eine Website möchten, für die jemand anderes die Verwaltungsarbeiten übernimmt?

Genau das bietet Ihnen Azure Websites. Dieses Compute-Modell stellt eine verwaltete Webumgebung mit dem Azure-Verwaltungsportal sowie APIs bereit. Sie können eine bestehende Website unverändert in Azure Websites verschieben, oder Sie können direkt in der Cloud eine neue erstellen. Sobald eine Website ausgeführt wird, können Sie Instanzen dynamisch hinzufügen oder entfernen, wobei Azure Websites den Lastenausgleich von Anfragen zwischen den Instanzen übernimmt. Azure Websites bietet eine Option für eine gemeinsame Nutzung, sodass Sie Ihre Website zusammen mit anderen Websites auf einem virtuellen Computer betreiben können, sowie eine Standardoption, mit der Sie eine Website auf einem eigenen virtuellen Computer betreiben können. Mit der Standardoption können Sie bei Bedarf auch die Größe (Rechenleistung) Ihrer Instanzen ändern.

Bei der Entwicklung unterstützt Azure Websites .NET, PHP, Node.js und Python sowie SQL-Datenbank und MySQL (von ClearDB, einem Microsoft-Partner) für relationalen Speicher. Es bietet auch integrierte Unterstützung für mehrere beliebte Anwendungen wie WordPress, Joomla und Drupal. Ziel ist es, eine kostengünstige, skalierbare und vielseitig nutzbare Plattform zum Erstellen von Websites und Webanwendungen in der öffentlichen Cloud bereitzustellen.

**Website-Szenarien**

Azure Websites wurde für den Einsatz durch Unternehmen, Entwickler und Webdesign-Agenturen konzipiert. Für Unternehmen bietet es eine einfach zu verwaltende, skalierbare, sehr sichere und hoch verfügbare Lösung für den Betrieb von Präsenzwebsites. Wenn Sie eine Website einrichten müssen, beginnen Sie am sinnvollsten zuerst mit Azure Websites und gehen dann zu Cloud Services über, wenn Sie eine Option benötigen, die in Azure Websites nicht enthalten ist. Weitere Links, die Ihnen bei der Wahl der geeigneten Option helfen können, finden Sie am Ende des Abschnitts "Compute".

### Cloud Services

![Azure Cloud Service][]
**Azure Cloud Services ermöglicht die Ausführung von hoch skalierbarem benutzerdefinierten Code in einer Platform-as-a-Service (PaaS)-Umgebung.**

Angenommen Sie möchten eine Cloud-Anwendung erstellen, die viele parallel angemeldete Benutzer unterstützt, die keines riesigen Verwaltungsaufwands bedarf und die niemals ausfällt. Sie könnten beispielsweise ein erfahrener Softwareanbieter sein, der sich für die Implementierung von Software-as-a-Service (SaaS) entschieden hat, indem er eine Version von einer Ihrer Anwendungen in der Cloud erstellt. Oder Sie sind ein Startup-Unternehmen, das eine Benutzeranwendung erstellt, von der Sie erwarten, dass sie schnell wachsen wird. Welches Ausführungsmodell sollten Sie verwenden, wenn Sie Azure als Grundlage nutzen?

Mit Azure Websites können Sie eine derartige Webanwendung erstellen, es gibt jedoch einige Einschränkungen. Sie haben zum Beispiel keinen Administratorzugriff, das heißt, Sie können nicht eigenmächtig Software installieren. Azure Virtual Machines bietet Ihnen eine große Flexibilität, darunter auch Administratorzugriff, und es ist auf jeden Fall geeignet, um eine hoch skalierbare Anwendung zu erstellen. Sie sind jedoch für viele Aspekte im Hinblick auf Zuverlässigkeit und Verwaltung selbst verantwortlich. Sie brauchen eine Option, die Ihnen die gewünschte Kontrolle bietet, die jedoch auch den Großteil der im Hinblick auf Zuverlässigkeit und Verwaltung erforderlichen Arbeiten übernimmt.

Und genau hier kommen die Azure-Clouddienste ins Spiel. Diese Technologie ist darauf ausgelegt, skalierbare, zuverlässige Anwendungen mit geringem Verwaltungsaufwand zu unterstützen. Zugleich ist sie ein Beispiel für die gemeinhin als Platform-as-a-Service (PaaS) bezeichnete Technologie. Zur Nutzung erstellen Sie eine Anwendung mit der von Ihnen gewählten Technologie, wie C\#, Java, PHP, Python, Node.js usw. Der Code wird dann auf virtuellen Computern ausgeführt, die als Instanzen bezeichnet werden. Dazu muss auf dem virtuellen Computer eine Version von Windows Server laufen.

Diese virtuellen Computer unterscheiden sich jedoch von jenen, die Sie mit Azure Virtual Machines erstellen. Zum einen werden sie von Azure selbst verwaltet, sprich, es werden Patches für das Betriebssystem installiert und neue Images mit Patches werden automatisch implementiert. Das bedeutet, dass Ihre Anwendung den Status in Web- oder Workerrolleninstanzen nicht verwaltet. Wenden Sie stattdessen eine der im nächsten Abschnitt beschriebenen Optionen der Azure-Datenverwaltung dauerhaft auf sie an. Azure überwacht auch diese virtuellen Computer und startet jeden fehlgeschlagenen virtuellen Computer neu. Sie können festlegen, dass Cloud Services je nach Bedarf automatisch mehr oder weniger Instanzen erstellt. So können Sie die vermehrte Nutzung bewältigen und die Anzahl der Instanz anschließend wieder verringern, damit Sie bei geringerer Nutzung weniger bezahlen müssen.

Beim Erstellen einer Instanz können Sie zwischen zwei Rollen wählen, die beide auf Windows Server basieren. Der Hauptunterschied zwischen den beiden besteht darin, dass für eine Instanz einer Webrolle IIS ausgeführt wird, während dies bei der Instanz einer Workerrolle nicht der Fall ist. Beide werden jedoch gleichermaßen verwaltet, und häufig verwendet eine Anwendung beide. Eine Webrolleninstanz akzeptiert beispielsweise Anfragen von Benutzern und leitet sie dann zur Verarbeitung an eine Workerrolleninstanz. Zum Skalieren Ihrer Anwendung können Sie anfordern, dass Azure weitere Instanzen von einer der beiden Rollen erstellt oder bestehende einstellt. Ähnlich wie bei Azure Virtual Machines werden Ihnen nur Gebühren für die Zeit in Rechnung gestellt, in der die Web- oder Workerrolleninstanzen auch tatsächlich ausgeführt wurden.

**Cloud Services-Szenarien**

Cloud Services eignen sich ideal für Skalierungen enormen Umfangs, wenn Sie mehr Kontrolle über die Plattform benötigen, als mit Azure Websites möglich ist, aber keine Kontrolle des zugrunde liegenden Betriebssystems erforderlich ist.

#### Wählen eines Compute-Modells

Die Seite "Azure Websites, Cloud Services and Virtual Machines comparison" (Vergleich von Azure Websites, Cloud Services und Virtual Machines, in englischer Sprache) (<http://azure.microsoft.com/en-us/documentation/articles/choose-web-site-cloud-service-vm/>) enthält ausführlichere Informationen zur Auswahl eines Compute-Modells.

## <span id="data"></span></a>Datenverwaltung

Anwendungen brauchen Daten, und unterschiedliche Arten von Anwendungen benötigen unterschiedliche Arten von Daten. Daher bietet Azure verschiedene Wege, um Daten zu speichern und zu verwalten. Azure bietet viele Speicheroptionen, alle sind jedoch als sehr dauerhafte Speicher ausgelegt. Bei jeder dieser Optionen werden stets drei Kopien Ihrer Daten in einem Azure-Datencenter synchronisiert – sechs, wenn Sie in Azure die Georedundanz aktivieren, um Sicherungen in einem anderen mindestens 480 Kilometer entfernten Datencenter zu speichern.

### Auf virtuellen Computern

Die Möglichkeit, SQL Server oder eine andere DBMS auf einem virtuellen Computer auszuführen, der mit Azure Virtual Machines erstellt wurde, wurde bereits erwähnt. Denken Sie daran, dass diese Option nicht auf relationale Systeme beschränkt ist: Sie können auch NoSQL-Technologien wie MongoDB und Cassandra ausführen. Das Betreiben eines eigenen Datenbanksystems ist unkompliziert: Im Grunde werden die aus unseren eigenen Datencentern vertrauten Systeme repliziert, aber darüber hinaus muss dieses DBMS verwaltet werden. Bei anderen Optionen übernimmt Azure die Verwaltung zum größten Teil oder vollständig.

Der Status des virtuellen Computers und zusätzlicher Datenträger, die Sie erstellen oder hochladen, werden wiederum vom Blob-Speicher (über den wir später sprechen werden) unterstützt.

### Azure SQL-Datenbank

![Azure Storage SQL Database][]
**Azure SQL-Datenbank stellt einen verwalteten relationalen Datenbankdienst in der Cloud zur Verfügung.**

Für relationalen Speicher stellt Azure die Option SQL-Datenbank bereit. Lassen Sie sich von dem Namen nicht irreführen. Hierbei handelt es sich um etwas anderes als eine typische SQL-Datenbank, die von SQL Server unter Windows Server bereitgestellt wird.

Früher unter dem Namen SQL Azure bekannt, bietet Azure SQL-Datenbank alle wichtigen Funktionen eines Verwaltungssystems für relationale Datenbanken, darunter atomare Transaktionen, paralleler Datenzugriff von mehreren Benutzern mit Datenintegrität, ANSI SQL-Abfragen und ein bekanntes Programmierungsmodell. Sie können mithilfe des Entity Framework, ADO.NET, JDBC und anderer bekannter Datenzugriffstechnologien auf SQL-Datenbank zugreifen, wie dies auch bei SQL Server der Fall ist. Unterstützt werden auch der Großteil der T-SQL-Sprache sowie SQL Server-Tools wie SQL Server Management Studio. Für Benutzer, die mit SQL Server (oder einer anderen relationalen Datenbank) vertraut sind, ist die Nutzung von SQL-Datenbank schnell erlernbar.

SQL-Datenbank ist jedoch nicht nur ein DBMS in der Cloud, es handelt sich um einen PaaS. Sie kontrollieren weiterhin Ihre Daten und steuern den Zugriff, aber SQL-Datenbank übernimmt die Routinearbeiten bei der Verwaltung, z. B. die Verwaltung der Hardwareinfrastruktur und die automatische Aktualisierung der Datenbank und des Betriebssystems. SQL-Datenbank bietet außerdem hohe Verfügbarkeit, automatische Sicherungen sowie Zeitpunktwiederherstellungen und kann Kopien in geografischen Regionen replizieren.

Darüber hinaus gibt es eine Premium-Option, für die Sie ein wenig mehr zahlen, damit Sie über Ihren eigenen dedizierten Server verfügen. Mit der Standardoption wird die Datenbank auf gemeinsam genutzter Hardware ausgeführt. Dabei können die Datenbankabfragen gedrosselt werden, wenn Sie zufällig einen besonders stark beanspruchten Server nutzen.

**Szenarien für SQL-Datenbank**

Wenn Sie eine Azure-Anwendung (mit einem der Compute-Modelle) erstellen, die einen relationalen Speicher benötigt, ist SQL-Datenbank u. U. eine gute Wahl. Anwendungen, die außerhalb der Cloud ausgeführt werden, können diesen Dienst jedoch ebenfalls nutzen, sodass es viele andere Szenarios gibt. So können Sie von verschiedenen Clientsystemen aus, darunter Desktops, Laptops, Tablets und Telefone, beispielsweise auf in SQL-Datenbank gespeicherte Daten zugreifen. Und da dank der Replizierung eine integrierte Hochverfügbarkeit bereitgestellt wird, kann der Einsatz von SQL-Datenbank dabei helfen, die Downtime zu minimieren.

### Tabellen

![Azure Storage Tables][]
**Azure Tables ist ein flaches NoSQL-Verfahren zum Speichern von Daten.**

Diese Funktion wird gelegentlich anders bezeichnet, da sie Teil einer umfangreicheren Funktion namens "Azure Storage" ist. Wenn Sie "Tables", "Azure Tables" oder "Storage Tables" lesen handelt es sich jeweils um die gleiche Sache.

Lassen Sie sich vom Namen jedoch nicht täuschen: Diese Technologie bietet keinen relationalen Speicher. Tatsächlich handelt es sich um ein Beispiel für einen NoSQL-Ansatz, einen so genannten Schlüsselwertspeicher. Azure Tables ermöglicht es einer Anwendung, Eigenschaften unterschiedlicher Art, z. B. Zeichenfolgen, Ganzzahlen und Daten, zu speichern. Eine Anwendung kann dann eine Gruppe von Eigenschaften abrufen, indem ein eindeutiger Schlüssel für diese Gruppe angegeben wird. Während komplexe Vorgänge wie Verknüpfungen nicht unterstützt werden, bieten Tabellen einen schnellen Zugriff auf typisierte Daten. Sie sind auch hoch skalierbar, da eine einzelne Tabelle bis zu einem Terabyte an Daten umfassen kann. Neben der Einfachheit sind Tabellen in der Regel auch kostengünstiger als relationale Speicher von SQL-Datenbank.

**Szenarien für Tables**

Angenommen Sie möchten eine Azure-Anwendung erstellen, die schnellen Zugriff auf typisierte Daten benötigt, womöglich sogar auf viele Daten, die jedoch für diese Daten keine komplexen SQL-Abfragen durchführen muss. Beispiel: Sie möchten eine Benutzeranwendung erstellen, die für jeden Benutzer Kundenprofilinformationen speichern muss. Ihre App wird sehr beliebt sein, sodass Sie eine große Datenmenge einkalkulieren müssen. Doch außer der Speicherung und dem einfachen Abrufen geschieht nicht viel mit diesen Daten. Genau bei solch einem Szenario ergibt der Einsatz von Azure Tables einen Sinn.

### Blobs

![Azure Storage Blobs][]
**Abbildung: Azure-Blobs bietet unstrukturierte binäre Daten.**

Azure-Blobs ("Blob-Speicher" und "Speicher-Blobs" bezeichnen die gleiche Sache) ist für das Speichern unstrukturierter binärer Daten vorgesehen. Wie Tabellen stellen Blobs einen kostengünstigen Speicher dar, wobei ein einzelner Blob bis zu ein Terabyte groß sein kann. Azure-Anwendungen können auch Azure-Laufwerke nutzen, sodass Blobs einen beständigen Speicher für ein in einer Azure-Instanz gemountetes Windows-Dateisystem bieten können. In der Anwendung sind herkömmliche Windows-Dateien sichtbar, doch der Inhalt wird tatsächlich in einem Blob gespeichert.

Blob-Speicher wird von vielen weiteren Azure-Funktionen (einschließlich Virtual Machines) verwendet, sodass er ohne weiteres auch Ihre Arbeitsauslastungen verarbeiten kann.

**Szenarien für Blobs**

Eine Anwendung, in der z. B. Videos, sehr große Dateien oder andere Binärinformationen gespeichert werden, kann Blobs als einfachen, kostengünstigen Speicher nutzen. Blobs werden häufig auch in Verbindung mit anderen Diensten wie Content Delivery Network verwendet, die wir später behandeln werden.

### Import/Export

![Azure Import Export Service][]
**Abbildung: Azure Import/Export bietet die Möglichkeit, eine physische Festplatte an oder von Azure zu schicken, um große Datenmengen schneller und billiger zu importieren oder zu exportieren.**

Gelegentlich möchten Sie große Datenmengen nach Azure verschieben. Dies kann viel Zeit, eventuell Tage, in Anspruch nehmen und sehr viel Bandbreite belegen. In diesen Fällen können Sie Azure Import/Export verwenden. Damit können Sie Bitlocker-verschlüsselte 3,5"-SATA-Festplattenlaufwerke direkt in Azure-Datencenter verschieben, wo Microsoft die Daten für Sie in einen Blob-Speicher überträgt. Nach dem Upload schickt Microsoft die Laufwerke an Sie zurück. Sie können auch veranlassen, dass große Datenmengen aus dem Blob-Speicher auf Festplattenlaufwerke exportiert und Ihnen per Post zugesandt werden.

**Szenarien für Import/Export**

-   **Migration großer Datenmengen** – Wenn Sie große Datenmengen (Terabyte) nach Azure hochladen möchten, ist der Import-/Export-Dienst häufig schneller und billiger als eine Übertragung über das Internet. Wenn sich die Daten Blobs befinden, können Sie sie in andere Formate wie Tabellenspeicher oder eine SQL-Datenbank konvertieren.

-   **Wiederherstellung archivierter Daten** – Mit Import/Export können Sie große Datenmengen, die in Azure Blob Storage gespeichert sind, von Microsoft auf ein Speichergerät übertragen lassen, das Sie schicken, und dieses Gerät wieder an den gewünschten Ort zurückschicken lassen. Da dies eine gewisse Zeit dauert, ist dies keine gute Option für die Notfallwiederherstellung. Am sinnvollsten eignet es sich für archivierte Daten, auf die Sie nicht schnell zugreifen müssen.

### Azure-Dateidienst

![Azure File Service][]
**Abbildung: Azure-Dateidienste stellt \\\\Server\\share-Pfade mit dem SMB-Protokoll für in der Cloud ausgeführte Anwendungen bereit.**

Lokal stehen häufig große Mengen an Dateispeicher zur Verfügung, auf die über das Server Message Block (SMB)-Protokoll unter Verwendung eines \\\\Server\\share-Formats zugegriffen wird. Azure verfügt jetzt über einen Dienst, mit dem Sie dieses Protokoll in der Cloud verwenden können. Anwendungen, die in Azure ausgeführt werden, können damit über bekannte Dateisystem-APIs wie ReadFile und WriteFile Dateien zwischen virtuellen Computern freigeben. Darüber hinaus kann auf die Dateien gleichzeitig über eine REST-Schnittstelle zugegriffen werden. Dies ermöglicht den Zugriff auf die Freigaben über Ihr lokales System, wenn Sie auch ein virtuelles Netzwerk einrichten. Azure-Dateidienste setzt auf den Blob-Dienst auf und verhält sich daher in Bezug auf Verfügbarkeit, Stabilität, Skalierbarkeit und Georedundanz ebenso wie Azure Storage.

**Szenarien für Azure-Dateidienste**

-   **Migrieren vorhandener Apps in die Cloud** - Es ist einfacher, lokale Anwendungen, die Dateifreigaben verwenden, in die Cloud zu migrieren, um Daten in verschiedenen Teilen der Anwendung gemeinsam zu nutzen. Jeder virtuelle Computer stellt eine Verbindung mit der Dateifreigabe her und kann Dateien anschließend genauso lesen und schreiben wie bei einer Verbindung mit einer lokalen Dateifreigabe.

-   **Gemeinsame Anwendungseinstellungen** - Bei verteilten Anwendungen befinden sich die Konfigurationsdateien häufig an einem zentralen Ort, wo viele verschiedene virtuelle Computer auf sie zugreifen können. Diese Konfigurationsdateien können in einer Azure-Dateifreigabe gespeichert und von allen Anwendungsinstanzen gelesen werden. Die Einstellungen können auch über die REST-Schnittstelle verwaltet werden, sodass ein weltweiter Zugriff auf die Konfigurationsdateien möglich ist.

-   **Diagnosefreigabe** - Sie können Diagnosedateien wie Protokolle, Metriken und Absturzabbilder speichern und freigeben. Wenn diese Dateien sowohl über die SMB- als auch über die REST-Schnittstelle zur Verfügung stehen, können Anwendungen die Diagnosedaten mit verschiedenen Analysetools verarbeiten und analysieren.

-   **Entwickeln/Testen/Debuggen** - Wenn Entwickler oder Administratoren virtuelle Computer in der Cloud bearbeiten, benötigen sie häufig verschiedene Tools oder Dienstprogramme. Installation und Verteilung dieser Dienstprogramme auf jedem virtuellen Computer sind zeitaufwändig. Mit der Azure-Dateifreigabe können Entwickler oder Administrator ihre Liebingstools in einer Dateifreigabe speichern und über jeden virtuellen Computer eine Verbindung damit herstellen.

## <span id="networking"></span></a>Netzwerk

Azure wird heute in vielen Datencentern überall auf der Welt ausgeführt. Wenn Sie eine Anwendung ausführen oder Daten speichern, können Sie ein oder mehrere dieser Datencenter auswählen. Mithilfe der unten genannten Dienste können Sie auch auf verschiedene Weise eine Verbindung mit diesen Datencentern herstellen.

### Virtuelles Netzwerk

![VirtualNetwork][]
 **Abbildung: Virtuelle Netzwerke stellen in der Cloud ein privates Netzwerk zur Verfügung, sodass verschiedene Dienste miteinander oder mit lokalen Ressourcen kommunizieren können, wenn Sie eine VPN-Verbindung oder eine standortübergreifende Verbindung einrichten.**

Ein nützlicher Einsatzbereich für eine öffentliche Cloud ist die Erweiterung Ihres eigenen Datencenters.

Da Sie virtuelle Computer bei und nach Bedarf erstellen können, die Sie dann auch wieder entfernen können (womit auch keine weiteren Kosten mehr anfallen), wenn sie nicht mehr benötigt werden, steht Ihnen genau die Rechenleistung zur Verfügung, die Sie benötigen. Und da Sie mit Azure Virtual Machines virtuelle Computer erstellen können, auf denen SharePoint, Active Directory oder andere bekannte lokale Softwareanwendungen ausgeführt werden, ist dieser Ansatz auch für bereits vorhandene Anwendungen geeignet.

Um den größtmöglichen Nutzen zu erreichen, sollten Ihre Benutzer jedoch in der Lage sein, diese Anwendungen so zu verwenden, als würden sie in Ihrem eigenen Datencenter ausgeführt werden. Genau das ermöglicht Azure Virtual Network. Mithilfe eines VPN-Gateways kann ein Administrator ein virtuelles privates Netzwerk (VPN) zwischen Ihrem lokalen Netzwerk und Ihren virtuellen Computern einrichten, die in Azure in einem virtuellen Netzwerk bereitgestellt werden. Da Sie den Cloud-VMs Ihre eigenen IPv4-Adressen zuweisen, scheinen sie sich in Ihrem Netzwerk zu befinden. Benutzer in Ihrer Organisation können auf die Anwendungen auf diesen virtuellen Computern zugreifen, ganz so als würden sie lokal ausgeführt.

Weitere Informationen über das Planen und Erstellen eines auf Ihren Bedarf zugeschnittenen virtuellen Netzwerks finden Sie unter [Virtuelles Netzwerk][].

### ExpressRoute

![ExpressRoute][]
 **Abbildung: ExpressRoute verwendet ein virtuelles Azure-Netzwerk, verwendet für Verbindungen jedoch schnellere dedizierte Leitungen anstelle des öffentlichen Internets.**

Wenn Sie mehr Bandbreite oder Sicherheit benötigen, als eine Azure Virtual Network-Verbindung bieten kann, können Sie auf ExpressRoute zurückgreifen. In bestimmten Fällen können Sie mit ExpressRoute sogar Geld sparen. Sie benötigen zwar weiterhin ein virtuelles Netzwerk in Azure, zwischen Azure und Ihrem Standort wird jedoch eine dedizierte Verbindung eingerichtet, die das öffentliche Internet umgeht. Um diesen Dienst nutzen zu können, benötigen Sie einen Vertrag mit einem Netzwerk-Dienstanbieter oder einem Exchange-Anbieter.

Das Einrichten einer ExpressRoute-Verbindung erfordert mehr Zeit und Planung, daher sollten Sie mit einem VPN zwischen verschiedenen Standorten beginnen und danach zu einer ExpressRoute-Verbindung übergehen.

Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – Technische Übersicht][].

### Traffic Manager

![TrafficManager][]
 **Abbildung: Mit Azure Traffic Manager können Sie globalen Datenverkehr mithilfe intelligenter Regeln zu Ihrem Dienst lenken.**

Wenn Ihre Azure-Anwendung in mehreren Datencentern ausgeführt wird, können Sie den Azure Traffic Manager verwenden, um Anfragen von Benutzern intelligent zwischen Instanzen der Anwendung zu leiten. Sie können Datenverkehr auch zu Diensten lenken, die nicht in Azure ausgeführt werden, sofern sie über das Internet zugänglich sind.

Eine Azure-Anwendung mit Benutzern an nur einem geografischen Standort wird womöglich nur in einem Azure-Datencenter ausgeführt. Eine Anwendung mit auf der ganzen Welt verteilten Benutzern wird jedoch sehr wahrscheinlich in verschiedenen Datencentern ausgeführt, womöglich sogar in allen. Aus dieser zweiten Situation ergibt sich ein Problem: Wie können Sie Benutzer intelligent den einzelnen Anwendungsinstanzen zuweisen? Das folgende Szenario ist wohl das bekannteste: Jeder Benutzer soll Zugriff auf das Datencenter in seiner Nähe haben, da somit die beste Reaktionszeit sichergestellt wird. Was ist jedoch, wenn diese Anwendungsinstanz ausgelastet oder nicht verfügbar ist? In diesem Fall wäre es gut, wenn die Anfrage automatisch an ein anderes Datencenter geleitet wird. Und genau diese Aufgabe übernimmt Azure Traffic Manager.

Der Verantwortliche für eine Anwendung definiert Regeln, die festlegen, wie Anfragen von Benutzern an Datencenter geleitet werden sollen, und überlässt dann Traffic Manager die Umsetzung dieser Regeln. Benutzer werden beispielsweise normalerweise an das nächste Azure-Datencenter geleitet, werden dann jedoch an ein anderes weitergeleitet, wenn das Standarddatencenter langsamer antwortet als andere Datencenter. Bei global verteilten Anwendungen mit vielen Benutzern ist solch ein integrierter Dienst für derartige Probleme nützlich.

## <span id="DevService"></span></a>Entwicklerdienste

Azure bietet Entwicklern und IT-Experten verschiedene Tools zum Erstellen und Verwalten von Anwendungen in der Cloud.

### Azure-SDK und Azure-Tools für Visual Studio

Im Jahr 2008 unterstützte die erste Vorabversion von Azure nur die .NET-Entwicklung. Heute können Sie jedoch Azure-Anwendungen in nahezu jeder Sprache erstellen. Microsoft stellt derzeit sprachspezifische SDKs für .NET, Java, PHP, Node.js, Ruby und Python bereit. Es gibt auch ein allgemeines Azure-SDK, das grundlegende Unterstützung für jede Sprache, wie z. B. C++, bietet.

Diese SDKs helfen Ihnen dabei, Azure-Anwendungen zu erstellen, bereitzustellen und zu verwalten. Sie stehen auf www.windowsazure.com oder GitHub zur Verfügung und können mit Visual Studio und Eclipse verwendet werden. Azure bietet auch Befehlszeilentools, die Entwickler mit jedem Editor oder mit jeder Entwicklungsumgebung verwenden können, darunter auch Tools zum Bereitstellen von Anwendungen auf Azure von Linux- und Macintosh-Systemen.

Doch diese SDKs unterstützen Sie nicht nur beim Erstellen von Azure-Anwendungen, sie bieten auch Clientbibliotheken, mit denen Sie Software erstellen können, die außerhalb der Cloud ausgeführt wird und die Azure-Dienste nutzt. So können Sie beispielsweise eine Anwendung erstellen, die bei einem Host ausgeführt wird, der Azure-Blobs nutzt. Oder Sie erstellen ein Tool, das Azure-Anwendungen über die Azure-Verwaltungsschnittstelle bereitstellt.

### Visual Studio Online

Visual Studio Online ist eine Marketingbezeichnung für verschiedene Dienste zum Entwickeln von Anwendungen in Azure.

Um Missverständnisse zu vermeiden, es handelt sich nicht um eine gehostete oder webbasierte Version von Visual Studio. Sie benötigen weiterhin Ihre lokal ausgeführte Kopie von Visual Studio. Es bietet jedoch viele weitere Tools, die sehr hilfreich sein können.

Es umfasst ein gehostetes Quellcodeverwaltungssystem namens Team Foundation Service, das eine Versionskontrolle und die Verfolgung von Arbeitsaufgaben ermöglicht. Sie können sogar Git für die Versionskontrolle verwenden, wenn Sie dies vorziehen. Und Sie können das Quellcodeverwaltungssystem je nach Projekt variieren. Sie haben die Möglichkeit, unbegrenzt viele private Teamprojekte zu erstellen, auf die Sie von jedem Ort der Welt zugreifen können.

Visual Studio Online verfügt über einen Auslastungstestdienst. Sie können Auslastungstests ausführen, die in Visual Studio auf virtuellen Computern in der Cloud erstellt wurden. Sie müssen die Gesamtzahl der Benutzer angeben, mit der Sie den Auslastungstest ausführen möchten. Visual Studio Online ermittelt dann automatisch, wie viele Agenten benötigt werden, fährt die erforderlichen virtuellen Computer hoch und führt die Auslastungstest durch. Als MSDN-Abonnent erhalten Sie jeden Monat Tausende kostenloser Benutzerminuten für Auslastungstests.

Visual Studio Online bietet auch einen Dienst namens Application Insights, mit dem Sie Ihre gesamte Anwendung analysieren können. Er bietet statistische Daten zur Leistung und zur Verwendung der Anwendung. Wenn Sie bereits System Center Operations Manager verwenden, kann der Dienst auch eine Verbindung damit herstellen und bei Problemen Alarme auslösen.

Außerdem wird die agile Entwicklung mit Funktionen wie fortlaufenden Integrationsbuilds, Kanban-Karten und virtuellen Teamräumen unterstützt.

**Szenarien für Visual Studio Online**

Visual Studio Online ist eine gute Option für Unternehmen, die weltweit zusammenarbeiten müssen und noch nicht über die dafür notwendige Infrastruktur verfügen. Die Einrichtung ist in wenigen Minuten abgeschlossen. Sie wählen ein Quellcodeverwaltungssystem und beginnen noch am gleichen Tag mit dem Schreiben von Code und dem Erstellen eines Builds. Die Teamtools stellen Möglichkeiten zur Koordination und Zusammenarbeit bereit, während die zusätzlichen Tools die Analyse ermöglichen, die zum raschen Testen und Optimieren Ihrer Anwendung erforderlich ist.

Organisationen hingegen, die bereits über ein lokales System verfügen, können neue Projekte in Visual Studio Online testen, um festzustellen, ob dies effizienter ist.

### Automation

Niemand verschwendet gerne Zeit mit der Wiederholung der immer gleichen manuellen Prozesse. Azure Automation gibt Ihnen die Möglichkeit, Ressourcen in Ihrer Azure-Umgebung zu erstellen, zu überwachen und bereitzustellen.

Azure Automation verwendet "Runbooks", bei denen es sich eigentlich um Windows PowerShell-Workflows (im Unterschied zur normalen PowerShell) handelt. Runbooks sind für die Ausführung ohne Eingriffe seitens des Benutzers vorgesehen. Mit PowerShell-Workflows kann der Status eines Skripts an bestimmten Prüfpunkten gespeichert werden. Bei Auftreten eines Fehlers müssen Sie das Skript dann nicht von Anfang an wiederholen. Sie können es am letzten Prüfpunkt neu starten. Dadurch ersparen Sie sich die Mühe, das Skript so zu schreiben, dass jeder mögliche Fehler behandelt wird.

**Automation-Szenarien**

Azure Automation ist eine gute Option, um manuelle, lange dauernde, fehleranfällige und häufig wiederholte Aufgaben in Azure zu automatisieren.

### API Management

Das Erstellen und Veröffentlichen von APIs (Application Programming Interfaces - Anwendungsprogrammierschnittstellen) im Internet ist ein häufig verwendetes Verfahren, um Anwendungen Dienste bereitzustellen. Wenn diese Dienste wiederkaufsfähig sind (z. B. Wetterdaten), kann eine Organisation Drittanbietern gegen eine Gebühr Zugriff auf diese Dienste gewähren. Wenn Sie weitere Partner aufnehmen, müssen Sie den Zugriff in der Regel optimieren und kontrollieren. Manche Partner benötigen die Daten eventuell auch in einem anderen Format.

Azure API Management erleichtert es Organisationen, APIs für Partner, Mitarbeiter und Drittanbieter-Entwickler sicher und im entsprechenden Umfang zu veröffentlichen. Es stellt einen anderen API-Endpunkt zur Verfügung und fungiert als Proxy, der den tatsächlichen Endpunkt aufruft. Gleichzeitig werden Dienste wie Zwischenspeicherung, Transformation, Drosselung, Zugriffssteuerung und Analyseaggregation bereitgestellt.

**API Management-Szenarien**
Angenommen, Ihr Unternehmen verfügt über verschiedene Geräte, die sämtlich Daten von einem zentralen Dienst abrufen müssen – z. B. eine Spedition mit Geräten in jedem LKW, der unterwegs ist. Das Unternehmen möchte mit Sicherheit ein System einrichten, um seine eigenen LKW zu verfolgen, damit Lieferzeiten zuverlässig prognostiziert und aktualisiert werden können. Es weiß, wie viele LKW vorhanden sind, und kann entsprechend planen. Jeder LKW benötigt ein Gerät, das seine Positions- und Geschwindigkeitsdaten und eventuell weitere Daten an eine zentrale Stelle übermittelt.

Für einen Kunden der Spedition wäre es wahrscheinlich ebenfalls nützlich, diese Positionsdaten abrufen zu können. Der Kunde wüsste dann, wie lang der Transportweg der Produkte ist, wo sie aufgehalten werden, welche Kosten auf bestimmten Strecken anfallen (bei Kombination der Daten mit den Transportkosten). Wenn die Spedition diese Daten bereits aggregiert, würden viele Kunden vielleicht dafür bezahlen. Dann benötigt die Spedition aber auch eine Möglichkeit, den Kunden die Daten zu übermitteln. Wenn sie den Kunden Datenzugriff gewährt, kann sie möglicherweise nicht steuern, wie oft die Daten abgefragt werden. Die Spedition muss dann Regeln dafür aufstellen, wer auf welche Daten zugreifen kann. Alle diese Regeln müssten in die externe API integriert werden. In diesem Fall kann API Management helfen.

## <span id="identity"></span></a>Identität und Zugriff

Bei den meisten Anwendungen findet eine Identitätsbestimmung statt. Eine Anwendung kann anhand der Identität eines Benutzers bestimmen, auf welche Weise mit diesem Benutzer interagiert werden soll. Azure bietet Dienste zur Erfassung der Identität sowie zu deren Integration in Identitätsspeicher, die Sie eventuell bereits verwenden.

### Active Directory

Wie die meisten Verzeichnisdienste speichert Azure Active Directory Informationen über Benutzer und Organisationen, denen sie angehören. Active Directory ermöglicht es Benutzern sich anzumelden und weist ihnen Tokens zu, mit denen ihre Identität in Anwendungen bestätigt wird. Auch die Synchronisierung der Benutzerinformationen mit Windows Server Active Directory, das in Ihrem lokalen Netzwerk ausgeführt wird, ist möglich. Auch wenn die Mechanismen und Datenformate, die von Azure Active Directory verwendet werden, nicht mit denen in Windows Server Active Directory identisch sind, sind die durchgeführten Funktionen sich doch sehr ähnlich.

Es ist wichtig, dass Sie verstehen, dass Azure Active Directory primär für Cloud-Anwendungen konzipiert wurde. Es kann beispielsweise von Anwendungen unter Azure oder auf einer anderen Cloud-Plattform verwendet werden. Es wird auch von Microsoft-eigenen Cloud-Anwendungen wie die von Office 365 verwendet. Wenn Sie Ihr Datencenter jedoch mit Azure Virtual Machines und Azure Virtual Network auf die Cloud erweitern möchten, ist Azure Active Directory nicht die richtige Wahl. Stattdessen sollten Sie Windows Server Active Directory auf Virtual Machines ausführen.

Damit Anwendungen auf die enthaltenen Informationen zugreifen können, bietet Azure Active Directory eine RESTful-API mit dem Namen Azure Active Directory Graph. Mit dieser API können die auf einer beliebigen Plattform ausgeführten Anwendungen auf Verzeichnisobjekte und auf die Beziehungen zwischen ihnen zugreifen. Eine autorisierte Anwendung kann diese API beispielsweise verwenden, um Informationen über einen Benutzer, über Gruppen, denen er angehört, oder um andere Informationen zu erhalten. Anwendungen können auch die Beziehungen zwischen Benutzern (den sogenannten "Social Graph") anzeigen, sodass sie intelligenter mit den Beziehungen zwischen Menschen arbeiten können.

Eine weitere Funktion dieses Dienstes ist die Azure Active Directory-Zugriffssteuerung. Sie erleichtert es Anwendungen, Identitätsinformationen von Facebook, Google, Windows Live ID und anderen beliebten Identitätsanbietern zu akzeptieren. Die Anwendung muss nicht mehr verschiedene Datenformate und Protokolle verstehen, die von diesen Anbietern verwendet werden, vielmehr übersetzt der Zugriffssteuerungsdienst alle Informationen in ein einzelnes allgemeines Format. Die Funktion ermöglicht es der Anwendung auch, Anmeldungen aus einer oder mehreren Active Directory-Domänen zu akzeptieren. So kann beispielsweise der Anbieter einer SaaS-Anwendung den Azure Active Directory-Zugriffssteuerungsdienst verwenden, um Benutzern bei jedem seiner Kunden Single-Sign-On für die Anwendung zu bieten.

Verzeichnisdienste sind eine wichtige Grundlage der lokalen Rechenleistung. Daher ist es nicht überraschend, dass dies auch in der Cloud von großer Bedeutung ist.

### Multi-Factor Authentication

![Azure Multi-Factor Authentication][]
**Abbildung:**
Sicherheit ist immer wichtig. Multi-Factor Authentication (MFA) stellt sicher, dass nur die Benutzer selbst auf ihre Konten zugreifen können. Bei der MFA (auch Two-Factor Authentication oder "2FA" genannt) müssen die Benutzer für Anmeldungen und Transaktionen zwei der drei folgenden Methoden der Identitätsverifizierung verwenden.

-   Etwas, das Sie wissen (normalerweise ein Kennwort)
-   Etwas, das Sie haben (ein vertrautes Gerät, das nicht leicht dupliziert werden kann, wie ein Telefon)
-   Etwas, das Sie sind (biometrisch)

Wenn sich ein Benutzer anmeldet, können Sie festlegen, dass er zur Bestätigung seiner Identität sein Kennwort auch mit einer mobilen App, einem Telefonanruf oder einer Textnachricht kombinieren muss. Azure Active Directory unterstützt standardmäßig nur Kennwörter als Authentifizierungsmethode bei Benutzeranmeldungen. Mithilfe des MFA-SDK können Sie MFA zusammen mit Azure AD oder mit benutzerdefinierten Anwendungen und Verzeichnissen verwenden. Bei Einsatz des Multi-Factor Authentication-Servers können Sie es auch zusammen mit lokalen Anwendungen verwenden.

**MFA-Szenarien**
Anmeldungsschutz für sensible Konten wie Bankanmeldungen und Zugriff auf Quellcode, bei denen ein unbefugtes Eindringen hohe finanzielle Schäden oder Schäden an geistigem Eigentum nach sich ziehen kann.

## <span id="Mobile"></span></a>Mobile

Wenn Sie eine App für ein mobiles Gerät erstellen, kann Azure dabei helfen, Daten in der Cloud zu speichern, Benutzer zu authentifizieren und Pushbenachrichtigungen zu senden, ohne dass Sie viel benutzerdefinierten Code schreiben müssen.

Auch wenn Sie mit Virtual Machines, Cloud Services oder Websites das Backend für eine mobile App erstellen können, so ersparen Ihnen die Dienste von Azure sehr viel Zeit beim Schreiben der zugrunde liegenden Dienstkomponenten.

### Mobile Services

![MobileServices][]
 **Abbildung: Mobile Services stellt Funktionen bereit, die häufig von Anwendungen mit einer Schnittstelle für mobile Geräte benötigt werden.**

Azure Mobile Services enthält zahlreiche nützliche Funktionen, mit deren Hilfe Sie Zeit sparen, wenn Sie ein Backend für eine mobile Anwendung erstellen. Es vereinfacht die Bereitstellung und Verwaltung von Daten, die in einer SQL-Datenbank gespeichert sind. Mit serverseitigem Code können Sie ohne weiteres zusätzliche Datenspeicheroptionen wie Blob-Speicher oder MongoDB verwenden. Mobile Services unterstützt Benachrichtigungen, dennoch können Sie in bestimmten Fällen stattdessen Benachrichtigungshubs verwenden, wie im Anschluss beschrieben wird. Der Dienst verfügt auch über eine REST-API, die Ihre mobile Anwendung aufrufen kann, um die Arbeit auszuführen. Mobile Services bietet auch die Möglichkeit, Benutzer über Microsoft und Active Directory sowie über weitere bekannte Identitätsanbieter wie Facebook, Twitter und Google zu authentifizieren.

Sie können andere Azure-Dienste wie Service Bus und Workerrollen nutzen und Verbindungen mit lokalen Systemen herstellen. Sie können sogar Drittanbieter-Add-Ons aus dem Azure Store (z. B. SendGrid für E-Mails) verwenden, um zusätzliche Funktionen bereitzustellen.

Systemeigene Client-Bibliotheken für Android, iOS, HTML/JavaScript, Windows Phone und Windows Store erleichtern die Entwicklung von Apps auf allen wichtigen Mobilplattformen. Mit einer REST-API können Sie Mobile Services-Daten und Authentifizierungsfunktionen in Apps auf anderen Plattformen verwenden. Ein einzelner mobiler Service kann mehrere Client-Apps stützen, sodass Sie ein konsistentes Benutzererlebnis auf verschiedenen Geräten bieten können.

Da Azure Skalierungen enormen Umfangs bereits unterstützt, bewältigen Sie den Datenverkehr auch bei gestiegener Beliebtheit Ihrer App. Überwachung und Protokollierung helfen bei der Behebung von Problemen und der Verwaltung der Leistung.

### Notification Hubs

![NotificationHubs][]
 **Abbildung: Notification Hubs stellt Funktionen bereit, die häufig von Anwendungen mit einer Schnittstelle für mobile Geräte benötigt werden.**

Während Sie in Azure Mobile Services Code für Benachrichtigungen schreiben können, ist Notification Hubs optimal darauf ausgelegt, Millionen hoch personalisierter Pushbenachrichtigungen innerhalb weniger Minuten zu senden. Sie müssen sich nicht um Einzelheiten wie Mobilfunkbetreiber oder Gerätehersteller kümmern. Mit einem einzigen API-Aufruf können Sie einzelne oder Millionen von Benutzern ansprechen.

Notification Hubs ist auf die Kombination mit jedem beliebigen Backend ausgelegt. Sie können Azure Mobile Services, einen benutzerdefinierten Backend in der Cloud, der auf einem beliebigen Anbieter ausgeführt wird, oder einen lokalen Backend verwenden.

**Notification Hub-Szenarien**
Wenn Sie ein mobiles Spiel schreiben, in dem sich die Spieler abwechseln, müssen Sie Spieler 2 eventuell darüber benachrichtigen, dass Spieler 1 seine Runde beendet hat. Wenn dies schon alles ist, können Sie auch einfach Mobile Services verwenden. Wenn ihr Spiel jedoch 100.000 Teilnehmer hat und Sie jedem ein zeitkritisches kostenloses Angebot senden möchten, ist Notification Hubs die bessere Wahl.

Sie können Millionen Benutzern aktuelle Nachrichten, Sportereignisse und Produktankündigungen mit niedriger Latenz senden. Unternehmen können ihren Mitarbeitern neue zeitkritische Informationen übermitteln, beispielsweise Verkaufstipps, damit die Mitarbeiter nicht ständig ihre E-Mails oder andere Anwendungen überprüfen müssen, um informiert zu bleiben. Darüber hinaus können Sie einmal zu verwendende Kennwörter senden, die für die Multi-Factor Authentication benötigt werden.

## <span id="Backup"></span></a>Sicherung

Jedes Unternehmen muss Daten sichern und wiederherstellen. Mit Azure können Sie Ihre Anwendung in der Cloud oder lokal sichern und wiederherstellen. Azure bietet je nach Art der Sicherung verschiedene Optionen.

### Site Recovery

Mit Azure Site Recovery (früher Hyper-V-Wiederherstellungsmanager) können Sie wichtige Anwendungen schützen, indem Sie die Replikation und Wiederherstellung von Hyper-V-Images standortübergreifend koordinieren. Sie können die Sicherung auf einem sekundären Standort oder auf dem Standort eines Hosters ablegen oder Azure verwenden und die mit dem Erstellen und Verwalten eines sekundären Standorts verknüpften Kosten und Schwierigkeiten vermeiden. Azure verschlüsselt die Daten und die Kommunikation, und Sie haben die Option, die Verschlüsselung auch für gespeicherte Daten zu aktivieren.

Es überwacht ständig den Zustand Ihrer Dienste und automatisiert die geordnete Wiederherstellung von Diensten bei Ausfall eines Standorts im primären Datencenter. Virtuelle Computer können systematisch hochgefahren werden, um den Dienst selbst bei komplexen mehrschichtigen Arbeitsauslastungen schnell wiederherzustellen.

Site Recovery nutzt vorhandene Technologien wie Hyper-V Replica, System Center und SQL Server AlwaysOn.

### Azure Backup

![Azure Backup][]
**Abbildung: Azure Backup sichert Daten von lokalen Windows-Servern in der Cloud.**
Azure Backup sichert Daten von lokalen Servern unter Windows Server in der Cloud. Sie können die Sicherungen direkt mit den Sicherungstools in Windows Server 2012, Windows Server 2012 Essentials oder System Center 2012 - Data Protection Manager verwalten. Sie können aber auch einen speziellen Sicherungsagenten verwenden.

Die Daten sind besser geschützt, da die Sicherungen vor der Übertragung verschlüsselt, verschlüsselt in Azure gespeichert und durch ein Zertifikat geschützt werden, das Sie hochladen. Der Dienst verwendet den gleichen redundanten, hoch verfügbaren Datenschutz wie in Azure Storage. Sie können Dateien und Ordner regelmäßig oder sofort sichern und entweder vollständige oder inkrementelle Sicherungen durchführen. Nachdem die Daten in der Cloud gesichert wurden, können autorisierte Benutzer die Sicherungen ohne weiteres auf jedem beliebigen Server wiederherstellen. Azure Backup bietet auch konfigurierbare Datenaufbewahrungsrichtlinien, Datenkomprimierung und Datenübertragungsdrosselung, sodass Sie die Kosten für die Speicherung und Übertragung der Daten steuern können.

**Szenarien für Azure Backup**

Wenn Sie bereits Windows Server oder System Center verwenden, ist Azure Backup die sich anbietende Lösung zum Sichern des Dateisystems Ihrer Server, Ihrer virtuellen Computer und SQL Server-Datenbanken. Es arbeitet mit verschlüsselten, wenigen und komprimierten Dateien. Da bestimmte Beschränkungen gelten, sollten Sie zunächst die [Voraussetzungen für Azure Backup prüfen][].

## <span id="messaging"></span></a>Messaging und Integration

Unabhängig von der Funktion eines Codes muss eine häufige Interaktion mit anderem Code stattfinden. In manchen Situationen reichen einfache Nachrichten in Warteschlangen aus. In anderen Fällen sind jedoch komplexe Interaktionen erforderlich. Azure bietet verschiedene Methoden zur Lösung dieser Probleme. Abbildung 5 zeigt die verfügbaren Optionen.

### Warteschlangen

Eine Warteschlange ist ein einfaches Konzept: Eine Anwendung stellt eine Nachricht in eine Warteschlange, und diese Nachricht wird von einer anderen Anwendung gelesen. Wenn Ihre Anwendung nur diesen überschaubaren Dienst erfordert, ist Azure Queues womöglich die beste Wahl.

Aufgrund der Art und Weise, wie Azure im Laufe der Zeit gewachsen ist, bieten Azure-Speicherwarteschlangen und Service Bus-Warteschlangen ähnliche Warteschlangendienste. Die Gründe dafür, weshalb Sie eine Option der anderen eventuell vorziehen sollten, werden in dem ziemlich technischen Artikel [Azure-Warteschlangen und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung<link>][] behandelt. In den meisten Szenarien funktionieren beide Optionen.

**Warteschlangen-Szenarien**

Warteschlangen werden heutzutage häufig dazu verwendet, die Kommunikation zwischen einer Webrolleninstanz und einer Workerrolleninstanz in derselben Cloud Services-Anwendung zuzulassen.

Angenommen Sie erstellen eine Azure-Anwendung für die Videofreigabe. Die Anwendung besteht aus einem PHP-Code, der in einer Webrolle ausgeführt wird, die es Benutzern ermöglicht, Videos hochzuladen und anzuschauen. Darüber hinaus übersetzt eine Workerrolle, die mit C# implementiert wurde, hochgeladene Videos in verschiedene Formate.

Wenn eine Webrolleninstanz ein neues Video von einem Benutzer erhält, kann das Video in ein Blob gespeichert werden. Dann wird über eine Warteschlange eine Nachricht an eine Workerrolle gesendet, in der der Speicherort des neuen Videos angegeben wird. Eine beliebige Workerrolleninstanz liest die Nachricht in der Warteschlange und führt die erforderliche Videoübersetzung im Hintergrund durch. Die Strukturierung einer Anwendung auf diese Weise ermöglicht eine asynchrone Verarbeitung. Darüber hinaus ist die Anwendung leichter zu skalieren, da die Anzahl der Web- und Workerrolleninstanzen unabhängig voneinander variiert werden kann. Sie können die Warteschlangengröße auch als Trigger zum Hoch- und Herunterskalieren der Workerrollen verwenden. Wird die Warteschlange zu groß, fügen Sie Rollen hinzu. Wird sie kleiner, können Sie die Anzahl ausgeführter Rollen verringern, um Geld zu sparen.

### Service Bus

Anwendungen müssen interagieren, unabhängig davon, ob sie in der Cloud, im Datencenter, auf einem mobilen Gerät oder andernorts ausgeführt werden. Das Ziel von Azure Service Bus ist der Datenaustausch zwischen Anwendungen, die nahezu überall ausgeführt werden können.

Neben den zuvor beschriebenen Warteschlangen (eins zu eins) bietet Service Bus auch andere Kommunikationsmethoden.

![Azure Service Bus Relay][]
**Abbildung: Service Bus Relay ermöglicht die Kommunikation zwischen Anwendungen auf verschiedenen Seiten einer Firewall.**
Service Bus ermöglicht eine direkte Kommunikation über seinen Relaydienst, der für eine sichere Interaktion über Firewalls hinweg sorgt. Mithilfe von Service Bus-Relais können Anwendungen durch den Austausch von Nachrichten über einen Endpunkt kommunizieren, der nicht lokal, sondern in der Cloud gehostet wird.

**Service Bus Relay-Szenarien**

Bei Anwendungen, die über Service Bus kommunizieren, kann es sich um Azure-Anwendungen oder um Softwareanwendungen handeln, die auf einer anderen Cloud-Plattform ausgeführt werden. Es können jedoch auch Anwendungen sein, die außerhalb der Cloud ausgeführt werden. Denken Sie beispielsweise an eine Fluggesellschaft, die den Buchungsdienst auf Computern innerhalb des Datencenters implementiert. Die Fluggesellschaft muss diese Dienste für viele Clients bereitstellen, darunter Checkin-Schalter an Flughäfen, Terminals von Buchungsagenten und unter Umständen Kundentelefone. Dazu kann die Fluggesellschaft Service Bus verwenden, wobei lose gekoppelte Interaktionen zwischen den verschiedenen Anwendungen erstellt werden.

![Azure Service Bus Topics][]
 **Abbildung: Service Bus-Themen bietet mehreren Apps die Möglichkeit, Nachrichten zu senden und anderen Anwendungen, über ein Abonnement Nachrichten zu empfangen, die bestimmte Kriterien erfüllen.**

Service Bus verfügt über einen Veröffentlichen-und-Abonnieren-Mechanismus namens Themen und Abonnements. Mit Veröffentlichen/Abonnieren kann eine Anwendung Nachrichten zu einem Thema senden, während andere Anwendungen Abonnements zu diesem Thema erstellen können. Dadurch wird eine One-to-Many-Kommunikation zwischen Anwendungen eines Satzes ermöglicht, sodass dieselbe Nachricht von mehreren Empfängern gelesen werden kann.

**Service Bus Relay-Szenarien**
In allen Situationen, in denen viele Nachrichten vorkommen, die zwar sämtlich wichtig sind, von den verschiedenen nachgelagerten Systemen jedoch nur teilweise überwacht werden müssen, sind Service Bus-Themen und -Abonnements eine gute Option.

### BizTalk Services

![BizTalk Services][]
 **Abbildung: BizTalk Services ermöglicht die Transformation von XML-Nachrichtenformaten in der Cloud.**

Gelegentlich müssen Sie Verbindungen mit Systemen herstellen, die mit anderen Messagingformaten kommunizieren. Auch wenn ein gemeinsamer Standard verfügbar ist, ist in der Geschäftswelt das Arbeiten mit verschiedenen Datenbankschemata und XML-Messagingformaten der Normalfall. Anstatt viel benutzerdefinierten Code zu schreiben, können Sie mit BizTalk Server verschiedene Systeme lokal integrieren. Azure BizTalk Services bietet den gleichen Dienst, allerdings in der Cloud. Sie bezahlen nur, was Sie nutzen, und müssen sich nicht wie bei der lokalen Umsetzung um die Skalierung kümmern.

**BizTalk Services-Szenarien**
Diese Art der Übersetzung ist in der Regel bei der Interaktion zwischen Unternehmen erforderlich. Ein Flugzeughersteller muss z. B. Teile bei seinen verschiedenen Teilezulieferern bestellen. Das Unternehmen arbeitet mit vielen Teilezulieferern. Die Bestellungen sollten automatisch direkt von den Systemen des Flugzeugherstellers in die Systeme der Zulieferer übertragen werden. Keines der Unternehmen möchte seine Kernsysteme und Nachrichtenformate ändern, und es ist sehr unwahrscheinlich, dass diese Formate identisch sind. BizTalk Services kann Nachrichten in beiden Richtungen in die jeweils erforderlichen Formate übersetzen. Je nachdem, wer mehr Kontrolle wünscht, und je nach dem erforderlichen Übersetzungsumfang kann die Übersetzung entweder beim Flugzeughersteller oder bei den verschiedenen Zulieferern ausgeführt werden.

## <span id="ComputeAssist"></span></a>Compute-Unterstützung

Azure kann Dienste unterstützen, die nicht ständig ausgeführt werden müssen.

### Scheduler

![Azure Scheduler][]
**Azure Scheduler ermöglicht die Planung von Aufträgen zu bestimmten Zeiten für eine bestimmte Dauer.**

Gelegentlich müssen Anwendungen nur zu einer bestimmten Zeit ausgeführt werden. Mit Azure können Sie bei solchen Apps Geld sparen, anstatt eine Anwendung rund um die Uhr ausführen und auf zu verarbeitende Daten warten zu lassen. Mit Azure Scheduler können Sie durch Angabe eines bestimmten Zeitintervalls oder per Kalender festlegen, wann eine Anwendung ausgeführt werden soll. Der Dienst arbeitet zuverlässig und prüft, ob ein Prozess ausgeführt wird, auch wenn Netzwerk, Computer oder Datencenter ausfallen. Diese Aktionen können Sie mit der Scheduler-REST-API verwalten.

Wenn ein geplanter Alarm auftritt, sendet Scheduler HTTP- oder HTTPS-Nachrichten an einen bestimmten Endpunkt oder fügt eine Nachricht in eine Speicherwarteschlange ein. Entweder benötigt Ihre Anwendung also einen zugänglichen Endpunkt, oder Sie können damit eine Speicherwarteschlange überwachen lassen. Sobald sie die Nachricht empfängt, kann sie die jeweils programmierte Aktion ausführen.

**Scheduler-Szenarien**

-   Periodische Anwendungsaktionen: Ein Dienst ruft z. B. regelmäßig Daten von Twitter ab und fasst die Daten in einem normalen Feed zusammen.
-   Tägliche Wartung: Protokollverarbeitung oder -bereinigung, Durchführen von Sicherungen oder anderen periodisch geplanten Aufgaben.
-   Aufgaben, die nachts ausgeführt werden.
-   Webanwendungsaufgaben wie die tägliche Bereinigung von Protokollen, das Durchführen von Sicherungen und anderen Wartungsaufgaben. Ein Administrator möchte eine Datenbank z. B. für die nächsten neun Monate täglich um 1:00 Uhr sichern.

Mit der Scheduler-API können Sie "Jobsammlungen" und geplante "Jobs" programmgesteuert erstellen, aktualisieren, löschen, anzeigen und verwalten.

## <span id="Performance"></span></a>Leistung

Leistung ist für eine Anwendung immer wichtig. Anwendungen greifen immer wieder auf dieselben Daten zu. Ein Weg zur Verbesserung der Leistung besteht darin, eine Kopie dieser Daten näher an der Anwendung aufzubewahren, wodurch die Abrufzeit minimiert wird. Azure bietet dafür zwei verschiedene Dienste.

### Azure Cache

![Azure Caching][]
 **Abbildung: Eine Azure-Anwendung kann Daten im Arbeitsspeicher zwischenspeichern und die Daten sogar auf viele Workerrollen verteilen.**

Der Zugriff auf Daten, die in einem der Datenverwaltungsdienste von Azure (SQL-Datenbank, Tabellen oder Blobs) gespeichert sind, ist sehr schnell. Doch der Zugriff auf Daten, die im Speicher gespeichert sind, erfolgt sogar noch schneller. Daher kann eine speicherinterne Kopie von häufig aufgerufenen Daten die Anwendungsleistung verbessern. Verwenden Sie dazu den speicherinternen Cachedienst von Azure.

Eine Cloud Services-Anwendung kann Daten in diesem Cache speichern und sie dann direkt und ohne Zugriff auf einen beständigen Speicher abrufen. Der Cache kann auf den virtuellen Computern Ihrer Anwendung verwaltet werden, oder er wird von virtuellen Computern bereitgestellt, die ausschließlich für die Zwischenspeicherung reserviert sind. Der Cache kann in beiden Fällen verteilt werden, wobei die darin enthaltenen Daten über mehrere virtuelle Computer in einem Azure-Datencenter verteilt werden.

Azure verfügt über verschiedene Cache-Technologien, die sich im Laufe der Zeit geändert haben. Nach der Reihenfolge ihrer Einführung geordnet unterscheiden wir zwischen einem freigegebenen, einem In-Role-, einem verwalteten und einem Redis-Cache. Der freigegebene Cache ist eine ältere Technologie, mit der Sie keine neuen Implementierungen erstellen sollten. Der verwaltete Cache hat dieselben Merkmale wie der In-Role-Cache, allerdings als verwalteter Dienst außerhalb des Azure-Verwaltungsportals. Der Redis-Cache befindet sich in der Testphase. Die Redis-Implementierung besitzt die meisten Funktionen und wird empfohlen, wenn Sie neuen Cachecode schreiben.

**Azure-Cache-Szenarien**
Eine Anwendung, die wiederholt einen Produktkatalog liest, kann beispielsweise von einer derartigen Form der Zwischenspeicherung profitieren, da die benötigten Daten schneller verfügbar sind. Diese Technologie unterstützt auch Sperrfunktionen, sodass sie sowohl mit Daten mit Lese-/Schreibzugriff als auch mit schreibgeschützten Daten verwendet werden kann. Auch ASP.NET-Anwendungen können den Dienst nutzen, um Sitzungsdaten mit nur einer Konfigurationsänderung zu speichern.

### CDN

![Azure CDN][]
 **Abbildung: Kopien eines Blob können auf Standorten auf der ganzen Welt zwischengespeichert werden.**

Angenommen Sie müssen Blob-Daten speichern, auf die Benutzer auf der ganzen Welt zugreifen werden. Dabei kann es sich zum Beispiel um ein Video des letzten WM-Spiels oder um Treiberupdates oder ein beliebtes E-Book handeln. Das Speichern einer Kopie der Daten in verschiedenen Azure-Datencentern ist bereits eine Hilfe, doch wenn es viele Benutzer gibt, reicht das vermutlich nicht aus. Für eine noch bessere Leistung können Sie das Azure CDN verwenden.

Das CDN verfügt über dutzende Standorte auf der ganzen Welt, wobei an jedem Kopien der Azure-Blobs gespeichert werden können. Wenn ein Benutzer an einem beliebigen Standort das erste Mal auf ein bestimmtes Blob zugreift, werden die darin enthaltenen Informationen aus einem Azure-Datencenter in den lokalen CDN-Speicher dieses Standorts kopiert. Danach wird bei Zugriffen aus dieser geografischen Region die Blob-Kopie verwendet, die im CDN zwischengespeichert ist, d. h., es muss nicht mehr der Weg bis zum nächsten Azure-Datencenter zurückgelegt werden. Das Ergebnis ist ein schnellerer Zugriff auf häufig verwendete Daten von überall auf der Welt.

**CDN-Szenarien**
CDN wird häufig zusammen mit Media Services verwendet, um Videodaten weltweit zu übermitteln. Videodaten sind in der Regel umfangreich und benötigen sehr viel Bandbreite. Media Services wird an anderer Stelle in diesem Artikel behandelt.

## <span id="BigStuff"></span></a>Big Data und Big Compute

### HDInsight (Hadoop)

![HDInsight][]
 **Abbildung: HDInsight unterstützt die Massenverarbeitung riesiger Datenmengen.**
Seit Jahren wird die Datenanalyse überwiegend an relationalen Daten ausgeführt, die in einem Data Warehouse gespeichert sind, das mit einem relationalen DBMS erstellt wurde. Diese Art von Geschäftsanalyse ist immer noch wichtig und wird es auch noch lange sein. Aber was wäre, wenn die zu analysierende Datenmenge so groß wäre, dass sie nicht mehr von relationalen Datenbanken bewältigt werden kann? Und angenommen die Daten sind nicht relational? Womöglich handelt es sich um Serverprotokolle in einem Datencenter oder um historische Ereignisdaten aus Sensoren oder ähnliches. In Fällen wie diesem stehen Sie vor einem sogenannten Big-Data-Problem. Sie benötigen also einen anderen Ansatz.

Die heute vorherrschende Technologie zur Analyse von großen Datenmengen ist Hadoop. Diese Technologie ist ein Open-Source-Projekt von Apache. Die Daten werden mit dem Hadoop Distributed File System (HDFS) gespeichert, sodass Entwickler dann MapReduce-Aufträge erstellen können, um diese Daten zu analysieren. HDFS verteilt die Daten über mehrere Server und führt auf jedem Server Blöcke des MapReduce-Auftrags aus, sodass die große Datenmenge parallel verarbeitet werden kann.

HDInsight ist der Name des Apache Hadoop-basierten Dienstes unter Azure. HDInsight ermöglicht es dem HDFS, Daten im Cluster zu speichern und auf mehreren virtuellen Computern zu verteilen. Auch die Logik eines MapReduce-Auftrags wird über diese virtuellen Computer verteilt. Wie bei lokalem Hadoop werden die Daten für eine bessere Leistung lokal (d. h., die Logik und die verarbeiteten Daten befinden sich auf demselben virtuellen Computer) und parallel verarbeitet. HDInsight kann Daten auch in Azure Storage Vault (ASV) speichern, wobei Blobs verwendet werden. Mithilfe von ASV können Sie Geld sparen, da Sie Ihr HDInsight-Cluster löschen können, wenn es nicht mehr verwendet wird. Zugleich bleiben die Daten in der Cloud jedoch erhalten.

HDInsight unterstützt auch andere Komponenten der Hadoop-Umgebung, darunter Hive and Pig. Microsoft hat auch Komponenten erstellt, die die Arbeit mit aus HDInsight generierten Daten dank herkömmlicher Business Intelligence-Tools wie dem HiveODBC-Adapter und Data Explorer, der mit Excel arbeitet, erleichtern.

### High Performance Computing (Big Compute)

Eine der attraktivsten Nutzungsarten einer Cloud-Plattform ist das High Performance Computing (HPC) und der Einsatz anderer "Big Compute"-Anwendungen. Beispiele hierfür sind spezielle technische Anwendungen, die auf die branchenübliche Message Passing Interface (MPI) ausgelegt sind, sowie so genannte hochgradig parallele Anwendungen wie finanzielle Risikomodelle.

Bei Big Compute geht es im Kern um die Ausführung von Code auf vielen Computern gleichzeitig. Für Azure bedeutet das, dass viele virtuelle Computer gleichzeitig ausgeführt werden, die alle parallel daran arbeiten, ein Problem zu beheben. Dafür ist eine Bereitstellung von Ressourcen und eine Planung der Anwendungen erforderlich, damit die Arbeit über diese Instanzen verteilt wird. Das kostenlose HPC Pack von Microsoft und andere Rechenclusterlösungen bieten unter Azure eine gute Leistung, denn sie nutzen die Azure-Rechen- und Infrastrukturdienste, um die Kapazität eines lokalen Rechenclusters bei Bedarf zu erweitern oder Big Compute-Anwendungen vollständig in der Cloud auszuführen.

Azure ermöglicht verschiedene VM-Instanzgrößen mit unterschiedlichen Konfigurationen bei Prozessorkernen, Arbeitsspeicher, Festplattenkapazität und anderen Eigenschaften, die die Anforderungen verschiedener Anwendungen erfüllen. Die vor kurzem eingeführten Instanzen A8 und A9 eignen sich gut für zahlreiche rechenintensive Arbeitsauslastungen und insbesondere parallele MPI-Anwendungen, da sie über schnelle Prozessoren mit mehreren Kernen und sehr viel Arbeitsspeicher verfügen. In bestimmten Konfigurationen nutzen die Instanzen in der Cloud ein Anwendungsnetzwerk mit niedriger Latenz und hohem Durchsatz, das die RDMA-Technologie (Remote Direct Memory Access) umfasst, um eine maximale Effizienz paralleler MPI-Anwendungen zu erreichen.

Azure bietet Big Compute-Anwendungsentwicklern und Partnern zudem ein umfassendes Paket an Rechenfunktionen, Diensten, Architekturoptionen und Entwicklungstools. Azure unterstützt benutzerdefinierte Big Compute-Workflows, die spezielle Datenworkflows und Job- sowie Aufgabenplanungsmuster umfassen, die auf Tausende von Rechenkernen skaliert werden können.

## <span id="media"></span></a>Medien

![Azure Media Services][]
 **Abbildung: Media Services ist eine Plattform für Anwendungen, die Kunden rund um die Welt Videos und andere Medien zur Verfügung stellen.**

Ein Großteil des Internetverkehrs besteht heutzutage aus Videos, und dieser Anteil wird in Zukunft sogar noch weiter zunehmen. Die Bereitstellung von Videos im Netz ist jedoch einfaches Unterfangen. Es gibt viele Variablen zu beachten, z. B. den Codierungsalgorithmus und die Bildschirmauflösung bei den Benutzern. Bei Videos kommt es auch zu Abrufspitzen, z. B. am Samstagabend, wenn viele Leute sich dazu entschließen, online einen Film zu schauen.

Aufgrund der Beliebtheit kann man davon ausgehen, dass viele neue Anwendungen entwickelt werden, die Videos nutzen. Doch für alle Anwendungen müssen einige der allgemeinen Probleme gelöst werden. Dabei ergibt es keinen Sinn, wenn diese Probleme für jede Anwendung neu gelöst werden. Ein besserer Ansatz wäre die Erstellung einer Plattform, die eine allgemeine Lösung bietet, die von vielen Anwendungen genutzt werden kann. Die Erstellung einer solchen Plattform in der Cloud bringt einige eindeutige Vorteile mit sich. Sie ist allgemein erhältlich und bietet ein nutzungsbasiertes Gebührenmodell. Sie kann auch die Nachfrageschwankungen solcher Videoanwendungen auffangen.

Azure Media Services löst dieses Problem. Es bietet verschiedene Cloud-Komponenten, die die Arbeit von Benutzern erleichtern, die Anwendungen mit Videos und anderen Medien erstellen und ausführen.

Wie die Abbildung zeigt, bietet Media Services verschiedene Komponenten für Anwendungen, die mit Videos und anderen Medien arbeiten. So umfasst es beispielsweise eine Medienübertragungskomponente zum Hochladen von Videos in Media Services (wo sie in Azure-Blobs gespeichert werden), eine Codierungskomponente, die verschiedene Video- und Audioformate unterstützt, eine Inhaltsschutzkomponente, die Komponenten zur Verwaltung digitaler Rechte bietet, eine Komponente zum Einfügen von Werbeanzeigen in einen Videostream, Komponenten für das Streaming und vieles andere mehr. Microsoft-Partner können auch Komponenten für die Plattform bereitstellen, die dann von Microsoft verteilt werden und in deren Auftrag in Rechnung gestellt werden.

Anwendungen, die diese Plattform verwenden, können unter Azure oder anderweitig ausgeführt werden. Eine Desktopanwendung für eine Videoproduktionsfirma ermöglicht es Benutzern beispielsweise, Videos in Media Services hochzuladen, und verarbeitet sie dann auf verschiedene Weise. Darüber hinaus könnte ein Cloud-basierter Inhaltsverwaltungsdienst auf Azure auf Media Services zurückgreifen, um Videos zu verarbeiten und zu verteilen. Unabhängig vom Ausführungsort und der Funktion wählt jede Anwendung die erforderlichen Komponenten und greift über RESTful-Schnittstellen darauf zu.

Zum Verteilen der produzierten Medien kann eine Anwendung das Azure CDN oder ein anderes CDN nutzen oder die Bits direkt an die Benutzer senden. Unabhängig davon, wie die Medien dorthin gelangen, können Videos, die mit Media Services erstellt werden, von verschiedenen Clientsystemen verwendet werden, darunter Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash und Silverlight. Das Ziel besteht darin, leichter moderne Medienanwendungen zu erstellen.

Für eine visuelle Darstellung der Funktionsweise von Media Services können Sie das [Azure Media Services-Poster][] herunterladen.

## <span id="commerce"></span></a>Commerce

Die wachsende Akzeptanz von Software-as-a-Service transformiert den Erstellungsprozess von Anwendungen. Auch die Art und Weise, wie wir Anwendungen verkaufen, wird dabei transformiert. Da eine SaaS-Anwendung in der Cloud lebt, bietet es sich an, dass potenzielle Kunden online nach Lösungen suchen. Diese Änderung gilt für Daten aber auch für Anwendungen. Warum sollte die Cloud nicht für käuflich verfügbare Datasets genutzt werden? Mit [Azure Marketplace][] und [Azure Store][] hat Microsoft eine Antwort auf beide Fragen gefunden.

![Azure Commerce][]
 **Abbildung: Mit Azure Marketplace und Azure Store können Sie Azure-Anwendungen sowie kommerziell erhältliche Datasets suchen und kaufen und sie in Ihren Azure-Anwendungen verwenden.**

Der Unterschied zwischen den beiden besteht darin, dass Marketplace sich außerhalb des Azure-Verwaltungsportals befindet, wohingegen der Store über das Portal aufgerufen werden kann. Potenzielle Kunden können nach Azure-Anwendungen suchen, die ihren Anforderungen entsprechen. Kunden können auch nach käuflich erhältlichen Datasets suchen, darunter auch demografische Daten, Finanzdaten, geografische Daten und mehr. Wenn sie das Gewünschte finden, können sie über den Anbieter, direkt über den Marketplace- oder die Store-Websites oder in manchen Fällen auch über das Verwaltungsportal darauf zugreifen. Anwendungen können auch die Bing Search-API über den Marketplace verwenden, sodass sie Zugriff auf die Ergebnisse von Websuchen haben.

**Commerce-Szenarien**
SendGrid ist eine Anwendung in Azure Store, mit der Sie E-Mails senden können. Sie bietet zusätzliche Funktionen wie zuverlässige Übermittlung und Statistik. Sie können diese Anwendung und die zugehörigen Dienste kaufen, anstatt eine solche Infrastruktur selbst aufzubauen.

## <span id="start"></span></a>Erste Schritte

Nachdem Sie nun einen Überblick erhalten haben, besteht der nächste Schritt darin, Ihre erste Azure-Anwendung zu schreiben. Wählen Sie die gewünschte Sprache, [holen Sie sich das entsprechende SDK][], und los geht's. Cloud-Computing ist der neue Standard – machen Sie jetzt den ersten Schritt.

  [Die Komponenten von Azure]: #components
  [Verwaltungsportal]: #portal
  [Compute]: #compute
  [Datenverwaltung]: #data
  [Netzwerk]: #networking
  [Entwicklerdienste]: #DevService
  [Identität und Zugriff]: #identity
  [Mobile]: #mobile
  [Sicherung]: #backup
  [Messaging und Integration]: #messaging
  [Compute-Unterstützung]: #ComputeAssist
  [Leistung]: #Performance
  [Big Compute und Big Data]: #BigStuff
  [Medien]: #media
  [Commerce]: #commerce
  [Erste Schritte]: #start
  [Was ist Windows Azure-Infografik]: http://azure.microsoft.com/en-us/documentation/infographics/azure/ "What Is Microsoft Azure Poster Infographic"
  [Azure components]: ./media/intro-to-azure/AzureComponentsIntroNew800.png
  [Azure Virtual Machines]: ./media/intro-to-azure/VirtualMachinesIntroNew.png
  [Azure Websites]: ./media/intro-to-azure/AzureWebsitesIntroNew.png
  [Azure Cloud Service]: ./media/intro-to-azure/CloudServicesIntroNew.png
  [Azure Storage SQL Database]: ./media/intro-to-azure/StorageAzureSQLDatabaseIntroNew.png
  [Azure Storage Tables]: ./media/intro-to-azure/StorageTablesIntroNew.png
  [Azure Storage Blobs]: ./media/intro-to-azure/StorageBlobsIntroNew.png
  [Azure Import Export Service]: ./media/intro-to-azure/ImportExportIntroNew.png
  [Azure File Service]: ./media/intro-to-azure/FileServiceIntroNew.png
  [VirtualNetwork]: ./media/intro-to-azure/VirtualNetworkIntroNew.png
  [Virtuelles Netzwerk]: http://msdn.microsoft.com/library/azure/jj156007.aspx
  [ExpressRoute]: ./media/intro-to-azure/ExpressRouteIntroNew.png
  [ExpressRoute – Technische Übersicht]: http://msdn.microsoft.com/en-us/library/azure/dn606309.aspx
  [TrafficManager]: ./media/intro-to-azure/TrafficManagerIntroNew.png
  [Azure Multi-Factor Authentication]: ./media/intro-to-azure/MFAIntroNew.png
  [MobileServices]: ./media/intro-to-azure/MobileServicesIntroNew.png
  [NotificationHubs]: ./media/intro-to-azure/NotificationHubstIntroNew.png
  [Azure Backup]: ./media/intro-to-azure/AzureBackupIntroNew.png
  [Voraussetzungen für Azure Backup prüfen]: http://technet.microsoft.com/en-us/library/dn296608.aspx
  [Azure-Warteschlangen und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung<link>]: http://msdn.microsoft.com/library/azure/hh767287.aspx "Azure Queues and Service Bus Queues - Compared and Contrasted"
  [Azure Service Bus Relay]: ./media/intro-to-azure/ServiceBusRelayIntroNew.png
  [Azure Service Bus Topics]: ./media/intro-to-azure/ServiceBusTopicsSubsIntroNew.png
  [BizTalk Services]: ./media/intro-to-azure/BizTalkServIntroNew2.png
  [Azure Scheduler]: ./media/intro-to-azure/SchedulerIntroNew2.png
  [Azure Caching]: ./media/intro-to-azure/AzureCacheIntroNew.png
  [Azure CDN]: ./media/intro-to-azure/CDNIntroNew.png
  [HDInsight]: ./media/intro-to-azure/HDInsightIntroNew.png
  [Azure Media Services]: ./media/intro-to-azure/MediaServicesIntroNew.png
  [Azure Media Services-Poster]: http://azure.microsoft.com/en-us/documentation/infographics/media-services/
  [Azure Marketplace]: http://datamarket.azure.com/
  [Azure Store]: /en-us/store/overview/
  [Azure Commerce]: ./media/intro-to-azure/CommerceIntroNew.png
  [holen Sie sich das entsprechende SDK]: /en-us/downloads/
