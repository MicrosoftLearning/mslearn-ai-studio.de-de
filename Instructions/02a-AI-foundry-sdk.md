---
lab:
  title: Erstellen einer generativen KI-Chat-App
  description: 'Erfahren Sie, wie Sie das Azure AI Foundry SDK verwenden, um eine App zu erstellen, die eine Verbindung mit Ihrem Projekt und Chats mit einem Sprachmodell herstellt.'
---

# Erstellen einer generativen KI-Chat-App

In dieser Übung verwenden Sie das Azure AI Foundry SDK, um eine einfache Chat-App zu erstellen, die eine Verbindung mit einem Projekt und Chats mit einem Sprachmodell herstellt.

Diese Übung dauert ca. **40** Minuten.

> **Hinweis**: Diese Übung basiert auf Vorabversionen von SDKs, die sich möglicherweise noch ändern können. Wo nötig, haben wir spezielle Versionen von Paketen verwendet, die möglicherweise nicht die neuesten verfügbaren Versionen widerspiegeln. Es kann zu unerwartetem Verhalten, Warnungen oder Fehlern kommen.

## Bereitstellen Sie ein DALL-E-Modell in einem Azure KI Foundry-Projekt

Beginnen wir mit dem Erstellen eines Azure KI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartfenster, die bei der ersten Anmeldung geöffnet werden, und verwenden Sie gegebenenfalls das Logo **Azure AI Foundry** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht (schließen Sie das **Hilfe**-Fenster, falls es geöffnet ist):

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Suchen Sie auf der Startseite im Abschnitt **Modelle und Funktionen erkunden** nach dem Modell `gpt-4o`, das wir in unserem Projekt verwenden werden.
1. Wählen Sie in den Suchergebnissen das Modell **gpt-4o** aus, um dessen Details anzuzeigen, und wählen Sie dann oben auf der Seite für das Modell die Option **Dieses Modell verwenden** aus.
1. Wenn Sie zum Erstellen eines Projekts aufgefordert werden, geben Sie einen gültigen Namen für Ihr Projekt ein und erweitern Sie **Erweiterte Optionen**.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihr Projekt fest:
    - **Azure KI Foundry-Ressource**: *Ein gültiger Name für Ihre Azure KI Foundry-Ressource*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Region**: *Wählen Sie einen beliebigen Standort aus, an dem KI Services unterstützt wird***\*

    > \* Einige Azure KI-Ressourcen unterliegen regionalen Modellkontingenten. Sollte im weiteren Verlauf der Übung eine Kontingentgrenze überschritten werden, müssen Sie möglicherweise eine weitere Ressource in einer anderen Region anlegen.

1. Wählen Sie **Erstellen** und warten Sie, bis Ihr Projekt einschließlich der von Ihnen ausgewählten GPT-4-Modellbereitstellung erstellt wurde.
1. Wenn Ihr Projekt erstellt wird, wird der Chat-Playground automatisch geöffnet.
1. Notieren Sie sich im Bereich **Setup** den Namen Ihrer Modellbereitstellung, der **gpt-4o** lauten sollte. Sie können dies überprüfen, indem Sie die Bereitstellung auf der Seite **Modelle und Endpunkte** anzeigen (öffnen Sie dazu einfach diese Seite im Navigationsbereich auf der linken Seite).
1. Wählen Sie im Navigationsbereich auf der linken Seite **Übersicht**, um die Hauptseite Ihres Projekts anzuzeigen, die wie folgt aussieht:

    > **Hinweis**: Wenn die Fehlermeldung *Unzureichende Berechtigungen** angezeigt wird, klicken Sie auf die Schaltfläche „**Beheben**“, um das Problem zu beheben.

    ![Screenshot einer Projektübersichtsseite von Azure AI Foundry.](./media/ai-foundry-project.png)

## Erstellen einer Clientanwendung zum Chatten mit dem Modell

Nachdem Sie ein Modell bereitgestellt haben, können Sie die Azure AI Foundry und die Azure KI-Modellinferenz-SDKs verwenden, um eine Anwendung zu entwickeln, die mit dem Modell chattet.

> **Tipp**: Sie können Ihre Lösung mit Python oder Microsoft C# entwickeln. Folgen Sie den Anweisungen im entsprechenden Abschnitt für Ihre ausgewählte Sprache.

### Vorbereiten der Anwendungskonfiguration

1. Wechseln Sie im Azure AI Foundry-Portal zur **Übersichtsseite** Ihres Projekts.
1. Notieren Sie sich im Bereich **Projektdetails** den **Azure AI Foundry-Projektendpunkt**. Sie verwenden diesen Endpunkt, um in einer Clientanwendung eine Verbindung zu Ihrem Projekt herzustellen.
1. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.

    Schließen Sie alle Willkommensbenachrichtigungen, um die Startseite des Azure-Portals anzuzeigen.

1. Verwenden Sie die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud-Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung ohne Speicher in Ihrem Abonnement.

    Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Fenster am unteren Rand des Azure-Portals. Sie können die Größe dieses Bereichs ändern oder maximieren, um die Arbeit zu vereinfachen.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

1. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Geben Sie im Cloud Shell-Bereich die folgenden Befehle ein, um das GitHub-Repository zu klonen, das die Codedateien für diese Übung enthält (geben Sie den Befehl ein, oder kopieren Sie ihn in die Zwischenablage, und klicken Sie dann mit der rechten Maustaste in die Befehlszeile, und fügen Sie ihn als Nur-Text ein):

    ```
   rm -r mslearn-ai-foundry -f
   git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Tipp**: Wenn Sie Befehle in die Cloud Shell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers in Anspruch nehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

1. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Chatanwendung enthält:

    Verwenden Sie den folgenden Befehl je nach gewählter Programmiersprache.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/c-sharp
    ```

1. Geben Sie im Befehlszeilenfenster der Cloud Shell den folgenden Befehl ein, um die zu verwendenden Bibliotheken zu installieren:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.9
   dotnet add package Azure.AI.Inference --version 1.0.0-beta.5
    ```
    

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    Die Datei wird in einem Code-Editor geöffnet.

1. Ersetzen Sie in der Code-Datei den Platzhalter **your_project_endpoint** durch den Endpunkt für Ihr Projekt (kopiert von der Seite **Übersicht** des Projekts im Azure AI Foundry-Portal) und den Platzhalter **your_model_deployment** durch den Namen Ihrer GPT-4-Modellbereitstellung.
1. Nachdem Sie die Platzhalter ersetzt haben, verwenden Sie im Code-Editor den Befehl **STRG+S** oder **Rechtsklick > Speichern**, um Ihre Änderungen zu speichern, und verwenden Sie dann den Befehl **STRG+Q** oder **Rechtsklick > Beenden**, um den Code-Editor zu schließen, während die Cloud Shell-Befehlszeile geöffnet bleibt.

### Schreiben von Code, um sich mit Ihrem Projekt zu verbinden und mit Ihrem Modell zu chatten

> **Tipp**: Achten Sie beim Hinzufügen von Code auf den richtigen Einzug.

1. Geben Sie den folgenden Befehl ein, um die bereitgestellte Codedatei zu bearbeiten:

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Beachten Sie in der Codedatei die vorhandenen Anweisungen, die am Anfang der Datei hinzugefügt wurden, um die erforderlichen SDK-Namespaces zu importieren. Suchen Sie dann den Kommentar **Add references** und fügen Sie den folgenden Code hinzu, um auf die Namespaces in den zuvor installierten Bibliotheken zu verweisen:

    **Python**

    ```python
   # Add references
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import SystemMessage, UserMessage, AssistantMessage
    ```

    **C#**

    ```csharp
   // Add references
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

1. Beachten Sie, dass der Code in der **Hauptfunktion** unter dem Kommentar **Get configuration settings** die Zeichenfolge für die Projektverbindung und die Werte für den Namen der Modellbereitstellung lädt, die Sie in der Konfigurationsdatei definiert haben.
1. Finden Sie den Kommentar **Initialize the project client** und fügen Sie den folgenden Code hinzu, um sich mit Ihrem Azure AI Foundry-Projekt zu verbinden. Verwenden Sie dazu die Azure-Anmeldedaten, mit denen Sie derzeit angemeldet sind:

    > **Tipp**: Achten Sie darauf, die richtige Einzugsebene für den Code beizubehalten.

    **Python**

    ```python
   # Initialize the project client
   projectClient = AIProjectClient(            
            credential=DefaultAzureCredential(
                exclude_environment_credential=True,
                exclude_managed_identity_credential=True
            ),
            endpoint=project_connection,
        )
    ```

    **C#**

    ```csharp
   // Initialize the project client
   DefaultAzureCredentialOptions options = new()
       { ExcludeEnvironmentCredential = true,
        ExcludeManagedIdentityCredential = true };
   var projectClient = new AIProjectClient(
        new Uri(project_connection),
        new DefaultAzureCredential(options));
    ```

