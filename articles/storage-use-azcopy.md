<properties urlDisplayName="AzCopy" pageTitle="Gewusst wie: Verwenden von AzCopy mit Microsoft Azure Storage" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Erfahren Sie, wie Sie das Hilfsprogramm AzCopy zum Hochladen, Herunterladen und Kopieren von Blob- und Dateiinhalten verwenden." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="Gewusst wie: Verwenden von AzCopy mit Microsoft Azure Storage" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/17/2014" ms.author="tamram" />

# Erste Schritte mit dem Befehlszeilenprogramm AzCopy

AzCopy ist ein Befehlszeilenprogramm, das zum leistungsstarken Hochladen, Herunterladen und Kopieren von Daten zu und vom Microsoft Azure Blob-, Datei- und Tabellenspeicher konzipiert wurde. Dieser Leitfaden bietet einen Überblick über die Verwendung von AzCopy.

> [WACOM.NOTE] In diesem Leitfaden wird davon ausgegangen, dass Sie AzCopy 3.0.0 oder höher installiert haben. AzCopy 3.x ist nun allgemein verfügbar.
> Diese Leitfaden deckt auch die Verwendung von AzCopy 4.0.0 ab. Hierbei handelt es sich um eine Vorschauversion von AzCopy. In diesem Leitfaden werden Funktionen, die nur in der Vorschauversion verfügbar sind, mit *Vorschau* gekennzeichnet.
> Beachten Sie, dass sich die Befehlszeilenoptionen und Funktionalitäten für AzCopy 4.x in künftigen Veröffentlichungen ändern können.

## Inhaltsverzeichnis

-   [Herunterladen und Installieren von AzCopy][Herunterladen und Installieren von AzCopy]
-   [Informationen zur Befehlszeilensyntax von AzCopy][Informationen zur Befehlszeilensyntax von AzCopy]
-   [Einschränken gleichzeitiger Schreibvorgänge beim Kopieren von Daten][Einschränken gleichzeitiger Schreibvorgänge beim Kopieren von Daten]
-   [Kopieren von Azure-BLOBs mit AzCopy][Kopieren von Azure-BLOBs mit AzCopy]
-   [Kopieren von Dateien in einer Azure-Dateifreigabe mit AzCopy (nur Vorschauversion)][Kopieren von Dateien in einer Azure-Dateifreigabe mit AzCopy (nur Vorschauversion)]
-   [Kopieren von Azure-Tabellenentitäten mit AzCopy (nur Vorschauversion)][Kopieren von Azure-Tabellenentitäten mit AzCopy (nur Vorschauversion)]
-   [AzCopy-Versionen][AzCopy-Versionen]
-   [Nächste Schritte][Nächste Schritte]

## <span id="install"></span></a> Herunterladen und Installieren von AzCopy

