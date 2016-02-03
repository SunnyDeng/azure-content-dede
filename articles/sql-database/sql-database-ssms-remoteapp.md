<properties pageTitle="Herstellen einer Verbindung mit SQL-Datenbank per SQL Server Management Studio in Azure RemoteApp | Microsoft Azure" description="In diesem Lernprogramm erfahren Sie, wie Sie SQL Server Management Studio in Azure RemoteApp beim Herstellen einer Verbindung mit SQL-Datenbank für die Bereiche Sicherheit und Leistung verwenden." services="sql-database" documentationCenter="" authors="adhurwit" manager="" tags=""//>

<tags 
	ms.service="sql-database" 
	ms.workload="data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="adhurwit"/>

# Verwenden von SQL Server Management Studio in Azure RemoteApp zum Herstellen einer Verbindung mit SQL-Datenbank

## Einführung  
In diesem Lernprogramm erfahren Sie, wie Sie SQL Server Management Studio (SSMS) in Azure RemoteApp zum Herstellen einer Verbindung mit SQL-Datenbank verwenden. Sie werden durch die Einrichtung von SQL Server Management Studio in Azure RemoteApp geführt. Außerdem werden die Vorteile beschrieben und Sicherheitsfeatures vorgestellt, die Sie in Azure Active Directory nutzen können.

**Geschätzter Zeitaufwand:** 45 Minuten

## SSMS in Azure RemoteApp

Azure RemoteApp ist ein RDS-Dienst in Azure, mit dem Anwendungen bereitgestellt werden. Weitere Informationen finden Sie unter [Was ist RemoteApp?](../remoteapp-whatis.md)

Die Ausführung von SSMS in Azure RemoteApp entspricht in Bezug auf die Oberfläche der lokalen Ausführung von SSMS.

![Screenshot der Ausführung von SSMS in Azure RemoteApp][1]



## Vorteile

Die Verwendung von SSMS in Azure RemoteApp hat viele Vorteile, z. B.:

- Port 1433 von Azure SQL Server muss nicht extern (außerhalb von Azure) verfügbar gemacht werden.
- Es ist nicht erforderlich, in der Azure SQL Server-Firewall IP-Adressen hinzuzufügen und zu entfernen. 
- Alle Azure RemoteApp-Verbindungen werden per HTTPS über Port 443 mit einem verschlüsselten Remotedesktopprotokoll hergestellt.
- Es besteht Mehrbenutzerfähigkeit und die Möglichkeit zur Skalierung.
- Die Nutzung von SSMS in derselben Region wie die SQL-Datenbank führt zu Leistungssteigerungen.
- Sie können die Nutzung von Azure RemoteApp mit der Premium Edition von Azure Active Directory überwachen und deren Aktivitätsberichte verwenden.
- Sie können die Multi-Factor Authentication (MFA) aktivieren.
- Zugriff auf SSMS ist von jedem Ort aus möglich, wenn Sie einen der unterstützten Azure RemoteApp-Clients verwenden, z. B. iOS, Android, Macintosh, Windows Phone und Windows-PCs.


## Erstellen der Azure RemoteApp-Sammlung

Hier sind die Schritte zum Erstellen der Azure RemoteApp-Sammlung mit SSMS angegeben:


### 1\. Erstellen einer neuen Windows-VM aus einem Image
Verwenden Sie das Image „Windows Server Remote Desktop Session Host Windows Server 2012 R2“ aus dem Katalog, um die neue VM zu erstellen.


### 2\. Installieren von SSMS aus SQL Express

Wechseln Sie auf die neue virtuelle Maschine, und navigieren Sie zu dieser Downloadseite: [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/de-DE/download/details.aspx?id=42299).

Es ist eine Option zum Herunterladen von SSMS vorhanden. Navigieren Sie nach dem Download in das Installationsverzeichnis, und führen Sie das Setup zum Installieren von SSMS aus.

