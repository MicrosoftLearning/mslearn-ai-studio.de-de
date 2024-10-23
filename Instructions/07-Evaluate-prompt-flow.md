---
lab:
  title: Bewerten Sie die Leistung Ihres benutzerdefinierten Copiloten im Azure KI Studio
---

# Bewerten Sie die Leistung Ihres benutzerdefinierten Copiloten im Azure KI Studio

In dieser Übung untersuchen Sie integrierte und benutzerdefinierte Auswertungen, um die Leistung Ihrer KI-Anwendungen mit Azure KI Studio zu bewerten und zu vergleichen.

Diese Übung dauert ungefähr **30** Minuten.

## Erstellen eines KI-Hubs und eines KI-Projekts in Azure KI Studio

Zunächst erstellen Sie ein Azure KI Studio-Projekt innerhalb eines Azure KI-Hubs:

1. Öffnen Sie in einem Webbrowser [https://ai.azure.com](https://ai.azure.com), und melden Sie sich mit Ihren Azure-Anmeldeinformationen an.
1. Wählen Sie die **Startseite** und dann **+ Neues Projekt** aus.
1. Geben Sie im **Projekt erstellen**-Assistenten einen eindeutigen Namen für Ihr Projekt ein, wählen Sie dann **Anpassen** und legen Sie die folgenden Einstellungen fest:
    - **Hub-Name:** *Ein eindeutiger Name*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe:** *Neue Ressourcengruppe*
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** aus, wählen Sie dann **gpt-35-turbo** im Fenster der Standorthilfe aus und verwenden Sie die empfohlene Region\*
    - **Verbinden von Azure KI Services oder Azure OpenAI**: *Erstellen einer neuen Verbindung*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die in der Standorthilfe aufgelisteten Regionen enthalten Standardquoten für den/die in dieser Übung verwendeten Modelltyp(en). Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze erreicht. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen. Erfahren Sie mehr über die [Modellverfügbarkeit pro Region](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Klicken Sie auf **Weiter**, um Ihre Konfiguration zu überprüfen.
1. Wählen Sie **Ein Projekt erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.

## Bereitstellen eines GPT-Modells

Um ein Sprachmodell im Prompt Flow zu verwenden, müssen Sie zuerst ein Modell bereitstellen. Mit Azure KI Studio können Sie OpenAI-Modelle bereitstellen, die Sie in Ihren Flows verwenden können.

1. Wählen Sie im Navigationsbereich auf der linken Seite unter **Komponenten** die Seite **Bereitstellungen** aus.
1. Erstellen Sie eine neue Bereitstellung des **gpt-35-Turbo**-Modells mit den folgenden Einstellungen:
    - **Bereitstellungsname:** *Ein eindeutiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Standard
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **KI-Ressource**: *Wählen Sie die zuvor erstellte Quelle* aus
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: StandardV2 
    - **Dynamische Quote aktivieren**: Deaktiviert
1. Warten Sie, bis das Modell bereitgestellt wurde. Wenn die Bereitstellung bereit ist, wählen Sie **Im Playground öffnen** aus.
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

1. Wählen Sie **Speichern**.
1. Geben Sie im Chatfenster die Abfrage `What can you do?` ein, um zu überprüfen, ob das Sprachmodell erwartungsgemäß funktioniert.

Nachdem Sie nun über ein bereitgestelltes Modell mit einer aktualisierten Systemmeldung verfügen, können Sie das Modell auswerten.

## Auswerten eines Sprachmodells in Azure KI Studio

Sie können Modellantworten basierend auf Testdaten manuell überprüfen. Durch die manuelle Überprüfung können Sie verschiedene Eingaben einzeln testen, um zu bewerten, ob das Modell wie erwartet ausgeführt wird.

1. Wählen Sie im **Chat-Playground** das Dropdown-Menü **Auswerten** in der oberen Leiste und wählen Sie **Manuelle Auswertung**.
1. Ändern Sie die **Systemnachricht** in die gleiche Nachricht, die Sie oben verwendet haben (hier wieder enthalten):

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

1. Fügen Sie im Abschnitt **Ergebnis der manuellen Auswertung** fünf Eingaben hinzu, für die Sie die Ausgabe überprüfen wollen. Geben Sie die folgenden fünf Fragen als fünf separate **Eingaben** ein:

   `Can you provide a list of the top-rated budget hotels in Rome?`

   `I'm looking for a vegan-friendly restaurant in New York City. Can you help?`

   `Can you suggest a 7-day itinerary for a family vacation in Orlando, Florida?`

   `Can you help me plan a surprise honeymoon trip to the Maldives?`

   `Are there any guided tours available for the Great Wall of China?`

1. Wählen Sie **Ausführen** in der oberen Leiste aus, um Ausgaben für alle Fragen zu generieren, die Sie als Eingaben hinzugefügt haben.
1. Jetzt können Sie die Ausgaben für jede Frage manuell überprüfen, indem Sie unten rechts in einer Antwort das Symbol mit dem Daumen nach oben oder unten auswählen. Bewerten Sie jede Antwort, und stellen Sie sicher, dass Sie in Ihren Bewertungen mindestens einen Daumen nach oben und einen Daumen nach unten verwenden.
1. Klicken Sie in der oberen Menüleiste auf **Ergebnisse speichern**. Geben Sie `manual_evaluation_results` als Namen für die Ergebnisse ein.
1. Navigieren Sie über das Menü auf der linken Seite zu **Auswertungen**.
1. Wählen Sie die Registerkarte **Manuellen Auswertungen** aus, um die manuellen Auswertungen zu finden, die Sie gerade gespeichert haben. Beachten Sie, dass Sie Ihre zuvor erstellten manuellen Auswertungen durchsehen, dort weitermachen, wo Sie aufgehört haben, und die aktualisierten Auswertungen speichern können.

## Bewerten Ihres Copilots mit integrierten Metriken

Wenn Sie einen Copilot mit einem Chatfluss erstellt haben, können Sie den Fluss bewerten, indem Sie eine Batchausführung durchführen und die Leistung des Flusses mit integrierten Metriken bewerten.

1. Wählen Sie die Registerkarte **Automatisierte Auswertungen** und erstellen Sie eine **Neue Auswertung** mit den folgenden Einstellungen: <details>  
      <summary><b>Tip zur Problembehandlung</b>: Berechtigungsfehler</summary>
        <p>Wenn beim Erstellen eines neuen Eingabeaufforderungsflusses ein Berechtigungsfehler angezeigt wird, versuchen Sie Folgendes zur Problembehandlung:</p>
        <ul>
          <li>Wählen Sie im Ressourcenmenü des Azure-Portals AI Dienste aus.</li>
          <li>Bestätigen Sie auf der Registerkarte Identität unter Ressourcenverwaltung, dass es sich um eine systemzugewiesene, verwaltete Identität handelt.</li>
          <li>Navigieren Sie zum dazugehörigen Speicherkonto. Fügen Sie auf der IAM-Seite die Rollenzuweisung <em>Storage blob data reader</em> hinzu.</li>
          <li>Wählen Sie unter <strong>Zugriff zuweisen zu</strong> die Option <strong>Verwaltete Identität</strong>, <strong>+ Mitglieder auswählen</strong>, und wählen Sie die Option <strong>Alle vom System zugewiesenen verwalteten Identitäten</strong>.</li>
          <li>Überprüfen und zuweisen, um die neuen Einstellungen zu speichern, und wiederholen Sie den vorherigen Schritt.</li>
        </ul>
    </details>

    - **Was möchten Sie auswerten?**: Dataset
    - **Auswertungsname**: *Geben Sie einen eindeutigen Namen ein.*
    - **Welche Art von Szenario bewerten Sie?**: Frage und Antwort ohne Kontext
    - Wählen Sie **Weiter** aus.
    - **Wählen Sie die Daten aus, die Sie auswerten möchten**: Fügen Sie Ihr Dataset hinzu.
        - Laden Sie die https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl-JSONL-Datei herunter, und laden Sie sie auf die Benutzeroberfläche hoch.
    - **Metriken auswählen**: Kohärenz, Sprachfluss
    - **Verbindung**: *Ihre KI Services-Verbindung*
    - **Bereitstellungsname/Modell**: *Ihr bereitgestelltes GPT-3.5-Modell*
1. Klicken Sie auf **Weiter**, überprüfen Sie Ihre Daten und übermitteln Sie die neue Auswertung.
1. Warten Sie, bis die Auswertungen abgeschlossen sind. Eventuell müssen Sie die Seite aktualisieren.
1. Wählen Sie die gerade erstellte Auswertung aus.
1. Erkunden Sie das **Metrik-Dashboard** und **Detaillierte Metrikergebnisse**.

## Löschen von Azure-Ressourcen

Wenn Sie mit der Erkundung von Azure KI Studio fertig sind, löschen Sie die erstellten Ressourcen, um unnötige Azure-Kosten zu vermeiden.

- Navigieren Sie zum [Azure-Portal](https://portal.azure.com) unter `https://portal.azure.com`.
- Wählen Sie auf der **Startseite** des Azure-Portals die Option **Ressourcengruppen** aus.
- Wählen Sie die Ressourcengruppe aus, die Sie für diese Übung erstellt haben.
- Wählen Sie oben auf der Seite **Übersicht** für Ihre Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
- Geben Sie den Namen der Ressourcengruppe ein, um zu bestätigen, dass Sie sie löschen möchten, und wählen Sie **Löschen** aus.
