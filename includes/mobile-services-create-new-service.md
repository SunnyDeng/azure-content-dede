Führen Sie die folgenden Schritte aus, um einen neuen mobilen Dienst zu erstellen.

1.  Melden Sie sich beim [Verwaltungsportal][Verwaltungsportal] an.

2.  Klicken Sie unten im Navigationsbereich auf **+NEW**.

    ![][0]

3.  Erweitern Sie **Compute** und **Mobile Service**, und klicken Sie dann auf **Erstellen**.

    ![][1]

    Das Dialogfeld **New Mobile Service** wird angezeigt.

4.  Wählen Sie auf der Seite **Create a mobile service** die Option **Create a free 20 MB SQL Database** und die Laufzeit **Node.js** aus. Geben Sie dann einen Unterdomänennamen für den neuen mobilen Dienst im Textfeld **URL** ein, und warten Sie, bis der Name überprüft wurde. Nachdem der Name fertig überprüft wurde, klicken Sie auf den Pfeil nach rechts, um die nächste Seite aufzurufen.

    ![][2]

    Dadurch wird die Seite **Specify database settings** angezeigt.

    <div class="dev-callout"> 
<b>Hinweis</b> 
<p>Im Rahmen dieses Lernprogramms erstellen Sie eine neue SQL-Datenbankinstanz und einen neuen SQL-Datenbankserver. Sie k&ouml;nnen diese neue Datenbank wiederverwenden und wie jede andere SQL-Datenbankinstanz verwalten. Wenn Sie bereits &uuml;ber eine Datenbank in derselben Region wie der neue mobile Dienst verf&uuml;gen, k&ouml;nnen Sie stattdessen <strong>Vorhandene Datenbank verwenden</strong> und dann diese Datenbank ausw&auml;hlen. Die Verwendung einer Datenbank in einer anderen Region wird nicht empfohlen, da zus&auml;tzliche Kosten f&uuml;r Bandbreite anfallen k&ouml;nnen und es zu h&ouml;herer Latenz kommen kann.</p></div>

5.  Geben Sie unter **Name** den Namen der neuen Datenbank ein. Geben Sie dann den **Anmeldenamen** ein. Dies ist der Administratoranmeldename für den neuen SQL-Datenbankserver. Geben Sie das Kennwort ein, und bestätigen Sie es, und klicken Sie dann auf den Häkchenknopf, um den Vorgang abzuschließen.

    ![][3]

    <div class="dev-callout"> 
<b>Hinweis</b> 
<p>Wenn das eingegebene Kennwort nicht den Mindestanforderungen entspricht oder es keine &Uuml;bereinstimmung gibt, wird eine Warnung angezeigt. <br/>Es wird empfohlen, sich den Anmeldenamen und das Kennwort des Administrators zu notieren. Sie ben&ouml;tigen diese Informationen, um die SQL-Datenbankinstanz oder den Server in der Zukunft wiederzuverwenden.</p> 
</div>

Sie haben nun einen neuen mobilen Dienst erstellt, der von Ihren mobilen Apps verwendet werden kann.

<!-- URLs. -->

  [Verwaltungsportal]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-create-new-service/plus-new.png
  [1]: ./media/mobile-services-create-new-service/mobile-create.png
  [2]: ./media/mobile-services-create-new-service/mobile-create-page1.png
  [3]: ./media/mobile-services-create-new-service/mobile-create-page2.png