1. Finden Sie den Kommentar **Get a chat client** und fügen Sie den folgenden Code hinzu, um ein Client-Objekt für den Chat mit einem Modell zu erstellen:

    **Python**

    ```python
   # Get a chat client
   chat = projectClient.inference.get_chat_completions_client()
    ```

    **C#**

    ```csharp
   // Get a chat client
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```

    > **Hinweis**: Dieser Code verwendet den Azure AI Foundry-Projektclient, um eine sichere Verbindung zum Standard-Endpunkt des Azure KI-Modellinferenz-Dienstes herzustellen, der mit Ihrem Projekt verknüpft ist. Sie können auch *direkt* eine Verbindung zum Endpunkt herstellen, indem Sie das Azure KI-Modellinferenz-SDK verwenden, den Endpunkt-URI angeben, der für die Dienstverbindung im Azure AI Foundry-Portal oder auf der entsprechenden Azure KI Services-Ressourcenseite im Azure-Portal angezeigt wird, und einen Authentifizierungsschlüssel oder ein Entra-Berechtigungstoken verwenden. Weitere Informationen zum Herstellen einer Verbindung mit dem Azure KI-Modellinferenz-Dienst finden Sie unter [Azure KI-Modellinferenz-SDK](https://learn.microsoft.com/azure/machine-learning/reference-model-inference-api).

1. Finden Sie den Kommentar **Initialize prompt with system message** und fügen Sie den folgenden Code hinzu, um eine Sammlung von Nachrichten mit einer Systemansage zu initialisieren.

    **Python**

    ```python
   # Initialize prompt with system message
   prompt=[
            SystemMessage("You are a helpful AI assistant that answers questions.")
        ]
    ```

    **C#**

    ```csharp
   // Initialize prompt with system message
   var prompt = new List<ChatRequestMessage>(){
                    new ChatRequestSystemMessage("You are a helpful AI assistant that answers questions.")
                };
    ```

1. Beachten Sie, dass der Code einen Loop (Schleife) enthält, damit Benutzende einen Prompt eingeben können, bis sie „quit“ eingeben. Suchen Sie dann im Loop-Abschnitt den Kommentar **Get a chat completion** und fügen Sie den folgenden Code hinzu, um die Benutzereingabe zum Prompt hinzuzufügen, die Vervollständigung aus Ihrem Modell abzurufen und die Vervollständigung zum Prompt hinzuzufügen (damit Sie den Chatverlauf für zukünftige Iterationen aufbewahren):

    **Python**

    ```python
   # Get a chat completion
   prompt.append(UserMessage(input_text))
   response = chat.complete(
        model=model_deployment,
        messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append(AssistantMessage(completion))
    ```

    **C#**

    ```csharp
   // Get a chat completion
   prompt.Add(new ChatRequestUserMessage(input_text));
   var requestOptions = new ChatCompletionsOptions()
   {
       Model = model_deployment,
       Messages = prompt
   };

   Response<ChatCompletions> response = chat.Complete(requestOptions);
   var completion = response.Value.Content;
   Console.WriteLine(completion);
   prompt.Add(new ChatRequestAssistantMessage(completion));
    ```

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern.

### Bei Azure anmelden und die App ausführen

1. Geben Sie im Befehlszeilenbereich der Cloud-Shell den folgenden Befehl ein, um sich bei Azure anzumelden.

    ```
   az login
    ```

    **<font color="red">Sie müssen sich bei Azure anmelden - auch wenn die Cloud-Shell-Sitzung bereits authentifiziert ist.</font>**

    > **Hinweis**: In den meisten Szenarien ist nur die Verwendung von *az login* ausreichend. Wenn Sie jedoch Abonnements in mehreren Mandqanten haben, müssen Sie möglicherweise den Mandanten mit dem Parameter *--tenant* angeben. Weitere Informationen finden Sie unter [Interaktive Anmeldung bei Azure mit der Azure CLI](https://learn.microsoft.com/cli/azure/authenticate-azure-cli-interactively).
    
1. Wenn Sie dazu aufgefordert werden, folgen Sie den Anweisungen, um die Anmeldeseite in einer neuen Registerkarte zu öffnen, und geben Sie den angegebenen Authentifizierungscode und Ihre Azure-Anmeldeinformationen ein. Schließen Sie dann den Anmeldevorgang in der Befehlszeile ab, und wählen Sie das Abonnement aus, das Ihren Azure AI Foundry Hub enthält, wenn Sie dazu aufgefordert werden.
1. Geben Sie nach der Anmeldung den folgenden Befehl ein, um die Anwendung auszuführen:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Wenn Sie dazu aufgefordert werden, geben Sie eine Frage ein, z. B. `What is the fastest animal on Earth?`, und überprüfen Sie die Antwort Ihres generativen KI-Modells.
1. Versuchen Sie es mit einigen Folgefragen wie `Where can I see one?` oder `Are they endangered?`. Die Unterhaltung sollte fortgesetzt werden, wobei der Chatverlauf als Kontext für jede Iteration verwendet wird.
1. Wenn Sie fertig sind, geben Sie `quit` ein, um das Programm zu beenden.

> **Tipp**: Wenn die App fehlschlägt, weil das Ratenlimit überschritten wird. Warten Sie einige Sekunden, und versuchen Sie es noch mal. Wenn in Ihrem Abonnement nicht genügend Kontingent verfügbar ist, kann das Modell möglicherweise nicht reagieren.

## Zusammenfassung

In dieser Übung haben Sie das Azure AI Foundry SDK verwendet, um eine Clientanwendung für ein generatives KI-Modell zu erstellen, das Sie in einem Azure AI Foundry-Projekt bereitgestellt haben.

## Bereinigen

Wenn Sie die Erkundung des Azure KI-Foundry-Portals abgeschlossen haben, sollten Sie die Ressourcen, die Sie in dieser Übung erstellt haben, löschen, um unnötige Azure-Kosten zu vermeiden.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und zeigen Sie den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
