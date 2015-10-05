<properties
	pageTitle="Anmelden bei Azure über die Befehlszeilenschnittstelle | Microsoft Azure"
	description="Herstellen einer Verbindung mit Ihrem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/18/2015"
	ms.author="danlep"/>

# Herstellen einer Verbindung mit einem Azure-Abonnement von der Azure Befehlszeilenschnittstelle (Azure-CLI)

Die Azure-Befehlszeilenschnittstelle beinhaltet eine Reihe von plattformübergreifenden Open Source-Befehlen für die Arbeit mit der Azure Platform. Dieser Artikel beschreibt, wie Sie mit der Azure-Befehlszeilenschnittstelle eine Verbindung mit Ihrem Azure-Abonnement herstellen, damit Sie alle Befehle der Befehlszeilenschnittstelle nutzen können. Wenn Sie die Befehlszeilenschnittstelle noch nicht installiert haben, finden Sie alle erforderlichen Informationen unter [Installieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md).

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Erstellen und Verwalten von Ressourcen mit dem Ressourcen-Manager- oder dem klassischen Bereitstellungsmodell.

Es gibt zwei Möglichkeiten, über die Azure-Befehlszeilenschnittstelle eine Verbindung mit Ihrem Abonnement herzustellen:

* **Anmelden an Azure mit einem Geschäfts- oder Schulkonto**: Hierbei wird Azure Active Directory zur Authentifizierung der Anmeldeinformationen verwendet. Ab Version 0.9.9 unterstützt die Befehlszeilenschnittstelle Organisationskonten, die für die mehrstufige Authentifizierung aktiviert sind. Nach der Anmeldung können Sie entweder Ressourcen-Manager-Befehle oder klassische Befehle (über die Dienstverwaltung) verwenden.

* **Herunterladen und Verwenden einer Datei mit Veröffentlichungseinstellungen**: Hiermit wird ein Zertifikat installiert, mit dem Sie Verwaltungsaufgaben ausführen können, solange das Abonnement und das Zertifikat gültig sind. Bei dieser Methode können Sie nur klassische Befehle (über die Dienstverwaltung) verwenden.

Weitere Informationen zur Authentifizierung und Abonnementverwaltung finden Sie unter [Was ist der Unterschied zwischen kontobasierter Authentifizierung und zertifikatbasierter Authentifizierung][authandsub].

Wenn Sie noch kein Azure-Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion][free-trial].


## Verwenden der Anmeldemethode

