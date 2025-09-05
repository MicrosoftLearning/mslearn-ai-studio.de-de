---
lab:
  title: Auswählen und Bereitstellen eines Sprachmodells
  description: 'Generative KI-Anwendungen basieren auf einem oder mehreren Sprachmodellen. Erfahren Sie, wie Sie geeignete Modelle für Ihr generatives KI-Projekt finden und auswählen.'
---

# Auswählen und Bereitstellen eines Sprachmodells

Der Modellkatalog von Azure KI Foundry dient als zentrales Repository, in dem Sie eine Vielzahl von Modellen erforschen und verwenden können, was die Erstellung Ihres generativen KI-Szenarios erleichtert.

In dieser Übung erkunden Sie den Modellkatalog im Azure AI Foundry-Portal und vergleichen potenzielle Modelle für eine generative KI-Anwendung, die bei der Lösung von Problemen hilft.

Diese Übung dauert ungefähr **25** Minuten.

> **Hinweis**: Einige der in dieser Übung verwendeten Technologien befinden sich in der Vorschau oder in der aktiven Entwicklung. Es kann zu unerwartetem Verhalten, Warnungen oder Fehlern kommen.

## Untersuchen von Modellen

Beginnen wir damit, sich beim Azure AI Foundry-Portal anzumelden und einige der verfügbaren Modelle zu erkunden.

