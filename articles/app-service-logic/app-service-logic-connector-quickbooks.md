<properties 
   pageTitle="QuickBooks-Connector" 
   description="Verwenden des QuickBooks-Connectors" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/01/2015"
   ms.author="vagarw"/>


#Verwenden des QuickBooks-Connectors in Logik-Apps#

Logik-Apps können basierend auf einer Vielzahl von Datenquellen ausgelöst werden und Connectors anbieten, um Daten als Teil des Datenflusses abzurufen und zu verarbeiten. Mit dem QuickBooks-Connector können Sie unterschiedliche QuickBooks-Entitäten erstellen und bearbeiten. Im Folgenden finden Sie eine Liste der QuickBooks-Entitäten, die vom QuickBooks-Connector unterstützt werden.

<Table>
<TR><TD><B>Entitäten</TD><TD><B>Beschreibung</TR> <TR> <TD> Konto </TD> <TD> Konto ist eine Komponente von einem Diagramm Konten und ist Teil einer Finanzbuchhaltung. Zum Erfassen von insgesamt Geldbetrag zugeordnet, die für einen bestimmten Verwendungszweck verwendet </TD> </TR> <TR> <TD> CreditMemo </TD> <TD> der CreditMemo ist eine finanzielle Transaktion, die eine Rückerstattung oder Gutschrift Zahlung oder Teil einer Zahlung für Waren oder Dienstleistungen, die verkauft wurden. </TD> </TR> <TR> <TD> Kunden </TD> <TD> ein Kunden ist ein Consumer des Dienstes oder Produkts, das Ihr Unternehmen bietet. </TD> </TR> <TR> <TD> Geschätzte </TD> <TD> der Schätzung stellt einen Vorschlag für eine finanzielle Transaktion von einem Unternehmen eines Kunden für Waren oder Dienstleistungen verkauft werden vorgeschlagen, einschließlich vorgeschlagenen Preise. </TD> </TR> <TR> <TD> Rechnung </TD> <TD> einer Rechnung stellt eine sales Form, in denen der Kunde für ein Produkt oder Dienst später zahlt. Weitere Informationen zur Verwendung des Rechnungsdatenmodells finden Sie hier. </TD> </TR> <TR> <TD> Artikel </TD> <TD> ein Elements ist eine Sache, die Ihr Unternehmen kauft, verkauft oder verkauft, z. B. Produkte, Versand- und Bearbeitungsgebühren Kosten, Rabatten und Mehrwertsteuer (falls zutreffend). Ein Artikel wird als Position in einer Rechnung oder einem Verkaufsformular angezeigt. </TD> </TR> <TR> <TD> SalesReceipt </TD> <TD> dieser Entität darstellt, die sales-Empfangsbestätigung an einen Kunden erhält. </TD> </TR> </Table>


##QuickBooks-Aktionen ##
Im Folgenden werden die verschiedenen im QuickBooks-Connector verfügbaren Aktionen aufgeführt. <table> <tbody> <tr><td> <strong>Aktion</strong> </td> <td> <strong>Beschreibung</strong> </td> </tr> <tr> <td> Lesen Entität </td> <td> Lesen Entitätsobjekt. </td> </tr> <tr> <td> Erstellen oder Aktualisieren von Entitäten </td> <td> Entity-Objekt erstellen oder aktualisieren. Objekt wird aktualisiert, wenn es vorhanden ist, andernfalls wird ein neues Objekt erstellt. </td> </tr> <tr> <td> Löschen Sie </td> <td> Diese Aktion löscht das angegebene Objekt aus der ausgewählten Entität. </td> </tr> <tr> <td> Abfrage </td> <td> Abfragevorgang ist die Methode für eine angeleitete Abfrage für eine Entität erstellen. </td> </tr> </tbody> </table>

##Erstellen einer QuickBooks-Connector-API-App##
1.	Öffnen Sie den Azure Marketplace mit der Option "+NEU" unten rechts im Azure-Portal.
2.	Wechseln Sie zu "Web und Mobil > API-Apps", und suchen Sie nach "QuickBooks".
3.	Konfigurieren Sie den QuickBooks-Connector, indem Sie die Informationen für den Webhosting-Plan und die Ressourcengruppe angeben und einen Namen für die API-App wählen.

	![][13]
4. Konfigurieren Sie die QuickBooks-Entitäten, die Sie beim Lesen/Schreiben in die Paketeinstellungen interessiert sind.

Mit diesen Informationen können Sie jetzt eine QuickBooks-Connector-API-App erstellen.


