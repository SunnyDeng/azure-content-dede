<properties
	pageTitle="Verwalten des Schlüsseltresors mit der CLI | Microsoft Azure"
	description="Verwenden Sie dieses Lernprogramm zum Automatisieren von häufigen Aufgaben in Schlüsseltresor mithilfe der CLI"
	services="key-vault"
	documentationCenter=""
	authors="BrucePerlerMS"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="bruceper"/>

# Verwalten von Schlüsseltresor mit CLI #
Azure-Schlüsseltresor ist in den meisten Regionen verfügbar. Weitere Informationen finden Sie auf der Seite [Preisübersicht für Schlüsseltresor](../../../../pricing/details/key-vault/).

## Einführung  
Verwenden Sie dieses Lernprogramm für den Einstieg in Azure-Schlüsseltresor, um einen geschützten Container (einen Tresor) in Azure zu erstellen, in dem Sie kryptografische und geheime Schlüssel in Azure speichern und verwalten. Das Programm leitet Sie durch den Prozess, die plattformübergreifende Azure-Befehlszeilenschnittstelle zu verwenden, um einen Tresor zu erstellen, der einen Schlüssel oder ein Kennwort enthält, den/das Sie anschließend mit einer Azure-Anwendung verwenden können. Anschließend wird gezeigt, wie eine Anwendung diesen Schlüssel bzw. das Kennwort verwenden kann.

**Geschätzter Zeitaufwand:** 20 Minuten

>[AZURE.NOTE]  Dieses Lernprogramm enthält keine Anweisungen zum Schreiben der Azure-Anwendung, die in einen der Schritte enthält, der zeigt, wie Sie eine Anwendung zum Verwenden eines Schlüssels oder geheimen Schlüssels im Schlüsseltresor autorisieren.
>
>Derzeit können Sie den Azure-Schlüsseltresor nicht im Azure-Portal konfigurieren. Verwenden Sie stattdessen die Anleitungen für die plattformübergreifende Befehlszeilenschnittstelle. Alternativ finden Sie Azure PowerShell-Anweisungen unter [Äquivalentes Lernprogramm ](key-vault-get-started.md).

Eine Übersicht über den Azure-Schlüsseltresor finden Sie unter [Was ist der Azure-Schlüsseltresor?](key-vault-whatis.md)

## Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Ein Abonnement für Microsoft Azure. Wenn Sie kein Abonnement haben, können Sie sich für eine [kostenlose Testversion](../../../pricing/free-trial) registrieren.
- Plattformübergreifende Azure-Befehlszeilenschnittstelle Version 0.9.1 oder aktueller. Informationen zum Installieren der aktuellen Version und Verbindungsaufbau mit Ihrem Azure-Abonnement, finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](xplat-cli-install.md).
- Eine Anwendung, die zur Verwendung des Schlüssels oder Kennworts konfiguriert wird, den bzw. das Sie in diesem Lernprogramm erstellen. Eine Beispielanwendung erhalten Sie im [Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=45343). Anweisungen finden Sie in der zugehörigen Readme-Datei.

## Hilfestellung für die plattformübergreifende Azure-Befehlszeilenschnittstelle

In diesem Lernprogramm wird davon ausgegangen, dass Sie mit der Befehlszeilenschnittstelle (Bash, Terminal, Eingabeaufforderung) vertraut sind.

Der Parameter --help oder -h kann angegeben werden, um Hilfetexte zu bestimmten Befehlen anzuzeigen. Stattdessen kann auch das Format azure help [Befehl] [Optionen] verwendet werden, um dieselben Informationen abzurufen. Beispielsweise geben die folgenden Befehle die gleichen Informationen zurück:

    azure account set --help

    azure account set -h

    azure help account set

Wenn Sie sich nicht ganz sicher sind, welche Parameter für einen Befehl angegeben werden müssen, rufen Sie mit --help, -h oder azure help [Befehl] die entsprechende Hilfe ab.

Lesen Sie bitte auch die folgenden Lernprogramme, um sich mit Azure-Ressourcen-Manager in der plattformübergreifenden Azure-Befehlszeilenschnittstelle vertraut zu machen:

