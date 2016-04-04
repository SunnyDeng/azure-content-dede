

Wenn Sie ein persönliches Azure-Konto erstellt oder ein persönliches MSDN-Abonnement besitzen und das Azure-Konto erstellt haben, um das MSDN Azure-Guthaben nutzen zu können, haben Sie zum Erstellen als Identität ein *Microsoft-Konto* verwendet. Viele hervorragende Funktionen von Azure – beispielsweise [Ressourcengruppenvorlagen](../resource-group-overview.md) – erfordern ein Arbeits- oder Schulkonto (eine Identität, die von Azure Active Directory verwaltet wird). Zum Erstellen eines neuen Geschäfts- oder Schulkontos können Sie die Anweisungen unten befolgen, da einer der größten Vorteile Ihres persönlichen Azure-Kontos glücklicherweise darin liegt, dass eine Azure Active Directory-Standarddomäne enthalten ist, mit der Sie ein neues Arbeits- oder Schulkonto für die Azure-Funktionen, die dies erfordern, erstellen können.

Aufgrund aktueller Änderungen können Sie Ihr Abonnement jedoch mit jeder Art von Azure-Konto verwalten, wenn Sie die [hier](../xplat-cli-connect.md) beschriebene interaktive Anmeldemethode über den Befehl `azure login` anwenden. Sie können diese Methode anwenden oder die nachfolgenden Anweisungen befolgen.

> [AZURE.NOTE] Wenn Sie einen Benutzernamen und ein Kennwort von einem Administrator erhalten haben, besteht eine hohe Wahrscheinlichkeit, dass Sie bereits eine Arbeits- oder Schul-ID (auch als *Organisations-ID* bezeichnet) besitzen. In diesem Fall können Sie sofort Ihr Azure-Konto verwenden, um auf entsprechende Azure-Ressourcen zuzugreifen. Wenn Sie feststellen, dass Sie diese Ressourcen nicht verwenden können, können Sie bei Bedarf zu diesem Artikel zurückkehren. Weitere Informationen finden Sie unter [Konten, die für die Anmeldung verwendet werden können](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts) und insbesondere [Die Beziehung zwischen einem Azure-Abonnement und Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir).

Die Schritte sind einfach. Sie müssen Ihre angemeldete Identität im klassischen Azure-Portal suchen, Ihre Azure Active Directory-Standarddomäne ermitteln und als Azure-Co-Administrator einen neuen Benutzer hinzufügen.

## Suchen des Standardverzeichnisses im klassischen Azure-Portal

Melden Sie sich als Erstes mit Ihrer persönlichen Microsoft-Kontoidentität beim [klassischen Azure-Portal](https://manage.windowsazure.com) an. Nachdem Sie angemeldet sind, scrollen Sie im blauen Bereich auf der linken Seite nach unten, und klicken Sie auf **ACTIVE DIRECTORY**.

![Azure Active Directory](./media/virtual-machines-common-create-aad-work-id/azureactivedirectorywidget.png)

Zunächst suchen Sie einige Informationen über Ihre Identität in Azure. Im Hauptbereich sehen Sie eine Anzeige ähnlich der folgenden, aus der Sie entnehmen können, dass Sie über ein Standardverzeichnis verfügen.

![](./media/virtual-machines-common-create-aad-work-id/defaultaadlisting.png)

Nun werden Sie einige weitere Informationen darüber herausfinden. Klicken Sie auf die Standardverzeichniszeile, um zu den Eigenschaften des Standardverzeichnisses zu gelangen.

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectorypage.png)

Um den Standarddomänennamen anzuzeigen, klicken Sie auf **Domänen**.

![](./media/virtual-machines-common-create-aad-work-id/domainclicktoseeyourdefaultdomain.png)

Hier können Sie sehen, dass Azure Active Directory beim Erstellen des Azure-Kontos eine persönliche Standarddomäne als Hash Ihrer persönlichen ID erstellt hat, die als Unterdomäne von onmicrosoft.com verwendet wird. Das ist die Domäne, der Sie nun einen neuen Benutzer hinzufügen.

## Erstellen neuer Benutzer in der Standarddomäne

Klicken Sie auf **BENUTZER**, und suchen Sie dann nach Ihrem einzigen persönlichen Konto. Daraufhin sollte in der Spalte **QUELLE** angezeigt werden, dass es sich um ein **Microsoft-Konto** handelt. Sie möchten einen Benutzer in Ihrer .onmicrosoft.com-Azure Active Directory-Standarddomäne erstellen.

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectoryuserslisting.png)

In den nächsten Schritten folgen Sie [diesen Anweisungen](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1), jedoch anhand eines konkreten Beispiels.

Klicken Sie unten auf der Seite auf **+BENUTZER HINZUFÜGEN**. Geben Sie im daraufhin angezeigten Dialogfeld den neuen Benutzernamen ein, und legen Sie für **Art des Benutzers** die Option **Neuer Benutzer in Ihrem Unternehmen** fest. In diesem Beispiel lautet der neue Benutzername `ahmet`. Stellen Sie sicher, dass Sie die weiter oben ermittelte Standarddomäne als Domäne für die E-Mail-Adresse von Ahmet auswählen. Klicken Sie anschließend auf den Pfeil zum Aufrufen der nächsten Seite.

