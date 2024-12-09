---
lab:
  title: Erste Schritte mit Eventstream in Microsoft Fabric
  module: Get started with Eventstream in Microsoft Fabric
---
# Erste Schritte mit Eventstream in Microsoft Fabric

Eventstream ist ein No-Code-Feature in Microsoft Fabric, das Echtzeitereignisse erfasst, transformiert und an verschiedene Ziele sendet. Sie können dem Eventstream Ereignisdatenquellen, Routingziele und den Ereignisprozessor hinzufügen, wenn die Transformation erforderlich ist. EventStore von Microsoft Fabric ist eine Überwachungsoption, die Ereignisse aus dem Cluster verwaltet und eine Möglichkeit bietet, den Status Ihres Clusters oder Ihrer Workload zu einem bestimmten Zeitpunkt nachzuvollziehen. Der EventStore-Dienst kann nach Ereignissen abgefragt werden, die in Ihrem Cluster für jede Entität und jeden Entitätstyp verfügbar sind. Dies bedeutet, dass Sie Ereignisse auf verschiedenen Ebenen abfragen können (z. B. Cluster, Knoten, Anwendungen, Dienste, Partitionen und Partitionsreplikate). Der EventStore-Dienst hat auch die Möglichkeit, Ereignisse in Ihrem Cluster zu korrelieren. Durch den Blick auf Ereignisse, die gleichzeitig von unterschiedlichen Entitäten geschrieben wurden und sich möglicherweise gegenseitig beeinträchtigt haben, kann der EventStore-Dienst diese Ereignisse verknüpfen und beim Identifizieren von Ursachen für Aktivitäten in Ihrem Cluster helfen. Eine weitere Option zur Überwachung und Diagnose von Microsoft Fabric-Clustern ist das Aggregieren und Sammeln von Ereignissen mithilfe von EventFlow.

Dieses Lab dauert ungefähr **30** Minuten.