- [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](xplat-cli-install.md)
- [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit Azure-Ressourcen-Manager](xplat-cli-azure-resource-manager.md)


## Verbindungsherstellung mit Ihren Abonnements

Verwenden Sie den folgenden Befehl, um sich mit einem Organisations-Konto anzumelden:

    azure login -u username -p password

oder wenn Sie sich durch interaktive Eingabe anmelden möchten.

    azure login

>[AZURE.NOTE]  Die Login-Methode funktioniert nur mit Organisationskonto. Ein Organisations-Konto ist ein Benutzer, der von Ihrer Organisation verwaltet wird und im Azure Active Directory-Mandant Ihrer Organisation definiert ist.


Wenn Sie derzeit kein Organisations-Konto besitzen und sich mit einem Microsoft-Konto bei Ihrem Azure-Abonnement anmelden, können Sei wie folgt mühelos ein Organisations-Konto erstellen:

1.	Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an und klicken Sie auf Active Directory.
2.	Wenn kein Verzeichnis vorhanden ist, wählen Sie Verzeichnis erstellen und geben Sie die angeforderten Informationen ein.
3.	Wählen Sie Ihr Verzeichnis aus, und fügen Sie einen neuen Benutzer hinzu. Dieser neue Benutzer ist ein Organisations-Konto. Während der Erstellung des Benutzers erhalten Sie sowohl eine E-Mail-Adresse für den Benutzer als auch ein temporäres Kennwort. Speichern Sie diese Informationen, da sie in einem anderen Schritt benötigt werden.
4.	Wählen Sie im Portal Einstellungen und anschließend Administratoren aus. Wählen Sie Hinzufügen, und fügen Sie den neuen Benutzer als Co-Administrator hinzu. Dies ermöglicht es dem Organisations-Konto, Ihr Azure-Abonnement zu verwalten.
5.	Melden Sie sich schließlich vom Azure-Portal ab und dann unter Verwendung des Organisations-Kontos erneut beim Azure-Portal an. Wenn Sie sich zu diesem Zeitpunkt zum ersten Mal mit diesem Konto anmelden, werden Sie aufgefordert, das Kennwort zu ändern.

Weitere Informationen zum Verwenden eines Organisations-Kontos in Microsoft Azure finden Sie unter [Anmelden bei Microsoft Azure als Organisation](sign-up-organization.md).

Wenn Sie über mehrere Abonnements verfügen und zur Verwendung für den Azure-Schlüsseltresor ein spezifisches Abonnement verwenden möchten, geben Sie den folgenden Befehl ein, um die Abonnements für Ihr Konto anzuzeigen:

    azure account list

Geben Sie Folgendes ein, um das zu verwendende Abonnement anzugeben:

    azure account set <subscription name>

Weitere Informationen über die Konfiguration der plattformübergreifenden Azure-Befehlszeilenschnittstellen finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle](xplat-cli-install.md).


## Wechseln zum Azure-Ressourcen-Manager

Der Schlüsseltresor erfordert Azure-Ressourcen-Manager, geben Sie deshalb den folgenden Befehl ein, um in den Azure-Ressourcen-Manager-Modus zu wechseln:

    azure config mode arm

## Erstellen einer neuen Ressourcengruppe

Wenn Sie den Azure-Ressourcen-Manager verwenden, werden alle zugehörigen Ressourcen in einer Ressourcengruppe erstellt. Im Rahmen dieses Lernprogramms erstellen wir eine neue Ressourcengruppe mit dem Namen "ContosoResourceGroup".

    azure group create 'ContosoResourceGroup' 'East Asia'

Der erste Parameter ist der Ressourcengruppenname und der zweite Parameter der Speicherort. Verwenden Sie für den Speicherort den Befehl `azure location list`, um zu ermitteln, wie Sie einen anderen Speicherort als den in diesem Beispiel verwendeten angeben können. Wenn Sie weitere Informationen benötigen, geben Sie Folgendes ein: `azure help location`



## Erstellen eines Schlüsseltresors

Verwenden Sie den Befehl `azure keyvault create`, um einen Schlüsseltresor zu erstellen. Das Skript verfügt über drei erforderliche Parameter: einen Ressourcengruppennamen, einen Schlüsseltresornamen und den geografischen Standort.