1.  Laden Sie die [neueste Version von AzCopy][neueste Version von AzCopy] oder die [neueste Vorschauversion][neueste Vorschauversion] herunter.
2.  Führen Sie die Installation aus. Standardmäßig wird bei der Installation von AzCopy ein Ordner namens `AzCopy` unter `%ProgramFiles(x86)%\Microsoft SDKs\Azure\` (auf einem Computer mit einer 64-Bit-Version von Windows) oder `%ProgramFiles%\Microsoft SDKs\Azure\` (auf einem Computer mit einer 32-Bit-Version von Windows) erstellt. Sie können den Installationspfad jedoch über den Setup-Assistenten ändern.
3.  Bei Bedarf können Sie den Speicherort für die AzCopy-Installation zum Systempfad hinzufügen.

## <span id="syntax"></span></a> Informationen zur Befehlszeilensyntax von AzCopy

Als nächstes öffnen Sie ein Befehlsfenster und navigieren zum Installationsverzeichnis von AzCopy auf Ihrem Computer, in dem sich die ausführbare Datei `AzCopy.exe` befindet. Die grundlegende Syntax für AzCopy-Befehle ist:

    AzCopy /Source:<source> /Dest:<destination> /Pattern:<filepattern> [Options]

> [WACOM.NOTE] Ab AzCopy Version 3.0.0 erfordert die AzCopy-Befehlszeilensyntax, dass jeder Parameter zum Einbeziehen des Parameternamens angegeben werden muss, *beispielsweise* `/ParameterName:ParameterValue`.

Parameter für AzCopy werden in der folgenden Tabelle beschrieben. Sie können auch einen der folgenden Befehle von der Befehlszeile für die Unterstützung in Bezug auf die Verwendung von AzCopy eingeben:

-   Für eine detaillierte Befehlszeilenhilfe für AzCopy: `AzCopy /?`
-   Für eine detaillierte Hilfe mit AzCopy-Parametern: `AzCopy /?:SourceKey`
-   Für Befehlszeilenbeispiele: `AzCopy /?:Samples`

| Optionsname                                                             | Beschreibung                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Anwendbar auf BLOB-Speicher (J/N) | Anwendbar auf Dateispeicher (J/N) (nur Vorschauversion) | Anwendbar auf Tabellenspeicher (J/N) (nur Vorschauversion) |
|-------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------|---------------------------------------------------------|------------------------------------------------------------|
| **/Source:\<source\>**                                                  | Gibt die Quelldaten an, aus denen kopiert werden soll. Die Quelle kann ein Dateisystemverzeichnis, ein Blobcontainer, ein virtuelles Blobverzeichnis, eine Speicherdateifreigabe, ein Speicherdateiverzeichnis oder eine Azure-Tabelle sein.                                                                                                                                                                                                                                 | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/Dest:\<destination\>**                                               | Geben das Ziel für das Kopieren an. Das Ziel kann ein Dateisystemverzeichnis, ein Blobcontainer, ein virtuelles Blobverzeichnis, eine Speicherdateifreigabe, ein Speicherdateiverzeichnis oder eine Azure-Tabelle sein.                                                                                                                                                                                                                                                      | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/Pattern:\<file-pattern\>**                                           | Gibt ein Dateimuster an, das angibt, welche Dateien kopiert werden sollen. Das Verhalten des „/Pattern“-Parameters wird über den Speicherort der Quelldaten und über die Angabe der Option für den rekursiven Modus bestimmt. Der rekursive Modus wird über die Option „/S“ angegeben.                                                                                                                                                                                       
                                                                            Wenn es sich bei der angegebenen Quelle um ein Verzeichnis im Dateisystem handelt, dann sind Standardplatzhalter wirksam und das bereitgestellte Dateimuster wird anhand von Dateien in diesem Verzeichnis verglichen. Wenn die Option „/S“ angegeben ist, vergleicht AzCopy das angegebene Muster auch mit allen Dateien in sämtlichen Unterordnern dieses Verzeichnisses.                                                                                                  
                                                                            Wenn die angegebene Quelle ein BLOB-Container oder ein virtuelles Verzeichnis ist, werden keine Platzhalter angewendet. Wenn die Option „/S“ angegeben ist, interpretiert AzCopy die angegebenen Dateimuster als Blobpräfix. Wenn die Option „/S“ angegeben ist, vergleicht AzCopy die angegebenen Dateimuster mit den genauen Blobnamen.                                                                                                                                    
                                                                            Wenn es sich bei der angegebenen Quelle um eine Azure-Dateifreigabe handelt, müssen Sie entweder den genauen Dateinamen (z. B. „abc.txt“) angeben, um eine einzelne Datei zu kopieren, oder die Option „/S“ angeben, um alle Dateien in der Freigabe rekursiv zu kopieren. Wenn Sie sowohl ein Dateimuster als auch die Option „/S“ angeben, führt dies zu einem Fehler.                                                                                                     
                                                                            Das standardmäßige Dateimuster lautet \*.\* für ein Dateisystemspeicherort oder ein leeres Präfix für ein Azure Storage-Speicherort, wenn kein Dateimuster angegeben ist. Das Angeben mehrerer Dateimuster wird nicht unterstützt.                                                                                                                                                                                                                                           | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | N                                                          |
| **/DestKey:\<Speicherschlüssel\>**                                      | Gibt den Speicherkontoschlüssel für die Zielressource an.                                                                                                                                                                                                                                                                                                                                                                                                                    | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/DestSAS:\<sas-token\>**                                              | Gibt eine SAS (Shared Access Signature) für den Zielcontainer an (falls zutreffend). Schließen Sie die SAS in doppelte Anführungszeichen ein, da sie möglicherweise spezielle Befehlszeilenzeichen enthält.                                                                                                                                                                                                                                                                  
                                                                            Wenn die Zielressource ein Blobcontainer oder eine Tabelle ist, können Sie entweder diese Option gefolgt vom SAS-Token oder die SAS als Teil des URI des Zielblobs ohne diese Option angeben.                                                                                                                                                                                                                                                                                
                                                                            Wenn es sich bei der Quelle und beim Ziel um Blobs handelt, muss sich das Zielblob im selben Speicherkonto wie das Quellblob befinden.                                                                                                                                                                                                                                                                                                                                       | Y                                 | N                                                       | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/SourceKey:\<Speicherschlüssel\>**                                    | Gibt den Speicherkontoschlüssel für die Quellressource an.                                                                                                                                                                                                                                                                                                                                                                                                                   | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/SourceSAS:\<sas-token\>**                                            | Gibt eine SAS (Shared Access Signature) für den Quellcontainer an (falls zutreffend). Schließen Sie die SAS in doppelte Anführungszeichen ein, da sie möglicherweise spezielle Befehlszeilenzeichen enthält.                                                                                                                                                                                                                                                                 
                                                                            Wenn die Quellressource ein Blobcontainer ist und weder ein Schlüssel noch eine SAS angegeben wird, wird der Blobcontainer über den anonymen Zugriff gelesen.                                                                                                                                                                                                                                                                                                                
                                                                            Wenn die Quelle eine Tabelle ist, muss ein Schlüssel oder eine SAS angegeben werden.                                                                                                                                                                                                                                                                                                                                                                                         | Y                                 | N                                                       | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/S**                                                                  | Gibt den rekursiven Modus für Kopiervorgänge an. Im rekursiven Modus kopiert AzCopy alle BLOBs oder Dateien, die mit dem angegebenen Dateimuster übereinstimmen, einschließlich der BLOBs oder Dateien in den Unterordnern.                                                                                                                                                                                                                                                  | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | N                                                          |
| **/BlobType:\<Block | Seite\>**                                         | Gibt an, ob es sich beim Zielblob um einen Block- oder um einen Seitenblob handelt. Diese Option kann nur beim Hochladen des Blobs angewendet werden. Ansonsten wird ein Fehler generiert. Wenn das Ziel ein Blob ist und diese Option nicht angegeben wurde, erstellt AzCopy standardmäßig einen Blockblob.                                                                                                                                                                 | Y                                 | N                                                       | N                                                          |
| **/CheckMD5**                                                           | Berechnet einen MD5-Hash für heruntergeladene Daten und überprüft, ob der in der Content-MD5-Eigenschaft des BLOBs oder der Datei gespeicherte MD5-Hash mit dem berechneten Hash übereinstimmt. Die MD5-Prüfung ist standardmäßig deaktiviert, daher müssen Sie diese Option angeben, um die MD5-Prüfung beim Herunterladen von Daten auszuführen.                                                                                                                           
                                                                            Beachten Sie, dass Azure Storage nicht dafür garantiert, dass der für den BLOB oder die Datei gespeicherte MD5-Hash auf dem neuesten Stand ist. Der Client ist für die MD5-Aktualisierung zuständig, wenn der BLOB oder die Datei geändert wird.                                                                                                                                                                                                                             
                                                                            AzCopy legt die Content-MD5-Eigenschaft für einen Azure-BLOB oder eine entsprechende Datei immer fest, nachdem diese(r) in den Dienst hochgeladen wurde.                                                                                                                                                                                                                                                                                                                     | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | N                                                          |
| **/Snapshot**                                                           | Gibt an, ob Momentaufnahmen übertragen werden. Diese Option ist nur gültig, wenn es sich bei der Quelle um einen BLOB handelt.                                                                                                                                                                                                                                                                                                                                               
                                                                            Die übertragenen BLOB-Momentaufnahmen werden mit dem folgenden Format umbenannt: [BLOB-Name] (Zeitpunkt der Momentaufnahme)[Erweiterung].                                                                                                                                                                                                                                                                                                                                    
                                                                            Momentaufnahmen werden standardmäßig nicht kopiert.                                                                                                                                                                                                                                                                                                                                                                                                                          | Y                                 | N                                                       | N                                                          |
| **/V:[ausführliche Protokolldatei]**                                    | Gibt ausführliche Statusmeldungen in eine Protokolldatei aus. Die ausführliche Protokolldatei erhält standardmäßig die Bezeichnung `AzCopyVerbose.log` und wird im Verzeichnis `%LocalAppData%\Microsoft\Azure\AzCopy` gespeichert. Wenn Sie für diese Option einen vorhandenen Dateispeicherort angeben, wird das ausführliche Protokoll an diese Datei angefügt.                                                                                                           | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/Z:[Journaldatei-Ordner]**                                            | Gibt einen Journaldateiordner zum Fortsetzen eines Vorgangs an.                                                                                                                                                                                                                                                                                                                                                                                                              
                                                                            AzCopy unterstützt die Fortsetzung immer, wenn ein Vorgang unterbrochen wurde.                                                                                                                                                                                                                                                                                                                                                                                               
                                                                            Wenn diese Option nicht oder ohne einen Ordnerpfad angegeben wird, dann erstellt AzCopy die Journaldatei am Standardspeicherort, `%LocalAppData%\Microsoft\Azure\AzCopy`.                                                                                                                                                                                                                                                                                                    
                                                                            Sobald ein Befehl für AzCopy ausgeführt wird, prüft es, ob eine Journaldatei im Standardordner oder in einem Ordner vorhanden ist, den Sie über diese Option angegeben haben. Wenn die Journaldatei in keinem dieser Ordner vorhanden ist, behandelt AzCopy diesen Vorgang als neuen Vorgang und generiert somit eine neue Journaldatei.                                                                                                                                     
                                                                            Wenn die Journaldatei vorhanden ist, prüft AzCopy, ob die von Ihnen eingegebene Befehlszeile mit der Befehlszeile in der Journaldatei übereinstimmt. Wenn die beiden Befehlszeilen übereinstimmen, setzt AzCopy den unvollständigen Vorgang fort. Wenn sie nicht übereinstimmen, werden Sie aufgefordert, entweder die Journaldatei zu überschreiben, um einen neuen Vorgang zu starten, oder den aktuellen Vorgang abzubrechen.                                             
                                                                            Nachdem der Vorgang erfolgreich abgeschlossen wurde, wird die Journaldatei gelöscht.                                                                                                                                                                                                                                                                                                                                                                                         
                                                                            Beachten Sie, dass die Fortsetzung eines Vorgangs aus einer Journaldatei nicht unterstützt wird, die mit einer früheren Version von AzCopy erstellt wurde.                                                                                                                                                                                                                                                                                                                   | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/@:parameter-file**                                                   | Gibt eine Datei an, die Parameter enthält. AzCopy verarbeitet die Parameter in der Datei auf die gleiche Weise wie bei der Angabe über die Befehlszeile.                                                                                                                                                                                                                                                                                                                     
                                                                            In einer Antwortdatei können Sie entweder mehrere Parameter in einer einzelnen Zeile oder jeden Parameter in einer eigenen Zeile angeben. Beachten Sie, dass ein einzelner Parameter nicht mehrere Zeilen umfassen darf.                                                                                                                                                                                                                                                     
                                                                            Antwortdateien können Kommentarzeilen einbeziehen, die mit dem Symbol `#` beginnen.                                                                                                                                                                                                                                                                                                                                                                                          
                                                                            Sie können mehrere Antwortdateien angeben. Beachten Sie jedoch, dass AzCopy geschachtelte Antwortdateien nicht unterstützt.                                                                                                                                                                                                                                                                                                                                                  | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/Y**                                                                  | Unterdrückt alle Bestätigungsaufforderungen von AzCopy.                                                                                                                                                                                                                                                                                                                                                                                                                      | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/L**                                                                  | Gibt nur einen Auflistungsvorgang an. Dabei werden keine Daten kopiert.                                                                                                                                                                                                                                                                                                                                                                                                      | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | N                                                          |
