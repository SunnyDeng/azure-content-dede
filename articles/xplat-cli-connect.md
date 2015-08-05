<properties
	pageTitle="Anmelden von der Azure-Befehlszeilenschnittstelle (Azure-CLI) | Microsoft Azure"
	description="Herstellen einer Verbindung mit einem Azure-Abonnement von der Azure-Befehlszeilenschnittstelle (Azure-CLI)"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="danlep"/>

# Herstellen einer Verbindung mit einem Azure-Abonnement von der Azure Befehlszeilenschnittstelle (Azure-CLI)

Die Azure-Befehlszeilenschnittstelle beinhaltet eine Reihe von plattformübergreifenden Open Source-Befehlen für die Arbeit mit der Azure-Plattform. Dieses Dokument beschreibt, wie Sie mit der Azure-Befehlszeilenschnittstelle eine Verbindung mit Ihrem Azure-Abonnement herstellen. Installationsanweisungen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md).

<a id="configure"></a>
## Verbinden mit dem Azure-Abonnement

Die meisten von der Azure-CLI bereitgestellten Befehle erfordern eine Verbindung mit einem Azure-Konto. Sie können die plattformübergreifende Befehlszeilenschnittstelle auf zweierlei Weise für die Verwendung mit Ihrem Abonnement konfigurieren:

* Melden Sie sich mit einem Geschäfts- oder Schulkonto (auch Organisationskonto genannt) bei Azure an. Dabei werden die Anmeldeinformationen unter Verwendung von Azure Active Directory authentifiziert.

oder

* Laden Sie eine Datei mit Veröffentlichungseinstellunge herunter, und verwenden Sie diese. Damit wird ein Zertifikat installiert, das es Ihnen ermöglicht, solange Verwaltungsaufgaben auszuführen, wie das Abonnement und das Zertifikat gültig sind.

Weitere Informationen zur Authentifizierung und Abonnementverwaltung finden Sie unter ["Was ist der Unterschied zwischen kontobasierter Authentifizierung und zertifikatbasierter Authentifizierung"][authandsub].

Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion][free-trial].

> [AZURE.NOTE]Die flexibelsten und fortschrittlichsten Azure Services nutzen den Azure-Ressourcen-Manager oder den [ARM-Modus](xplat-cli-azure-resource-manager.md). Der ARM-Modus erfordert eine Verbindung mit Azure, die unter Verwendung eines Geschäfts- oder Schulkontos und dem unten beschriebenen Anmeldeverfahren hergestellt wurde.

### Verwenden der Anmeldemethode

Diese Anmeldemethode funktioniert nur mit einem Geschäfts- oder Schulkonto. Dieses Konto wird von der Organisation verwaltet und im Azure Active Directory der Organisation definiert ist.

> [AZURE.NOTE]Wenn Sie derzeit kein Geschäfts- oder Schulkonto besitzen und sich mit einem persönlichen Konto bei Ihrem Azure-Abonnement anmelden, können Sie mit den folgenden Schritten ganz leicht ein solches Konto erstellen.
>
> 1. Melden Sie sich beim [Azure-Portal][portal] an, und wählen Sie **Active Directory**.
>
> 2. Wenn kein Verzeichnis vorhanden ist, wählen Sie **Create your directory** (Verzeichnis erstellen) aus, und stellen Sie die geforderten Informationen bereit.
>
> 3. Wählen Sie Ihr Verzeichnis aus, und fügen Sie einen neuen Benutzer hinzu. Bei diesem neuen Benutzer handelt es sich um ein Geschäfts- oder Schulkonto.
>
>     Während der Erstellung des Benutzers erhalten Sie sowohl eine E-Mail-Adresse für den Benutzer als auch ein temporäres Kennwort. Speichern Sie diese Informationen, da sie später benötigt werden.
>
> 4. Wählen Sie im Azure-Portal **Einstellungen** und anschließend **Administratoren** aus. Wählen Sie **Hinzufügen** aus, und fügen Sie den neuen Benutzer als Co-Administrator hinzu. Dies ermöglicht es dem Geschäfts- oder Schulkonto, Ihr Azure-Abonnement zu verwalten.
>
> 5. Melden Sie sich schließlich vom Azure-Portal ab und dann unter Verwendung des neuen Geschäfts- oder Schulkontos erneut beim Azure-Portal an. Wenn Sie sich zu diesem Zeitpunkt zum ersten Mal mit diesem Konto anmelden, werden Sie aufgefordert, das Kennwort zu ändern.
>
> 6. Stellen Sie sicher, dass Ihre Abonnements angezeigt werden, wenn Sie sich unter dem Geschäfts- oder Schulkonto anmelden.
>
>Weitere Informationen zur Anmeldung bei Microsoft Azure mit einem Geschäfts- oder Schulkonto finden Sie unter [Anmelden bei Microsoft Azure als Organisation][signuporg]

