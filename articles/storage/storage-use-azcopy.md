<properties 
	pageTitle="Verwenden von AzCopy mit Microsoft Azure Storage" 
	description="Erfahren Sie, wie Sie das Hilfsprogramm AzCopy zum Hochladen, Herunterladen und Kopieren von Blob- und Dateiinhalten verwenden." 
	services="storage" 
	documentationCenter="" 
	authors="micurd" 
	manager="jahogg" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="micurd"/>

# Erste Schritte mit dem Befehlszeilenprogramm AzCopy

## Übersicht

AzCopy ist ein Befehlszeilenprogramm, das zum leistungsstarken Hochladen, Herunterladen und Kopieren von Daten zu und vom Microsoft Azure Blob-, Datei- und Tabellenspeicher konzipiert wurde. Dieser Leitfaden bietet einen Überblick über die Verwendung von AzCopy.

> [AZURE.NOTE]In diesem Leitfaden wird davon ausgegangen, dass Sie AzCopy 5.0 oder höher installiert haben.

Wir haben außerdem eine Open-Source-Bibliothek basierend auf dem Kernframework für die Datenverschiebung veröffentlicht, das Grundlage von AzCopy ist. Weitere Einzelheiten finden Sie unter [Introducing Azure Storage Data Movement Library Preview](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).

## Herunterladen und Installieren von AzCopy

