<properties linkid="storage-use-azcopy" urlDisplayName="AzCopy" pageTitle="How to use AzCopy with Microsoft Azure Storage" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Learn how to use the AzCopy utility to upload, download, and copy blob and file content." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use AzCopy with Microsoft Azure Storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="tamram" />

# Erste Schritte mit dem Befehlszeilenprogramm AzCopy

AzCopy ist ein Befehlszeilenprogramm, das zum leistungsstarken Hochladen, Herunterladen und Kopieren von Daten zu und vom Microsoft Azure BLOB- und Dateispeicher konzipiert wurde. Dieser Leitfaden bietet einen Überblick über die Verwendung von AzCopy.

> [WACOM.NOTE] In diesem Leitfaden wird davon ausgegangen, dass Sie AzCopy 2.5 oder höher installiert haben. Beachten Sie, dass AzCopy eine Vorabversion ist, daher können sich die Befehlszeilenoptionen und ihre Funktionen möglicherweise in zukünftigen Versionen ändern.

## Inhaltsverzeichnis

- [Herunterladen und Installieren von AzCopy][Herunterladen und Installieren von AzCopy]
- [Informationen zur Befehlszeilensyntax von AzCopy][Informationen zur Befehlszeilensyntax von AzCopy]
- [Einschränken gleichzeitiger Schreibvorgänge beim Kopieren von Daten][Einschränken gleichzeitiger Schreibvorgänge beim Kopieren von Daten]
- [Kopieren von Azure-BLOBs mit AzCopy][Kopieren von Azure-BLOBs mit AzCopy]
- [Kopieren von Dateien in einer Azure-Dateifreigabe mit AzCopy][Kopieren von Dateien in einer Azure-Dateifreigabe mit AzCopy]
- [AzCopy-Versionen][AzCopy-Versionen]
- [Nächste Schritte][Nächste Schritte]

## <span id="install"></span></a> Herunterladen und Installieren von AzCopy

