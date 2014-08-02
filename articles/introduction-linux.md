<properties linkid="manage-linux-fundamentals-intro-to-linux" urlDisplayName="Intro to Linux" pageTitle="Introduction to Linux in Azure - Azure Tutorial" metaKeywords="Azure Linux vm, Linux vm" description="Learn about using Linux virtual machines on Azure." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Introduction to Linux on Azure" authors="" solutions="" manager="" editor="" />

Einführung in Linux auf Azure
=============================

Dieses Thema bietet einen Überblick über einige Aspekte der Verwendung virtueller Linux-Computer in der Azure-Cloud. Die Bereitstellung eines virtuellen Linux-Computers ist ein unkomplizierter Prozess, wenn sie mithilfe eines bereits in der Galerie vorhandenen Images erfolgt.

Inhaltsverzeichnis
------------------

-   [Authentifizierung: Benutzernamen, Kennwörter und SSH-Schlüssel.](#authentication)
-   [Erzeugen und Verwenden von SSH-Schlüsseln für die Anmeldung auf virtuellen Linux-Computern](#keygeneration)
-   [Erlangen von Superuser-Berechtigungen mit sudo](#superuserprivileges)
-   [Firewall-Konfiguration](#firewallconfiguration)
-   [Hostnamen-Änderungen](#hostnamechanges)
-   [Image-Erfassung virtueller Computer](#virtualmachine)
-   [Anfügen von Datenträgern](#attachingdisks)

Authentifizierung: Benutzernamen, Kennwörter und SSH-Schlüssel
--------------------------------------------------------------

Wenn Sie mithilfe des Azure-Verwaltungsportals einen virtuellen Linux-Computer erstellen, werden Sie aufgefordert, einen Benutzernamen, ein Kennwort und (optional) einen öffentlichen SSH-Schlüssel anzugeben. Die Auswahl eines Benutzernamens zur Bereitstellung eines virtuellen Computers auf Azure ist den folgenden Beschränkungen unterworfen: Namen von bereits auf dem virtuellen Computer vorhandenen Systemkonten – z. B. "Root" – sind nicht erlaubt. Wenn Sie keinen öffentlichen SSH-Schlüssel angeben, benötigen Sie zum Anmelden auf dem virtuellen Computer nur den angegebenen Benutzernamen und das Kennwort. Wenn Sie die Option wählen, einen öffentlichen SSH-Schlüssel im Verwaltungsportal hochzuladen, wird die kennwortbasierte Authentifizierung deaktiviert, und Sie müssen den entsprechenden privaten SSH-Schlüssel verwenden, um sich auf dem virtuellen Computer zu authentifizieren und anzumelden.

Erzeugen eines SSH-Schlüssels
-----------------------------

Die aktuelle Version des Verwaltungsportals akzeptiert nur öffentliche SSH-Schlüssel, die in einem X.509-Zertifikat enthalten sind. Gehen Sie wie nachstehend beschrieben vor, um SSH-Schlüssel mit Azure zu erzeugen und zu verwenden.

1.  Verwenden Sie openssl, um ein X.509-Zertifikat mit einem 2048-bit-RSA-Schlüsselpaar zu erzeugen.

         openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

    Bitte beantworten Sie die Fragen, die openssl Ihnen stellt (oder lassen Sie die Felder leer). Der Inhalt dieser Felder wird nicht von der Plattform verwendet.

2.  Ändern Sie die Berechtigungen auf dem privaten Schlüssel, um diesen zu sichern.

         chmod 600 myPrivateKey.key

3.  Wandeln Sie die Datei myCert.pem in das Dateiformat myCert.cer (DER-codiertes X.509-Zertifikat) um.

         openssl  x509 -outform der -in myCert.pem -out myCert.cer

4.  Laden Sie das myCert.cer hoch, während Sie den virtuellen Linux-Computer erstellen. Der Bereitstellungsvorgang installiert automatisch den öffentlichen Schlüssel in diesem Zertifikat in der Datei authorized\_keys für den angegebenen Benutzer im virtuellen Computer.

5.  Stellen Sie die Verbindung mit dem virtuellen Linux-Computer mithilfe von ssh her.

         ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

    Beim ersten Anmelden werden Sie aufgefordert, den Fingerabdruck des öffentlichen Schlüssels des Hosts zu übernehmen.

6.  Optional können Sie myPrivateKey.key in \~/.ssh/id\_rsa kopieren, so dass Ihr OpenSSH-Client dies automatisch übernimmt, ohne die -i-Option zu verwenden.

Erlangen von Superuser-Berechtigungen mit sudo
----------------------------------------------

Das Benutzerkonto, das während der Bereitstellung der virtuellen Computerinstanz auf Azure angegeben wird, ist ein Konto mit lokal weit reichenden Berechtigungen. Dieses Konto wird vom Azure Linux Agent konfiguriert, damit die Berechtigungen mithilfe des Tools sudo auf "root" erweitert werden können (Superuser-Konto). Wenn Sie über dieses Benutzerkonto angemeldet sind, können Sie mithilfe von "sudo command" Befehle als root ausführen. Optional können Sie über **sudo -s** eine Root-Shell abrufen.

Firewall-Konfiguration
----------------------

Azure bietet einen integrierten Paketfilter, der die Konnektivität auf die im Verwaltungsportal angegebenen Ports beschränkt. Standardmäßig ist SSH der einzige erlaubte Port. Sie können den Zugriff auf zusätzliche Ports auf Ihrem virtuellen Linux-Computer aktivieren, indem Sie im Verwaltungsportal Regeln hinzufügen.

Die Linux-Images in der Galerie aktivieren nicht die IPtables-Firewall in den virtuellen Linux-Computern. Bei Bedarf kann die IPtables-Firewall für die Bereitstellung zusätzlicher Funktionen konfiguriert werden.

Hostnamen-Änderungen
--------------------

Wenn Sie eine Instanz eines Linux-Images zum ersten Mal bereitstellen, müssen Sie einen Hostnamen für den virtuellen Computer bereitstellen. Wenn der virtuelle Computer läuft, wird dieser Hostname auf den Plattform-DNS-Servern veröffentlicht, so dass mehrere miteinander verbundene virtuelle Computer IP-Adresssuchen anhand von Hostnamen durchführen können. Wenn nach der Bereitstellung eines virtuellen Computers Hostnamen-Änderungen gewünscht werden, verwenden Sie den Befehl **hostname newname**. Der Azure Linux Agent enthält eine Funktion zur automatischen Erkennung dieser Namensänderung und zur entsprechenden Konfiguration des virtuellen Computers für eine Beibehaltung dieser Änderung und für ihre Veröffentlichung auf Plattform-DNS-Servern. Bitte lesen Sie die README-Datei für den Azure Linux Agent, um zusätzliche Details und Konfigurationsoptionen für diese Funktion zu erhalten.

Image-Erfassung virtueller Computer
-----------------------------------

Azure bietet die Möglichkeit, den Status eines vorhandenen virtuellen Computers in einem Image zu erfassen, das hinterher verwendet kann, um zusätzliche virtuelle Computerinstanzen bereitzustellen. Der Azure Linux Agent kann verwendet werden, um einige während des Bereitstellungsprozesses durchgeführten Anpassungen zurückzusetzen. Sie können die Schritte unten ausführen, um einen virtuellen Computer als Image zu erfassen.

1.  Führen Sie **waagent -deprovision** aus, um bei Bereitstellungen durchgeführte Anpassungen rückgängig zu machen. Oder optional **waagent -deprovision+user**, um das während der Bereitstellung angegebene Benutzerkonto und alle damit zusammenhängenden Daten zu löschen.

2.  Schalten Sie den virtuellen Computer mithilfe der Tools im Verwaltungsportal aus.

3.  Klicken Sie im Verwaltungsportal auf "Erfassen", oder verwenden Sie die Tools, um den virtuellen Computer als Image zu erfassen.

Anfügen von Datenträgern
------------------------

Dieser Vorgang wird Schritt für Schritt im Lernprogramm [Erstellen eines virtuellen Linux-Computers](../virtual-machine-from-gallery/) beschrieben.

