---
lab:
  title: 'Erstellen einer generativen KI-App, die Ihre eigenen Daten verwendet'
  description: 'Erfahren Sie, wie Sie mithilfe des RAG-Modells (Retrieval Augmented Generation) eine Chat-App erstellen, die Aufforderungen zur Verwendung Ihrer eigenen Daten anfordert.'
---

# Erstellen einer generativen KI-App, die Ihre eigenen Daten verwendet

Retrieval Augmented Generation (RAG) ist eine Methode zum Erstellen von Anwendungen, die Daten aus benutzerdefinierten Datenquellen in einen Prompt für ein generatives KI-Modell integrieren. RAG ist ein häufig verwendetes Muster für die Entwicklung generativer KI-Apps – chatbasierte Anwendungen, die ein Sprachmodell verwenden, um Eingaben zu interpretieren und entsprechende Antworten zu generieren.

In dieser Übung verwenden Sie Azure AI Foundry, um benutzerdefinierte Daten in eine generative KI-Lösung zu integrieren.

Diese Übung dauert ca. **45** Minuten.

> **Hinweis**: Diese Übung basiert auf Vorabversionen von Diensten, die sich möglicherweise noch ändern können.

## Erstellen eines Azure AI Foundry-Hubs und -Projekts

Die Features von Azure AI Foundry, die wir in dieser Übung verwenden werden, erfordern ein Projekt, das auf einer Azure AI Foundry-*Hub-Ressource* basiert.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartfenster, die bei der ersten Anmeldung geöffnet werden, und verwenden Sie gegebenenfalls das Logo **Azure AI Foundry** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht (schließen Sie das **Hilfe**-Fenster, falls es geöffnet ist):

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Navigieren Sie im Browser zu `https://ai.azure.com/managementCenter/allResources` und wählen Sie **Erstellen**. Wählen Sie dann die Option zum Erstellen einer neuen **KI-Hubressource** aus.
1. Geben Sie im Assistenten **Projekt erstellen** einen gültigen Namen für Ihr Projekt ein. Wenn ein vorhandener Hub vorgeschlagen wird, wählen Sie die Option zum Erstellen eines neuen Hubs aus und erweitern Sie **Erweiterte Optionen**, um die folgenden Einstellungen für Ihr Projekt festzulegen:
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Hubname**: Geben Sie einen gültigen Namen für Ihren Hub an.
    - **Standort**: USA, Osten 2 oder Schweden, Mitte\*

    > \* Einige Azure KI-Ressourcen unterliegen regionalen Modellkontingenten. Sollte im weiteren Verlauf der Übung eine Kontingentgrenze überschritten werden, müssen Sie möglicherweise eine weitere Ressource in einer anderen Region anlegen.

1. Warten Sie, bis Ihr Projekt erstellt wurde.

## Bereitstellen von Modellen

Sie benötigen zwei Modelle, um Ihre Lösung zu implementieren:

- Ein *Einbettungsmodell* zum Vektorisieren von Textdaten für eine effiziente Indizierung und Verarbeitung.
- Ein Modell, das in natürlicher Sprache Antworten auf Fragen generieren kann, basierend auf Ihren Daten.

1. Wählen Sie im Azure KI Foundry-Portal in Ihrem Projekt im Navigationsbereich links unter **Meine Assets** die Seite **Modelle + Endpunkte**.
1. Erstellen Sie eine neue Bereitstellung des Modells **text-embedding-ada-002** mit den folgenden Einstellungen, indem Sie **Anpassen** im Assistenten zum Bereitstellen des Modells wählen:

    - **Bereitstellungsname:***Ein gültiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Globaler Standard
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **Verbundene KI-Ressource**: *Wählen Sie die zuvor erstellte Ressource*
    - **Token pro Minute Ratenlimit (Tausende)**: 50K *(oder das in Ihrem Abonnement verfügbare Maximum, wenn weniger als 50K)*
    - **Inhaltsfilter**: StandardV2 

    > **Hinweis**: Wenn an Ihrem aktuellen Speicherort für KI-Ressourcen kein Kontingent für das Modell, das Sie bereitstellen möchten, verfügbar ist, werden Sie aufgefordert, einen anderen Speicherort zu wählen, an dem eine neue KI-Ressource erstellt und mit Ihrem Projekt verbunden wird.

