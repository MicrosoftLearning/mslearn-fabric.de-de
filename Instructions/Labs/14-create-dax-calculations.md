---
lab:
  title: Erstellen von DAX-Berechnungen in Power BI Desktop
  module: Create DAX calculations in Power BI Desktop
---

# Erstellen von DAX-Berechnungen in Power BI Desktop

## **Labszenario**

In diesem Lab erstellen Sie mithilfe von Data Analysis Expressions (DAX) berechnete Tabellen, berechnete Spalten und einfache Measures.

In diesem Lab lernen Sie Folgendes:

- Erstellen berechneter Tabellen
- Erstellen berechneter Spalten
- Erstellen von Measures

**Dieses Lab sollte ungefähr 45 Minuten in Anspruch nehmen.**

## Erste Schritte

Um diese Übung abzuschließen, öffnen Sie zuerst einen Webbrowser, und geben Sie die folgende URL ein, um den ZIP-Ordner herunterzuladen:

`https://github.com/MicrosoftLearning/mslearn-fabric/raw/main/Allfiles/Labs/14/14-create-dax.zip`

Extrahieren Sie den Ordner in den Ordner **C:\Users\Student\Downloads\14-create-dax**.

Öffnen Sie die Datei **14-Starter-Sales Analysis.pbix**.

> ***Hinweis**: Sie können die Anmeldung abbrechen, indem Sie **Abbrechen** wählen. Schließen Sie alle anderen Informationsfenster. Wählen Sie **Später anwenden**, wenn Sie aufgefordert werden, die Änderungen anzuwenden.*

## Erstellen der berechneten Tabelle „Salesperson“

In dieser Aufgabe erstellen Sie die berechnete Tabelle **Salesperson** (direkte Beziehung zu **Sales**).

Eine berechnete Tabelle wird erstellt, indem zuerst der Tabellenname eingegeben wird, gefolgt vom Gleichheitssymbol (=), gefolgt von einer DAX-Formel, die eine Tabelle zurückgibt. Der Tabellenname darf nicht bereits im Datenmodell vorhanden sein.

Die Bearbeitungsleiste unterstützt das Eingeben einer gültigen DAX-Formel. Sie bietet Features wie AutoVervollständigen, Intellisense und Farbcodierung, mit denen Sie die Formel schnell und fehlerfrei eingeben können.

1. Klicken Sie in Power BI Desktop in der Berichtsansicht auf dem Menüband **Modellierung** in der Gruppe **Berechnungen** auf **Neue Tabelle**.

     ![Abbildung 1](Images/create-dax-calculations-in-power-bi-desktop_image9.png)

2. Geben Sie in die Bearbeitungsleiste (die beim Erstellen oder Bearbeiten von Berechnungen direkt unterhalb des Menübands geöffnet wird) **SalesPerson =** ein. Drücken Sie **UMSCHALT+EINGABE**, geben Sie **'SalesPerson (Performance)'** ein, und drücken Sie dann die **EINGABETASTE**.

    > **Anmerkung**: *Zu Ihrer Erleichterung können alle DAX-Definitionen in diesem Lab aus der Snippets-Datei kopiert werden, die sich in **14-create-dax\Snippets.txt** befindet.*

     ![Bild 4](Images/create-dax-calculations-in-power-bi-desktop_image10.png)

    > *Mit dieser Tabellendefinition wird eine Kopie der Tabelle **Salesperson (Performance)** erstellt. Es werden nur die Daten kopiert. Eigenschaften wie Sichtbarkeit, Formatierung usw. werden jedoch nicht kopiert.*

1. Im Bereich **Daten** sehen Sie, dass dem Tabellensymbol ein zusätzlicher Taschenrechner vorangestellt ist (zur Kennzeichnung einer berechneten Tabelle).

    ![Abbildung 10](Images/create-dax-calculations-in-power-bi-desktop_image11.png)

    > ***Hinweis**: Berechnete Tabellen werden mithilfe einer DAX-Formel definiert, die eine Tabelle zurückgibt. Es ist wichtig zu verstehen, dass berechnete Tabellen das Datenmodell vergrößern, da sie Werte materialisieren und speichern. Sie werden immer dann neu berechnet, wenn die Formelabhängigkeiten aktualisiert werden, wie es in diesem Datenmodell der Fall sein wird, wenn neue (zukünftige) Datumswerte in Tabellen geladen werden.*
    >
    > *Im Gegensatz zu Power Query-Tabellen können berechnete Tabellen nicht zum Laden von Daten aus externen Datenquellen genutzt werden. Sie können Daten nur auf Grundlage dessen transformieren, was bereits in das Datenmodell geladen wurde.*

