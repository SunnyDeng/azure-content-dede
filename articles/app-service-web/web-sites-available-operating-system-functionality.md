<properties 
	pageTitle="Betriebssystemfunktionen für Web-Apps in Azure App Service" 
	description="Erfahren Sie mehr über die Funktionen des Betriebssystems, die für Webanwendungen in Azure App Service zur Verfügung stehen." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="cephalin"/>

# Betriebssystemfunktionen für Web-Apps in Azure App Service #

In diesem Artikel werden allgemeine grundlegende Betriebssystemfunktionen beschrieben, die für alle Anwendungen zur Verfügung stehen, die in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps ausgeführt werden. Diese Funktionen umfassen Zugriff auf Dateien, Netzwerke und Registrierung sowie Diagnoseprotokolle und Ereignisse.

<a id="tiers"></a>
## App Service-Planstufen

Web-Apps führen Kunden-Apps in einer Hostingumgebung mit mehreren Mandanten aus. Web-Apps mit Bereitstellung in den Modi **Free** und **Shared** werden mit Workerprozessen auf gemeinsamen virtuellen Computern ausgeführt. Web-Apps mit Bereitstellung in den Modi **Standard** und **Premium** werden hingegen auf virtuellen Computern ausgeführt, die nur für die Web-Apps eines einzelnen Kunden bestimmt sind.

Da Web-Apps eine nahtlose Skalierung zwischen unterschiedlichen Modi unterstützen, bleibt die durchgeführte Sicherheitskonfiguration für Web-Apps gleich. So wird gewährleistet, dass sich Webanwendungen nicht plötzlich anders verhalten und unerwartet ausfallen, wenn der Web-App-Modus gewechselt wird.

<a id="developmentframeworks"></a>
## Entwicklungsframeworks

Web-App-Modi steuern die Rechnerressourcen (CPU, Datenspeicher, Speicherplatz und Netzwerkausgang), die Web-Apps zur Verfügung stehen. Der Umfang verfügbarer Frameworkfunktionen für Anwendungen bleibt jedoch gleich, unabhängig vom Web-App-Modus.

Web-Apps bieten Unterstützung für eine Vielzahl an Entwicklungsframeworks, einschließlich ASP.NET, klassischem ASP, Node.js, PHP und Python. Diese werden alle als Erweiterungen innerhalb von IIS ausgeführt. Web-Apps führen die verschiedenen Entwicklungsframeworks mit deren Standardeinstellungen aus, um die Sicherheitskonfiguration zu vereinfachen und zu normalisieren. Ein Ansatz für die Konfiguration von Web-Apps hätte die Anpassung des API-Oberflächenbereichs und der Funktionen für jedes einzelne Entwicklungsframework sein können. Stattdessen ist der Ansatz von Web-Apps allgemeiner. Es wird eine allgemeine Grundlage der Betriebssystemfunktionen ermöglicht, unabhängig vom Anwendungsentwicklungsframework einer Web-App.

In den folgenden Abschnitten werden die allgemeinen Betriebssystemfunktionen für Web-Apps in Azure zusammengefasst.

<a id="FileAccess"></a>
##Dateizugriff

In Web-Apps gibt es viele Laufwerke, einschließlich lokaler Laufwerke und Netzwerklaufwerke.

<a id="LocalDrives"></a>
### Lokale Laufwerke

