
# Einführung in Azure

Azure ist die Anwendungsplattform von Microsoft für die öffentliche Cloud. Sie können diese Plattform auf vielfältige Weise nutzen. So können Sie beispielsweise Azure zum Erstellen einer Webanwendung verwenden, die in Azure-Datencentern ausgeführt wird und dort auch Daten speichert. Sie können Azure verwenden, um ausschließlich Daten zu speichern, wobei die Anwendungen, die diese Daten nutzen, lokal ausgeführt werden (d. h. außerhalb der öffentlichen Cloud). Mit Azure können Sie auch virtuelle Computer zu Entwicklungs- und Testzwecken erstellen oder SharePoint und andere Anwendungen ausführen. Sie können Azure zum Erstellen von hochgradig skalierbaren Anwendungen mit unzähligen Benutzern verwenden. All dies und noch viel mehr ist möglich, da die Plattform eine Vielzahl an unterschiedlichen Diensten bietet.

Für alle diese Aufgaben benötigen Sie jedoch Grundkenntnisse. Auch wenn Sie noch gar nichts über das Cloud Computing wissen, so erhalten Sie mit diesem Artikel einen Einblick in die Grundlagen von Azure. Ziel ist es, Ihnen Grundkenntnisse über die Funktionsweise und die Arbeit mit dieser Cloud-Plattform zu vermitteln.

## Inhaltsverzeichnis

