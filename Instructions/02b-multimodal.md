---
lab:
  title: Entwickeln einer multimodalen generativen KI-App
  description: 'Erfahren Sie, wie Sie Azure AI Foundry verwenden, um eine generative KI-App zu erstellen, die Text-, Bild- und Audioeingaben unterstützt.'
---

# Entwickeln einer multimodalen generativen KI-App

In dieser Übung verwenden Sie das generative KI-Modell *Phi-4-multimodal-instruct*, um Antworten auf Prompts zu generieren, die Text, Bilder und Audio enthalten. Sie entwickeln eine App, die KI-Unterstützung bei frischen Produkten in einem Lebensmittelgeschäft bietet, indem Sie Azure AI Foundry und den Azure KI-Modellinferenz-Dienst verwenden.

Diese Übung dauert ca. **30** Minuten.

> **Hinweis**: Diese Übung basiert auf Vorabversionen von SDKs, die sich möglicherweise noch ändern können. Wo nötig, haben wir spezielle Versionen von Paketen verwendet, die möglicherweise nicht die neuesten verfügbaren Versionen widerspiegeln. Es können unerwartete Verhaltensweisen, Warnungen oder Fehler auftreten.

## Erstellen eines Azure KI Foundry-Projekts

Beginnen wir mit dem Erstellen eines Azure AI Foundry-Projekts.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartbereiche, die beim ersten Anmelden geöffnet werden, und verwenden Sie bei Bedarf das **Azure AI Foundry-Logo** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht:

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