Im Kern ist eine Web-App ein Dienst, der auf der Azure-PaaS-Infrastruktur (Platform-as-a-Service) ausgeführt wird. Daher sind die lokalen Laufwerke, die an einen virtuellen Computer "angehängt" sind, die gleichen Laufwerkstypen wie die für jede in Azure ausgeführte Workerrolle verfügbaren Typen. Dies umfasst ein Betriebssystemlaufwerk (Laufwerk "D:\"), ein Anwendungslaufwerk, das Azure-Paketdateien (CSPKG-Dateien) enthält, die ausschließlich von Web-Apps verwendet werden (Kunden können darauf nicht zugreifen), und ein "Benutzer"-Laufwerk (Laufwerk "C:\"), dessen Umfang von der Größe des virtuellen Computers abhängt.

<a id="NetworkDrives"></a>
### Netzwerklaufwerke (oder auch UNC-Freigaben)

Ein Alleinstellungsmerkmal von Web-Apps, das die Webanwendungsbereitstellung und -wartung so unkompliziert macht, ist die Tatsache, dass alle Benutzerinhalte auf einem Satz an UNC-Freigaben gespeichert werden. Dieses Modell passt sehr gut zum allgemeinen Muster der Inhaltsspeicherung, das von lokalen Webhostingumgebungen verwendet wird, die über mehrere Server mit Lastenausgleich verfügen.

In den Web-Apps werden mehrere UNC-Freigaben in jedem Rechenzentrum erstellt. Ein Prozentsatz der Benutzerinhalte aller Kunden in jedem Rechenzentrum wird auf alle UNC-Freigaben verteilt. Außerdem werden alle Dateiinhalte für das Abonnement eines Kunden immer auf derselben UNC-Freigabe abgelegt.

Beachten Sie, dass sich aufgrund der Funktionsweise von Clouddiensten der spezifische virtuelle Computer, der die UNC-Freigabe hostet, im Laufe der Zeit ändert. Es wird gewährleistet, dass UNC-Freigaben von unterschiedlichen virtuellen Computern bereitgestellt werden, da diese während des normalen Verlaufs von Cloudvorgängen ein- und ausgeschaltet werden. Aus diesem Grund sollten Anwendungen nicht fest codiert annehmen, dass die Computerinformationen in einem UNC-Dateipfad immer stabil bleiben. Stattdessen muss der praktische *pseudoabsolute* Pfad **D:\\home\\site** verwendet werden, den Web-Apps zur Verfügung stellen. Dieser pseudoabsolute Pfad bietet eine portable, Web-App- und benutzeragnostische Methode für die Verknüpfung zur eigenen App. Wenn Sie **D:\\home\\site** verwenden, können Sie freigegebene Dateien von App zu App übertragen, ohne für jede Übertragung einen neuen absoluten Pfad konfigurieren zu müssen.

<a id="TypesOfFileAccess"></a>
### Dateizugriffstypen für eine Webanwendung

Jedes Abonnement eines Kunden verfügt über eine reservierte Verzeichnisstruktur auf einer bestimmten UNC-Freigabe innerhalb eines Rechenzentrums. Ein Kunde kann über mehrere Web-Apps verfügen, die in einem bestimmten Rechenzentrum erstellt werden. Alle Verzeichnisse, die zu einem Abonnement eines Kunden gehören, werden daher auf derselben UNC-Freigabe erstellt. In der Freigabe können Verzeichnisse für Inhalt, Fehler- und Diagnoseprotokolle und frühere Versionen der Web-App, erstellt von der Quellcodeverwaltung, enthalten sein. Wie erwartet, sind Web-App-Verzeichnisse eines Kunden für Lese- und Schreibzugriff während der Laufzeit über den Anwendungscode der Web-App verfügbar.

Auf den lokalen Laufwerken, die zum virtuellen Computer gehören, auf dem eine Webanwendung ausgeführt wird, reserviert die Web-App Speicherplatz auf Laufwerk C:\\ für eine App-spezifische, temporäre lokale Speicherung. Obwohl eine Web-App vollständigen Lese- und Schreibzugriff für den eigenen temporären lokalen Speicher besitzt, ist dieser Speicherplatz nicht dazu gedacht, direkt von Anwendungscode verwendet zu werden. Stattdessen dient er dem Zweck, temporären Dateispeicher für IIS und Webanwendungsframeworks bereitzustellen. Web-Apps beschränken außerdem den temporären lokalen Speicherplatz für jede Webanwendung, damit einzelne Apps nicht übermäßig viel lokalen Speicherplatz verbrauchen können.

Zwei Beispiele dazu, wie Web-Apps temporären lokalen Speicherplatz verwenden, sind das Verzeichnis für ASP.NET-Dateien und das Verzeichnis für komprimierte IIS-Dateien. Das Kompilierungssystem von ASP.NET verwendet das Verzeichnis "Temporary ASP.NET Files" als temporären Speicherort für den Kompilierungscache. IIS verwenden das Verzeichnis "IIS Temporary Compressed Files", um komprimierte Rückmeldungen zu speichern. Diese beiden Typen der Dateinutzung (und weitere) werden in Web-Apps über temporären lokalen Speicherplatz pro App neu zugewiesen. Durch diese Neuzuweisung wird eine durchgängige Funktionalität gewährleistet.

Jede App in Web-Apps wird als zufällige, eindeutige Workerrollenidentität mit geringen Berechtigungen ausgeführt. Weitere Informationen zu dieser als Anwendungspoolidentität bezeichneten Identität finden Sie unter folgendem Link: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities) (in englischer Sprache). Anwendungscode nutzt diese Identität für grundlegenden schreibgeschützten Zugriff auf das Betriebssystemlaufwerk (Laufwerk D:\\). Das bedeutet, Anwendungscode kann allgemeine Verzeichnisstrukturen auflisten und allgemeine Dateien auf dem Betriebssystemlaufwerk lesen. Dies erscheint zwar wie ein sehr umfassender Zugriff, dieselben Verzeichnisse und Dateien sind jedoch zugänglich, wenn Sie in einem von Azure gehosteten Dienst eine Workerrolle bereitstellen und die Laufwerksinhalte lesen.

