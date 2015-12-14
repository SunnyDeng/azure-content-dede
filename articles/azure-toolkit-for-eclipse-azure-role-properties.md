<properties
    pageTitle="Eigenschaften der Azure-Rolle"
    description="Hier erfahren Sie, wie Sie die Einstellungen für die Azure-Rolle mithilfe des Azure-Toolkits für Eclipse konfigurieren."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/20/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/de-DE/library/azure/hh690945.aspx -->

# Eigenschaften der Azure-Rolle #

Im Azure-Toolkit für Eclipse können verschiedene Konfigurationseinstellungen für Ihre Azure-Rolle festgelegt werden.

## Konfigurieren von Eigenschaften für die Azure-Rolle ##

Die Eigenschaften Ihrer Azure-Rolle werden über die Eigenschaftsdialogfelder für Ihre Workerrolle konfiguriert. Öffnen Sie im Projekt-Explorer-Bereich von Eclipse das Kontextmenü für die Rolle, und wählen Sie das Untermenü **Azure** aus. (Sollte die Rolle im Projekt-Explorer von Eclipse nicht angezeigt werden, erweitern Sie Ihr Azure-Projekt im Projekt-Explorer.)

![][ic789599]

In diesem Thema werden die verschiedenen Eigenschaften beschrieben, die in den Eigenschaftendialogfeldern festgelegt werden können. Beachten Sie, dass viele Eigenschaften automatisch ausgefüllt werden, wenn Sie ein neues Azure-Bereitstellungsprojekt erstellen.

Die folgenden Eigenschaftenseiten stehen für Azure-Rollen zur Verfügung:

