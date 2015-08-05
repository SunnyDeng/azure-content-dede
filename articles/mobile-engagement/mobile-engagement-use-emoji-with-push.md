<properties 
	pageTitle="Verwenden von Emoticons in Pushbenachrichtigungen" 
	description="Gewusst wie: Verwenden von Emoticons in Pushbenachrichtigungen"					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="piyushjo" />

#Verwenden von Emoticons in Pushbenachrichtigungen

Sie können in Pushbenachrichtigungen Emoticons einfügen. Azure Mobile Engagement unterstützt derzeit für App-interne und App-externe Benachrichtigungen nur 3-Byte-Emoticons. Führen Sie die folgenden Schritte aus:

1.  Zunächst müssen Sie eine Bibliothek von 3-Byte-Emoticons suchen. Unter dem folgenden [Link](http://stackoverflow.com/questions/10153529/emoji-on-mysql-and-php-why-some-symbol-yes-other-not) finden Sie alle Emoticons, die Sie verwenden können.

	![][1]

2. Navigieren Sie im Azure Mobile Engagement-Portal zur Registerkarte "Reach".

3. Wählen Sie den Typ der Pushbenachrichtigung (Ankündigung, Umfragen usw.) aus. Für dieses Beispiel wählen wir eine Ankündigungspushbenachrichtigung.

4. Füllen Sie die verschiedenen Felder der Benachrichtigung aus, bis Sie zum Text der Benachrichtigung gelangen. Dort fügen Sie das Emoticon hinzu. Sie können es im Titel, in der Nachricht oder in beiden einfügen.

	![][2]

5. Schneiden Sie das Emoticon, das Sie verwenden möchten, aus dem vorherigen Link aus. Fügen Sie es direkt an der gewünschten Stelle im Titel und/oder in der Nachricht ein.

6. Füllen Sie die anderen Felder für die Benachrichtigung aus, und speichern Sie sie.

7. Wenn Sie einen Test ausführen oder die Ankündigung aktivieren, wird eine Benachrichtigung mit dem Emoticon entsprechend Ihren Festlegungen angezeigt.

	![][3] ![][4]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/emoji.png
[2]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/notification_android.png
[4]: ./media/mobile-engagement-use-emoji-with-push/notification_ios.png
 

<!---HONumber=July15_HO4-->