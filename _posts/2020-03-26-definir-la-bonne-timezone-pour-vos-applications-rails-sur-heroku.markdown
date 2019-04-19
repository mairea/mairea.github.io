---
layout: post
title:  "Définir correctement la timezone pour vos applications Rails sur Heroku"
date:   2019-04-18 13:37:00 -0500
categories: timezone heroku
---

Récemment, j'ai constaté une différence entre les données en local et celle sur
mon application de test sur Heroku pour un module de gestion de temps.

L'heure de mon serveur local n'était pas la même que celle du serveur Heroku.
Ainsi, tout les enregistrements était décalé dans le temps (fuseau horaire).

Pour remédier à cela, il suffit de préciser à heroku la bonne timezone à utiliser
pour votre application.

{% highlight ruby %}
heroku config:add TZ="America/Toronto" --app votre_application
{% endhighlight %}

A noter qu'il n'est pas nécessaire de configurer votre application rails avec une
timezone car, c'est celle du serveur qui sera prise en compte.

---
### *Liens et documentations*
[Liste des timezone](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)