1. Wechseln Sie zur Modellansicht, und beachten Sie, dass die Tabelle **Salesperson** verfügbar ist (möglicherweise müssen Sie die Ansicht zurücksetzen, um die Tabelle zu finden).

1. Erstellen Sie eine Beziehung zwischen der Spalte **Salesperson \| EmployeeKey** und der Spalte **Sales \| EmployeeKey**.

1. Klicken Sie mit der rechten Maustaste auf die inaktive Beziehung zwischen den Tabellen **Salesperson (Performance)** und **Sales**, und wählen Sie **Löschen** aus. Wenn Sie aufgefordert werden, den Löschvorgang zu bestätigen, klicken Sie auf **Ja**.

1. Wählen Sie in der Tabelle **Salesperson** die folgenden Spalten zusammen aus, und blenden Sie sie dann aus (legen Sie die Eigenschaft **Ausgeblendet** auf **Ja** fest):

    - EmployeeID
    - EmployeeKey
    - UPN

1. Wählen Sie im Modelldiagramm die Tabelle **Salesperson** aus.

1. Geben Sie im Bereich **Eigenschaften** in das Feld **Beschreibung** Folgendes ein: **Salesperson related to Sales**
    
    > *Denken Sie daran, dass die Beschreibungen als QuickInfo im Bereich **Daten** angezeigt werden, wenn Benutzer*innen den Cursor über eine Tabelle oder ein Feld bewegen.*

1. Legen Sie für die Tabelle **Salesperson (Performance)** die Beschreibung wie folgt fest: **Salesperson related to region(s)**

*Das Datenmodell bietet zwei Alternativen bei der Analyse von Vertriebsmitarbeiter*innen. Die Tabelle **Salesperson** ermöglicht die Analyse der von einem Vertriebsmitarbeiter erzielten Umsätze, während die Tabelle **Salesperson (Performance)** die Analyse der Umsätze ermöglicht, die in den dem Vertriebsmitarbeiter zugewiesenen Vertriebsregionen erzielt wurden.*

## Erstellen der Tabelle „Date“

In dieser Aufgabe erstellen Sie die Tabelle **Date**.

1. Wechseln Sie zur Tabellenansicht. Klicken Sie auf der Registerkarte **Start** des Menübands in der Gruppe **Berechnungen** auf **Neue Tabelle**.

    ![Bild 5](Images/create-dax-calculations-in-power-bi-desktop_image15.png)

1. Geben Sie auf der Formelleiste die folgende DAX ein:

    ```DAX
    Date =  
    CALENDARAUTO(6)
    ```

    ![Bild 6](Images/create-dax-calculations-in-power-bi-desktop_image16.png)

    > *Die CALENDARAUTO()-Funktion gibt eine Tabelle mit einer einzigen Spalte mit Datumswerten zurück. Es werden automatisch alle Datumsspalten des Datenmodells durchsucht, um die im Datenmodell gespeicherten frühesten und spätesten Datumswerte zu ermitteln. Anschließend wird für jedes Datum innerhalb dieses Bereichs eine Zeile angelegt, wobei der Bereich in beide Richtungen erweitert wird, um sicherzustellen, dass Daten für vollständige Jahre gespeichert werden.*
    >
    > *Diese Funktion kann ein einzelnes optionales Argument verwenden, das die Zahl des letzten Monats eines Jahres ist. Falls weggelassen, ist der Wert 12, was bedeutet, dass Dezember der letzte Monat des Jahres ist. In diesem Fall wird „6“ eingegeben, was bedeutet, dass Juni der letzte Monat des Geschäftsjahres ist.*

1. Beachten Sie die Spalte mit Datumsangaben, die mit US-amerikanischen Regionseinstellungen formatiert sind (d. h. MM/TT/JJJJ).

    ![Bild 7](Images/create-dax-calculations-in-power-bi-desktop_image17.png)

1. Links unten auf der Statusleiste sehen Sie die Tabellenstatistik, die bestätigt, dass 1.826 Datenzeilen generiert wurden, was den Daten fünf vollständiger Jahre entspricht.

    ![Bild 9](Images/create-dax-calculations-in-power-bi-desktop_image18.png)

## Erstellen berechneter Spalten