1. Laden Sie die [neueste Version von AzCopy](http://aka.ms/downloadazcopy) herunter
2. Führen Sie die Installation aus. Standardmäßig wird AzCopy in `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy\AzCopy.exe` (auf einem Computer mit einer 64-Bit-Version von Windows) oder `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy\AzCopy.exe` (auf einem Computer mit einer 32-Bit-Version von Windows) installiert. Sie können den Installationspfad jedoch über den Setup-Assistenten ändern.
3. Bei Bedarf können Sie den Speicherort für die AzCopy-Installation zum Systempfad hinzufügen.

## Informationen zur Befehlszeilensyntax von AzCopy

Als Nächstes öffnen Sie ein Befehlsfenster und navigieren zum Installationsverzeichnis von AzCopy auf Ihrem Computer, in dem sich die ausführbare Datei `AzCopy.exe` befindet. Die grundlegende Syntax für AzCopy-Befehle ist:

	AzCopy /Source:<source> /Dest:<destination> /Pattern:<filepattern> [Options]

> [AZURE.NOTE]Ab AzCopy Version 3.0.0 erfordert die AzCopy-Befehlszeilensyntax, dass bei jedem Parameter auch der Parametername angegeben werden muss, *z. B.* `/ParameterName:ParameterValue`.

## Schreiben Sie Ihren ersten AzCopy-Befehl

**Hochladen einer Datei aus dem Dateisystem in den Blobspeicher:**
	
	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

Beachten Sie, dass Sie beim Kopieren einer einzelnen Datei die Option „/Pattern“ mit dem Dateinamen angeben müssen. Weitere Beispiele finden Sie im unteren Abschnitt dieses Artikels.

## Einführung in die Parameter

Parameter für AzCopy werden in der folgenden Tabelle beschrieben. Sie können auch einen der folgenden Befehle von der Befehlszeile für die Unterstützung in Bezug auf die Verwendung von AzCopy eingeben:

- Für eine detaillierte Befehlszeilenhilfe für AzCopy: `AzCopy /?`
- Für eine detaillierte Hilfe zu einzelnen AzCopy-Parametern: `AzCopy /?:SourceKey`
- Für Befehlszeilenbeispiele: `AzCopy /?:Samples` 

<table>
  <tr>
    <th>Optionsname</th>
    <th>Beschreibung</th>
    <th>Anwendbar auf BLOB-Speicher (J/N)</th>
    <th>Anwendbar auf Dateispeicher (J/N)</th>
    <th>Anwendbar auf Tabellenspeicher (J/N)</th>
  </tr>
  <tr>
    <td><b>/Source:&lt;source></b></td>
    <td>Gibt die Quelldaten an, aus denen kopiert werden soll. Die Quelle kann ein Dateisystemverzeichnis, ein Blobcontainer, ein virtuelles Blobverzeichnis, eine Speicherdateifreigabe, ein Speicherdateiverzeichnis oder eine Azure-Tabelle sein.</td>
    <td>J</td>
    <td>J<br /></td>
    <td>Y<br /></td>
  </tr>
  <tr>
    <td><b>/Dest:&lt;destination></b></td>
    <td>Geben das Ziel für das Kopieren an. Das Ziel kann ein Dateisystemverzeichnis, ein Blobcontainer, ein virtuelles Blobverzeichnis, eine Speicherdateifreigabe, ein Speicherdateiverzeichnis oder eine Azure-Tabelle sein.</td>
    <td>J</td>
    <td>J<br /></td>
    <td>J<br /></td>
  </tr>
  <tr>
    <td><b>/Pattern:&lt;file-pattern></b></td>
      <td>
          Gibt ein Dateimuster an, das angibt, welche Dateien kopiert werden sollen. Das Verhalten des „/Pattern“-Parameters wird über den Speicherort der Quelldaten und über die Angabe der Option für den rekursiven Modus bestimmt. Der rekursive Modus wird über die Option „/S“ angegeben.
          <br />
          Wenn es sich bei der angegebenen Quelle um ein Verzeichnis im Dateisystem handelt, dann sind Standardplatzhalter wirksam und das bereitgestellte Dateimuster wird anhand von Dateien in diesem Verzeichnis verglichen. Wenn die Option „/S“ angegeben ist, vergleicht AzCopy das angegebene Muster auch mit allen Dateien in sämtlichen Unterordnern dieses Verzeichnisses.
          <br />
          Wenn die angegebene Quelle ein BLOB-Container oder ein virtuelles Verzeichnis ist, werden keine Platzhalter angewendet. Wenn die Option „/S“ angegeben ist, interpretiert AzCopy die angegebenen Dateimuster als Blobpräfix. Wenn die Option „/S“ angegeben ist, vergleicht AzCopy die angegebenen Dateimuster mit den genauen Blobnamen.
          <br />
          Wenn es sich bei der angegebenen Quelle um eine Azure-Dateifreigabe handelt, müssen Sie entweder den genauen Dateinamen (z.&#160;B. „abc.txt“) angeben, um eine einzelne Datei zu kopieren, oder die Option „/S“ angeben, um alle Dateien in der Freigabe rekursiv zu kopieren. Wenn Sie sowohl ein Dateimuster als auch die Option „/S“ angeben, führt dies zu einem Fehler.
          <br />
          AzCopy beachtet für das Abgleichen die Groß-/Kleinschreibung, wenn die Quelle (/Source) ein BLOB-Container oder ein virtuelles BLOB-Verzeichnis ist. In allen anderen Fällen wird die Groß-/Kleinschreibung nicht beachtet.
          <br/>
          Das standardmäßige Dateimuster lautet *.* für ein Dateisystemspeicherort oder ein leeres Präfix für ein Azure Storage-Speicherort, wenn kein Dateimuster angegeben ist. Das Angeben mehrerer Dateimuster wird nicht unterstützt.</td>
    <td>J</td>
    <td>J<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;storage-key></b></td>
    <td>Gibt den Speicherkontoschlüssel für die Zielressource an.</td>
    <td>Y</td>
    <td>J<br /></td>
    <td>J<br /></td>
  </tr>
  <tr>
    <td class="auto-style1"><b>/DestSAS:&lt;sas-token></b></td>
    <td class="auto-style1">Gibt eine SAS (Shared Access Signature) mit LESE- und SCHREIB-Berechtigung für das Ziel an (falls zutreffend). Schließen Sie die SAS in doppelte Anführungszeichen ein, da sie möglicherweise spezielle Befehlszeilenzeichen enthält.<br />
        Wenn die Zielressource ein BLOB-Container, eine Dateifreigabe oder eine Tabelle ist, können Sie entweder diese Option gefolgt vom SAS-Token oder die SAS als Teil des Ziel-BLOB-Containers, der Dateifreigabe oder des URI der Tabelle ohne diese Option angeben.<br />
        Wenn es sich bei der Quelle und beim Ziel um Blobs handelt, muss sich das Zielblob im selben Speicherkonto wie das Quellblob befinden.</td>
    <td class="auto-style1">J</td>
    <td class="auto-style1">J<br /></td>
    <td class="auto-style1">J<br /></td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;storage-key></b></td>
    <td>Gibt den Speicherkontoschlüssel für die Quellressource an.</td>
    <td>Y</td>
    <td>J<br /></td>
    <td>J<br /></td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;sas-token></b></td>
    <td>Gibt eine Shared Access Signature mit LESE- und AUFLISTUNGS-Berechtigung für die Quelle an (falls zutreffend). Schließen Sie die SAS in doppelte Anführungszeichen ein, da sie möglicherweise spezielle Befehlszeilenzeichen enthält.
        <br />
        Wenn die Quellressource ein Blobcontainer ist und weder ein Schlüssel noch eine SAS angegeben wird, wird der Blobcontainer über den anonymen Zugriff gelesen.
        <br />
        Wenn die Quelle eine Dateifreigabe oder Tabelle ist, muss ein Schlüssel oder eine SAS angegeben werden.</td>
    <td>J</td>
    <td>J<br /></td>
    <td>Y<br /></td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>Gibt den rekursiven Modus für Kopiervorgänge an. Im rekursiven Modus kopiert AzCopy alle BLOBs oder Dateien, die mit dem angegebenen Dateimuster übereinstimmen, einschließlich der BLOBs oder Dateien in den Unterordnern.</td>
    <td>Y</td>
    <td>J<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;block | page | append></b></td>
    <td>Gibt an, ob es sich beim Zielblob um ein Block-, Seiten- oder Anfügeblob handelt. Diese Option kann nur beim Hochladen des Blobs angewendet werden. Ansonsten wird ein Fehler generiert. Wenn das Ziel ein Blob ist und diese Option nicht angegeben wurde, erstellt AzCopy standardmäßig einen Blockblob.</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/CheckMD5</b></td>
    <td>Berechnet einen MD5-Hash für heruntergeladene Daten und überprüft, ob der in der Content-MD5-Eigenschaft des BLOBs oder der Datei gespeicherte MD5-Hash mit dem berechneten Hash übereinstimmt. Die MD5-Prüfung ist standardmäßig deaktiviert, daher müssen Sie diese Option angeben, um die MD5-Prüfung beim Herunterladen von Daten auszuführen.
	<br />
    Beachten Sie, dass Azure Storage nicht dafür garantiert, dass der für den BLOB oder die Datei gespeicherte MD5-Hash auf dem neuesten Stand ist. Der Client ist für die MD5-Aktualisierung zuständig, wenn der BLOB oder die Datei geändert wird.
	<br />
    AzCopy legt die Content-MD5-Eigenschaft für einen Azure-BLOB oder eine entsprechende Datei immer fest, nachdem diese(r) in den Dienst hochgeladen wurde.</td>
    <td>Y</td>
    <td>Y<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Snapshot</b></td>
    <td>Gibt an, ob Momentaufnahmen übertragen werden. Diese Option ist nur gültig, wenn es sich bei der Quelle um einen BLOB handelt. 
        <br />
        Die übertragenen Blobmomentaufnahmen werden im folgenden Format umbenannt: [Blobname] (snapshot-time)[Erweiterung]. 
        <br />
        Momentaufnahmen werden standardmäßig nicht kopiert.</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:[Ausführliche Protokolldatei]</b></td>
    <td>Gibt ausführliche Statusmeldungen in eine Protokolldatei aus. Die ausführliche Protokolldatei erhält standardmäßig die Bezeichnung <code>AzCopyVerbose.log</code> und wird im Verzeichnis <code>%LocalAppData%\Microsoft\Azure\AzCopy</code> gespeichert. Wenn Sie für diese Option einen vorhandenen Dateispeicherort angeben, wird das ausführliche Protokoll an diese Datei angefügt.</td>
    <td>Y</td>
    <td>J<br /></td>
    <td>Y<br /></td>
  </tr>
  <tr>
    <td><b>/Z:[Journaldateiordner]</b></td>
    <td>Gibt einen Journaldateiordner zum Fortsetzen eines Vorgangs an.<br />
        AzCopy unterstützt die Fortsetzung immer, wenn ein Vorgang unterbrochen wurde.<br />
        Wenn diese Option nicht oder ohne einen Ordnerpfad angegeben wird, dann erstellt AzCopy die Journaldatei am Standardspeicherort <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>.<br />
        Sobald ein Befehl für AzCopy ausgeführt wird, prüft es, ob eine Journaldatei im Standardordner oder in einem Ordner vorhanden ist, den Sie über diese Option angegeben haben. Wenn die Journaldatei in keinem dieser Ordner vorhanden ist, behandelt AzCopy diesen Vorgang als neuen Vorgang und generiert somit eine neue Journaldatei.
        <br />
		Wenn die Journaldatei vorhanden ist, prüft AzCopy, ob die von Ihnen eingegebene Befehlszeile mit der Befehlszeile in der Journaldatei übereinstimmt. Wenn die beiden Befehlszeilen übereinstimmen, setzt AzCopy den unvollständigen Vorgang fort. Wenn sie nicht übereinstimmen, werden Sie aufgefordert, entweder die Journaldatei zu überschreiben, um einen neuen Vorgang zu starten, oder den aktuellen Vorgang abzubrechen. 
        <br />
        Nachdem der Vorgang erfolgreich abgeschlossen wurde, wird die Journaldatei gelöscht.
		<br />
		Beachten Sie, dass die Fortsetzung eines Vorgangs aus einer Journaldatei nicht unterstützt wird, die mit einer früheren Version von AzCopy erstellt wurde.</td>
    <td>Y</td>
    <td>J<br /></td>
    <td>J<br /></td>
  </tr>
  <tr>
    <td><b>/@:parameter-file</b></td>
    <td>Gibt eine Datei an, die Parameter enthält. AzCopy verarbeitet die Parameter in der Datei auf die gleiche Weise wie bei der Angabe über die Befehlszeile.<br /> 
		In einer Antwortdatei können Sie entweder mehrere Parameter in einer einzelnen Zeile oder jeden Parameter in einer eigenen Zeile angeben. Beachten Sie, dass ein einzelner Parameter nicht mehrere Zeilen umfassen darf. 
        <br />
		Antwortdateien können Kommentarzeilen einbeziehen, die mit dem Symbol <code>#</code> beginnen. 
        <br />
        Sie können mehrere Antwortdateien angeben. Beachten Sie jedoch, dass AzCopy geschachtelte Antwortdateien nicht unterstützt.</td>
    <td>Y</td>
    <td>J<br /></td>
    <td>Y<br /></td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>Unterdrückt alle Bestätigungsaufforderungen von AzCopy.</td>
    <td>Y</td>
    <td>J<br /></td>
    <td>Y<br /></td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>Gibt nur einen Auflistungsvorgang an. Dabei werden keine Daten kopiert.
    <br />
    AzCopy interpretiert diese Option so, dass ein Ausführen der Befehlszeile ohne die Option "/L" simuliert und gezählt wird, wie viele Objekte kopiert werden. Sie können gleichzeitig die Option "/V" angeben, um zu überprüfen, welche Objekte in das ausführliche Protokoll kopiert werden.
    <br />
    Das Verhalten dieser Option wird auch durch den Speicherort der Quelldaten sowie dadurch bestimmt, ob die Option "/S" für den rekursiven Modus und die Dateimusteroption "/Pattern" vorhanden sind.
    <br />
    Wird diese Option verwendet, benötigt AzCopy AUFLISTUNGS- und LESE-Berechtigung für den Quellspeicherort.</td>
    <td>J</td>
    <td>J<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>Legt für die Uhrzeit der letzten Änderung der heruntergeladenen Datei denselben Zeitpunkt wie für den Quell-BLOB oder die Quelldatei fest.</td>
    <td>Y</td>
    <td>J<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>Schließt eine neuere Quellressource aus. Die Ressource wird nicht kopiert, wenn der Zeitpunkt der letzten Änderung der Quelle mit dem Ziel identisch oder neuer ist.</td>
    <td>J</td>
    <td>J<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>Schließt eine ältere Quellressource aus. Die Ressource wird nicht kopiert, wenn der Zeitpunkt der letzten Änderung der Quelle mit dem Ziel identisch oder älter ist.</td>
    <td>J</td>
    <td>Y<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>Lädt ausschließlich Dateien hoch, für die das Archivattribut festgelegt ist.</td>
    <td>Y</td>
    <td>Y<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>Lädt ausschließlich Dateien hoch, für die eines der angegebenen Attribute festgelegt ist.<br />
        Zu den verfügbaren Attributen zählen:  
        <br />
        R&#160;&#160;&#160;Schreibgeschützte Dateien
        <br />
        A&#160;&#160;&#160;Dateien, die bereit sind für die Archivierung
        <br />
        S&#160;&#160;&#160;Systemdateien
        <br />
        H&#160;&#160;&#160;Versteckte Dateien
        <br />
        C&#160;&#160;&#160;Komprimierte Dateien
        <br />
        N&#160;&#160;&#160;Normale Dateien
        <br />
        E&#160;&#160;&#160;Verschlüsselte Dateien
        <br />
        T&#160;&#160;&#160;Temporäre Dateien
        <br />
        O&#160;&#160;&#160;Offlinedateien
        <br />
        I&#160;&#160;&#160;Nicht indizierte Dateien</td>
    <td>Y</td>
    <td>J<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>Schließt Dateien aus, für die eines der angegebenen Attribute festgelegt ist.<br />
        Zu den verfügbaren Attributen zählen:  
        <br />
        R&#160;&#160;&#160;Schreibgeschützte Dateien  
        <br />
        A&#160;&#160;&#160;Dateien, die bereit sind für die Archivierung  
        <br />
        S&#160;&#160;&#160;Systemdateien  
        <br />
        H&#160;&#160;&#160;Versteckte Dateien  
        <br />
        C&#160;&#160;&#160;Komprimierte Dateien  
        <br />
        N&#160;&#160;&#160;Normale Dateien  
        <br />
        E&#160;&#160;&#160;Verschlüsselte Dateien  
        <br />
        T&#160;&#160;&#160;Temporäre Dateien  
        <br />
        O&#160;&#160;&#160;Offlinedateien  
        <br />
        I&#160;&#160;&#160;Nicht indizierte Dateien</td>
    <td>Y</td>
    <td>Y<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;delimiter></b></td>
    <td>Zeigt das Trennzeichen an, mit dem virtuelle Verzeichnisse in einem BLOB-Namen getrennt werden.<br />
        AzCopy verwendet „/“ standardmäßig als Trennzeichen. AzCopy unterstützt jedoch die Verwendung beliebiger allgemeiner Zeichen (z.&#160;B. @, # oder %) als Trennzeichen. Wenn Sie eines dieser Sonderzeichen auf der Befehlszeile einbeziehen müssen, schließen Sie den Dateinamen in doppelte Anführungszeichen ein. 
        <br />
        Diese Option kann nur zum Herunterladen von BLOBs angewendet werden.</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC:&lt;number-of-concurrents></b></td>
    <td>Gibt die Anzahl gleichzeitiger Vorgänge an.
        <br />
        AzCopy startet standardmäßig eine bestimmte Anzahl gleichzeitiger Vorgänge zum Erhöhen des Datenübertragungsdurchsatzes. Beachten Sie, dass eine große Anzahl gleichzeitiger Vorgänge in einer Umgebung mit geringer Bandbreite die Netzwerkverbindung überlasten kann, wodurch möglicherweise verhindert wird, dass die Vorgänge vollständig abgeschlossen werden. Senken Sie die Anzahl gleichzeitiger Vorgänge auf Basis der tatsächlich verfügbaren Netzwerkbandbreite.
        <br />
		Maximal können 512 gleichzeitige Vorgänge ausgeführt werden.</td>
    <td>J</td>
    <td>J<br /></td>
    <td>J<br /></td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob|Table</b></td>
    <td>Gibt an, dass die <code>source</code>-Ressource ein in der lokalen Entwicklungsumgebung verfügbares Blob ist (Ausführung im Speicheremulator).</td>
    <td>J</td>
    <td>N</td>
    <td>J<br /></td>
  </tr>
  <tr>
    <td><b>/DestType:Blob|Table</b></td>
    <td>Gibt an, dass die <code>destination</code>-Ressource ein in der lokalen Entwicklungsumgebung verfügbares Blob ist (Ausführung im Speicheremulator).</td>
    <td>J</td>
    <td>N</td>
    <td>Y<br /></td>
  </tr>
  <tr>
    <td><strong>/PKRS:&lt;"key1#key2#key3#..."></strong></td>
    <td>Teilt den Partitionsschlüsselbereich auf, um parallel das Exportieren von Tabellendaten zu ermöglichen, wodurch die Geschwindigkeit des Exportvorgangs erhöht wird.
        <br />
        Wenn diese Option nicht angegeben ist, verwendet AzCopy einen einzelnen Thread zum Exportieren von Tabellenentitäten. Wenn der Benutzer beispielsweise /PKRS:"aa#bb" angibt, startet AzCopy drei gleichzeitige Vorgänge.
        <br />
        Jeder Vorgang exportiert einen der drei Partitionsschlüsselbereiche, wie dies im Folgenden gezeigt wird. 
        <br />
        &#160;&#160;&#160;[&lt;erster Partitionsschlüssel>, aa) 
        <br />
        &#160;&#160;&#160;[aa, bb)
        <br />
        &#160;&#160;&#160;[bb, &lt;letzter Partitionsschlüssel>] </td>
    <td>N</td>
    <td>N</td>
    <td>J<br /></td>
  </tr>
  <tr>
    <td><strong>/SplitSize:</strong><file-size><strong>&lt;file-size></strong></td>
    <td>Gibt die Teilungsgröße der exportierten Datei in MB an. Der zulässige Mindestwert ist 32.
        <br />
        Wenn diese Option nicht angegeben ist, exportiert AzCopy Tabellendaten in eine einzelne Datei.
        <br />
        Wenn die Tabellendaten in ein Blob exportiert wird, und die exportierte Dateigröße die 200-GB-Begrenzung für die Blobgröße erreicht, teilt AzCopy die exportierte Datei auf, selbst wenn diese Option nicht angegeben ist. </td>
    <td>N</td>
    <td>N</td>
    <td>J<br /></td>
  </tr>
  <tr>
    <td><b>/EntityOperation:&lt;InsertOrSkip | InsertOrMerge | InsertOrReplace> </b>
</td>
    <td>Gibt das Tabellendaten-Importverhalten an.
        <br />
        InsertOrSkip – Überspringt eine vorhandene Entität oder fügt eine neue Entität ein, wenn sie in der Tabelle nicht vorhanden ist.
        <br />
        InsertOrMerge – Führt eine vorhandene Entität zusammen oder fügt eine neue Entität ein, wenn sie in der Tabelle nicht vorhanden ist.
        <br />
        InsertOrReplace – Ersetzt eine vorhandene Entität oder fügt eine neue Entität ein, wenn sie in der Tabelle nicht vorhanden ist. </td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /></td>
  </tr>
  <tr>
    <td><b>/Manifest:&lt;manifest-file></b></td>
    <td>Gibt die Manifestdatei für den Tabellenexport- und -importvorgang an. <br />
    Diese Option ist während eines Exportvorgangs optional. AzCopy generiert eine Manifestdatei mit vordefiniertem Namen, wenn diese Option nicht angegeben ist.
    <br />
    Diese Option ist während eines Importvorgangs erforderlich, damit die Datendateien gefunden werden.</td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /></td>
  </tr>
  <tr>
    <td><b>/SyncCopy</b></td>
    <td>Gibt an, ob Blobs oder Dateien synchron zwischen zwei Azure-Speicherendpunkten kopiert werden sollen. <br />
		Standardmäßig führt AzCopy serverseitige asynchrone Kopiervorgänge aus. Geben Sie diese Option an, um Kopiervorgänge synchron ausführen zu lassen. Blobs und Dateien werden so in den lokalen Arbeitsspeicher herunter- und dann in den Azure-Speicher hochgeladen. Diese Option eignet sich zum Kopieren von Dateien innerhalb eines Blob-Speichers oder eines Dateispeichers sowie zwischen Blob- und Dateispeicher.</td>
    <td>J</td>
    <td>J<br /></td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SetContentType:&lt;content-type></b></td>
    <td>Gibt den MIME-Inhaltstyp für Ziel-Blobs oder -dateien an. <br />
		AzCopy legt den Inhaltstyp eines Blobs oder einer Datei standardmäßig als <code>application/octet-stream</code> fest. Sie können den Inhaltstyp für alle Blobs oder Dateien ändern, indem Sie für diese Option einen Wert angeben. Wenn Sie für diese Option keinen Wert angeben, legt AzCopy den Inhaltstyp jedes Blobs und jeder Datei gemäß der jeweiligen Dateierweiterung fest.</td>
    <td>J</td>
    <td>J<br /></td>
    <td>N</td>
  </tr>
    <tr>
    <td><b>/PayloadFormat:&lt;JSON | CSV></b></td>
    <td>Gibt das Format der exportierten Tabellendatendatei an.<br />
    Ist diese Option nicht angegeben, exportiert AzCopy eine Tabellendatendatei standardmäßig im JSON-Format.</td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /></td>
  </tr>
</table>
<br/>

## Einschränken gleichzeitiger Schreibvorgänge beim Kopieren von Daten

Wenn Sie BLOBs oder Dateien mit AzCopy kopieren, denken Sie daran, dass eine andere Anwendung die Daten möglicherweise modifiziert, während Sie diese kopieren. Stellen Sie nach Möglichkeit sicher, dass die von Ihnen kopierten Daten während des Kopiervorgangs nicht verändert werden. Wenn Sie z. B. eine virtuelle Festplatte (Virtual Hard Disk, VHD) mit einem virtuellen Azure-Computer kopieren, stellen Sie sicher, dass derzeit keine anderen Anwendungen auf diese virtuelle Festplatte schreiben. Alternativ können Sie zunächst eine Momentaufnahme der virtuellen Festplatte (VHD) erstellen und anschließend die Momentaufnahme kopieren.

Wenn Sie andere Anwendungen nicht daran hindern können, während des Kopiervorgangs in BLOBs oder Dateien zu schreiben, beachten Sie, dass die kopierten Ressourcen bei der Beendigung des Auftrags möglicherweise nicht mehr vollständig mit den Quellressourcen übereinstimmen.

## Kopieren von Azure-BLOBs mit AzCopy

Die nachfolgenden Beispiele veranschaulichen eine Reihe von Szenarien zum Kopieren von BLOBs mit AzCopy.

### Kopieren einzelner BLOBs

**Hochladen einer Datei aus dem Dateisystem in den Blobspeicher:**
	
	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

**Herunterladen eines Blobs aus dem Blobspeicher in das Dateisystem:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Weitere Informationen zur Verwendung von Speicherzugriffsschlüsseln finden Sie unter [Informationen zu Azure-Speicherkonten](../storage-create-storage-account/#regeneratestoragekeys).

### Kopieren von BLOBs über serverseitige Kopien

Wenn Sie einen BLOB innerhalb eines Speicherkontos oder zwischen Speicherkonten kopieren, wird ein serverseitiger Kopiervorgang ausgeführt. Weitere Informationen zu serverseitigen Kopiervorgängen finden Sie unter [Einführung zum asynchronen kontoübergreifenden Kopier-BLOB](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

**Kopieren von Blobs innerhalb eines Speicherkontos:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt 

**Kopieren von Blobs zwischen Speicherkonten:**

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
 
### Kopieren von BLOBs aus der sekundären Region 

Wenn für Ihr Speicherkonto der Lesezugriff auf georedundanten Speicher aktiviert ist, können Sie Daten aus der sekundären Region kopieren.

**Kopieren von Blobs aus dem sekundären ins primäre Konto:**

	AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

**Herunterladen von Blobs aus dem sekundären Konto in eine Datei des Dateisystems:**

	AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### Hochladen von Dateien in einen neuen BLOB-Container oder in ein neues virtuelles BLOB-Verzeichnis

**Hochladen von Dateien in einen neuen Blobcontainer**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mynewcontainer /DestKey:key /Pattern:abc.txt

Wenn der angegebene Zielcontainer nicht vorhanden ist, wird er von AzCopy erstellt und die Datei anschließend in den Container hochgeladen.

**Hochladen von Dateien in ein neues virtuelles Blobverzeichnis**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Wenn das angegebene virtuelle Verzeichnis nicht vorhanden ist, lädt AzCopy die Datei so hoch, dass das virtuelle Verzeichnis Teil des Dateinamens wird (*z. B.* `vd/abc.txt` im obigen Beispiel).

### Herunterladen von BLOBs in einen neuen Ordner

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Wenn der Ordner `C:\myfolder` noch nicht vorhanden ist, wird er von AzCopy im Dateisystem erstellt. Anschließend wird `abc.txt ` in den neuen Ordner heruntergeladen.

### Rekursives Hochladen von Dateien und Unterordnern eines Verzeichnisses in einen Container

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

Wenn Sie die Option `/S` angeben, werden die Inhalte des angegebenen Verzeichnisses rekursiv in den Blobspeicher kopiert. Das bedeutet, dass ebenso alle Unterordner und die darin enthaltenen Dateien kopiert werden. Nehmen Sie z. B. an, die folgenden Dateien befinden sich im Ordner `C:\myfolder`:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

Nach dem Kopiervorgang enthält der Container die folgenden Dateien:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### Nicht rekursives Hochladen von Dateien aus einem Verzeichnis in einen Container

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key

Wenn Sie die Option `/S` nicht an der Befehlszeile angeben, wird der Kopiervorgang von AzCopy nicht rekursiv ausgeführt. Es werden nur die Dateien im angegebenen Verzeichnis kopiert. Es werden KEINE Unterordner oder die darin enthaltenen Dateien kopiert. Nehmen Sie z. B. an, die folgenden Dateien befinden sich im Ordner `C:\myfolder`:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

Nach dem Kopiervorgang enthält der Container die folgenden Dateien:

	abc.txt
	abc1.txt
	abc2.txt

### Rekursives Herunterladen aller BLOBs eines Containers in ein Verzeichnis im Dateisystem

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Nehmen Sie an, die folgenden BLOBs befinden sich im angegebenen Container:

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

Nach dem Kopiervorgang enthält das Verzeichnis `C:\myfolder` die folgenden Dateien:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\vd1\a.txt
	C:\myfolder\vd1\abcd.txt

### Rekursives Herunterladen von BLOBs in ein virtuelles BLOB-Verzeichnis im Dateisystem

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/vd1/ /Dest:C:\myfolder /SourceKey:key /S

Nehmen Sie an, die folgenden BLOBs befinden sich im angegebenen Container:

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

Nach dem Kopiervorgang enthält das Verzeichnis `C:\myfolder` die folgenden Dateien: Beachten Sie, dass nur BLOBs im virtuellen Verzeichnis kopiert werden:

	C:\myfolder\a.txt
	C:\myfolder\abcd.txt

### Rekursives Hochladen von Dateien, die mit dem angegebenen Dateimuster übereinstimmen, in einen Container 

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Nehmen Sie z. B. an, die folgenden Dateien befinden sich im Ordner `C:\myfolder`:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\xyz.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

Nach dem Kopiervorgang enthält der Container die folgenden Dateien:

	abc.txt
	abc1.txt
	abc2.txt
	subfolder\a.txt
	subfolder\abcd.txt
	
### Rekursives Herunterladen von BLOBs mit angegebenem Präfix in das Dateisystem

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Nehmen Sie an, die folgenden BLOBs befinden sich im angegebenen Container. Alle Blobs, die mit dem Präfix `a` beginnen, werden kopiert:

	abc.txt
	abc1.txt
	abc2.txt
	xyz.txt
	vd1\a.txt
	vd1\abcd.txt

Nach dem Kopiervorgang enthält der Ordner `C:\myfolder` die folgenden Dateien:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

Beachten Sie, dass das Präfix für das virtuelle Verzeichnis gilt, das den ersten Teil des BLOB-Namens bildet. Im obigen Beispiel stimmt das virtuelle Verzeichnis nicht mit dem angegebenen Präfix überein, daher wird es nicht kopiert.


### Kopieren von BLOBs und ihren Momentaufnamen in ein anderes Speicherkonto

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Nach dem Kopiervorgang enthält der Zielcontainer den BLOB und seine Momentaufnahmen. Unter der Annahme, der BLOB im obigen Beispiel besitzt zwei Momentaufnahmen, enthält der Container folgenden BLOB und folgende Momentaufnahmen:

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt


### Verwenden einer Antwortdatei zum Angeben von Befehlszeilenparametern

	AzCopy /@:"C:\myfolder\abc.txt"

Sie können beliebige AzCopy-Befehlszeilenparameter in eine Antwortdatei einfügen. AzCopy verarbeitet die Parameter in der Datei ebenso wie bei der Angabe über die Befehlszeile, wobei mit den Inhalten der Datei ein direkter Austausch erfolgt.

**Angeben mindestens einer Antwortdateien mit einzelner Zeile**

Nehmen wir an, eine Antwortdatei namens `source.txt` gibt einen Quellcontainer an:

	/Source:http://myaccount.blob.core.windows.net/mycontainer

Und eine Antwortdatei namens `dest.txt` gibt einen Zielordner im Dateisystem an:

	/Dest:C:\myfolder

Und eine Antwortdatei namens `options.txt` gibt die Optionen für AzCopy an:

	/S /Y

Verwenden Sie den folgenden Befehl, um AzCopy mit diesen Antwortdateien aufzurufen, die sich jeweils im Verzeichnis `C:\responsefiles` befinden:

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy verarbeitet diesen Befehl auf dieselbe Weise wie bei der Angabe der einzelnen Parameter in der Befehlszeile:

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

**Angeben einer Antwortdatei mit mehreren Zeilen**

Nehmen wir an, eine Antwortdatei namens `copyoperation.txt` enthält die folgenden Zeilen. Jeder AzCopy-Parameter wird in einer eigenen Zeile angegeben:

	/Source:http://myaccount.blob.core.windows.net/mycontainer
	/Dest:C:\myfolder
	/SourceKey:<sourcekey>
	/S 
	/Y

Verwenden Sie den Befehl, um AzCopy mit diesen Antwortdateien aufzurufen:

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy verarbeitet diesen Befehl auf dieselbe Weise wie bei der Angabe der einzelnen Parameter in der Befehlszeile:

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

Beachten Sie, dass jeder AzCopy-Parameter in einer Zeile angegeben werden muss. Wenn Sie den Parameter z. B. auf zwei Zeilen verteilen, tritt bei der Ausführung von AzCopy ein Fehler auf, wie hier für den `/sourcekey`-Parameter gezeigt:

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	[sourcekey]
	/S 
	/Y

### Angeben einer Shared Access Signature (SAS)
	
**Angeben einer SAS für den Quellcontainer mithilfe der Option "/sourceSAS"**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /DestC:\myfolder /SourceSAS:SAS /S

**Angeben einer SAS für den Quellcontainer im URI des Quellcontainers**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

**Angeben einer SAS für den Zielcontainer mithilfe der Option "/destSAS"**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer1 /DestSAS:SAS /Pattern:abc.txt

**Angeben einer SAS für die Quell- und Zielcontainer**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

### Angeben eines Journaldateiordners

Sobald ein Befehl für AzCopy ausgeführt wird, prüft es, ob eine Journaldatei im Standardordner oder in einem Ordner vorhanden ist, den Sie über diese Option angegeben haben. Wenn die Journaldatei in keinem dieser Ordner vorhanden ist, behandelt AzCopy diesen Vorgang als neuen Vorgang und generiert somit eine neue Journaldatei.

Wenn die Journaldatei vorhanden ist, prüft AzCopy, ob die von Ihnen eingegebene Befehlszeile mit der Befehlszeile in der Journaldatei übereinstimmt. Wenn die beiden Befehlszeilen übereinstimmen, setzt AzCopy den unvollständigen Vorgang fort. Wenn sie nicht übereinstimmen, werden Sie aufgefordert, entweder die Journaldatei zu überschreiben, um einen neuen Vorgang zu starten, oder den aktuellen Vorgang abzubrechen.

**Verwenden des Standardspeicherorts für die Journaldatei**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Wenn Sie die Option `/Z` auslassen oder die Option `/Z` ohne den Ordnerpfad angeben (wie oben gezeigt), dann erstellt AzCopy die Journaldatei am Standardspeicherort `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Wenn die Journaldatei bereits vorhanden ist, setzt AzCopy den Vorgang auf Basis der Journaldatei fort.

**Angeben eines benutzerdefinierten Speicherorts für die Journaldatei**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

In diesem Beispiel wird die Journaldatei erstellt, sofern diese nicht bereits vorhanden ist. Wenn sie vorhanden ist, setzt AzCopy den Vorgang auf Basis der Journaldatei fort.

**Fortsetzen eines AzCopy-Vorgangs**

	AzCopy /Z:C:\journalfolder\

In diesem Beispiel wird der letzte Vorgang fortgesetzt, der möglicherweise nicht abgeschlossen werden konnte.


### Generieren einer Protokolldatei

**Schreiben in die ausführliche Protokolldatei am Standardspeicherort**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Wenn Sie die Option `/V` ohne einen Dateipfad zum ausführlichen Protokoll angeben, dann erstellt AzCopy die Protokolldatei am Standardspeicherort `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

**Schreiben in die ausführliche Protokolldatei an einem benutzerdefinierten Speicherort**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Wenn Sie nach der Option `/V` einen relativen Pfad angeben, z. B. `/V:test/azcopy1.log`, dann wird das ausführliche Protokoll im aktuellen Arbeitsverzeichnis in einem Unterordner namens `test` erstellt.


### Festlegen desselben Zeitpunkts für die Uhrzeit der letzten Änderung der heruntergeladenen Dateien wie für die Quell-BLOBs

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

### Ausschließen von BLOBs aus dem Kopiervorgang auf Basis der Uhrzeit der letzten Änderung

Geben Sie die Option `/MT` an, um die Uhrzeit der letzten Änderung des Quellblobs mit der Zieldatei zu vergleichen.

**Ausschließen von Blobs, deren Zeitpunkt der letzten Änderung mit der Zieldatei identisch oder neuer ist**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

**Ausschließen von Blobs, deren Zeitpunkt der letzten Änderung mit der Zieldatei identisch oder älter ist**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

### Angeben der Anzahl der zu startenden gleichzeitigen Vorgängen

Die Option `/NC` gibt die Anzahl der gleichzeitigen Kopiervorgänge an. Standardmäßig startet AzCopy gleichzeitige Vorgänge mit dem Achtfachen der Anzahl der verfügbaren Core-Prozessoren. Wenn Sie AzCopy in einem Netzwerk mit geringer Bandbreite ausführen, können Sie eine niedrigere Anzahl für diese Option angeben, um Fehler durch gleichzeitig verwendete Ressourcen zu vermeiden.


### 	Ausführen von AzCopy für BLOB-Ressourcen im Speicheremulator

	AzCopy /Source:https://127.0.0.1:10004/myaccount/myfileshare/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

### Synchrones Kopieren von Blobs zwischen zwei Azure Storage-Endpunkten

Standardmäßig kopiert AzCopy Daten zwischen zwei Speicherendpunkten auf asynchrone Weise. Der Kopiervorgang wird also im Hintergrund ausgeführt und verwendet nur nicht reservierte Bandbreite, für die bezüglich der Kopiergeschwindigkeit für Blobs kein SLA besteht. AzCopy überprüft den Kopierstatus regelmäßig, bis der Vorgang beendet ist oder abbricht.

Die Option `/SyncCopy` gewährleistet, dass der jeweilige Kopiervorgang mit gleichmäßiger Geschwindigkeit erfolgt. Beim synchronen Kopieren lädt AzCopy die zu kopierenden Blobs aus der angegebenen Quelle in den lokalen Arbeitsspeicher herunter und dann in das Speicherziel des jeweiligen Blobs hoch.

	AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

Hinweis: Anders als beim asynchronen Kopieren kann es bei der Verwendung von `/SyncCopy` zu Zusatzkosten für den ausgehenden Datenverkehr kommen. Um dies zu vermeiden, sollten Sie diese Option in dem virtuellen Azure-Computer verwenden, der sich in derselben Region wie das Quellspeicherkonto befindet.

### Angeben des MIME-Inhaltstyps für Ziel-Blobs

AzCopy legt den Inhaltstyp eines Zielblobs standardmäßig als `application/octet-stream` fest. Ab Version 3.1.0 können Sie den Inhaltstyp mit der Option `/SetContentType:[content-type]` explizit angeben. Mit dieser Syntax wird dann der Inhaltstyp für alle zu kopierenden Blobs festgelegt.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Wenn Sie für `/SetContentType` keinen Wert angeben, legt AzCopy den Inhaltstyp jedes Blobs und jeder Datei gemäß der jeweiligen Dateierweiterung fest.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## Kopieren von Dateien im Azure-Dateispeicher mit AzCopy

Die nachfolgenden Beispiele veranschaulichen eine Reihe von Szenarien zum Kopieren von Azure-Dateien mit AzCopy.

### Herunterladen von Dateien aus einer Azure-Dateifreigabe in das Dateisystem

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Hinweis: Wenn es sich bei der angegebenen Quelle um eine Azure-Dateifreigabe handelt, müssen Sie entweder den genauen Dateinamen (*z. B.* `abc.txt`) angeben, um eine einzelne Datei zu kopieren, oder die Option `/S`, um alle Dateien in der Freigabe rekursiv zu kopieren. Wenn Sie sowohl ein Dateimuster als auch die Option `/S` angeben, führt dies zu einem Fehler.

### Rekursives Herunterladen von Dateien und Ordnern in einer Azure-Dateifreigabe in das Dateisystem, Angeben der Share Access Signature

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceSAS:SAS /S

Beachten Sie, dass leere Ordner nicht kopiert werden.


### Rekursives Hochladen von Dateien und Ordnern aus dem Dateisystem in eine Azure-Dateifreigabe

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Beachten Sie, dass leere Ordner nicht kopiert werden.


### Rekursives Hochladen von Dateien, die mit dem angegebenen Dateimuster übereinstimmen, in eine Azure-Dateifreigabe

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

### Asynchrones Kopieren von Dateien in einen Azure-Dateispeicher

Azure-Dateispeicher unterstützt serverseitiges asynchrones Kopieren.

Asynchrones Kopieren aus einem Dateispeicher in einen Dateispeicher:

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

Asynchrones Kopieren aus einem Dateispeicher in ein Blockblob:
  
	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

Asynchrones Kopieren aus einem Block-/Seitenblob-Speicher in einen Dateispeicher:

	AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

Asynchrones Kopieren aus einem Dateispeicher in ein Seitenblob wird nicht unterstützt.

### Synchrones Kopieren von Dateien in den Azure-Dateispeicher

Außer dem asynchronen Kopieren kann ein Benutzer auch die Option `/SyncCopy` angeben, um Daten synchron aus einem Dateispeicher in einen Dateispeicher, aus einem Dateispeicher in einen Blob-Speicher oder aus einem Blobspeicher in einen Dateispeicher zu kopieren. AzCopy lädt dazu die Quelldaten in den lokalen Arbeitsspeicher herunter und lädt diese Daten dann in das Ziel hoch.

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
	
	AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

Der Standardblobtyp beim Kopieren aus File Storage in einen Blob Storage ist Blockblob. Benutzer können die Option `/BlobType:page` angeben, um den Zielblobtyp zu ändern.

Hinweis: Anders als beim asynchronen Kopieren kann es bei der Verwendung von `/SyncCopy` zu Zusatzkosten für den ausgehenden Datenverkehr kommen. Um dies zu vermeiden, sollten Sie diese Option in dem virtuellen Azure-Computer verwenden, der sich in derselben Region wie das Quellspeicherkonto befindet.


## Kopieren von Entitäten in einer Azure-Tabelle mit AzCopy

Die nachfolgenden Beispiele veranschaulichen eine Reihe von Szenarien zum Kopieren von Azure-Tabellenentitäten mit AzCopy.

### Exportieren von Entitäten zum lokalen Dateisystem

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy schreibt eine Manifestdatei in angegebenen Zielordner oder Blobcontainer. Die Manifestdatei wird durch den Importvorgang verwendet, um die erforderlichen Datendateien zu suchen und um die Datenüberprüfung während des Importvorgangs auszuführen. Für die Manifestdatei wird standardmäßig die folgende Namenskonvention verwendet:

	<account name>_<table name>_<timestamp>.manifest

Ein Benutzer kann auch die Option `/Manifest:<manifest file name>` angeben, um den Namen der Manifestdatei festzulegen.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest


### Exportieren von Entitäten im JSON- und CSV-Datendateiformat

AzCopy exportiert Tabellenentitäten standardmäßig in JSON-Dateien. Ein Benutzer kann aber die Option `/PayloadFormat:JSON|CSV` angeben, um den Typ der exportierten Datendatei festzulegen.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

Wird das CSV-Nutzlastformat angegeben, erstellt AzCopy zusätzlich zu den Datendateien mit der `.csv`-Erweiterung, die in dem Speicherort zu finden sind, der durch den Parameter `/Dest` angegeben ist, für jede Datendatei eine Schemadatei mit der Erweiterung `.schema.csv`. Beachten Sie, dass AzCopy keine Unterstützung für ein "Importieren" einer CSV-Datendatei bietet. Sie können das JSON-Format verwenden, um Tabellendaten zu exportieren und zu importieren.

### Exportieren von Entitäten zu einem Azure-Blob

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy generiert eine JSON-Datendatei in den lokalen Ordner oder Blobcontainer mit der folgenden Namenskonvention:

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Die generierte JSON-Datendatei folgt dem Nutzlastformat für minimale Metadaten. Details in Bezug auf dieses Nutzlastformat finden Sie unter [Nutzlastformat für Tabellendienstvorgänge](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Hinweis: Beim Exportieren von Speichertabellenentitäten in das Speicherblob exportiert AzCopy die Tabellenentitäten zunächst in lokale temporäre Datendateien und lädt sie dann in das Blob hoch. Dabei werden diese temporären Dateien im Journaldateiordner mit dem Standardpfad <code>%LocalAppData%\\Microsoft\\Azure\\AzCopy</code> gespeichert, und Sie können die Option "/Z: [Journaldateiordner]" angeben, um den Speicherort des Journaldateiordners und damit den temporären Speicherort für die Datendateien zu ändern. Die Größe der temporären Datendateien wird durch die Tabellenentitäten und die Größe bestimmt, die Sie mit der Option "/SplitSize" angegeben haben. Auch wenn die temporäre Datei auf dem lokalen Datenträger sofort gelöscht wird, sobald sie in das Blob hochgeladen wurde, sollten Sie sicherstellen, dass Sie über genügend lokalen Speicherplatz für diese temporären Dateien verfügen, bevor sie gelöscht werden,

### Aufteilen der exportierten Dateien

	AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy verwendet einen *Volumeindex* in den Dateinamen für die Aufteilungsdaten zum Unterscheiden mehrerer Dateien. Der Volumeindex besteht aus zwei Teilen, einem *Partitionsschlüsselbereichs-Index* und einen *Aufteilungsdateiindex*. Beide Indizes sind nullbasiert.

Der Partitionsschlüsselbereichs-Index ist 0, wenn der Benutzer die Option `/PKRS` (wird im nächsten Abschnitt vorgestellt) nicht angibt.

Angenommen, AzCopy generiert zwei Datendateien, nachdem der Benutzer die Option `/SplitSize` angegeben hat. Die daraus resultierenden Datendateinamen könnten wie folgt aussehen:

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Beachten Sie, dass der zulässige Mindestwert für die Option `/SplitSize` 32 MB lautet. Wenn es sich beim angegebenen Ziel um einen Blobspeicher handelt, teilt AzCopy die Datendatei auf, sobald die Größe die Blobgrößenbegrenzung (200 GB) erreicht, und zwar unabhängig davon, ob die Option `/SplitSize` durch den Benutzer angegeben wurde.

### Paralleles Exportieren von Entitäten

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

AzCopy startet gleichzeitige Vorgänge zum Exportieren von Entitäten, wenn der Benutzer die Option `/PKRS` angibt. Jeder Vorgang exportiert einen Partitionsschlüsselbereich.

Beachten Sie, dass die Anzahl gleichzeitiger Vorgänge auch durch die Option `/NC` gesteuert wird. AzCopy verwendet beim Kopieren von Tabellenentitäten die Anzahl der Core-Prozessoren als den Standardwert von `/NC`, selbst wenn `/NC` nicht angegeben wurde. Wenn der Benutzer die Option `/PKRS` angibt, verwendet AzCopy den kleineren der beiden Werte (Partitionsschlüsselbereich im Vergleich zu implizit oder explizit angegebenen gleichzeitigen Vorgängen), um die Anzahl der zu startenden gleichzeitigen Vorgänge zu bestimmen. Geben Sie Geben Sie `AzCopy /?:NC` an der Befehlszeile ein, um weitere Informationen zu erhalten.

### Paralleles Importieren von Entitäten

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace 

Die Option `/EntityOperation` gibt an, wie Entitäten in die Tabelle eingefügt werden. Mögliche Werte:

- `InsertOrSkip`: Überspringt eine vorhandene Entität oder fügt eine neue Entität ein, wenn sie in der Tabelle nicht vorhanden ist.
- `InsertOrMerge`: Führt eine vorhandene Entität zusammen oder fügt eine neue Entität ein, wenn sie in der Tabelle nicht vorhanden ist.
- `InsertOrReplace`: Ersetzt eine vorhandene Entität oder fügt eine neue Entität ein, wenn sie in der Tabelle nicht vorhanden ist.

Beachten Sie, dass Sie die Option `/PKRS` im Importszenario nicht angeben können. Im Gegensatz zum Exportszenario, in dem Sie die Option `/PKRS` zum Starten gleichzeitiger Vorgänge angeben müssen, startet AzCopy beim Importieren von Entitäten standardmäßig gleichzeitige Vorgänge. Die standardmäßige Anzahl gleichzeitig gestarteter Vorgänge entspricht der Anzahl an Core-Prozessoren. Sie können jedoch eine unterschiedliche Anzahl gleichzeitiger Vorgänge mit der Option `/NC` angeben. Geben Sie `AzCopy /?:NC` an der Befehlszeile ein, um weitere Informationen zu erhalten.


## Bekannte Probleme und Best Practices

#### Führen Sie eine AzCopy-Instanz auf einem Computer aus.
AzCopy ist darauf ausgelegt, die Auslastung Ihrer Computerressourcen zu maximieren, um die Datenübertragung zu beschleunigen. Am besten führen Sie nur eine AzCopy-Instanz auf einem Computer aus und geben die Option `/NC` an, wenn mehrere gleichzeitige Vorgänge erforderlich sind. Geben Sie `AzCopy /?:NC` an der Befehlszeile ein, um weitere Informationen zu erhalten.

#### Aktivieren Sie den FIPS-konformen MD5-Algorithmus für AzCopy, wenn Sie "FIPS-konformen Algorithmus für Verschlüsselung, Hashing und Signatur verwenden".
AzCopy verwendet standardmäßig die MD5-Implementierung von .NET, um den MD5 beim Kopieren von Objekten zu berechnen. Es gibt aber einige Sicherheitsanforderungen, die zu berücksichtigen sind, damit AzCopy eine FIPS-konforme MD5-Einstellung aktivieren kann.

Sie können eine Anwendungskonfigurationsdatei namens `AzCopy.exe.config` erstellen, die die Eigenschaft `AzureStorageUseV1MD5` enthält, und diese Datei im selben Speicherort wie "AzCopy.exe" ablegen.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	  <appSettings>
	    <add key="AzureStorageUseV1MD5" value="false"/>
	  </appSettings>
	</configuration>

Ist die Eigenschaft "AzureStorageUseV1MD5" gleich "true" (Standardwert), verwendet AzCopy die MD5-Implementierung von .NET. Ist die Eigenschaft gleich "false", verwendet AzCopy den FIPS-konformen MD5-Algorithmus.

Auf einem Windows-Computer ist der FIPS-konforme Algorithmus standardmäßig deaktiviert. Sie können "secpol.msc" im Fenster "Ausführen" eingeben und diesen Parameter über "Sicherheitseinstellungen -> Lokale Richtlinien -> Sicherheitsoptionen -> Systemkryptografie: FIPS-konformen Algorithmus für Verschlüsselung, Hashing und Signatur verwenden" überprüfen.

## AzCopy-Versionen

> [AZURE.NOTE]Wir empfehlen das Installieren der neuesten Version von AzCopy, um in den Genuss neuer Features und einer besseren Leistung zu kommen.

| Version | Neuerungen | Refenzierte .NET-Clientbibliotheksversioin | Zielversion der Storage-REST-API |
|---------|-----------------------------------------------------------------------------------------------------------------|--------|----------|
| [**V5.0.0**](http://xdmrelease.blob.core.windows.net/azcopy-5-0-0/MicrosoftAzureStorageTools.msi) | **Aktuelle Vorabversion. Enthält die gesamte Funktionalität von V4.2.0. Alle Funktionen für Datei-und Tabellenspeicher sind jetzt allgemein verfügbar.** | **V6.0.0** | **2015-04-05**
| [V4.2.0](http://xdmrelease.blob.core.windows.net/azcopy-4-2-0-preview/MicrosoftAzureStorageTools.msi) | Enthält die gesamte Funktionalität von V3.2.0. Unterstützt auch SAS für Dateispeicherfreigaben, asynchrones Kopieren von Dateispeicher, Exportieren von Tabellenentitäten in CSV und Angeben von Manifestnamen, wenn Tabellenentitäten exportiert werden | V5.0.0 | 2015-02-21
| [V3.2.0](http://xdmrelease.blob.core.windows.net/azcopy-3-2-0/MicrosoftAzureStorageTools.msi) | Unterstützt Anfügeblobs und FIPS-kompatible MD5-Einstellung | V5.0.0 | 2015-02-21
| [V4.1.0](http://xdmrelease.blob.core.windows.net/azcopy-4-1-0-preview/MicrosoftAzureStorageTools.msi) | Enthält die gesamte Funktionalität von V3.1.0. Unterstützt zudem das synchrone Kopieren von Blobs und Dateien und das Angeben des Inhaltstyps von Ziel-Blobs und -dateien. | V4.3.0 | 2014-02-14
| [V3.1.0](http://xdmrelease.blob.core.windows.net/azcopy-3-1-0/MicrosoftAzureStorageTools.msi) | Unterstützt das synchrone Kopieren von Blobs und das Angeben des Inhaltstyps von Ziel-Blobs.| V4.3.0 | 2014-02-14
| [V4.0.0](http://xdmrelease.blob.core.windows.net/azcopy-4-0-0-preview/MicrosoftAzureStorageTools.msi) | Enthält alle Funktionalitäten von V3.0.0. Unterstützt zudem das Kopieren zum oder aus dem Azure-Dateispeicher und das Kopieren von Entitäten zum oder aus dem Azure-Tabellenspeicher.| V4.2.1 | 2014-02-14
| [V3.0.0](http://xdmrelease.blob.core.windows.net/azcopy-3-0-0/MicrosoftAzureStorageTools.msi) | Ändert die AzCopy-Befehlszeilensyntax dahingehend, dass Parameternamen erforderlich sind, und überarbeitet die Befehlszeilenhilfe. Diese Version unterstützt ausschließlich das Kopieren zum und aus dem Azure-Blob-Speicher.| V4.2.1 | 2014-02-14
| V2.5.1 | Optimiert die Leistung bei der Verwendung der Optionen /xo und /xn. Behebt Fehler, die mit Fehlern in Bezug auf Sonderzeichen in Quelldateinamen und Journaldateien in Zusammenhang stehen, nachdem der Benutzer eine falsche Befehlszeilensyntax eingegeben hat.| V4.1.0 | 2014-02-14
| V2.5.0 | Optimiert die Leistung für umfangreiche Kopierszenarien und führt verschiedene wichtige Verbesserungen für Benutzerfunktionen ein.| V4.1.0 | 2014-02-14
| V2.4.1 | Unterstützt die Angabe des Zielordners im Installations-Assistenten.| V4.0.0 | 2014-02-14
| V2.4.0 | Unterstützt das Hochladen und Herunterladen von Dateien für den Azure-Dateispeicher.| V4.0.0 | 2014-02-14
| V2.3.0 | Unterstützt den Lesezugriff für georedundante Speicherkonten.| V3.0.3 | 15\.08.2013
| V2.2.2 | Aktualisierung für die Verwendung der Azure-Speicherclientbibliothek, Version 3.0.3.| V3.0.3 | 15\.08.2013
| V2.2.1 | Behobene Leistungsprobleme beim Kopieren großer Dateimengen innerhalb desselben Speicherkontos.| V2.1.0 |
| V2.2 | Unterstützt das Festlegen des virtuellen Verzeichnistrennzeichens für BLOB-Namen. Unterstützt die Angabe des Journaldateipfades.| V2.1.0 |
| V2.1 | Bietet mehr als 20 Optionen zur Unterstützung von BLOB-Vorgängen zum effizienten Hochladen, Herunterladen und Kopieren.| V2.0.5 |


## Nächste Schritte

Weitere Informationen zu Azure Storage und zu AzCopy finden Sie in den folgenden Ressourcen.

### Azure Storage-Dokumentation:

- [Einführung in Azure Storage](storage-introduction.md)
- [Speichern von Dateien im Blobspeicher](storage-dotnet-how-to-use-blobs.md)
- [Erstellen einer SMB-Dateifreigabe in Azure mit Dateispeicher](storage-dotnet-how-to-use-files.md)

### Azure Storage-Blogbeiträge:
- [DML: Introducing Azure Storage Data Movement Library Preview](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy: Einführung in das synchrone Kopieren und benutzerdefinierte Inhaltstypen](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy: Ab sofort allgemein verfügbar: AzCopy 3.0 sowie die Vorschau auf AzCopy 4.0 mit Tabellen- und Dateiunterstützung](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy: Optimiert für große Kopierszenarien](http://go.microsoft.com/fwlink/?LinkId=507682)
- [Einführung in den Microsoft Azure-Dateidienst](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [AzCopy: Unterstützung des Lesezugriffs auf georedundanten Speicher](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: Übertragen von Daten mit neu startbarem Modus und SAS-Token](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: Verwenden des kontoübergreifenden Kopierblobs](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Hochladen/Herunterladen von Dateien für Windows Azure-Blobs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

<!---HONumber=AcomDC_1203_2015-->