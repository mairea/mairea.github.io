---
layout: post
title:  "Sauvegarder et restaurer vos bases de données Postgresql en local et sur Heroku pour vos projets Rails"
date:   2019-03-25 13:37:00 -0500
categories: rails heroku database
---

Pour vos projet Rails, voici comment sauvegarder et restaurer vos bases de données Postgresql à la fois
en local avec Heroku

## *En local*

### **Faire une sauvegarde de la base de données Postgresql**

{% highlight ruby %}
pg_dump -Fc --no-acl --no-owner -h localhost -U utilisateur votre_database > fichier_sauvegarde.dump
{% endhighlight %}

### **Restaurer une la base de données Postgresql**
{% highlight ruby %}
pg_restore --verbose --clean --no-acl --no-owner -h localhost -U [utilisateur] -d  [base_de_donnees] fichier_sauvegarde.dump
{% endhighlight %}

---

## *Sur Heroku*


### **Définir les sauvegardes automatique de sa base de données**

{% highlight ruby %}
heroku pg:backups:schedule DATABASE_URL --at '21:00 Canada/Eastern' --app [nom_application]
{% endhighlight %}


### **Restaurer une sauvegarde de données depuis Heroku Backups**
Pour restaurer une base de données depuis les sauvegardes automatiques vous pouvez utiliser la commande suivante
{% highlight ruby %}
heroku pg:backups:restore [numero_du_backup] DATABASE_URL --app [nom_application]
{% endhighlight %}


### **Restaurer une base de données sur Heroku**
Si vous voulez importer une base de données (votre base de données locale par exemple), effectuer votre sauvegarde et
hébergez la en ligne quelque part, puis utiliser la commande suivante.

{% highlight ruby %}
heroku pg:backups:restore "http://lien/vers/votre/fichier_sauvegarde.dump" DATABASE_URL --app [nom_application]
{% endhighlight %}

---
### *Liens et documentations*
Plus d'informations sur  [les imports/exports sur Heroku](https://devcenter.heroku.com/articles/heroku-postgres-import-export)
et sur [les backups](https://devcenter.heroku.com/articles/heroku-postgres-backups)
