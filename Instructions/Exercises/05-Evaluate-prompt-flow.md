---
lab:
  title: Auswerten von Sprach-Apps in Azure KI Studio
---

# Auswerten von Sprach-Apps in Azure KI Studio

In dieser Übung untersuchen Sie integrierte und benutzerdefinierte Auswertungen, um die Leistung Ihrer KI-Anwendungen mit Azure KI Studio zu bewerten und zu vergleichen.

> Um diese Übung abzuschließen, muss für Ihr Azure-Abonnement der Zugriff auf den Azure OpenAI-Dienst genehmigt werden. Füllen Sie das [Registrierungsformular](https://learn.microsoft.com/legal/cognitive-services/openai/limited-access) aus, um den Zugriff auf Azure OpenAI-Modelle anzufordern.

Zum Erstellen eines Copilots mit Prompt Flow müssen Sie die folgenden Schritte ausführen:

- Erstellen Sie einen KI-Hub und ein KI-Projekt in Azure KI Studio.
- Stellen Sie ein GPT-Modell bereit.
- Bewerten Sie ein Testdatenset mit den integrierten Metriken.
- Definieren Sie eine benutzerdefinierte Auswertungsmetrik, und führen Sie sie für das Test-Dataset aus.

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
1. Geben Sie im Chatfenster die Abfrage `What can you do?` ein, um zu überprüfen, ob das Sprachmodell erwartungsgemäß funktioniert.

Nachdem Sie nun über ein bereitgestelltes Modell mit einer aktualisierten Systemmeldung verfügen, können Sie das Modell auswerten.

## Auswerten eines Sprachmodells in Azure KI Studio

Sie können Modellantworten basierend auf Testdaten manuell überprüfen. Durch die manuelle Überprüfung können Sie verschiedene Eingaben einzeln testen, um zu bewerten, ob das Modell wie erwartet ausgeführt wird.

1. Wählen Sie im **Chat-Playground** in der oberen Leiste die Option **Auswerten** aus.
1. Ein neues Fenster wird geöffnet, in dem die vorherige Systemmeldung bereits ausgefüllt und das bereitgestellte Modell ausgewählt ist.
1. Fügen Sie im Abschnitt **Ergebnis der manuellen Auswertung** fünf Eingaben hinzu, für die Sie die Ausgabe überprüfen wollen. Geben Sie die folgenden fünf Fragen als fünf separate **Eingaben** ein:

   `Can you provide a list of the top-rated budget hotels in Rome?`

   `I'm looking for a vegan-friendly restaurant in New York City. Can you help?`

   `Can you suggest a 7-day itinerary for a family vacation in Orlando, Florida?`

   `Can you help me plan a surprise honeymoon trip to the Maldives?`

   `Are there any guided tours available for the Great Wall of China?`

1. Wählen Sie **Ausführen** in der oberen Leiste aus, um Ausgaben für alle Fragen zu generieren, die Sie als Eingaben hinzugefügt haben.
1. Jetzt können Sie die Ausgaben für jede Frage manuell überprüfen, indem Sie unten rechts in einer Antwort das Symbol mit dem Daumen nach oben oder unten auswählen. Bewerten Sie jede Antwort, und stellen Sie sicher, dass Sie in Ihren Bewertungen mindestens einen Daumen nach oben und einen Daumen nach unten verwenden.
1. Klicken Sie in der oberen Menüleiste auf **Ergebnisse speichern**. Geben Sie `manual_evaluation_results` als Namen für die Ergebnisse ein.
1. Navigieren Sie über das Menü links zu **Auswertungen**.
1. Wählen Sie die Registerkarte **Manuellen Auswertungen** aus, um die manuellen Auswertungen zu finden, die Sie gerade gespeichert haben. Beachten Sie, dass Sie Ihre zuvor erstellten manuellen Auswertungen durchsehen, dort weitermachen, wo Sie aufgehört haben, und die aktualisierten Auswertungen speichern können.
1. Wählen Sie die Registerkarte **Metrikauswertungen** aus, und erstellen Sie eine neue Auswertung mit den folgenden Einstellungen:
    - **Auswertungsname**: *Geben Sie einen eindeutigen Namen ein.*
    - **Welche Art von Szenario bewerten Sie?**: Frage und Antwort ohne Kontext
    - **Wählen Sie die Daten aus, die Sie auswerten möchten**: Fügen Sie Ihr Dataset hinzu.
        - Laden Sie die https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl-JSONL-Datei herunter, und laden Sie sie auf die Benutzeroberfläche hoch.
    - **Metriken auswählen**: Kohärenz, Sprachfluss
    - **Verbindung**: *Ihre KI Services-Verbindung*
    - **Bereitstellungsname/Modell**: *Ihr bereitgestelltes GPT-3.5-Modell*
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