Wenn Sie beispielsweise den Tresornamen ContosoKeyVault, den Ressourcengruppennamen ContosoResourceGroup und den Speicherort East Asia verwenden, geben Sie Folgendes ein:

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

Die Ausgabe dieses Befehls zeigt die Eigenschaften des Schlüsseltresors, den Sie soeben erstellt haben. Die zwei wichtigsten Eigenschaften sind diese:

- **Name**: Im Beispiel ist dies "ContosoKeyVault". Sie verwenden diesen Namen für andere Schlüsseltresor-Cmdlets.
- **vaultUri**: In diesem Beispiel ist dies https://contosokeyvault.vault.azure.net. Anwendungen, die Ihren Tresor über die zugehörige REST-API nutzen, müssen diesen URI verwenden.

Ihr Azure-Konto ist jetzt autorisiert, Vorgänge in diesem Schlüsseltresor durchzuführen. Bisher sind Sie der einzige Benutzer mit dieser Berechtigung.


## Hinzufügen eines Schlüssels oder geheimen Schlüssels zum Schlüsseltresor

Wenn Sie mit dem Azure-Schlüsseltresor einen softwaregeschützten Schlüssel erstellen möchten, verwenden Sie hierzu den Befehl `azure key create` und geben Sie Folgendes ein:

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

Wenn Sie jedoch einen vorhandenen Schlüssel in einer PEM-Datei als lokale Datei in einer Datei namens softkey.pem gespeichert haben, die Sie in Azure-Schlüsseltresor hochladen möchten, geben Sie Folgendes ein, um den Schlüssel aus der PEM-Datei zu importieren, die den Schlüssel durch Software im Schlüsseltresor-Dienst schützt:

    azure keyvault key import --vaultName 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

Jetzt können Sie mittels seiner URI auf den Schlüssel verweisen, den Sie erstellt oder in Azure-Schlüsseltresor hochgeladen haben. Verwenden Sie ****https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey**, um immer die aktuelle Version zu erhalten, und verwenden Sie ****https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**, um diese bestimmte Version abzurufen.

Um einen geheimen Schlüssel zum Schlüsseltresor hinzuzufügen – in diesem Fall das Kennwort "SQLPassword" mit dem Wert "Pa$$w0rd" für den Azure-Schlüsseltresor – geben Sie Folgendes ein:

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

Jetzt können Sie mit dem zugehörigen URI auf das Kennwort verweisen, das Sie dem Azure-Schlüsseltresor hinzugefügt haben. Verwenden Sie ****https://ContosoVault.vault.azure.net/secrets/SQLPassword**, um immer die aktuelle Version zu erhalten, und verwenden Sie ****https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**, um diese bestimmte Version abzurufen.

Zeigen wir den Schlüssel oder geheimen Schlüssel an, den Sie soeben erstellt haben:

- Geben Sie Folgendes ein, um Ihren Schlüssel anzuzeigen: `azure keyvault key list --vault-name 'ContosoKeyVault'`
- Geben Sie Folgendes ein, um Ihren geheimen Schlüssel anzuzeigen: `azure keyvault secret list --vault-name 'ContosoKeyVault'`


## Registrieren einer Anwendung mit Azure Active Directory

Dieser Schritt wird üblicherweise durch einen Entwickler auf einem separaten Computer durchgeführt. Dieser Schritt ist nicht spezifisch für den Azure-Schlüsseltresor, wird der Vollständigkeit halber jedoch hier aufgeführt.


>[AZURE.IMPORTANT] Zum Abschließen dieses Lernprogramms müssen sich Ihr Konto, der Schlüsseltresor und die in diesem Schritt registrierte Anwendung im selben Azure-Verzeichnis befinden.

Anwendungen, die einen Schlüsseltresor verwenden, müssen sich mithilfe eines Azure Active Directory-Tokens authentifizieren. Hierzu muss der Besitzer der Anwendung die Anwendung zunächst in Azure Active Directory registrieren. Zum Abschluss der Registrierung erhält der Anwendungsbesitzer die folgenden Werte:


