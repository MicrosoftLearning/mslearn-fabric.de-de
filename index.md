---
title: Online gehostete Anweisungen
permalink: index.html
layout: home
---

# Microsoft Fabric-Übungen

Die folgenden Übungen sind eine Ergänzung für die Module in [Microsoft Learn](https://aka.ms/learn-fabric).

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| Modul | Lab |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} – {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

