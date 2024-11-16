---
lab:
  title: 'Erforschen, Bereitstellen und Chatten mit Sprachmodellen im Azure AI Studio'
---

# Erforschen, Bereitstellen und Chatten mit Sprachmodellen im Azure AI Studio

Der Modellkatalog von Azure KI Studio dient als zentrales Repository, in dem Sie eine Vielzahl von Modellen erkunden und verwenden können. Dadurch wird die Erstellung eines generativen KI-Szenarios erleichtert.

In dieser Übung erkunden Sie den Modellkatalog in Azure KI Studio.

Diese Übung dauert ungefähr **25** Minuten.

## Erstellen eines Azure KI-Hubs

Sie benötigen einen Azure KI-Hub in Ihrem Azure-Abonnement, um Projekte zu hosten. Sie können diese Ressource entweder beim Erstellen eines Projekts erstellen oder vorab bereitstellen (wie in dieser Übung).

1. Wählen Sie im Abschnitt **Verwaltung** die Option **Alle Ressourcen** und dann **+ Neuer Hub** aus. Erstellen Sie eine neue Regel mit den folgenden Einstellungen:
    - **Hub-Name:** *Ein eindeutiger Name*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe:** *Neue Ressourcengruppe*
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** aus, wählen Sie dann **gpt-35-turbo** im Fenster der Standorthilfe aus und verwenden Sie die empfohlene Region\*
    - **Verbinden Sie Azure AI Dienst oder Azure OpenAI**: (Neu) *Automatisches Ausfüllen Ihres ausgewählten Hub-Namens*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die in der Standorthilfe aufgelisteten Regionen enthalten Standardquoten für den/die in dieser Übung verwendeten Modelltyp(en). Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze erreicht. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen. Erfahren Sie mehr über die [Modellverfügbarkeit pro Region](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

    Nachdem der Azure KI-Hub erstellt wurde, sollte er ähnlich wie in der folgenden Abbildung aussehen:

    ![Screenshot: Details eines Azure KI-Hubs in Azure KI Studio.](./media/azure-ai-resource.png)

1. Öffnen Sie eine neue Browserregisterkarte (lassen Sie die Registerkarte mit Azure KI Studio geöffnet), und navigieren Sie unter [https://portal.azure.com](https://portal.azure.com?azure-portal=true) zum Azure-Portal. Melden Sie sich dort mit Ihren Azure-Anmeldeinformationen an, wenn Sie dazu aufgefordert werden.
1. Navigieren Sie zu der Ressourcengruppe, in der Sie Ihren Azure KI-Hub erstellt haben, und zeigen Sie die erstellten Azure-Ressourcen an.

    ![Screenshot: Azure KI-Hub und zugehörige Ressourcen im Azure-Portal.](./media/azure-portal.png)

1. Wechseln Sie zurück zur Browserregisterkarte von Azure KI Studio.
1. Zeigen Sie die einzelnen Seiten im Bereich links auf der Seite für Ihren Azure KI-Hub an, und sehen Sie sich die Artefakte an, die Sie erstellen und verwalten können. Beachten Sie auf der Seite **Verbindungen**, dass bereits Verbindungen zu Azure OpenAI- und KI-Diensten erstellt wurden.

## Erstellen eines Projekts

Ein Azure KI-Hub bietet einen Arbeitsbereich für die Zusammenarbeit, in dem Sie ein oder mehrere *Projekte* definieren können. Erstellen Sie nun ein Projekt in Ihrem Azure KI-Hub.

1. Stellen Sie in Azure KI Studio sicher, dass Sie sich im soeben erstellten Hub befinden (Sie sehen im Pfad oben auf dem Bildschirm, wo Sie sich befinden).
1. Navigieren Sie über das Menü links zu **Alle Projekte**.
1. Wählen Sie **+ New project** aus.
1. Erstellen Sie im **Assistenten zum Erstellen eines neuen Projekts** ein Projekt mit den folgenden Einstellungen:
    - **Aktueller Hub**: *Ihr KI-Hub*
    - **Projektname:** *Ein eindeutiger Name für Ihr Projekt*
1. Warten Sie, bis Ihr Projekt erstellt wurde. Wenn es fertig ist, sollte es ähnlich wie in der folgenden Abbildung aussehen:

    ![Screenshot: Projektdetailseite in Azure KI Studio](./media/azure-ai-project.png)

1. Zeigen Sie die Seiten im linken Bereich an, erweitern Sie jeden Abschnitt, und sehen Sie sich die Aufgaben an, die Sie ausführen können, und die Ressourcen, die Sie in einem Projekt verwalten können.

## Auswählen eines Modells mithilfe von Modell-Benchmarks

Bevor Sie ein Modell bereitstellen, können Sie die Modell-Benchmarks untersuchen, um zu entscheiden, welches Modell ihren Anforderungen am besten entspricht.

Stellen Sie sich vor, Sie möchten einen benutzerdefinierten Copilot erstellen, der als Reiseassistent dient. Insbesondere möchten Sie, dass Ihr Copilot Unterstützung für reisebezogene Anfragen wie Visaanforderungen, Wettervorhersagen, lokale Attraktionen und kulturelle Normen bietet.

Ihr Kopilot muss sachlich korrekte Informationen liefern, daher ist Bodenständigkeit wichtig. Darüber hinaus möchten Sie, dass die Antworten des Copilot leicht zu lesen und zu verstehen sind. Daher sollten Sie auch ein Modell wählen, das sich durch einen hohen Grad an Geläufigkeit und Kohärenz auszeichnet.

1. Navigieren Sie im Azure AI Studio über das Menü auf der linken Seite zu **Modell-Benchmarks** unter dem Abschnitt **Get started**.
    Auf der Registerkarte **Qualitäts-Benchmarks** finden Sie einige bereits visualisierte Diagramme, die verschiedene Modelle vergleichen.
1. Filtern Sie die angezeigten Modelle:
    - **Aufgaben**: Beantworten von Fragen
    - **Sammlungen** Azure OpenAI
    - **Metriken**: Kohärenz, Geläufigkeit, Bodenständigkeit
1. Erkunden Sie die resultierenden Diagramme und die Vergleichstabelle. Beim Erkunden können Sie die folgenden Fragen ausprobieren und beantworten:
    - Beobachten Sie einen Unterschied bei der Leistung zwischen GPT-3.5- und GPT-4-Modellen?
    - Gibt es einen Unterschied zwischen Versionen desselben Modells?
    - Wie unterscheiden sich die 32k Varianten von den Basismodellen?

In der Azure OpenAI-Sammlung können Sie zwischen GPT-3.5- und GPT-4-Modellen wählen. Lassen Sie uns diese beiden Modelle einsetzen und untersuchen, wie sie sich für Ihren Anwendungsfall eignen.

## Bereitstellen von Azure OpenAI-Modellen

Nachdem Sie Ihre Optionen nun mithilfe von Modell-Benchmarks untersucht haben, können Sie Sprachmodelle bereitstellen. Sie können den Modellkatalog durchsuchen und von dort aus bereitstellen oder ein Modell über die Seite**Bereitstellungen** zur Verfügung stellen. Lassen Sie uns beide Optionen erkunden.

### Bereitstellen eines Modells aus dem Modellkatalog

Beginnen wir mit der Bereitstellung eines Modells aus dem Modellkatalog. Sie können diese Option bevorzugen, wenn Sie alle verfügbaren Modelle filtern möchten.

1. Navigieren Sie zur Seite **Modellkatalog** unter dem Abschnitt **Einstieg**, indem Sie das Menü auf der linken Seite verwenden.
1. Suchen Sie nach dem Modell `gpt-35-turbo`, das von Azure AI erstellt wurde, und stellen Sie es mit den folgenden Einstellungen bereit:
    - **Bereitstellungsname:** *Ein eindeutiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Standard
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **KI-Ressource**: *Wählen Sie die zuvor erstellte Quelle* aus
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: StandardV2 
    - **Dynamische Quote aktivieren**: Deaktiviert

### Bereitstellen eines Modells über Bereitstellungen

Wenn Sie bereits genau wissen, welches Modell Sie bereitstellen möchten, sollten Sie es über Bereitstellungen tun.

1. Navigieren Sie zur Seite **Einrichtungen** unter dem Abschnitt **Komponenten**, indem Sie das Menü auf der linken Seite verwenden.
1. Erstellen Sie auf der Registerkarte **Modellbereitstellungen** eine neue Bereitstellung mit den folgenden Einstellungen:
    - **Modell**: gpt-4
    - **Bereitstellungsname:** *Ein eindeutiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Standard
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **KI-Ressource**: *Wählen Sie die zuvor erstellte Quelle* aus
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: StandardV2 
    - **Dynamische Quote aktivieren**: Deaktiviert

    > **Hinweis**: Vielleicht haben Sie bemerkt, dass einige Modelle die Modell-Benchmarks anzeigen, aber nicht als Option in Ihrem Modellkatalog. Die Modellverfügbarkeit unterscheidet sich je nach Standort. Ihr Standort wird auf der KI-Hub-Ebene angegeben, wo Sie den **Standorthilfsprogramm** verwenden können, um das Modell anzugeben, das Sie einsetzen wollen, um eine Liste von Standorten zu erhalten, an denen Sie es einsetzen können.

## Testen von Modellen im Chat-Playground

Nachdem wir nun zwei Modelle zum Vergleichen haben, sehen wir uns an, wie sich die Modelle in einer Unterhaltungsinteraktion verhalten.

1. Navigieren Sie zur Seite **Chat** unter dem Abschnitt **Projektspielplatz**, indem Sie das Menü auf der linken Seite verwenden.
1. Wählen Sie im **Chat-Playground** Ihre GPT-3.5-Bereitstellung aus.
1. Geben Sie im Chat-Fenster die Abfrage `What can you do?` ein und sehen Sie sich die Antwort an.
    Die Antworten sind sehr allgemein gehalten. Denken Sie daran, dass wir einen benutzerdefinierten Kopiloten erstellen wollen, der als Reiseassistent dient. In der Frage, die Sie stellen, können Sie angeben, welche Art von Hilfe Sie benötigen.
1. Geben Sie im Chatfenster die Abfrage `Imagine you're a travel assistant, what can you help me with?` ein. Die Antworten sind bereits spezifischer. Möglicherweise möchten Sie nicht, dass Ihre Endbenutzer bei jeder Interaktion mit Ihrem Copilot den erforderlichen Kontext bereitstellen müssen. Um globale Anweisungen hinzuzufügen, können Sie die Systemmeldung bearbeiten.
1. Aktualisieren Sie die Systemnachricht mit der folgenden Eingabeaufforderung:

   ```
   You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
   ```

1. Klicken Sie auf **Speichern**, und **Chat löschen**.
1. Geben Sie im Chatfenster die Abfrage `What can you do?` ein, und schauen Sie sich die neue Antwort an. Beobachten Sie, wie sich dies von der Antwort unterscheidet, die Sie zuvor erhalten haben. Die Antwort ist ausreichend spezifisch, um jetzt zu reisen.
1. Setzen Sie das Gespräch fort, indem Sie fragen: `I'm planning a trip to London, what can I do there?` Der Copilot bietet viele reisebezogene Informationen. Möglicherweise möchten Sie die Ausgabe noch weiter verbessern. Vielleicht möchten Sie zum Beispiel, dass die Antwort prägnanter ausfällt.
1. Aktualisieren Sie die Systemnachricht, indem Sie am Ende der Nachricht `Answer with a maximum of two sentences.`hinzufügen. Wenden Sie die Änderung an, löschen Sie den Chat und testen Sie den Chat erneut, indem Sie eine Frage stellen: `I'm planning a trip to London, what can I do there?` Vielleicht möchten Sie auch, dass Ihr Copilot das Gespräch fortsetzt, anstatt nur die Frage zu beantworten.
1. Aktualisieren Sie die Systemnachricht, indem Sie `End your answer with a follow-up question.`am Ende der Nachricht hinzufügen . Speichern Sie die Änderung, löschen Sie den Chat, und testen Sie den Chat erneut, indem Sie fragen: `I'm planning a trip to London, what can I do there?`
1. Ändern Sie Ihre **Einrichtung** auf Ihr GPT-4-Modell und wiederholen Sie alle Schritte in diesem Abschnitt. Beachten Sie, wie die Modelle in ihren Ausgaben variieren können.
1. Testen Sie schließlich beide Modelle für die Abfrage `Who is the prime minister of the UK?`. Die Leistung bei dieser Frage hängt mit der Fundiertheit der Modelle zusammen (d. h. ob die Antworten sachlich richtig sind). Korreliert die Leistung mit Ihren Schlussfolgerungen aus den Modell-Benchmarks?

Nachdem Sie nun beide Modelle untersucht haben, überlegen Sie, welches Modell Sie jetzt für Ihren Anwendungsfall auswählen würden. Zu Beginn können die Ergebnisse der Modelle unterschiedlich sein, und Sie werden vielleicht ein Modell dem anderen vorziehen. Nach der Aktualisierung der Systemmeldung werden Sie jedoch feststellen, dass der Unterschied minimal ist. Unter dem Gesichtspunkt der Kostenoptimierung können Sie sich dann für das Modell GPT-3.5 und nicht für das Modell GPT-4 entscheiden, da ihre Leistung sehr ähnlich ist.

## Bereinigung

Wenn Sie mit der Erkundung von Azure KI Studio fertig sind, sollten Sie die in dieser Übung erstellten Ressourcen löschen, um unnötige Azure-Kosten zu vermeiden.

1. Kehren Sie zur Browserregisterkarte mit dem Azure-Portal zurück (oder öffnen Sie das [Azure-Portal](https://portal.azure.com?azure-portal=true) auf einer neuen Browserregisterkarte erneut), und zeigen Sie den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