Die Anmeldemethode funktioniert nur mit einem Geschäfts- oder Schulkonto (auch *Organisationskonto* genannt). Dieses Konto wird von der Organisation verwaltet und ist im Azure Active Directory der Organisation definiert. Sie können ein [Organisationskonto erstellen](#create-an-organizational-account), falls Sie noch keins besitzen.


* Um sich über die Azure-Befehlszeilenschnittstelle mit einem Geschäfts- oder Schulkonto **anzumelden**, verwenden Sie den folgenden Befehl:

	```
	azure login -u <username>
	```

	Geben Sie Ihr Kennwort ein, wenn Sie dazu aufgefordert werden.

	Wenn Sie sich das erste Mal mit diesen Anmeldeinformationen anmelden, werden Sie dazu aufgefordert zu bestätigen, dass ein Authentifizierungstoken im Cache gespeichert werden soll. Diese Aufforderung wird auch angezeigt, wenn Sie zuvor den unten beschriebenen `azure logout`-Befehl verwendet haben. Um diese Eingabeaufforderung in Automatisierungsszenarien zu umgehen, führen Sie `azure login` mit dem `-q`-Parameter aus.

* Verwenden Sie den folgenden Befehl, um sich **abzumelden**:

	```
	azure logout -u <username>
	```

	Wenn die mit dem Konto verknüpften Abonnements nur über Active Directory authentifiziert wurden, werden bei der Abmeldung die Abonnementinformationen aus dem lokalen Profil gelöscht. Wenn jedoch auch eine Datei mit Veröffentlichungseinstellungen für die Abonnements importiert wurde, dann werden durch die Abmeldung nur die auf Active Directory bezogenen Informationen aus dem lokalen Profil gelöscht.

### Mehrstufige Authentifizierung
Ab Version 0.9.9 der Azure-Befehlszeilenschnittstelle können Sie sich mit einem Organisationskonto anmelden, das die mehrstufige Authentifizierung verwendet (hierbei erfolgt die Authentifizierung über ein Kennwort sowie mindestens eine weitere Verifizierungsmethode wie z. B. ein vertrauenswürdiges Gerät oder die Bereitstellung weiterer persönlicher Daten).

Nach der Ausführung von `azure login` mit dem Benutzernamen und Kennwort für das Konto gibt die Befehlszeilenschnittstelle die Adresse einer Webseite an, die Sie öffnen müssen. Gemäß den Anweisungen müssen Sie auf dieser Seite einen Code eingeben, um mit der Authentifizierung fortzufahren. Nachdem die Authentifizierungsrichtlinie erfüllt wurde, schließt die Azure-Befehlszeilenschnittstelle die Anmeldung ab.


### Erstellen eines Organisationskontos

Wenn Sie derzeit kein Geschäfts- oder Schulkonto besitzen und sich mit einem persönlichen Konto bei Ihrem Azure-Abonnement anmelden, können Sie mit den folgenden Schritten ganz leicht ein Organisationskonto erstellen.

1. Melden Sie sich beim [Azure-Portal][portal] an, und wählen Sie **Active Directory** aus.

2. Wenn kein Verzeichnis vorhanden ist, wählen Sie **Create your directory** (Verzeichnis erstellen) aus, und stellen Sie die geforderten Informationen bereit.

3. Wählen Sie Ihr Verzeichnis aus, und fügen Sie einen neuen Benutzer hinzu. Bei diesem neuen Benutzer handelt es sich um ein Geschäfts- oder Schulkonto.

	Während der Erstellung des Benutzers erhalten Sie sowohl eine E-Mail-Adresse für den Benutzer als auch ein temporäres Kennwort. Speichern Sie diese Informationen, da sie später benötigt werden.

4. Wählen Sie im Azure-Portal **Einstellungen** und anschließend **Administratoren** aus. Wählen Sie **Hinzufügen** aus, und fügen Sie den neuen Benutzer als Co-Administrator hinzu. Dies ermöglicht es dem Geschäfts- oder Schulkonto, Ihr Azure-Abonnement zu verwalten.

5. Melden Sie sich schließlich vom Azure-Portal ab und dann unter Verwendung des neuen Geschäfts- oder Schulkontos erneut beim Azure-Portal an. Wenn Sie sich zum ersten Mal mit diesem Konto anmelden, werden Sie aufgefordert, das Kennwort zu ändern.

	Stellen Sie sicher, dass Ihre Abonnements angezeigt werden, wenn Sie sich mit dem neuen Konto anmelden.

Weitere Informationen zur Anmeldung bei Microsoft Azure mit einem Geschäfts- oder Schulkonto finden Sie unter [Anmelden bei Microsoft Azure als Organisation][signuporg]

## Verwenden der Methode mit der Veröffentlichungseinstellungendatei

Wenn Sie nur die klassischen Befehle der Befehlszeilenschnittstelle (über die Dienstverwaltung) benötigen, können Sie die Verbindung mithilfe einer Datei mit Veröffentlichungseinstellungen herstellen.

* Mit dem folgenden Befehl können Sie die **Veröffentlichungseinstellungen für Ihr Konto herunterladen**:

	```
	azure account download
	```

	Ihr Standardbrowser wird geöffnet, und Sie werden aufgefordert, sich beim [Azure-Portal][portal] anzumelden. Nach der Anmeldung wird eine `.publishsettings`-Datei heruntergeladen. Notieren Sie sich den Speicherort der Datei.

	> [AZURE.NOTE]Wenn Ihr Konto mit mehreren Azure Active Directory-Mandanten verknüpft ist, werden Sie möglicherweise aufgefordert, das Active Directory auszuwählen, für das eine Datei mit Veröffentlichungseinstellungen heruntergeladen werden soll.
	>
	> Das Active Directory, das Sie auf der Downloadseite oder im Azure-Portal ausgewählt haben, wird zur Standardeinstellung, die vom Portal und der Downloadseite verwendet wird. Nachdem eine Standardeinstellung festgelegt wurde, wird am oberen Rand der Downloadseite der Text '__click here to return to the selection page__' (Klicken Sie hier, um zur Auswahlseite zurückzukehren) angezeigt. Klicken Sie auf den bereitgestellten Link, um zur Auswahlseite zurückzukehren.

* Führen Sie den folgenden Befehl aus, um die **Datei mit den Veröffentlichungseinstellungen zu importieren**:

	```
	azure account import <path to your .publishsettings file>
	```

	Nach dem Importieren der Veröffentlichungseinstellungen, sollten Sie die Datei `.publishsettings` löschen, denn diese wird nicht mehr für die Azure-CLI benötigt und stellt ein Sicherheitsrisiko dar, da sie für den Zugriff auf Ihr Abonnement verwendet werden kann.


## Mehrere Abonnements

Wenn Sie mehrere Azure-Abonnements besitzen, erhalten Sie durch das Herstellen einer Verbindung mit Azure Zugriff auf alle Abonnements, die mit Ihren Anmeldeinformationen verknüpft sind. Ein Abonnement wird als Standardabonnement ausgewählt und von der Azure-Befehlszeilenschnittstelle zum Ausführen von Vorgängen verwendet. Mit dem Befehl `azure account list` können Sie die Abonnements anzeigen und herausfinden, welches Abonnement das Standardabonnement ist. Die Ausgabe dieses Befehls sieht etwa so aus:

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

In der oben angeführten Liste gibt die Spalte **Aktuell** das aktuelle Standardabonnement als Azure-sub-1 an. Um das Standardabonnement zu ändern, verwenden Sie den Befehl `azure account set`, und geben Sie das Abonnement an, das als Standard verwendet werden soll. Beispiel:

	azure account set Azure-sub-2

Damit wird das Standardabonnement in "Azure-sub-2" geändert.

> [AZURE.NOTE]Die Änderung des Standardabonnements tritt sofort in Kraft, und es ist eine globale Änderung. Für weitere Befehle der Azure-Befehlszeilenschnittstelle wird das neue Standardabonnement verwendet, unabhängig davon, ob die Befehle in derselben oder einer anderen Befehlsschnittstelleninstanz ausgeführt werden.

Wenn Sie in der Azure-Befehlszeilenschnittstelle ein anderes Abonnement als das Standardabonnement verwenden, aber das aktuelle Standardabonnement nicht ändern möchten, können Sie die Option `--subscription` und den Namen des Abonnements angeben, das Sie für den Vorgang verwenden möchten.

Nachdem Sie mit Ihrem Azure-Abonnement verbunden sind, können Sie beginnen, die Befehle der Azure-Befehlszeilenschnittstelle zu verwenden. Weitere Informationen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle](xplat-cli.md).

