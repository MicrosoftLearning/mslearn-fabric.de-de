---
lab:
  title: Generieren von Batchvorhersagen mithilfe eines bereitgestellten Modells in Microsoft Fabric
  module: Generate batch predictions using a deployed model in Microsoft Fabric
---

# Generieren von Batchvorhersagen mithilfe eines bereitgestellten Modells in Microsoft Fabric

In diesem Lab verwenden Sie ein Machine Learning-Modell, um ein quantitatives Measure für Diabetes vorherzusagen.

Bei diesem Lab sammeln Sie praktische Erfahrung mit dem Generieren von Vorhersagen und der Visualisierung der Ergebnisse.

Dieses Lab dauert ungefähr **20** Minuten.

> **Hinweis:** Für diese Übung benötigen Sie ein *Geschäfts-*, *Schul- oder Unikonto* von Microsoft. Wenn Sie über kein Microsoft-Konto verfügen, können Sie sich [für eine kostenlose Testversion von Microsoft Office 365 E3 oder höher registrieren](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans).

## Erstellen eines Arbeitsbereichs

Erstellen Sie vor dem Arbeiten mit Daten in Fabric einen Arbeitsbereich mit aktivierter Fabric-Testversion.

1. Navigieren Sie in einem Browser unter `https://app.fabric.microsoft.com` zur Willkommensseite von Microsoft Fabric.
1. Wählen Sie auf der Microsoft Fabric-Startseite die Option **Data Science mit Synapse** aus.
1. Wählen Sie auf der Menüleiste auf der linken Seite **Arbeitsbereiche** aus (Symbol ähnelt &#128455;).
1. Erstellen Sie einen neuen Arbeitsbereich mit einem Namen Ihrer Wahl, und wählen Sie einen Lizenzierungsmodus mit Fabric-Kapazitäten aus (*Testversion*, *Premium* oder *Fabric*).
1. Wenn Ihr neuer Arbeitsbereich geöffnet wird, sollte er leer sein.

    ![Screenshot eines leeren Arbeitsbereichs in Fabric](./Images/new-workspace.png)

## Erstellen eines Notebooks

Sie verwenden in dieser Übung ein *Notebook* zum Trainieren und Verwenden eines Modells.

1. Erstellen Sie auf der Startseite von **Data Science mit Synapse** ein neues **Notebook**.

    Nach einigen Sekunden wird ein neues Notebook mit einer einzelnen *Zelle* geöffnet. Notebooks bestehen aus einer oder mehreren Zellen, die *Code* oder *Markdown* (formatierten Text) enthalten können.

1. Wählen Sie die erste Zelle aus (die derzeit eine *Codezelle* ist), und verwenden Sie dann auf der dynamischen Symbolleiste oben rechts die Schaltfläche **M&#8595;** , um die Zelle in eine *Markdownzelle* zu konvertieren.

    Wenn die Zelle in eine Markdownzelle geändert wird, wird der enthaltene Text gerendert.

1. Falls erforderlich, verwenden Sie die Schaltfläche **&#128393;** (Bearbeiten), um die Zelle in den Bearbeitungsmodus zu versetzen, löschen Sie dann den Inhalt und geben Sie den folgenden Text ein:

    ```text
   # Train and use a machine learning model
    ```

## Trainieren eines Machine Learning-Modells

Als Erstes trainieren wir ein Machine Learning-Modell, das einen *Regressionsalgorithmus* verwendet, um die relevanten Antworten für Diabetespatienten vorherzusagen (ein quantitatives Maß für das Fortschreiten der Krankheit ein Jahr nach der Baseline)

1. Verwenden Sie in Ihrem Notebook das Symbol **+ Code** unter der neuesten Zelle, um dem Notebook eine neue Codezelle hinzuzufügen.

    > **Tipp**: Um das Symbol **+ Code** anzuzeigen, zeigen Sie mit dem Mauszeiger direkt links unter die Ausgabe der aktuellen Zelle. Alternativ können Sie auf der Menüleiste auf der Registerkarte **Bearbeiten** die Option **+ Codezelle hinzufügen** auswählen.

1. Geben Sie den folgenden Code ein, um Daten zu laden und vorzubereiten und diese zum Trainieren eines Modells zu verwenden.

    ```python
   import pandas as pd
   import mlflow
   from sklearn.model_selection import train_test_split
   from sklearn.tree import DecisionTreeRegressor
   from mlflow.models.signature import ModelSignature
   from mlflow.types.schema import Schema, ColSpec

   # Get the data
   blob_account_name = "azureopendatastorage"
   blob_container_name = "mlsamples"
   blob_relative_path = "diabetes"
   blob_sas_token = r""
   wasbs_path = f"wasbs://%s@%s.blob.core.windows.net/%s" % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set("fs.azure.sas.%s.%s.blob.core.windows.net" % (blob_container_name, blob_account_name), blob_sas_token)
   df = spark.read.parquet(wasbs_path).toPandas()

   # Split the features and label for training
   X, y = df[['AGE','SEX','BMI','BP','S1','S2','S3','S4','S5','S6']].values, df['Y'].values
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.30, random_state=0)

   # Train the model in an MLflow experiment
   experiment_name = "experiment-diabetes"
   mlflow.set_experiment(experiment_name)
   with mlflow.start_run():
       mlflow.autolog(log_models=False)
       model = DecisionTreeRegressor(max_depth=5)
       model.fit(X_train, y_train)
       
       # Define the model signature
       input_schema = Schema([
           ColSpec("integer", "AGE"),
           ColSpec("integer", "SEX"),\
           ColSpec("double", "BMI"),
           ColSpec("double", "BP"),
           ColSpec("integer", "S1"),
           ColSpec("double", "S2"),
           ColSpec("double", "S3"),
           ColSpec("double", "S4"),
           ColSpec("double", "S5"),
           ColSpec("integer", "S6"),
        ])
       output_schema = Schema([ColSpec("integer")])
       signature = ModelSignature(inputs=input_schema, outputs=output_schema)
   
       # Log the model
       mlflow.sklearn.log_model(model, "model", signature=signature)
    ```

1. Verwenden Sie die Schaltfläche **&#9655; Zelle ausführen** links neben der Zelle, um diese auszuführen. Alternativ können Sie **SHIFT** + **ENTER** auf Ihrer Tastatur drücken, um eine Zelle auszuführen.

    > **Hinweis**: Da Sie Spark-Code zum ersten Mal in dieser Sitzung ausführen, muss der Spark-Pool gestartet werden. Dies bedeutet, dass die erste Ausführung in der Sitzung etwa eine Minute dauern kann. Nachfolgende Ausführungen erfolgen schneller.

1. Verwenden Sie das Symbol **+ Code** unterhalb der Zellenausgabe, um dem Notebook eine neue Codezelle hinzuzufügen, und geben Sie den folgenden Code ein, um das Modell zu registrieren, das vom Experiment in der vorherigen Zelle trainiert wurde:

    ```python
   # Get the most recent experiement run
   exp = mlflow.get_experiment_by_name(experiment_name)
   last_run = mlflow.search_runs(exp.experiment_id, order_by=["start_time DESC"], max_results=1)
   last_run_id = last_run.iloc[0]["run_id"]

   # Register the model that was trained in that run
   print("Registering the model from run :", last_run_id)
   model_uri = "runs:/{}/model".format(last_run_id)
   mv = mlflow.register_model(model_uri, "diabetes-model")
   print("Name: {}".format(mv.name))
   print("Version: {}".format(mv.version))
    ```

    Ihr Modell wird jetzt in Ihrem Arbeitsbereich als **diabetes-model**gespeichert. Optional können Sie die Suchfunktion in Ihrem Arbeitsbereich verwenden, um das Modell im Arbeitsbereich zu finden und mithilfe der Benutzeroberfläche zu erkunden.

## Erstellen eines Testdataset in einem Lakehouse

Um das Modell zu verwenden, benötigen Sie ein Dataset mit Patientendaten, für die Sie eine Diabetesdiagnose vorhersagen müssen. Sie erstellen dieses Dataset als Tabelle in einem Microsoft Fabric Lakehouse.

1. Wählen Sie im Notebook-Editor im **Explorer**-Bereich auf der linken Seite **+ Datenquellen** aus, um ein Lakehouse hinzuzufügen.
1. Wählen Sie **Neues Lakehouse** aus und dann **Hinzufügen**, und erstellen Sie ein neues **Lakehouse** mit einem gültigen Namen Ihrer Wahl.
1. Wenn Sie aufgefordert werden, die aktuelle Sitzung zu beenden, wählen Sie **Jetzt beenden** aus, um das Notebook neu zu starten.
1. Wenn das Lakehouse erstellt und an Ihr Notebook angefügt wird, fügen Sie eine neue Zeile hinzu und führen Sie den folgenden Code aus, um ein Dataset zu erstellen und in einer Tabelle im Lakehouse zu speichern:

    ```python
   from pyspark.sql.types import IntegerType, DoubleType

   # Create a new dataframe with patient data
   data = [
       (62, 2, 33.7, 101.0, 157, 93.2, 38.0, 4.0, 4.8598, 87),
       (50, 1, 22.7, 87.0, 183, 103.2, 70.0, 3.0, 3.8918, 69),
       (76, 2, 32.0, 93.0, 156, 93.6, 41.0, 4.0, 4.6728, 85),
       (25, 1, 26.6, 84.0, 198, 131.4, 40.0, 5.0, 4.8903, 89),
       (53, 1, 23.0, 101.0, 192, 125.4, 52.0, 4.0, 4.2905, 80),
       (24, 1, 23.7, 89.0, 139, 64.8, 61.0, 2.0, 4.1897, 68),
       (38, 2, 22.0, 90.0, 160, 99.6, 50.0, 3.0, 3.9512, 82),
       (69, 2, 27.5, 114.0, 255, 185.0, 56.0, 5.0, 4.2485, 92),
       (63, 2, 33.7, 83.0, 179, 119.4, 42.0, 4.0, 4.4773, 94),
       (30, 1, 30.0, 85.0, 180, 93.4, 43.0, 4.0, 5.3845, 88)
   ]
   columns = ['AGE','SEX','BMI','BP','S1','S2','S3','S4','S5','S6']
   df = spark.createDataFrame(data, schema=columns)

   # Convert data types to match the model input schema
   df = df.withColumn("AGE", df["AGE"].cast(IntegerType()))
   df = df.withColumn("SEX", df["SEX"].cast(IntegerType()))
   df = df.withColumn("BMI", df["BMI"].cast(DoubleType()))
   df = df.withColumn("BP", df["BP"].cast(DoubleType()))
   df = df.withColumn("S1", df["S1"].cast(IntegerType()))
   df = df.withColumn("S2", df["S2"].cast(DoubleType()))
   df = df.withColumn("S3", df["S3"].cast(DoubleType()))
   df = df.withColumn("S4", df["S4"].cast(DoubleType()))
   df = df.withColumn("S5", df["S5"].cast(DoubleType()))
   df = df.withColumn("S6", df["S6"].cast(IntegerType()))

   # Save the data in a delta table
   table_name = "diabetes_test"
   df.write.format("delta").mode("overwrite").save(f"Tables/{table_name}")
   print(f"Spark dataframe saved to delta table: {table_name}")
    ```

1. Wenn der Code abgeschlossen ist, wählen Sie **...** neben den **Tabellen** im Bereich **Lakehouse-Explorer** aus und dann **Aktualisieren**. Die Tabelle **diabetes_test** sollte angezeigt werden.
1. Erweitern Sie die Tabelle **diabetes_test** im linken Bereich, um alle darin enthaltenen Felder anzuzeigen.

## Anwendung des Modells zur Erstellung von Vorhersagen

Nun können Sie das zuvor trainierte Modell verwenden, um Vorhersagen über den Verlauf des Diabetes für die Patientendaten in Ihrer Tabelle zu erstellen.

1. Fügen Sie den folgenden Code in einer neuen Codezelle hinzu und führen Sie ihn aus:

    ```python
   import mlflow
   from synapse.ml.predict import MLFlowTransformer

   ## Read the patient features data 
   df_test = spark.read.format("delta").load(f"Tables/{table_name}")

   # Use the model to generate diabetes predictions for each row
   model = MLFlowTransformer(
       inputCols=["AGE","SEX","BMI","BP","S1","S2","S3","S4","S5","S6"],
       outputCol="predictions",
       modelName="diabetes-model",
       modelVersion=1)
   df_test = model.transform(df)

   # Save the results (the original features PLUS the prediction)
   df_test.write.format('delta').mode("overwrite").option("mergeSchema", "true").save(f"Tables/{table_name}")
    ```

1. Wenn der Code abgeschlossen ist, wählen Sie **...** neben der Tabelle **diabetes_test** im Bereich **Lakehouse-Explorer** aus und dann **Aktualisieren**. Das Feld **Vorhersagen** wurde hinzugefügt.
1. Fügen Sie dem Notebook eine neue Codezelle hinzu und ziehen Sie die Tabelle **diabetes_test** hinein. Der erforderliche Code zum Anzeigen des Inhalts der Tabelle wird angezeigt. Führen Sie die Zelle aus, um die Daten anzuzeigen.

## Bereinigen von Ressourcen

In dieser Übung haben Sie ein Modell verwendet, um Batchvorhersagen zu generieren.

Wenn Sie mit dem Notebook fertig sind, können Sie den Arbeitsbereich löschen, den Sie für diese Übung erstellt haben.

1. Wählen Sie auf der Leiste auf der linken Seite das Symbol für Ihren Arbeitsbereich aus, um alle darin enthaltenen Elemente anzuzeigen.
2. Wählen Sie im Menü **...** auf der Symbolleiste die **Arbeitsbereichseinstellungen** aus.
3. Wählen Sie im Abschnitt **Andere** die Option **Diesen Arbeitsbereich entfernen** aus.