* [Die Komponenten von Azure](#components)
* [Ausführungsmodelle/Compute](#models)
* [Datenverwaltung](#data)
* [Netzwerk](#networking)
* [Geschäftsanalyse](#analytics)
* [Nachrichten](#messaging)
* [Cachedienst](#caching)
* [Identität](#identity)
* [High Performance Computing (HPC)](#HPC)
* [Medien](#media)
* [Commerce](#commerce)
* [SDKs](#sdk)
* [Erste Schritte](#start)

<h2><a id="components" ></a>Die Komponenten von Azure</h2>


Um das Angebot von Azure besser zu verstehen, ist es sinnvoll, die verschiedenen Dienste in eigene Kategorien einzuteilen. In Abbildung 1 sehen Sie eine mögliche Kategorisierung.

![Azure-Komponenten](./media/intro-to-azure/IntroAzure1.png)   
 **Abbildung 1: Azure bietet über das Internet zugängige Anwendungsdienste, die in Azure-Datencentern ausgeführt werden.**

Vor der Arbeit mit Azure sollten Sie zumindest die Grundlagen der einzelnen Komponenten kennen. Für einen schnellen grafischen Überblick können Sie auch das [Azure-Poster][1] herunterladen. Die Farben der Felder in Abbildung 1 stimmen mit der Gruppierung auf dem Poster
überein.

Im Folgenden werden die in der Abbildung aufgeführten Technologien vorgestellt. Sie erhalten zudem eine Beschreibung der einzelnen Angebote sowie von möglichen Einsatzbereichen.

<h2><a id="models" ></a>Ausführungsmodelle/Compute</h2>


Eine der grundlegendsten Aufgaben einer Cloud-Plattform ist die Ausführung von Anwendungen. Azure bietet dazu vier Optionen: Virtual Machines, Cloud Services, Websites und Mobile Services. Jedes Azure-Ausführungsmodell erfüllt seinen eigenen Zweck.

Azure Virtual Machines bietet eine allgemeine bedarfsgesteuerte Computerumgebung. Cloud Services ist eine großartige Wahl, um skalierbare, zuverlässige Anwendungen mit geringen Verwaltungskosten zu erstellen. Azure Websites bietet verschiedene Anwendungen, Frameworks und Vorlagen, mit deren Hilfe Sie große, skalierbare Webanwendungen und Präsenzwebsites schnell erstellen können, deren Entwicklung, Test und Arbeit dann effizient verwaltet werden kann. Gleichermaßen beschleunigt Azure Mobile Services die Entwicklung von Apps für mobile Geräte. Sie können auf sofort einsetzbare Mittel zurückgreifen, um Daten in der Cloud zu speichern, um Benutzer zu authentifizieren und um Push-Benachrichtigungen zu senden.

Sie können diese Technologien separat verwenden oder sie wie benötigt miteinander kombinieren, um die richtige Grundlage für Ihre Anwendung zu erstellen. Bei der Wahl des richtigen Ansatzes ist das Problem, das Sie lösen möchten, ausschlaggebend.

### Virtuelle Computer

Die Möglichkeit, bei Bedarf einen virtuellen Computer zu erstellen, sei es anhand eines Standard-Images oder anhand eines von Ihnen bereitgestellten Images, kann sehr nützlich sein. Wenn Sie dann noch die Möglichkeit haben, für diesen virtuellen Computer mit minutengenauer Abrechnung und nur bei Nutzung zu zahlen, profitieren Sie zusätzlich davon. Genau diesen Ansatz, im Allgemeinen als Infrastructure-as-a-Services (IaaS) bezeichnet, bietet Ihnen Azure Virtual Machines.

Zum Erstellen eines virtuellen Computers legen Sie die VHD, die verwendet werden soll, sowie die Größe des virtuellen Computers fest. Dann zahlen Sie für die Zeit, die der virtuelle Computer ausgeführt wird. Azure Virtual Machines bietet eine Galerie an Bestands-VHDs. Dazu gehören von Microsoft bereitgestellte Optionen wie Windows Server 2008 R2, Windows Server 2012 und Windows Server 2008 R2 mit SQL Server sowie von Microsoft-Partnern bereitgestellte Linux-Images. Sie können die virtuellen Computer von Ihren eigenen VHDs erstellen und sie dann auch der Galerie hinzufügen.

Unabhängig vom Ursprung des Images können Sie alle Änderungen, die beim Ausführen eines virtuellen Computers vorgenommen wurden, dauerhaft speichern. Wenn Sie das nächste Mal einen virtuellen Computer von dieser VHD erstellen, werden die zuletzt verwendeten Einstellungen übernommen. Sie können auch die geänderte VHD aus Azure kopieren und sie dann lokal ausführen.

Azure-VMs können auf unterschiedliche Weise genutzt werden. Sie können sie beispielsweise verwenden, um eine günstige Entwicklungs- und Testplattform zu erstellen, die Sie nach Abschluss Ihrer Arbeiten wieder außer Betrieb nehmen. Sie können auch Anwendungen erstellen und ausführen, die genau die Sprachen und Bibliotheken nutzen, die Sie benötigen. Diese Anwendungen können jede Datenverwaltungsoption nutzen, die in Azure zur Verfügung steht. Sie können auch SQL Server oder ein anderes DBMS verwenden, das auf einem oder mehreren virtuellen Computern ausgeführt wird. Eine weitere Option ist die Nutzung von Azure-VMs als Erweiterung Ihres lokalen Datencenters, auf denen SharePoint oder andere Anwendungen ausgeführt werden. Zur Unterstützung dieser Option können Sie Windows-Domänen in der Cloud erstellen, indem Sie Active Directory auf Azure-VMs ausführen. Dieser eher allgemeine Cloud-Computing-Ansatz kann zur Lösung verschiedener Probleme herangezogen werden. Die endgültige Entscheidung liegt bei Ihnen.

### Websites

Die Cloud wird am häufigsten dazu verwendet, Websites und Webanwendungen auszuführen. Azure Virtual Machines macht dies möglich; Sie sind jedoch für die Verwaltung der virtuellen Computer verantwortlich. Was, wenn Sie nur eine Website möchten, für die jemand anderes die Verwaltungsarbeiten übernimmt?

Genau das bietet Ihnen Azure Websites. Dieses Ausführungsmodell stellt eine verwaltete Webumgebung mit dem Azure-Verwaltungsportal sowie APIs bereit. Sie können eine bestehende Website unverändert in Azure Websites verschieben, oder Sie können direkt in der Cloud eine neue erstellen. Sobald die Website betriebsbereit ist, können Sie dynamisch Instanzen hinzufügen oder entfernen, wobei Azure Websites den Lastenausgleich von Anfragen zwischen den Instanzen übernimmt. Azure Websites bietet eine Option für eine gemeinsame Nutzung, sodass Sie Ihre Website zusammen mit anderen Websites auf einem virtuellen Computer betreiben können, sowie eine Standardoption, mit der Sie eine Website auf einem eigenen virtuellen Computer betreiben können. Mit der Standardoption können Sie bei Bedarf auch die Größe (Rechenleistung) Ihrer Instanzen ändern.

Azure Websites wurde mit Unternehmen, Entwicklern und Webdesign-Agenturen vor Auge konzipiert. Für Unternehmen bietet es eine einfach zu verwaltende, skalierbare, hochsichere und hochverfügbare Lösung für den Betrieb von Präsenzwebsites. Bei der Entwicklung unterstützt es .NET, PHP, Node.js und Python sowie SQL-Datenbank und MySQL (von ClearDB, einem Microsoft-Partner) für relationalen Speicher. Es bietet auch integrierte Unterstützung für mehrere beliebte Anwendungen wie WordPress, Joomla und Drupal. Ziel ist es, eine kostengünstige, skalierbare und vielseitig nutzbare Plattform zum Erstellen von Websites und Webanwendungen in der öffentlichen Cloud bereitzustellen.

### Cloud Services

Angenommen Sie möchten eine Cloud-Anwendung erstellen, die viele parallel angemeldete Benutzer unterstützt, die keines riesigen Verwaltungsaufwands bedarf und die niemals ausfällt. Sie könnten beispielsweise ein erfahrener Softwareanbieter sein, der sich für die Implementierung von Software-as-a-Service (SaaS) entschieden hat, indem er eine Version von einer Ihrer Anwendungen in der Cloud erstellt. Oder Sie sind ein Startup-Unternehmen, das eine Benutzeranwendung erstellt, von der Sie erwarten, dass sie schnell wachsen wird. Welches Ausführungsmodell sollten Sie verwenden, wenn Sie Azure als Grundlage nutzen?

Mit Azure Websites können Sie eine derartige Webanwendung erstellen, es gibt jedoch einige Einschränkungen. Sie haben zum Beispiel keinen Administratorzugriff, das heißt, Sie können nicht eigenmächtig Software installieren. Azure Virtual Machines bietet Ihnen eine große Flexibilität, darunter auch Administratorzugriff, und es ist auf jeden Fall geeignet, um eine hoch skalierbare Anwendung zu erstellen. Sie sind jedoch für viele Aspekte im Hinblick auf Zuverlässigkeit und Verwaltung selbst verantwortlich. Sie brauchen eine Option, die Ihnen die gewünschte Kontrolle bietet, die jedoch auch den Großteil der im Hinblick auf Zuverlässigkeit und Verwaltung erforderlichen Arbeiten
übernimmt.

Und genau hier kommen die Azure-Clouddienste ins Spiel. Diese Technologie ist darauf ausgelegt, skalierbare, zuverlässige Anwendungen mit geringem Verwaltungsaufwand zu unterstützen. Zugleich ist sie ein Beispiel für die gemeinhin als Platform-as-a-Service (PaaS) bezeichnete Technologie. Zur Nutzung erstellen Sie eine Anwendung mit der von Ihnen gewählten Technologie, wie C#, Java, PHP, Python, Node.js usw. Der Code wird dann auf virtuellen Computern ausgeführt, die als Instanzen bezeichnet werden. Dazu muss auf dem virtuellen Computer eine Version von Windows Server laufen.

Diese virtuellen Computer unterscheiden sich jedoch von jenen, die Sie mit Azure Virtual Machines erstellen. Zum einen werden sie von Azure selbst verwaltet, sprich, es werden Patches für das Betriebssystem installiert und neue Images mit Patches werden automatisch implementiert. (Das bedeutet, dass Ihre Anwendung den Status in Web- oder Workerrolleninstanzen nicht verwaltet. Wenden Sie stattdessen eine der im nächsten Abschnitt beschriebenen Optionen der Azure-Datenverwaltung dauerhaft auf sie an.) Azure überwacht auch die virtuellen Computer und startet jeden fehlgeschlagenen virtuellen Computer neu.

Beim Erstellen einer Instanz können Sie zwischen zwei Rollen wählen, die beide auf Windows Server basieren. Der Hauptunterschied zwischen den beiden besteht darin, dass für eine Instanz einer Webrolle IIS ausgeführt wird, während dies bei der Instanz einer Workerrolle nicht der Fall ist. Beide werden jedoch gleichermaßen verwaltet, und häufig verwendet eine Anwendung beide. Eine Webrolleninstanz akzeptiert beispielsweise Anfragen von Benutzern und leitet sie dann zur Verarbeitung an eine Workerrolleninstanz. Zum Skalieren Ihrer Anwendung können Sie anfordern, dass Azure weitere Instanzen von einer der beiden Rollen erstellt oder bestehende einstellt. Ähnlich wie bei Azure Virtual Machines werden Ihnen nur Gebühren für die Zeit in Rechnung gestellt, in der die Web- oder Workerrolleninstanzen auch tatsächlich ausgeführt wurden.

### Mobile Services

Wenn Sie eine App für ein mobiles Gerät erstellen, beschleunigt Azure Mobile Services die Entwicklung, indem es sofort einsetzbare Methoden zum Speichern von Daten in der Cloud, zum Authentifizieren von Benutzern und zum Senden von Push-Benachrichtigungen bereitstellt. Systemeigene Client-Bibliotheken für Android, iOS, HTML/JavaScript, Windows Phone und Windows Store erleichtern den Betrieb von Apps, die auf allen wichtigen Mobilplattformen verfügbar sind. Eine offene und flexible REST API ermöglicht es Ihnen auch, Mobile Services-Daten und die Authentifizierungsfunktion mit Apps auf nahezu jeder Plattform zu nutzen. Ein einzelner mobiler Service kann mehrere Client-Apps stützen, sodass Sie ein konsistentes Benutzererlebnis auf verschiedenen Geräten bieten können.

Mit Mobile Services können Sie in einer SQL-Datenbank gespeicherte Daten mühelos bereitstellen und verwalten. Sie können Benutzer über bekannte Identitätsanbieter wie Facebook-, Twitter-, Microsoft- oder Google-Konten authentifizieren und Benachrichtigungsdienste verwenden, um Push-Benachrichtigungen an Ihre App zu übermitteln. Mit serverseitigem Code können Sie mühelos zusätzliche Datenspeicheroptionen wie Blob Storage oder MongoDB verwenden, Add-Ons wie SendGrid oder Push aus dem Azure Store nutzen, andere Azure-Services wie Service-Bus und Workerrollen verwenden oder sogar eine Verbindung zu lokalen Systemen herstellen. Ein Dienst kann skaliert werden, wenn eine App beliebter wird; zudem werden Überwachung und Protokollierung unterstützt.

Auch wenn Sie mit Virtual Machines, Cloud Services oder Websites das Backend für eine mobile App erstellen können, so erspart Mobile Services Ihnen sehr viel Zeit beim Schreiben der zugrunde liegenden Dienstkomponenten.

<h2><a id="data" ></a>Datenverwaltung</h2>


Anwendungen brauchen Daten, und unterschiedliche Arten von Anwendungen benötigen unterschiedliche Arten von Daten. Daher bietet Azure verschiedene Wege, um Daten zu speichern und zu verwalten.

Sie können SQL Server oder eine andere DBMS auf einem virtuellen Computer ausführen, der mit Azure Virtual Machines erstellt wurde. (Es ist wichtig, dass Sie sich dessen bewusst sind, dass diese Option nicht auf relationale Systeme beschränkt ist: Sie können auch NoSQL-Technologien wie MongoDB und Cassandra ausführen.) Das Betreiben eines eigenen Datenbanksystems ist unkompliziert: Im Grunde werden die aus unseren eigenen Datencentern vertrauten Systeme repliziert, aber darüber hinaus muss dieses DBMS verwaltet werden. Zur Erleichterung bietet Azure drei Datenverwaltungsoptionen, die zum Großteil für Sie verwaltet werden. Abbildung 2 zeigt die verfügbaren Optionen.

![Azure-Datenverwaltung](./media/intro-to-azure/IntroAzure3.png)   
 **Abbildung 2: Für die Datenverwaltung bietet Azure relationalen Speicher, skalierbare NoSQL-Tabellen sowie unstrukturierten Binärspeicher.**

Jede dieser drei Optionen berücksichtigt ein anderes Bedürfnis: relationalen Speicher, schnellen Zugriff auf potenziell große Mengen einfach typisierter Daten und unstrukturierten Binärspeicher. In allen drei Fällen werden die Daten automatisch auf drei verschiedene Computer in einem Azure-Datencenter repliziert, um die Hochverfügbarkeit sicherzustellen. Erwähnenswert ist auch, dass alle drei Optionen für Azure-Anwendungen verfügbar sind oder für Anwendungen, die anderweitig, z. B. in Ihrem lokalen Datencenter, auf Ihrem Laptop oder auf Ihrem Telefon, ausgeführt werden. Unabhängig davon, wie Sie sie einsetzen, zahlen Sie für alle Azure-Datenverwaltungsdienste stets nur nach Nutzung, einschließlich einer Gigabyte-pro-Monat-Gebühr für gespeicherte Daten.

### SQL-Datenbank

Für relationalen Speicher stellt Azure eine SQL-Datenbank bereit. Früher unter dem Namen SQL Azure bekannt, bietet SQL-Datenbank alle wichtigen Funktionen eines Verwaltungssystems für relationale Datenbanken, darunter atomare Transaktionen, paralleler Datenzugriff von mehreren Benutzern mit Datenintegrität, ANSI SQL-Abfragen und ein bekanntes Programmierungsmodell. Sie können mithilfe des Entity Framework, ADO.NET, JDBC und anderer bekannter Datenzugriffstechnologien auf SQL-Datenbank zugreifen, wie dies auch bei SQL Server der Fall ist. Unterstützt werden auch der Großteil der T-SQL-Sprache sowie SQL Server-Tools wie SQL Server Management Studio. Für Benutzer, die mit SQL Server (oder einer anderen relationalen Datenbank) vertraut sind, ist die Nutzung von SQL-Datenbank schnell erlernbar.

SQL-Datenbank ist jedoch nicht nur ein DBMS in der Cloud, es handelt sich um einen PaaS. Sie kontrollieren weiterhin Ihre Daten und steuern den Zugriff, aber SQL-Datenbank übernimmt die Routinearbeiten bei der Verwaltung, z. B. die Verwaltung der Hardwareinfrastruktur und die automatische Aktualisierung der Datenbank und des Betriebssystems. SQL-Datenbank bietet auch eine Verbundoption, die Daten über mehrere Server verteilt. Das ist nützlich für Anwendungen, die mit großen Datenmengen arbeiten oder die Datenzugriffsanforderungen für eine bessere Leistung über mehrere Server verteilen müssen.

Wenn Sie eine Azure-Anwendung (mit einem der drei Ausführungsmodelle) erstellen, die einen relationalen Speicher benötigt, ist SQL-Datenbank u. U. eine gute Wahl. Anwendungen, die außerhalb der Cloud ausgeführt werden, können diesen Dienst jedoch ebenfalls nutzen, sodass es viele andere Szenarios gibt. So können Sie von verschiedenen Clientsystemen aus, darunter Desktops, Laptops, Tablets und Telefone, beispielsweise auf in SQL-Datenbank gespeicherte Daten zugreifen. Und da dank der Replizierung eine integrierte Hochverfügbarkeit bereitgestellt wird, kann der Einsatz von SQL-Datenbank dabei helfen, die Downtime zu minimieren.

### Tabellen

Angenommen Sie möchten eine Azure-Anwendung erstellen, die schnellen Zugriff auf typisierte Daten benötigt, womöglich sogar auf viele Daten, die jedoch für diese Daten keine komplexen SQL-Abfragen durchführen muss. Beispiel: Sie möchten eine Benutzeranwendung erstellen, die für jeden Benutzer Kundenprofilinformationen speichern muss. Ihre App wird sehr beliebt sein, sodass Sie eine große Datenmenge einkalkulieren müssen. Doch außer der Speicherung und dem einfachen Abrufen geschieht nicht viel mit diesen Daten. Genau bei solch einem Szenario ergibt der Einsatz von Azure Tables einen Sinn.

Lassen Sie sich vom Namen jedoch nicht täuschen: Diese Technologie bietet keinen relationalen Speicher. (Tatsächlich handelt es sich um ein Beispiel für einen NoSQL-Ansatz, einen so genannten Schlüsselwertspeicher.) Stattdessen ermöglicht Azure Tables es einer Anwendung, Eigenschaften unterschiedlicher Art, z. B. Zeichenfolgen, Ganzzahlen und Daten, zu speichern. Eine Anwendung kann dann eine Gruppe von Eigenschaften abrufen, indem ein eindeutiger Schlüssel für diese Gruppe angegeben wird. Während komplexe Vorgänge wie Verknüpfungen nicht unterstützt werden, bieten Tabellen einen schnellen Zugriff auf typisierte Daten. Sie sind auch hoch skalierbar, da eine einzelne Tabelle bis zu einem Terabyte an Daten umfassen kann. Neben der Einfachheit sind Tabellen in der Regel auch kostengünstiger als relationale Speicher von SQL-Datenbank.

### Blobs

Die dritte Datenverwaltungsoption, Azure-Blobs, wurde zum Speichern von unstrukturierten Binärdaten entwickelt. Wie Tabellen stellen Blobs einen kostengünstigen Speicher dar, wobei ein einzelner Blob bis zu ein Terabyte groß sein kann. So kann eine Anwendung, in der z. B. Videos oder Backup-Daten bzw. andere Binärinformationen gespeichert werden, Blobs als einfachen, kostengünstigen Speicher nutzen. Azure-Anwendungen können auch Azure-Laufwerke nutzen, sodass Blobs einen beständigen Speicher für ein in einer Azure-Instanz gemountetes Windows-Dateisystem bieten können. In der Anwendung sind herkömmliche Windows-Dateien sichtbar, doch der Inhalt wird tatsächlich in einem Blob gespeichert.

<h2><a id="networking" ></a>Netzwerk</h2>


Azure wird heutzutage in verschiedenen Datencentern in den USA, Europa und Asien eingesetzt. Wenn Sie eine Anwendung ausführen oder Daten speichern, können Sie ein oder mehrere dieser Datencenter auswählen. Sie können auch auf verschiedene Weise eine Verbindung zu diesen Datencentern herstellen:

* Mit Azure Virtual Network können Sie eine Verbindung Ihres eigenen lokalen Netzwerks vor Ort mit einem definierten Satz von Azure-VMs herstellen.

* Wenn Ihre Azure-Anwendung in mehreren Datencentern ausgeführt wird, können Sie den Azure Traffic Manager verwenden, um Anfragen von Benutzern intelligent zwischen Instanzen der Anwendung zu leiten.

Abbildung 3 zeigt diese Optionen.

![Azure-Netzwerk](./media/intro-to-azure/IntroAzure4.png)   
 **Abbildung 3: Azure ermöglicht die Erstellung eines Cloud-VPN sowie die intelligente Verteilung von Benutzeranfragen über verschiedene Datencenter hinweg.**

### Virtuelles Netzwerk

Ein nützlicher Einsatzbereich für eine öffentliche Cloud ist die Erweiterung Ihres eigenen Datencenters. Da Sie virtuelle Computer bei und nach Bedarf erstellen können, die Sie dann auch wieder entfernen können (womit auch keine weiteren Kosten mehr anfallen), wenn sie nicht mehr benötigt werden, steht Ihnen genau die Rechenleistung zur Verfügung, die Sie benötigen. Und da Sie mit Azure Virtual Machines virtuelle Computer erstellen können, auf denen SharePoint, Active Directory oder andere bekannte lokale Softwareanwendungen ausgeführt werden, ist dieser Ansatz auch für bereits vorhandene Anwendungen geeignet.

Um den größtmöglichen Nutzen zu erreichen, sollten Ihre Benutzer jedoch in der Lage sein, diese Anwendungen so zu verwenden, als würden sie in Ihrem eigenen Datencenter ausgeführt werden. Genau das ermöglicht Azure Virtual Network. Mithilfe eines VPN-Gateways kann ein Administrator ein virtuelles privates Netzwerk (VPN) zwischen Ihrem lokalen Netzwerk und einer definierten Gruppe von virtuellen Computern einrichten, die in Azure ausgeführt werden. Da Sie den Cloud-VMs Ihre eigenen IPv4-Adressen zuweisen, scheinen sie sich in Ihrem Netzwerk zu befinden. Benutzer in Ihrer Organisation können auf die Anwendungen auf diesen virtuellen Computern zugreifen, ganz so als würden sie lokal ausgeführt.

### Traffic Manager

Eine Azure-Anwendung mit Benutzern an nur einem geografischen Standort wird womöglich nur in einem Azure-Datencenter ausgeführt. Eine Anwendung mit auf der ganzen Welt verteilten Benutzern wird jedoch sehr wahrscheinlich in verschiedenen Datencentern ausgeführt, womöglich sogar in allen. Aus dieser zweiten Situation ergibt sich ein Problem: Wie können Sie Benutzer intelligent den einzelnen Anwendungsinstanzen zuweisen? Das folgende Szenario ist wohl das bekannteste: Jeder Benutzer soll Zugriff auf das Datencenter in seiner Nähe haben, da somit die beste Reaktionszeit sichergestellt wird. Was ist jedoch, wenn diese Anwendungsinstanz ausgelastet oder nicht verfügbar ist? In diesem Fall wäre es gut, wenn die Anfrage automatisch an ein anderes Datencenter geleitet wird. Und genau diese Aufgabe übernimmt Azure Traffic Manager.

Der Verantwortliche für eine Anwendung definiert Regeln, die festlegen, wie Anfragen von Benutzern an Datencenter geleitet werden sollen, und überlässt dann Traffic Manager die Umsetzung dieser Regeln. Benutzer werden beispielsweise normalerweise an das nächste Azure-Datencenter geleitet, werden dann jedoch an ein anderes weitergeleitet, wenn das Standarddatencenter langsamer antwortet als andere Datencenter. Bei global verteilten Anwendungen mit vielen Benutzern ist solch ein integrierter Dienst für derartige Probleme nützlich.

<h2><a id="analytics" ></a>Geschäftsanalyse</h2>


Die Analyse von Daten ist eine wichtige Aufgabe der IT für Unternehmen. Eine Cloud-Plattform bietet einen Pool an On-Demand-Ressourcen mit nutzungsbasiertem Gebührensystem, sodass sie eine gute Grundlage für diese Form des Computing darstellt. Dementsprechend bietet Azure zwei Optionen für die Geschäftsanalyse. Abbildung 4 zeigt die verfügbaren Optionen.

![Azure-Analyse](./media/intro-to-azure/IntroAzure5.png)   
 **Abbildung 4: Für die Geschäftsanalyse bietet Azure verschiedene Berichte sowie die Unterstützung für große Datenmengen.**

Die Analyse von Daten kann viele Formen annehmen, daher sind diese beiden Option auch sehr unterschiedlich. Beide sollten im Einzelnen näher betrachtet werden.

### Berichterstellung mit SQL-Datenbank

Gespeicherte Daten werden am häufigsten zum Erstellen von Berichten herangezogen.

Wenn Sie SQL Server Reporting Services (SSRS) auf Windows Virtual Machines ausführen, können Sie mühelos verfügbare Berichtsfunktionen in Ihre Azure-Anwendung integrieren. Sie können Berichte mit Tabellen, Diagrammen, Karten, Maßstäben und vielem mehr in verschiedenen Formaten erstellen, darunter HTML, XML, PDF und Excel.

Sie können auch Analysen mit SQL-Datenbankdaten durchführen, wobei lokale Business Intelligence-Tools wie SSRS zum Einsatz kommen. Für einen Client sieht SQL-Datenbank wie SQL Server aus, sodass beide dieselben Technologien nutzen können.

### HDInsight (Hadoop)

Viele Jahre lang wurde der Großteil der Datenanalyse anhand von relationalen Daten durchgeführt, die in einem Data Warehouse auf der Basis eines relationalen DBMS gespeichert waren. Diese Art von Geschäftsanalyse ist immer noch wichtig und wird es auch noch lange sein. Aber was wäre, wenn die zu analysierende Datenmenge so groß wäre, dass sie nicht mehr von relationalen Datenbanken bewältigt werden kann? Und angenommen die Daten sind nicht relational? Womöglich handelt es sich um Serverprotokolle in einem Datencenter oder um historische Ereignisdaten aus Sensoren oder ähnliches. In Fällen wie diesem stehen Sie vor einem sogenannten Big-Data-Problem. Sie benötigen also einen anderen Ansatz.

Die heute vorherrschende Technologie zur Analyse von großen Datenmengen ist Hadoop. Diese Technologie ist ein Open-Source-Projekt von Apache. Die Daten werden mit dem Hadoop Distributed File System (HDFS) gespeichert, sodass Entwickler dann MapReduce-Aufträge erstellen können, um diese Daten zu analysieren. HDFS verteilt die Daten über mehrere Server und führt auf jedem Server Blöcke des MapReduce-Auftrags aus, sodass die große Datenmenge parallel verarbeitet werden kann.

HDInsight ist der Name des Apache Hadoop-basierten Dienstes unter Azure. Wie in Abbildung 4 deutlich wird, ermöglicht HDInsight es dem HDFS, Daten im Cluster zu speichern und auf mehreren virtuellen Computern zu verteilen. Auch die Logik eines MapReduce-Auftrags wird über diese virtuellen Computer verteilt. Wie bei lokalem Hadoop werden die Daten für eine bessere Leistung lokal (d. h., die Logik und die verarbeiteten Daten befinden sich auf demselben virtuellen Computer) und parallel verarbeitet. HDInsight kann Daten auch in Azure Storage Vault (ASV) speichern, wobei Blobs verwendet werden. Mithilfe von ASV können Sie Geld sparen, da Sie Ihr HDInsight-Cluster löschen können, wenn es nicht mehr verwendet wird. Zugleich bleiben die Daten in der Cloud jedoch erhalten.

HDInsight unterstützt auch andere Komponenten der Hadoop-Umgebung, darunter Hive and Pig. Microsoft hat auch Komponenten erstellt, die die Arbeit mit aus HDInsight generierten Daten dank herkömmlicher Business Intelligence-Tools wie dem HiveODBC-Adapter und Data Explorer, der mit Excel arbeitet, erleichtern.

<h2><a id="messaging" ></a>Nachrichten</h2>


Unabhängig von der Funktion eines Codes muss eine häufige Interaktion mit anderem Code stattfinden. In manchen Situationen reichen einfache Nachrichten in Warteschlangen aus. In anderen Fällen sind jedoch komplexe Interaktionen erforderlich. Azure bietet verschiedene Methoden zur Lösung dieser Probleme. Abbildung 5 zeigt die verfügbaren Optionen.

![Azure-Nachrichten](./media/intro-to-azure/IntroAzure6.png)   
 **Abbildung 5: Zum Herstellen von Verbindungen zwischen Anwendungen bietet Azure Warteschlangen, Veröffentlichen-und-Abonnieren-Mechanismen sowie synchrone Verbindungen über die Cloud.**

### Warteschlangen

Eine Warteschlange ist ein einfaches Konzept: Eine Anwendung stellt eine Nachricht in eine Warteschlange, und diese Nachricht wird von einer anderen Anwendung gelesen. Wenn Ihre Anwendung nur diesen überschaubaren Dienst erfordert, ist Azure Queues womöglich die beste Wahl.

Warteschlangen werden heutzutage häufig dazu verwendet, die Kommunikation zwischen einer Webrolleninstanz und einer Workerrolleninstanz in derselben Cloud Services-Anwendung zuzulassen. Angenommen Sie erstellen eine Azure-Anwendung für die Videofreigabe. Die Anwendung besteht aus einem PHP-Code, der in einer Webrolle ausgeführt wird, die es Benutzern ermöglicht, Videos hochzuladen und anzuschauen. Darüber hinaus übersetzt eine Workerrolle, die mit C# implementiert wurde, hochgeladene Videos in verschiedene Formate. Wenn eine Webrolleninstanz ein neues Video von einem Benutzer erhält, kann das Video in ein Blob gespeichert werden. Dann wird über eine Warteschlange eine Nachricht an eine Workerrolle gesendet, in der der Speicherort des neuen Videos angegeben wird. Eine beliebige Workerrolleninstanz liest die Nachricht in der Warteschlange und führt die erforderliche Videoübersetzung im Hintergrund durch. Die Strukturierung einer Anwendung auf diese Weise ermöglicht eine asynchrone Verarbeitung. Darüber hinaus ist die Anwendung leichter zu skalieren, da die Anzahl der Web- und Workerrolleninstanzen unabhängig voneinander variiert werden kann.

### Service Bus

Anwendungen müssen interagieren, unabhängig davon, ob sie in der Cloud, im Datencenter, auf einem mobilen Gerät oder andernorts ausgeführt werden. Das Ziel von Azure Service Bus ist der Datenaustausch zwischen Anwendungen, die nahezu überall ausgeführt werden können.

Wie Abbildung 5 zeigt, bietet Service Bus einen Warteschlangendienst. Dieser Dienst ist jedoch nicht mit den gerade beschriebenen Warteschlangen identisch. Im Gegensatz zu Azure Queues bietet Service Bus beispielsweise Warteschlangen (Eins-zu-Eins) sowie Veröffentlichen-und-Abonnieren-Mechanismen. Mit Veröffentlichen/Abonnieren kann eine Anwendung Nachrichten zu einem Thema senden, während andere Anwendungen Abonnements zu diesem Thema erstellen können. Dadurch wird eine One-to-Many-Kommunikation zwischen Anwendungen eines Satzes ermöglicht, sodass dieselbe Nachricht von mehreren Empfängern gelesen werden kann. Warteschlangen sind jedoch nicht die einzige Option: Service Bus ermöglicht auch eine direkte Kommunikation über seinen Relaisdienst, der eine sichere Interaktion über Firewalls hinweg ermöglicht. Mithilfe von Service Bus-Relais können Anwendungen durch den Austausch von Nachrichten über einen Endpunkt kommunizieren, der nicht lokal, sondern in der Cloud gehostet wird.

Bei Anwendungen, die über Service Bus kommunizieren, kann es sich um Azure-Anwendungen oder um Softwareanwendungen handeln, die auf einer anderen Cloud-Plattform ausgeführt werden. Es können jedoch auch Anwendungen sein, die außerhalb der Cloud ausgeführt werden. Denken Sie beispielsweise an eine Fluggesellschaft, die den Buchungsdienst auf Computern innerhalb des Datencenters implementiert. Die Fluggesellschaft muss diese Dienste für viele Clients bereitstellen, darunter Checkin-Schalter an Flughäfen, Terminals von Buchungsagenten und unter Umständen Kundentelefone. Dazu kann die Fluggesellschaft Service Bus verwenden, wobei lose gekoppelte Interaktionen zwischen den verschiedenen Anwendungen erstellt werden.

<h2><a id="caching" ></a>Cachedienst</h2>


Anwendungen greifen immer wieder auf dieselben Daten zu. Ein Weg zur Verbesserung der Leistung besteht darin, eine Kopie dieser Daten näher an der Anwendung aufzubewahren, wodurch die Abrufzeit minimiert wird. Azure bietet dafür zwei verschiedene Dienste: eine speicherinterne Zwischenspeicherung von Daten, die von Azure-Anwendungen verwendet werden, und ein Netzwerk für die Inhaltsübermittlung (CDN, Content Delivery Network), das Blob-Daten auf einer Festplatte zwischenspeichert, die sich näher bei den Benutzern befindet. In Abbildung 6 können Sie beide Dienste sehen.

![Azure-Cachedienst](./media/intro-to-azure/IntroAzure7.png)   
 **Abbildung 6: Eine Azure-Anwendung kann Daten im Arbeitsspeicher
zwischenspeichern, und Kopien eines Blobs können an Standorten auf der ganzen Welt zwischengespeichert werden.**

### Cachedienst

Der Zugriff auf Daten, die in einem der Datenverwaltungsdienste von Azure (SQL-Datenbank, Tabellen oder Blobs) gespeichert sind, ist sehr schnell. Doch der Zugriff auf Daten, die im Speicher gespeichert sind, erfolgt sogar noch schneller. Daher kann eine speicherinterne Kopie von häufig aufgerufenen Daten die Anwendungsleistung verbessern. Verwenden Sie dazu den speicherinternen Cachedienst von Azure.

Eine Cloud Services-Anwendung kann Daten in diesem Cache speichern und sie dann direkt und ohne Zugriff auf einen beständigen Speicher abrufen. Wie in Abbildung 6 gezeigt, kann der Cache auf den virtuellen Computern Ihrer Anwendung verwaltet werden, oder er wird von virtuellen Computern bereitgestellt, die ausschließlich für die Zwischenspeicherung reserviert sind. Der Cache kann in beiden Fällen verteilt werden, wobei die darin enthaltenen Daten über mehrere virtuelle Computer in einem Azure-Datencenter verteilt werden.

Eine Anwendung, die wiederholt einen Produktkatalog liest, kann beispielsweise von einer derartigen Form der Zwischenspeicherung profitieren, da die benötigten Daten schneller verfügbar sind. Diese Technologie unterstützt auch Sperrfunktionen, sodass sie sowohl mit Daten mit Lese-/Schreibzugriff als auch mit schreibgeschützten Daten verwendet werden kann. Auch ASP.NET-Anwendungen können den Dienst nutzen, um Sitzungsdaten mit nur einer Konfigurationsänderung zu speichern.

### CDN

Angenommen Sie müssen Blob-Daten speichern, auf die Benutzer auf der ganzen Welt zugreifen werden. Dabei kann es sich zum Beispiel um ein Video des letzten WM-Spiels oder um Treiberupdates oder ein beliebtes E-Book handeln. Das Speichern einer Kopie der Daten in verschiedenen Azure-Datencentern ist bereits eine Hilfe, doch wenn es viele Benutzer gibt, reicht das vermutlich nicht aus. Für eine noch bessere Leistung können Sie das Azure CDN verwenden.

Das CDN verfügt über dutzende Standorte auf der ganzen Welt, wobei an jedem Kopien der Azure-Blobs gespeichert werden können. Wenn ein Benutzer an einem beliebigen Standort das erste Mal auf ein bestimmtes Blob zugreift, werden die darin enthaltenen Informationen aus einem Azure-Datencenter in den lokalen CDN-Speicher dieses Standorts kopiert. Danach wird bei Zugriffen aus dieser geografischen Region die Blob-Kopie verwendet, die im CDN zwischengespeichert ist, d. h., es muss nicht mehr der Weg bis zum nächsten Azure-Datencenter zurückgelegt werden. Das Ergebnis ist ein schnellerer Zugriff auf häufig verwendete Daten von
überall auf der Welt.

<h2><a id="identity" ></a>Identität</h2>


Bei den meisten Anwendungen findet eine Identitätsbestimmung statt. So kann beispielsweise eine Anwendung anhand der Identität eines Benutzers bestimmen, auf welche Weise mit diesem Benutzer interagiert werden soll. Zur Unterstützung dieser Aufgabe stellt Microsoft Azure Active Directory bereit.

Wie die meisten Verzeichnisdienste speichert Azure Active Directory Informationen über Benutzer und Organisationen, denen sie angehören. Active Directory ermöglicht es Benutzern sich anzumelden und weist ihnen Tokens zu, mit denen ihre Identität in Anwendungen bestätigt wird. Auch die Synchronisierung der Benutzerinformationen mit Windows Server Active Directory, das in Ihrem lokalen Netzwerk ausgeführt wird, ist möglich. Auch wenn die Mechanismen und Datenformate, die von Azure Active Directory verwendet werden, nicht mit denen in Windows Server Active Directory identisch sind, sind die durchgeführten Funktionen sich doch sehr ähnlich.

Es ist wichtig, dass Sie verstehen, dass Azure Active Directory primär für Cloud-Anwendungen konzipiert wurde. Es kann beispielsweise von Anwendungen unter Azure oder auf einer anderen Cloud-Plattform verwendet werden. Es wird auch von Microsoft-eigenen Cloud-Anwendungen wie zum Beispiel Office 365 verwendet. Wenn Sie Ihr Datencenter jedoch mit Azure Virtual Machines und Azure Virtual Network auf die Cloud erweitern möchten, ist Azure Active Directory nicht die richtige Wahl. Stattdessen sollten Sie Windows Server Active Directory auf virtuellen Cloud-Computern ausführen, wie bereits zuvor beschrieben.

Damit Anwendungen auf die enthaltenen Informationen zugreifen können, bietet Azure Active Directory eine RESTful-API mit dem Namen Azure Active Directory Graph. Mit dieser API können die auf einer beliebigen Plattform ausgeführten Anwendungen auf Verzeichnisobjekte und auf die Beziehungen zwischen ihnen zugreifen. Eine autorisierte Anwendung kann diese API beispielsweise verwenden, um Informationen über einen Benutzer, über Gruppen, denen er angehört, oder um andere Informationen zu erhalten. Anwendungen können auch die Beziehungen zwischen Benutzern (den sogenannten "Social Graph") anzeigen, sodass sie intelligenter mit den Beziehungen zwischen Menschen arbeiten können.

Eine weitere Funktion dieses Dienstes ist die Azure Active Directory-Zugriffssteuerung. Sie erleichtert es Anwendungen, Identitätsinformationen von Facebook, Google, Windows Live ID und anderen beliebten Identitätsanbietern zu akzeptieren. Die Anwendung muss nicht mehr verschiedene Datenformate und Protokolle verstehen, die von diesen Anbietern verwendet werden, vielmehr übersetzt der Zugriffssteuerungsdienst alle Informationen in ein einzelnes allgemeines Format. Die Funktion ermöglicht es der Anwendung auch, Anmeldungen aus einer oder mehreren Active Directory-Domänen zu akzeptieren. So kann beispielsweise der Anbieter einer SaaS-Anwendung den Azure Active Directory-Zugriffssteuerungsdienst verwenden, um Benutzern bei jedem seiner Kunden Single-Sign-On für die Anwendung zu bieten.

Verzeichnisdienste sind eine wichtige Grundlage der lokalen Rechenleistung. Daher ist es nicht überraschend, dass dies auch in der Cloud von großer Bedeutung ist.

<h2><a id="media" ></a>Medien</h2>


Ein Großteil des Internetverkehrs besteht heutzutage aus Videos, und dieser Anteil wird in Zukunft sogar noch weiter zunehmen. Die Bereitstellung von Videos im Netz ist jedoch einfaches Unterfangen. Es gibt viele Variablen zu beachten, z. B. den Codierungsalgorithmus und die Bildschirmauflösung bei den Benutzern. Bei Videos kommt es auch zu Abrufspitzen, z. B. am Samstagabend, wenn viele Leute sich dazu entschließen, online einen Film zu schauen.

Aufgrund der Beliebtheit kann man davon ausgehen, dass viele neue Anwendungen entwickelt werden, die Videos nutzen. Doch für alle Anwendungen müssen einige der allgemeinen Probleme gelöst werden. Dabei ergibt es keinen Sinn, wenn diese Probleme für jede Anwendung neu gelöst werden. Ein besserer Ansatz wäre die Erstellung einer Plattform, die eine allgemeine Lösung bietet, die von vielen Anwendungen genutzt werden kann. Die Erstellung einer solchen Plattform in der Cloud bringt einige eindeutige Vorteile mit sich. Sie ist allgemein erhältlich und bietet ein nutzungsbasiertes Gebührenmodell. Sie kann auch die Nachfrageschwankungen solcher Videoanwendungen auffangen.

Azure Media Services löst dieses Problem. Es bietet verschiedene Cloud-Komponenten, die die Arbeit von Benutzern erleichtern, die Anwendungen mit Videos und anderen Medien erstellen und ausführen. In Abbildung 7 wird die Technologie dargestellt.

![Azure Media Services](./media/intro-to-azure/IntroAzure8.png)   
 **Abbildung 7: Media Services ist eine Plattform für Anwendungen, die Kunden rund um die Welt Videos und andere Medien zur Verfügung stellen.**

Wie die Abbildung zeigt, bietet Media Services verschiedene Komponenten für Anwendungen, die mit Videos und anderen Medien arbeiten. So umfasst es beispielsweise eine Medienübertragungskomponente zum Hochladen von Videos in Media Services (wo sie in Azure-Blobs gespeichert werden), eine Codierungskomponente, die verschiedene Video- und Audioformate unterstützt, eine Inhaltsschutzkomponente, die Komponenten zur Verwaltung digitaler Rechte bietet, eine Komponente zum Einfügen von Werbeanzeigen in einen Videostream, Komponenten für das Streaming und vieles andere mehr. Microsoft-Partner können auch Komponenten für die Plattform bereitstellen, die dann von Microsoft verteilt werden und in deren Auftrag in Rechnung gestellt werden.

Anwendungen, die diese Plattform verwenden, können unter Azure oder anderweitig ausgeführt werden. Eine Desktopanwendung für eine Videoproduktionsfirma ermöglicht es Benutzern beispielsweise, Videos in Media Services hochzuladen, und verarbeitet sie dann auf verschiedene Weise. Darüber hinaus könnte ein Cloud-basierter Inhaltsverwaltungsdienst auf Azure auf Media Services zurückgreifen, um Videos zu verarbeiten und zu verteilen. Unabhängig vom Ausführungsort und der Funktion wählt jede Anwendung die erforderlichen Komponenten und greift über RESTful-Schnittstellen darauf zu.

Zum Verteilen der produzierten Medien kann eine Anwendung das Azure CDN oder ein anderes CDN nutzen oder die Bits direkt an die Benutzer senden. Unabhängig davon, wie die Medien dorthin gelangen, können Videos, die mit Media Services erstellt werden, von verschiedenen Clientsystemen verwendet werden, darunter Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash und Silverlight. Das Ziel besteht darin, leichter moderne Medienanwendungen zu erstellen.

Für eine visuelle Darstellung der Funktionsweise von Media Services können Sie das [Azure Media Services-Poster][2] herunterladen.

<h2><a id="HPC" ></a>High Performance Computing</h2>


Eine der verlockendsten Methoden, eine Cloud-Plattform zu nutzen, ist das High Performance Computing (HPC). Die Grundlage des HPC besteht darin, dass auf vielen Computern gleichzeitig Code ausgeführt wird. Für Azure bedeutet das, dass viele virtuelle Computer gleichzeitig ausgeführt werden, die alle parallel daran arbeiten, ein Problem zu beheben. Dafür ist eine Planung der Anwendungen erforderlich, damit die Arbeit über diese Instanzen verteilt wird. Für eine solche Planung bietet Azure den HPC Scheduler.

Diese Komponente kann mit HPC-Anwendungen zusammenarbeiten, die darauf ausgelegt sind, die branchenübliche Message Passing Interface (MPI) zu nutzen. Software für eine Finite-Elemente-Analyse, z. B. Unfallsimulationen, ist nur ein Beispiel für Anwendungen dieser Art. Es gibt noch viele andere. Der HPC Scheduler kann auch mit sogenannten "embarrassingly parallel" Anwendungen verwendet werden, z. B. Monte Carlo-Simulationen. Unabhängig vom Problem, das gelöst werden soll, wird immer derselbe Wert ausgegeben: Der HPC Scheduler berücksichtigt das komplexe Problem einer Planung von parallelen Rechenarbeiten auf vielen Azure-VMs. Das Ziel ist eine leichtere Erstellung von HPC-Anwendungen, die in der Cloud ausgeführt werden.

<h2><a id="commerce" ></a>Commerce</h2>


Die wachsende Akzeptanz von Software-as-a-Service transformiert den Erstellungsprozess von Anwendungen. Auch die Art und Weise, wie wir Anwendungen verkaufen, wird dabei transformiert. Da eine SaaS-Anwendung in der Cloud lebt, bietet es sich an, dass potenzielle Kunden online nach Lösungen suchen. Diese Änderung gilt für Daten aber auch für Anwendungen. Warum sollte die Cloud nicht für käuflich verfügbare Datasets genutzt werden? Microsoft hat beide Bedenken berücksichtigt und bietet daher [Azure Marketplace][3] und [Azure Store](/en-us/store/overview/), die in Abbildung 8 dargestellt werden.

![Azure Commerce](./media/intro-to-azure/IntroAzure9.png)   
 **Abbildung 8: Über Azure Marketplace und Azure Store können Sie Azure-Anwendungen und erwerblich verfügbare Datasets suchen und kaufen.**

Der Unterschied zwischen den beiden besteht darin, dass Marketplace sich außerhalb des Azure-Verwaltungsportals befindet, wohingegen der Store über das Portal aufgerufen werden kann. Potenzielle Kunden können nach Azure-Anwendungen suchen, die ihren Anforderungen entsprechen, und diese dann direkt über den Marketplace oder Store bzw. beim Entwickler der Anwendung abonnieren. Kunden können auch nach erwerblich verfügbaren Datasets suchen, darunter auch demografische Daten, Finanzdaten, geografische Daten und mehr. Wenn sie das Gewünschte finden, können sie über den Anbieter, direkt über den Marketplace- oder die Store-Websites oder in manchen Fällen auch über das Verwaltungsportal darauf zugreifen. Anwendungen können auch die Bing Search-API über den Marketplace verwenden, sodass sie Zugriff auf die Ergebnisse von Websuchen haben.

<h2><a id="sdk" ></a>SDKs</h2>


Im Jahr 2008 unterstützte die erste Vorabversion von Azure nur die .NET-Entwicklung. Heute können Sie jedoch Azure-Anwendungen in nahezu jeder Sprache erstellen. Microsoft stellt derzeit sprachspezifische SDKs für .NET, Java, PHP, Node.js, Ruby und Python bereit. Es gibt auch ein allgemeines Azure-SDK, das grundlegende Unterstützung für jede Sprache, wie z. B. C++, bietet.

Diese SDKs helfen Ihnen dabei, Azure-Anwendungen zu erstellen, bereitzustellen und zu verwalten. Sie stehen auf www.windowsazure.com oder GitHub zur Verfügung und können mit Visual Studio und Eclipse verwendet werden. Azure bietet auch Befehlszeilentools, die Entwickler mit jedem Editor oder mit jeder Entwicklungsumgebung verwenden können, darunter auch Tools zum Bereitstellen von Anwendungen auf Azure von Linux- und Macintosh-Systemen.

Doch diese SDKs unterstützen Sie nicht nur beim Erstellen von Azure-Anwendungen, sie bieten auch Clientbibliotheken, mit denen Sie Software erstellen können, die außerhalb der Cloud ausgeführt wird und die Azure-Dienste nutzt. So können Sie beispielsweise eine Anwendung erstellen, die bei einem Host ausgeführt wird, der Azure-Blobs nutzt. Oder Sie erstellen ein Tool, das Azure-Anwendungen über die Azure-Verwaltungsschnittstelle bereitstellt.

<h2><a id="start" ></a>Erste Schritte</h2>


Nachdem Sie nun einen Überblick erhalten haben, besteht der nächste Schritt darin, Ihre erste Azure-Anwendung zu schreiben. Wählen Sie die gewünschte Sprache, [holen Sie sich das entsprechende SDK](/en-us/downloads/), und los geht\'s. Cloud-Computing ist der neue Standard -- machen Sie jetzt den ersten Schritt.



[1]: http://www.microsoft.com/en-us/download/details.aspx?id=35473
[2]: http://www.microsoft.com/en-us/download/details.aspx?id=38195
[3]: http://datamarket.azure.com/