In dieser Aufgabe fügen Sie weitere Spalten hinzu, um das Filtern und Gruppieren nach verschiedenen Zeiträumen zu aktivieren. Sie erstellen auch eine berechnete Spalte, um die Sortierreihenfolge anderer Spalten zu steuern.

> **Hinweis**: *Der Einfachheit halber können Sie alle DAX-Definitionen in diesem Lab aus der Datei **Snippets.txt** kopieren.*

1. Klicken Sie auf dem kontextbezogenen Menüband **Tabellentools** in der Gruppe **Berechnungen** auf **Neue Spalte**.

    > *Eine berechnete Spalte wird erstellt, indem zuerst der Spaltenname eingegeben wird, gefolgt vom Gleichheitszeichen (=), gefolgt von einer DAX-Formel, die ein Ergebnis mit einem Wert zurückgibt. Der Spaltenname darf nicht bereits in der Tabelle vorhanden sein.*

    ![Bild 11](Images/create-dax-calculations-in-power-bi-desktop_image19.png)

1. Geben Sie auf der Formelleiste Folgendes ein (oder kopieren Sie die Eingaben aus der Datei mit den Codeausschnitten), und drücken Sie dann die **EINGABETASTE**:
    > *Die Formel nutzt den Jahreswert des Datums, addiert jedoch 1 zum Jahreswert, wenn der Monat später als Juni ist. Auf diese Weise werden Geschäftsjahre bei Adventure Works berechnet.*

   ```DAX
   Year =
   "FY" & YEAR('Date'[Date]) + IF(MONTH('Date'[Date]) > 6, 1)
   ```



1. Erstellen Sie mithilfe der Definitionen in der Codeausschnittsdatei die folgenden beiden berechneten Spalten für die Tabelle **Date**:

    - Quarter
    - Monat

1. Überprüfen Sie, ob die neuen Spalten hinzugefügt wurden.

    ![Bild 14](Images/create-dax-calculations-in-power-bi-desktop_image21.png)

1. Um die Berechnungen zu überprüfen, wechseln Sie zur Ansicht „Bericht“.

1. Um eine neue Berichtsseite zu erstellen, wählen Sie das Plussymbol neben Seite 1 aus.

    ![Bild 15](Images/create-dax-calculations-in-power-bi-desktop_image22.png)

1. Um der neuen Berichtsseite ein Matrixvisual hinzuzufügen, wählen Sie im Bereich **Visualisierungen** den Visualtyp der Matrix aus.

    > *Tipp: Sie können den Cursor über jedes Symbol bewegen, um eine QuickInfo mit einer Beschreibung des Visualtyps anzuzeigen.*

    ![Bild 51](Images/create-dax-calculations-in-power-bi-desktop_image23.png)

1. Ziehen Sie im Bereich **Daten** aus der Tabelle **Date** das Feld **Year** in den Bereich **Zeilen**.

    ![Bild 17](Images/create-dax-calculations-in-power-bi-desktop_image24.png)

1. Ziehen Sie das Feld **Month** in den Bereich **Zeilen** direkt unter dem Feld **Year**.

1. Klicken Sie rechts oben im visuellen Matrixelement (oder unten, je nach Position des visuellen Elements) auf den gabelförmigen Doppelpfeil (damit werden alle Jahre um eine Ebene nach unten erweitert).

    ![Bild 19](Images/create-dax-calculations-in-power-bi-desktop_image26.png)

1. Beachten Sie, dass die Jahre zu Monaten aufgeklappt und dass die Monate nicht chronologisch, sondern alphabetisch sortiert sind.

    ![Bild 20](Images/create-dax-calculations-in-power-bi-desktop_image27.png)

    > *Standardmäßig werden Textwerte alphabetisch, Zahlen vom kleinsten bis zum größten Wert und Datumsangaben vom frühesten bis zum spätesten Wert sortiert.*

1. Um die Sortierreihenfolge des Felds **Month** anzupassen, wechseln Sie zur Tabellenansicht.

1. Fügen Sie die Spalte **MonthKey** zur Tabelle **Date** hinzu.

    ```DAX
    MonthKey =
    (YEAR('Date'[Date]) * 100) + MONTH('Date'[Date])
    ```

    > *Diese Formel berechnet für jede Kombination aus Jahr/Monat einen numerischen Wert.*

1. Überprüfen Sie in der Tabellenansicht, ob die neue Spalte numerische Werte enthält (z. B. 201707 für Juli 2017).

    ![Bild 21](Images/create-dax-calculations-in-power-bi-desktop_image28.png)