1. Kehren Sie zur Seite **Modelle + Endpunkte** zurück und wiederholen Sie die vorherigen Schritte, um ein **gpt-4o**-Modell mit einer **Global Standard**-Bereitstellung der neuesten Version mit einem TPM-Ratenlimit von **50K** (oder dem in Ihrem Abonnement verfügbaren Maximum, falls weniger als 50K) bereitzustellen.

    > **Hinweis:** Durch das Verringern der Token pro Minute (TPM) wird die Überlastung des Kontingents vermieden, das in Ihrem verwendeten Abonnement verfügbar ist. 50.000 TPM sind für die in dieser Übung verwendeten Daten ausreichend.

## Hinzufügen von Daten zu Ihrem Projekt

Die Daten für Ihre App bestehen aus einer Reihe von Reisebroschüren im PDF-Format von dem fiktiven Reisebüro *Margie's Travel*. Fügen wir sie dem Projekt hinzu.

1. Laden Sie in einer neuen Registerkarte des Browsers das [gezippte Archiv der Broschüren](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) von `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` herunter und entpacken Sie es in einen Ordner mit dem Namen **Broschüren** auf Ihrem lokalen Dateisystem.
1. Wählen Sie im Azure KI Foundry-Portal in Ihrem Projekt im Navigationsbereich auf der linken Seite unter **Meine Assets** die Seite **Daten + Indizes**.
1. Wählen Sie **+ Neue Daten** aus.
1. Erweitern Sie im Assistenten **Hinzufügen Ihrer Daten** das Dropdownmenü, um **Dateien/Ordner hochladen** auszuwählen.
1. Wählen Sie **Ordner hochladen** und laden Sie den Ordner **Broschüren** hoch. Warten Sie, bis alle Dateien des Ordners aufgelistet sind.
1. Wählen Sie **Weiter** und setzen Sie den Dateinamen auf `brochures`.
1. Warten Sie, bis der Ordner hochgeladen wurde, und beachten Sie, dass er mehrere .pdf-Dateien enthält.

## Erstellen eines Indexes für Ihre Daten

Nachdem Sie Ihrem Projekt nun eine Datenquelle hinzugefügt haben, können Sie sie verwenden, um einen Index in Ihrer Azure KI Search-Ressource zu erstellen.

1. Wählen Sie im Azure KI Foundry-Portal in Ihrem Projekt im Navigationsbereich auf der linken Seite unter **Meine Assets** die Seite **Daten + Indizes**.
1. Auf der Registerkarte **Indizes** fügen Sie einen neuen Index mit den folgenden Einstellungen hinzu:
    - **Quellstandort**:
        - **Datenquelle**: Daten im Azure KI Foundry-Portal
            - *Auswählen der Datenquelle **Broschüren***
    - **Indexkonfiguration**:
        - **Azure KI-Suche auswählen**: *Erstellen Sie eine neue Azure KI-Suchressource mit den folgenden Einstellungen*:
            - **Abonnement**: *Ihr Azure-Abonnement*
            - **Ressourcengruppe**: *Die gleiche Ressourcengruppe wie Ihr KI-Hub*
            - **Dienstname**: *Ein gültiger Name für Ihre KI-Suchressource*
            - **Ort**: *Der gleiche Standort wie Ihr KI-Hub*
            - **Tarif**: Basic
            
            Bitte warten Sie, bis die KI-Suchressource erstellt wurde. Kehren Sie anschließend zur Azure AI Foundry zurück und schließen Sie die Konfiguration des Index ab, indem Sie **Andere KI-Suchressource verbinden** auswählen und eine Verbindung zu der soeben erstellten KI-Suchressource hinzufügen.
 
        - **Vektorindex**: `brochures-index`
        - **VM**: Automatisch auswählen
    - **Sucheinstellungen**:
        - **Vektoreinstellungen**: Hinzufügen der Vektorsuche zu dieser Suchressource
        - **Azure OpenAI-Verbindung**: *Wählen Sie die standardmäßige Azure OpenAI-Ressource für Ihren Hub aus.*
        - **Einbettungsmodell**: text-embedding-ada-002
        - **Bereitstellung des Einbettungsmodells**: *Ihre Bereitstellung des* text-embedding-ada-002 *Modells*

