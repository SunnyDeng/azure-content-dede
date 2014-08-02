<properties linkid="manage-services-storage-net-shared-access-signature-part-2" urlDisplayName="Shared Access Signature Part 2" pageTitle="Shared Access Signatures, Part 2: Create and Use a SAS with the Blob Service" metaKeywords="" description="Learn how to generate and then use shared access signatures with the Azure Blob service. The examples are written in C# and use the Azure Storage Client Library for .NET." metaCanonical="" services="storage" documentationCenter=".NET" title="Operating System Functionality Available to Applications on Azure Web Sites" authors="timamm" solutions="" writer="timamm" manager="" editor="" />

Betriebssystemfunktionen für Anwendungen in Azure-Websites
==========================================================

In diesem Artikel werden allgemeine grundlegende Betriebssystemfunktionen beschrieben, die für Anwendungen zur Verfügung stehen, welche über Azure-Websites ausgeführt werden. Diese Funktionen umfassen Zugriff auf Dateien, Netzwerke und Verzeichnisse sowie Diagnoseprotokolle und Ereignisse.

Inhaltsverzeichnis
------------------

-   [Websitemodi](#websitemodes)
-   [Entwicklungsframeworks](#developmentframeworks)
-   [Dateizugriff](#FileAccess)
    -   [Lokale Laufwerke](#LocalDrives)
    -   [Netzwerklaufwerke (oder auch UNC-Freigaben)](#NetworkDrives)
    -   [Dateizugriff über mehrere Instanzen](#multipleinstances)
    -   [Typen von Dateizugriff für eine Webanwendung](#TypesOfFileAccess)
-   [Netzwerkzugriff](#NetworkAccess)
-   [Codeausführung, Prozesse und Speicher](#Code)
-   [Diagnoseprotokolle und Ereignisse](#Diagnostics)
-   [Verzeichniszugriff](#RegistryAccess)

Websitemodi
-----------

Mit Azure-Websites werden Kundenwebsites in einer mehrinstanzenfähigen Hostingumgebung ausgeführt. Websites mit Bereitstellung im Free- und Shared-Skalierungsmodus werden mit Workerprozessen auf gemeinsamen virtuellen Computern ausgeführt. Websites mit Bereitstellung im standardmäßigen Websiteskalierungsmodus werden hingegen auf virtuellen Computern ausgeführt, die nur für die Websites eines einzelnen Kunden bestimmt sind.

Da Azure-Websites eine nahtlose Skalierung zwischen unterschiedlichen Modi unterstützt, bleibt die durchgeführte Sicherheitskonfiguration für Websites gleich. So wird gewährleistet, dass sich Webanwendungen nicht plötzlich anders verhalten und unerwartet ausfallen, wenn zwischen einem Websitemodus und einem anderen umgeschaltet wird.

Entwicklungsframeworks
----------------------

Websitemodi steuern die Rechnerressourcen (CPU, Datenspeicher, Speicherplatz und Netzwerkausgang), die Websites zur Verfügung stehen. Der Umfang verfügbarer Frameworkfunktionen für Anwendungen bleibt jedoch gleich, unabhängig vom Websitemodus.

Azure-Websites unterstützt eine Vielzahl an Entwicklungsframeworks, einschließlich ASP.NET, klassischem ASP, Node.js, PHP und Python. Diese werden alle als Erweiterungen innerhalb von IIS ausgeführt. Azure-Websites führt die verschiedenen Entwicklungsframeworks mit deren Standardeinstellungen aus, um die Sicherheitskonfiguration zu vereinfachen und zu normalisieren. Ein Ansatz für die Konfiguration von Azure-Websites wäre die Anpassung des API-Oberflächenbereichs und der Funktionen für jedes einzelne Entwicklungsframework. Stattdessen ist der Ansatz von Azure-Websites allgemeiner. Es wird eine allgemeine Grundlage der Betriebssystemfunktionen ermöglicht, unabhängig vom Anwendungsentwicklungsframework einer Website.

In den folgenden Abschnitten werden die allgemeinen Betriebssystemfunktionen für Websites in Azure zusammengefasst.

Dateizugriff
------------

In Azure-Websites gibt es viele Laufwerke, einschließlich lokaler Laufwerke und Netzwerklaufwerke.

### Lokale Laufwerke

Im Kern ist Azure-Websites ein Dienst, der zusätzlich zur Azure-PaaS (Platform-as-a-Service)-Infrastruktur ausgeführt wird. Daher sind die lokalen Laufwerke, die an einen virtuellen Computer "angehängt" sind, die gleichen Laufwerkstypen wie die für jede in Azure ausgeführte Workerrolle verfügbaren Typen. Dies umfasst ein Betriebssystemlaufwerk (Laufwerk D:\\), ein Anwendungslaufwerk, das Azure-Paketdateien (cspkg-Dateien) enthält, die ausschließlich von Azure-Websites verwendet werden (Kunden können darauf nicht zugreifen) und ein "Benutzer"-Laufwerk (Laufwerk C:\\), dessen Umfang von der Größe des virtuellen Computers abhängt.

### Netzwerklaufwerke (oder auch UNC-Freigaben)

Ein Alleinstellungsmerkmal von Azure-Websites, das Anwendungsentwicklung und Wartung so unkompliziert macht: Alle Benutzerinhalte werden auf einem Satz UNC-Freigaben gespeichert. Dieses Modell passt sehr gut zum allgemeinen Muster der Inhaltsspeicherung, das von lokalen Webhostingumgebungen verwendet wird, die über mehrere Server mit Lastenausgleich verfügen.

In Azure-Websites werden mehrere UNC-Freigaben in jedem Rechenzentrum erstellt. Ein Prozentsatz der Benutzerinhalte aller Kunden in jedem Rechenzentrum wird auf alle UNC-Freigaben verteilt. Außerdem werden alle Dateiinhalte für das Abonnement eines Kunden immer auf derselben UNC-Freigabe abgelegt.

Beachten Sie, dass sich aufgrund der Funktionsweise von Clouddiensten der spezifische virtuelle Computer, der die UNC-Freigabe hostet, im Laufe der Zeit ändert. Es wird gewährleistet, dass UNC-Freigaben von unterschiedlichen virtuellen Computern bereitgestellt werden, da diese während des normalen Verlaufs von Cloudvorgängen ein- und ausgeschaltet werden. Aus diesem Grund sollten Anwendungen nicht fest codiert annehmen, dass die Computerinformationen in einem UNC-Dateipfad immer stabil bleiben. Stattdessen sollte der praktische *pseudo*absolute Pfad **D:\\home\\site** verwendet werden, den Azure-Websites zur Verfügung stellt. Dieser pseudoabsolute Pfad bietet eine portable, website- und benutzeragnostische Methode für die Verknüpfung zur eigenen Website. Wenn Sie **D:\\home\\site** verwenden, können Sie freigegebene Dateien von Website zu Website übertragen, ohne für jede Übertragung einen neuen absoluten Pfad konfigurieren zu müssen.

### Typen von Dateizugriff für eine Webanwendung

Jedes Abonnement eines Kunden verfügt über eine reservierte Verzeichnisstruktur auf einer bestimmten UNC-Freigabe innerhalb eines Rechenzentrums. Ein Kunde kann über mehrere Websites verfügen, die in einem bestimmten Rechenzentrum erstellt werden. Alle Verzeichnisse, die zu einem Abonnement eines Kunden gehören, werden daher auf derselben UNC-Freigabe erstellt. In der Freigabe können Verzeichnisse für Inhalt, Fehler- und Diagnoseprotokolle und frühere Versionen der Website, erstellt von der Quellcodeverwaltung, enthalten sein. Wie erwartet, sind Websiteverzeichnisse eines Kunden für Lese- und Schreibzugriff während der Laufzeit über den Anwendungscode der Website verfügbar.

Auf den lokalen Laufwerken, die zum virtuellen Computer gehören, auf dem eine Website ausgeführt wird, reserviert Azure-Websites einen Anteil an Speicherplatz auf dem Laufwerk C:\\ für websitespezifische, temporäre lokale Speicherung. Obwohl Websites vollständigen Lese- und Schreibzugriff für den eigenen temporären lokalen Speicher besitzen, ist dieser Speicherplatz nicht dazu gedacht, direkt von Anwendungscode verwendet zu werden. Stattdessen dient er dem Zweck, temporären Dateispeicher für IIS und Webanwendungsframeworks bereitzustellen. Azure-Websites beschränkt außerdem den temporären lokalen Speicherplatz für jede Website, damit einzelne Websites nicht übermäßig viel lokalen Speicherplatz verbrauchen können.

Zwei Beispiele dazu, wie Azure-Websites temporären lokalen Speicherplatz verwendet, sind das Verzeichnis für ASP.NET und das Verzeichnis für komprimierte Dateien aus IIS. Das Kompilierungssystem von ASP.NET verwendet das Verzeichnis "Temporary ASP.NET Files" als temporären Speicherort für den Kompilierungscache. IIS verwenden das Verzeichnis "IIS Temporary Compressed Files", um komprimierte Rückmeldungen zu speichern. Diese beiden Typen der Dateinutzung (und weitere) werden in Azure-Websites über temporären lokalen Speicherplatz pro Website neu zugewiesen. Durch diese Neuzuweisung wird eine durchgängige Funktionalität gewährleistet.

Jede Website in Azure-Websites führt eine zufällige, eindeutige Workerrollenidentität mit geringen Berechtigungen aus, die "Anwendungspoolidentität" heißt, und hier beschrieben wird (in englischer Sprache): <http://www.iis.net/learn/manage/configuring-security/application-pool-identities>. Anwendungscode nutzt diese Identität für grundlegenden schreibgeschützten Zugriff auf das Betriebssystemlaufwerk (Laufwerk D:\\). Das bedeutet, Anwendungscode kann allgemeine Verzeichnisstrukturen auflisten und allgemeine Dateien auf dem Betriebssystemlaufwerk lesen. Dies erscheint zwar wie ein sehr umfassender Zugriff, dieselben Verzeichnisse und Dateien sind jedoch zugänglich, wenn Sie in einem von Azure gehosteten Dienst eine Workerrolle bereitstellen und die Laufwerksinhalte lesen.

### Dateizugriff über mehrere Instanzen

Das Homeverzeichnis enthält den Inhalt einer Website, und Webanwendungen können darauf schreiben. Wenn eine Website auf mehreren Instanzen ausgeführt wird, wird das Homeverzeichnis auf alle Instanzen verteilt, sodass für alle Instanzen das gleiche Verzeichnis angezeigt wird. Wenn also beispielsweise eine Website hochgeladene Dateien im Homeverzeichnis speichert, sind diese Dateien sofort für alle Instanzen verfügbar.

Netzwerkzugriff
---------------

Anwendungscode kann TCP/IP- und UDP-basierte Protokolle verwenden, um ausgehende Verbindungen zu zugänglichen Endpunkten im Internet herzustellen, die externe Dienste bereitstellen. Anwendungen können die gleichen Protokolle verwenden, um eine Verbindung mit Diensten innerhalb von Azure herzustellen, beispielsweise HTTPS-Verbindungen zu SQL-Azure.

Es ist außerdem eine eingeschränkte Funktion für Anwendungen vorhanden, mit der eine lokale Loopbackverbindung erstellt werden kann und die Anwendungen über dieses Loopbacksocket empfängt. Diese Funktion dient vor allem dazu, Anwendungen zu ermöglichen, die als Teil ihrer Funktionalität über lokale Loopbacksockets empfangen. Beachten Sie, dass in der Anwendungen eines jeden Kunden eine "private" Loopbackverbindung angezeigt wird. Anwendung "A" kann nicht über ein lokales Loopbacksocket empfangen, das von Anwendung "B" eingerichtet wurde.

Named Pipes werden auch als Mechanismus für die Kommunikation zwischen Prozessen (IPC) unterstützt. Dies umfasst unterschiedliche Prozesse, die zusammen eine Website ausführen. Beispielsweise nutzt das IIS FastCGI-Modul Named Pipes für die Koordinierung der individuellen Prozesse, die PHP-Websites ausführen.

Codeausführung, Prozesse und Speicher
-------------------------------------

Wie bereits zuvor erwähnt, werden Websites innerhalb von Workerprozessen mit geringen Berechtigungen und mit einer zufälligen Anwendungspoolidentität ausgeführt. Anwendungscode besitzt Zugriff auf den Speicherplatz, der zum Workerprozess gehört, sowie alle untergeordneten Prozesse, die aus CGI-Prozessen oder anderen Anwendungen stammen. Anwendungen von einer Website können jedoch nicht auf den Speicher oder die Daten einer Website eines anderen Kunden zugreifen, selbst wenn diese auf demselben virtuellen Computer ausgeführt werden.

Anwendungen können Skripts oder Seiten ausführen, die mit unterstützten Webanwendungsframeworks erstellt wurden. Azure-Websites konfiguriert die Einstellungen von Webanwendungsframeworks nicht auf eingeschränktere Modi. Beispielsweise werden ASP.NET-Websites, die in Azure-Websites ausgeführt werden, als voll vertrauenswürdig statt auf eingeschränkter Vertrauensebene ausgeführt. Anwendungsframeworks, einschließlich klassischem ASP und ASP.NET, können prozessinterne COM-Komponenten aufrufen (jedoch keine prozessexternen COM-Komponenten), zum Beispiel ADO (ActiveX Data Objects), die standardmäßig auf dem Windows-Betriebssystem registriert sind.

Webanwendungen können willkürlichen Code erzeugen und ausführen. Es ist zulässig, dass Webanwendungen beispielsweise eine Befehlsshell erzeugen oder ein PowerShell-Skript ausführen. Obwohl willkürlicher Code und willkürliche Prozesse von einer Webanwendung erzeugt werden können, sind ausführbare Programme und Skripte jedoch immer durch die Berechtigungen beschränkt, die der übergeordnete Anwendungspool besitzt. Beispielsweise kann eine Website ein ausführbares Programm erzeugen, das einen ausgehenden HTTP-Aufruf ausführt. Dieses ausführbare Programm kann jedoch nicht versuchen, die IP-Adresse eines virtuellen Computers von deren NIC loszulösen. Die Durchführung eines ausgehenden Netzwerkaufrufes ist für Code mit geringen Berechtigungen zulässig, der Versuch, die Netzwerkeinstellungen auf einem virtuellen Computer zu konfigurieren erfordert jedoch Administratorberechtigungen.

Diagnoseprotokolle und Ereignisse
---------------------------------

Bei Protokollinformationen handelt es sich um einen weiteren Datensatz, auf den einige Webanwendungen versuchen, zuzugreifen. Die Typen von Protokollinformationen, die für in Azure ausgeführten Code verfügbar sind, umfassen diagnostische und Protokollinformationen, die von einer Website generiert wurden, und auf die Websites einfach zugreifen können.

Beispielsweise sind W3C-HTTP-Protokolle, die von einer aktiven Website generiert werden, entweder in einem Protokollverzeichnis in der Netzwerkfreigabe, die für die Website erstellt wurde, verfügbar oder im Blob-Speicher, wenn ein Kunde W3C-Protokollierung in einem Speicher eingerichtet hat. Die zweite Option ermöglicht es Ihnen, große Mengen an Protokollen zu sammeln, ohne die Dateispeicherbeschränkungen der Netzwerkfreigabe zu überschreiten.

Ebenso können Echtzeitdiagnoseinformationen aus .NET-Anwendungen protokolliert werden. Dies ist möglich mit der Nachverfolgungs- und Diagnosestruktur von .NET, wobei die Nachverfolgungsinformationen entweder auf die Netzwerkfreigabe der Website oder in einen Blob-Speicherort geschrieben werden können.

Bereiche der Diagnoseprotokollierung und Nachverfolgung, die für Webanwendungen in Azure nicht verfügbar sind, sind Windows-ETW-Ereignisse und Windows-Ereignisprotokolle (zum Beispiel System-, Anwendungs- und Sicherheitsereignisprotokolle). Da ETW-Nachverfolgungsinformationen potenziell computerweit sichtbar sind (mit den entsprechenden ACLs), sind Lese- und Schreibzugriff auf ETW-Ereignisse blockiert. Entwickler erkennen wahrscheinlich, dass API-Aufrufe für Schreiben und Lesen von ETW-Ereignissen und allgemeinen Windows-Ereignisprotokollen scheinbar funktionieren. Dies liegt jedoch daran, dass Azure-Websites die Aufrufe "fälscht", sodass sie scheinbar erfolgreich ausgeführt werden. Tatsächlich erhält Websiteanwendungscode keinen Zugriff auf diese Ereignisdaten.

Verzeichniszugriff
------------------

Anwendungen verfügen über schreibgeschützten Zugriff auf einen großen Teil des Verzeichnisses des virtuellen Computers (jedoch nicht auf das gesamte), auf dem sie ausgeführt werden. In der Praxis bedeutet das, Registrierungsschlüssel, die schreibgeschützten Zugriff auf lokale Benutzergruppen gewähren, sind für Webanwendungen zugänglich. Ein Bereich des Verzeichnisses, der aktuell nicht für Lese- oder Schreibzugriff unterstützt wird, ist die Struktur HKEY\_CURRENT\_USER.

Der Schreibzugriff auf das Verzeichnis ist blockiert, einschließlich des Zugriffs auf benutzerbezogene Registrierungsschlüssel. Aus Sicht der Anwendung sollte sich in einer Cloudumgebung nie auf Schreibzugriff auf ein Verzeichnis gestützt werden, da Anwendungen über unterschiedliche virtuelle Computer migriert werden können (und dies auch geschieht). Der einzige dauerhaft beschreibbare Speicher, auf den sich eine Webanwendung stützen kann, ist die Inhaltsverzeichnisstruktur jeder Website, die auf UNC-Freigaben von Azure-Websites gespeichert wird.

