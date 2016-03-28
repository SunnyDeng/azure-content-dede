<properties 
   pageTitle="Schützen von Daten, die im Azure Data Lake-Speicher gespeichert sind | Azure" 
   description="Informationen zum Schützen von Daten im Azure Data Lake-Speicher mithilfe von Gruppen und Zugriffssteuerungslisten" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="03/15/2016"
   ms.author="nitinme"/>

# Schützen von Daten, die im Azure Data Lake-Speicher gespeichert sind

Das Schützen von Daten im Azure Data Lake-Speicher ist ein Ansatz, der drei Schritte umfasst.

1. Beginnen Sie, indem Sie in Azure Active Directory (AAD) Sicherheitsgruppen erstellen. Diese Sicherheitsgruppen werden verwendet, um die rollenbasierte Zugriffssteuerung im Azure-Portal zu implementieren. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung in Microsoft Azure](../active-directory/role-based-access-control-configure.md).

2. Weisen Sie die AAD-Sicherheitsgruppen dem Azure Data Lake-Speicherkonto zu. Hiermit werden der Zugriff auf das Data Lake-Speicherkonto über das Portal und die Verwaltungsvorgänge über das Portal oder APIs gesteuert.

3. Weisen Sie die AAD-Sicherheitsgruppen als Zugriffssteuerungslisten im Data Lake-Speicher-Dateisystem zu.

Dieser Artikel enthält eine Anleitung zur Verwendung des Azure-Portals für die oben genannten Aufgaben.

## Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
- **Ein Azure Data Lake-Speicherkonto**. Eine Anleitung zur Erstellung finden Sie unter [Erste Schritte mit dem Azure Data Lake-Speicher](data-lake-store-get-started-portal.md).

## Erstellen von Sicherheitsgruppen in Azure Active Directory