| **/MT**                                                                 | Legt für die Uhrzeit der letzten Änderung der heruntergeladenen Datei denselben Zeitpunkt wie für den Quell-BLOB oder die Quelldatei fest.                                                                                                                                                                                                                                                                                                                                   | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | N                                                          |
| **/XN**                                                                 | Schließt eine neuere Quellressource aus. Die Ressource wird nicht kopiert, wenn die Quelle neuer ist als das Ziel.                                                                                                                                                                                                                                                                                                                                                           | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | N                                                          |
| **/XO**                                                                 | Schließt eine ältere Quellressource aus. Die Ressource wird nicht kopiert, wenn die Quellressource älter ist als das Ziel.                                                                                                                                                                                                                                                                                                                                                   | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | N                                                          |
| **/A**                                                                  | Lädt ausschließlich Dateien hoch, für die das Archivattribut festgelegt ist.                                                                                                                                                                                                                                                                                                                                                                                                 | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | N                                                          |
| **/IA:[RASHCNETOI]**                                                    | Lädt ausschließlich Dateien hoch, für die eines der angegebenen Attribute festgelegt ist.                                                                                                                                                                                                                                                                                                                                                                                    
                                                                            Zu den verfügbaren Attributen zählen:                                                                                                                                                                                                                                                                                                                                                                                                                                        
                                                                            R   Schreibgeschützte Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                
                                                                            A   Dateien, die bereit sind für die Archivierung                                                                                                                                                                                                                                                                                                                                                                                                                            
                                                                            S   Systemdateien                                                                                                                                                                                                                                                                                                                                                                                                                                                            
                                                                            H   Versteckte Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                       
                                                                            C   Komprimierte Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                     
                                                                            N   Normale Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                            E   Verschlüsselte Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                   
                                                                            T   Temporäre Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                        
                                                                            O   Offlinedateien                                                                                                                                                                                                                                                                                                                                                                                                                                                           
                                                                            I   Nicht indizierte Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | N                                                          |