2. Wählen Sie auf der Startseite **+ Projekt erstellen**.
3. Geben Sie im Assistenten **Projekt erstellen** einen gültigen Namen für Ihr Projekt ein und wählen Sie die Option zum Erstellen eines neuen Hubs, falls ein vorhandener Hub vorgeschlagen wird. Überprüfen Sie dann die Azure-Ressourcen, die automatisch erstellt werden, um Ihren Hub und Ihr Projekt zu unterstützen.
4. Wählen Sie **Anpassen** aus und legen Sie die folgenden Einstellungen für Ihren Hub fest:
    - **Hubname**: *Ein gültiger Name für Ihren Hub*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*
    - **Region:** Wählen Sie eine der folgenden Regionen aus\*:
        - East US
        - USA (Ost) 2
        - USA Nord Mitte
        - USA Süd Mitte
        - Schweden, Mitte
        - USA (Westen)
        - USA, Westen 3
    - **Azure KI Services oder Azure OpenAI verbinden**: *Erstellen Sie eine neue KI-Dienst-Ressource*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Zum Zeitpunkt des Schreibens steht das Microsoft Modell *Phi-4-multimodal-instruct*, das wir in dieser Übung verwenden werden, in diesen Regionen zur Verfügung. Sie können die neueste regionale Verfügbarkeit für bestimmte Modelle in der [Azure AI Foundry-Dokumentation](https://learn.microsoft.com/azure/ai-foundry/how-to/deploy-models-serverless-availability#region-availability) überprüfen. Wenn später in der Übung eine regionale Kontingentgrenze erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen.

5. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
6. Sobald Ihr Projekt erstellt wurde, schließen Sie alle angezeigten Tipps und überprüfen Sie die Projektseite im Azure AI Foundry-Portal, die in etwa wie in der folgenden Abbildung aussehen sollte:

    ![Screenshot eines Azure KI-Projekts im Azure AI Foundry-Portal.](./media/ai-foundry-project.png)

## Bereitstellen eines Modells

Jetzt können Sie ein *Phi-4-multimodal-instruct*-Modell bereitstellen, um multimodale Prompts zu unterstützen.

1. Verwenden Sie in der Symbolleiste oben rechts auf Ihrer Azure KI-Foundry-Projektseite das Symbol „Vorschau-Funktionen“ (**Preview features**) (**&#9215;**), um sicherzustellen, dass die Funktion **Modelle im Azure KI-Modellinferenzdienst bereitstellen**) aktiviert ist. Mit diesem Feature wird sichergestellt, dass Ihre Modellbereitstellung für den Azure KI-Rückschlussdienst verfügbar ist, den Sie im Anwendungscode verwenden.
2. Wählen Sie im linken Fensterbereich für Ihr Projekt im Abschnitt **Meine Assets** die Seite **Modelle + Endpunkte**.
3. Wählen Sie auf der Seite **Modelle + Endpunkte** auf der Registerkarte **Modellbereitstellungen** im Menü **+ Modell bereitstellen** die Option **Basismodell bereitstellen**.
4. Suchen Sie in der Liste das Modell **Phi-4-multimodal-instruct**, wählen Sie es aus und bestätigen Sie es.
5. Stimmen Sie der Lizenzvereinbarung zu, wenn Sie dazu aufgefordert werden, und stellen Sie das Modell mit den folgenden Einstellungen bereit, indem Sie **Anpassen** in den Bereitstellungsdetails wählen:
    - **Bereitstellungsname**: *Ein gültiger Name für die Bereitstellung Ihres Modells*
    - **Bereitstellungstyp**: Globaler Standard
    - **Bereitstellungsdetails**: *Verwenden der Standardeinstellungen.*
6. Warten Sie, bis der Bereitstellungsstatus **Abgeschlossen** ist.

## Das Erstellen einer Clientanwendung

Nachdem Sie das Modell bereitgestellt haben, können Sie die Bereitstellung in einer Clientanwendung verwenden.

> **Tipp**: Sie können Ihre Lösung mit Python oder Microsoft C# entwickeln. Folgen Sie den Anweisungen im entsprechenden Abschnitt für Ihre ausgewählte Sprache.

### Vorbereiten der Anwendungskonfiguration

1. Wechseln Sie im Azure AI Foundry-Portal zur **Übersichtsseite** Ihres Projekts.
2. Beachten Sie im Bereich **Projektdetails** die **Projektverbindungszeichenfolge**. Sie verwenden diese Verbindungszeichenfolge, um eine Verbindung mit Ihrem Projekt in einer Clientanwendung herzustellen.
3. Öffnen Sie eine neue Browserregisterkarte (wobei das Azure AI Foundry-Portal auf der vorhandenen Registerkarte geöffnet bleibt). Wechseln Sie dann in der neuen Registerkarte zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an, wenn Sie dazu aufgefordert werden.

    Schließen Sie alle Willkommensbenachrichtigungen, um die Startseite des Azure-Portals anzuzeigen.

1. Verwenden Sie die Schaltfläche **[\>_]** rechts neben der Suchleiste oben auf der Seite, um eine neue Cloud-Shell im Azure-Portal zu erstellen, und wählen Sie eine ***PowerShell***-Umgebung ohne Speicher in Ihrem Abonnement.

    Die Cloud-Shell stellt eine Befehlszeilenschnittstelle in einem Bereich unten im Azure-Portal bereit. Sie können die Größe dieses Bereichs ändern oder maximieren, um die Arbeit zu vereinfachen.

    > **Hinweis**: Wenn Sie zuvor eine Cloud-Shell erstellt haben, die eine *Bash*-Umgebung verwendet, wechseln Sie zu ***PowerShell***.

5. Wählen Sie in der Cloud Shell-Symbolleiste im Menü **Einstellungen** das Menüelement **Zur klassischen Version wechseln** aus (dies ist für die Verwendung des Code-Editors erforderlich).

    **<font color="red">Stellen Sie sicher, dass Sie zur klassischen Version der Cloud Shell gewechselt haben, bevor Sie fortfahren.</font>**

1. Geben Sie im Cloud Shell-Bereich die folgenden Befehle ein, um das GitHub-Repository mit den Code-Dateien für diese Übung zu klonen (geben Sie den Befehl ein oder kopieren Sie ihn in die Zwischenablage, klicken Sie dann mit der rechten Maustaste in die Befehlszeile und fügen Sie ihn als Nur-Text ein):

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **TIPP**: Wenn Sie Befehle in die Cloudshell einfügen, kann die Ausgabe einen großen Teil des Bildschirmpuffers in Anspruch nehmen. Sie können den Bildschirm löschen, indem Sie den Befehl `cls` eingeben, um sich besser auf die einzelnen Aufgaben konzentrieren zu können.

7. Navigieren Sie nach dem Klonen des Repositorys zu dem Ordner, der die Codedateien der Anwendung enthält:  

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/multimodal/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/multimodal/c-sharp
    ```

8. Geben Sie im Befehlszeilenbereich von Cloud Shell den folgenden Befehl ein, um die Bibliotheken zu installieren, die Sie verwenden möchten:

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

9. Geben Sie den folgenden Befehl ein, um die bereitgestellte Konfigurationsdatei zu bearbeiten:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    Die Datei wird in einem Code-Editor geöffnet.

10. Ersetzen Sie in der Datei den Platzhalter **your_project_connection_string** durch die Verbindungszeichenfolge für Ihr Projekt (kopiert von der Seite **Übersicht** des Projekts im Azure KI Foundry-Portal) und den Platzhalter **your_model_deployment** durch den Namen, den Sie Ihrer Bereitstellung des GPT-4-Modells zugewiesen haben.
11. Nachdem Sie die Platzhalter ersetzt haben, speichern Sie Ihre Änderungen im Code-Editor mit dem Befehl **STRG+S** oder **Rechtsklick > Speichern** und schließen Sie den Code-Editor mit dem Befehl **STRG+Q** oder **Rechtsklick > Beenden**, wobei die Cloud Shell-Befehlszeile geöffnet bleibt.

### Schreiben von Code, um sich mit Ihrem Projekt zu verbinden und einen Chat-Client für Ihr Modell zu erhalten

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

2. Beachten Sie in der Codedatei die vorhandenen Anweisungen, die am Anfang der Datei hinzugefügt wurden, um die erforderlichen SDK-Namespaces zu importieren. Fügen Sie dann unter dem Kommentar **Add references** den folgenden Code hinzu, um auf die Namespaces in den zuvor installierten Bibliotheken zu verweisen:

    **Python**

    ```python
   # Add references
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import (
       SystemMessage,
       UserMessage,
       TextContentItem,
       ImageContentItem,
       ImageUrl,
       AudioContentItem,
       InputAudio,
       AudioContentFormat,
   )
    ```

    **C#**

    ```csharp
   // Add references
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

3. Beachten Sie, dass der Code in der **Hauptfunktion** unter dem Kommentar **Get configuration settings** die Zeichenfolge für die Projektverbindung und die Werte für den Namen der Modellbereitstellung lädt, die Sie in der Konfigurationsdatei definiert haben.
4. Fügen Sie unter dem Kommentar **Projektclient initialisieren** den folgenden Code hinzu, um eine Verbindung zu Ihrem Azure KI-Foundry-Projekt herzustellen, wobei Sie die Anmeldeinformationen für Azure verwenden, mit denen Sie derzeit angemeldet sind:

    **Python**

    ```python
   # Get configuration settings
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```csharp
   // Get configuration settings
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

5. Fügen Sie unter dem Kommentar **Get a chat client** den folgenden Code ein, um ein Client-Objekt zum Chatten mit Ihrem Modell zu erstellen:

    **Python**

    ```python
   # Get a chat client
   chat_client = project_client.inference.get_chat_completions_client(model=model_deployment)
    ```

    **C#**

    ```csharp
   // Get a chat client
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```


### Schreiben von Code zur Verwendung eines textbasierten Prompts

1. Beachten Sie, dass der Code einen Loop (Schleife) enthält, damit Benutzende einen Prompt eingeben können, bis sie „quit“ eingeben. Fügen Sie dann im Schleifenabschnitt unter dem Kommentar **Get a response to text input** den folgenden Code hinzu, um einen textbasierten Prompt zu übermitteln und die Antwort aus Ihrem Modell abzurufen:

    **Python**

    ```python
   # Get a response to text input
   response = chat_client.complete(
       messages=[
           SystemMessage(system_message),
           UserMessage(content=[TextContentItem(text= prompt)])
       ])
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
   // Get a response to text input
   var requestOptions = new ChatCompletionsOptions()
   {
   Model = model_deployment,
   Messages =
       {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage(prompt),
       }
   };

   Response<ChatCompletions> response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern. Schließen Sie sie jedoch noch nicht.

3. Geben Sie im Cloud Shell-Befehlszeilenbereich unterhalb des Code-Editors den folgenden Befehl ein, um die App auszuführen:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Wenn Sie dazu aufgefordert werden, geben Sie `1` ein, um einen textbasierten Prompt zu verwenden, und geben Sie dann den Prompt `I want to make an apple pie. What kind of apple should I use?` ein
5. Überprüfen Sie die Antwort. Geben Sie dann `quit` zum Beenden des Programms ein.

### Schreiben von Code zur Verwendung eines bildbasierten Prompts

1. Fügen Sie im Code-Editor für die Datei **chat-app.py** im Schleifenabschnitt unter dem Kommentar **Get a response to image input** den folgenden Code hinzu, um einen Prompt zu übermitteln, der das folgende Bild enthält:

    ![Ein Foto einer Orange.](../labfiles/multimodal/orange.jpg)

    **Python**

    ```python
   # Get a response to image input
   image_url = "https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/orange.jpg"
   image_format = "jpeg"
   request = Request(image_url, headers={"User-Agent": "Mozilla/5.0"})
   image_data = base64.b64encode(urlopen(request).read()).decode("utf-8")
   data_url = f"data:image/{image_format};base64,{image_data}"

   response = chat_client.complete(
       messages=[
           SystemMessage(system_message),
           UserMessage(content=[
               TextContentItem(text=prompt),
               ImageContentItem(image_url=ImageUrl(url=data_url))
           ]),
       ]
   )
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
  // Get a response to image input
   string imageUrl = "https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/orange.jpg";
   ChatCompletionsOptions requestOptions = new ChatCompletionsOptions()
   {
       Messages = {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage([
               new ChatMessageTextContentItem(prompt),
               new ChatMessageImageContentItem(new Uri(imageUrl))
           ]),
       },
       Model = model_deployment
   };
   var response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern. Schließen Sie sie jedoch noch nicht.

3. Geben Sie im Cloud Shell-Befehlszeilenbereich unterhalb des Code-Editors den folgenden Befehl ein, um die App auszuführen:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Wenn Sie dazu aufgefordert werden, geben Sie `2` ein, um einen bildbasierten Prompt zu verwenden und geben Sie dann den Prompt `I don't know what kind of fruit this is. Can you identify it, and tell me what kinds of food I could make with it?` ein
5. Überprüfen Sie die Antwort. Geben Sie dann `quit` zum Beenden des Programms ein.

### Schreiben von Code zur Verwendung eines audiobasierten Prompts

1. Fügen Sie im Code-Editor für die Datei **chat-app.py** im Schleifenabschnitt unter dem Kommentar **Get a response to audio input** den folgenden Code hinzu, um einen Prompt zu übermitteln, der folgendes Audio enthält:

    <video controls src="./media/manzanas.mp4" title="Es ist 2:15 Uhr." width="150"></video>

    **Python**

    ```python
   # Get a response to audio input
   file_path="https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/manzanas.mp3"
   response = chat_client.complete(
           messages=[
               SystemMessage(system_message),
               UserMessage(
                   [
                       TextContentItem(text=prompt),
                       {
                           "type": "audio_url",
                           "audio_url": {"url": file_path}
                       }
                   ]
               )
           ]
       )
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
   // Get a response to audio input
   string audioUrl="https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/manzanas.mp3";
   var requestOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage(
               new ChatMessageTextContentItem(prompt),
               new ChatMessageAudioContentItem(new Uri(audioUrl))),
       },
       Model = model_deployment
   };
   var response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Verwenden Sie den Befehl **STRG+S**, um Ihre Änderungen in der Codedatei zu speichern. Sie können den Code-Editor (**STRG+Q**) auch schließen, wenn Sie möchten.

3. Geben Sie im Cloud Shell-Befehlszeilenbereich unterhalb des Code-Editors den folgenden Befehl ein, um die App auszuführen:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Wenn Sie dazu aufgefordert werden, geben Sie `3` ein, um einen audiobasierten Prompt zu verwenden ein, und geben Sie dann den Prompt `What is this customer saying in English?` ein
5. Überprüfen Sie die Antwort.
6. Sie können die App weiterhin ausführen, verschiedene Prompttypen auswählen und unterschiedliche Prompts ausprobieren. Wenn Sie fertig sind, geben Sie `quit` ein, um das Programm zu beenden.

    Wenn Sie Zeit haben, können Sie den Code so ändern, dass eine andere Systemansage und Ihre eigenen über das Internet zugänglichen Bild- und Audiodateien verwendet werden.

    > **Hinweis**: In dieser einfachen App wurde keine Logik implementiert, um die aufgezeichneten Unterhaltungen beizubehalten. Daher behandelt das Modell jeden Prompt als neue Anforderung ohne Kontext des vorherigen Prompts.

## Zusammenfassung

In dieser Übung haben Sie Azure AI Foundry und das Azure AI Inference SDK verwendet, um eine Client-Anwendung zu erstellen, die ein multimodales Modell verwendet, um Antworten auf Text, Bilder und Audio zu generieren.

## Bereinigen

Wenn Sie die Erkundung von Azure AI Foundry abgeschlossen haben, sollten Sie die Ressourcen, die Sie in dieser Übung erstellt haben, löschen, um unnötige Azure-Kosten zu vermeiden.

1. Kehren Sie zu der Browserregisterkarte zurück, die das Azure-Portal enthält (oder öffnen Sie das [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com` erneut in einer neuen Browserregisterkarte) und sehen Sie sich den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