Eine Anleitung zum Erstellen von AAD-Sicherheitsgruppen und zum Hinzufügen von Benutzern zur Gruppe finden Sie unter [Verwalten von Sicherheitsgruppen in Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

## Zuweisen von Benutzern oder Sicherheitsgruppen zu Azure Data Lake-Speicherkonten

Wenn Sie Benutzer oder Sicherheitsgruppen Azure Data Lake-Speicherkonten zuweisen, steuern Sie den Zugriff auf die Verwaltungsvorgänge des Kontos, indem Sie das Azure-Portal und die Azure-Ressourcen-Manager-APIs verwenden.

1. Öffnen Sie ein Azure Data Lake-Speicherkonto. Klicken Sie im linken Bereich auf **Durchsuchen** und dann auf **Data Lake-Speicher**. Klicken Sie anschließend auf dem Blatt „Data Lake-Speicher“ auf den Namen des Kontos, dem Sie einen Benutzer oder eine Sicherheitsgruppe zuordnen möchten.

2. Klicken Sie auf dem Blatt Ihres Data Lake-Speicherkontos auf das Benutzersymbol.

	![Sicherheitsgruppe dem Azure Data Lake-Speicherkonto zuweisen](./media/data-lake-store-secure-data/adl.select.user.icon.png "Sicherheitsgruppe dem Azure Data Lake-Speicherkonto zuweisen")

3. Auf dem Blatt **Benutzer** wird standardmäßig die Gruppe **Abonnement-Administratoren** als Besitzer aufgeführt.

	![Benutzer und Rollen hinzufügen](./media/data-lake-store-secure-data/adl.add.group.roles.png "Benutzer und Rollen hinzufügen")
 
	Es gibt zwei Wege zum Hinzufügen einer Gruppe und Zuweisen der relevanten Rollen:

	* Hinzufügen eines Benutzers/einer Gruppe zum Konto und anschließendes Zuweisen einer Rolle oder
	* Hinzufügen einer Rolle und anschließendes Zuweisen von Benutzern/Gruppen zur Rolle

	In diesem Abschnitt untersuchen wir den ersten Ansatz, also das Hinzufügen einer Gruppe und anschließende Zuweisen von Rollen. Sie können ähnliche Schritte ausführen, um zuerst eine Rolle auszuwählen und dieser Rolle dann Gruppen zuzuweisen.
	
4. Klicken Sie auf dem Blatt **Benutzer** auf **Hinzufügen**, um das Blatt **Zugriff hinzufügen** zu öffnen. Klicken Sie auf dem Blatt **Zugriff hinzufügen** auf **Rolle auswählen**, und wählen Sie dann eine Rolle für den Benutzer bzw. die Gruppe aus.

	 ![Rolle für den Benutzer hinzufügen](./media/data-lake-store-secure-data/adl.add.user.1.png "Rolle für den Benutzer hinzufügen")

	Mit der Rolle **Besitzer** und **Mitwirkender** wird Zugriff auf viele verschiedene Verwaltungsfunktionen des Data Lake-Kontos gewährt. Für Benutzer, die unter Data Lake mit Daten interagieren, können Sie diese Rollen der Rolle **Leser** hinzufügen. Der Umfang dieser Rollen ist auf die Verwaltungsvorgänge beschränkt, die sich auf das Azure Data Lake-Speicherkonto beziehen.

	Für Datenvorgänge wird mithilfe von individuellen Dateisystemberechtigungen definiert, was Benutzer tun können. Aus diesem Grund kann ein Benutzer mit der Rolle „Leser“ nur Verwaltungseinstellungen anzeigen, die dem Konto zugeordnet sind. Potenziell kann er aber basierend auf den zugewiesenen Dateisystemberechtigungen Daten lesen und schreiben. Die Dateisystemberechtigungen des Data Lake-Speichers sind unter [Zuweisen von Sicherheitsgruppen als Zugriffssteuerungslisten zum Azure Data Lake-Speicher-Dateisystem](#filepermissions) beschrieben.



5. Klicken Sie auf dem Blatt **Zugriff hinzufügen** auf **Benutzer hinzufügen**, um das Blatt **Benutzer hinzufügen** zu öffnen. Suchen Sie auf diesem Blatt nach der Sicherheitsgruppe, die Sie in Azure Active Directory bereits erstellt haben. Wenn Sie über viele Gruppen verfügen, in denen Sie suchen können, können Sie das Textfeld oben zum Filtern nach dem Gruppennamen verwenden. Klicken Sie auf **Auswählen**.

	![Sicherheitsgruppe hinzufügen](./media/data-lake-store-secure-data/adl.add.user.2.png "Sicherheitsgruppe hinzufügen")

	Falls Sie eine nicht aufgeführte Gruppe bzw. einen Benutzer hinzufügen möchten, können Sie über das Symbol **Einladen** eine Einladung erstellen und die E-Mail-Adresse für den Benutzer bzw. die Gruppe angeben.

6. Klicken Sie auf **OK**. Die Sicherheitsgruppe sollte wie unten dargestellt hinzugefügt werden.

	![Hinzugefügte Sicherheitsgruppe](./media/data-lake-store-secure-data/adl.add.user.3.png "Hinzugefügte Sicherheitsgruppe")

7. Der Benutzer bzw. die Sicherheitsgruppe verfügt jetzt über Zugriff auf den Azure Data Lake-Speicher. Wenn Sie bestimmten Benutzern Zugriff gewähren möchten, können Sie sie der Sicherheitsgruppe hinzufügen. Wenn Sie den Zugriff für einen Benutzer widerrufen möchten, können Sie sie auch aus der Sicherheitsgruppe entfernen. Sie können einem Konto auch mehrere Sicherheitsgruppen zuweisen.

## <a name="filepermissions"></a>Zuweisen von Benutzern oder Sicherheitsgruppen als Zugriffssteuerungslisten zum Azure Data Lake-Speicher-Dateisystem

Indem Sie dem Azure Data Lake-Dateisystem Benutzer oder Sicherheitsgruppen zuweisen, legen Sie die Zugriffssteuerung für die im Azure Data Lake-Speicher gespeicherten Daten fest. In der aktuellen Version können Sie Zugriffssteuerungslisten nur für den Stammknoten Ihres Dateisystems festlegen.

1. Klicken Sie auf dem Blatt Ihres Data Lake-Speicherkontos auf **Daten-Explorer**.

	![Verzeichnisse in Data Lake-Speicherkonto erstellen](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Verzeichnisse in Data Lake-Konto erstellen")

2. Klicken Sie auf dem Blatt **Daten-Explorer** auf den Stamm Ihres Kontos und dann auf dem Kontoblatt auf das Symbol **Zugriff**.

	![Zugriffssteuerungslisten für Data Lake-Dateisystem festlegen](./media/data-lake-store-secure-data/adl.acl.1.png "Zugriffssteuerungslisten für Data Lake-Dateisystem festlegen")

3. Auf dem Blatt **Zugriff** sind der Standardzugriff und der benutzerdefinierte Zugriff aufgeführt, die dem Stamm bereits zugewiesen wurden. Klicken Sie auf das Symbol **Hinzufügen**, um Zugriffssteuerungslisten auf benutzerdefinierter Ebene hinzuzufügen.

	![Standardzugriff und benutzerdefinierten Zugriff auflisten](./media/data-lake-store-secure-data/adl.acl.2.png "Standardzugriff und benutzerdefinierten Zugriff auflisten")

	* Der Standardzugriff ist ein Zugriff im UNIX-Stil, bei dem Sie Lesen, Schreiben und Ausführen (read, write, execute (rwx)) für drei eigene Benutzerklassen angeben: Besitzer, Gruppe und Sonstige.
	* Der benutzerdefinierte Zugriff entspricht den POSIX-Zugriffssteuerungslisten, bei denen Sie Berechtigungen für speziell benannte Benutzer oder Gruppen festlegen können, und nicht nur den Besitzer oder die Gruppe der Datei.
	
	Weitere Informationen finden Sie unter [HDFS-Zugriffssteuerungslisten](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists) (in englischer Sprache).

4. Klicken Sie auf das Symbol **Hinzufügen**, um das Blatt **Benutzerdefinierten Zugriff hinzufügen** zu öffnen. Klicken Sie auf diesem Blatt auf **Benutzer oder Gruppe auswählen**, und suchen Sie dann auf dem Blatt **Benutzer oder Gruppe auswählen** nach der Sicherheitsgruppe, die Sie in Azure Active Directory bereits erstellt haben. Wenn Sie über viele Gruppen verfügen, in denen Sie suchen können, können Sie das Textfeld oben zum Filtern nach dem Gruppennamen verwenden. Klicken Sie auf die Gruppe, die Sie hinzufügen möchten, und klicken Sie dann auf **Auswählen**.

	![Gruppe hinzufügen](./media/data-lake-store-secure-data/adl.acl.3.png "Gruppe hinzufügen")

5. Klicken Sie auf **Berechtigungen auswählen**, wählen Sie die Berechtigungen aus, die Sie dieser Gruppe zuweisen möchten, und klicken Sie dann auf **OK**.

	![Berechtigungen für Gruppe zuweisen](./media/data-lake-store-secure-data/adl.acl.4.png "Berechtigungen für Gruppe zuweisen")


	>[AZURE.NOTE] Die Berechtigung zum Ausführen (Execute) ist für die Aufzählung von Verzeichnissen erforderlich und wird häufig benötigt, wenn für einen Benutzer oder eine Gruppe der schreibgeschützte Zugriff auf Daten bereitgestellt wird.


6. Klicken Sie auf dem Blatt **Benutzerdefinierten Zugriff hinzufügen** auf **OK**. Die neu hinzugefügte Gruppe mit den zugeordneten Berechtigungen wird jetzt auf dem Blatt **Zugriff** aufgelistet.

	![Berechtigungen für Gruppe zuweisen](./media/data-lake-store-secure-data/adl.acl.5.png "Berechtigungen für Gruppe zuweisen")

	> [AZURE.IMPORTANT] In der aktuellen Version können unter **Benutzerdefinierter Zugriff** nur neun Einträge vorhanden sein. Wenn Sie mehr als neun Benutzer hinzufügen möchten, sollten Sie Sicherheitsgruppen erstellen, die Benutzer den Sicherheitsgruppen hinzufügen und für das Data Lake-Speicherkonto den Zugriff auf diese Sicherheitsgruppen gewähren.

7. Falls erforderlich, können Sie die Zugriffsberechtigungen auch ändern, nachdem Sie die Gruppe hinzugefügt haben. Deaktivieren oder aktivieren Sie das Kontrollkästchen für jeden Berechtigungstyp (Lesen, Schreiben, Ausführen) in Abhängigkeit davon, ob Sie die Berechtigung für die Sicherheitsgruppe entfernen oder zuweisen möchten. Klicken Sie auf **Speichern**, um die Änderungen zu speichern, oder auf **Verwerfen**, um die Änderungen rückgängig zu machen.

## Entfernen von Sicherheitsgruppen für ein Azure Data Lake-Speicherkonto

Wenn Sie Sicherheitsgruppen aus Azure Data Lake-Speicherkonten entfernen, ändern Sie lediglich den Zugriff auf die Verwaltungsvorgänge des Kontos, indem Sie das Azure-Portal und die Azure-Ressourcen-Manager-APIs verwenden.

1. Klicken Sie auf dem Blatt Ihres Data Lake-Speicherkontos auf das Benutzersymbol.

	![Sicherheitsgruppe dem Azure Data Lake-Konto zuweisen](./media/data-lake-store-secure-data/adl.select.user.icon.png "Sicherheitsgruppe dem Azure Data Lake-Konto zuweisen")

2. Klicken Sie auf dem Blatt **Benutzer** auf die Sicherheitsgruppe, die Sie entfernen möchten.

	![Zu entfernende Sicherheitsgruppe](./media/data-lake-store-secure-data/adl.add.user.3.png "Zu entfernende Sicherheitsgruppe")

3. Klicken Sie auf dem Blatt für die Sicherheitsgruppe auf **Entfernen**.

	![Entfernte Sicherheitsgruppe](./media/data-lake-store-secure-data/adl.remove.group.png "Entfernte Sicherheitsgruppe")

## Entfernen der Zugriffssteuerungslisten von Sicherheitsgruppen aus dem Azure Data Lake-Speicher-Dateisystem

Wenn Sie Zugriffssteuerungslisten von Sicherheitsgruppen aus dem Azure Data Lake-Speicher-Dateisystem entfernen, ändern Sie den Zugriff auf die Daten im Data Lake-Speicher.

1. Klicken Sie auf dem Blatt Ihres Data Lake-Speicherkontos auf **Daten-Explorer**.

	![Verzeichnisse in Data Lake-Konto erstellen](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Verzeichnisse in Data Lake-Konto erstellen")

2. Klicken Sie auf dem Blatt **Daten-Explorer** auf den Stamm Ihres Kontos und dann auf dem Kontoblatt auf das Symbol **Zugriff**.

	![Zugriffssteuerungslisten für Data Lake-Dateisystem festlegen](./media/data-lake-store-secure-data/adl.acl.1.png "Zugriffssteuerungslisten für Data Lake-Dateisystem festlegen")

3. Klicken Sie auf dem Blatt **Zugriff** im Abschnitt **Benutzerdefinierter Zugriff** auf die Sicherheitsgruppe, die Sie entfernen möchten. Klicken Sie auf dem Blatt **Benutzerdefinierter Zugriff** auf **Entfernen** und dann auf **OK**.

	![Berechtigungen für Gruppe zuweisen](./media/data-lake-store-secure-data/adl.remove.acl.png "Berechtigungen für Gruppe zuweisen")


## Weitere Informationen

- [Übersicht über Azure Data Lake-Speicher](data-lake-store-overview.md)
- [Kopieren von Daten aus Azure Storage-Blobs in den Data Lake-Speicher](data-lake-store-copy-data-azure-storage-blob.md)
- [Verwenden von Azure Data Lake Analytics mit Data Lake-Speicher](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Verwenden von Azure HDInsight mit Data Lake-Speicher](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Erste Schritte mit Data Lake-Speicher mithilfe von PowerShell](data-lake-store-get-started-powershell.md)
- [Erste Schritte mit Data Lake-Speicher mithilfe des .NET SDK](data-lake-store-get-started-net-sdk.md)

<!---HONumber=AcomDC_0316_2016-->