---
lab:
  title: 'Erforschen, Bereitstellen und Chatten mit Sprachmodellen in Azure KI Foundry'
---

# Erforschen, Bereitstellen und Chatten mit Sprachmodellen in Azure KI Foundry

Der Modellkatalog von Azure KI Foundry dient als zentrales Repository, in dem Sie eine Vielzahl von Modellen erforschen und verwenden können, was die Erstellung Ihres generativen KI-Szenarios erleichtert.

In dieser Übung werden Sie den Modellkatalog im Azure KI Foundry-Portal erkunden.

Diese Übung dauert ungefähr **25** Minuten.

## Erstellen eines Azure KI-Hubs und eines Projekts

Ein Azure KI-Hub bietet einen Arbeitsbereich für die Zusammenarbeit, in dem Sie ein oder mehrere *Projekte* definieren können. Erstellen Sie uns ein Projekt und ein Azure KI-Hub.

1. Öffnen Sie in einem Webbrowser das [Azure KI Foundry Portal](https://ai.azure.com) unter `https://ai.azure.com` und melden Sie sich mit Ihren Azure-Anmeldedaten an.

1. Wählen Sie auf der Startseite **+ Projekt erstellen**. Im Assistenten **Projekt erstellen** sehen Sie alle Azure-Ressourcen, die automatisch mit Ihrem Projekt erstellt werden, oder Sie können die folgenden Einstellungen anpassen, indem Sie **Anpassen** wählen, bevor Sie **Erstellen** wählen:

    - **Hub-Name:** *Ein eindeutiger Name*
    - **Abonnement:** *Geben Sie Ihr Azure-Abonnement an.*
    - **Ressourcengruppe:** *Neue Ressourcengruppe*
    - **Standort**: Wählen Sie **Hilfe bei der Auswahl** aus, wählen Sie dann **gpt-35-turbo** im Fenster der Standorthilfe aus und verwenden Sie die empfohlene Region\*
    - **Verbinden Sie Azure AI Dienst oder Azure OpenAI**: (Neu) *Automatisches Ausfüllen Ihres ausgewählten Hub-Namens*
    - **Azure KI-Suche verbinden**: Verbindung überspringen

    > \* Azure OpenAI-Ressourcen werden auf Mandantenebene durch regionale Kontingente eingeschränkt. Die in der Standorthilfe aufgelisteten Regionen enthalten Standardquoten für den/die in dieser Übung verwendeten Modelltyp(en). Durch die zufällige Auswahl einer Region wird das Risiko reduziert, dass eine einzelne Region ihre Kontingentgrenze erreicht. Wenn später in der Übung ein Kontingentlimit erreicht wird, besteht eventuell die Möglichkeit, eine andere Ressource in einer anderen Region zu erstellen. Erfahren Sie mehr über die [Modellverfügbarkeit pro Region](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Wenn Sie **Anpassen** gewählt haben, wählen Sie **Weiter** und überprüfen Sie Ihre Konfiguration.
1. Klicken Sie auf **Erstellen** und warten Sie, bis der Vorgang abgeschlossen ist.
   
    Nachdem Sie den Azure KI-Hub und das Projekt erstellt haben, sollte es ähnlich wie das folgende Bild aussehen:

    ![Screenshot der Details eines Azure KI-Hubs im Azure KI Foundry-Portal.](./media/azure-ai-resource.png)

1. Öffnen Sie eine neue Browser-Registerkarte (lassen Sie die Registerkarte des Azure KI Foundry-Portals geöffnet) und rufen Sie das Azure-Portal unter [https://portal.azure.com](https://portal.azure.com?azure-portal=true) auf, indem Sie sich mit Ihren Azure-Zugangsdaten anmelden, falls Sie dazu aufgefordert werden.
1. Navigieren Sie zu der Ressourcengruppe, in der Sie Ihren Azure KI-Hub erstellt haben, und zeigen Sie die erstellten Azure-Ressourcen an.

    ![Screenshot: Azure KI-Hub und zugehörige Ressourcen im Azure-Portal.](./media/azure-portal.png)

1. Kehren Sie zur Browser-Registerkarte des Azure KI Foundry-Portals zurück.
1. Zeigen Sie die einzelnen Seiten im Bereich links auf der Seite für Ihren Azure KI-Hub an, und sehen Sie sich die Artefakte an, die Sie erstellen und verwalten können. Auf der Seite **Management Center** können Sie **Verbundene Ressourcen** auswählen, entweder unter Ihrem Hub oder Ihrem Projekt, und feststellen, dass bereits Verbindungen zu Azure OpenAI und KI-Services erstellt wurden.
1. Wenn Sie sich auf der Seite Management Center befinden, wählen Sie **Zum Projekt gehen**.

## Auswählen eines Modells mithilfe von Modell-Benchmarks

Bevor Sie ein Modell bereitstellen, können Sie die Modell-Benchmarks untersuchen, um zu entscheiden, welches Modell ihren Anforderungen am besten entspricht.

Stellen Sie sich vor, Sie möchten einen benutzerdefinierten Copilot erstellen, der als Reiseassistent dient. Insbesondere möchten Sie, dass Ihr Copilot Unterstützung für reisebezogene Anfragen wie Visaanforderungen, Wettervorhersagen, lokale Attraktionen und kulturelle Normen bietet.

Ihr Kopilot muss sachlich korrekte Informationen liefern, daher ist Bodenständigkeit wichtig. Darüber hinaus möchten Sie, dass die Antworten des Copilot leicht zu lesen und zu verstehen sind. Daher sollten Sie auch ein Modell wählen, das in Bezug auf Geläufigkeit und Kohärenz gut abschneidet.

1. Navigieren Sie im Azure KI Foundry-Projektportal über das Menü auf der linken Seite zu **Modellkatalog**.
    Wählen Sie auf der Katalogseite **Mit Benchmarks vergleichen** aus. Auf der Seite „Modell-Benchmarks“ finden Sie eine bereits für Sie erstellte Tabelle, in der verschiedene Modelle verglichen werden.
1. Wählen Sie **+ Modell aus, um zu vergleichen** und **gpt-4-32k** und **gpt-4** zum Metrikdiagramm hinzuzufügen. Wählen Sie im Dropdown-Menü **X-Achse** unter **Qualität** die folgenden Metriken aus und betrachten Sie jedes resultierende Diagramm, bevor Sie zum nächsten wechseln:
    - Kohärenz
    - Geläufigkeit
    - Quellenübereinstimmung
1. Beim Erkunden der Ergebnisse können Sie versuchen, die folgenden Fragen zu beantworten:
    - Beobachten Sie einen Unterschied bei der Leistung zwischen GPT-3.5- und GPT-4-Modellen?
    - Gibt es einen Unterschied zwischen Versionen desselben Modells?
    - Wie unterscheidet sich die 32k-Variante von GPT-4 vom Basismodell?

In der Azure OpenAI-Sammlung können Sie zwischen GPT-3.5- und GPT-4-Modellen wählen. Lassen Sie uns diese beiden Modelle einsetzen und untersuchen, wie sie sich für Ihren Anwendungsfall eignen.

## Bereitstellen von Azure OpenAI-Modellen

Nachdem Sie Ihre Optionen nun mithilfe von Modell-Benchmarks untersucht haben, können Sie Sprachmodelle bereitstellen. Sie können den Modellkatalog durchsuchen und von dort aus bereitstellen oder ein Modell über die Seite**Bereitstellungen** zur Verfügung stellen. Lassen Sie uns beide Optionen erkunden.

### Bereitstellen eines Modells aus dem Modellkatalog

Beginnen wir mit der Bereitstellung eines Modells aus dem Modellkatalog. Sie können diese Option bevorzugen, wenn Sie alle verfügbaren Modelle filtern möchten.

1. Navigieren Sie über das Menü auf der linken Seite zur Seite **Modellkatalog**.
1. Suchen Sie nach dem von Azure KI kuratierten `gpt-35-turbo`-Modell und stellen Sie es mit den folgenden Einstellungen bereit, indem Sie **Anpassen** in den Bereitstellungsdetails wählen:
   
    - **Bereitstellungsname:** *Ein eindeutiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Standard
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **KI-Ressource**: *Wählen Sie die zuvor erstellte Quelle* aus
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: StandardV2 
    - **Dynamische Quote aktivieren**: Deaktiviert

    > **Hinweis**: Wenn an Ihrem aktuellen Speicherort für KI-Ressourcen kein Kontingent für das Modell, das Sie bereitstellen möchten, verfügbar ist, werden Sie aufgefordert, einen anderen Speicherort zu wählen, an dem eine neue KI-Ressource erstellt und mit Ihrem Projekt verbunden wird.

### Bereitstellen eines Modells über Models + Endpunkte

Wenn Sie bereits genau wissen, welches Modell Sie bereitstellen möchten, ziehen Sie es vielleicht vor, dies über **Modelle + Endpunkte** zu tun.

1. Navigieren Sie zur Seite **Modelle + Endpunkte** unter dem Abschnitt **Meine Assets**, indem Sie das Menü auf der linken Seite verwenden.
1. Stellen Sie auf der Registerkarte **Modellbereitstellungen** ein neues Basismodell mit den folgenden Einstellungen bereit, indem Sie in den Bereitstellungsdetails **Anpassen** wählen:
    - **Modell**: gpt-4
    - **Bereitstellungsname:** *Ein eindeutiger Name für die Modellimplementierung*
    - **Bereitstellungstyp**: Standard
    - **Modellversion**: *Wählen Sie die Standardversion aus.*
    - **KI-Ressource**: *Wählen Sie die zuvor erstellte Quelle* aus
    - **Ratenbegrenzung für Token pro Minute (Tausender)**: 5.000
    - **Inhaltsfilter**: StandardV2 
    - **Dynamische Quote aktivieren**: Deaktiviert

## Testen von Modellen im Chat-Playground

Nachdem wir nun zwei Modelle zum Vergleichen haben, sehen wir uns an, wie sich die Modelle in einer Unterhaltungsinteraktion verhalten.

1. Navigieren Sie über das Menü auf der linken Seite zur Seite **Spielplätze**.
1. Wählen Sie im **Chat-Playground** Ihre GPT-3.5-Bereitstellung aus.
1. Geben Sie im Chat-Fenster die Abfrage `What can you do?` ein und sehen Sie sich die Antwort an.
    Die Antworten sind sehr allgemein gehalten. Denken Sie daran, dass wir einen benutzerdefinierten Kopiloten erstellen wollen, der als Reiseassistent dient. In der Frage, die Sie stellen, können Sie angeben, welche Art von Hilfe Sie benötigen.
1. Geben Sie im Chatfenster die Abfrage `Imagine you're a travel assistant, what can you help me with?` ein. Die Antworten sind bereits spezifischer. Möglicherweise möchten Sie nicht, dass Ihre Endbenutzer bei jeder Interaktion mit Ihrem Copilot den erforderlichen Kontext bereitstellen müssen. Um globale Anweisungen hinzuzufügen, können Sie die Systemmeldung bearbeiten.
1. Aktualisieren Sie unter **Setup** das Feld **Geben Sie die Modellanweisungen und den Kontext** mit der folgenden Aufforderung:

   ```
   You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
   ```

1. Wählen Sie **Änderungen übernehmen** aus.
1. Geben Sie im Chatfenster die Abfrage `What can you do?` ein, und schauen Sie sich die neue Antwort an. Beobachten Sie, wie sich dies von der Antwort unterscheidet, die Sie zuvor erhalten haben. Die Antwort ist ausreichend spezifisch, um jetzt zu reisen.
1. Setzen Sie das Gespräch fort, indem Sie fragen: `I'm planning a trip to London, what can I do there?` Der Copilot bietet viele reisebezogene Informationen. Möglicherweise möchten Sie die Ausgabe noch weiter verbessern. Vielleicht möchten Sie zum Beispiel, dass die Antwort prägnanter ausfällt.
1. Aktualisieren Sie die Systemnachricht, indem Sie am Ende der Nachricht `Answer with a maximum of two sentences.`hinzufügen. Wenden Sie die Änderung an, löschen Sie den Chat und testen Sie den Chat erneut, indem Sie eine Frage stellen: `I'm planning a trip to London, what can I do there?` Vielleicht möchten Sie auch, dass Ihr Copilot das Gespräch fortsetzt, anstatt nur die Frage zu beantworten.
1. Aktualisieren Sie den Kontext des Modells, indem Sie `End your answer with a follow-up question.` an das Ende der Eingabeaufforderung anhängen. Speichern Sie die Änderung und testen Sie den Chat erneut, indem Sie nachfragen: `I'm planning a trip to London, what can I do there?`
1. Ändern Sie Ihre **Einrichtung** auf Ihr GPT-4-Modell und wiederholen Sie alle Schritte in diesem Abschnitt. Beachten Sie, wie die Modelle in ihren Ausgaben variieren können.
1. Testen Sie schließlich beide Modelle für die Abfrage `Who is the prime minister of the UK?`. Die Leistung bei dieser Frage hängt mit der Fundiertheit der Modelle zusammen (d. h. ob die Antworten sachlich richtig sind). Korreliert die Leistung mit Ihren Schlussfolgerungen aus den Modell-Benchmarks?

Nachdem Sie nun beide Modelle untersucht haben, überlegen Sie, welches Modell Sie jetzt für Ihren Anwendungsfall auswählen würden. Zu Beginn können die Ergebnisse der Modelle unterschiedlich sein, und Sie werden vielleicht ein Modell dem anderen vorziehen. Nach der Aktualisierung der Systemmeldung werden Sie jedoch feststellen, dass der Unterschied minimal ist. Unter dem Gesichtspunkt der Kostenoptimierung können Sie sich dann für das Modell GPT-3.5 und nicht für das Modell GPT-4 entscheiden, da ihre Leistung sehr ähnlich ist.

## Bereinigen

Wenn Sie die Erkundung des Azure KI-Foundry-Portals abgeschlossen haben, sollten Sie die Ressourcen, die Sie in dieser Übung erstellt haben, löschen, um unnötige Azure-Kosten zu vermeiden.

1. Kehren Sie zur Browserregisterkarte mit dem Azure-Portal zurück (oder öffnen Sie das [Azure-Portal](https://portal.azure.com?azure-portal=true) auf einer neuen Browserregisterkarte erneut), und zeigen Sie den Inhalt der Ressourcengruppe an, in der Sie die in dieser Übung verwendeten Ressourcen bereitgestellt haben.
1. Wählen Sie auf der Symbolleiste die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Namen der Ressourcengruppe ein, und bestätigen Sie, dass Sie sie löschen möchten.