- Eine **Anwendungs-ID** (auch Client-ID genannt) und einen **Authentifizierungsschlüssel** (auch als gemeinsamer geheimer Schlüssel bezeichnet). Die Anwendung muss beide dieser Werte in Azure Active Directory vorlegen, um ein Token zu erhalten. Wie die Anwendung konfiguriert wird, um dies zu erreichen, richtet sich nach der Anwendung. Bei der Beispielanwendung für den Schlüsseltresor legt der Anwendungsbesitzer diese Werte in der Datei "app.config" fest.



So registrieren Sie die Anwendungen in Azure Active Directory

1. Melden Sie sich beim Azure-Portal an.
2. Klicken Sie auf der linken Seite auf **Active Directory**, und wählen Sie das Verzeichnis, in dem Sie Ihre Anwendung registrieren möchten. <br> <br> Hinweise: Sie müssen dasselbe Verzeichnis auswählen, das auch das Azure-Abonnement enthält, mit dem Sie Ihren Schlüsseltresor erstellt haben. Wenn Sie nicht wissen, welches Verzeichnis dies ist, klicken Sie auf **Einstellungen**, ermitteln Sie das Abonnement, mit dem Sie Ihren Schlüsseltresor erstellt haben, und notieren Sie sich den Namen des Verzeichnisses, der in der letzten Spalte angezeigt wird.

