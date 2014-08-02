<properties linkid="develop-java-tutorials-jenkins-continuous-integration" urlDisplayName="Jenkins Continuous Integration" pageTitle="Using Azure Storage with a Jenkins Continuous Integration Solution | Microsoft Azure" metaKeywords="" description="This tutorial show how to use the Azure blob service as a repository for build artifacts created by a Jenkins continuous integration solution." metaCanonical="" services="storage" documentationCenter="Java" title="Using Azure Storage with a Jenkins Continuous Integration solution" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

Verwenden von Azure-Speicher mit einer Jenkins-Lösung für die fortlaufende Integration
======================================================================================

*Von [Microsoft Open Technologies Inc.](http://msopentech.com)*

Im Folgenden wird beschrieben, wie Sie den Blob-Dienst von Azure als Repository für Buildartefakte verwenden, die von einer Jenkins-Lösung für die fortlaufende Integration (Jenkins CI) erstellt wurden. Dies ist zum Beispiel dann hilfreich, wenn Sie in einer agilen Entwicklungsumgebung (in Java oder anderen Sprachen) programmieren, Builds auf Basis der fortlaufenden Integration ausgeführt werden, und Sie ein Repository für Ihre Buildartefakte benötigen, sodass Sie sie beispielsweise mit anderen Mitgliedern der Organisation oder Ihren Kunden gemeinsam nutzen oder ein Archiv pflegen können.

In diesem Lernprogramm verwenden Sie das Azure-Speicher-Plug-In für Jenkins CI, das von Microsoft Open Technologies, Inc. zur Verfügung gestellt wird.

Inhaltsverzeichnis
------------------

-   [Überblick über Jenkins](#overview)
-   [Vorteile der Verwendung des Blob-Dienstes](#benefits)
-   [Voraussetzungen](#prerequisites)
-   [Verwenden des Blob-Dienstes mit Jenkins CI](#howtouse)
-   [Installieren des Azure-Speicher-Plug-Ins](#howtoinstall)
-   [Konfigurieren des Azure-Speicher-Plug-Ins für die Verwendung Ihres Speicherkontos](#howtoconfigure)
-   [Erstellen einer Postbuildaktion, die Ihre Buildartefakte in Ihr Speicherkonto hochlädt](#howtocreatepostbuild)
-   [Vom Blob-Dienst verwendete Komponenten](#components)

ÜberblickÜberblick über Jenkins
-------------------------------

Jenkins ermöglicht die fortlaufende Integration (Continuous Integration, CI) eines Softwareprojekts, da Entwickler ihre Codeänderungen auf einfache Weise einbinden und Builds automatisch und häufig erstellen lassen können. Dadurch wird die Produktivität der Entwickler gesteigert. Builds werden mit Versionsangaben versehen, und Buildartefakte können in verschiedene Repositorys hochgeladen werden. In diesem Thema wird gezeigt, wie Sie Azure-Blob-Speicher als Repository für die Buildartefakte verwenden.

Weitere Informationen zu Jenkins finden Sie unter [Meet Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

VorteileVorteile der Verwendung des Blob-Dienstes
-------------------------------------------------

Die Verwendung des Blob-Dienstes zum Hosten Ihrer Buildartefakte aus der agilen Entwicklung hat folgende Vorteile:

-   Hohe Verfügbarkeit Ihrer Buildartefakte
-   Schnelles Hochladen Ihrer Buildartefakte durch Jenkins CI
-   Schnelles Herunterladen Ihrer Buildartefakte durch Kunden und Partner
-   Kontrolle über Benutzerzugriffsrichtlinien, mit Wahlmöglichkeiten zwischen anonymer Zugriff, Zugriff per Shared Access Signature mit Ablaufdatum, privater Zugriff usw.

VoraussetzungenVoraussetzungen
------------------------------

Sie müssen folgende Voraussetzungen erfüllen, um den Blob-Dienst mit Ihrer Jenkins CI-Lösung zu verwenden:

-   Eine Jenkins-Lösung für die fortlaufende Integration (Jenkins CI)

    Wenn Sie noch keine Jenkins CI-Lösung im Einsatz haben, können Sie eine Jenkins CI-Lösung mithilfe folgender Methode ausführen:

    1.  Laden Sie auf einem Java-fähigen Computer "jenkins.war" von &lt;http://jenkins-ci.org\&gt; herunter.
    2.  Führen Sie in einer Eingabeaufforderung, die für den Ordner, der "jenkins.war" enthält, geöffnet wird, folgenden Befehl aus:

        `java -jar jenkins.war`

    3.  Öffnen Sie in Ihrem Browser `http://localhost:8080/`. Dadurch wird das Jenkins-Dashboard geöffnet, über das Sie das Azure-Speicher-Plug-In installieren und konfigurieren können.

        Eine typische Jenkins CI-Lösung würde zwar zur Ausführung als Service konfiguriert, die Ausführung von "jenkins.war" über die Befehlszeile reicht für dieses Lernprogramm jedoch aus.

-   Ein Azure-Konto. Sie können sich für ein Azure-Konto unter &lt;http://www.windowsazure.com\&gt; anmelden.

-   Ein Azure-Speicherkonto. Wenn Sie noch kein Speicherkonto haben, können Sie eines erstellen, indem Sie die Schritte unter [How to Create a Storage Account](http://go.microsoft.com/fwlink/?LinkId=279823) (Erstellen eines Speicherkontos, in englischer Sprache) befolgen.

-   Vorkenntnisse der Jenkins CI-Lösung werden empfohlen, sind aber nicht zwingend erforderlich, da in den folgenden Abschnitten ein einfaches Beispiel verwendet wird, um zu zeigen, welche Schritte erforderlich sind, wenn Sie den Blob-Dienst als Repository für Jenking CI-Buildartefakte nutzen möchten.

Verwenden des Blob-DienstesVerwenden des Blob-Dienstes mit Jenkins CI
---------------------------------------------------------------------

Um den Blob-Dienst mit Jenkins verwenden zu können, müssen Sie das Azure-Speicher-Plug-In installieren, das Plug-In für die Verwendung Ihres Speicherkontos konfigurieren und dann eine Postbuildaktion erstellen, die Ihre Buildartefakte in Ihr Speicherkonto hochlädt. Diese Schritte sind in den folgenden Abschnitten beschrieben.

InstallierenInstallieren des Azure-Speicher-Plug-Ins
----------------------------------------------------

1.  Klicken Sie im Jenkins-Dashboard auf **Manage Jenkins**.
2.  Klicken Sie auf der Seite **Manage Jenkins** auf **Manage Plugins**.
3.  Klicken Sie auf die Registerkarte **Available**.
4.  Aktivieren Sie im Bereich **Artifact Uploaders** das Kontrollkästchen **Azure Storage plugin**.
5.  Klicken Sie entweder auf **Install without restart** oder auf **Download now and install after restart**.
6.  Starten Sie Jenkins neu.

KonfigurierenKonfigurieren des Azure-Speicher-Plug-Ins für die Verwendung Ihres Speicherkontos
----------------------------------------------------------------------------------------------

1.  Klicken Sie im Jenkins-Dashboard auf **Manage Jenkins**.
2.  Klicken Sie auf der Seite **Manage Jenkins** auf **Configure System**.
3.  Führen Sie im Bereich **Azure Storage Account Configuration** folgende Schritte aus:
    1.  Geben Sie Ihren Speicherkontonamen ein, den Sie über das Azure-Portal unter &lt;https://manage.windowsazure.com\&gt; erhalten.
    2.  Geben Sie Ihren Speicherkontoschlüssel ein, der ebenfalls über das Azure-Portal erhältlich ist.
    3.  Verwenden Sie den Standardwert für **Blob Service Endpoint URL**, wenn Sie die öffentliche Azure-Cloud verwenden. Wenn Sie mit einer anderen Azure-Cloud arbeiten, verwenden Sie den Endpunkt, der im Azure-Verwaltungsportal für Ihr Speicherkonto angegeben ist.
    4.  Klicken Sie auf **Validate Storage Credentials**, um Ihr Speicherkonto zu validieren.
    5.  [Optional] Wenn Sie über weitere Speicherkonten verfügen, die Sie für Jenkins CI verfügbar machen möchten, klicken Sie auf **Add more Storage Accounts**.
    6.  Klicken Sie auf **Save**, um Ihre Einstellungen zu speichern.

Erstellen einer PostbuildaktionErstellen einer Postbuildaktion, die Ihre Buildartefakte in Ihr Speicherkonto hochlädt
---------------------------------------------------------------------------------------------------------------------

Für das Lernprogramm müssen wir zunächst einen Auftrag erstellen, der mehrere Dateien erstellen wird, und ihn dann zur Postbuildaktion hinzufügen, um die Dateien in Ihr Speicherkonto hochzuladen.

1.  Klicken Sie im Jenkins-Dashboard auf **New Job**.
2.  Nennen Sie den Auftrag **MyJob**, klicken Sie auf **Build a free-style software project**, und klicken Sie dann auf **OK**.
3.  Klicken Sie in der Auftragskonfiguration im Bereich **Build** auf **Add build step**, und wählen Sie dann **Execute Windows batch command** aus.
4.  Verwenden Sie in **Command** folgende Befehle:

         md text
         cd text
         echo Hello Azure Storage from Jenkins > hello.txt
         date /t > date.txt
         time /t >> date.txt

5.  Klicken Sie in der Auftragskonfiguration im Bereich **Post-build Actions** auf **Add post-build action**, und wählen Sie **Upload artifacts to Azure Blob storage** aus.
6.  Wählen Sie unter **Storage Account Name** das zu verwendende Speicherkonto aus.
7.  Geben Sie unter **Container Name** den Containernamen ein. (Der Container wird erstellt, wenn er beim Hochladen der Buildartefakte noch nicht vorhanden ist.) Sie können Umgebungsvariablen verwenden. Geben Sie also für dieses Beispiel **\${JOB\_NAME}** als Containernamen ein.

    **Tipp**

    Unter dem Bereich **Command**, in dem Sie ein Skript für **Execute Windows batch command** eingegeben haben, befindet sich ein Link zu den von Jenkins erkannten Umgebungsvariablen. Klicken Sie auf diesen Link, um die Namen und Beschreibungen der Umgebungsvariablen anzuzeigen. Beachten Sie, dass Umgebungsvariablen, die Sonderzeichen enthalten, z. B. die Umgebungsvariable **BUILD\_URL**, nicht als Containername oder gemeinsamer virtueller Pfad zulässig sind.

8.  Klicken Sie für dieses Beispiel auf **Make container public**. (Wenn Sie einen privaten Container verwenden möchten, müssen Sie eine Shared Access Signature erstellen, um den Zugriff zu ermöglichen. Dies geht jedoch über den Rahmen dieses Thema hinaus. Sie finden weitere Informationen zu Shared Access Signatures unter [Erstellen und Verwenden einer SAS (Shared Access Signature)](http://go.microsoft.com/fwlink/?LinkId=279889).
9.  Geben Sie unter **List of Artifacts to upload** die Zeichenfolge **text/\*.txt** ein.
10. Geben Sie unter **Common virtual path for uploaded artifacts** die Zeichenfolge **\${BUILD\_ID}/\${BUILD\_NUMBER}** ein.
11. Klicken Sie auf **Save**, um Ihre Einstellungen zu speichern.
12. Klicken Sie im Jenkins-Dashboard auf **Build Now**, um **MyJob** auszuführen. Prüfen Sie den Status in der Ausgabe der Konsole. Statusmeldungen für Azure-Speicher werden in die Ausgabe der Konsole aufgenommen, wenn die Postbuildaktion mit dem Hochladen von Buildartefakten beginnt.
13. Nach erfolgreichem Abschluss des Auftrags können Sie die Buildartefakte überprüfen, indem Sie den öffentlichen Blob öffnen.
    1.  Melden Sie sich am Azure-Verwaltungsportal unter &lt;https://manage.windowsazure.com\&gt; an.
    2.  Klicken Sie auf **Storage**.
    3.  Klicken Sie auf den Speicherkontonamen, den Sie für Jenkins verwendet haben.
    4.  Klicken Sie auf **Container**.
    5.  Klicken Sie auf den Container **myjob**. Dies ist die Version des Auftragsnamens, den Sie beim Erstellen des Jenkins-Auftrags zugewiesen haben, in Kleinbuchstaben. Containernamen und Blob-Namen bestehen im Azure-Speicher aus Kleinbuchstaben (es wird zwischen Groß- und Kleinschreibung unterschieden). In der Liste der Blobs für den Container **myjob** sollte **hello.txt** und **date.txt** angezeigt werden. Kopieren Sie die URL für beide Elemente, und öffnen Sie sie in Ihrem Browser. Sie sehen die Textdatei, die als Buildartefakt hochgeladen wurde.

Komponenten des Blob-DienstesVom Blob-Dienst verwendete Komponenten
-------------------------------------------------------------------

Im Folgenden erhalten Sie einen Überblick über die Komponenten des Blob-Dienstes.

-   **Speicherkonto**: Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Dies ist die höchste Ebene des Namespaces für den Zugriff auf Blobs. Ein Konto kann eine beliebige Anzahl von Containern enthalten, solange deren Gesamtgröße 100TB nicht überschreitet.
-   **Container**: Ein Container dient zur Gruppierung eines Satzes von Blobs. Alle Blobs müssen sich in Containern befinden. Ein Konto kann eine beliebige Anzahl von Containern enthalten. In einem Container kann eine beliebige Anzahl von Blobs gespeichert sein.
-   **Blob**: Eine Datei von beliebiger Art und Größe. Es gibt zwei Arten von Blobs, die im Azure-Speicher gespeichert werden können: Block- und Seiten-Blobs. Die meisten Dateien sind Block-Blobs. Ein einzelner Block-Blob kann bis zu 200 GB groß sein. In diesem Lernprogramm werden Block-Blobs verwendet. Die andere Art von Blobs, Seiten-Blobs, kann bis zu 1 TB groß sein und ist effizienter, wenn Byte-Bereiche in einer Datei häufig geändert werden. Weitere Informationen über BLOBs finden Sie unter [Grundlegendes zu Block-BLOBs und Seiten-BLOBs](http://msdn.microsoft.com/en-us/library/windowsazure/ee691964.aspx).
-   **URL-Format**: Blobs sind über das folgende URL-Format adressierbar:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    (Das Format oben gilt für die öffentliche Azure-Cloud. Wenn Sie mit einer anderen Azure-Cloud arbeiten, verwenden Sie den Endpunkt im Azure-Verwaltungsportal, um Ihren URL-Endpunkt zu ermitteln.)

    Bei obigem Format steht `storageaccount` für den Namen Ihres Speicherkontos, `container_name` für den Namen des Containers und `blob_name` für den Namen des Blobs. Der Containername kann mehrere Pfade umfassen, die durch einen Schrägstrich (**/**) getrennt sind. Der Beispielcontainername in diesem Lernprogramm war **MyJob**, und **\${BUILD\_ID}/\${BUILD\_NUMBER}** wurde für den gemeinsamen virtuellen Pfad verwendet. Der Blob hat also eine URL in folgendem Format:

    `http://example.blob.core.windows.net/myjob/2013-01-28_15-00-35/2/hello.txt`