1. Erstellen Sie den Vektorindex und warten Sie, bis der Indizierungsprozess abgeschlossen ist, was je nach verfügbaren Rechenressourcen in Ihrem Abonnement eine Weile dauern kann.

    Der Indexerstellungsvorgang besteht aus den folgenden Aufträgen:

    - Zerlegen, segmentieren und integrieren Sie die Texttoken in Ihre Broschürendaten.
    - Erstellen Sie den Azure KI-Suchindex.
    - Registrieren Sie die Indexressource.

    > **Tipp**: Während Sie darauf warten, dass der Index erstellt wird, können Sie einen Blick auf die heruntergeladenen Broschüren werfen, um sich mit deren Inhalt vertraut zu machen.

## Testen des Indexes im Playground

Bevor Sie Ihren Index in einem RAG-basierten Prompt Flow verwenden, überprüfen wir, ob er verwendet werden kann, um generative KI-Antworten zu beeinflussen.

1. Wählen Sie im Navigationsbereich auf der linken Seite die Seite **Playgrounds** aus und öffnen Sie den Playground **Chat**.
1. Vergewissern Sie sich auf der Seite Chat Playground im Bereich Setup, dass Ihre **gpt-4o**-Modellimplementierung ausgewählt ist. Übermitteln Sie dann im Hauptchatsitzungsbereich die Eingabeaufforderung `Where can I stay in New York?`
1. Überprüfen Sie die Antwort, die eine generische Antwort aus dem Modell ohne Daten aus dem Index sein sollte.
1. Erweitern Sie im Bereich „Einrichtung“ das Feld **Ihre Daten hinzufügen** und fügen Sie dann den Projektindex **Broschüren-Index** hinzu und wählen Sie den Suchtyp **hybrid (Vektor + Schlüsselwort)** aus.

   > **Tipp**: In einigen Fällen sind neu erstellte Indizes möglicherweise nicht sofort verfügbar. Das Aktualisieren des Browsers hilft in der Regel, aber wenn das Problem weiterhin auftritt, bei dem der Index nicht gefunden werden kann, müssen Sie möglicherweise warten, bis der Index erkannt wird.

1. Nachdem der Index hinzugefügt wurde und die Chatsitzung neu gestartet wurde, übermitteln Sie die Eingabeaufforderung `Where can I stay in New York?` erneut
1. Überprüfen Sie die Antwort, die auf Daten im Index basiert.

<!-- DEPRECATED STEPS

## Create a RAG client app with the Azure AI Foundry and Azure OpenAI SDKs

Now that you have a working index, you can use the Azure AI Foundry and Azure OpenAI SDKs to implement the RAG pattern in a client application. Let's explore the code to accomplish this in a simple example.

> **Tip**: You can choose to develop your RAG solution using Python or Microsoft C#. Follow the instructions in the appropriate section for your chosen language.

### Prepare the application configuration

1. In the Azure AI Foundry portal, view the **Overview** page for your project.
1. In the **Project details** area, note the **Project connection string**. You'll use this connection string to connect to your project in a client application.
1. Return to the browser tab containing the Azure portal (keeping the Azure AI Foundry portal open in the existing tab).
1. Use the **[\>_]** button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a ***PowerShell*** environment with no storage in your subscription.

    The cloud shell provides a command-line interface in a pane at the bottom of the Azure portal. You can resize or maximize this pane to make it easier to work in.

    > **Note**: If you have previously created a cloud shell that uses a *Bash* environment, switch it to ***PowerShell***.