![](./media/virtual-machines-common-create-aad-work-id/addingauserwithdirectorydropdown.png)

Geben Sie hier weitere Details für Ahmet ein, stellen Sie dabei jedoch sicher, den entsprechenden Wert für **ROLLE** auszuwählen. Es kann einfach **Global Admin** ausgewählt werden, damit alles in jedem Fall funktioniert, aber wenn Sie eine Rolle mit geringeren Berechtigungen verwenden können, ist das eine gute Idee. In diesem Beispiel wird die Rolle **Benutzer** verwendet. (Weitere Informationen finden Sie unter [Administratorberechtigungen nach Rolle](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1).) Aktivieren Sie die mehrstufige Authentifizierung nicht, es sei denn, Sie möchten sie für jeden Anmeldevorgang verwenden. Klicken Sie anschließend auf den Pfeil zum Aufrufen der nächsten Seite.

![](./media/virtual-machines-common-create-aad-work-id/userprofileuseradmin.png)

Klicken Sie auf die Schaltfläche **Erstellen**, um ein temporäres Kennwort für Ahmet zu generieren und anzuzeigen.

![](./media/virtual-machines-common-create-aad-work-id/gettemporarypasswordforuser.png)

Kopieren Sie die Benutzernamen-E-Mail-Adresse, oder verwenden Sie **KENNWORT IN E-MAIL SENDEN**. In beiden Fällen benötigen Sie die Informationen in Kürze für die Anmeldung.

![](./media/virtual-machines-common-create-aad-work-id/receivedtemporarypassworddialog.png)

Nun wird der neue Benutzer angezeigt, in diesem Fall **Ahmet der Entwickler**, der aus Azure Active Directory stammt. Sie haben die neue Arbeits- oder Schulidentität mit Azure Active Directory erstellt. Diese Identität verfügt jedoch noch nicht über Berechtigungen zum Verwenden von Azure-Ressourcen.

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectoryusersaftercreate.png)

Bei Verwendung von **KENNWORT IN E-MAIL SENDEN** wird die folgende Art der E-Mail-Adresse gesendet.

![](./media/virtual-machines-common-create-aad-work-id/emailreceivedfromnewusercreation.png)

## Hinzufügen von Azure-Co-Administrator-Rechten für Abonnements

Nun müssen Sie den neuen Benutzer als Co-Administrator Ihres Abonnements hinzufügen, damit der neue Benutzer sich am Verwaltungsportal anmelden kann. Klicken Sie hierzu im unteren linken Bereich auf **Einstellungen**.

![](./media/virtual-machines-common-create-aad-work-id/thesettingswidget.png)

Klicken Sie oben im Haupteinstellungsbereich auf **ADMINISTRATOREN** – es sollte nur Ihre persönliche Microsoft-Konto-Identität angezeigt werden. Klicken Sie unten auf der Seite auf **+HINZUFÜGEN**, um einen Co-Administrator anzugeben. Geben Sie hier die E-Mail-Adresse des neuen Benutzers ein, den Sie erstellt haben, einschließlich der Standarddomäne. Wie im folgenden Screenshot dargestellt, wird ein grünes Häkchen neben dem Benutzer im Standardverzeichnis angezeigt. Denken Sie daran, alle Abonnements auszuwählen, die dieser Benutzer verwalten können soll.

![](./media/virtual-machines-common-create-aad-work-id/addingnewuserascoadmin.png)

Wenn Sie fertig sind, sollten nun zwei Benutzer angezeigt werden, einschließlich der neuen Co-Administrator-Identität. Melden Sie sich im Portal ab.

![](./media/virtual-machines-common-create-aad-work-id/newuseraddedascoadministrator.png)

## Anmelden und Ändern des Kennworts des neuen Benutzers

Melden Sie sich als der neue Benutzer an, den Sie erstellt haben.

![](./media/virtual-machines-common-create-aad-work-id/signinginwithnewuser.png)

Sie werden sofort aufgefordert, ein neues Kennwort zu erstellen.

![](./media/virtual-machines-common-create-aad-work-id/mustupdateyourpassword.png)

Der Erfolg wird folgendermaßen angezeigt.

![](./media/virtual-machines-common-create-aad-work-id/successtourdialog.png)


## Nächste Schritte

Beispielsweise können Sie nun mit Ihrer neuen Azure Active Directory-Identität [Azure-Ressourcengruppenvorlagen](../xplat-cli-azure-resource-manager.md) verwenden.

    azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@aztrainpassxxxxxoutlook.onmicrosoft.com
    Password: *********
    /info:    Added subscription Azure Pass
    info:    Setting subscription Azure Pass as default
    +
    info:    login command OK
    ralph@local:~$ azure config mode arm
    info:    New mode is arm
    ralph@local:~$ azure group list
    info:    Executing command group list
    + Listing resource groups
    info:    No matched resource groups were found
    info:    group list command OK
    ralph@local:~$ azure group create newgroup westus
    info:    Executing command group create
    + Getting resource group newgroup
    + Creating resource group newgroup
    info:    Created resource group newgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/newgroup
    data:    Name:                newgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK

<!---HONumber=AcomDC_0323_2016-->