1. Wechseln Sie zurück zur Berichtsansicht. Wählen Sie im Bereich **Daten** die Option **Monat** aus.

1. Klicken Sie auf dem kontextbezogenen Menüband **Spaltentools** innerhalb der Gruppe **Sortieren** auf **Nach Spalte sortieren**, und wählen Sie dann **MonthKey** aus.

    ![Bild 22](Images/create-dax-calculations-in-power-bi-desktop_image29.png)

1. Beachten Sie im Matrixvisual, dass die Monate jetzt chronologisch sortiert sind.

    ![Bild 23](Images/create-dax-calculations-in-power-bi-desktop_image30.png)

## Vervollständigen der Tabelle „Date“

In dieser Aufgabe vervollständigen Sie den Entwurf der Tabelle **Date**, indem Sie eine Spalte ausblenden und eine Hierarchie erstellen. Sie erstellen dann Beziehungen zu den Tabellen **Sales** und **Targets**.

1. Wechseln Sie zur Ansicht „Modell“. Blenden Sie in der Tabelle **Date** die Spalte **MonthKey** aus (legen Sie **Ausgeblendet** auf **Ja** fest).

1. Wählen Sie im Bereich **Daten** rechts die Tabelle **Date** aus, klicken Sie mit der rechten Maustaste auf die Spalte **Year**, und wählen Sie **Hierarchie erstellen** aus.

1. Benennen Sie die neu erstellte Hierarchie mit **Fiscal**, indem Sie mit der rechten Maustaste darauf klicken und **Umbenennen** auswählen.

1. Fügen Sie der Hierarchie „Fiscal“ die folgenden beiden verbleibenden Felder hinzu, indem Sie sie im Bereich **Daten** auswählen, mit der rechten Maustaste darauf klicken und **Zu Hierarchie hinzufügen** -> **Fiscal** auswählen.

    - Quarter
    - Month

    ![Bild 24](Images/create-dax-calculations-in-power-bi-desktop_image31.png)

1. Erstellen Sie die beiden folgenden Modellbeziehungen:

    - **Date \| Date** mit **Sales \| OrderDate**
    - **Date \| Date** mit **Targets \| TargetMonth**


    > *In den Labs wird eine verkürzte Notation verwendet, um auf ein Feld zu verweisen. Das sieht folgendermaßen aus: **Sales \| Unit Price**. In diesem Beispiel ist **Sales** der Tabellenname und **Unit Price** der Feldname.*

1. Blenden Sie die folgenden beiden Spalten aus:

    - Sales \| OrderDate
    - Targets \| TargetMonth

## Markieren der „Date“-Tabelle

In dieser Aufgabe markieren Sie die Tabelle **Date** als Datumstabelle.

1. Wechseln Sie zur Ansicht „Bericht“. Wählen Sie im Bereich **Daten** die Tabelle **Date** und nicht das Feld **Date** aus.

1. Wählen Sie im kontextbezogenen Menüband **Tabellentools** innerhalb der Gruppe **Kalender** die Option **Als Datumstabelle markieren**.

1. Schieben Sie im Fenster **Als Datumstabelle markieren** die Eigenschaft **Als Datumstabelle markieren** auf **Ja** und wählen Sie in der Dropdown-Liste **Datumsspalte auswählen** die Option **Datum**. Wählen Sie **Speichern**.

    ![Als Datumstabelle markieren](Images/create-dax-calculations-in-power-bi-desktop_date-table.png)

1. Speichern Sie die Power BI Desktop-Datei.

> *Power BI Desktop versteht jetzt, dass diese Tabelle Datumsangaben (Zeit) definiert. Dieser Entwurfsansatz ist für eine Datumstabelle geeignet, wenn in Ihrer Datenquelle keine Datumstabelle vorhanden ist. Wenn Sie ein Data Warehouse verwenden, wäre es angebracht, Datumsdaten aus dessen Datendimensionstabelle zu laden, anstatt die Datumslogik in Ihrem Datenmodell neu zu definieren.*

## Erstellen einfacher Measures

In dieser Aufgabe erstellen Sie einfache Measures. Einfache Measures aggregieren Werte in einer einzelnen Spalte oder zählen die Zeilen einer Tabelle.

1. Ziehen Sie in der Berichtsansicht auf **Seite 2** im Bereich **Daten** das Feld **Sales \| Unit Price** in das Matrixvisual.

    ![Bild 27](Images/create-dax-calculations-in-power-bi-desktop_image35.png)

