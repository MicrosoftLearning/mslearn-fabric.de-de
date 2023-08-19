---
lab:
  title: Trainieren eines Klassifizierungsmodells zur Vorhersage der Kundenabwanderung
  module: Get started with data science in Microsoft Fabric
---

# Verwenden von Notebooks zum Trainieren eines Modells in Microsoft Fabric

In diesem Lab verwenden wir Microsoft Fabric, um ein Notebook zu erstellen und ein Machine Learning-Modell zu trainieren, das die Kundenabwanderung vorhersagt. Wir werden Scikit-Learn verwenden, um das Modell zu trainieren, und MLflow, um seine Leistung zu verfolgen. Kundenabwanderung ist ein kritisches Geschäftsproblem, mit dem viele Unternehmen konfrontiert sind, und die Vorhersage, welche Kunden wahrscheinlich abwandern werden, kann Unternehmen helfen, ihre Kunden zu halten und den Umsatz zu steigern. Bei diesem Lab sammeln Sie praktische Erfahrung mit maschinellem Lernen und der Modellnachverfolgung und lernen, wie Sie mit Microsoft Fabric ein Notebook für Ihre Projekte erstellen können.

Dieses Lab dauert ungefähr **45** Minuten.

> **Hinweis:** Sie benötigen eine Microsoft Fabric-Lizenz, um diese Übung durchführen zu können. Weitere Informationen zum Aktivieren einer kostenlosen Fabric-Testlizenz finden Sie unter [Erste Schritte mit Fabric](https://learn.microsoft.com/fabric/get-started/fabric-trial). Dazu benötigen Sie ein *Schul-* , *Geschäfts-* oder Unikonto von Microsoft. Wenn Sie über kein Microsoft-Konto verfügen, können Sie sich [für eine kostenlose Testversion von Microsoft Office 365 E3 oder höher registrieren](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Melden Sie sich bei [Microsoft Fabric](https://app.fabric.microsoft.com) unter `https://app.fabric.microsoft.com` an, und wählen Sie **Power BI** aus.
2. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
3. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
4. Beim Öffnen Ihres neuen Arbeitsbereichs sollte dieser wie im Folgenden gezeigt leer sein:

    ![Screenshot eines leeren Arbeitsbereichs in Power BI](./Images/new-workspace.png)

## Erstellen eines Lakehouse und Hochladen von Dateien

Nachdem Sie nun über einen Arbeitsbereich verfügen, können Sie zu *Data Science* im Portal wechseln und ein Data Lakehouse für die zu analysierenden Datendateien erstellen.

1. Wählen Sie unten links im Power BI-Portal das **Power BI**-Symbol aus, und wechseln Sie zu **Datentechnik**.
1. Erstellen Sie auf der Startseite **Datentechnik** ein neues **Lakehouse** mit einem Namen Ihrer Wahl.

    Nach etwa einer Minute wird ein neues Lakehouse ohne **Tabellen** oder **Dateien** erstellt. Sie müssen einige Daten für die Analyse in das Data Lakehouse einfügen. Es gibt mehrere Möglichkeiten, dies zu tun, aber in dieser Übung laden Sie einfach einen Ordner mit Textdateien auf Ihrem lokalen Computer (oder ggf. einer Lab-VM) herunter, extrahieren sie und laden sie dann in Ihr Lakehouse hoch.

1. Laden Sie die `churn.csv`-CSV-Datei für diese Übung von herunter, und speichern Sie sie unter [https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/churn.csv](https://raw.githubusercontent.com/MicrosoftLearning/dp-data/main/churn.csv).


1. Kehren Sie zur Webbrowser-Registerkarte mit Ihrem Lakehouse zurück, und wählen Sie im Menü **...** für den Knoten **Dateien** im Bereich **Lake-Ansicht** die Optionen **Hochladen** und **Dateien hochladen** aus. Laden Sie dann die Datei **churn.csv** von Ihrem lokalen Computer (oder ggf. Ihrer Lab-VM) in das Lakehouse hoch.
6. Erweitern Sie nach dem Hochladen der Dateien den Eintrag **Dateien**, und überprüfen Sie, ob die CSV-Datei hochgeladen wurde.

## Erstellen eines Notebooks

Um ein Modell zu trainieren, können Sie ein *Notebook* erstellen. Notebooks bieten eine interaktive Umgebung, in der Sie Code (in mehreren Sprachen) als *Experimente* schreiben und ausführen können.

1. Wählen Sie unten links im Power BI-Portal das **Datentechnik**-Symbol aus, und wechseln Sie zu **Data Science**.

1. Erstellen Sie auf der **Data Science**-Startseite ein neues **Notebook**.

    Nach einigen Sekunden wird ein neues Notebook mit einer einzelnen *Zelle* geöffnet. Notebooks bestehen aus einer oder mehreren Zellen, die *Code* oder *Markdown* (formatierten Text) enthalten können.

1. Wählen Sie die erste Zelle aus (die derzeit eine *Codezelle* ist), und verwenden Sie dann auf der dynamischen Symbolleiste oben rechts die Schaltfläche **M&#8595;** , um die Zelle in eine *Markdownzelle* zu konvertieren.

    Wenn die Zelle in eine Markdownzelle geändert wird, wird der enthaltene Text gerendert.

1. Verwenden Sie die Schaltfläche **&#128393;** (Bearbeiten), um die Zelle in den Bearbeitungsmodus zu versetzen, löschen Sie dann den Inhalt und geben Sie den folgenden Text ein:

    ```text
   # Train a machine learning model and track with MLflow

   Use the code in this notebook to train and track models.
    ``` 

## Laden von Daten in einen Dataframe

Jetzt können Sie Code ausführen, um Daten aufzubereiten und ein Modell zu trainieren. Um mit Daten zu arbeiten, verwenden Sie *Dataframes*. Dataframes in Spark ähneln Pandas-Dataframes in Python und bieten eine gemeinsame Struktur für die Arbeit mit Daten in Zeilen und Spalten.

1. Wählen Sie im Bereich **Lakehouse hinzufügen** die Option **Hinzufügen** aus, um ein Lakehouse hinzuzufügen.
1. Wählen Sie **Vorhandenes Lakehouse** und **Hinzufügen** aus.
1. Wählen Sie das Lakehouse aus, das Sie in einem vorherigen Abschnitt erstellt haben.
1. Erweitern Sie den Ordner **Dateien**, sodass die CSV-Datei neben dem Notebook-Editor aufgeführt wird.
1. Wählen Sie im Menü **...** für **churn.csv** die Option **Daten laden** > **Pandas** aus. Dem Notebook sollte eine neue Codezelle mit folgendem Code hinzugefügt werden:

    ```python
   import pandas as pd
   # Load data into pandas DataFrame from "/lakehouse/default/" + "Files/churn.csv"
   df = pd.read_csv("/lakehouse/default/" + "Files/churn.csv")
   display(df)
    ```

    > **Tipp**: Sie können den Bereich mit den Dateien auf der linken Seite ausblenden, indem Sie das **<<** -Symbol verwenden. Dies hilft Ihnen, sich auf das Notebook zu konzentrieren.

1. Verwenden Sie die Schaltfläche **&#9655; Zelle ausführen** links neben der Zelle, um diese auszuführen.

    > **Hinweis**: Da Sie Spark-Code zum ersten Mal in dieser Sitzung ausführen, muss der Spark-Pool gestartet werden. Dies bedeutet, dass die erste Ausführung in der Sitzung etwa eine Minute dauern kann. Nachfolgende Ausführungen erfolgen schneller.

1. Wenn der Zellenbefehl abgeschlossen ist, überprüfen Sie die Ausgabe unterhalb der Zelle, die wie folgt aussehen sollte:

    |Index|CustomerID|years_with_company|total_day_calls|total_eve_calls|total_night_calls|total_intl_calls|average_call_minutes|total_customer_service_calls|age|Churn|
    | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
    |1|1000038|0|117|88|32|607|43.90625678|0.810828179|34|0|
    |2|1000183|1|164|102|22|40|49.82223317|0.294453889|35|0|
    |3|1000326|3|116|43|45|207|29.83377967|1.344657937|57|1|
    |4|1000340|0|92|24|11|37|31.61998183|0.124931779|34|0|
    | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

    Die Ausgabe zeigt die Zeilen und Spalten der Kundendaten aus der churn.csv-Datei an.

## Trainieren eines Machine Learning-Modells

Nachdem Sie die Daten geladen haben, können Sie sie verwenden, um ein Machine Learning-Modell zu trainieren und Kundenabwanderungen vorherzusagen. Sie trainieren ein Modell mithilfe der Scikit-Learn-Bibliothek und verfolgen das Modell mit MLflow nach. 

1. Verwenden Sie das Symbol **+ Code** unterhalb der Zellenausgabe, um dem Notebook eine neue Codezelle hinzuzufügen, und geben Sie darin den folgenden Code ein:

    ```python
   from sklearn.model_selection import train_test_split

   print("Splitting data...")
   X, y = df[['years_with_company','total_day_calls','total_eve_calls','total_night_calls','total_intl_calls','average_call_minutes','total_customer_service_calls','age']].values, df['churn'].values
   
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.30, random_state=0)
    ```

1. Führen Sie die hinzugefügte Codezelle aus, und beachten Sie, dass Sie „CustomerID“ aus dem Dataset weglassen und die Daten in ein Trainings- und Testdataset aufteilen.
1. Fügen Sie dem Notebook eine weitere neue Codezelle hinzu, geben Sie den folgenden Code darin ein, und führen Sie ihn aus:
    
    ```python
   import mlflow
   experiment_name = "experiment-churn"
   mlflow.set_experiment(experiment_name)
    ```
    
    Der Code erstellt ein MLflow-Experiment namens `experiment-churn`. Ihre Modelle werden in diesem Experiment nachverfolgt.

1. Fügen Sie dem Notebook eine weitere neue Codezelle hinzu, geben Sie den folgenden Code darin ein, und führen Sie ihn aus:

    ```python
   from sklearn.linear_model import LogisticRegression
   
   with mlflow.start_run():
       mlflow.autolog()

       model = LogisticRegression(C=1/0.1, solver="liblinear").fit(X_train, y_train)

       mlflow.log_param("estimator", "LogisticRegression")
    ```
    
    Der Code trainiert ein Klassifizierungsmodell mithilfe der logistischen Regression. Parameter, Metriken und Artefakte werden automatisch mit MLflow protokolliert. Darüber hinaus protokollieren Sie einen Parameter namens `estimator`mit dem Wert `LogisticRegression`.

1. Fügen Sie dem Notebook eine weitere neue Codezelle hinzu, geben Sie den folgenden Code darin ein, und führen Sie ihn aus:

    ```python
   from sklearn.tree import DecisionTreeClassifier
   
   with mlflow.start_run():
       mlflow.autolog()

       model = DecisionTreeClassifier().fit(X_train, y_train)
   
       mlflow.log_param("estimator", "DecisionTreeClassifier")
    ```

    Der Code trainiert ein Klassifizierungsmodell mithilfe des Entscheidungsstrukturklassifizierers. Parameter, Metriken und Artefakte werden automatisch mit MLflow protokolliert. Darüber hinaus protokollieren Sie einen Parameter namens `estimator`mit dem Wert `DecisionTreeClassifier`.

## Verwenden von MLflow zum Suchen und Anzeigen Ihrer Experimente

Wenn Sie Modelle mit MLflow trainiert und nachverfolgt haben, können Sie die MLflow-Bibliothek verwenden, um Ihre Experimente und die zugehörigen Details abzurufen.

1. Verwenden Sie den folgenden Code, um alle Experimente aufzulisten:

    ```python
   import mlflow
   experiments = mlflow.search_experiments()
   for exp in experiments:
       print(exp.name)
    ```

1. Ein bestimmtes Experiment können Sie anhand seines Namens abrufen:

    ```python
   experiment_name = "experiment-churn"
   exp = mlflow.get_experiment_by_name(experiment_name)
   print(exp)
    ```

1. Mithilfe eines Experimentnamens können Sie alle Aufträge dieses Experiments abrufen:

    ```python
   mlflow.search_runs(exp.experiment_id)
    ```

1. Um Auftragsausführungen und -ausgaben einfacher zu vergleichen, können Sie die Suche so konfigurieren, dass die Ergebnisse sortiert werden. Die folgende Zelle sortiert die Ergebnisse beispielsweise anhand von `start_time` und zeigt maximal `2` Ergebnisse: 

    ```python
   mlflow.search_runs(exp.experiment_id, order_by=["start_time DESC"], max_results=2)
    ```

1. Schließlich können Sie die Auswertungsmetriken mehrerer Modelle nebeneinander zeichnen, um Modelle einfach zu vergleichen:

    ```python
   import matplotlib.pyplot as plt
   
   df_results = mlflow.search_runs(exp.experiment_id, order_by=["start_time DESC"], max_results=2)[["metrics.training_accuracy_score", "params.estimator"]]
   
   fig, ax = plt.subplots()
   ax.bar(df_results["params.estimator"], df_results["metrics.training_accuracy_score"])
   ax.set_xlabel("Estimator")
   ax.set_ylabel("Accuracy")
   ax.set_title("Accuracy by Estimator")
   for i, v in enumerate(df_results["metrics.training_accuracy_score"]):
       ax.text(i, v, str(round(v, 2)), ha='center', va='bottom', fontweight='bold')
   plt.show()
    ```

    Die Ausgabe sollte dem folgenden Bild entsprechen:

    ![Screenshot der gezeichneten Auswertungsmetriken](./Images/plotted-metrics.png)

## Erkunden der Experimente

Mit Microsoft Fabric können Sie alle Ihre Experimente nachverfolgen und visuell untersuchen.

1. Navigieren Sie zur Startseite **Data Science**.
1. Wählen Sie das `experiment-churn`-Experiment aus, um es zu öffnen.

    > **Tipp:** Wenn keine protokollierten Experimentausführungen angezeigt werden, aktualisieren Sie die Seite.

1. Wählen Sie die Registerkarte **Ansicht** aus.
1. Wählen Sie **Ausführungsliste** aus. 
1. Wählen Sie die beiden neuesten Ausführungen aus, indem Sie jedes Kontrollkästchen aktivieren.
    Dadurch werden Ihre beiden letzten Ausführungen im Bereich **Metrikvergleich** miteinander verglichen. Standardmäßig werden die Metriken nach Ausführungsname gezeichnet. 
1. Wählen Sie die Schaltfläche **&#128393;** (Bearbeiten) des Diagramms aus, um die Genauigkeit für jede Ausführung zu visualisieren. 
1. Ändern Sie den **Visualisierungstyp** in `bar`. 
1. Ändern Sie die **X-Achse** in `estimator`. 
1. Wählen Sie **Ersetzen** aus, und erkunden Sie das neue Diagramm.

Indem Sie die Genauigkeit pro protokolliertem Schätzer darstellen, können Sie überprüfen, welcher Algorithmus zu einem besseren Modell geführt hat.

## Speichern des Modells

Nach dem Vergleichen von Machine Learning-Modellen, die Sie experimentübergreifend trainiert haben, können Sie das Modell mit der besten Leistung auswählen. Um das Modell mit der besten Leistung zu verwenden, speichern Sie das Modell, und verwenden Sie es, um Vorhersagen zu generieren.

1. Stellen Sie in der Experimentübersicht sicher, dass die Registerkarte **Ansicht** ausgewählt ist.
1. Wählen Sie **Ausführungsdetails** aus.
1. Wählen Sie die Ausführung mit der höchsten Genauigkeit aus. 
1. Wählen Sie im Feld **Als Modell speichern** die Option **Speichern** aus.
1. Wählen Sie im neu geöffneten Popupfenster die Option **Neues Modell erstellen** aus.
1. Nennen Sie das Modell `model-churn`, und wählen Sie **Erstellen** aus. 
1. Wählen Sie **Modell anzeigen** in der Benachrichtigung aus, die beim Erstellen des Modells oben rechts auf dem Bildschirm angezeigt wird. Sie können das Fenster auch aktualisieren. Das gespeicherte Modell ist unter **Registrierte Version** verknüpft. 

Beachten Sie, dass das Modell, das Experiment und die Experimentausführung verknüpft sind, sodass Sie überprüfen können, wie das Modell trainiert wird. 

## Speichern des Notebooks und Beenden der Spark-Sitzung

Nachdem Sie das Training und die Auswertung der Modelle abgeschlossen haben, können Sie das Notebook unter einem aussagekräftigen Namen speichern und die Spark-Sitzung beenden.

1. Verwenden Sie in der Menüleiste des Notebooks das Symbol ⚙️ **Einstellungen**, um die Einstellungen des Notebooks anzuzeigen.
2. Legen Sie den **Namen** des Notebooks auf **Trainieren und Vergleichen von Modellen** fest, und schließen Sie dann den Einstellungsbereich.
3. Wählen Sie im Notebookmenü **Sitzung beenden** aus, um die Spark-Sitzung zu beenden.

## Bereinigen von Ressourcen

In dieser Übung haben Sie ein Notebook erstellt und ein Machine Learning-Modell trainiert. Sie haben Scikit-Learn verwendet, um das Modell zu trainieren, und MLflow, um seine Leistung zu verfolgen.

Wenn Sie das Modell und die Experimente abgeschlossen haben, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus, um alle darin enthaltenen Elemente anzuzeigen.
2. Wählen Sie im Menü **...** auf der Symbolleiste die **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Andere** die Option **Diesen Arbeitsbereich entfernen** aus.