<a name="multipleinstances"></a>
### Dateizugriff über mehrere Instanzen

Das Basisverzeichnis enthält den Inhalt einer App, und Webanwendungen können darin schreiben. Wenn eine Web-App auf mehreren Instanzen ausgeführt wird, wird das Homeverzeichnis auf alle Instanzen verteilt, sodass für alle Instanzen das gleiche Verzeichnis angezeigt wird. Wenn also beispielsweise eine Web-App hochgeladene Dateien im Homeverzeichnis speichert, sind diese Dateien sofort für alle Instanzen verfügbar.

<a id="NetworkAccess"></a>
## Netzwerkzugriff
Anwendungscode kann TCP/IP- und UDP-basierte Protokolle verwenden, um ausgehende Verbindungen zu zugänglichen Endpunkten im Internet herzustellen, die externe Dienste bereitstellen. Anwendungen können die gleichen Protokolle verwenden, um eine Verbindung mit Diensten innerhalb von Azure herzustellen, beispielsweise HTTPS-Verbindungen zu SQL Azure.

Es ist außerdem eine eingeschränkte Funktion für Anwendungen vorhanden, mit der eine lokale Loopbackverbindung erstellt werden kann und die Anwendungen über dieses Loopbacksocket empfängt. Diese Funktion dient vor allem dazu, Anwendungen zu ermöglichen, die als Teil ihrer Funktionalität über lokale Loopbacksockets empfangen. Beachten Sie, dass in der Anwendungen eines jeden Kunden eine "private" Loopbackverbindung angezeigt wird. Anwendung "A" kann nicht über ein lokales Loopbacksocket empfangen, das von Anwendung "B" eingerichtet wurde.

Named Pipes werden auch als Mechanismus für die Kommunikation zwischen Prozessen (IPC) unterstützt. Dies umfasst unterschiedliche Prozesse, die zusammen eine Web-App ausführen. Beispielsweise nutzt das IIS FastCGI-Modul Named Pipes für die Koordinierung der individuellen Prozesse, die PHP-Websites ausführen.


<a id="Code"></a>
## Codeausführung, Prozesse und Speicher
Wie bereits zuvor erwähnt, werden Web-Apps innerhalb von Workerprozessen mit geringen Berechtigungen und mit einer zufälligen Anwendungspoolidentität ausgeführt. Anwendungscode besitzt Zugriff auf den Speicherplatz, der zum Workerprozess gehört, sowie alle untergeordneten Prozesse, die aus CGI-Prozessen oder anderen Anwendungen stammen. Die Web-App eines Kunden kann jedoch nicht auf den Speicher oder die Daten einer Web-App eines anderen Kunden zugreifen, selbst wenn diese auf demselben virtuellen Computer ausgeführt werden.

Anwendungen können Skripts oder Seiten ausführen, die mit unterstützten Webanwendungsframeworks erstellt wurden. Web-Apps konfigurieren die Einstellungen von Webanwendungsframeworks nicht auf eingeschränktere Modi. Beispielsweise werden ASP.NET-Apps, die in Web-Apps ausgeführt werden, als voll vertrauenswürdig statt auf eingeschränkter Vertrauensebene ausgeführt. Anwendungsframeworks, einschließlich klassischem ASP und ASP.NET, können prozessinterne COM-Komponenten aufrufen (jedoch keine prozessexternen COM-Komponenten), zum Beispiel ADO (ActiveX Data Objects), die standardmäßig auf dem Windows-Betriebssystem registriert sind.