* [Eigenschaften für virtuelle Maschinen](#virtual_machine_properties)
* [Caching-Eigenschaften](#caching_properties)
* [Eigenschaften für Zertifikate](#certificates_properties)
* [Eigenschaften für Komponenten](#components_properties)
* [Debuggingeigenschaften](#debugging_properties)
* [Eigenschaften für Endpunkte](#endpoints_properties)
* [Eigenschaften für Umgebungsvariablen](#environment_variables_properties)
* [Eigenschaften für den Lastenausgleich/für die Sitzungsaffinität (bzw. persistente Sitzungen)](#session_affinity_properties)
* [Eigenschaften für lokalen Speicher](#local_storage_properties)
* [Eigenschaften für die Serverkonfiguration](#server_configuration_properties)
* [SSL-Abladungseigenschaften](#ssl_offloading_properties)
	
<a name="virtual_machine_properties"></a>
### Eigenschaften für virtuelle Maschinen ###

Öffnen Sie im Projekt-Explorer-Bereich von Eclipse das Kontextmenü für die Rolle. Klicken Sie auf **Azure** und anschließend auf **Eigenschaften**. Hier können Sie die Größe der virtuellen Maschine sowie die Anzahl der Instanzen ändern, wie in der folgenden Abbildung zu sehen:

![][ic719499]

>[AZURE.NOTE]Nur Windows: Wenn Sie die Anzahl der Instanzen auf einen Wert größer 1 festlegen und auch einen Anwendungsserver konfigurieren, lässt das Toolkit unabhängig von dieser Einstellung nur die Ausführung einer einzelnen Rolleninstanz im Emulator zu. Dadurch werden Portbindungskonflikte zwischen den verschiedenen Serverinstanzen vermieden, wenn diese auf dem gleichen Computer ausgeführt werden und beispielsweise alle versuchen, eine Bindung mit Port 8080 herzustellen. Die gewünschte Einstellung für die Instanzenanzahl wird gespeichert, aber bei der Bereitstellung in der Cloud wirksam.

<a name="caching_properties"></a>
### Caching-Eigenschaften ###

Öffnen Sie im Projekt-Explorer-Bereich von Eclipse das Kontextmenü für die Rolle. Klicken Sie auf **Azure** und anschließend auf **Caching**. In diesem Dialogfeld können Sie zur Beschleunigung Ihrer Webanwendungen benannte, zusammengestellte, Memcache-kompatible Caches aktivieren.

![][ic719483]

Auf der Eigenschaftenseite **Caching** können Sie globale Einstellungen für Folgendes angeben:

* Aktivierung von zusammengestelltem Caching
* Cachegröße als Prozentsatz des Arbeitsspeichers
* Name des Speicherkontos zum Speichern des Cachezustands, wenn Ihre Anwendung als Clouddienst ausgeführt wird (oder keine Angabe, wenn Sie den Cachezustand nicht speichern möchten). (Beim Ausführen der Anwendung im Serveremulator wird der Name des Speicherkontos nicht verwendet.) Wenn Sie den Namen des Speicherkontos auf **(Auto)** (Standardeinstellung) festlegen, wird für die Cachekonfiguration automatisch das im Dialogfeld **Veröffentlichen in Azure** ausgewählte Speicherkonto verwendet.

>[AZURE.NOTE]Die Einstellung **(Auto)** funktioniert nur dann wie gewünscht, wenn Sie Ihre Bereitstellung mit dem Bereitstellungs-Assistenten des Eclipse-Toolkits veröffentlichen. Wenn Sie die CSPKG-Datei stattdessen manuell (etwa über das [Azure-Verwaltungsportal][]) veröffentlichen, funktioniert die Bereitstellung nicht ordnungsgemäß.

Das folgende Dialogfeld zeigt die Eigenschaften für einen Cache:

![][ic719501]

* **Name:** Der Name des zusammengestellten Caches.
* **Portnummer:** Die Portnummer für den Cache.
* **Ablaufrichtlinie:** Einer der folgenden Werte zum Angeben des Ablaufs eines Schlüssel im Cache:
    * **Absolut:** Der Schlüssel läuft ab, wenn die Zeit unter **Gültigkeitsdauer in Minuten** angegebene Zeit erreicht ist.
    * **Kein Ablauf:** Der Schlüssel hat keine Ablaufzeit.
    * **Gleitendes Fenster:** Der Schlüssel läuft ab, wenn für den unter **Gültigkeitsdauer in Minuten** angegebenen Zeitraum nicht darauf zugegriffen wurde. Der Ablauftimer wird bei jedem Zugriff zurückgesetzt.
* **Gültigkeitsdauer in Minuten:** Die maximale Gültigkeitsdauer eines Memcache-Schlüssels im Rahmen der Ablaufrichtlinie.
* **Hohe Verfügbarkeit mit replizierten Sicherungen für verschiedene Rolleninstanzen:** Trägt mithilfe von replizierten Sicherungen für verschiedene Rolleninstanzen zur hohen Verfügbarkeit bei. Zur Verwendung dieses Features müssen für Ihre Bereitstellung mindestens zwei Rolleninstanzen aktiviert sein.

Wenn Sie einen neuen Cache hinzufügen möchten, klicken Sie auf der Eigenschaftenseite **Caching** auf die Schaltfläche **Hinzufügen**. Daraufhin öffnet sich ein Dialogfeld zum Konfigurieren eines benannten Cache. Geben Sie Werte für die oben beschriebenen Eigenschaften an.

Wenn Sie einen benannten Cache ändern möchten, wählen Sie den Cache aus, und klicken Sie auf der Eigenschaftenseite **Caching** auf die Schaltfläche **Bearbeiten**. Daraufhin öffnet sich ein Dialogfeld, in dem Sie die Cacheeigenschaften ändern können. Klicken Sie auf **OK**, um die Cachewerte zu speichern.

Wenn Sie einen Cache löschen möchten, wählen Sie ihn aus, klicken Sie auf der Eigenschaftenseite **Caching** auf die Schaltfläche **Entfernen**, und bestätigen Sie den Löschvorgang anschließend durch Klicken auf **Ja**.

Weitere Informationen zur Verwendung von Caching finden Sie unter [Vorgehensweise: Verwenden von zusammengestelltem Caching][].

<a name="certificates_properties"></a>
### Eigenschaften für Zertifikate ###

Öffnen Sie im Projekt-Explorer-Bereich von Eclipse das Kontextmenü für die Rolle. Klicken Sie auf **Azure** und anschließend auf **Zertifikate**.

![][ic710964]

In diesem Dialogfeld können Sie Zertifikate hinzufügen oder entfernen, auf die von Ihrem Eclipse-Projekt verwiesen wird. Beachten Sie, dass die hier aufgeführten Zertifikate nicht automatisch in jedem Java-Schlüsselspeicher gespeichert werden und daher nicht automatisch für jede Verwendung in einer Java-Anwendung zur Verfügung stehen. Sie werden nur bei Azure registriert, damit sie auf den virtuellen Maschinen, die Ihre Bereitstellung ausführen, vorab in den Windows-Zertifikatspeicher geladen und später von anderer Windows-Software verwendet werden können. Derzeit ist [SSL-Abladung][] die einzige Funktion des Toolkits, die Zertifikate verwendet, auf die im Dialogfeld **Zertifikate** auf diese Weise verwiesen wird. Dies ist der Abhängigkeit von IIS (Internet Information Services; Internetinformationsdienste) und ARR (Application Request Routing; Routing von Anwendungsanforderungen) geschuldet, für die das richtige Zertifikat auf diese Weise verfügbar gemacht werden muss.

Wenn Sie Ihr Projekt mithilfe des Veröffentlichungs-Assistenten in Azure bereitstellen, werden Sie aufgefordert, auf die entsprechenden Personal Information Exchange (PFX)-Dateien für diese Zertifikate (einschließlich Kennwörter) zu verweisen, damit sie automatisch in den Azure-Dienst hochgeladen werden (sofern nicht bereits geschehen).

<a name="components_properties"></a>
### Eigenschaften für Komponenten ###

Öffnen Sie im Projekt-Explorer-Bereich von Eclipse das Kontextmenü für die Rolle. Klicken Sie auf **Azure** und anschließend auf **Komponenten**. In diesem Dialogfeld können Sie Rollenkomponenten hinzufügen, ändern oder entfernen und deren Verarbeitungsreihenfolge ändern.

![][ic719502]

Mit dem Komponentenfeature können Sie Ihrem Azure-Bereitstellungsprojekt Abhängigkeiten hinzufügen – beispielsweise Java-Anwendungsprojekte, spezielle Dateien und ausführbare Befehlszeilenanweisungen, die für die Bereitstellung erforderlich sind.

Für jede Komponente kann Folgendes angegeben werden:

* Der Schritt, der ausgeführt werden soll, wenn die Komponente bei der Erstellung Ihres Azure-Bereitstellungsprojekts in das Projekt importiert wird.
* Der Schritt, der ausgeführt werden soll, wenn die Komponente in der Azure-Cloud bereitgestellt wird.

>[AZURE.NOTE]Denken Sie beim Angeben von Komponentendateien oder Befehlszeilen daran, dass Ihre Bereitstellung für eine virtuelle Windows-Maschine veröffentlicht wird. Die benutzerdefinierten Schritte müssen also auf ein Windows-basiertes Betriebssystem zugeschnitten sein.

Für Komponenten stehen folgende Eigenschaften zur Verfügung:

* **Import:** Methode, die angibt, wie die Komponente bei der Erstellung in das Projekt importiert wird. Mögliche Werte:
    * **Kopieren:** Die Komponente wird aus dem lokalen Pfad (angegeben durch die Eigenschaft **Von**) in das Verzeichnis **approot** der Rolle kopiert.
    * **EAR:** Bei der Komponente handelt es sich um ein Java-Unternehmensarchiv (Enterprise Archive, EAR), das aus einem Unternehmensanwendungsprojekt an dem lokalen, durch die Eigenschaft **Von** angegebenen Pfad importiert wird. (Wird vom Toolkit auf der Grundlage der Art des Projekts an diesem Speicherort automatisch erkannt.)
    * **JAR:** Bei der Komponente handelt es sich um ein Java-Archiv (JAR), das aus einem Java-Projekt an dem lokalen, durch die Eigenschaft **Von** angegebenen Pfad importiert wird. (Wird vom Toolkit auf der Grundlage der Art des Projekts an diesem Speicherort automatisch erkannt.)
    * **Keine:** Es wird keine Aktion zum Importieren der Komponente ausgeführt. Diese Option wird verwendet, wenn davon ausgegangen wird, dass die Komponente bereits im Verzeichnis **approot** der Rolle vorhanden ist oder wenn es sich bei der Komponente lediglich um eine ausführbare Befehlszeilenanweisung (gemäß Angabe in der Eigenschaft **Als** bei Verwendung der Bereitstellungsmethode **Ausführen**) handelt.
    * **JAR:** Bei der Komponente handelt es sich um ein Java-Webanwendungsarchiv (WAR), das aus einem dynamischen Webprojekt an dem lokalen, durch die Eigenschaft **Von** angegebenen Pfad importiert wird. (Wird vom Toolkit auf der Grundlage der Art des Projekts an diesem Speicherort automatisch erkannt.)
    * **ZIP-Datei:** Die Komponente ist eine ZIP-Datei. Zum Importieren wird das durch die Eigenschaft **Von** angegebene Verzeichnis (bzw. die durch die Eigenschaft angegebene Datei) gezippt.
* **Von:** Der Quellpfad auf Ihrem lokalen Computer zu dem Ordner oder der Datei, der bzw. die die zu importierenden Elemente für Ihre Bereitstellung darstellt. In dieser Eigenschaft können Windows-Umgebungsvariablen verwendet werden. Bei der Projekterstellung werden alle importierbaren Komponenten in das Verzeichnis **approot** der Rolle importiert.
	
	Hinweis: Im Gegensatz zur Bereitstellung für den Serveremulator haben Sie bei der Cloudbereitstellung die Möglichkeit, eine Komponente auf der Grundlage eines Downloads bereitzustellen. Informationen zum Hinzufügen einer Komponente finden Sie in den entsprechenden Informationen weiter unten.
	
* **Als:** Dateiname, unter dem die Komponente in das Verzeichnis **approot** der Rolle importiert und schließlich in der Azure-Cloud bereitgestellt wird. Lassen Sie diese Eigenschaft leer, um den gleichen Namen zu verwenden wie auf dem lokalen Computer. Bei ausführbaren Komponenten (also Komponenten, deren Bereitstellungsmethode auf **Ausführen** festgelegt ist) kann es sich um eine beliebige Windows-Befehlszeilenanweisung handeln.

	>[AZURE.IMPORTANT]Die Behandlung von Leerzeichen ist bei diesem Wert abhängig von der Bereitstellungsmethode. Bei der Bereitstellungsmethode **Ausführen** werden Leerzeichen nicht als Teil des Dateinamens, sondern als Trennzeichen für Befehlszeilenargumente interpretiert. Bei allen anderen Bereitstellungsmethoden werden Leerzeichen als Teil des Dateinamens interpretiert.
	
* **Bereitstellen:** Methode zum Angeben der Aktion, die beim Starten der Bereitstellung für die Komponente ausgeführt wird. Mögliche Werte:
    * **Kopieren:** Die Komponente wird an den durch die Eigenschaft **Nach** angegebenen Zielpfad kopiert.
    * **Ausführen:** Die Komponente ist eine ausführbare Windows-Befehlszeilenanweisung und wird beim Start der Bereitstellung im Kontext des durch die Eigenschaft **Nach** angegebenen Pfads ausgeführt.
    * **Keine:** Für die Komponente wird beim Start der Bereitstellung keine Aktion ausgeführt.
    * **ZIP-Datei:** Die Komponente wird an dem durch die Eigenschaft **Nach** angegebenen Zielpfad entzippt. Diese Methode ist nur verfügbar, wenn die Importeigenschaft auf **ZIP-Datei** festgelegt ist.
* **Nach:** Der Zielpfad auf der virtuellen Maschine, auf der die Komponente bereitgestellt wird. In dieser Eigenschaft können Windows-Umgebungsvariablen verwendet werden, und Dateipfade sind relativ zu **approot**.
	
Wenn Sie eine neue Komponente hinzufügen möchten, klicken Sie auf der Eigenschaftenseite **Komponenten** auf die Schaltfläche **Hinzufügen**. Daraufhin öffnet sich ein Dialogfeld für Azure-Rollenkomponenten. Geben Sie Werte für die oben beschriebenen Eigenschaften an.

Im Anschluss sehen Sie ein Beispiel für das Hinzufügen einer neuen WAR-Komponente:

![][ic719503]

Bei der Bereitstellung in der Cloud (also nicht im Serveremulator) gilt: Wenn Sie die Komponente auf der Grundlage eines Downloads bereitstellen möchten, muss **Bei der Cloudbereitstellung nicht in das Paket einschließen, sondern bereitstellen von** aktiviert sein. Wenn Sie einen Download aus Ihrem Azure-Speicherkonto verwenden möchten, wählen Sie in der Dropdownliste **Speicherkonto** das Speicherkonto aus. (Der Listeninhalt kann durch Klicken auf den Link **Konten** geändert werden.) Daraufhin wird das Feld **URL** teilweise ausgefüllt. Geben Sie anschließend den Rest der URL ein. Wenn Sie Azure Storage nicht verwenden möchten, wählen Sie in der Dropdownliste **Speicherkonto** die Option **(Kein)** aus, und geben Sie in das Feld **URL** die URL für Ihre Komponente ein. Geben Sie eine der folgenden Methoden an:

* **Kopieren:** Die Downloadkomponente wird an den unter **Zielverzeichnis** angegebenen Pfad kopiert.
* **Gleich:** Die gleiche Methode wird sowohl für die downloadbasierte als auch für die paketbasierte Bereitstellung verwendet.
* **ZIP-Datei:** Die Downloadkomponente wird am unter **Zielverzeichnis** angegebenen Pfad entzippt.

Wenn Sie eine Komponente ändern möchten, wählen Sie sie aus, und klicken Sie auf der Eigenschaftenseite **Komponenten** auf die Schaltfläche **Bearbeiten**. Daraufhin öffnet sich ein Dialogfeld, in dem Sie die Komponenteneigenschaften ändern können. Klicken Sie auf **OK**, um die Komponentenwerte zu speichern.

Wenn Sie eine Komponente löschen möchten, wählen Sie sie aus, klicken Sie auf der Eigenschaftenseite **Komponenten** auf die Schaltfläche **Entfernen**, und bestätigen Sie den Löschvorgang anschließend durch Klicken auf **Ja**.

Komponenten werden in der angegebenen Reihenfolge verarbeitet. Die Reihenfolge kann mithilfe der Schaltflächen **Nach oben** und **Nach unten** geändert werden.

>[AZURE.NOTE]Auch das Serverkonfigurationsfeature basiert auf Komponenten. Diese Komponenten können nicht entfernt oder bearbeitet werden, ohne die entsprechende Serverkonfiguration zu entfernen. Wenn Sie versuchen, eine solche Komponente zu ändern, wird ein entsprechender Hinweis angezeigt.

<a name="debugging_properties"></a>
### Debuggingeigenschaften ###

Öffnen Sie im Projekt-Explorer-Bereich von Eclipse das Kontextmenü für die Rolle. Klicken Sie auf **Azure** und anschließend auf **Debugging**. In diesem Dialogfeld können Sie das Remotedebuggen aktivieren/deaktivieren und Debugkonfigurationen erstellen, wie in der folgenden Abbildung zu sehen:

![][ic719504]

Weitere Informationen zum Debuggen finden Sie unter [Debuggen von Azure-Anwendungen in Eclipse][].

<a name="endpoints_properties"></a>
### Eigenschaften für Endpunkte ###

Öffnen Sie im Projekt-Explorer-Bereich von Eclipse das Kontextmenü für die Rolle. Klicken Sie auf **Azure** und anschließend auf **Endpunkte**. In diesem Dialogfeld können Sie einen Endpunkt erstellen, bearbeiten oder entfernen, wie in der folgenden Abbildung zu sehen:

![][ic719505]

Wenn Sie einen Endpunkt hinzufügen möchten, klicken Sie auf der Eigenschaftenseite **Endpunkte** auf die Schaltfläche **Hinzufügen**. Daraufhin öffnet sich ein Dialogfeld zum Hinzufügen eines Endpunkts.

![][ic710897]

Geben Sie einen Namen für den Endpunkt ein, wählen Sie den Typ (**Eingabe**, **Intern**, oder **Instanzeingabe**) aus, und geben Sie den öffentlichen und privaten Port an. Klicken Sie auf **OK**, um die neuen Endpunktwerte zu speichern.

Je nach Art des Endpunkts können folgende Portbereiche verwendet werden:

* Bei einem Eingabeinstanz-Endpunkt kann der öffentliche Port als Portbereich (z. B. **2000–2010**) und der private Port als fester Wert angegeben werden.
* Bei einem internen Endpunkt wird der öffentliche Port nicht verwendet. Der private Port kann als Bereich angegeben, leer gelassen oder auf ein Sternchen festgelegt werden. In letzterem Fall wird er automatisch von Azure festgelegt.
* Bei einem Eingabeendpunkt kann der öffentliche Port nur auf einen festen Wert festgelegt werden. Der private Port kann als fester Wert angegeben, leer gelassen oder auf ein Sternchen festgelegt werden. In letzterem Fall wird er automatisch von Azure festgelegt.

Wenn Sie nur eine einzelne Portnummer und keinen Bereich verwenden möchten, lassen Sie das Textfeld für das Ende des Bereichs leer.

Wenn Sie bei Verwendung der automatischen Portfestlegung ermitteln möchten, welcher Port zur Laufzeit tatsächlich verwendet wird, kann Ihre Anwendung die Azure Service Runtime-API verwenden. Die entsprechende Dokumentation finden Sie in der [Paketzusammenfassung für „com.microsoft.windowsazure.serviceruntime“][].

Informationen zum Verwenden von Instanzeingabe-Endpunkten beim Debuggen einer Bereitstellung mit mehreren Instanzen finden Sie unter [Debuggen einer bestimmten Rolleninstanz in einer Bereitstellung mit mehreren Instanzen][].

Wenn Sie einen Endpunkt ändern möchten, wählen Sie ihn aus, und klicken Sie auf der Eigenschaftenseite **Endpunkte** auf die Schaltfläche **Bearbeiten**. Daraufhin erscheint ein Dialogfeld, in dem Sie Name und Typ sowie den öffentlichen und privaten Port des Endpunkts ändern können. Klicken Sie auf **OK**, um die geänderten Endpunktwerte zu speichern.

Wenn Sie einen Endpunkt löschen möchten, wählen Sie ihn aus, klicken Sie auf der Eigenschaftenseite **Endpunkte** auf die Schaltfläche **Entfernen**, und bestätigen Sie den Löschvorgang anschließend durch Klicken auf **Ja**.

Zur ordnungsgemäßen Konfiguration einiger Features (etwa Caching, Remotedebuggen, Sitzungsaffinität oder SSL-Abladung), die durch den Benutzer für eine Rolle aktiviert werden, konfiguriert das Toolkit unter Umständen automatisch spezielle Endpunkte, die zusammen mit den benutzerdefinierten Endpunkten aufgeführt werden. Diese automatisch generierten Endpunkte können vom Benutzer nicht bearbeitet oder gelöscht werden, solange das entsprechende Feature aktiviert ist.

<a name="environment_variables_properties"></a>
### Eigenschaften für Umgebungsvariablen ###

Öffnen Sie im Projekt-Explorer-Bereich von Eclipse das Kontextmenü für die Rolle. Klicken Sie auf **Azure** und anschließend auf **Umgebungsvariablen**. In diesem Dialogfeld können Sie eine Umgebungsvariable erstellen, ändern oder entfernen, wie in der folgenden Abbildung zu sehen:

![][ic719506]

Umgebungsvariablen stehen Ihrem Startskript beim Start der Rolle zur Verfügung.

>[AZURE.NOTE]Denken Sie beim Angeben von Umgebungsvariablen daran, dass Ihre Bereitstellung für eine virtuelle Windows-Maschine veröffentlicht wird. Die Umgebungsvariablen müssen also auf ein Windows-basiertes Betriebssystem zugeschnitten sein.

Klicken Sie auf die Schaltfläche **Hinzufügen**, um als Beispiel für eine Umgebungsvariable, die beim Start der Rolle verfügbar ist, eine neue Umgebungsvariable zu erstellen. Das folgende Beispiel zeigt eine Umgebungsvariable namens **MyRoleVersion**, die erstellt und mit dem Wert **1.0** versehen wird:

![][ic659268]

Der Wert kann innerhalb Ihres JSP-Codes mithilfe der Methode `System.getenv` angezeigt werden:

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

Dadurch ergibt sich beim Ausführen Ihrer Anwendung folgende Ausgabe:

![][ic552233]

Wenn Sie eine Umgebungsvariable ändern möchten, wählen Sie die Umgebungsvariable aus, und klicken Sie auf der Eigenschaftenseite **Umgebungsvariablen** auf die Schaltfläche **Bearbeiten**. Daraufhin öffnet sich ein Dialogfeld, in dem Sie die Eigenschaften der Umgebungsvariablen ändern können. Klicken Sie auf **OK**, um die geänderten Umgebungsvariablenwerte zu speichern.

Wenn Sie eine Umgebungsvariable löschen möchten, wählen Sie sie aus, klicken Sie auf der Eigenschaftenseite **Umgebungsvariablen** auf die Schaltfläche **Löschen**, und bestätigen Sie den Löschvorgang anschließend durch Klicken auf **Ja**.

Zur ordnungsgemäßen Konfiguration einiger Features (etwa Serverkonfiguration, Remotedebuggen oder lokaler Speicher), die durch den Benutzer für eine Rolle aktiviert werden, konfiguriert das Toolkit unter Umständen automatisch spezielle Umgebungsvariablen, die zusammen mit den benutzerdefinierten Umgebungsvariablen aufgeführt werden. Diese automatisch generierten Umgebungsvariablen können vom Benutzer nicht bearbeitet oder gelöscht werden, solange das entsprechende Feature aktiviert ist.

<a name="session_affinity_properties"></a>
### Eigenschaften für den Lastenausgleich/für die Sitzungsaffinität (bzw. persistente Sitzungen) ###

Öffnen Sie im Projekt-Explorer-Bereich von Eclipse das Kontextmenü für die Rolle. Klicken Sie auf **Azure** und anschließend auf **Lastenausgleich**. In diesem Dialogfeld können Sie die Sitzungsaffinität aktivieren/deaktivieren, wie in der folgenden Abbildung zu sehen:

![][ic719492]

Weitere Informationen finden Sie unter [Sitzungsaffinitiät][]. Beachten Sie außerdem das Verhalten dieses Features im Kontext der [SSL-Abladung][].

<a name="local_storage_properties"></a>
### Eigenschaften für lokalen Speicher ###

Öffnen Sie im Projekt-Explorer-Bereich von Eclipse das Kontextmenü für die Rolle. Klicken Sie auf **Azure** und anschließend auf **Lokaler Speicher**. In diesem Dialogfeld können Sie temporären lokalen Speicher für die virtuelle Maschine erstellen, ändern oder entfernen, die Ihre Anwendung ausführt. Sie können spezifische Werte für die Größe des lokalen Speichers festlegen und angeben, ob die Inhalte beim Recyceln der Rolle beibehalten werden sollen, wie in der folgenden Abbildung zu sehen:

![][ic719508]

Darüber hinaus können Sie optional eine Umgebungsvariable angeben, die dem lokalen Speicher entspricht.

Standardmäßig werden alle Elemente, die Sie in Azure bereitstellen, im Ordner **approot** der Rolleninstanz platziert (und entzippt). Die meisten einfachen Bereitstellungen finden dort zwar auch nach dem Entzippen Platz, dem Verzeichnis **approot** ist jedoch nur eine begrenzte Menge an Speicherplatz zugewiesen, und diese ist nicht klar definiert. (Weniger als 1 GB ist eine gute Faustregel.) Daher empfiehlt es sich, über das Dialogfeld für lokalen Speicher eine lokale Speicherressource einzurichten, um sicherzustellen, dass Azure ausreichend Speicherplatz für größere Bereitstellungen zuordnet, die unter Umständen nicht in den Ordner **approot** passen. Eine einfache Anleitung hierzu finden Sie unter [Bereitstellen großer Bereitstellungen][].

Auf die Speicherressource kann ganz einfach in Startskripts (etwa **startup.cmd**) verwiesen werden. Dabei wird die Umgebungsvariable verwendet, die der Ressource automatisch vom Eclipse-Toolkit zugeordnet wurde, wie im Dialogfeld für den lokalen Speicher zu sehen. Diese Umgebungsvariable enthält den vollständigen Pfad der lokalen Ressource, die Sie zum Zeitpunkt der Ausführung Ihres Startskripts konfiguriert haben.

Wenn Sie eine lokale Speicherressource ändern möchten, wählen Sie sie aus, und klicken Sie auf der Eigenschaftenseite für den lokalen Speicher auf die Schaltfläche **Bearbeiten**. Daraufhin öffnet sich ein Dialogfeld, in dem Sie die Eigenschaften der lokalen Speicherressource ändern können. Klicken Sie auf **OK**, um die Werte der lokalen Speicherressource zu speichern.

Wenn Sie eine lokale Speicherressource löschen möchten, wählen Sie sie aus, und klicken Sie auf der Eigenschaftenseite für den lokalen Speicher auf die Schaltfläche **Entfernen**, und bestätigen Sie den Löschvorgang anschließend durch Klicken auf **Ja**.

<a name="server_configuration_properties"></a>
### Eigenschaften für die Serverkonfiguration ###

Öffnen Sie im Projekt-Explorer-Bereich von Eclipse das Kontextmenü für die Rolle. Klicken Sie auf **Azure** und anschließend auf **Serverkonfiguration**. In diesem Dialogfeld können Sie das von Ihrer Bereitstellung verwendete JDK-Paket und den Java-Anwendungsserver hinzufügen, entfernen und ändern sowie die von Ihrer Bereitstellung verwendeten Anwendungen (beispielsweise WAR-, JAR- oder EAR-Dateien) hinzufügen/entfernen.

### JDK-Konfiguration ###

In diesem Dialogfeld können Sie das JDK-Paket für Ihre Bereitstellung angeben. Bei Verwendung von Eclipse unter Windows können Sie das JDK-Paket angeben, das lokal bei der Ausführung im Azure-Emulator verwendet wird. Außerdem haben Sie die Möglichkeit, diese lokale Installation in Azure bereitzustellen. Bei Windows-fremden Betriebssystemen hat die JDK-Emulatoreinstellung keine Relevanz, und das lokal installierte JDK-Paket kann nicht bereitgestellt werden, da es nicht mit Windows kompatibel ist. Unabhängig vom verwendeten Betriebssystem haben Sie jedoch die Möglichkeit, die zur Auswahl stehenden JDK-Drittanbieterpakete in Azure bereitzustellen oder auf Ihr eigenes Windows-kompatibles JDK-Paket an einem alternativen Downloadspeicherort zu verweisen.

Das folgende Beispiel zeigt, wie Sie ein JDK-Paket unter Windows angeben können:

![][ic780647]

Bei Verwendung von Eclipse unter Windows können Sie ein JDK-Paket zur Verwendung mit dem Serveremulator angeben. Vergewissern Sie sich hierzu, dass im Abschnitt für die Emulatorbereitstellung das Kontrollkästchen **JDK-Paket an diesem Dateipfad für lokale Tests verwenden** aktiviert ist. Geben Sie dann den lokalen Pfad zu Ihrem JDK-Paket an. Sollte das gewünschte Paket nicht automatisch ausgewählt werden, können Sie zum gewünschten JDK-Paket navigieren. Sie haben auch die Möglichkeit, Ihr JDK-Paket in Ihrem Azure-Clouddienst bereitzustellen. Aktivieren Sie hierzu im Abschnitt **Cloudbereitstellung** die Option **Eigenes lokales JDK-Paket bereitstellen (automatisch in den Cloudspeicher hochladen)**.

Hinweis: Unter Windows-fremden Betriebssystemen sind die Einstellungen für die Emulatorbereitstellung und die Option zum Bereitstellen eines eigenen lokalen JDK-Pakets nicht verfügbar. Das folgende Beispiel veranschaulicht das Angeben eines JDK-Pakets auf einem Mac oder unter einem anderen unterstützten, Windows-fremden Betriebssystem:

![][ic789643]

Die beiden folgenden Cloudbereitstellungsoptionen für Quelle und Art Ihres JDK-Pakets stehen unabhängig vom verwendeten Betriebssystem zur Verfügung:

* **In Azure verfügbares JDK-Drittanbieterpaket bereitstellen** 
* **Auf der Grundlage eines benutzerdefinierten Downloads bereitstellen** 

Informationen zur Option **In Azure verfügbares JDK-Drittanbieterpaket bereitstellen**:

1. Aktivieren Sie das Kontrollkästchen **In Azure verfügbares JDK-Drittanbieterpaket bereitstellen**.
1. Wählen Sie in der Dropdownliste das in Azure verfügbare JDK-Drittanbieterpaket aus.
1. Die Registerkarte **JDK** sieht unter Windows etwa wie folgt aus: ![][ic780648] Unter Mac OS und anderen unterstützten, Windows-fremden Betriebssystemen sieht sie in etwa so aus: ![][ic789643]
1. Klicken Sie zum Speichern der Änderungen auf **OK**.
1. Wenn Sie aufgefordert werden, den Lizenzvertrag des Drittanbieters für das JDK-Paket zu akzeptieren, lesen Sie sich die Lizenzbedingungen durch. Klicken Sie auf **Ja**, sofern Sie den Bedingungen zustimmen. Das Dialogfeld zum Akzeptieren des Lizenzvertrags wird geschlossen. Beachten Sie, dass die Logik, die der Anzeige von Elementen in der Dropdownliste für die Option **In Azure verfügbares JDK-Drittanbieterpaket bereitstellen** zugrunde liegt, angepasst werden kann. Klicken Sie zum Anpassen der Elemente im Dialogfeld **JDK** auf den Link **Anpassen**. Daraufhin wird die Eigenschaftenseite **JDK** geschlossen und die Datei **componentsets.xml** zur Änderung in Eclipse geöffnet. Die Dokumentation für **componentsets.xml** finden Sie direkt in der Datei **componentsets.xml**.

Informationen zur Option **JDK auf der Grundlage eines benutzerdefinierten Downloads bereitstellen**:

1. Erstellen Sie eine ZIP-Datei Ihres JDK-Installationsverzeichnisses. Achten Sie dabei darauf, dass der Verzeichnisknoten (und nicht dessen Inhalt) das untergeordnete Element der ZIP-Struktur bildet. Notieren Sie sich den Namen des Verzeichnisses. Diesen benötigen Sie später noch. Zur Erinnerung: Diese JDK-Installation wird auf einer virtuellen Windows-Maschine bereitgestellt.
1. Laden Sie die ZIP-Datei als Blob in Ihr Azure-Speicherkonto hoch. Für das Hochladen von Blobs in Azure Storage kann ein extern verfügbares Tool verwendet werden. Es wird empfohlen, einen privaten Blob zu verwenden. Notieren Sie sich die Blob-URL der ZIP-Inhalte.
1. Aktivieren Sie das Kontrollkästchen **JDK auf der Grundlage eines benutzerdefinierten Downloads bereitstellen**. Wenn Sie einen Download aus Ihrem Azure-Speicherkonto verwenden möchten, wählen Sie in der Dropdownliste **Speicherkonto** das Speicherkonto aus. (Der Listeninhalt kann durch Klicken auf den Link **Konten** geändert werden.) Daraufhin wird das Feld **URL** teilweise ausgefüllt. Geben Sie anschließend den Rest der URL ein. Wenn Sie Azure Storage nicht verwenden möchten, wählen Sie in der Dropdownliste **Speicherkonto** die Option **(Kein)** aus, und geben Sie in das Feld **URL** die URL für Ihren JDK-Download ein. Bei Verwendung von Azure Storage müssen Blob-Namen in der URL in Kleinbuchstaben angegeben werden.
1. Vergewissern Sie sich, dass das Textfeld **JAVA\_HOME** auf den richtigen Verzeichnisnamen verweist. Standardmäßig wird auf den JDK-Verzeichnisnamen verwiesen, den Sie auch als Wert für die lokale Verwendung ausgewählt haben. Sollte das in der ZIP-Datei enthaltene Verzeichnis jedoch einen anderen Namen besitzen (beispielsweise aufgrund einer anderen Version), aktualisieren Sie den Namen des Verzeichnisses im Feld **JAVA\_HOME** entsprechend, da diese Einstellung in der Cloud (nicht im Serveremulator) verwendet wird.
1. Klicken Sie zum Speichern der Änderungen auf **OK**.

Das ist alles. Wenn Sie nun die Erstellung für die Cloud durchführen, ist die Paketgröße deutlich geringer. Außerdem sind in der Regel der Erstellungsprozess und die eigentliche Bereitstellung bei der Veröffentlichung in der Cloud schneller abgeschlossen. Beachten Sie, dass die Optionen **Eigenes lokales JDK-Paket bereitstellen (automatisch in den Cloudspeicher hochladen)** und **JDK auf der Grundlage eines benutzerdefinierten Downloads bereitstellen** nur dann relevant sind, wenn Ihre Anwendung in der Cloud bereitgestellt wird. Auf den Serveremulator haben sie dagegen keinerlei Auswirkung. Bei der Bereitstellung für den Serveremulator wird auch weiterhin die lokale Version der Komponenten verwendet.

### Serverkonfiguration ###

Das folgende Beispiel zeigt, wie Sie einen Anwendungsserver angeben können:

![][ic796926]

Vergewissern Sie sich, dass das Kontrollkästchen **Server dieses Typs bereitstellen** aktiviert ist, und wählen Sie dann die Art des Anwendungsservers aus, die Sie verwenden möchten.

Bei einem Server für die Cloudbereitstellung stehen folgende Optionen zur Verfügung:

1. **In Azure verfügbaren Drittanbieterserver bereitstellen**: Empfiehlt sich insbesondere in Entwicklungs-/Testszenarien, in denen Bereitstellungseffizienz und Einfachheit eine wichtige Rolle spielen und der Server keine benutzerdefinierte Konfiguration erfordert, oder wenn Sie einen der Server als Ausgangspunkt verwenden möchten, die entsprechenden Serveranpassungsschritte aber in der Startlogik Ihrer Bereitstellung enthalten sind.
1. **Auf der Grundlage eines benutzerdefinierten Downloads bereitstellen**: Empfiehlt sich insbesondere in Produktionsszenarien mit einem speziell vorbereiteten und konfigurierten Server, den Sie in der Cloud verwenden möchten.
1. **Eigene lokale Serverinstallation bereitstellen**: Empfiehlt sich insbesondere, wenn Ihre lokale Serverinstallation bereits speziell für Ihre individuellen Anforderungen konfiguriert ist. Bei Verwendung dieser Option müssen Sie im Textfeld **Lokaler Serverpfad** den Pfad zu Ihrem lokalen Server angeben.

Informationen zur Option **In Azure verfügbaren Drittanbieterserver bereitstellen**:

1. Aktivieren Sie das Kontrollkästchen **In Azure verfügbaren Drittanbieterserver bereitstellen**.
1. Wählen Sie im Dropdownmenü die Serversoftware aus, die Sie für Ihre Bereitstellung in der Cloud verwenden möchten. Hinweis: Wenn Sie vorher bereits einen zu verwendenden Servertyp angegeben haben, können Sie nur einen Cloudserver aus dieser Servertypfamilie auswählen. Wenn Sie noch keinen Servertyp ausgewählt haben, stehen alle Server zur Auswahl, die derzeit in Azure verfügbar sind. Der Servertyp wird dann automatisch für Sie ausgewählt.
1. Klicken Sie zum Speichern der Änderungen auf **OK**.

Informationen zur Option **Auf der Grundlage eines benutzerdefinierten Downloads bereitstellen**:

1. Vergewissern Sie sich, dass Sie gemäß den vorhergehenden Schritten bereits einen Servertyp ausgewählt haben. Diese Angabe wird vom Plug-In benötigt, um den Server auf der Grundlage Ihres benutzerdefinierten Downloads bereitzustellen. Dieser muss nämlich der gleichen Servertypfamilie angehören.
1. Aktivieren Sie das Kontrollkästchen **Auf der Grundlage eines benutzerdefinierten Downloads bereitstellen**. Wenn Sie einen Download aus Ihrem Azure-Speicherkonto verwenden möchten, wählen Sie in der Dropdownliste **Speicherkonto** das Speicherkonto aus. (Der Listeninhalt kann durch Klicken auf den Link **Konten** geändert werden.) Daraufhin wird das Feld **URL** teilweise ausgefüllt. Geben Sie anschließend den Rest der URL zu Ihrer Serverdownload-ZIP-Datei ein. (Bei Verwendung von Azure Storage müssen Blob-Namen in der URL in Kleinbuchstaben angegeben werden.) Wenn Sie Azure Storage nicht verwenden möchten, wählen Sie in der Dropdownliste **Speicherkonto** die Option **(Kein)** aus, und geben Sie in das Feld **URL** die URL für Ihre Serverdownload-ZIP-Datei ein. Die ZIP-Datei enthält einen untergeordneten Ordner, der das Installationsverzeichnis Ihres Anwendungsservers darstellt. Wenn Sie also beispielsweise eine ZIP-Datei für Apache Tomcat 7.0.35 verwenden, enthält die ZIP-Datei den untergeordneten Ordner für das Installationsverzeichnis (etwa **apache-tomcat-7.0.35**). 
1. Geben Sie den Wert für die Basisverzeichnis-Umgebungsvariable an. Standardmäßig wird der Wert für Ihren lokalen Anwendungsserver verwendet (sofern vorhanden). Sie können jedoch einen anderen Wert angeben, falls Ihr Cloudanwendungsserver nicht Ihrem lokalen Anwendungsserver entspricht. Ihr Cloudanwendungsserver muss jedoch ebenfalls der zuvor ausgewählten Servertypfamilie angehören. Bei einer späteren Aktualisierung Ihrer ZIP-Datei für den Cloudanwendungsserver können Sie die Einstellung für das Basisverzeichnis manuell ändern oder wieder auf Ihre lokale Einstellung festlegen (falls Sie auch Ihren lokalen Anwendungsserver geändert haben).
1. Klicken Sie zum Speichern der Änderungen auf **OK**.

Die Logik, die der Anzeige von Elementen auf der Registerkarte **Server** der Eigenschaftenseite **Serverkonfiguration** zugrunde liegt, kann angepasst werden. Hierbei handelt es sich um ein erweitertes Feature für den Fall, dass Ihre Anforderungen über die Standardwerte hinausgehen oder Sie weitere Server hinzufügen möchten. Klicken Sie zum Anpassen der Logik im Dialogfeld **Server** auf den Link **Anpassen**. Dadurch wird die Eigenschaftenseite **Serverkonfiguration** geschlossen und die Datei **componentsets.xml** zur Erweiterung der Serverkonfigurationsvorlage in Eclipse geöffnet. Die Dokumentation für **componentsets.xml** finden Sie direkt in der Datei **componentsets.xml**.

Informationen zur Option **Eigenen lokalen Server bereitstellen (automatisch in den Cloudspeicher hochladen)**:

1. Aktivieren Sie das Kontrollkästchen **Eigenen lokalen Server bereitstellen (automatisch in den Cloudspeicher hochladen)**.
1. Wählen Sie in der Dropdownliste **Speicherkonto** die Option **(Auto)** aus. Bei Angabe von **(Auto)** wird für Ihren Server das Speicherkonto verwendet, das Sie im Dialogfeld **Veröffentlichen in Azure** für Ihre Bereitstellung ausgewählt haben.
1. Klicken Sie zum Speichern der Änderungen auf **OK**.

Wählen Sie im Textfeld **Lokaler Serverpfad** einen Serverinstallationspfad auf Ihrem Computer aus, falls eine der folgenden Bedingungen zutrifft:

* Sie möchten Ihre Bereitstellung im Emulator testen (gilt nur für Windows).
* Sie möchten Ihren lokal installierten Server in der Cloud bereitstellen.
* Sie möchten einen eigenen benutzerdefinierten Serverdownload in der Cloud verwenden. (In diesem Fall muss auch die Option **Eigenen lokalen Server bereitstellen (automatisch in den Cloudspeicher hochladen)** ausgewählt sein.)

Falls keine der obigen Optionen zutrifft, ist die Einstellung für den lokalen Server optional.

### Anwendungskonfiguration ###

Das folgende Beispiel zeigt, wie Sie eine Anwendung angeben können:

![][ic719512]

Klicken Sie auf **Hinzufügen**, um eine weitere Anwendung hinzuzufügen, oder auf **Entfernen**, um eine Anwendung zu entfernen. Wenn Sie bei der Cloudbereitstellung einen Download als Anwendungsquelle verwenden möchten, geben Sie aus Effizienzgründen mithilfe der Komponenteneigenschaften[ ](#components_properties)eine URL, ein Speicherkonto usw. an.

Ab der Version vom April 2014 werden Ihre Anwendungen automatisch in das für Ihre Bereitstellung ausgewählte Speicherkonto (unter dem Container **eclipsedeploy**) hochgeladen. Die Startlogik Ihrer Bereitstellung enthält einen Schritt, der zuerst die Anwendungen aus diesem Speicherkonto herunterlädt. Das bedeutet, dass Sie Ihre Anwendungen in der Bereitstellung ohne erneute Erstellung und Bereitstellung des gesamten Pakets upgraden können, indem Sie neuere Anwendungsversionen manuell direkt in das entsprechende Speicherkonto hochladen (beispielsweise über das Azure-Portal) und dabei die ursprünglich durch das Toolkit hochgeladenen WAR-Dateien ersetzen. Anschließend müssen Sie dann nur noch das Recycling dieser Rolleninstanzen initiieren – entweder mithilfe des Azure-Verwaltungsportals oder mithilfe von Befehlszeilenprogrammen. (Die direkte Initiierung des Rollenrecyclings im Eclipse-Toolkit wird derzeit nicht unterstützt.)

### Hinweise zur Serverkonfiguration ###

Auf der Eigenschaftenseite **Serverkonfiguration** vorgenommene Änderungen werden auf die Elemente vom Typ `<component>` in der Datei „package.xml“ übertragen.

Wenn Sie die automatische Uploadoption oder die downloadbasierte Bereitstellungsoption für das JDK-Paket oder den Anwendungsserver verwenden, eine Erstellung für die Cloud (nicht für den Serveremulator) durchführen und eine Netzwerkverbindung besteht, werden in der Konsolenausgabe unter Umständen Erstellungsmeldungen wie die folgenden angezeigt, während der Ant-Builder die Verfügbarkeit des Downloads überprüft:

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...`

Bei Verwendung der downloadbasierten Bereitstellungsoption wird möglicherweise die folgende Warnung angezeigt, der Erstellungsprozess wird jedoch fortgesetzt:

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).`

Diese Warnung ist der einzige Hinweis darauf, dass die Verfügbarkeit des Downloads nicht verifiziert wurde. Falls also eine Bereitstellung in der Cloud aus irgendeinem Grund nicht erfolgreich ist, überprüfen Sie, ob diese Warnung ausgegeben wurde.

Wenn Sie die Downloadüberprüfung deaktivieren möchten (etwa, weil sie Ihrer Meinung nach den Erstellungsprozess unnötig verlangsamt), legen Sie das Attribut `verifydownloads` im Element `<windowsazurepackage>` der Datei „package.xml“ auf `false` fest:

`<windowsazurepackage verifydownloads="false" ...>`

Bei Verwendung der automatischen Uploadoption informieren Erstellungsmeldungen im Konsolenfenster alle fünf Sekunden über den Fortschritt des Uploads, wenn ein Upload erforderlich ist.

<a name="ssl_offloading_properties"></a>
### SSL-Abladungseigenschaften ###

Öffnen Sie im Projekt-Explorer-Bereich von Eclipse das Kontextmenü für die Rolle. Klicken Sie auf **Azure** und anschließend auf **SSL-Abladung**.

![][ic719481]

In diesem Dialogfeld können Sie die SSL-Abladung aktivieren. Dies ermöglicht die unkomplizierte Unterstützung von HTTPS (Hypertext Transfer-Protokoll Secure) in Ihrer Java-Bereitstellung für Azure, ohne SSL auf Ihrem Java-Anwendungsserver konfigurieren zu müssen. Weitere Informationen finden Sie unter [SSL-Abladung][] sowie unter [Vorgehensweise: Verwenden der SSL-Abladung][].

## Weitere Informationen ##

[Azure-Toolkit für Eclipse][]

[Installieren des Azure-Toolkits für Eclipse][]

[Erstellen einer Hello World-Anwendung für Azure in Eclipse][]

[Azure-Projekteigenschaften][]

[Azure-Speicherkontoliste][]

Weitere Informationen zur Verwendung von Azure mit Java finden Sie im [Azure Java Developer Center][].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure-Verwaltungsportal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure-Toolkit für Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure-Projekteigenschaften]: http://go.microsoft.com/fwlink/?LinkID=699524
[Azure-Speicherkontoliste]: http://go.microsoft.com/fwlink/?LinkID=699528
[Paketzusammenfassung für „com.microsoft.windowsazure.serviceruntime“]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Erstellen einer Hello World-Anwendung für Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Debuggen einer bestimmten Rolleninstanz in einer Bereitstellung mit mehreren Instanzen]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Debuggen von Azure-Anwendungen in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Bereitstellen großer Bereitstellungen]: http://go.microsoft.com/fwlink/?LinkID=699536
[Vorgehensweise: Verwenden von zusammengestelltem Caching]: http://go.microsoft.com/fwlink/?LinkID=699542
[Vorgehensweise: Verwenden der SSL-Abladung]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installieren des Azure-Toolkits für Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Sitzungsaffinitiät]: http://go.microsoft.com/fwlink/?LinkID=699548
[SSL-Abladung]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png

<!---HONumber=AcomDC_1203_2015-->