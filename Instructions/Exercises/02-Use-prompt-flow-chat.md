---
lab:
  title: "Erste Schritte beim Erstellen von benutzerdefinierten Copilots mit Prompt Flow in Azure\_KI Studio"
---

# Erste Schritte beim Erstellen von benutzerdefinierten Copilots mit Prompt Flow in Azure KI Studio

In dieser Übung verwenden Sie den Prompt Flow von Azure KI Studio, um einen benutzerdefinierten Copilot zu erstellen, der einen Benutzerprompt- und Chatverlauf als Eingaben verwendet und ein GPT-Modell aus Azure OpenAI verwendet, um eine Ausgabe zu generieren.

> Um diese Übung abzuschließen, muss für Ihr Azure-Abonnement der Zugriff auf den Azure OpenAI-Dienst genehmigt werden. Füllen Sie das [Registrierungsformular](https://learn.microsoft.com/legal/cognitive-services/openai/limited-access) aus, um den Zugriff auf Azure OpenAI-Modelle anzufordern.

Zum Erstellen eines Copilots mit Prompt Flow müssen Sie die folgenden Schritte ausführen:

- Erstellen Sie einen KI-Hub und ein KI-Projekt in Azure KI Studio.
- Stellen Sie ein GPT-Modell bereit.
- Erstellen Sie einen Flow, der das bereitgestellte GPT-Modell verwendet, um eine Antwort basierend auf der Eingabe des Benutzers zu generieren.
- Testen Sie den Flow, und stellen Sie ihn bereit.

## Erstellen eines KI-Hubs und eines KI-Projekts in Azure KI Studio

Zunächst erstellen Sie ein Azure KI Studio-Projekt innerhalb eines Azure KI-Hubs:

1. Öffnen Sie in einem Webbrowser [https://ai.azure.com](https://ai.azure.com), und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.
1. Wählen Sie die **Startseite** und dann **+ Neues Projekt** aus.
1. Erstellen Sie im **Assistenten zum Erstellen eines neuen Projekts** ein Projekt mit den folgenden Einstellungen:
    - **Projektname:** *Ein eindeutiger Name für Ihr Projekt*
    - **Hub:** *Erstellen Sie einen neuen Hub mit den folgenden Einstellungen:*
        - **Hub-Name:** *Ein eindeutiger Name*
        - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
        - **Ressourcengruppe:** *Neue Ressourcengruppe*
        - **Speicherort:** *Treffen Sie eine **zufällige** Auswahl aus einer der folgenden Regionen*\*
        - Australien (Osten)
        - Kanada, Osten
        - East US
        - USA (Ost) 2
        - Frankreich, Mitte
        - Japan, Osten
        - USA Nord Mitte
        - Schweden, Mitte
        - Schweiz, Norden
        - UK, Süden
    - **Verbinden von Azure KI Services oder Azure OpenAI**: *Erstellen einer neuen Verbindung*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die aufgeführten Regionen enthalten das Standardkontingent für die in dieser Übung verwendeten Modelltypen. Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze erreicht. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen. Erfahren Sie mehr über die [Modellverfügbarkeit pro Region](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Überprüfen Sie Ihre Konfiguration, und erstellen Sie Ihr Projekt.
1. Warten Sie 5 bis 10 Minuten, bis Ihr Projekt erstellt wurde.

## Bereitstellen eines GPT-Modells

Um ein Sprachmodell im Prompt Flow zu verwenden, müssen Sie zuerst ein Modell bereitstellen. Mit Azure KI Studio können Sie OpenAI-Modelle bereitstellen, die Sie in Ihren Flows verwenden können.

1. Wählen Sie im Navigationsbereich auf der linken Seite unter **Komponenten** die Seite **Bereitstellungen** aus.
1. Erstellen Sie eine neue Bereitstellung des **gpt-35-Turbo**-Modells mit den folgenden Einstellungen:
    - **Bereitstellungsname:** *Ein eindeutiger Name für die Modellimplementierung*
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **Bereitstellungstyp**: Standard
    - **Verbundene Azure OpenAI-Ressource**: *Wählen Sie die Standardverbindung aus.*
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: Standard
1. Warten Sie, bis das Modell bereitgestellt wurde. Wenn die Bereitstellung bereit ist, wählen Sie **Im Playground öffnen** aus.
1. Geben Sie im Chatfenster die Abfrage „`What can you do?`“ ein.

    Beachten Sie, dass die Antwort generisch ist, da es keine spezifischen Anweisungen für den Assistenten gibt. Um den Fokus auf eine bestimmte Aufgabe zu legen, können Sie den Systemprompt ändern.

1. Ändern Sie die **Systemmeldung** wie folgt:

   ```
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Wählen Sie **Änderungen übernehmen** aus.
1. Geben Sie im Chatfenster dieselbe Abfrage wie zuvor ein: `What can you do?` Beachten Sie die geänderte Antwort.

Nachdem Sie nun mit der Systemmeldung für das bereitgestellte GPT-Modell experimentiert haben, können Sie die Anwendung weiter anpassen, indem Sie mit dem Prompt Flow arbeiten.

## Erstellen und Ausführen eines Chatflows in Azure KI Studio

Sie können einen neuen Flow mithilfe einer Vorlage erstellen oder einen Flow basierend auf Ihren Konfigurationen im Playground erstellen. Da Sie bereits im Playground experimentiert haben, verwenden Sie diese Option, um einen neuen Flow zu erstellen.

1. Wählen Sie im **Chat-Playground** in der oberen Leiste die Option **Prompt Flow** aus.
1. Geben Sie den Ordnernamen „`Travel-Chat`“ ein.

    Ein einfacher Chatflow wird für Sie erstellt. Beachten Sie, dass folgende Elemente vorhanden sind: zwei Eingaben (Chatverlauf und Frage des Benutzers), ein LLM-Knoten, der eine Verbindung mit Ihrem bereitgestellten Sprachmodell herstellt, und eine Ausgabe, die die Antwort im Chat widerspiegelt.

    Zum Testen des Flows benötigen Sie eine Computeressource.

1. Wählen Sie **Computesitzung starten** in der oberen Leiste aus.
1. Es dauert 1 bis 3 Minuten, bis die Computesitzung gestartet wird.
1. Suchen Sie den LLM-Knoten mit dem Namen **Chat**. Beachten Sie, dass der Prompt bereits den Systemprompt enthält, den Sie im Chat-Playground angegeben haben.

    Sie müssen den LLM-Knoten noch mit Ihrem bereitgestellten Modell verbinden.

1. Wählen Sie im Abschnitt „LLM-Knoten“ für **Verbindung** die Verbindung aus, die beim Erstellen des KI-Hubs für Sie erstellt wurde.
1. Als **API** wählen Sie **Chat** aus.
1. Wählen Sie unter **Bereitstellungsname** das von Ihnen bereitgestellte **GPT-35-Turbo**-Modell aus.
1. Wählen Sie für **Antwortformat** die Option **{"type":"text"}** aus.
1. Überprüfen Sie das Feld für den Prompt, und stellen Sie sicher, dass es wie folgt aussieht:

   ```yml
   {% raw %}
   system:
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.

   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.

   {% for item in chat_history %}
   user:
   {{item.inputs.question}}
   assistant:
   {{item.outputs.answer}}
   {% endfor %}

   user:
   {{question}}
   {% endraw %}
   ```

### Testen und Bereitstellen des Flows

Nachdem Sie den Flow entwickelt haben, können Sie ihn im Chatfenster testen.

1. Stellen Sie sicher, dass die Computesitzung ausgeführt wird.
1. Wählen Sie **Speichern**.
1. Wählen Sie **Chat** aus, um den Flow zu testen.
1. Geben Sie die Abfrage ein: `I have one day in London, what should I do?`. Überprüfen Sie dann die Ausgabe.

    Wenn Sie mit dem Verhalten des von Ihnen erstellten Flows zufrieden sind, können Sie ihn bereitstellen.

1. Wählen Sie **Bereitstellen** aus, um den Flow mit den folgenden Einstellungen bereitzustellen:
    - **Grundeinstellungen**:
        - **Endpunkt**: Neu
        - **Endpunktname**: *Geben Sie einen eindeutigen Namen ein.*
        - **Bereitstellungsname**: *Geben Sie einen eindeutigen Namen ein.*
        - **VM**: Standard_DS3_v2
        - **Instanzenanzahl:** 3
        - **Rückschließen der Datensammlung**: Aktiviert
    - **Erweiterte Einstellungen**:
        - *Verwenden der Standardeinstellungen*
1. Wählen Sie in Azure KI Studio im Navigationsbereich auf der linken Seite unter **Komponenten** die Seite **Bereitstellungen** aus.
1. Beachten Sie, dass standardmäßig die **Modellbereitstellungen** aufgelistet werden, einschließlich Ihres bereitgestellten Sprachmodells.
1. Wählen Sie die Registerkarte **App-Bereitstellungen** aus, um Ihren bereitgestellten Flow zu finden. Es kann einige Zeit dauern, bis die Bereitstellung aufgelistet und erfolgreich erstellt wird.
1. Wenn die Bereitstellung erfolgreich war, wählen Sie sie aus. Geben Sie dann auf der Seite **Test** den Prompt `What is there to do in San Francisco?` ein und überprüfen Sie die Antwort.
1. Geben Sie den Prompt `Where else could I go?` ein und überprüfen Sie die Antwort.
1. Zeigen Sie die Seite **Nutzen** für den Endpunkt an und beachten Sie, dass sie Verbindungsinformationen und Beispielcode enthält, die Sie zum Erstellen einer Clientanwendung für Ihren Endpunkt verwenden können, sodass Sie die Prompt Flow-Lösung als benutzerdefinierten Copilot in eine Anwendung integrieren können.

## Löschen von Azure-Ressourcen

Wenn Sie mit der Erkundung von Azure KI Studio fertig sind, löschen Sie die erstellten Ressourcen, um unnötige Azure-Kosten zu vermeiden.

- Navigieren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`.
- Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressourcengruppen** aus.
- Wählen Sie die Ressourcengruppe aus, die Sie für diese Übung erstellt haben.
- Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
- Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
