---
lab:
  title: Erstellen einer generativen KI-Chat-App
  description: 'Erfahren Sie, wie Sie das Azure AI Foundry SDK verwenden, um eine App zu erstellen, die eine Verbindung mit Ihrem Projekt und Chats mit einem Sprachmodell herstellt.'
---

# Erstellen einer generativen KI-Chat-App

In dieser Übung verwenden Sie das Azure AI Foundry SDK, um eine einfache Chat-App zu erstellen, die eine Verbindung mit einem Projekt und Chats mit einem Sprachmodell herstellt.

Diese Übung dauert ca. **40** Minuten.

> **Hinweis**: Diese Übung basiert auf Vorabversionen von SDKs, die sich möglicherweise noch ändern können. Wo nötig, haben wir spezielle Versionen von Paketen verwendet, die möglicherweise nicht die neuesten verfügbaren Versionen widerspiegeln.

## Erstellen eines Azure KI Foundry-Projekts

Beginnen wir mit dem Erstellen eines Azure AI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden, und verwenden Sie bei Bedarf das **Azure AI Foundry**-Logo oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht (schließen Sie den Bereich **Hilfe**, falls er geöffnet ist):

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Wählen Sie auf der Startseite **+ Projekt erstellen**.
1. Geben Sie im Assistenten **Projekt erstellen** einen gültigen Namen für Ihr Projekt ein und wählen Sie, falls ein vorhandener Hub vorgeschlagen wird, die Option zum Erstellen eines neuen. Überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihren Hub und Ihr Projekt zu unterstützen.
1. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Geben Sie einen gültigen Namen für Ihren Hub an*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** aus, wählen Sie dann **gpt-4o** im Fenster der Standorthilfe aus und verwenden Sie die empfohlene Region\*.
    - A**zure KI Services oder Azure OpenAI verbinden**: *Wählen Sie Neuen KI-Dienst erstellen aus*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden durch regionale Modellkontingente eingeschränkt. Wenn später in der Übung eine Kontingentgrenze erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen.

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
1. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](./media/ai-foundry-project.png)

## Bereitstellen eines generativen KI-Modells

Jetzt können Sie ein generatives KI-Sprachmodell bereitstellen, um Ihre Chat-Anwendung zu unterstützen. In diesem Beispiel verwenden Sie das OpenAI gpt-4o-Modell; die Prinzipien sind jedoch für jedes Modell gleich.

