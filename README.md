# Leitfaden für Mitwirkende bei der technischen Dokumentation zu Azure

Sie haben das GitHub Repository gefunden, das die Quelle für die technische Dokumentation zu Microsoft Azure beherbergt, wie sie im Azure-Dokumentationscenter unter [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) veröffentlicht wird.

Dieses Repository enthält auch Anleitungen, wie Sie zu unserer technischen Dokumentation beitragen können. Die einzelnen Artikel dieses Leitfadens für Mitwirkende finden Sie [im Index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## Mitwirken bei der Azure-Dokumentation

Vielen Dank für Ihr Interesse an der Azure-Dokumentation.

* [Möglichkeiten zur Mitwirkung](#ways-to-contribute)
* [Über Ihre Beiträge zu Azure-Inhalten](#about-your-contributions-to-azure-content)
* [Aufbau des Repositorys](#repository-organization)
* [Verwenden von GitHub und Git sowie dieses Repositorys](#use-github-git-and-this-repository)
* [Verwenden von Markdown für die Formatierung](#how-to-use-markdown-to-format-your-topic)
* [Weitere Ressourcen](#more-resources)
* [Index aller Artikel im Leitfaden für Mitwirkende](./contributor-guide/contributor-guide-index.md) (öffnet eine neue Seite)

## Möglichkeiten zur Mitwirkung

Sie können auf verschiedene Weise zur [Azure-Dokumentation](http://azure.microsoft.com/documentation/) beitragen:

* Beteiligen Sie sich an einer [Forumsdiskussion](http://social.msdn.microsoft.com/Forums/windowsazure/home).
* Verfassen Sie unter den Artikeln Disqus-Kommentare.
* Tragen Sie zu technischen Artikeln über die GitHub-Benutzeroberfläche bei. Das ist einfach. Entweder finden Sie den Artikel in diesem Repository, oder Sie besuchen die Seite mit dem Artikel unter [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) und klicken dort auf den Link, der zur GitHub-Quelle für den Artikel führt.
* Wenn Sie wesentliche Änderungen an einem vorhandenen Artikel vornehmen, Bilder hinzufügen oder austauschen oder einen neuen Artikel verfassen möchten, müssen Sie eine Verzweigung von diesem Repository erstellen, Git Bash und Markdown Pad installieren und einige Git-Befehle erlernen.

##Über Ihre Beiträge zu Azure-Inhalten

###Kleinere Korrekturen

Kleinere Korrekturen oder Klarstellungen, die Sie für die Dokumentation oder für Codebeispiele in diesem Repository einreichen, werden durch die [Nutzungsbedingungen für die Azure-Website](http://azure.microsoft.com/support/legal/website-terms-of-use/) abgedeckt.


###Größere Beiträge

Wenn Sie einen Pull Request mit neuen Inhalten oder signifikanten Änderungen an der Dokumentation und Codebeispielen übermitteln, senden wir Ihnen einen Kommentar in GitHub, in dem wir Sie bitten, online eine Lizenzvereinbarung für Beiträge (Contribution License Agreement, CLA) zu unterzeichnen. Dazu müssen Sie einer der folgenden Gruppen angehören:

* Mitglieder der Microsoft Open Technologies-Gruppe
* Mitwirkende, die nicht für Microsoft arbeiten

Sie müssen das Onlineformular ausfüllen, bevor wir Ihren Pull Request annehmen können.

Ausführliche Informationen finden Sie unter [http://azure.github.io/guidelines.html#cla](http://azure.github.io/guidelines.html#cla).

## Aufbau des Repositorys

Der Inhalt im Repository „azure-content“ folgt in seiner Struktur der Dokumentation unter [Azure.Microsoft.com](http://azure.microsoft.com). Das Repository enthält zwei Stammordner:

### \articles

Der Ordner *\articles* enthält die Dokumentationsartikel, formatiert als Markdown-Dateien mit der Erweiterung *.md*.

Artikel im Stammverzeichnis werden auf „Azure.Microsoft.com“ unter dem Pfad *http://azure.microsoft.com/documentation/articles/{article-name-without-md}/* veröffentlicht.

* **Dateinamen für Artikel:** Lesen Sie unsere [Anleitung zur Benennung von Dateien](./contributor-guide/file-names-and-locations.md).

Artikel in einem eigenen dienstspezifischen Ordner („Dienstordner“) werden auf „Azure.Microsoft.com“ unter dem Pfad 
*http://azure.microsoft.com/documentation/articles/service-folder/{article-name-without-md}/* veröffentlicht.

* **Unterordner für Medien:** Der Ordner *\articles* enthält den Ordner *\media* für Mediendateien zu den Stammverzeichnisartikeln. Dieser enthält Unterordner mit den Bildern für den jeweiligen Artikel. Die Dienstordner enthalten einen separaten Medienordner für die Artikel im jeweiligen Dienstordner. Die Medienordner für Bilder zu einem Artikel heißen genauso wie die Artikeldatei, aber ohne die Dateierweiterung *.md*.

### \includes

Sie können Abschnitte mit wiederverwendbaren Inhalten erstellen, die in einen oder mehrere Artikel eingefügt werden sollen. Weitere Informationen finden Sie unter [Benutzerdefinierte Erweiterungen für unsere technischen Inhalte](./contributor-guide/custom-markdown-extensions.md).

### \markdown templates

Dieser Ordner enthält unsere Markdown-Standardvorlage mit der grundlegenden Markdown-Formatierung, die Sie für einen Artikel benötigen.

### \contributor-guide

Dieser Ordner enthält die Artikel unseres Leitfadens für Mitwirkende.

## Verwenden von GitHub und Git sowie dieses Repositorys

Lesen Sie unter [Installieren und Einrichten der Tools zum Erstellen von Beiträgen in GitHub](./contributor-guide/tools-and-setup.md), wie Sie in der GitHub-Benutzeroberfläche kleine Änderungen vornehmen und wie Sie eine Verzweigung und einen Klon des Repositorys erstellen, um größere Beiträge zu verfassen.

Sie möchten Git Bash installieren und lokal arbeiten? Dann finden Sie die Schritte zum Erstellen einer lokalen Arbeitsverzweigung, zum Vornehmen von Änderungen und zum Einreichen der Änderungen an die Hauptverzweigung unter [Git-Befehle zum Erstellen eines neuen Artikels oder zum Aktualisieren eines vorhandenen Artikels](./contributor-guide/git-commands-for-master.md).

### Verzweigungen

Wir empfehlen, dass Sie lokale Arbeitsverzweigungen erstellen, die auf den genauen Umfang der beabsichtigen Änderung ausgerichtet sind. Jede Verzweigung sollte auf ein einziges Konzept oder einen einzigen Artikel begrenzt sein. Dies vereinfacht den Arbeitsablauf und verringert die Wahrscheinlichkeit von Konflikten bei der Zusammenführung. Die folgenden Vorhaben rechtfertigen vom Umfang her eine neue Verzweigung:

* Ein neuer Artikel (und zugehörige Bilder)
* Überarbeitung der Rechtschreibung und Grammatik in einem Artikel
* Änderung der Formatierung bei einer großen Zahl von Artikeln (z. B. neue Copyright-Fußzeile)

## Verwenden von Markdown für die Formatierung eines Themas

Bei allen Artikeln in diesem Repository wird ein an GitHub angepasstes Markdown verwendet. Hier eine Liste mit Ressourcen:

- [Grundlegendes zu Markdown](https://help.github.com/articles/markdown-basics/)

- [Markdown-Cheatsheet zum Ausdrucken](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Eine Liste der von uns empfohlenen Markdown-Editoren finden Sie im Thema zum [Installieren und Einrichten der Tools](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## Metadaten zu Artikeln

Metadaten zu Artikeln sind die Voraussetzung für bestimmte Funktionen auf der Website „azure.microsoft.com“, z. B. für die Zuordnung von Autoren und Mitwirkenden, für Brotkrümelnavigation und Suchmaschinenoptimierung sowie für Berichtsfunktionen, mit denen Microsoft den Inhalt bewertet. Metadaten sind also wichtig. [Hier finden Sie eine Anleitung, wie Sie Metadaten richtig verwenden](./contributor-guide/article-metadata.md).

## Weitere Ressourcen

Alle Anleitungsthemen finden Sie im [Index zum Leitfaden für Mitwirkende](./contributor-guide/contributor-guide-index.md).

<!---HONumber=AcomDC_0307_2016-->