| **/XA:[RASHCNETOI]**                                                    | Schließt Dateien aus, für die eines der angegebenen Attribute festgelegt ist.                                                                                                                                                                                                                                                                                                                                                                                                
                                                                            Zu den verfügbaren Attributen zählen:                                                                                                                                                                                                                                                                                                                                                                                                                                        
                                                                            R   Schreibgeschützte Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                
                                                                            A   Dateien, die bereit sind für die Archivierung                                                                                                                                                                                                                                                                                                                                                                                                                            
                                                                            S   Systemdateien                                                                                                                                                                                                                                                                                                                                                                                                                                                            
                                                                            H   Versteckte Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                       
                                                                            C   Komprimierte Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                     
                                                                            N   Normale Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                          
                                                                            E   Verschlüsselte Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                   
                                                                            T   Temporäre Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                        
                                                                            O   Offlinedateien                                                                                                                                                                                                                                                                                                                                                                                                                                                           
                                                                            I   Nicht indizierte Dateien                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | N                                                          |
| **/Delimiter:\<Trennzeichen\>**                                         | Zeigt das Trennzeichen an, mit dem virtuelle Verzeichnisse in einem BLOB-Namen getrennt werden.                                                                                                                                                                                                                                                                                                                                                                              
                                                                            AzCopy verwendet „/“ standardmäßig als Trennzeichen. AzCopy unterstützt jedoch die Verwendung beliebiger allgemeiner Zeichen (z. B. @, \# oder %) als Trennzeichen. Wenn Sie eines dieser Sonderzeichen auf der Befehlszeile einbeziehen müssen, schließen Sie den Dateinamen in doppelte Anführungszeichen ein.                                                                                                                                                             
                                                                            Diese Option kann nur zum Herunterladen von BLOBs angewendet werden.                                                                                                                                                                                                                                                                                                                                                                                                         | Y                                 | N                                                       | N                                                          |
| **/NC:\<number-of-concurrents\>**                                       | Gibt die Anzahl gleichzeitiger Vorgänge an.                                                                                                                                                                                                                                                                                                                                                                                                                                  
                                                                            AzCopy startet standardmäßig eine bestimmte Anzahl gleichzeitiger Vorgänge zum Erhöhen des Datenübertragungsdurchsatzes. Beachten Sie, dass eine große Anzahl gleichzeitiger Vorgänge in einer Umgebung mit geringer Bandbreite die Netzwerkverbindung überlasten kann, wodurch möglicherweise verhindert wird, dass die Vorgänge vollständig abgeschlossen werden. Senken Sie die Anzahl gleichzeitiger Vorgänge auf Basis der tatsächlich verfügbaren Netzwerkbandbreite.  
                                                                            Maximal können 512 gleichzeitige Vorgänge ausgeführt werden.                                                                                                                                                                                                                                                                                                                                                                                                                 | Y                                 | J                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               (nur Vorschau)                                          | N                                                          |
| **/SourceType:Blob|Table**                                              | Gibt an, dass die `source`-Ressource ein BLOB ist, der in der lokalen Entwicklungsumgebung verfügbar ist, der im Speicheremulator ausgeführt wird.                                                                                                                                                                                                                                                                                                                           | Y                                 | N                                                       | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/DestType:Blob|Table**                                                | Gibt an, dass die `destination`-Ressource ein BLOB ist, der in der lokalen Entwicklungsumgebung verfügbar ist, der im Speicheremulator ausgeführt wird.                                                                                                                                                                                                                                                                                                                      | Y                                 | N                                                       | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/PKRS:\<"key1\#key2\#key3\#..."\>**                                   | Teilt den Partitionsschlüsselbereich auf, um parallel das Exportieren von Tabellendaten zu ermöglichen, wodurch die Geschwindigkeit des Exportvorgangs erhöht wird.                                                                                                                                                                                                                                                                                                          
                                                                            Wenn diese Option nicht angegeben ist, verwendet AzCopy einen einzelnen Thread zum Exportieren von Tabellenentitäten. Wenn der Benutzer beispielsweise **/PKRS:"aa\#bb"** angibt, startet AzCopy drei gleichzeitige Vorgänge.                                                                                                                                                                                                                                                
                                                                            Jeder Vorgang exportiert einen der drei Partitionsschlüsselbereiche, wie dies im Folgenden gezeigt wird.                                                                                                                                                                                                                                                                                                                                                                     
                                                                               [\<erster Partitionsschlüssel\>, aa)                                                                                                                                                                                                                                                                                                                                                                                                                                      
                                                                               [aa, bb)                                                                                                                                                                                                                                                                                                                                                                                                                                                                  
                                                                               [bb, \<letzter Partitionsschlüssel\>]                                                                                                                                                                                                                                                                                                                                                                                                                                     | N                                 | N                                                       | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/SplitSize:**<file-size>**\<file-size\>**                             | Gibt die Teilungsgröße der exportierten Datei in MB an.                                                                                                                                                                                                                                                                                                                                                                                                                      
                                                                            Wenn diese Option nicht angegeben ist, exportiert AzCopy Tabellendaten in eine einzelne Datei.                                                                                                                                                                                                                                                                                                                                                                               
                                                                            Wenn die Tabellendaten in ein Blob exportiert wird, und die exportierte Dateigröße die 200-GB-Begrenzung für die Blobgröße erreicht, teilt AzCopy die exportierte Datei auf, selbst wenn diese Option nicht angegeben ist.                                                                                                                                                                                                                                                   | N                                 | N                                                       | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/EntityOperation:\<InsertOrSkip | InsertOrMerge | InsertOrReplace\>** | Gibt das Tabellendaten-Importverhalten an.                                                                                                                                                                                                                                                                                                                                                                                                                                   
                                                                            InsertOrSkip – Überspringt eine vorhandene Entität oder fügt eine neue Entität ein, wenn sie in der Tabelle nicht vorhanden ist.                                                                                                                                                                                                                                                                                                                                             
                                                                            InsertOrMerge – Führt eine vorhandene Entität zusammen oder fügt eine neue Entität ein, wenn sie in der Tabelle nicht vorhanden ist.                                                                                                                                                                                                                                                                                                                                         
                                                                            InsertOrReplace – Ersetzt eine vorhandene Entität oder fügt eine neue Entität ein, wenn sie in der Tabelle nicht vorhanden ist.                                                                                                                                                                                                                                                                                                                                              | N                                 | N                                                       | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |
| **/Manifest:\<manifest-file\>**                                         | Gibt die Manifestdatei für den Importvorgang an.                                                                                                                                                                                                                                                                                                                                                                                                                             
                                                                            Die Manifestdatei wird während des Exportvorgangs generiert.                                                                                                                                                                                                                                                                                                                                                                                                                 | N                                 | N                                                       | J                                                          
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (nur Vorschau)                                             |

## <span id="limit-writes"></span></a> Einschränken gleichzeitiger Schreibvorgänge beim Kopieren von Daten

Wenn Sie BLOBs oder Dateien mit AzCopy kopieren, denken Sie daran, dass eine andere Anwendung die Daten möglicherweise modifiziert, während Sie diese kopieren. Stellen Sie nach Möglichkeit sicher, dass die von Ihnen kopierten Daten während des Kopiervorgangs nicht verändert werden. Wenn Sie z. B. eine virtuelle Festplatte (Virtual Hard Disk, VHD) mit einem virtuellen Azure-Computer kopieren, stellen Sie sicher, dass derzeit keine anderen Anwendungen auf diese virtuelle Festplatte schreiben. Alternativ können Sie zunächst eine Momentaufnahme der virtuellen Festplatte (VHD) erstellen und anschließend die Momentaufnahme kopieren.

Wenn Sie andere Anwendungen nicht daran hindern können, während des Kopiervorgangs in BLOBs oder Dateien zu schreiben, beachten Sie, dass die kopierten Ressourcen bei der Beendigung des Auftrags möglicherweise nicht mehr vollständig mit den Quellressourcen übereinstimmen.

## <span id="copy-blobs"></span></a> Kopieren von Azure-BLOBs mit AzCopy

Die nachfolgenden Beispiele veranschaulichen eine Reihe von Szenarien zum Kopieren von BLOBs mit AzCopy.

### Kopieren einzelner BLOBs

**Hochladen einer Datei aus dem Dateisystem in den BLOB-Speicher:**

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

**Herunterladen eines BLOBs aus dem BLOB-Speicher in das Dateisystem:**

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### Kopieren von BLOBs über serverseitige Kopien

Wenn Sie einen BLOB innerhalb eines Speicherkontos oder zwischen Speicherkonten kopieren, wird ein serverseitiger Kopiervorgang ausgeführt. Weitere Informationen zu serverseitigen Kopiervorgängen finden Sie unter [Einführung zum asynchronen kontoübergreifenden Kopier-BLOB][Einführung zum asynchronen kontoübergreifenden Kopier-BLOB].

**Kopieren von BLOBs innerhalb eines Speicherkontos:**

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt 

**Kopieren von BLOBs zwischen Speicherkonten:**

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

### Kopieren von BLOBs aus der sekundären Region

Wenn für Ihr Speicherkonto der Lesezugriff auf georedundanten Speicher aktiviert ist, können Sie Daten aus der sekundären Region kopieren.

**Kopieren von BLOBs aus dem sekundären ins primäre Konto:**

    AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

**Herunterladen von BLOBs aus dem sekundären Konto in eine Datei des Dateisystems:**

    AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### Hochladen von Dateien in einen neuen BLOB-Container oder in ein neues virtuelles BLOB-Verzeichnis

**Hochladen von Dateien in einen neuen BLOB-Container**

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mynewcontainer /DestKey:key /Pattern:abc.txt

Wenn der angegebene Zielcontainer nicht vorhanden ist, wird er von AzCopy erstellt und die Datei anschließend in den Container hochgeladen.

**Hochladen von Dateien in ein neues virtuelles BLOB-Verzeichnis**

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Wenn das angegebene virtuelle Verzeichnis nicht vorhanden ist, lädt AzCopy die Datei hoch, um das virtuelle Verzeichnis in ihren Namen einzubeziehen (*z. B.* `vd/abc.txt` im obigen Beispiel).

### Herunterladen von BLOBs in einen neuen Ordner

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Wenn der Ordner `C:\myfolder` noch nicht vorhanden ist, wird er von AzCopy im Dateisystem erstellt und anschließend `abc.txt` in den neuen Ordner heruntergeladen.

### Rekursives Hochladen von Dateien und Unterordnern eines Verzeichnisses in einen Container

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

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

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key

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

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

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

Beachten Sie, dass jeder AzCopy-Parameter in einer Zeile angegeben werden muss. Wenn Sie den Parameter z. B. auf zwei Zeilen verteilen, schlägt AzCopy fehl, wie hier für den `/sourcekey`-Parameter gezeigt:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    [sourcekey]
    /S 
    /Y

### Angeben einer Shared Access Signature (SAS)

**Angeben einer SAS für den Quellcontainer mithilfe der Option /sourceSAS**

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /DestC:\myfolder /SourceSAS:SAS /S

**Angeben einer SAS für den Quellcontainer im URI des Quellcontainers**

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

**Angeben einer SAS für den Zielcontainer mithilfe der Option /destSAS**

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer1 /DestSAS:SAS /Pattern:abc.txt

**Angeben einer SAS für die Quell- und Zielcontainer**

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

### Angeben eines Journaldateiordners

Sobald ein Befehl für AzCopy ausgeführt wird, prüft es, ob eine Journaldatei im Standardordner oder in einem Ordner vorhanden ist, den Sie über diese Option angegeben haben. Wenn die Journaldatei in keinem dieser Ordner vorhanden ist, behandelt AzCopy diesen Vorgang als neuen Vorgang und generiert somit eine neue Journaldatei.

Wenn die Journaldatei vorhanden ist, prüft AzCopy, ob die von Ihnen eingegebene Befehlszeile mit der Befehlszeile in der Journaldatei übereinstimmt. Wenn die beiden Befehlszeilen übereinstimmen, setzt AzCopy den unvollständigen Vorgang fort. Wenn sie nicht übereinstimmen, werden Sie aufgefordert, entweder die Journaldatei zu überschreiben, um einen neuen Vorgang zu starten, oder den aktuellen Vorgang abzubrechen.

**Verwenden des Standardspeicherorts für die Journaldatei**

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Wenn Sie die Option `/Z` auslassen oder die Option `/Z` ohne den Ordnerpfad angeben (wie oben gezeigt), erstellt AzCopy die Journaldatei am Standardspeicherort, `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Wenn die Journaldatei bereits vorhanden ist, setzt AzCopy den Vorgang auf Basis der Journaldatei fort.

**Angeben eines benutzerdefinierten Speicherorts für die Journaldatei**

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

In diesem Beispiel wird die Journaldatei erstellt, sofern diese nicht bereits vorhanden ist. Wenn sie vorhanden ist, setzt AzCopy den Vorgang auf Basis der Journaldatei fort.

**Fortsetzen eines AzCopy-Vorgangs**

    AzCopy /Z:C:\journalfolder\

In diesem Beispiel wird der letzte Vorgang fortgesetzt, der möglicherweise nicht abgeschlossen werden konnte.

### Generieren einer Protokolldatei

**Schreiben in die ausführliche Protokolldatei am Standardspeicherort**

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Wenn Sie die Option `/V` ohne einen Dateipfad zum ausführlichen Protokoll angeben, erstellt AzCopy die Protokolldatei am Standardspeicherort, `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

**Schreiben in die ausführliche Protokolldatei an einem benutzerdefinierten Speicherort**

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Wenn Sie nach der Option `/V` einen relativen Pfad angeben, z. B. `/V:test/azcopy1.log`, dann wird das ausführliche Protokoll im aktuellen Arbeitsverzeichnis in einem Unterordner namens `test` erstellt.

### Festlegen desselben Zeitpunkts für die Uhrzeit der letzten Änderung der heruntergeladenen Dateien wie für die Quell-BLOBs

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

### Ausschließen von BLOBs aus dem Kopiervorgang auf Basis der Uhrzeit der letzten Änderung

Geben Sie die Option `/MT` an, um die Uhrzeit der letzten Änderung des Quell-BLOBs mit der Zieldatei zu vergleichen.

**Ausschließen von BLOBs, die neuer sind als die Zieldatei**

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

**Ausschließen von BLOBs, die älter sind als die Zieldatei**

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

### Angeben der Anzahl der zu startenden gleichzeitigen Vorgängen

Die Option `/NC` gibt die Anzahl der gleichzeitigen Kopiervorgänge an. Standardmäßig startet AzCopy gleichzeitige Vorgänge mit dem Achtfachen der Anzahl der verfügbaren Core-Prozessoren. Wenn Sie AzCopy in einem Netzwerk mit geringer Bandbreite ausführen, können Sie eine niedrigere Anzahl für diese Option angeben, um Fehler durch gleichzeitig verwendete Ressourcen zu vermeiden.

### Ausführen von AzCopy für BLOB-Ressourcen im Speicheremulator

    AzCopy /Source:https://127.0.0.1:10004/myaccount/myfileshare/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

## <span id="copy-files"></span></a> Kopieren von Dateien in den Azure-Dateispeicher mit AzCopy (nur Vorschauversion)

Die nachfolgenden Beispiele veranschaulichen eine Reihe von Szenarien zum Kopieren von Azure-Dateien mit AzCopy.

### Herunterladen von Dateien aus einer Azure-Dateifreigabe in das Dateisystem

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Wenn die angegebene Quelle eine Azure-Dateifreigabe ist, müssen Sie entweder den genauen Dateinamen angeben, (*z. B.* `abc.txt`), um eine einzelne Datei zu kopieren, oder die Option `/S` angeben, um alle Dateien in der Freigabe rekursiv zu kopieren. Wenn Sie sowohl ein Dateimuster als auch die Option `/S` angeben, führt dies zu einem Fehler.

### Rekursives Herunterladen von Dateien und Ordnern einer Azure-Dateifreigabe in das Dateisystem

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Beachten Sie, dass leere Ordner nicht kopiert werden.

### Rekursives Hochladen von Dateien und Ordnern aus dem Dateisystem in eine Azure-Dateifreigabe

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Beachten Sie, dass leere Ordner nicht kopiert werden.

### Rekursives Hochladen von Dateien, die mit dem angegebenen Dateimuster übereinstimmen, in eine Azure-Dateifreigabe

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## <span id="copy-entities"></span></a> Kopieren von Entitäten in einer Azure-Tabelle mit AzCopy (nur Vorschauversion)

Die nachfolgenden Beispiele veranschaulichen eine Reihe von Szenarien zum Kopieren von Azure-Tabellenentitäten mit AzCopy.

### Exportieren von Entitäten zum lokalen Dateisystem

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:D:\test\ /SourceKey:key

### Exportieren von Entitäten zu einem Azure-Blob

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy generiert eine JSON-Datendatei in den lokalen Ordner oder Blobcontainer mit der folgenden Namenskonvention:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Die generierte JSON-Datendatei folgt dem Nutzlastformat für minimale Metadaten. Details in Bezug auf dieses Nutzlastformat finden Sie unter [Nutzlastformat für Tabellendienstvorgänge][Nutzlastformat für Tabellendienstvorgänge].

### Aufteilen der exportierten Dateien

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy verwendet einen *Volumeindex* in den Dateinamen für die Aufteilungsdaten zum Unterscheiden mehrerer Dateien. Der Volumeindex besteht aus zwei Teilen, einem *Partitionsschlüsselbereichs-Index* und einen *Aufteilungsdateiindex*. Beide Indizes sind nullbasiert.

Der Partitionsschlüsselbereichs-Index ist 0, wenn der Benutzer die Option `/PKRS` (wird im nächsten Abschnitt vorgestellt) nicht angibt.

Angenommen, AzCopy generiert zwei Datendateien, nachdem der Benutzer die Option `/SplitSize` angegeben hat. Die daraus resultierenden Datendateinamen könnten wie folgt aussehen:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Beachten Sie, dass der minimal mögliche Wert für die Option `/SplitSize` 32 MB lautet. Wenn es sich beim angegebenen Ziel um einen Blobspeicher handelt, teilt AzCopy die Datendatei auf, sobald die Größe die Blobgrößenbegrenzung (200 GB) erreicht, und zwar unabhängig davon, welche `/SplitSize`-Option durch den Benutzer angegeben wurde.

### Paralleles Exportieren von Entitäten

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:D:\test\ /SourceKey:key /PKRS:"aa#bb"

AzCopy startet gleichzeitige Vorgänge zum Exportieren von Entitäten, wenn der Benutzer die `/PKRS`-Option angibt. Jeder Vorgang exportiert einen Partitionsschlüsselbereich.

Beachten Sie, dass die Anzahl gleichzeitiger Vorgänge auch durch die `/NC`-Option gesteuert wird. AzCopy verwendet beim Kopieren von Tabellenentitäten die Anzahl der Core-Prozessoren als den Standardwert von `/NC`, selbst wenn `/NC` nicht angegeben wurde. Wenn der Benutzer die `/PKRS`-Option angibt, verwendet AzCopy den kleineren der beiden Werte (Partitionsschlüsselbereich im Vergleich zu implizit oder explizit angegebenen gleichzeitigen Vorgängen), um die Anzahl der zu startenden gleichzeitigen Vorgänge zu bestimmen. Geben Sie `AzCopy /?:NC` an der Befehlszeile ein, um weitere Einzelheiten zu erhalten.

### Paralleles Importieren von Entitäten

    AzCopy /Source:D:\test\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace 

Beim Exportieren von Tabellenentitäten schreibt AzCopy eine Manifestdatei zum angegebenen Zielordner oder Blobcontainer. Die Manifestdatei wird durch den Importvorgang verwendet, um die erforderlichen Datendateien zu suchen und um die Datenüberprüfung während des Importvorgangs auszuführen. Die Manifestdatei verwendet die folgende Namenskonvention:

    <account name>_<table name>_<timestamp>.manifest

Die `/EntityOperation`-Option gibt an, wie Entitäten in die Tabelle eingefügt werden. Mögliche Werte:

-   `InsertOrSkip`: Überspringt eine vorhandene Entität oder fügt eine neue Entität ein, wenn sie in der Tabelle nicht vorhanden ist.
-   `InsertOrMerge`: Führt eine vorhandene Entität zusammen oder fügt eine neue Entität ein, wenn sie in der Tabelle nicht vorhanden ist.
-   `InsertOrReplace`: Ersetzt eine vorhandene Entität oder fügt eine neue Entität ein, wenn sie in der Tabelle nicht vorhanden ist.

Beachten Sie, dass Sie die `/PKRS`-Option im Importszenario nicht angeben können. Im Gegensatz zum Exportszenario, in dem Sie die `/PKRS`-Option zum Starten gleichzeitiger Vorgänge angeben müssen, startet AzCopy beim Importieren von Entitäten standardmäßig gleichzeitige Vorgänge. Die standardmäßige Anzahl gleichzeitig gestarteter Vorgänge entspricht der Anzahl an Core-Prozessoren. Sie können jedoch eine unterschiedliche Anzahl gleichzeitiger Vorgänge mit der `/NC`-Option angeben. Geben Sie `AzCopy /?:NC` an der Befehlszeile ein, um weitere Einzelheiten zu erhalten.

## <span id="versions"></span></a> AzCopy-Versionen

| Version    | Neuerungen                                                                                                                                                                                                                                                                          |
|------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **V4.0.0** | **Aktuelle Vorschauversion. Enthält alle Funktionalitäten von V3.0.0. Unterstützt zudem das Kopieren zum oder aus dem Azure-Dateispeicher und das Kopieren von Entitäten zum oder aus dem Azure-Tabellenspeicher.**                                                                 |
| **V3.0.0** | **Aktuelle Vorabversion. Ändert die AzCopy-Befehlszeilensyntax dahingehend, dass Parameternamen erforderlich sind, und überarbeitet die Befehlszeilenhilfe. Diese Version unterstützt ausschließlich das Kopieren zum und aus dem Azure-Blobspeicher.**                             |
| V2.5.1     | Aktuelle Version. Optimiert die Leistung bei der Verwendung der Optionen /xo und /xn. Behebt Fehler, die mit Fehlern in Bezug auf Sonderzeichen in Quelldateinamen und Journaldateien in Zusammenhang stehen, nachdem der Benutzer eine falsche Befehlszeilensyntax eingegeben hat. |
| V2.5.0     | Optimiert die Leistung für umfangreiche Kopierszenarien und führt verschiedene wichtige Verbesserungen für Benutzerfunktionen ein.                                                                                                                                                  |
| V2.4.1     | Unterstützt die Angabe des Zielordners im Installations-Assistenten.                                                                                                                                                                                                                |
| V2.4.0     | Unterstützt das Hochladen und Herunterladen von Dateien für den Azure-Dateispeicher.                                                                                                                                                                                                |
| V2.3.0     | Unterstützt den Lesezugriff für georedundante Speicherkonten.                                                                                                                                                                                                                       |
| V2.2.2     | Aktualisierung für die Verwendung der Azure-Speicherclientbibliothek, Version 3.0.3.                                                                                                                                                                                                |
| V2.2.1     | Behobene Leistungsprobleme beim Kopieren großer Dateimengen innerhalb desselben Speicherkontos.                                                                                                                                                                                     |
| V2.2       | Unterstützt das Festlegen des virtuellen Verzeichnistrennzeichens für BLOB-Namen. Unterstützt die Angabe des Journaldateipfades.                                                                                                                                                    |
| V2.1       | Bietet mehr als 20 Optionen zur Unterstützung von BLOB-Vorgängen zum effizienten Hochladen, Herunterladen und Kopieren.                                                                                                                                                             |

## <span id="next-steps"></span></a>Nächste Schritte

Weitere Informationen zu Azure Storage und zu AzCopy finden Sie in den folgenden Ressourcen.

### Azure Storage-Dokumentation:

-   [Einführung in Azure Storage][Einführung in Azure Storage]
-   [Speichern von Dateien in BLOB-Speicher][Speichern von Dateien in BLOB-Speicher]
-   [Erstellen einer SMB-Dateifreigabe in Azure mit Dateispeicher][Erstellen einer SMB-Dateifreigabe in Azure mit Dateispeicher]

### Azure Storage-Blogbeiträge:

-   [Einführung in den Microsoft Azure-Dateidienst][Einführung in den Microsoft Azure-Dateidienst]
-   [AzCopy 2.5: Optimiert für große Kopierszenarien][AzCopy 2.5: Optimiert für große Kopierszenarien]
-   [AzCopy: Unterstützung des Lesezugriffs auf georedundanten Speicher][AzCopy: Unterstützung des Lesezugriffs auf georedundanten Speicher]
-   [AzCopy: Übertragen von Daten mit neu startbarem Modus und SAS-Token][AzCopy: Übertragen von Daten mit neu startbarem Modus und SAS-Token]
-   [AzCopy: Verwenden des kontoübergreifenden Kopier-BLOBs][AzCopy: Verwenden des kontoübergreifenden Kopier-BLOBs]
-   [AzCopy: Hochladen/Herunterladen von Dateien für Windows Azure-Blobs][AzCopy: Hochladen/Herunterladen von Dateien für Windows Azure-Blobs]

  [Herunterladen und Installieren von AzCopy]: #install
  [Informationen zur Befehlszeilensyntax von AzCopy]: #syntax
  [Einschränken gleichzeitiger Schreibvorgänge beim Kopieren von Daten]: #limit-writes
  [Kopieren von Azure-BLOBs mit AzCopy]: #copy-blobs
  [Kopieren von Dateien in einer Azure-Dateifreigabe mit AzCopy (nur Vorschauversion)]: #copy-files
  [Kopieren von Azure-Tabellenentitäten mit AzCopy (nur Vorschauversion)]: #copy-entities
  [AzCopy-Versionen]: #versions
  [Nächste Schritte]: #next-steps
  [neueste Version von AzCopy]: http://aka.ms/downloadazcopy
  [neueste Vorschauversion]: http://aka.ms/downloadazcopypr
  [Einführung zum asynchronen kontoübergreifenden Kopier-BLOB]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx
  [Nutzlastformat für Tabellendienstvorgänge]: http://msdn.microsoft.com/library/azure/dn535600.aspx
  [Einführung in Azure Storage]: http://azure.microsoft.com/de-de/documentation/articles/storage-introduction/
  [Speichern von Dateien in BLOB-Speicher]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Erstellen einer SMB-Dateifreigabe in Azure mit Dateispeicher]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-files/
  [Einführung in den Microsoft Azure-Dateidienst]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
  [AzCopy 2.5: Optimiert für große Kopierszenarien]: http://go.microsoft.com/fwlink/?LinkId=507682
  [AzCopy: Unterstützung des Lesezugriffs auf georedundanten Speicher]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx
  [AzCopy: Übertragen von Daten mit neu startbarem Modus und SAS-Token]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx
  [AzCopy: Verwenden des kontoübergreifenden Kopier-BLOBs]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx
  [AzCopy: Hochladen/Herunterladen von Dateien für Windows Azure-Blobs]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