##Erstellen einer Logik-App##
Lassen Sie uns erstellen Sie eine einfache Logik app, die ein Konto in QuickBooks erstellt und aktualisieren Sie 'Kategorietyp' das gleiche Konto.

1.	Melden Sie sich beim Azure-Portal an, und klicken Sie auf "Neu -> Web + Mobil -> Logik-App"

	![][1]

2.	Stellen Sie auf der Seite "Logik-App erstellen" die erforderlichen Details wie Name, App Service-Plan und -Ort bereit.

	![][2]

3.	Klicken Sie auf "Trigger und Aktionen". Der Bildschirm mit dem Logik-App-Editor wird angezeigt.

	![][3]

4.	Wählen Sie "Diese Logik manuell ausführen", sodass diese Logik-App nur manuell aufgerufen werden kann.


5.	Erweitern Sie "API-Apps in dieser Ressourcengruppe" im Katalog, um alle verfügbaren API-Apps anzuzeigen. Wählen Sie "QuickBooks-Connector" aus der Galerie und der QuickBooks Connector Ruft den Fluss hinzugefügt.


6.	Wenn Sie QuickBooks online verwenden, müssen Logik-Apps in Ihrem Namen authentifiziert und autorisiert werden. Zum Starten der Autorisierung klicken Sie im QuickBooks-Connector auf "Autorisieren".

	![][4]

7.	Durch das Klicken auf "Autorisieren" wird das Authentifizierungsdialogfeld von QuickBook angezeigt. Geben Sie die Anmeldeinformationen des QuickBook-Kontos an, mit dem Sie die Vorgänge ausführen möchten.

	![][5]

8. Gewähren Sie Logik-Apps Zugriff auf Ihr Konto, indem Sie im Zustimmungsdialogfeld auf "Autorisieren" klicken, sodass der Vorgang in Ihrem Namen ausgeführt wird.

	![][6]

9.	Sobald die Autorisierung abgeschlossen ist, werden alle Aktionen angezeigt.

	![][7]

10.	Aktionstyp auswählen 'Erstellen oder Konto aktualisieren' und die Eingabeparameter werden angezeigt.

	![][8]

11.	Geben Sie 'Name' und 'Kontotyp', und klicken Sie auf ✓.

	![][9]

12.	Wählen Sie "QuickBooks-Connector" aus dem Abschnitt "Zuletzt verwendete" in der Galerie und eine neue QuickBooks Aktion hinzugefügt wird.

13.	Wählen Sie aus der Liste der Aktionen und die Eingabe "Erstellen oder Konto aktualisieren" werden die Parameter der Aktion angezeigt.

	![][10]

14.	Klicken Sie auf "+" neben "Id", wählen Sie den Id-Wert aus der Ausgabe einer Aktion für Konto erstellen.

	![][11]

15.	Geben Sie neue Werte für den Kontotyp ein, und klicken Sie auf ✓.

	![][12]

16. Klicken Sie im Bildschirm des Logik-App-Editors auf "OK", und klicken Sie dann auf "Erstellen". Es dauert ungefähr 30 Sekunden, bis die Erstellung abgeschlossen ist.

17. Sehen Sie sich die neu erstellte Logik-App an, und klicken Sie auf "Ausführen", um eine Ausführung zu initiieren.

18. Sie können überprüfen, dass ein neues Konto mit Namen "Contoso" in Ihrem Konto QuickBooks erstellt wird.

<!--Image references-->
[1]: ./media/app-service-logic-connector-quickbooks/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-quickbooks/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-quickbooks/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-quickbooks/4_QuickBooks_Authorize.png
[5]: ./media/app-service-logic-connector-quickbooks/5_QuickBooks_Login.png
[6]: ./media/app-service-logic-connector-quickbooks/6_QuickBooks_User_Consent.png
[7]: ./media/app-service-logic-connector-quickbooks/7_QuickBooks_Actions.png
[8]: ./media/app-service-logic-connector-quickbooks/8_QuickBooks_Create_Account.png
[9]: ./media/app-service-logic-connector-quickbooks/9_Create_Account_OK.png
[10]: ./media/app-service-logic-connector-quickbooks/10_QuickBooks_Update_Account.png
[11]: ./media/app-service-logic-connector-quickbooks/11_Record_ID_from_Create.png
[12]: ./media/app-service-logic-connector-quickbooks/12_Update_Account_Address.png
[13]: ./media/app-service-logic-connector-quickbooks/13_Create_new_quickbooks_connector.png


 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->