1. Öffnen Sie in einem Webbrowser unter `https://ai.azure.com` das [Azure KI Foundry-Portal](https://ai.azure.com) und melden Sie sich mit Ihren Azure-Anmeldeinformationen an. Schließen Sie alle Tipps oder Schnellstartfenster, die bei der ersten Anmeldung geöffnet werden, und verwenden Sie gegebenenfalls das Logo **Azure AI Foundry** oben links, um zur Startseite zu navigieren, die ähnlich wie die folgende Abbildung aussieht (schließen Sie das **Hilfe**-Fenster, falls es geöffnet ist):

    ![Screenshot des Azure KI Foundry-Portals.](./media/ai-foundry-home.png)

1. Prüfen Sie die Informationen auf der Startseite.
1. Suchen Sie auf der Startseite im Abschnitt **Modelle und Funktionen erkunden** nach dem Modell `gpt-4o`, das wir in unserem Projekt verwenden werden.
1. Wählen Sie in den Suchergebnissen das **GPT-4o-Modell** aus, um dessen Details anzuzeigen.
1. Lesen Sie die Beschreibung und überprüfen Sie die weiteren Informationen auf der Registerkarte **Details**.

    ![Screenshot der Detailseite des gpt-4o-Modells.](./media/gpt4-details.png)

1. Zeigen Sie auf der Seite **gpt-4o** die Registerkarte **Benchmarks** an, um zu sehen, wie das Modell im Vergleich zu einigen Standardleistungs-Benchmarks bei anderen Modellen abschneidet, die in ähnlichen Szenarien verwendet werden.

    ![Screenshot der Seite „gpt-4o-Modell-Benchmarks“.](./media/gpt4-benchmarks.png)

1. Verwenden Sie den Zurück-Pfeil (**&larr;**) neben dem **GPT-4o**-Seitentitel, um zum Modellkatalog zurückzukehren.
1. Suchen Sie nach `Phi-4-mini-instruct`, und zeigen Sie die Details und Benchmarks für das Modell **Phi-4-mini-instruct** an.

## Vergleichen von Modellen

Sie haben zwei verschiedene Modelle überprüft, von denen beide verwendet werden können, um eine generative KI-Chatanwendung zu implementieren. Jetzt vergleichen wir die Metriken für diese beiden Modelle visuell.

1. Verwenden Sie den Zurück-Pfeil (**&larr;**), um zum Modellkatalog zurückzukehren.
1. Wählen Sie **Modelle vergleichen** aus. Ein visuelles Diagramm für den Modellvergleich wird mit einer Auswahl gängiger Modelle angezeigt.

    ![Screenshot der Modellvergleichsseite.](./media/compare-models.png)

1. Beachten Sie im Bereich **Zu vergleichende Modelle**, dass Sie beliebte Aufgaben wie *Fragen und Antworten* auswählen können, um automatisch häufig verwendete Modelle für bestimmte Aufgaben auszuwählen.
1. Verwenden Sie das Symbol **Alle Modelle löschen** (&#128465;), um alle vordefinierten Modelle zu entfernen.
1. Verwenden Sie die Schaltfläche **+Modell zum Vergleichen**, um der Liste das Modell **gpt-4o** hinzuzufügen. Verwenden Sie dann dieselbe Schaltfläche, um der Liste das Modell **Phi-4-mini-instruct** hinzuzufügen.
1. Überprüfen Sie das Diagramm, das die Modelle basierend auf dem **Qualitätsindex** (eine standardisierte Bewertung, die die Modellqualität angibt) und den **Kosten** vergleicht. Sie können die spezifischen Werte für ein Modell anzeigen, indem Sie den Mauszeiger über den entsprechenden Punkt im Diagramm bewegen.

    ![Screenshot: Modellvergleichsdiagramm für gpt-4o und Phi-4-mini-instruct](./media/comparison-chart.png)

1. Wählen Sie im Dropdown-Menü **X-Achse** unter **Qualität** die folgenden Metriken aus und betrachten Sie jedes resultierende Diagramm, bevor Sie zum nächsten wechseln:
    - Genauigkeit
    - Qualitätsindex

    Basierend auf den Benchmarks sieht das GPT-4o-Modell wie das beste Gesamtleistungsangebot aus, aber zu höheren Kosten.

1. Wählen Sie in der Liste der zu vergleichenden Modelle das **GPT-4o-Modell** aus, um die Benchmarks-Seite erneut zu öffnen.
1. Wählen Sie auf der Seite für das Modell **GPT-4o** die Registerkarte **Übersicht**, um die Modelldetails anzuzeigen.

## Erstellen eines Azure KI Foundry-Projekts

Um ein Modell zu verwenden, müssen Sie ein Azure AI Foundry-*Projekt* erstellen.

1. Wählen Sie oben auf der Übersichtsseite des Modells **GPT-4o** die Option **Dieses Modell verwenden** aus.
1. Wenn Sie zum Erstellen eines Projekts aufgefordert werden, geben Sie einen gültigen Namen für Ihr Projekt ein und erweitern Sie **Erweiterte Optionen**.
1. Legen Sie im Abschnitt **Erweiterte Optionen** die folgenden Einstellungen für Ihr Projekt fest:
    - **Azure KI Foundry-Ressource**: *Ein gültiger Name für Ihre Azure KI Foundry-Ressource*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe**: *Erstellen Sie eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus*.
    - **Region**: Wählen Sie die **empfohlene AI Foundry-Instanz aus***\*

    > \* Einige Azure KI-Ressourcen unterliegen regionalen Modellkontingenten. Sollte im weiteren Verlauf der Übung eine Kontingentgrenze überschritten werden, müssen Sie möglicherweise eine weitere Ressource in einer anderen Region anlegen.

1. Klicken Sie auf **Erstellen**, und warten Sie, bis das Projekt erstellt wird. Stellen Sie bei entsprechender Aufforderung das gpt-4o-Modell mithilfe des Bereitstellungstyps **Globaler Standard** bereit, und passen Sie die Bereitstellungsdetails an, um für **Ratenbegrenzung für Token pro Minute** den Wert „50.000“ (oder den maximal verfügbaren Wert, wenn er weniger als 50.000 beträgt) festzulegen.

    > **Hinweis:** Durch das Verringern des TPM wird die Überlastung des Kontingents vermieden, das in dem von Ihnen verwendeten Abonnement verfügbar ist. 50.000 TPM sollten für die in dieser Übung verwendeten Daten ausreichend sein. Wenn Ihr verfügbares Kontingent darunter liegt, können Sie die Übung zwar durchführen, aber es können Fehler auftreten, wenn das Kontingent überschritten wird.

1. Wenn Ihr Projekt erstellt wird, wird der Chat-Playground automatisch geöffnet, damit Sie Ihr Modell testen können:

    ![Screenshot des Chat-Playgrounds im Azure AI Foundry-Projekt.](./media/ai-foundry-chat-playground.png)

## Chatten mit dem *gpt-4o*-Modell

Nachdem Sie nun über eine Modellbereitstellung verfügen, können Sie den Playground verwenden, um es zu testen.

1. Stellen Sie im Chat-Playground im Bereich **Setup** sicher, dass Ihr **GPT-4o**-Modell ausgewählt ist, und legen Sie im Feld **Geben Sie dem Modell Anweisungen und Kontext** den System-Prompt auf `You are an AI assistant that helps solve problems.` fest.
1. Wählen Sie **Änderungen übernehmen**, um den System-Prompt zu aktualisieren.

1. Geben Sie im Chatfenster die folgende Abfrage ein:

    ```
   I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Zeigen Sie die Antwort an. Geben Sie dann die folgende Nachverfolgungsabfrage ein:

    ```
   Explain your reasoning.
    ```

## Bereitstellen eines weiteren Modells

Beim Erstellen des Projekts wurde das ausgewählte **GPT-4o-Modell** automatisch bereitgestellt. Stellen wir nun das von Ihnen ebenfalls in Betracht gezogene Modell ***Phi-4-mini-instruct** bereit.

1. Wählen Sie in der Navigationsleiste auf der linken Seite im Bereich **Meine Assets** die Option **Modelle + Endpunkte** aus.
1. Wählen Sie auf der Registerkarte **Modellbereitstellungen** in der Dropdownliste **+ Modell bereitstellen** die Option **Basismodell bereitstellen** aus. Suchen Sie dann nach `Phi-4-mini-instruct` und bestätigen Sie Ihre Auswahl.
1. Stimmen Sie der Modelllizenz zu.
1. Stellen Sie ein **Phi-4-mini-instruct**-Modell mit den folgenden Einstellungen bereit:
    - **Bereitstellungsname:***Ein gültiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Globaler Standard
    - **Bereitstellungsdetails**: *Verwenden der Standardeinstellungen.*

1. Warten Sie, bis die Bereitstellung abgeschlossen ist.

## Chatten mit dem *Phi-4*-Modell

Jetzt chatten wir mit dem neuen Modell auf dem Playground.

1. Wählen Sie in der Navigationsleiste **Playgrounds** aus. Wählen Sie dann den **Chatplayground** aus.
1. Vergewissern Sie sich im Chat-Playground im Bereich **Setup**, dass das Modell **Phi-4-mini-instruct** ausgewählt ist. Geben Sie im Chatfeld als erste Zeile `System message: You are an AI assistant that helps solve problems.` ein (derselbe Systemprompt, den Sie zum Testen des gpt-4o-Modells verwendet haben, aber da keine Systemmeldung eingerichtet ist, geben Sie dies als Kontext im ersten Chat an).
1. Geben Sie in einer neuen Zeile im Chatfenster (unter Ihrer Systemmeldung) die folgende Abfrage ein:

    ```
   I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Zeigen Sie die Antwort an. Geben Sie dann die folgende Nachverfolgungsabfrage ein:

    ```
   Explain your reasoning.
    ```

## Durchführen eines weiteren Vergleichs

1. Verwenden Sie die Dropdownliste im **Setupbereich**, um zwischen Ihren Modellen zu wechseln, indem Sie beide Modelle mit dem folgenden Puzzle testen (die richtige Antwort lautet 40!):

    ```
   I have 53 socks in my drawer: 21 identical blue, 15 identical black and 17 identical red. The lights are out, and it is completely dark. How many socks must I take out to make 100 percent certain I have at least one pair of black socks?
    ```

## Reflektieren der Modelle

Sie haben zwei Modelle verglichen, die sich sowohl in ihrer Fähigkeit, angemessene Reaktionen zu generieren, als auch in ihren Kosten unterscheiden können. In jedem generativen Szenario müssen Sie ein Modell finden, das die richtige Balance zwischen der Eignung für die Aufgabe, die Sie ihm übertragen möchten, und den Kosten für die Nutzung des Modells für die Anzahl der Anfragen, die es voraussichtlich bearbeiten muss, bietet.

Die im Modellkatalog enthaltenen Details und Benchmarks sowie die Möglichkeit, Modelle visuell zu vergleichen, bieten einen nützlichen Ausgangspunkt für die Identifizierung von Kandidatenmodellen für eine generative KI-Lösung. Anschließend können Sie Kandidatenmodelle mit einer Vielzahl von System- und Benutzerprompts im Chat-Playground testen.

## Bereinigen

Wenn Sie die Erkundung des Azure KI-Foundry-Portals abgeschlossen haben, sollten Sie die Ressourcen, die Sie in dieser Übung erstellt haben, löschen, um unnötige Azure-Kosten zu vermeiden.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und zeigen Sie den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