1. Beachten Sie, dass im Bereich der Visualfelder (unterhalb des Bereichs **Visualisierungen**) im Bereich **Werte** der **Unit Price** als **Average of Unit Price** aufgeführt ist. Klicken Sie auf den nach unten zeigenden Pfeil für **Unit Price**, und sehen Sie sich die verfügbaren Menüoptionen an.

    ![Bild 30](Images/create-dax-calculations-in-power-bi-desktop_image37.png)

    > *Sichtbare numerische Spalten ermöglichen Berichtsautoren, zur Zeit der Berichtsgestaltung zu entscheiden, wie eine Spalte summiert wird (oder nicht). Dies kann zu einer ungeeigneten Berichterstellung führen. Einige Datenmodellierer überlassen Dinge jedoch nicht gerne dem Zufall und entscheiden sich dafür, diese Spalten auszublenden und stattdessen eine durch Measures definierte Aggregationslogik verfügbar zu machen. Dies ist der Ansatz, den Sie nun in diesem Lab verfolgen.*

1. Um ein Measure zu erstellen, klicken Sie im Bereich **Daten** mit der rechten Maustaste auf die Tabelle **Sales**, und wählen Sie dann **Neues Measure** aus.

1. Fügen Sie in der Bearbeitungsleiste die folgende Measuredefinition hinzu:

    ```DAX
    Avg Price =  
    AVERAGE(Sales[Unit Price])
    ```

1. Fügen Sie das Measure **Avg Price** zum Matrixvisual hinzu und beachten Sie, dass dadurch das gleiche Ergebnis wie in der Spalte **Unit Price** erzielt wird (jedoch mit anderer Formatierung).

1. Öffnen Sie unter **Werte** das Kontextmenü für das Feld **Avg Price**, und beachten Sie, dass die Aggregationsmethode nicht geändert werden kann.

    ![Bild 32](Images/create-dax-calculations-in-power-bi-desktop_image39.png)

    > *Es ist nicht möglich, das Aggregationsverhalten eines Measure zu ändern.*

1. Erstellen Sie mithilfe der Definitionen in der Codeausschnittsdatei die folgenden fünf Measures für die Tabelle **Sales**:

    - Median Price
    - Min Price
    - Max Price
    - Orders
    - Order Lines

    > *Die im Measure **Orders** verwendete DISTINCTCOUNT()-Funktion zählt Aufträge nur einmal (Duplikate werden ignoriert). Die im Measure **Order Lines** verwendete COUNTROWS()-Funktion wird auf eine Tabelle angewandt.*
    >
    > *In diesem Fall wird die Anzahl der Aufträge durch Zählen der eindeutigen Werte in der Spalte **SalesOrderNumber** berechnet, während die Anzahl der Auftragszeilen einfach die Anzahl der Tabellenzeilen ist (jede Zeile ist eine Auftragsposition).*

1. Wechseln Sie zur Ansicht „Modell“, und wählen Sie in einer Mehrfachauswahl die vier Measures für Preise aus: **Avg Price**, **Max Price**, **Median Price** und **Min Price**.

11. Konfigurieren Sie für die Mehrfachauswahl von Measures die folgenden Anforderungen:

    - Legen Sie das Format auf zwei Dezimalstellen fest.

    - Weisen Sie sie einem Anzeigeordner namens **Pricing** zu.

    ![Bild 33](Images/create-dax-calculations-in-power-bi-desktop_image40.png)

12. Blenden Sie die Spalte **Unit Price** aus.

    > *Die Spalte **Unit Price** ist jetzt für Berichtsautor*innen nicht mehr verfügbar. Sie müssen die Preismeasuresverwenden, die Sie dem Modell hinzugefügt haben. Dieser Entwurfsansatz stellt sicher, dass Berichtsautor*innen die Preise nicht unsachgemäß aggregieren, z. B. durch Addition.*

13. Wählen Sie in einer Mehrfachauswahl die Measures **Order Lines** und **Orders** aus, und konfigurieren Sie dann die folgenden Anforderungen:

    - Legen Sie das Format so fest, dass Tausendertrennzeichen verwendet werden.

    - Weisen Sie sie einem Anzeigeordner namens **Counts** zu.

    ![Bild 36](Images/create-dax-calculations-in-power-bi-desktop_image41.png)