1. Laden Sie die [neueste Version von AzCopy][neueste Version von AzCopy] herunter.
2. Führen Sie die Installation aus. Standardmäßig wird bei der Installation von AzCopy ein Ordner namens `AzCopy` unter `%ProgramFiles(x86)%\Microsoft SDKs\Azure\` (auf einem Computer mit einer 64-Bit-Version von Windows) oder `%ProgramFiles%\Microsoft SDKs\Azure\` (auf einem Computer mit einer 32-Bit-Version von Windows) erstellt. Sie können den Installationspfad jedoch über den Setup-Assistenten ändern.
3. Bei Bedarf können Sie den Speicherort für die AzCopy-Installation zum Systempfad hinzufügen.

## <span id="syntax"></span></a> Informationen zur Befehlszeilensyntax von AzCopy

Als nächstes öffnen Sie ein Befehlsfenster und navigieren zum Installationsverzeichnis von AzCopy auf Ihrem Computer, in dem sich die ausführbare Datei `AzCopy.exe` befindet. Die grundlegende Syntax für AzCopy-Befehle ist:

	AzCopy <source> <destination> [filepattern] [options]

- Der Parameter `<source>` gibt die Quelldaten für den Kopiervorgang an. Die Quelle kann ein Dateisystemverzeichnis, ein BLOB-Container, ein virtuelles BLOB-Verzeichnis oder eine Speicherdateifreigabe sein.

- Der Parameter `<destination>` gibt das Ziel für den Kopiervorgang an. Das Ziel kann ein Dateisystemverzeichnis, ein BLOB-Container, ein virtuelles BLOB-Verzeichnis oder eine Speicherdateifreigabe sein.

- Das Verhalten des optionalen `filepattern`-Parameters wird über den Speicherort der Quelldaten und über die Angabe der Option für den rekursiven Modus bestimmt. Der rekursive Modus wird über die Option `/S` angegeben.

    Wenn es sich bei der angegebenen Quelle um ein Verzeichnis im Dateisystem handelt, dann sind Standardplatzhalter wirksam und das bereitgestellte Dateimuster wird anhand von Dateien in diesem Verzeichnis verglichen. Wenn die Option `/S` angegeben ist, vergleicht AzCopy das angegebene Muster auch mit allen Dateien in sämtlichen Unterordnern dieses Verzeichnisses.

    Wenn die angegebene Quelle ein BLOB-Container oder ein virtuelles Verzeichnis ist, werden keine Platzhalter angewendet. Wenn die Option `/S` angegeben ist, interpretiert AzCopy die angegebenen Dateimuster als BLOB-Präfix. Wenn die Option `/S` angegeben ist, vergleicht AzCopy die angegebenen Dateimuster mit den genauen BLOB-Namen.

    Wenn die angegebene Quelle eine Azure-Dateifreigabe ist, müssen Sie entweder den genauen Dateinamen angeben, (*z. B.* `abc.txt`), um eine einzelne Datei zu kopieren, oder die Option `/S` angeben, um alle Dateien in der Freigabe rekursiv zu kopieren. Wenn Sie sowohl ein Dateimuster als auch die Option `/S` angeben, führt dies zu einem Fehler.

    Wenn kein Dateimuster angegeben ist, wird als Standarddateimuster `*.*` für ein Dateisystemverzeichnis oder ein leeres Präfix für eine Azure-BLOB- oder Dateispeicherressource angegeben.

    > [WACOM.NOTE] Aufgrund von Leistungsüberlegungen werden mehrere Dateimuster in einem einzelnen Befehl von AzCopy, Version 2.5, nicht länger unterstützt. Sie müssen jetzt mehrere Befehle ausführen, die jeweils einzelne Dateimuster umfassen, um ein Szenario mit mehreren Dateimustern zu behandeln.

- Die verfügbaren Optionen für den Parameter `options` werden in der nachfolgenden Tabelle beschrieben. Sie können auch `AzCopy /?` über die Befehlszeile eingeben, um Hilfe zu den Optionen zu erhalten.

<table>
  <tr>
    <th>Optionsname</th>
    <th>Beschreibung</th>
    <th>Anwendbar auf BLOB-Speicher (J/N)</th>
    <th>Anwendbar auf Dateispeicher (J/N)</th>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;storage-key&gt;
    <td>Gibt den Speicherkontoschlüssel für die Zielressource an.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/DestSAS:&lt;container-SAS&gt;</b></td>
    <td>Gibt eine SAS (Shared Access Signature) für den Zielcontainer an (falls zutreffend). Schließen Sie die SAS in doppelte Anführungszeichen ein, da sie möglicherweise spezielle Befehlszeilenzeichen enthält.<br/>
        Wenn die Zielressource ein BLOB ist, können Sie entweder diese Option gefolgt vom SAS-Token oder die SAS als Teil des URI des Ziel-BLOBs ohne diese Option angeben.<br/>
        Diese Option kann nur auf BLOB-Speicher angewendet werden und ist nur beim Hochladen oder Kopieren von BLOBs innerhalb desselben Speicherkontos gültig.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;storage-key&gt;</b></td>
    <td>Gibt den Speicherkontoschlüssel für die Quellressource an.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;container-SAS&gt;</b></td>
    <td>Gibt eine SAS (Shared Access Signature) für den Quellcontainer an (falls zutreffend). Schließen Sie die SAS in doppelte Anführungszeichen ein, da sie möglicherweise spezielle Befehlszeilenzeichen enthält. 
        <br/>
        Wenn weder Schlüssel noch SAS bereitgestellt werden, wird der Container über einen anonymen Zugriff gelesen. 
        <br/>
        Diese Option kann nur auf BLOB-Speicher angewendet werden.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>Gibt den rekursiven Modus für Kopiervorgänge an. Im rekursiven Modus kopiert AzCopy alle BLOBs oder Dateien, die mit dem angegebenen Dateimuster übereinstimmen, einschließlich der BLOBs oder Dateien in den Unterordnern.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;block | page&gt;</b></td>
    <td>Gibt an, dass das Ziel ein Block-BLOB oder ein Seiten-BLOB ist. Diese Option kann nur angewendet werden, wenn das Ziel ein BLOB ist. Andernfalls wird ein Fehler generiert. Wenn das Ziel ein BLOB ist und diese Option nicht angegeben wurde, nimmt AzCopy standardmäßig an, dass ein Block-BLOB erwünscht ist.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/CheckMd5</b></td>
    <td>Berechnet einen MD5-Hash für heruntergeladene Daten und überprüft, ob der in der Content-MD5-Eigenschaft des BLOBs oder der Datei gespeicherte MD5-Hash mit dem berechneten Hash übereinstimmt. Die MD5-Prüfung ist standardmäßig deaktiviert, daher müssen Sie diese Option angeben, um die MD5-Prüfung beim Herunterladen von Daten auszuführen.
	<br/>
    Beachten Sie, dass Azure Storage nicht dafür garantiert, dass der für den BLOB oder die Datei gespeicherte MD5-Hash auf dem neuesten Stand ist. Der Client ist für die MD5-Aktualisierung zuständig, wenn der BLOB oder die Datei geändert wird.
	<br/>
    AzCopy legt die Content-MD5-Eigenschaft für einen Azure-BLOB oder eine entsprechende Datei immer fest, nachdem diese(r) in den Dienst hochgeladen wurde.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/Snapshot</b></td>
    <td>Gibt an, ob Momentaufnahmen übertragen werden. Diese Option ist nur gültig, wenn es sich bei der Quelle um einen BLOB handelt. 
        <br/>
        Die übertragenen BLOB-Momentaufnahmen werden mit dem folgenden Format umbenannt: [BLOB-Name] (Zeitpunkt der Momentaufnahme)[Erweiterung] 
        <br/>
        Momentaufnahmen werden standardmäßig nicht kopiert.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:[verbose log-file]</b></td>
    <td>Gibt ausführliche Statusmeldungen in eine Protokolldatei aus. Die ausführliche Protokolldatei erhält standardmäßig die Bezeichnung `AzCopyVerbose.log` und wird im Verzeichnis `%LocalAppData%\Microsoft\Azure\AzCopy` gespeichert. Wenn Sie für diese Option einen vorhandenen Dateispeicherort angeben, wird das ausführliche Protokoll an diese Datei angefügt.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/Z:[journal-file-folder]</b></td>
    <td>Gibt einen Journaldateiordner zum Fortsetzen eines Vorgangs an.<br/>
        AzCopy unterstützt die Fortsetzung immer, wenn ein Vorgang unterbrochen wurde.<br/>
        Wenn diese Option nicht oder ohne einen Ordnerpfad angegeben wird, dann erstellt AzCopy die Journaldatei am Standardspeicherort, `%LocalAppData%\Microsoft\Azure\AzCopy`.<br/>
        Sobald ein Befehl für AzCopy ausgeführt wird, prüft es, ob eine Journaldatei im Standardordner oder in einem Ordner vorhanden ist, den Sie über diese Option angegeben haben. Wenn die Journaldatei in keinem dieser Ordner vorhanden ist, behandelt AzCopy diesen Vorgang als neuen Vorgang und generiert somit eine neue Journaldatei.
        <br/>
		Wenn die Journaldatei vorhanden ist, prüft AzCopy, ob die von Ihnen eingegebene Befehlszeile mit der Befehlszeile in der Journaldatei übereinstimmt. Wenn die beiden Befehlszeilen übereinstimmen, setzt AzCopy den unvollständigen Vorgang fort. Wenn sie nicht übereinstimmen, werden Sie aufgefordert, entweder die Journaldatei zu überschreiben, um einen neuen Vorgang zu starten, oder den aktuellen Vorgang abzubrechen. 
        <br/>
        Nachdem der Vorgang erfolgreich abgeschlossen wurde, wird die Journaldatei gelöscht.
		<br/>
		Beachten Sie, dass die Fortsetzung eines Vorgangs aus einer Journaldatei nicht unterstützt wird, die mit einer früheren Version von AzCopy erstellt wurde.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/@:response-file</b></td>
    <td>Gibt eine Datei an, die Parameter enthält. AzCopy verarbeitet die Parameter in der Datei auf die gleiche Weise wie bei der Angabe über die Befehlszeile.<br/> 
		In einer Antwortdatei können Sie entweder mehrere Parameter in einer einzelnen Zeile oder jeden Parameter in einer eigenen Zeile angeben. Beachten Sie, dass ein einzelner Parameter nicht mehrere Zeilen umfassen darf. 
        <br/>
		Antwortdateien können Kommentarzeilen einbeziehen, die mit dem Symbol `#` beginnen.  
        <br/>
        Sie können mehrere Antwortdateien angeben. Beachten Sie jedoch, dass AzCopy geschachtelte Antwortdateien nicht unterstützt.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>Unterdrückt alle Bestätigungsaufforderungen von AzCopy.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>Gibt nur einen Auflistungsvorgang an. Dabei werden keine Daten kopiert.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>Legt für die Uhrzeit der letzten Änderung der heruntergeladenen Datei denselben Zeitpunkt wie für den Quell-BLOB oder die Quelldatei fest.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>Schließt eine neuere Quellressource aus. Die Ressource wird nicht kopiert, wenn die Quelle neuer ist als das Ziel.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>Schließt eine ältere Quellressource aus. Die Ressource wird nicht kopiert, wenn die Quellressource älter ist als das Ziel.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>Lädt ausschließlich Dateien hoch, für die das Archivattribut festgelegt ist.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>Lädt ausschließlich Dateien hoch, für die eines der angegebenen Attribute festgelegt ist.<br/>
        Zu den verfügbaren Attributen zählen:  
        <br/>
        R&nbsp;&nbsp;&nbsp;Schreibgeschützte Dateien 
        <br/>
        A&nbsp;&nbsp;&nbsp;Dateien, die bereit sind für die Archivierung
        <br/>
        S&nbsp;&nbsp;&nbsp;Systemdateien
        <br/>
        H&nbsp;&nbsp;&nbsp;Versteckte Dateien 
        <br/>
        C&nbsp;&nbsp;&nbsp;Komprimierte Dateien
        <br/>
        N&nbsp;&nbsp;&nbsp;Normale Dateien
        <br/>
        E&nbsp;&nbsp;&nbsp;Verschlüsselte Dateien
        <br/>
        T&nbsp;&nbsp;&nbsp;Temporäre Dateien
        <br/>
        O&nbsp;&nbsp;&nbsp;Offlinedateien
        <br/>
        I&nbsp;&nbsp;&nbsp;Nicht indizierte Dateien</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>Schließt Dateien aus, für die eines der angegebenen Attribute festgelegt ist.<br/>
        Zu den verfügbaren Attributen zählen:  
        <br/>
        R&nbsp;&nbsp;&nbsp;Schreibgeschützte Dateien  
        <br/>
        A&nbsp;&nbsp;&nbsp;Dateien, die bereit sind für die Archivierung  
        <br/>
        S&nbsp;&nbsp;&nbsp;Systemdateien  
        <br/>
        H&nbsp;&nbsp;&nbsp;Versteckte Dateien  
        <br/>
        C&nbsp;&nbsp;&nbsp;Komprimierte Dateien  
        <br/>
        N&nbsp;&nbsp;&nbsp;Normale Dateien 
        <br/>
        E&nbsp;&nbsp;&nbsp;Verschlüsselte Dateien  
        <br/>
        T&nbsp;&nbsp;&nbsp;Temporäre Dateien  
        <br/>
        O&nbsp;&nbsp;&nbsp;Offlinedateien  
        <br/>
        I&nbsp;&nbsp;&nbsp;Nicht indizierte Dateien</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;delimiter&gt;</b></td>
    <td>Zeigt das Trennzeichen an, mit dem virtuelle Verzeichnisse in einem BLOB-Namen getrennt werden.<br/>
        AzCopy verwendet „/“ standardmäßig als Trennzeichen. AzCopy unterstützt jedoch die Verwendung beliebiger allgemeiner Zeichen (z. B. @, # oder %) als Trennzeichen. Wenn Sie eines dieser Sonderzeichen auf der Befehlszeile einbeziehen müssen, schließen Sie den Dateinamen in doppelte Anführungszeichen ein. 
        <br/>
        Diese Option kann nur zum Herunterladen von BLOBs angewendet werden.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC</b></td>
    <td>Gibt die Anzahl gleichzeitiger Vorgänge an. 
        <br/>
        Die Standardanzahl entspricht dem achtfachen der Anzahl der Core-Prozessoren, die Sie ausführen. Wenn Ihr Computer also über acht Core-Prozessoren verfügt, startet AzCopy standardmäßig 64 gleichzeitige Vorgänge.<br/>
        Beachten Sie, dass eine große Anzahl gleichzeitiger Vorgänge in einer Umgebung mit geringer Bandbreite die Netzwerkverbindung überlasten kann, wodurch möglicherweise verhindert wird, dass die Vorgänge vollständig abgeschlossen werden. Senken Sie die Anzahl gleichzeitiger Vorgänge auf Basis der tatsächlich verfügbaren Netzwerkbandbreite.</td>
    <td>Y</td>
    <td>Y</td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob</b></td>
    <td>Gibt an, dass die `source`-Ressource ein BLOB ist, der in der lokalen Entwicklungsumgebung verfügbar ist, der im Speicheremulator ausgeführt wird.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestType:Blob</b></td>
    <td>Gibt an, dass die `destination`-Ressource ein BLOB ist, der in der lokalen Entwicklungsumgebung verfügbar ist, der im Speicheremulator ausgeführt wird.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
</table>

<br/>

## <span id="limit-writes"></span></a> Einschränken gleichzeitiger Schreibvorgänge beim Kopieren von Daten

Wenn Sie BLOBs oder Dateien mit AzCopy kopieren, denken Sie daran, dass eine andere Anwendung die Daten möglicherweise modifiziert, während Sie diese kopieren. Stellen Sie nach Möglichkeit sicher, dass die von Ihnen kopierten Daten während des Kopiervorgangs nicht verändert werden. Wenn Sie z. B. eine virtuelle Festplatte (Virtual Hard Disk, VHD) mit einem virtuellen Azure-Computer kopieren, stellen Sie sicher, dass derzeit keine anderen Anwendungen auf diese virtuelle Festplatte schreiben. Alternativ können Sie zunächst eine Momentaufnahme der virtuellen Festplatte (VHD) erstellen und anschließend die Momentaufnahme kopieren.

Wenn Sie andere Anwendungen nicht daran hindern können, während des Kopiervorgangs in BLOBs oder Dateien zu schreiben, beachten Sie, dass die kopierten Ressourcen bei der Beendigung des Auftrags möglicherweise nicht mehr vollständig mit den Quellressourcen übereinstimmen.

## <span id="copy-blobs"></span></a> Kopieren von Azure-BLOBs mit AzCopy

Die nachfolgenden Beispiele veranschaulichen eine Reihe von Szenarien zum Kopieren von BLOBs mit AzCopy.

### Kopieren einzelner BLOBs

**Hochladen einer Datei aus dem Dateisystem in den BLOB-Speicher:**
	
	AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key abc.txt

**Herunterladen eines BLOBs aus dem BLOB-Speicher in das Dateisystem:**

	AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

### Kopieren von BLOBs über serverseitige Kopien

Wenn Sie einen BLOB innerhalb eines Speicherkontos oder zwischen Speicherkonten kopieren, wird ein serverseitiger Kopiervorgang ausgeführt. Weitere Informationen zu serverseitigen Kopiervorgängen finden Sie unter [Einführung zum asynchronen kontoübergreifenden Kopier-BLOB][Einführung zum asynchronen kontoübergreifenden Kopier-BLOB].

**Kopieren von BLOBs innerhalb eines Speicherkontos:**

	AzCopy https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourcekey:key /destkey:key abc.txt 

**Kopieren von BLOBs zwischen Speicherkonten:**

	AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt
 
### Kopieren von BLOBs aus der sekundären Region

Wenn für Ihr Speicherkonto der Lesezugriff auf georedundanten Speicher aktiviert ist, können Sie Daten aus der sekundären Region kopieren.

**Kopieren von BLOBs aus dem sekundären ins primäre Konto:**

	AzCopy https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 https://myaccount2.blob.core.windows.net/mynewcontainer2 /sourcekey:key1 /destkey:key2 abc.txt

**Herunterladen von BLOBs aus dem sekundären Konto in eine Datei des Dateisystems:**

	AzCopy https://myaccount-secondary.blob.core.windows.net/mynewcontainer C:\myfolder /sourcekey:key abc.txt

### Hochladen von Dateien in einen neuen BLOB-Container oder in ein neues virtuelles BLOB-Verzeichnis

**Hochladen von Dateien in einen neuen BLOB-Container**

	AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mynewcontainer /destkey:key abc.txt

Wenn der angegebene Zielcontainer nicht vorhanden ist, wird er von AzCopy erstellt und die Datei anschließend in den Container hochgeladen.

**Hochladen von Dateien in ein neues virtuelles BLOB-Verzeichnis**

	AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer/vd /destkey:key abc.txt

Wenn das angegebene virtuelle Verzeichnis nicht vorhanden ist, lädt AzCopy die Datei hoch, um das virtuelle Verzeichnis in ihren Namen einzubeziehen (*z. B.* `vd/abc.txt` im obigen Beispiel).

### Herunterladen von BLOBs in einen neuen Ordner

	AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

Wenn der Ordner `C:\myfolder` noch nicht vorhanden ist, wird er von AzCopy im Dateisystem erstellt und anschließend `abc.txt` in den neuen Ordner heruntergeladen.

### Rekursives Hochladen von Dateien und Unterordnern eines Verzeichnisses in einen Container

	AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /S

Wenn Sie die Option `/S` angeben, werden die Inhalte des angegebenen Verzeichnisses rekursiv in den BLOB-Speicher kopiert. Das bedeutet, dass ebenso alle Unterordner und die darin enthaltenen Dateien kopiert werden. Nehmen Sie z. B. an, die folgenden Dateien befinden sich im Ordner `C:\myfolder`:

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

	AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key

Wenn Sie die Option `/S` nicht auf der Befehlszeile angeben, wird der Kopiervorgang von AzCopy nicht rekursiv ausgeführt. Es werden nur die Dateien im angegebenen Verzeichnis kopiert. Es werden KEINE Unterordner oder die darin enthaltenen Dateien kopiert. Nehmen Sie z. B. an, die folgenden Dateien befinden sich im Ordner `C:\myfolder`:

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

	AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /S

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

	AzCopy https://myaccount.blob.core.windows.net/mycontainer/vd1/ C:\myfolder /sourcekey:key /S

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

	AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key a* /S

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

	AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key a /S

Nehmen Sie an, die folgenden BLOBs befinden sich im angegebenen Container. Alle BLOBs, die mit dem Präfix `a` beginnen, werden kopiert:

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

	AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt /snapshot

Nach dem Kopiervorgang enthält der Zielcontainer den BLOB und seine Momentaufnahmen. Unter der Annahme, der BLOB im obigen Beispiel besitzt zwei Momentaufnahmen, enthält der Container folgenden BLOB und folgende Momentaufnahmen:

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt


### Verwenden einer Antwortdatei zum Angeben von Befehlszeilenparametern

	AzCopy /@:"C:\myfolder\abc.txt"

Sie können beliebige AzCopy-Befehlszeilenparameter in eine Antwortdatei einfügen. AzCopy verarbeitet die Parameter in der Datei ebenso wie bei der Angabe über die Befehlszeile, wobei mit den Inhalten der Datei ein direkter Austausch erfolgt.

**Angeben mindestens einer Antwortdateien mit einzelner Zeile**

Nehmen wir an, eine Antwortdatei namens `source.txt` gibt einen Quellcontainer an:

	http://myaccount.blob.core.windows.net/mycontainer

Und eine Antwortdatei namens `dest.txt` gibt einen Zielordner im Dateisystem an:

    C:\myfolder

Und eine Antwortdatei namens `options.txt` gibt die Optionen für AzCopy an:

    /S /Y

Verwenden Sie den folgenden Befehl, um AzCopy mit diesen Antwortdateien aufzurufen, die sich jeweils im Verzeichnis `C:\responsefiles` befinden:

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /sourcekey:[sourcekey] /@:"C:\responsefiles\options.txt"   

AzCopy verarbeitet diesen Befehl auf dieselbe Weise wie bei der Angabe der einzelnen Parameter in der Befehlszeile:

	AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

**Angeben einer Antwortdatei mit mehreren Zeilen**

Nehmen wir an, eine Antwortdatei namens `copyoperation.txt` enthält die folgenden Zeilen. Jeder AzCopy-Parameter wird in einer eigenen Zeile angegeben:

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:[sourcekey]
	/S 
	/Y

Verwenden Sie den Befehl, um AzCopy mit diesen Antwortdateien aufzurufen:

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy verarbeitet diesen Befehl auf dieselbe Weise wie bei der Angabe der einzelnen Parameter in der Befehlszeile:

	AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

Beachten Sie, dass jeder AzCopy-Parameter in einer Zeile angegeben werden muss. Wenn Sie den Parameter z. B. auf zwei Zeilen verteilen, schlägt AzCopy fehl, wie hier für den `/sourcekey`-Parameter gezeigt:

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	[sourcekey]
	/S 
	/Y

### Angeben einer Shared Access Signature (SAS)

**Angeben einer SAS für den Quellcontainer mithilfe der Option /sourceSAS**

	AzCopy https://myaccount.blob.core.windows.net/mycontainer1 C:\myfolder /sourceSAS:SAS /S

**Angeben einer SAS für den Quellcontainer im URI des Quellcontainers**

	AzCopy https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken C:\myfolder /S

**Angeben einer SAS für den Zielcontainer mithilfe der Option /destSAS**

	AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer1 /destSAS:SAS abc.txt

**Angeben einer SAS für die Quell- und Zielcontainer**

	AzCopy https://myaccount.blob.core.windows.net/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourceSAS:SAS1 /destSAS:SAS2 abc.txt


### Angeben eines Journaldateiordners

Sobald ein Befehl für AzCopy ausgeführt wird, prüft es, ob eine Journaldatei im Standardordner oder in einem Ordner vorhanden ist, den Sie über diese Option angegeben haben. Wenn die Journaldatei in keinem dieser Ordner vorhanden ist, behandelt AzCopy diesen Vorgang als neuen Vorgang und generiert somit eine neue Journaldatei.

Wenn die Journaldatei vorhanden ist, prüft AzCopy, ob die von Ihnen eingegebene Befehlszeile mit der Befehlszeile in der Journaldatei übereinstimmt. Wenn die beiden Befehlszeilen übereinstimmen, setzt AzCopy den unvollständigen Vorgang fort. Wenn sie nicht übereinstimmen, werden Sie aufgefordert, entweder die Journaldatei zu überschreiben, um einen neuen Vorgang zu starten, oder den aktuellen Vorgang abzubrechen.

**Verwenden des Standardspeicherorts für die Journaldatei**

	AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z

Wenn Sie die Option `/Z` auslassen oder die Option `/Z` ohne den Ordnerpfad angeben (wie oben gezeigt), erstellt AzCopy die Journaldatei am Standardspeicherort, `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Wenn die Journaldatei bereits vorhanden ist, setzt AzCopy den Vorgang auf Basis der Journaldatei fort.

**Angeben eines benutzerdefinierten Speicherorts für die Journaldatei**

	AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z:C:\journalfolder\

In diesem Beispiel wird die Journaldatei erstellt, sofern diese nicht bereits vorhanden ist. Wenn sie vorhanden ist, setzt AzCopy den Vorgang auf Basis der Journaldatei fort.

**Fortsetzen eines AzCopy-Vorgangs**

	AzCopy /Z:C:\journalfolder\

In diesem Beispiel wird der letzte Vorgang fortgesetzt, der möglicherweise nicht abgeschlossen werden konnte.


### Generieren einer Protokolldatei

**Schreiben in die ausführliche Protokolldatei am Standardspeicherort**

	AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V

Wenn Sie die Option `/V` ohne einen Dateipfad zum ausführlichen Protokoll angeben, erstellt AzCopy die Protokolldatei am Standardspeicherort, `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

**Schreiben in die ausführliche Protokolldatei an einem benutzerdefinierten Speicherort**

	AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V:C:\myfolder\azcopy1.log

Wenn Sie nach der Option `/V` einen relativen Pfad angeben, z. B. `/V:test/azcopy1.log`, dann wird das ausführliche Protokoll im aktuellen Arbeitsverzeichnis in einem Unterordner namens `test` erstellt.

### Festlegen desselben Zeitpunkts für die Uhrzeit der letzten Änderung der heruntergeladenen Dateien wie für die Quell-BLOBs

	AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT


### Ausschließen von BLOBs aus dem Kopiervorgang auf Basis der Uhrzeit der letzten Änderung

Geben Sie die Option `/MT` an, um die Uhrzeit der letzten Änderung des Quell-BLOBs mit der Zieldatei zu vergleichen.

**Ausschließen von BLOBs, die neuer sind als die Zieldatei**

	AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XN

**Ausschließen von BLOBs, die älter sind als die Zieldatei**

	AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XO

### Angeben der Anzahl der zu startenden gleichzeitigen Vorgängen

Die Option `/NC` gibt die Anzahl der gleichzeitigen Kopiervorgänge an. Standardmäßig startet AzCopy gleichzeitige Vorgänge mit dem Achtfachen der Anzahl der verfügbaren Core-Prozessoren. Wenn Sie AzCopy in einem Netzwerk mit geringer Bandbreite ausführen, können Sie eine niedrigere Anzahl für diese Option angeben, um Fehler durch gleichzeitig verwendete Ressourcen zu vermeiden.


### Ausführen von AzCopy für BLOB-Ressourcen im Speicheremulator

	AzCopy https://127.0.0.1:10004/myaccount/myfileshare/ C:\myfolder /SourceKey:key /SourceType:Blob /S


## <span id="copy-files"></span></a> Kopieren von Dateien in einer Azure-Dateifreigabe mit AzCopy

Die nachfolgenden Beispiele veranschaulichen eine Reihe von Szenarien zum Kopieren von Azure-Dateien mit AzCopy.

### Herunterladen von Dateien aus einer Azure-Dateifreigabe in das Dateisystem

	AzCopy https://myaccount.file.core.windows.net/myfileshare/myfolder1/ C:\myfolder /SourceKey:key abc.txt

Wenn die angegebene Quelle eine Azure-Dateifreigabe ist, müssen Sie entweder den genauen Dateinamen angeben, (*z. B.* `abc.txt`), um eine einzelne Datei zu kopieren, oder die Option `/S` angeben, um alle Dateien in der Freigabe rekursiv zu kopieren. Wenn Sie sowohl ein Dateimuster als auch die Option `/S` angeben, führt dies zu einem Fehler.

### Rekursives Herunterladen von Dateien und Ordnern einer Azure-Dateifreigabe in das Dateisystem

	AzCopy https://myaccount.file.core.windows.net/myfileshare/ C:\myfolder /SourceKey:key /S

Beachten Sie, dass leere Ordner nicht kopiert werden.

### Rekursives Hochladen von Dateien und Ordnern aus dem Dateisystem in eine Azure-Dateifreigabe

	AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Beachten Sie, dass leere Ordner nicht kopiert werden.

### Rekursives Hochladen von Dateien, die mit dem angegebenen Dateimuster übereinstimmen, in eine Azure-Dateifreigabe

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key ab* /S

## <span id="versions"></span></a> AzCopy-Versionen

| Version    | Neuerungen                                                                                                                                               |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **V2.5.0** | **Aktuelle Version. Optimiert die Leistung für umfangreiche Kopierszenarien und führt verschiedene wichtige Verbesserungen für Benutzerfunktionen ein.** |
| V2.4.1     | Unterstützt die Angabe des Zielordners im Installations-Assistenten.                                                                                     |
| V2.4.0     | Unterstützt das Hochladen und Herunterladen von Dateien für den Azure-Dateispeicher.                                                                     |
| V2.3.0     | Unterstützt den Lesezugriff für georedundante Speicherkonten.                                                                                            |
| V2.2.2     | Aktualisierung für die Verwendung der Azure-Speicherclientbibliothek, Version 3.0.3.                                                                     |
| V2.2.1     | Behobene Leistungsprobleme beim Kopieren großer Dateimengen innerhalb desselben Speicherkontos.                                                          |
| V2.2       | Unterstützt das Festlegen des virtuellen Verzeichnistrennzeichens für BLOB-Namen. Unterstützt die Angabe des Journaldateipfades.                         |
| V2.1       | Bietet mehr als 20 Optionen zur Unterstützung von BLOB-Vorgängen zum effizienten Hochladen, Herunterladen und Kopieren.                                  |

## <span id="next-steps"></span></a>Nächste Schritte

Weitere Informationen zu Azure Storage und zu AzCopy finden Sie in den folgenden Ressourcen.

### Azure Storage-Dokumentation:

- [Einführung in Azure Storage][Einführung in Azure Storage]
- [Speichern von Dateien in BLOB-Speicher][Speichern von Dateien in BLOB-Speicher]
- [Erstellen einer SMB-Dateifreigabe in Azure mit Dateispeicher][Erstellen einer SMB-Dateifreigabe in Azure mit Dateispeicher]

### Azure Storage-Blogbeiträge:

- [Einführung in den Microsoft Azure-Dateidienst][Einführung in den Microsoft Azure-Dateidienst]
- [AzCopy 2.5: Optimiert für große Kopierszenarien][AzCopy 2.5: Optimiert für große Kopierszenarien]
- [AzCopy: Unterstützung des Lesezugriffs auf georedundanten Speicher][AzCopy: Unterstützung des Lesezugriffs auf georedundanten Speicher]
- [AzCopy: Übertragen von Daten mit neu startbarem Modus und SAS-Token][AzCopy: Übertragen von Daten mit neu startbarem Modus und SAS-Token]
- [AzCopy: Verwenden des kontoübergreifenden Kopier-BLOBs][AzCopy: Verwenden des kontoübergreifenden Kopier-BLOBs]
- [AzCopy: Hochladen/Herunterladen von Dateien für Windows Azure-Blobs][AzCopy: Hochladen/Herunterladen von Dateien für Windows Azure-Blobs]

[Herunterladen und Installieren von AzCopy]: #install
[Informationen zur Befehlszeilensyntax von AzCopy]: #syntax
[Einschränken gleichzeitiger Schreibvorgänge beim Kopieren von Daten]: #limit-writes
[Kopieren von Azure-BLOBs mit AzCopy]: #copy-blobs
[Kopieren von Dateien in einer Azure-Dateifreigabe mit AzCopy]: #copy-files
[AzCopy-Versionen]: #versions
[Nächste Schritte]: #next-steps
[neueste Version von AzCopy]: http://aka.ms/downloadazcopy
[Einführung zum asynchronen kontoübergreifenden Kopier-BLOB]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx
[Einführung in Azure Storage]: http://azure.microsoft.com/de-de/documentation/articles/storage-introduction/
[Speichern von Dateien in BLOB-Speicher]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-blobs/
[Erstellen einer SMB-Dateifreigabe in Azure mit Dateispeicher]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-files/
[Einführung in den Microsoft Azure-Dateidienst]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
[AzCopy 2.5: Optimiert für große Kopierszenarien]: http://go.microsoft.com/fwlink/?LinkId=507682
[AzCopy: Unterstützung des Lesezugriffs auf georedundanten Speicher]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx
[AzCopy: Übertragen von Daten mit neu startbarem Modus und SAS-Token]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx
[AzCopy: Verwenden des kontoübergreifenden Kopier-BLOBs]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx
[AzCopy: Hochladen/Herunterladen von Dateien für Windows Azure-Blobs]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