> **Hinweis:** Sie benötigen eine [Microsoft Fabric-Testversion](https://learn.microsoft.com/fabric/get-started/fabric-trial), um diese Übung durchführen zu können.

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Melden Sie sich auf der [Microsoft Fabric-Startseite](https://app.fabric.microsoft.com/home?experience=fabric) bei `https://app.fabric.microsoft.com/home?experience=fabric` an und wählen Sie die Option **Power BI** aus.
2. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
3. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
4. Beim Öffnen Ihres neuen Arbeitsbereichs sollte dieser wie im Folgenden gezeigt leer sein:

   ![Screenshot eines leeren Arbeitsbereichs in Power BI](./Images/new-workspace.png)
5. Wählen Sie unten links im Power BI-Portal das **Power BI**-Symbol aus, und wechseln Sie zur Benutzeroberfläche von **Real-Time Intelligence**.

## Vorbereiten einer Real-Time Intelligence-Eventhouse

1. Erstellen Sie auf der Startseite von Real-Time Intelligence in Microsoft Fabric ein neues **Eventhouse** und geben Sie ihm einen eindeutigen Namen Ihrer Wahl.
1. Schließen Sie alle Tipps oder Aufforderungen, die angezeigt werden, bis Sie Ihr neues leeres Eventhouse sehen.

    ![Screenshot eines neuen Eventhouse](./Images/create-eventhouse.png)

## Erstellen einer KQL-Datenbank

1. Wählen Sie innerhalb des **Real-Time Intelligence-Eventhouse** das Feld **KQL-Datenbank +** aus.
1. Sie haben die Möglichkeit, eine **Neue Datenbank (Standard)** zu erstellen oder eine **Neue Verknüpfungsdatenbank (Nachfolger)** zu erstellen.

    >**Hinweis:** Mit der Funktion „Follower-Datenbank“ können Sie eine Datenbank, die sich in einem anderen Cluster befindet, an Ihren Azure Data Explorer-Cluster anfügen. Die Follower-Datenbank wird im schreibgeschützten Modus angefügt, sodass Sie die Daten anzeigen und Abfragen für die Daten ausführen können, die in der Leader-Datenbank erfasst wurden. Die Follower-Datenbank synchronisiert Änderungen in den Leader-Datenbanken. Aufgrund der Synchronisierung gibt es bei der Datenverfügbarkeit eine Zeitverzögerung von einigen Sekunden bis zu einigen Minuten. Die Länge der Zeitverzögerung hängt von der Gesamtgröße der Metadaten in der Leader-Datenbank ab. Die Leader- und Follower-Datenbanken verwenden dasselbe Speicherkonto zum Abrufen der Daten. Der Speicher befindet sich im Besitz der Leader-Datenbank. Die Follower-Datenbank zeigt die Daten an, ohne sie erfassen zu müssen. Da die angefügte Datenbank eine schreibgeschützte Datenbank ist, können die Daten, Tabellen und Richtlinien in der Datenbank nicht geändert werden, mit Ausnahme der Cacherichtlinie, Prinzipale und Berechtigungen.

1. Erstellen Sie eine neue Datenbank und nennen Sie sie `Eventhouse-DB`.

## Erstellen eines Eventstreams

1. Wählen Sie auf der Hauptseite Ihrer KQL-Datenbank **Daten abrufen**.
2. Wählen Sie für die Datenquelle **Eventstream** > **Neuer Eventstream**. Benennen Sie den Eventstream `bicycle-data`.

    Die Erstellung Ihres neuen Eventstreams im Arbeitsbereich wird in wenigen Augenblicken abgeschlossen sein. Nach der Einrichtung werden Sie automatisch zum primären Editor weitergeleitet und können dort mit der Integration von Quellen in Ihren Eventstream beginnen.

    ![Screenshot eines neuen Eventstreams.](./Images//name-eventstream.png)

## Einrichten einer Eventstreamquelle

1. Wählen Sie im Eventstream-Canvas **Beispieldaten verwenden** aus.
2. Nennen Sie die Quelle `Bicycles` und wählen Sie die Beispieldaten **Fahrräder**.

    Ihr Stream wird gemappt und Sie werden automatisch auf dem **Eventstream-Canvas** angezeigt.

   ![Überprüfen Sie den Eventstream-Canvas](./Images/real-time-intelligence-eventstream-sourced.png)

## Ziel hinzufügen

1. In der Dropdown-Liste **Ereignisse umwandeln oder Ziel hinzufügen** wählen Sie **Eventhouse**.
1. Im Bereich **Eventhouse** konfigurieren Sie die folgenden Einrichtungsoptionen.
   - **Datenerfassungsmodus:**: Ereignisverarbeitung vor der Erfassung
   - **Zielname:**`Bicycle-database`
   - **Arbeitsbereich:***Wählen Sie den Arbeitsbereich, den Sie zu Beginn dieser Übung erstellt haben*
   - **Eventhouse**: *Wählen Sie Ihr Eventhouse*
   - **KQL-Datenbank:** Eventhouse-DB
   - **Zieltabelle:** Erstellen Sie eine neue Tabelle namens `bike-count`
   - **Eingabe-Datenformat:** JSON

   ![KQL Datenbank Eventstream mit Erfassungsmodi](./Images/kql-database-event-processing-before-ingestion.png)

1. Wählen Sie im Bereich **Eventhouse** die Option **Speichern**. 
1. Wählen Sie auf der Symbolleiste **Veröffentlichen** aus.
1. Warten Sie etwa eine Minute, bis das Datenziel aktiv wird.

## Erfasste Daten anzeigen

Der von Ihnen erstellte Eventstream übernimmt Daten aus der Beispielquelle für Fahrraddaten und lädt sie in die Datenbank in Ihrem Eventhouse. Sie können die erfassten Daten einsehen, indem Sie die Tabelle in der Datenbank abfragen.

1. Wählen Sie in der Menüleiste auf der linken Seite Ihre **Eventhouse-DB** Datenbank.
1. Wählen Sie im Menü ** …** für die KQL-Datenbank **Eventhouse-DB** die Option **Daten abfragen**.
1. Ändern Sie im Abfragebereich die erste Beispielabfrage wie hier gezeigt:

    ```kql
    ['bike-count']
    | take 100
    ```

1. Wählen Sie den Abfragecode aus und führen Sie ihn aus, um 100 Datenzeilen aus der Tabelle anzuzeigen.

    ![Screenshot einer KQL-Abfrage.](./Images/kql-query.png)

## Ereignisdaten umwandeln

Die Daten, die Sie erfasst haben, sind von der Quelle her unverfälscht. In vielen Szenarien möchten Sie vielleicht die Daten im Eventstream umwandeln, bevor Sie sie in ein Ziel laden.

1. Wählen Sie in der Menüleiste auf der linken Seite den Eventstream **Fahrraddaten**.
1. Wählen Sie in der Symbolleiste **Bearbeiten**, um den Eventstream zu bearbeiten.

1. Wählen Sie im Menü **Ereignisse umwandeln** die Option **Gruppieren nach**, um dem Eventstream einen neuen Knoten **Gruppieren nach** hinzuzufügen.
1. Ziehen Sie eine Verbindung vom Ausgang des Knotens **Fahrraddaten** zum Eingang des neuen Knotens **Gruppieren nach**. Verwenden Sie dann das Symbol *Bleistift* im Knoten **Gruppieren nach**, um sie zu bearbeiten.

   ![Hinzufügen von „Gruppieren nach“ zum Transformationsereignis.](./Images/eventstream-add-aggregates.png)

1. Konfigurieren Sie die Eigenschaften des Einstellungsbereichs **Gruppieren nach**:
    - **Vorgangsname:** GroupByStreet
    - **Aggregat-Typ:***Wählen Sie* Summe
    - **Feld:***Wählen Sie* No_Bikes. *Wählen Sie dann **Hinzufügen**, um die Funktion zu erstellen* SUM_No_Bikes
    - **Gruppierung der Aggregationen nach (optional):** Straße
    - **Zeitfenster**: Rollierend
    - **Dauer**: 5 Sekunden
    - **Offset**: 0 Sekunden

    > **Hinweis**: Diese Konfiguration veranlasst den Eventstream, alle 5 Sekunden die Gesamtzahl der Fahrräder in jeder Straße zu berechnen.
      
1. Speichern Sie die Konfiguration und kehren Sie zum Eventstream-Canvas zurück, wo ein Fehler angezeigt wird (denn Sie müssen die Ausgabe der Gruppe nach Transformation irgendwo speichern!)

1. Verwenden Sie das Symbol **+** rechts neben dem Knoten **GroupByStreet**, um einen neuen Knoten **Eventhouse** hinzuzufügen.
1. Konfigurieren Sie den neuen Eventhouse-Knoten mit den folgenden Optionen:
   - **Datenerfassungsmodus:**: Ereignisverarbeitung vor der Erfassung
   - **Zielname:**`Bicycle-database`
   - **Arbeitsbereich:***Wählen Sie den Arbeitsbereich, den Sie zu Beginn dieser Übung erstellt haben*
   - **Eventhouse**: *Wählen Sie Ihr Eventhouse*
   - **KQL-Datenbank:** Eventhouse-DB
   - **Zieltabelle:** Erstellen Sie eine neue Tabelle namens `bikes-by-street`
   - **Eingabe-Datenformat:** JSON

   ![Screenshot einer Tabelle für gruppierte Daten.](./Images/group-by-table.png)

1. Wählen Sie im Bereich **Eventhouse** die Option **Speichern**. 
1. Wählen Sie auf der Symbolleiste **Veröffentlichen** aus.
1. Warten Sie etwa eine Minute, bis die Änderungen aktiv werden.

## Anzeigen der transformierten Daten

Jetzt können Sie die Fahrraddaten einsehen, die von Ihrem Eventstream umgewandelt und in eine Tabelle geladen wurden

1. Wählen Sie in der Menüleiste auf der linken Seite Ihre **Eventhouse-DB** Datenbank.
1. Wählen Sie im Menü ** …** für die KQL-Datenbank **Eventhouse-DB** die Option **Daten abfragen**.
1. Ändern Sie im Abfragebereich eine Beispielabfrage wie hier gezeigt:

    ```kql
    ['bikes-by-street']
    | take 100
    ```

1. Wählen Sie den Abfragecode aus und führen Sie ihn aus, um die ersten 100 Zeilen in der Tabelle zu sehen.

    ![Screenshot einer KQL-Abfrage.](./Images/kql-group-query.png)

    > **Tipp**: Sie können die Tabelle auch mit der SQL-Syntax abfragen. Versuchen Sie zum Beispiel die Abfrage `SELECT TOP 100 * FROM bikes-by-street`.

## Bereinigen von Ressourcen

In dieser Übung haben Sie ein Eventhouse erstellt und mithilfe eines Eventstreams Tabellen in dessen Datenbank pipettiert.

Wenn Sie die Untersuchung Ihrer KQL-Datenbank abgeschlossen haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus.
2. Wählen Sie in der Symbolleiste **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Allgemein** die Option **Diesen Arbeitsbereich entfernen** aus.
.
