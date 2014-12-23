<properties urlDisplayName="Convert a WordPress Site to a Multisite" pageTitle="Konvertieren einer WordPress-Website in eine Multisite" metaKeywords="WordPress, Multisite" description="Learn how to take an existing WordPress website created through the gallery in Azure and convert it to WordPress Multisite" metaCanonical="" services="web-sites" documentationCenter="PHP" title="Convert a WordPress Site to a Multisite" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/28/2014" ms.author="tomfitz" />





# Konvertieren einer WordPress-Website in eine Multisite

*Von [Ben Lobaugh][ben-lobaugh], [Microsoft Open Technologies Inc.][ms-open-tech]*

In diesem Lernprogramm erfahren Sie, wie Sie eine vorhandene WordPress-Website, die über die Galerie in Azure erstellt wurde, in eine WordPress Multisite-Installation konvertieren. Darüber hinaus erfahren Sie, wie Sie den einzelnen Unterwebsites eine benutzerdefinierte Domäne in Ihrer Installation zuweisen.

Es wird davon ausgegangen, dass Sie WordPress installiert haben. Wenn dies nicht der Fall ist, folgen Sie der Anleitung unter [Erstellen einer WordPress-Website über die Galerie in Azure][website-from-gallery].

Das Konvertieren einer vorhandenen WordPress-Einzelsite in eine Multisite ist im Allgemeinen sehr einfach. Viele der hier aufgeführten Schritte stammen direkt von der Seite [Create A Network][wordpress-codex-create-a-network] (Erstellen eines Netzwerks, in englischer Sprache) auf [WordPress Codex](http://codex.wordpress.org) (WordPress-Codex, in englischer Sprache).

Lassen Sie uns anfangen.

## Zulassen von Multisite

Sie müssen Multisite zunächst über die Datei"wp-config.php" mit der Konstanten **WP\_ALLOW\_MULTISITE** aktivieren. Es gibt zwei Möglichkeiten zum Bearbeiten von Websitedateien: über FTP und über Git. Wenn Sie sich mit der Einrichtung dieser Methoden nicht auskennen, finden Sie weitere Informationen in den folgenden Lernprogrammen:

* [PHP-Website mit MySQL und FTP][website-w-mysql-and-ftp-ftp-setup]

* [PHP-Website mit MySQL und Git][website-w-mysql-and-git-git-setup]

Öffnen Sie die Datei "wp-config.php" mit dem gewünschten Texteditor, und fügen Sie oberhalb der Zeile "/* That's all, stop editing! Happy blogging. */" Folgendes hinzu:

	/* Multisite */

	define( 'WP_ALLOW_MULTISITE', true );

Vergessen Sie nicht, die Datei zu speichern und erneut auf den Server zu laden!

## Netzwerkeinrichtung

Melden Sie sich am *wp-admin*-Bereich Ihrer Website an. Im Menü **Tools** sollte ein neues Element mit dem Namen **Network Setup** angezeigt werden. Klicken Sie auf **Network Setup**, und geben Sie die Details zu Ihrem Netzwerk ein.

![Network Setup Screen][wordpress-network-setup]

In diesem Lernprogramm wird das Siteschema *Sub-directories* verwendet, da dies immer funktionieren sollte. Später in diesem Lernprogramm richten wir benutzerdefinierte Domänen für die einzelnen Untersites ein. Es sollte jedoch möglich sein, eine Unterdomäneninstallation einzurichten, wenn Sie eine Domäne über das Portal zuweisen und Platzhalter-DNS ordnungsgemäß einrichten.

Weitere Informationen zu Unterdomäneneinrichtungen im Vergleich zu Unterverzeichniseinrichtungen finden Sie im Artikel [Types of multisite network][wordpress-codex-types-of-networks] (Arten von Multisite-Netzwerken, in englischer Sprache) im WordPress-Codex.

## Aktivieren des Netzwerks

Das Netzwerk ist jetzt in der Datenbank konfiguriert. Es muss noch ein weiterer Schritt durchgeführt werden, um die Netzwerkfunktionalität zu aktivieren. Schließen Sie die "wp-config.php"-Einstellungen ab, und stellen Sie sicher, dass "web.config" die einzelen Websites ordnungsgemäß weiterleitet.


Nach dem Anklicken der Schaltfläche **Install** auf der Seite *Network Setup* versucht WordPress, die Dateien "wp-config.php" und "web.config" zu aktualisieren. Sie sollten jedoch stets überprüfen, ob die Dateien erfolgreich aktualisiert wurden. Wenn nicht, werden in diesem Bildschirm die erforderlichen Aktualisierungen angezeigt. Bearbeiten und speichern Sie die Dateien.


Nachdem Sie diese Aktualisierungen durchgeführt haben, müssen Sie sich abmelden und erneut beim wp-admin-Dashboard anmelden.

Auf der Verwaltungsleiste sollte sich nun ein weiteres Menü mit der Bezeichnung **My Sites** befinden. Mit diesem Menü können Sie das neue Netzwerk über das **Network Admin**-Dashboard steuern.

# Hinzufügen von benutzerdefinierten Domänen

Mit dem [WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping]-Plugin (WordPress MU-Domänenzuordnung, in englischer Sprache) können Sie ganz einfach benutzerdefinierte Domänen zu einer beliebigen Website in Ihrem Netzwerk hinzufügen. Damit das Plug-In ordnungsgemäß funktioniert, müssen Sie im Portal sowie bei der Domänenregistrierungsstelle einige zusätzliche Einrichtungen vornehmen.

## Aktivieren der Domänenzuordnung zur Website

Im standardmäßigen, kostenlosen Websitemodus wird das Hinzufügen von benutzerdefinierten Domänen zu Azure-Websites nicht unterstützt. Sie müssen in den Modus "Freigegeben" oder "Standard" wechseln. Gehen Sie dazu folgendermaßen vor:

* Melden Sie sich beim Azure-Portal an, und suchen Sie Ihre Website. 
* Klicken Sie im Hauptbereich auf die Registerkarte **Skalieren**.
* Wählen Sie unter **Allgemein** entweder *SHARED* oder *STANDARD*
* Klicken Sie auf **Speichern**.

Es wird möglicherweise eine Meldung angezeigt, in der Sie aufgefordert werden, die Änderung zu bestätigen und anzuerkennen, dass die Website jetzt Kosten verursachen kann, je nach Nutzung und den anderen Konfigurationen, die Sie festlegen.

Die Verarbeitung der neuen Einstellungen dauert einige Sekunden, sodass Sie nun mit dem Einrichten der Domäne beginnen können.

## Überprüfen der Domäne

Bevor Sie in Azure-Websites der Website eine Domäne zuordnen können, müssen Sie zunächst sicherstellen, dass Sie über die Autorisierung zum Zuordnen der Domäne verfügen. Hierzu müssen Sie dem DNS-Eintrag einen neuen CNAME-Datensatz hinzufügen.

* Melden Sie sich beim DNS-Manager Ihrer Domäne an.
* Erstellen Sie einen neuen CNAME *awverify*.
* Zeigen Sie mit *awverify* auf *awverify.IHRE_DOMÄNE.azurewebsites.net*

Es kann einige Zeit dauern, bis die DNS-Änderungen vollständig übernommen werden. Wenn die folgenden Schritte daher nicht sofort funktionieren, warten Sie eine Weile, und versuchen Sie es erneut.

## Hinzufügen der Domäne zur Website

Kehren Sie über das Azure-Portal zu Ihrer Website zurück, und klicken Sie dieses Mal auf die Registerkarte **KONFIGURIEREN**. Die Schaltfläche **DOMÄNEN VERWALTEN** müsste angezeigt werden. Klicken Sie auf diese Schaltfläche.

Das Dialogfeld *Benutzerdefinierte Domänen verwalten* wird aufgerufen. Hier geben Sie alle Domänen ein, die Sie Ihrer Website zuordnen möchten. Wenn hier eine Domäne nicht aufgeführt wird, kann sie nicht in WordPress zugeordnet werden, unabhängig von der Einrichtung des Domänen-DNS.

![Manage custom domains dialog][wordpress-manage-domains]

Nachdem Sie die Domäne in das Textfeld eingegeben haben, überprüft Azure den CNAME-Datensatz *awverify*, den Sie zuvor erstellt haben. Wenn der DNS nicht vollständig ausgefüllt wurde, wird eine rote Markierung angezeigt. Wenn der Vorgang erfolgreich abgeschlossen wurde, wird ein grünes Häkchen angezeigt. 

Notieren Sie sich die IP-Adresse, die unten im Dialogfeld aufgeführt wird. Sie benötigen diese IP-Adresse zum Einrichten des A-Datensatzes für Ihre Domäne.

## Einrichten des A-Datensatzes der Domäne

Wenn die anderen Schritte erfolgreich durchgeführt werden konnten, können Sie Ihrer Azure-Website nun über einen DNS-A-Datensatz die Domäne zuordnen. 

Hierbei ist es wichtig zu beachten, dass Azure-Websites sowohl CNAME- als auch A-Datensätze akzeptieren, wobei Sie jedoch einen A-Datensatz verwenden *müssen*. Ein CNAME kann nicht an einen anderen CNAME weitergeleitet werden, den Azure für Sie mit "IHRE_DOMÄNE.azurewebsites.net" erstellt hat.

Kehren Sie mithilfe der IP-Adresse aus dem vorhergehenden Schritt zu Ihrem DNS-Manager zurück, und richten Sie den A-Datensatz so ein, das er auf diese IP verweist.


## Installieren und Einrichten des Plug-Ins

WordPress Multisite verfügt derzeit nicht über eine integrierte Methode zum Zuordnen von benutzerdefinierten Domänen. Es steht jedoch das Plug-In [WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] (WordPress MU-Domänenzuordnung, in englischer Sprache) zur Verfügung, mit dem diese Funktionalität hinzugefügt werden kann. Melden Sie sich im Bereich "Network Admin" der Website an, und installieren Sie das Plug-In **WordPress MU Domain Mapping**.

Nachdem Sie das Plug-In installiert und aktiviert haben, rufen Sie **Settings** > **Domain Mapping** auf, um das Plug-In zu konfigurieren. Geben Sie im ersten Textfeld, *Server IP Address* +, die IP-Adresse ein, die Sie zum Einrichten das A-Datensatzes für die Domäne verwendet haben. Legen Sie unter *Domain Options* die gewünschten Optionen für die Domäne fest (die Standardwerte sind in der Regel ausreichend), und klicken Sie auf **Save**.

## Zuordnen der Domäne

Rufen Sie das **Dashboard** der Website auf, der Sie die Domäne zuordnen möchten. Klicken Sie auf **Tools** > **Domain Mapping**, und geben Sie im Textfeld den Namen der neuen Domäne ein. Klicken Sie dann auf **Add**.

Die neue Domäne wird standardmäßig neu in die automatisch generierte Standortdomäne geschrieben. Wenn der gesamte Datenverkehr an die neue Domäne gesendet werden soll, aktivieren Sie vor dem Speichern das Kontrollkästchen *Primary domain* for this blog. Sie können einer Website eine unbegrenzte Anzahl von Domänen hinzufügen, wobei jedoch nur eine die primäre sein kann.

## Noch einmal

In Azure-Websites haben Sie die Möglichkeit, einer Website eine unbegrenzte Anzahl von Domänen hinzuzufügen. Um eine weitere Domäne hinzuzufügen, müssen Sie die in den Abschnitten **Überprüfen der Domäne** und **	Einrichten des A-Datensatzes der Domäne** aufgeführten Schritte für jede Domäne erneut durchführen.	

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/en-us/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/en-us/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/en-us/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