1. Verwenden Sie in der Symbolleiste oben rechts auf Ihrer Azure AI Foundry-Projektseite das Symbol **Funktionen anzeigen** (**&#9215;**) , um das Feature **Modelle zum Azure KI-Modellinferenz-Service bereitstellen** zu aktivieren. Mit diesem Feature wird sichergestellt, dass Ihre Modellbereitstellung für den Azure KI-Rückschlussdienst verfügbar ist, den Sie im Anwendungscode verwenden.
1. Wählen Sie im linken Fensterbereich für Ihr Projekt im Abschnitt **Meine Assets** die Seite **Modelle + Endpunkte**.
1. Wählen Sie auf der Seite **Modelle + Endpunkte** auf der Registerkarte **Modellbereitstellungen** im Menü **+ Modell bereitstellen** die Option **Basismodell bereitstellen**.
1. Suchen Sie das Modell **gpt-4** in der Liste, wählen Sie es aus und bestätigen Sie es.
1. Stellen Sie das Modell mit den folgenden Einstellungen bereit, indem Sie **Anpassen** in den Bereitstellungsdetails wählen:
    - **Bereitstellungsname:***Ein gültiger Name für Ihre Modellimplementierung*
    - **Bereitstellungstyp**: Globaler Standard
    - **Automatische Versionsaktualisierung**: Aktiviert
    - **Modellversion**: *Wählen Sie die neueste verfügbare Version aus.*
    - **Verbundene AI-Ressource**: *Wählen Sie Ihre Azure OpenAI-Ressourcenverbindung*
    - **Ratenlimit für Token pro Minute (Tausender)**: 50K *(oder das Maximum, das in Ihrem Abonnement verfügbar ist, falls weniger als 50K)*
    - **Inhaltsfilter**: StandardV2 

    > **Hinweis:** Durch das Verringern des TPM wird die Überlastung des Kontingents vermieden, das in dem von Ihnen verwendeten Abonnement verfügbar ist. 50.000 TPM reicht für die in dieser Übung verwendeten Daten aus. Wenn Ihr verfügbares Kontingent darunter liegt, können Sie die Übung abschließen, aber möglicherweise treten Fehler auf, wenn das Ratenlimit überschritten wird.

1. Warten Sie, bis die Bereitstellung abgeschlossen ist.

## Erstellen einer Clientanwendung zum Chatten mit dem Modell

Nachdem Sie ein Modell bereitgestellt haben, können Sie die Azure AI Foundry und die Azure KI-Modellinferenz-SDKs verwenden, um eine Anwendung zu entwickeln, die mit dem Modell chattet.

> **Tipp**: Sie können Ihre Lösung mit Python oder Microsoft C# entwickeln. Folgen Sie den Anweisungen im entsprechenden Abschnitt für Ihre ausgewählte Sprache.

### Vorbereiten der Anwendungskonfiguration

1. Wechseln Sie im Azure AI Foundry-Portal zur **Übersichtsseite** Ihres Projekts.
1. Beachten Sie im Bereich **Projektdetails** die **Projektverbindungszeichenfolge**. Sie verwenden diese Verbindungszeichenfolge, um eine Verbindung mit Ihrem Projekt in einer Clientanwendung herzustellen.
1. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.

    Schließen Sie alle Willkommensbenachrichtigungen, um die Startseite des Azure-Portals anzuzeigen.

1. Verwenden Sie die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud-Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung ohne Speicher in Ihrem Abonnement.

    Die Cloud Shell bietet eine Befehlszeilenschnittstelle in einem Bereich am unteren Rand des Azure-Portals. Sie können die Größe dieses Bereichs ändern oder maximieren, um die Arbeit zu vereinfachen.

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

1. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die Bibliotheken zu installieren, die Sie verwenden möchten:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.3
   dotnet add package Azure.AI.Inference --version 1.0.0-beta.3
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

1. In the code file, replace the **your_project_connection_string** placeholder with the connection string for your project (copied from the project **Overview** page in the Azure AI Foundry portal), and the **your_model_deployment** placeholder with the name you assigned to your gpt-4 model deployment.
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
   projectClient = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```csharp
   // Initialize the project client
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
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

### Ausführen der Chatanwendung

1. Geben Sie im Befehlszeilenbereich von Cloud Shell unterhalb des Code-Editors den folgenden Befehl ein, um die App auszuführen:

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

## Verwenden des OpenAI SDK

Ihre Client-App wird mit dem Azure KI-Modellinferenz-SDK erstellt, was bedeutet, dass sie mit jedem Modell verwendet werden kann, das für den Azure KI-Modellinferenz-Dienst bereitgestellt wird. Das bereitgestellte Modell ist ein OpenAI GPT-Modell, das Sie auch mit dem OpenAI SDK nutzen können.

Lassen Sie uns einige Codeänderungen vornehmen, um zu sehen, wie eine Chatanwendung mit dem OpenAI SDK implementiert wird.

1. Geben Sie in der Befehlszeile der Cloudshell für Ihren Codeordner (*Python* oder *C#*) den folgenden Befehl ein, um das erforderliche Paket zu installieren:

    **Python**

    ```
   pip install openai
    ```

    **C#**

    ```
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.6
   dotnet add package Azure.AI.OpenAI --prerelease
    ```

> **Hinweis**: Eine andere Vorabversion des Azure.AI.Projects-Pakets ist als Zwischenumgehung für einige Inkompatibilitäten mit dem Azure KI-Modellinferenz-SDK erforderlich.

1. Wenn Ihre Codedatei (*chat-app.py* oder *Program.cs*) noch nicht geöffnet ist, geben Sie den folgenden Befehl ein, um sie im Code-Editor zu öffnen:

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Fügen Sie am Anfang der Codedatei die folgende(n) Referenz(en) hinzu:

    **Python**

    ```python
   import openai
    ```

    **C#**

    ```csharp
   using OpenAI.Chat;
   using Azure.AI.OpenAI;
    ```

1. Finden Sie den Kommentar **Get a chat client** und ändern Sie den Code, der zum Erstellen eines Client-Objekts verwendet wird, wie folgt:

    **Python**

    ```python
   # Get a chat client 
   openai_client = projectClient.inference.get_azure_openai_client(api_version="2024-10-21")
    ```

    **C#**

    ```csharp
   // Get a chat client
   ChatClient openaiClient = projectClient.GetAzureOpenAIChatClient(model_deployment);
    ```

    > **Hinweis**: Dieser Code verwendet den Azure AI Foundry-Projektclient, um eine sichere Verbindung zum Standard-Endpunkt des Azure OpenAI Service herzustellen, der mit Ihrem Projekt verknüpft ist. Sie können auch *direkt* eine Verbindung zum Endpunkt herstellen, indem Sie das Azure OpenAI-SDK verwenden, die Endpunkt-URI angeben, die für die Dienstverbindung im Azure AI Foundry-Portal oder auf der entsprechenden Azure OpenAI- oder Azure KI Services-Ressourcenseite im Azure-Portal angezeigt wird, und einen Authentifizierungsschlüssel oder ein Entra-Berechtigungstoken verwenden. Weitere Informationen zum Herstellen einer Verbindung mit dem Azure OpenAI-Dienst finden Sie unter [Von Azure OpenAI unterstützten Programmiersprachen](https://learn.microsoft.com/azure/ai-services/openai/supported-languages).

1. Finden Sie den Kommentar **Initialize prompt with system message** und ändern Sie den Code, um eine Sammlung von Meldungen mit einer Systemansage wie folgt zu initialisieren:

    **Python**

    ```python
   # Initialize prompt with system message
   prompt=[
        {"role": "system", "content": "You are a helpful AI assistant that answers questions."}
    ]
    ```

    **C#**

    ```csharp
   // Initialize prompt with system message
    var prompt = new List<ChatMessage>(){
        new SystemChatMessage("You are a helpful AI assistant that answers questions.")
    };
    ```

1. Finden Sie den Kommentar **Get a chat completion** und ändern Sie den Code, um die Benutzereingabe zum Prompt hinzuzufügen, die Vervollständigung aus Ihrem Modell abzurufen und die Vervollständigung wie folgt zum Prompt hinzuzufügen:

    **Python**

    ```python
   # Get a chat completion
   prompt.append({"role": "user", "content": input_text})
   response = openai_client.chat.completions.create(
        model=model_deployment,
        messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append({"role": "assistant", "content": completion})
    ```

    **C#**

    ```csharp
   // Get a chat completion
   prompt.Add(new UserChatMessage(input_text));
   ChatCompletion completion = openaiClient.CompleteChat(prompt);
   var completionText = completion.Content[0].Text;
   Console.WriteLine(completionText);
   prompt.Add(new AssistantChatMessage(completionText));
    ```

1. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern.

1. Geben Sie im Befehlszeilenbereich von Cloud Shell unterhalb des Code-Editors den folgenden Befehl ein, um die App auszuführen:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Testen Sie die App, indem Sie Fragen wie zuvor übermitteln. Wenn Sie fertig sind, geben Sie `quit` ein, um das Programm zu beenden.

    > **Hinweis**: Das Azure KI-Modellinferenz-SDK und OpenAI SDKs verwenden ähnliche Klassen und Codekonstrukte, sodass der Code minimale Änderungen erforderte. Sie können das Azure KI-Modellinferenz-SDK mit *jedem* Modell verwenden, das für einen Azure KI-Modellinferenz-Dienstendpunkt bereitgestellt wird. Das OpenAI SDK funktioniert nur mit OpenAI-Modellen, aber Sie können es für Modelle verwenden, die entweder für einen Azure KI-Modellinferenz-Dienstendpunkt oder für einen Azure OpenAI-Endpunkt bereitgestellt werden.  

## Zusammenfassung

In dieser Übung haben Sie die Azure AI Foundry, Azure KI-Modellinferenz und Azure OpenAI SDKs verwendet, um eine Client-Anwendung für ein generatives KI-Modell zu erstellen, das Sie in einem Azure AI Foundry-Projekt bereitgestellt haben.

## Bereinigen

Wenn Sie die Erkundung des Azure KI-Foundry-Portals abgeschlossen haben, sollten Sie die Ressourcen, die Sie in dieser Übung erstellt haben, löschen, um unnötige Azure-Kosten zu vermeiden.

1. Kehren Sie zu der Browserregisterkarte zurück, die das Azure-Portal enthält (oder öffnen Sie das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` erneut in einer neuen Browserregisterkarte) und sehen Sie sich den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