Um sich über die Azure-Befehlszeilenschnittstelle mit einem Geschäfts- oder Schulkonto anzumelden, verwenden Sie den folgenden Befehl:

	azure login -u <username>

und geben Sie Ihr Kennwort ein, wenn Sie dazu aufgefordert werden.

> [AZURE.NOTE]Wenn Sie sich das erste Mal mit diesen Anmeldeinformationen anmelden, werden Sie dazu aufgefordert zu bestätigen, ob ein Authentifizierungstoken im Cache gespeichert werden soll. Diese Aufforderung wird auch angezeigt, wenn Sie zuvor den unten beschriebenen `azure logout`-Befehl verwendet haben. Um diese Eingabeaufforderung in Automatisierungsszenarien zu umgehen, geben Sie den `-q`-Parameter mit dem Befehl `azure login` an.

Verwenden Sie den folgenden Befehl, um sich abzumelden:

	azure logout -u <username>

> [AZURE.NOTE]Wenn die mit dem Konto verknüpften Abonnements nur über Active Directory authentifiziert wurden, werden die Abonnementinformationen durch die Abmeldung aus dem lokalen Profil gelöscht. Wurde allerdings auch eine Veröffentlichungseinstellungendatei für die Abonnements importiert, dann werden nur die auf Active Directory bezogenen Informationen durch die Abmeldung aus dem lokalen Profil gelöscht.

### Verwenden der Methode mit der Veröffentlichungseinstellungendatei

> [AZURE.NOTE]Wenn Sie mit dieser Methode eine Verbindung hergestellt haben, können Sie nur die Azure-Dienstverwaltungsbefehle (oder die ASM-Modus) verwenden.

Mit dem folgenden Befehl können Sie die Veröffentlichungseinstellungen für Ihr Konto herunterladen:

	azure account download

Daraufhin wird Ihr Standardwebbrowser geöffnet, und Sie werden aufgefordert, sich beim [Azure-Portal][portal] anzumelden. Nach der Anmeldung wird eine `.publishsettings`-Datei heruntergeladen. Notieren Sie sich den Speicherort der Datei.

> [AZURE.NOTE]Wenn Ihr Konto mit mehreren Azure Active Directory-Mandanten verknüpft ist, werden Sie möglicherweise aufgefordert, das Active Directory auszuwählen, für das eine Datei mit Veröffentlichungseinstellungen heruntergeladen werden soll.
>
> Das Active Directory, das Sie auf der Downloadseite oder im Azure-Portal ausgewählt haben, wird zur Standardeinstellung, die vom Portal und der Downloadseite verwendet wird. Nachdem eine Standardeinstellung festgelegt wurde, wird am oberen Rand der Downloadseite der Text „click here to return to the selection page“ (Klicken Sie hier, um zur Auswahlseite zurückzukehren) angezeigt. Klicken Sie auf den bereitgestellten Link, um zur Auswahlseite zurückzukehren.

Importieren Sie anschließend die `.publishsettings`-Datei, indem Sie den folgenden Befehl ausführen:

	azure account import <path to your .publishsettings file>

Nach dem Importieren der Veröffentlichungseinstellungen, sollten Sie die Datei `.publishsettings` löschen, denn diese wird nicht mehr für die Azure-CLI benötigt und stellt ein Sicherheitsrisiko dar, da sie für den Zugriff auf Ihr Abonnement verwendet werden kann.