3. Klicken Sie auf **ANWENDUNGEN**. Wenn Ihrem Verzeichnis keine Apps hinzugefügt wurden, wird auf dieser Seite der Link **App hinzufügen** angezeigt. Klicken Sie auf den Link, oder klicken Sie alternativ auf der Befehlsleiste auf **HINZUFÜGEN**.
4.	Klicken Sie im Assistenten **ANWENDUNG HINZUFÜGEN** auf der Seite **Was möchten Sie tun?** auf **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen**.
5.	Geben Sie auf der Seite **Erzählen Sie uns von Ihrer App** einen Namen für Ihre Anwendung ein, und wählen Sie **WEBANWENDUNG UND/ODER WEB-API** (die Standardeinstellung). Klicken Sie auf das Symbol "Weiter".
6.	Geben Sie auf der Seite **App-Eigenschaften** die **ANMELDE-URL** und den **APP-ID-URI** für Ihre Webanwendung an. Wenn Ihre Anwendung über keine solchen Werte verfügt, können Sie in diesem Schritt Pseudowerte verwenden (Sie können beispielsweise in beide Felder den Wert http://test1.contoso.com eingeben). Es spielt keine Rolle, ob diese Websites vorhanden sind. Es ist nur wichtig, dass der App-ID-URI für jede Anwendung in Ihrem Verzeichnis eindeutig ist. Das Verzeichnis verwendet diese Zeichenfolge zur Identifizierung Ihrer App.
7.	Klicken Sie auf das Symbol "Abschließen", um Ihre Änderungen im Assistenten zu speichern.
8.	Klicken Sie auf der Seite "Schnellstart" auf **KONFIGURIEREN**.
9.	Führen Sie einen Bildlauf zum Abschnitt **Schlüssel** durch, wählen Sie die Dauer aus, und klicken Sie dann auf **SPEICHERN**. Die Seite wird aktualisiert und zeigt jetzt einen Schlüsselwert. Sie müssen Ihre Anwendung mit diesem Schlüsselwert und der **CLIENT-ID** konfigurieren. (Die Anweisungen für diese Konfigurationen sind anwendungsspezifisch.)
10.	Kopieren Sie den Client-ID-Wert von dieser Seite. Sie verwenden ihn im nächsten Schritt, um Berechtigungen für Ihren Tresor festzulegen.




## Autorisieren der Anwendung zum Verwenden des Schlüssels oder geheimen Schlüssels

Verwenden Sie den Befehl `azure keyvault set-policy`, um die Anwendung zum Zugreifen auf den Schlüssel oder geheimen Schlüssel im Tresor zu autorisieren.

Wenn Ihr Tresorname beispielsweise "ContosoKeyVault" lautet, die Anwendung, die Sie autorisieren möchten, über die Client-ID 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed verfügt, und Sie die Anwendung zum Entschlüsseln und Anmelden mit Schlüsseln in Ihrem Tresor autorisieren möchten, führen Sie Folgendes aus:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '["decrypt","sign"]'

Wenn Sie dieselbe Anwendung so autorisieren möchten, dass sie geheime Schlüssel im Tresor liest, führen Sie Folgendes aus:

	azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '["get"]'

## Verwenden eins Hardwaresicherheitsmodul (HSM) ##

Zur Steigerung der Sicherheit können Sie Schlüssel in HSMs importieren oder in diesen generieren. Diese Schlüssel verbleiben immer innerhalb der HSM-Grenzen. Die HSMs sind FIPS 140-2 Ebene 2 überprüft. Wenn diese Anforderung auf Sie nicht zutrifft, überspringen Sie diesen Abschnitt und wechseln Sie zu [Löschen des Schlüsseltresors und zugeordneter Schlüssel und geheimer Schlüssel](#delete-the-key-vault-and-associated-keys-and-secrets).

Um diese HSM-geschützten Schlüssel zu erstellen, müssen Sie über ein Tresorabonnement mit Unterstützung von HSM-geschützten Schlüsseln verfügen.

Wenn Sie den Tresor erstellen, fügen Sie den Parameter "SKU" hinzu:

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

Sie können diesem Tresor softwaregeschützte Schlüssel (wie weiter oben gezeigt) und HSM-geschützte Schlüssel hinzufügen. Legen Sie den Parameter "Destination" auf "HSM" fest, um einen HSM-geschützten Schlüssel zu erstellen:

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

Sie können mit dem folgenden Befehl einen Schlüssel aus einer PEM-Datei auf Ihren Computer importieren. Dieser Befehl importiert den Schlüssel in HSMs im Schlüsseltresordienst:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

Der nächste Befehl importiert ein BYOK-Paket (Bring Your Own Key). Auf diese Weise können Sie Ihren Schlüssel im lokalen HSM generieren und ihn in HSMs im Schlüsseltresordienst übertragen, ohne dass der Schlüssel die HSM-Grenzen verlässt:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

Ausführlichere Informationen zum Generieren dieses BYOK-Pakets finden Sie unter [Verwenden von HSM-geschützten Schlüsseln mit dem Azure-Schlüsseltresor](key-vault-hsm-protected-keys.md).


## Löschen vom Schlüsseltresor sowie von zugeordneten Schlüsseln und geheimen Schlüsseln

Wenn Sie den Schlüsseltresor und die darin enthaltenen Schlüssel und geheimen Schlüssel nicht länger benötigen, können Sie den Schlüsseltresor mit dem Azure-Schlüsseltresor-Löschbefehl löschen:

    azure keyvault delete --vault-name 'ContosoKeyVault'

Alternativ können Sie eine gesamte Azure-Ressourcengruppe löschen, die den Schlüsseltresor und alle weiteren Ressourcen enthält, die Sie in diese Gruppe eingeschlossen haben:

    azure group delete --name 'ContosoResourceGroup'


## Weitere plattformübergreifende Microsoft Azure-Befehlszeilenschnittstelle-Befehle

Die folgenden weiteren Befehle sind möglicherweise ebenfalls für das Verwalten eines Azure-Schlüsseltresors von Nutzen.

Dieser Befehl ruft eine tabellarische Anzeige aller Schlüssel und ausgewählten Eigenschaften ab:

    azure keyvault key list --vault-name 'ContosoKeyVault'

Dieser Befehl zeigt eine vollständige Liste der Eigenschaften für den angegebenen Schlüssel an:

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Dieser Befehl ruft eine tabellarische Anzeige der Namen aller geheimen Schlüssel und ausgewählten Eigenschaften ab:

    azure keyvault secret list --vault-name 'ContosoKeyVault'

Hier ist Beispiel, wie ein bestimmter Schlüssel entfernt werden kann:

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Hier ist ein Beispiel, wie ein bestimmten geheimer Schlüssel entfernt werden kann:

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## Nächste Schritte

Eine Referenz zur Programmierung finden Sie im [Entwicklerhandbuch für den Azure-Schlüsseltresor](key-vault-developers-guide.md).

<!---HONumber=AcomDC_0302_2016-->