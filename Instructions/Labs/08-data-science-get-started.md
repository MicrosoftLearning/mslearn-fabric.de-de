---
lab:
  title: Grundlegendes zu Data Science in Microsoft Fabric
  module: Get started with data science in Microsoft Fabric
---

# Grundlegendes zu Data Science in Microsoft Fabric

In diesem Lab erfassen Sie Daten, untersuchen die Daten in einem Notebook, verarbeiten die Daten mit dem Data Wrangler und trainieren zwei Arten von Modellen. Durch das Befolgen dieser Schritte lernen Sie die Data Science-Features in Microsoft Fabric kennen.

Bei diesem Lab sammeln Sie praktische Erfahrung mit maschinellem Lernen und der Modellnachverfolgung und lernen, wie Sie in Microsoft Fabric mit *Notebooks*, *Data Wrangler*, *Experimenten* und *Modellen* arbeiten.

Dieses Lab dauert ungefähr **20** Minuten.

> **Hinweis:** Für diese Übung benötigen Sie ein *Geschäfts-*, *Schul- oder Unikonto* von Microsoft. Wenn Sie über kein Microsoft-Konto verfügen, können Sie sich [für eine kostenlose Testversion von Microsoft Office 365 E3 oder höher registrieren](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Navigieren Sie in einem Browser unter [https://app.fabric.microsoft.com](https://app.fabric.microsoft.com) zur Willkommensseite von Microsoft Fabric.
1. Wählen Sie **Synapse Data Science** aus.
1. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein.

    ![Screenshot eines leeren Arbeitsbereichs in Fabric](./Images/new-workspace.png)

## Erstellen eines Notebooks

Zum Ausführen von Code können Sie ein *Notebook* erstellen. Notebooks sind eine interaktive Umgebung, in der Sie Code (in mehreren Sprachen) schreiben und ausführen können.

1. Erstellen Sie auf der Startseite von **Data Science mit Synapse** ein neues **Notebook**.

    Nach einigen Sekunden wird ein neues Notebook mit einer einzelnen *Zelle* geöffnet. Notebooks bestehen aus einer oder mehreren Zellen, die *Code* oder *Markdown* (formatierten Text) enthalten können.

1. Wählen Sie die erste Zelle aus (die derzeit eine *Codezelle* ist), und verwenden Sie dann auf der dynamischen Symbolleiste oben rechts die Schaltfläche **M&#8595;** , um die Zelle in eine *Markdownzelle* zu konvertieren.

    Wenn die Zelle in eine Markdownzelle geändert wird, wird der enthaltene Text gerendert.

1. Verwenden Sie die Schaltfläche **&#128393;** (Bearbeiten), um die Zelle in den Bearbeitungsmodus zu versetzen, löschen Sie dann den Inhalt und geben Sie den folgenden Text ein:

    ```text
   # Data science in Microsoft Fabric
    ```

## Abrufen von Daten

Jetzt können Sie Code ausführen, um Daten abzurufen und ein Modell zu trainieren. Sie arbeiten mit dem [Diabetesdataset](https://learn.microsoft.com/azure/open-datasets/dataset-diabetes?tabs=azureml-opendatasets?azure-portal=true) aus Azure Open Datasets. Nachdem Sie die Daten geladen haben, konvertieren Sie diese in einen Pandas-Dataframe. Dabei handelt es sich um eine gängige Struktur für die Arbeit mit Daten in Zeilen und Spalten.

1. Verwenden Sie in Ihrem Notebook das Symbol **+ Code** unter der neuesten Zellenausgabe, um dem Notebook eine neue Codezelle hinzuzufügen.

    > **Tipp**: Um das Symbol **+ Code** anzuzeigen, zeigen Sie mit dem Mauszeiger direkt links unter die Ausgabe der aktuellen Zelle. Alternativ können Sie auf der Menüleiste auf der Registerkarte **Bearbeiten** die Option **+ Codezelle hinzufügen** auswählen.

1. Geben Sie den folgenden Code in die neue Codezelle ein:

    ```python
   # Azure storage access info for open dataset diabetes
   blob_account_name = "azureopendatastorage"
   blob_container_name = "mlsamples"
   blob_relative_path = "diabetes"
   blob_sas_token = r"" # Blank since container is Anonymous access
    
   # Set Spark config to access  blob storage
   wasbs_path = f"wasbs://%s@%s.blob.core.windows.net/%s" % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set("fs.azure.sas.%s.%s.blob.core.windows.net" % (blob_container_name, blob_account_name), blob_sas_token)
   print("Remote blob path: " + wasbs_path)
    
   # Spark read parquet, note that it won't load any data yet by now
   df = spark.read.parquet(wasbs_path)
    ```

1. Verwenden Sie die Schaltfläche **&#9655; Zelle ausführen** links neben der Zelle, um diese auszuführen. Alternativ können Sie `SHIFT` + `ENTER` auf Ihrer Tastatur drücken, um eine Zelle auszuführen.

    > **Hinweis**: Da Sie Spark-Code zum ersten Mal in dieser Sitzung ausführen, muss der Spark-Pool gestartet werden. Dies bedeutet, dass die erste Ausführung in der Sitzung etwa eine Minute dauern kann. Nachfolgende Ausführungen erfolgen schneller.

1. Verwenden Sie das Symbol **+ Code** unterhalb der Zellenausgabe, um dem Notebook eine neue Codezelle hinzuzufügen, und geben Sie darin den folgenden Code ein:

    ```python
   display(df)
    ```

1. Wenn der Zellenbefehl abgeschlossen ist, überprüfen Sie die Ausgabe unterhalb der Zelle, die wie folgt aussehen sollte:

    |AGE|SEX|BMI|BP|S1|S2|S3|S4|S5|S6|J|
    |---|---|---|--|--|--|--|--|--|--|--|
    |59|2|32,1|101.0|157|93.2|38.0|4,0|4.8598|87|151|
    |48|1|21.6|87,0|183|103.2|70,0|3.0|3.8918|69|75|
    |72|2|30.5|93.0|156|93.6|41.0|4,0|4.6728|85|141|
    |24|1|25.3|84.0|198|131.4|40.0|5.0|4.8903|89|206|
    |50|1|23.0|101.0|192|125,4|52.0|4,0|4.2905|80|135|
    | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

    Die Ausgabe zeigt die Zeilen und Spalten des Diabetesdatasets.

1. Im oberen Bereich der gerenderten Tabelle befinden sich zwei Registerkarten: **Tabelle** und **Diagramm**. Wählen Sie **Diagramm** aus.
1. Wählen Sie rechts oben im Diagramm **Optionen anzeigen** aus, um die Visualisierung zu ändern.
1. Ändern Sie die folgenden Einstellungen für das Diagramm:
    * **Diagrammtyp**: `Box plot`
    * **Schlüssel**: *leer lassen*
    * **Werte**: `Y`
1. Wählen Sie **Anwenden** aus, um die neue Visualisierung zu rendern und die Ausgabe zu untersuchen.

## Vorbereiten der Daten

Nachdem Sie die Daten erfasst und untersucht haben, können Sie sie transformieren. Sie können entweder Code in einem Notebook ausführen oder den Data Wrangler verwenden, um Code für Sie zu generieren.

1. Die Daten werden als Spark-Dataframe geladen. Zum Starten des Data Wrangler müssen Sie die Daten in einen Pandas-Dataframe konvertieren. Führen Sie den folgenden Code in Ihrem Notebook aus:

    ```python
   df = df.toPandas()
   df.head()
    ```

1. Wählen Sie im Menüband des Notebooks die Option **Daten** und dann die Dropdownliste **Data Wrangler starten** aus.
1. Wählen Sie das Dataset `df` aus. Beim Starten von Data Wrangler wird im Bereich **Zusammenfassung** eine beschreibende Übersicht über den angezeigten Dataframe generiert.

    Derzeit ist die Bezeichnungsspalte `Y`, also eine fortlaufende Variable. Um ein Machine Learning-Modell zu trainieren, das Y vorhersagt, müssen Sie ein Regressionsmodell trainieren. Die (vorhergesagten) Werte von Y sind möglicherweise schwer zu interpretieren. Stattdessen könnten wir das Training eines Klassifizierungsmodells untersuchen, das vorhersagt, ob jemand ein niedriges oder hohes Risiko für die Entwicklung von Diabetes aufweist. Um ein Klassifizierungsmodell trainieren zu können, müssen Sie eine binäre Bezeichnungsspalte basierend auf den Werten aus `Y` erstellen.

1. Wählen Sie die Spalte `Y` im Data Wrangler aus. Beachten Sie, dass die Häufigkeit für den `220-240`-Bin verringert wird. Das 75. Perzentil `211.5` entspricht ungefähr dem Übergang der beiden Regionen im Histogramm. Verwenden wir diesen Wert als Schwellenwert für niedriges und hohes Risiko.
1. Navigieren Sie zum Bereich **Vorgänge**, erweitern Sie **Formeln**, und wählen Sie dann **Spalte aus Formel erstellen** aus.
1. Erstellen Sie eine neue Spalte mit den folgenden Einstellungen:
    * **Spaltenname**: `Risk`
    * **Spaltenformel**: `(df['Y'] > 211.5).astype(int)`
1. Überprüfen Sie die neue Spalte `Risk`, die der Vorschau hinzugefügt wird. Vergewissern Sie sich, dass die Anzahl der Zeilen mit dem Wert `1` ungefähr 25 % aller Zeilen beträgt (da es sich um das 75. Perzentil von `Y` handelt).
1. Wählen Sie **Übernehmen**.
1. Wählen Sie **Code zu Notebook hinzufügen** aus.
1. Führen Sie die Zelle mit dem Code aus, der vom Data Wrangler generiert wird.
1. Führen Sie den folgenden Code in einer neuen Zelle aus, um zu überprüfen, ob die Spalte `Risk` wie erwartet aussieht:

    ```python
   df_clean.describe()
    ```

## Trainieren von Machine Learning-Modellen

Nachdem Sie die Daten aufbereitet haben, können Sie sie verwenden, um ein Machine Learning-Modell zu trainieren, das Diabetes vorhersagt. Wir können zwei verschiedene Modelltypen mit unserem Dataset trainieren: ein Regressionsmodell (Vorhersage von `Y`) oder ein Klassifizierungsmodell (Vorhersage von `Risk`). Sie trainieren die Modelle mithilfe der Scikit-learn-Bibliothek und verfolgen das Modell mit MLflow nach.

### Trainieren eines Regressionsmodells

1. Führen Sie den folgenden Code aus, um die Daten in ein Trainings- und Testdataset aufzuteilen und die Features von der Bezeichnung `Y` zu trennen, die Sie vorhersagen möchten:

    ```python
   from sklearn.model_selection import train_test_split
    
   X, y = df_clean[['AGE','SEX','BMI','BP','S1','S2','S3','S4','S5','S6']].values, df_clean['Y'].values
    
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.30, random_state=0)
    ```

1. Fügen Sie dem Notebook eine weitere neue Codezelle hinzu, geben Sie den folgenden Code darin ein, und führen Sie ihn aus:

    ```python
   import mlflow
   experiment_name = "diabetes-regression"
   mlflow.set_experiment(experiment_name)
    ```

    Der Code erstellt ein MLflow-Experiment namens `diabetes-regression`. Ihre Modelle werden in diesem Experiment nachverfolgt.

1. Fügen Sie dem Notebook eine weitere neue Codezelle hinzu, geben Sie den folgenden Code darin ein, und führen Sie ihn aus:

    ```python
   from sklearn.linear_model import LinearRegression
    
   with mlflow.start_run():
      mlflow.autolog()
    
      model = LinearRegression()
      model.fit(X_train, y_train)
    ```

    Der Code trainiert ein Regressionsmodell mithilfe der linearen Regression. Parameter, Metriken und Artefakte werden automatisch mit MLflow protokolliert.

### Trainieren eines Klassifizierungsmodells

1. Führen Sie den folgenden Code aus, um die Daten in ein Trainings- und Testdataset aufzuteilen und die Features von der Bezeichnung `Risk` zu trennen, die Sie vorhersagen möchten:

    ```python
   from sklearn.model_selection import train_test_split
    
   X, y = df_clean[['AGE','SEX','BMI','BP','S1','S2','S3','S4','S5','S6']].values, df_clean['Risk'].values
    
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.30, random_state=0)
    ```

1. Fügen Sie dem Notebook eine weitere neue Codezelle hinzu, geben Sie den folgenden Code darin ein, und führen Sie ihn aus:

    ```python
   import mlflow
   experiment_name = "diabetes-classification"
   mlflow.set_experiment(experiment_name)
    ```

    Der Code erstellt ein MLflow-Experiment namens `diabetes-classification`. Ihre Modelle werden in diesem Experiment nachverfolgt.

1. Fügen Sie dem Notebook eine weitere neue Codezelle hinzu, geben Sie den folgenden Code darin ein, und führen Sie ihn aus:

    ```python
   from sklearn.linear_model import LogisticRegression
    
   with mlflow.start_run():
       mlflow.sklearn.autolog()

       model = LogisticRegression(C=1/0.1, solver="liblinear").fit(X_train, y_train)
    ```

    Der Code trainiert ein Klassifizierungsmodell mithilfe der logistischen Regression. Parameter, Metriken und Artefakte werden automatisch mit MLflow protokolliert.

## Erkunden der Experimente

Mit Microsoft Fabric können Sie alle Ihre Experimente nachverfolgen und visuell untersuchen.

1. Navigieren Sie über die Hubmenüleiste auf der linken Seite zu Ihrem Arbeitsbereich.
1. Wählen Sie das `diabetes-regression`-Experiment aus, um es zu öffnen.

    > **Tipp:** Wenn keine protokollierten Experimentausführungen angezeigt werden, aktualisieren Sie die Seite.

1. Sehen Sie sich die **Ausführungsmetriken** an, um die Genauigkeit Ihres Regressionsmodells zu ermitteln.
1. Navigieren Sie zurück zur Startseite, und wählen Sie das Experiment `diabetes-classification` aus, um es zu öffnen.
1. Sehen Sie sich die **Ausführungsmetriken** an, um die Genauigkeit des Klassifizierungsmodells zu ermitteln. Es sind andere Metriken vorhanden, da Sie einen anderen Modelltyp trainiert haben.

## Speichern des Modells

Nach dem Vergleichen der Machine Learning-Modelle, die Sie experimentübergreifend trainiert haben, können Sie das Modell mit der besten Leistung auswählen. Um das Modell mit der besten Leistung zu verwenden, speichern Sie das Modell, und verwenden Sie es, um Vorhersagen zu generieren.

1. Wählen Sie im Feld **Als Modell speichern** die Option **Speichern** aus.
1. Wählen Sie im neu geöffneten Popupfenster die Option **Neues Modell erstellen** aus.
1. Wählen Sie den Ordner `model` aus.
1. Nennen Sie das Modell `model-diabetes`, und wählen Sie **Speichern** aus.
1. Wählen Sie **Modell anzeigen** in der Benachrichtigung aus, die beim Erstellen des Modells oben rechts auf dem Bildschirm angezeigt wird. Sie können das Fenster auch aktualisieren. Das gespeicherte Modell ist unter **Modellversionen** verknüpft.

Beachten Sie, dass das Modell, das Experiment und die Experimentausführung verknüpft sind, sodass Sie überprüfen können, wie das Modell trainiert wird.

## Speichern des Notebooks und Beenden der Spark-Sitzung

Nachdem Sie das Training und die Auswertung der Modelle abgeschlossen haben, können Sie das Notebook unter einem aussagekräftigen Namen speichern und die Spark-Sitzung beenden.

1. Verwenden Sie in der Menüleiste des Notebooks das Symbol ⚙️ **Einstellungen**, um die Einstellungen des Notebooks anzuzeigen.
2. Legen Sie den **Namen** des Notebooks auf **Trainieren und Vergleichen von Modellen** fest, und schließen Sie dann den Einstellungsbereich.
3. Wählen Sie im Notebookmenü **Sitzung beenden** aus, um die Spark-Sitzung zu beenden.

## Bereinigen von Ressourcen

In dieser Übung haben Sie ein Notebook erstellt und ein Machine Learning-Modell trainiert. Sie haben Scikit-learn verwendet, um das Modell zu trainieren, und MLflow, um seine Leistung zu verfolgen.

Wenn Sie mit dem Modell und den Experimenten fertig sind, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus, um alle darin enthaltenen Elemente anzuzeigen.
2. Wählen Sie im Menü **...** auf der Symbolleiste die **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Andere** die Option **Diesen Arbeitsbereich entfernen** aus.