> [AZURE.NOTE]Wenn Sie sich mit einem Geschäfts- oder Schulkonto anmelden oder Veröffentlichungseinstellungen importieren, werden die Daten für den Zugriff auf Ihr Azure-Abonnement in einem `.azure`-Verzeichnis in Ihrem `user`-Verzeichnis gespeichert. Ihr `user`-Verzeichnis wird durch das Betriebssystem geschützt; es wird jedoch empfohlen, zusätzliche Schritte zum Verschlüsseln Ihres `user`-Verzeichnisses auszuführen. Sie haben dazu folgende Möglichkeiten:
>
> * Windows: Ändern Sie die Verzeichniseigenschaften, oder verwenden Sie BitLocker.
> * Mac: Aktivieren Sie FileVault für das Verzeichnis.
> * Ubuntu: Verwenden Sie die Homeverzeichnis-Verschlüsselung. Andere Linux-Distributionen bieten äquivalente Funktionen.

### Mehrere Abonnements

Wenn Sie mehrere Azure-Abonnements besitzen, erhalten Sie durch das Herstellen einer Verbindung mit Azure Zugriff auf alle Abonnements, die mit Ihren Anmeldeinformationen verknüpft sind. Ein Abonnement wird als Standardabonnement ausgewählt und von der Azure-CLI zum Ausführen von Vorgängen verwendet. Mit dem Befehl `azure account list` können Sie die Abonnements anzeigen und herausfinden, welches Abonnement das Standardabonnement ist. Die Ausgabe dieses Befehls sieht etwa so aus:

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

In der oben angeführten Liste gibt die Spalte **Aktuell** das aktuelle Standardabonnement als Azure-sub-1 an. Um das Standardabonnement zu ändern, verwenden Sie den Befehl `azure account set`, und geben Sie das Abonnement an, das als Standard verwendet werden soll. Beispiel:

	azure account set Azure-sub-2

Damit wird das Standardabonnement in "Azure-sub-2" geändert.

> [AZURE.NOTE]Die Änderung des Standardabonnements tritt sofort in Kraft, und es ist eine globale Änderung. Für weitere Befehle der Azure-Befehlszeilenschnittstelle wird das neue Standardabonnement verwendet, unabhängig davon, ob die Befehle in derselben Befehlsschnittstelleninstanz oder einer anderen Instanz ausgeführt werden.

Wenn Sie in der Azure-Befehlszeilenschnittstelle ein anderes Abonnement als das Standardabonnement verwenden, aber das aktuelle Standardabonnement nicht ändern möchten, können Sie die Option `--subscription` und den Namen des Abonnements angeben, das Sie für den Vorgang verwenden möchten.

Nachdem Sie mit Ihrem Azure-Abonnement verbunden sind, können Sie beginnen, die Befehle der Azure-Befehlszeilenschnittstelle zu verwenden. Weitere Informationen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle](xplat-cli.md).

<a id="additional-resources"></a>
## Zusätzliche Ressourcen

* [Verwenden der Azure-Befehlszeilenschnittstelle mit Dienstverwaltungsbefehlen (oder ASM-Modus)][cliasm]

* [Verwenden der Azure-Befehlszeilenschnittstelle mit Ressourcenverwaltungsbefehlen (oder ARM-Modus)][cliarm]

* Wenn Sie weitere Informationen zur Azure-Befehlszeilenschnittstelle erhalten, Quellcode herunterladen, Probleme melden oder etwas zum Projekt beitragen möchten, dann besuchen Sie die Webseite [GitHub repository for the Azure CLI](https://github.com/azure/azure-xplat-cli).

* Wenn Sie Probleme bei der Verwendung der Azure-Befehlszeilenschnittstelle oder Azure haben, besuchen Sie die [Azure-Foren](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Weitere Informationen zu Azure finden Sie unter [http://azure.microsoft.com/](http://azure.microsoft.com).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=July15_HO4-->