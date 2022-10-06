---
title: Online Hosted Instructions
permalink: index.html
layout: home
---

# Database Administration Exercises

These exercises support Microsoft course [AZ-720: Troubleshooting Microsoft Azure Connectivity](https://learn.microsoft.com/training/courses/az-720t00).

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/labs'" %}
| Module | Exercise |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