Installieren Sie auch das SQL Server 2014 Service Pack 1. Sie können es hier herunterladen: [Microsoft SQL Server 2014 Service Pack 1 (SP1)](https://www.microsoft.com/de-DE/download/details.aspx?id=46694).

SQL Server 2014 Service Pack 1 enthält wichtige Funktionen für die Verwendung von Azure SQL-Datenbank.


### 3\. Ausführen von „Skript überprüfen“ und Sysprep

Auf dem Desktop der virtuellen Maschine befindet sich ein PowerShell-Skript mit dem Namen „Validate“ (Überprüfen). Führen Sie es per Doppelklick aus. Es wird überprüft, ob die VM für das Remotehosting von Anwendungen bereit ist. Wenn die Überprüfung abgeschlossen ist, werden Sie gefragt, ob Sysprep ausgeführt werden soll. Stimmen Sie der Ausführung zu.

Nachdem Sysprep abgeschlossen ist, wird die VM heruntergefahren.

Weitere Informationen zum Erstellen eines Azure RemoteApp-Images finden Sie unter [How to create a RemoteApp template image in Azure](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx).


### 4\. Erfassen des Images

Wenn die Ausführung der VM beendet wurde, können Sie im aktuellen Portal danach suchen und das Image erfassen.

Weitere Informationen zum Erfassen eines Images finden Sie unter [Erfassen eines Images einer Azure-Windows-VM, die mit dem klassischen Bereitstellungsmodell erstellt wurde](../virtual-machines-capture-image-windows-server.md).


### 5\. Hinzufügen von Azure RemoteApp-Vorlagenimages

Navigieren Sie im Azure RemoteApp-Abschnitt des aktuellen Portals zur Registerkarte „Vorlagenimages“, und klicken Sie auf „Hinzufügen“. Wählen Sie im Popupmenü die Option „Importieren Sie ein Image aus Ihrer Bibliothek virtueller Computer“, und wählen Sie das gerade erstellte Image aus.



### 6\. Erstellen einer Cloudsammlung

Erstellen Sie im aktuellen Portal eine neue Azure RemoteApp-Cloudsammlung. Wählen Sie das Vorlagenimage mit der SSMS-Installation aus, das Sie gerade importiert haben.

![Neue Cloudsammlung erstellen][2]


### 7\. Veröffentlichen von SSMS

Wählen Sie auf der Registerkarte „Veröffentlichung“ Ihrer neuen Cloudsammlung im Startmenü die Option zum Veröffentlichen einer Anwendung, und wählen Sie in der Liste dann SSMS aus.

![App veröffentlichen][5]

### 8\. Hinzufügen von Benutzern

Auf der Registerkarte „Benutzerzugriff“ können Sie die Benutzer auswählen, denen der Zugriff auf diese Azure RemoteApp-Sammlung gewährt wird, die nur SSMS enthält.

![Benutzer hinzufügen][6]


### 9\. Installieren der Azure RemoteApp-Clientanwendung

Sie können einen Azure RemoteApp-Client hier herunterladen und installieren: [Download | Azure RemoteApp](https://www.remoteapp.windowsazure.com/en/clients.aspx).



## Konfigurieren von Azure SQL Server

Sie müssen per Konfiguration lediglich sicherstellen, dass Azure-Dienste für die Firewall aktiviert sind. Wenn Sie diese Lösung verwenden, müssen Sie keine IP-Adressen zum Öffnen der Firewall hinzufügen. Der Netzwerkdatenverkehr, der für SQL Server zulässig ist, stammt von anderen Azure-Diensten.


![Azure zulassen][4]



## Multi-Factor Authentication (MFA)

MFA kann speziell für diese Anwendung aktiviert werden. Navigieren Sie zur Registerkarte „Anwendungen“ von Azure Active Directory. Sie enthält den Eintrag „Microsoft Azure RemoteApp“. Wenn Sie auf diese Anwendung und dann auf „Konfigurieren“ klicken, wird die unten angegebene Seite angezeigt, auf der Sie MFA für diese Anwendung aktivieren können.

![MFA aktivieren][3]



## Überwachen der Benutzeraktivität mit Azure Active Directory Premium

Wenn Sie nicht über Azure AD Premium verfügen, müssen Sie die Anwendung im Abschnitt „Lizenzen“ Ihres Verzeichnisses aktivieren. Falls Premium aktiviert ist, können Sie der Premium-Ebene Benutzer zuweisen.

Wenn Sie in Azure Active Directory auf einen Benutzer zugreifen, können Sie auf der Registerkarte „Aktivität“ die Anmeldeinformationen für Azure RemoteApp anzeigen.



## Nächste Schritte

Nach Abschluss aller oben genannten Schritte können Sie den Azure RemoteApp-Client ausführen und sich mit einem zugewiesenen Benutzer anmelden. SSMS wird als eine Ihrer Anwendungen angezeigt. Sie können sie so ausführen, als ob sie auf Ihrem Computer mit Zugriff auf Azure SQL Server installiert wäre.

Weitere Informationen zum Herstellen der Verbindung mit SQL-Datenbank finden Sie unter [Herstellen einer Verbindung mit einer Azure SQL-Datenbank mit SQL Server Management Studio und Ausführen einer T-SQL-Beispielabfrage](sql-database-connect-query-ssms.md).


Das ist erst einmal alles. Viel Spaß!



<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png

<!---HONumber=AcomDC_0121_2016-->