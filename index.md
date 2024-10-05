---
title: Übungen zu Azure OpenAI
permalink: index.html
layout: home
---

# Auswertung von generativen KI-Anwendungen mit Azure AI Studio

Die folgenden Übungen sollen Ihnen eine praktische Lernerfahrung bieten, in der Sie gängige Muster und Techniken erkunden, die Entwickler verwenden, um generative KI-Anwendungen wie Chat-basierte „Copilots“ zu erstellen, und lernen, wie diese Muster mit Azure AI Services – insbesondere Azure OpenAI Service und Azure AI Studio – implementiert werden können.

Sie können diese Übungen zwar auch alleine durchführen, aber sie sind als Ergänzung zu den Modulen von [Microsoft Learn](https://learn.microsoft.com/training/paths/create-custom-copilots-ai-studio/) gedacht, in denen Sie einige der Konzepte, auf denen diese Übungen basieren, vertiefen können.

> **Hinweis**: Um die Übungen durchzuführen, benötigen Sie ein Azure-Abonnement, in dem Sie über ausreichende Berechtigungen und Kontingente verfügen, um die von Azure AI Studio verwendeten Azure-Ressourcen bereitzustellen und Azure OpenAI GPT-Modelle einzusetzen und zu verwenden. Wenn Sie noch kein Konto haben, können Sie sich für ein [Azure-Konto](https://azure.microsoft.com/free) anmelden. Für neue Benutzende gibt es dort eine kostenlose Testoption, die Guthaben für die ersten 30 Tage beinhaltet.

## Übungen

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}