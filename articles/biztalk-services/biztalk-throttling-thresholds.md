<properties 
	pageTitle="Informationen zur Drosselung in BizTalk Services | Azure" 
	description="Erfahren Sie mehr über Drosselungsschwellenwerte und das daraus resultierende Laufzeitverhalten für BizTalk Services. Die Drosselung basiert auf der Arbeitsspeicherauslastung und der Nachrichtenanzahl. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="mandia"/>





# BizTalk Services: Drosselung

Azure BizTalk Services implementiert die Dienstdrosselung basierend auf zwei Bedingungen: Arbeitsspeicherauslastung und Anzahl der gleichzeitig verarbeiteten Nachrichten. In diesem Thema werden die Drosselungsschwellenwerte aufgelistet und das Laufzeitverhalten beim Auftreten einer Drosselungsbedingung beschrieben.

## Drosselungsschwellenwerte

In der folgenden Tabelle sind die Drosselungsquelle und -schwellenwerte aufgelistet:

<table border="1">

<tr bgcolor="FAF9F9">
        <th> </th>
        <td><strong>Beschreibung</strong></td>
        <td><strong>Niedriger Schwellenwert</strong></td>
        <td><strong>Hoher Schwellenwert</strong></td>
</tr>
    <tr>
        <td>Arbeitsspeicher</td>
        <td>% des verfügbaren Gesamtsystemarbeitsspeichers/PageFileBytes. 
<br/><br/>
Verfügbarer PageFileBytes-Gesamtwert beträgt etwa das Zweifache des RAMs des Systems.</td>
        <td>60&#160;%</td>
        <td>70&#160;%</td>
    </tr>
    <tr>
        <td>Nachrichtenverarbeitung</td>
        <td>Anzahl der simultan verarbeiteten Nachrichten</td>
        <td>40 * Anzahl der Kernspeicher</td>
        <td>100 * Anzahl der Kernspeicher</td>
    </tr>
</table>

Wenn ein hoher Schwellenwert erreicht ist, beginnt Azure BizTalk Services mit der Drosselung. Die Drosselung wird beendet, wenn ein niedriger Schwellenwert erreicht wird. Der Dienst nutzt beispielsweise 65 % des Systemarbeitsspeichers. In dieser Situation führt der Dienst keine Drosselung durch. Der Dienst beginnt damit, wenn 70 % des Systemarbeitsspeichers genutzt werden. In dieser Situation führt der Dienst eine Drosselung durch und setzt diese fort, bis der Dienst 60 % (niedriger Schwellenwert) des Systemarbeitsspeichers nutzt.

Azure BizTalk Services verfolgen den Drosselungsstatus (normaler Status vs. gedrosselter Status) und die Drosselungsdauer.


## Laufzeitverhalten

Wenn Azure BizTalk Services einen Drosselungsstatus erreichen, tritt Folgendes ein:

- Die Drosselung wird pro Rolleninstanz durchgeführt. Beispiel:<br/> RoleInstanceA wird gedrosselt. RoleInstanceB wird nicht gedrosselt. In dieser Situation werden die Nachrichten in RoleInstanceB erwartungsgemäß verarbeitet. Die Nachrichten in RoleInstanceA werden verworfen und geben folgenden Fehler aus:<br/><br/> **Server ist ausgelastet. Versuchen Sie es erneut.**<br/><br/>
- Keine der Pullquellen ruft eine Nachricht ab oder lädt eine herunter. Beispiel:<br/> Eine Pipeline ruft Nachrichten per Pullaktion aus einer externen FTP-Quelle ab. Die Rolleninstanz, welche die Pullaktion durchführt, geht in einen Drosselungsstatus über. In dieser Situation setzt die Pipeline das Herunterladen zusätzlicher Nachrichten aus, bis die Rolleninstanz die Drosselung beendet.
- Eine Antwort wird an den Client gesendet, so dass dieser die Nachricht neu senden kann.
- Sie müssen solange warten, bis die Drosselung aufgelöst ist. Insbesondere müssen Sie warten, bis ein niedriger Schwellenwert erreicht ist.

## Wichtige Hinweise
- Die Drosselung kann nicht deaktiviert werden.
- Die Drosselungsschwellenwerte können nicht modifiziert werden.
- Die Drosselung ist systemweit implementiert.
- Der Azure SQL-Datenbankserver verfügt ebenfalls über eine integrierte Drosselung.

## Zusätzliche Azure BizTalk Services-Themen

-  [Installieren des Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Lernprogramme: Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Wie verwende ich das Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## Weitere Informationen
- [BizTalk Services: Editionsübersicht](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk Services: Erstellen eines BizTalk Service im Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk Services: Bereitstellungsstatusübersicht](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk Services: Sichern und Wiederherstellen](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk Services: Name und Schlüssel des Ausstellers](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 

<!---HONumber=July15_HO4-->