1. In the cloud shell toolbar, in the **Settings** menu, select **Go to Classic version** (this is required to use the code editor).

    **<font color="red">Ensure you've switched to the classic version of the cloud shell before continuing.</font>**

1. In the cloud shell pane, enter the following commands to clone the GitHub repo containing the code files for this exercise (type the command, or copy it to the clipboard and then right-click in the command line and paste as plain text):

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Tip**: As you paste commands into the cloudshell, the output may take up a large amount of the screen buffer. You can clear the screen by entering the `cls` command to make it easier to focus on each task.

1. After the repo has been cloned, navigate to the folder containing the chat application code files:

    > **Note**: Follow the steps for your chosen programming language.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. In the cloud shell command-line pane, enter the following command to install the libraries you'll use:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-ai-projects azure-identity openai
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI --prerelease
    ```
    

1. Enter the following command to edit the configuration file that has been provided:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    The file is opened in a code editor.

1. In the code file, replace the following placeholders: 
    - **your_project_connection_string**: Replace with the connection string for your project (copied from the project **Overview** page in the Azure AI Foundry portal).
    - **your_gpt_model_deployment** Replace with the name you assigned to your **gpt-4o** model deployment.
    - **your_embedding_model_deployment**: Replace with the name you assigned to your **text-embedding-ada-002** model deployment.
    - **your_index**: Replace with your index name (which should be `brochures-index`).
1. After you've replaced the placeholders, in the code editor, use the **CTRL+S** command or **Right-click > Save** to save your changes and then use the **CTRL+Q** command or **Right-click > Quit** to close the code editor while keeping the cloud shell command line open.

### Explore code to implement the RAG pattern

1. Enter the following command to edit the code file that has been provided:

    **Python**

    ```
   code rag-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Review the code in the file, noting that it:
    - Uses the Azure AI Foundry SDK to connect to your project (using the project connection string)
    - Creates an authenticated Azure OpenAI client from your project connection.
    - Retrieves the default Azure AI Search connection from your project so it can determine the endpoint and key for your Azure AI Search service.
    - Creates a suitable system message.
    - Submits a prompt (including the system and a user message based on the user input) to the Azure OpenAI client, adding:
        - Connection details for the Azure AI Search index to be queried.
        - Details of the embedding model to be used to vectorize the query\*.
    - Displays the response from the grounded prompt.
    - Adds the response to the chat history.

    \* *The query for the search index is based on the prompt, and is used to find relevant text in the indexed documents. You can use a keyword-based search that submits the query as text, but using a vector-based search can be more efficient - hence the use of an embedding model to vectorize the query text before submitting it.*

1. Use the **CTRL+Q** command to close the code editor without saving any changes, while keeping the cloud shell command line open.

### Run the chat application

1. In the cloud shell command-line pane, enter the following command to run the app:

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. When prompted, enter a question, such as `Where should I go on vacation to see architecture?` and review the response from your generative AI model.

    Note that the response includes source references to indicate the indexed data in which the answer was found.

1. Try a follow-up question, for example `Where can I stay there?`

1. When you're finished, enter `quit` to exit the program. Then close the cloud shell pane.

-->

## Bereinigung

Um unnötige Azure-Kosten und Ressourcenauslastung zu vermeiden, sollten Sie die in dieser Übung bereitgestellten Ressourcen entfernen.

1. Wenn Sie die Erkundung von Azure KI Foundry abgeschlossen haben, kehren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` zurück und melden sich gegebenenfalls mit Ihren Azure-Anmeldedaten an. Löschen Sie dann die Ressourcen in der Ressourcengruppe, in der Sie Ihre Azure KI-Suche- und Azure AI-Ressourcen bereitgestellt haben.