## Speichern der Einstellungen für die Befehlszeilenschnittstelle

Unabhängig davon, ob Sie sich mit einem Geschäfts- oder Schulkonto anmelden oder Veröffentlichungseinstellungen importieren, werden Ihr Profil und die Protokolle für die Befehlszeilenschnittstelle in einem `.azure`-Verzeichnis gespeichert, das sich in Ihrem `user`-Verzeichnis befindet. Ihr `user`-Verzeichnis wird durch das Betriebssystem geschützt; es wird jedoch empfohlen, zusätzliche Schritte zum Verschlüsseln Ihres `user`-Verzeichnisses auszuführen. Sie haben dazu folgende Möglichkeiten:

* Windows: Ändern Sie die Verzeichniseigenschaften, oder verwenden Sie BitLocker.
* Mac: Aktivieren Sie FileVault für das Verzeichnis.
* Ubuntu: Verwenden Sie die Homeverzeichnis-Verschlüsselung. Andere Linux-Distributionen bieten ähnliche Funktionen.

## Zusätzliche Ressourcen

* [Verwenden der Azure-Befehlszeilenschnittstelle mit (klassischen) Dienstverwaltungsbefehlen][cliasm]

* [Verwenden der Azure-Befehlszeilenschnittstelle mit Ressourcen-Manager-Befehlen][cliarm]

* Wenn Sie weitere Informationen zur Azure-Befehlszeilenschnittstelle erhalten, den Quellcode herunterladen, Probleme melden oder etwas zum Projekt beitragen möchten, besuchen Sie die Webseite [GitHub repository for the Azure CLI](https://github.com/azure/azure-xplat-cli).

* Wenn Sie Probleme bei der Verwendung der Azure-Befehlszeilenschnittstelle oder Azure haben, besuchen Sie die [Azure-Foren](http://social.msdn.microsoft.com/Forums/windowsazure/home).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=Sept15_HO4-->