14. Klicken Sie in der Berichtsansicht im Bereich **Werte** des visuellen Matrixelements für das Feld **Unit Price** auf **X**, um es zu entfernen.

15. Vergrößern Sie das Matrixvisual so, dass es die Seitenbreite und -höhe ausfüllt.

16. Fügen Sie dem Matrixvisual die folgenden fünf Measures hinzu:

    - Median Price
    - Min Price
    - Max Price
    - Orders
    - Order Lines

17. Stellen Sie sicher, dass die Ergebnisse sinnvoll aussehen und richtig formatiert sind.

    ![Bild 39](Images/create-dax-calculations-in-power-bi-desktop_image43.png)

## Erstellen zusätzlicher Measures

In dieser Aufgabe erstellen Sie weitere Measures, die komplexere Formeln verwenden.

1. Wählen Sie in der Berichtsansicht **Seite 1** aus, und überprüfen Sie das visuelle Tabellenelement, wobei Sie die Summe für die Spalte **Target** notieren.

    ![Bild 41](Images/create-dax-calculations-in-power-bi-desktop_image45.png)

1. Wählen Sie das Tabellenvisual aus, und entfernen Sie dann im Bereich **Visualisierungen** das Feld **Ziel**.

1. Benennen Sie die Spalte **Targets \| Target** in **Targets \| TargetAmount** um.

    > *Tipp: Es gibt mehrere Möglichkeiten, die Spalte in der Ansicht „Bericht“ umzubenennen: Im Bereich **Daten** können Sie mit der rechten Maustaste auf die Spalte klicken und dann **Umbenennen** auswählen. Alternativ können Sie auf die Spalte doppelklicken oder **F2** drücken.*

1. Erstellen Sie das folgende Measure für die Tabelle **Targets**:

    ```DAX
    Target =
    IF(
    HASONEVALUE('Salesperson (Performance)'[Salesperson]),
    SUM(Targets[TargetAmount])
    )
    ```

    > *Die Funktion „HASONEVALUE()“ prüft, ob ein einzelner Wert in der Spalte **Salesperson** gefiltert wird. Falls TRUE, gibt der Ausdruck die Summe der Zielbeträge (für genau diesen Vertriebsmitarbeiter) zurück. Bei „false“ wird „BLANK“ zurückgegeben.*

1. Formatieren Sie das Measure **Target** ohne Dezimalstellen.

    > *Tipp: Sie können das kontextbezogene Menüband **Measuretools** verwenden.*

1. Blenden Sie die Spalte **TargetAmount** aus.

    > *Tipp: Im Bereich **Daten** können Sie mit der rechten Maustaste auf die Spalte klicken und dann **Ausblenden** auswählen.*

1. Fügen Sie das Measure **Target** dem Tabellenvisual hinzu.

1. Beachten Sie, dass die Gesamtsumme der Spalte **Target** jetzt BLANK ist.

    ![Bild 43](Images/create-dax-calculations-in-power-bi-desktop_image47.png)

1. Erstellen Sie mithilfe der Definitionen in der Codeausschnittsdatei die folgenden zwei Measures für die Tabelle **Targets**:

    - Variance
    - Variance Margin

1. Formatieren Sie das Measure **Variance** ohne Dezimalstellen.

1. Formatieren Sie das Measure **Variance Margin** als Prozentsatz mit zwei Dezimalstellen.

1. Fügen Sie die Measures **Variance** und **Variance Margin** zum Tabellenvisual hinzu.

1. Ändern Sie die Größe des Tabellenvisuals, sodass alle Spalten und Zeilen angezeigt werden.

    ![Bild 44](Images/create-dax-calculations-in-power-bi-desktop_image48.png)

    > *Auch wenn es so aussieht, als würden nicht alle Vertriebsmitarbeitenden ihre Ziele erreichen, sollten Sie bedenken, dass die Tabelle noch nach keinem bestimmten Zeitraum gefiltert wurde.*

1. Klappen Sie rechts oben im Bereich **Daten** den Bereich zu, und klappen Sie ihn dann auf.

    > *Durch das Zuklappen und erneute Öffnen des Bereichs wird der Inhalt zurückgesetzt.*

1. Beachten Sie, dass die Tabelle **Targets** jetzt oben in der Liste steht.

    ![Bild 46](Images/create-dax-calculations-in-power-bi-desktop_image50.png)

    *Tabellen, die nur sichtbare Measures enthalten, werden automatisch am Anfang der Liste angezeigt.*

## Lab abgeschlossen
