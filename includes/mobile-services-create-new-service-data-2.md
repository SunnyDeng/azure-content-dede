Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle in der verknüpften SQL-Datenbankinstanz erstellen.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf die Registerkarte **Daten** und anschließend auf **+Erstellen**.

    ![mobile-data-tab-empty][mobile-data-tab-empty]

    Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3.  Geben Sie unter **Tabellenname** den Namen *TodoItem* ein und klicken Sie auf das Kontrollkästchen.

    ![mobile-create-todoitem-table][mobile-create-todoitem-table]

Dadurch wird die neue Speichertabelle **TodoItem** mit Standardberechtigungen erstellt. Dies bedeutet, dass jeder mit dem Anwendungsschlüssel (der mit Ihrer App verteilt wird) auf die Daten in der Tabelle zugreifen und diese ändern kann.

> [WACOM.NOTE]
> Der gleiche Tabellenname wird im Mobile Services-Schnellstart verwendet. Jede Tabelle wird jedoch in einem Schema erstellt, das für einen bestimmten mobilen Dienst gilt. Dadurch soll verhindert werden, dass es zu Datenkollisionen kommt, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.

1.  Klicken Sie auf die neue Tabelle **TodoItem** und überprüfen Sie, dass keine Datenzeilen vorhanden sind.

2.  Klicken Sie auf die Registerkarte **Spalten**. Überprüfen Sie, dass folgende Standardspalten automatisch erstellt werden:

    | Spaltenname   | Typ               | Index                              |
    |---------------|-------------------|------------------------------------|
    | id            | string            | Indiziert                          |
    | \_\_createdAt | date              | Indiziert                          |
    | \_\_updatedAt | date              | <font color="transparent">-</font> |
    | \_\_version   | timestamp (MSSQL) | <font color="transparent">-</font> |

    Dies ist die Mindestanforderung für eine Tabelle in Mobile Services.

    <div class="dev-callout"><b>Hinweis</b>
<p>Wenn f&uuml;r Ihren mobilen Dienst das dynamische Schema aktiviert ist, werden neue Spalten immer dann automatisch erstellt, wenn JSON-Objekte &uuml;ber einen Einf&uuml;ge- oder Aktualisierungsvorgang an den mobilen Dienst gesendet werden.</p>
</div>

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.

  [mobile-data-tab-empty]: ./media/mobile-services-create-new-service-data-2/mobile-data-tab-empty.png
  [mobile-create-todoitem-table]: ./media/mobile-services-create-new-service-data-2/mobile-create-todoitem-table.png