Webanwendungen können willkürlichen Code erzeugen und ausführen. Es ist zulässig, dass Webanwendungen beispielsweise eine Befehlsshell erzeugen oder ein PowerShell-Skript ausführen. Obwohl willkürlicher Code und willkürliche Prozesse von einer Webanwendung erzeugt werden können, sind ausführbare Programme und Skripte jedoch immer durch die Berechtigungen beschränkt, die der übergeordnete Anwendungspool besitzt. Beispielsweise kann eine Web-App ein ausführbares Programm erzeugen, das einen ausgehenden HTTP-Aufruf ausführt. Dieses ausführbare Programm kann jedoch nicht versuchen, die IP-Adresse eines virtuellen Computers von deren NIC loszulösen. Die Durchführung eines ausgehenden Netzwerkaufrufes ist für Code mit geringen Berechtigungen zulässig, der Versuch, die Netzwerkeinstellungen auf einem virtuellen Computer zu konfigurieren erfordert jedoch Administratorberechtigungen.


<a id="Diagnostics"></a>
## Diagnoseprotokolle und Ereignisse
Bei Protokollinformationen handelt es sich um einen weiteren Datensatz, auf den einige Webanwendungen versuchen, zuzugreifen. Die Typen von Protokollinformationen, die für in Web-Apps ausgeführten Code verfügbar sind, umfassen Diagnose- und Protokollinformationen, die von einer Web-App generiert wurden, und auf die Web-Apps einfach zugreifen können.

Beispielsweise sind W3C-HTTP-Protokolle, die von einer aktiven Web-App generiert werden, entweder in einem Protokollverzeichnis in der Netzwerkfreigabe, die für die Web-App erstellt wurde, verfügbar oder im Blobspeicher, wenn ein Kunde W3C-Protokollierung in einem Speicher eingerichtet hat. Die zweite Option ermöglicht es Ihnen, große Mengen an Protokollen zu sammeln, ohne die Dateispeicherbeschränkungen der Netzwerkfreigabe zu überschreiten.

Ebenso können Echtzeitdiagnoseinformationen aus .NET-Anwendungen protokolliert werden. Dies ist möglich mit der Nachverfolgungs- und Diagnosestruktur von .NET, wobei die Nachverfolgungsinformationen entweder auf die Netzwerkfreigabe der Web-App oder an einen Blobspeicherort geschrieben werden können.

Bereiche der Diagnoseprotokollierung und Nachverfolgung, die für Webanwendungen in Azure nicht verfügbar sind, sind Windows-ETW-Ereignisse und Windows-Ereignisprotokolle (zum Beispiel System-, Anwendungs- und Sicherheitsereignisprotokolle). Da ETW-Nachverfolgungsinformationen potenziell computerweit sichtbar sind (mit den entsprechenden ACLs), sind Lese- und Schreibzugriff auf ETW-Ereignisse blockiert. Entwickler erkennen wahrscheinlich, dass API-Aufrufe für das Schreiben und Lesen von ETW-Ereignissen und allgemeinen Windows-Ereignisprotokollen scheinbar funktionieren. Dies liegt jedoch daran, dass Web-Apps die Aufrufe "fälschen", sodass sie scheinbar erfolgreich ausgeführt werden. Tatsächlich erhält Web-App-Code keinen Zugriff auf diese Ereignisdaten.

<a id="RegistryAccess"></a>
## Registrierungszugriff
Anwendungen verfügen über schreibgeschützten Zugriff auf einen großen Teil des Verzeichnisses des virtuellen Computers (jedoch nicht auf das gesamte), auf dem sie ausgeführt werden. In der Praxis bedeutet das, Registrierungsschlüssel, die schreibgeschützten Zugriff auf lokale Benutzergruppen gewähren, sind für Webanwendungen zugänglich. Ein Bereich der Registrierung, der aktuell nicht für Lese- oder Schreibzugriff unterstützt wird, ist die Struktur HKEY\_CURRENT\_USER.

Der Schreibzugriff auf die Registrierung ist blockiert, einschließlich des Zugriffs auf benutzerbezogene Registrierungsschlüssel. Aus Sicht einer Anwendung sollte in einer Cloudumgebung nie von Schreibzugriff auf eine Registrierung ausgegangen werden, da Anwendungen über unterschiedliche virtuelle Computer migriert werden können (und dies auch geschieht). Der einzige dauerhaft beschreibbare Speicher, auf den sich eine Webanwendung stützen kann, ist die Inhaltsverzeichnisstruktur jeder Web-App, die auf UNC-Freigaben von Web-Apps gespeichert wird.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 

<!---HONumber=August15_HO6-->