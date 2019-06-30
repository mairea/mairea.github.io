---
layout: post
title:  "Mettre en place une balise Google Analytics avec Rails 6+ et Turbolinks"
date:   2019-05-30 13:37:00 -0500
categories: rails turbolinks analytics
---

La plupart des sites web embarquent une balise de suivi pour mesurer le trafic.
Avec Rails et Turbolinks, le code de suivi n'est pas systématiquement rechargé et
cela fausse les statistiques et surtout le suivi temps réel.

Que ce soit Piwik, Google Analytics ou autre, voici comment faire que le code soit
exécuté au chargement de la page en tout temps.

## Balise à placer dans votre page

`app/view/layouts/application.html.erb`
{% highlight ruby %}

<head>  
    <% if request.host == 'votredomaine.com' %>

        <!-- Global site tag (gtag.js) - Google Analytics -->
        <script async src="https://www.googletagmanager.com/gtag/js?id=UA-XXXXXXXXX-X"></script>
        <script>
          window.dataLayer = window.dataLayer || [];
          function gtag(){dataLayer.push(arguments);}
          gtag('js', new Date());
          gtag('config', 'UA-XXXXXXXXX-X');

          document.addEventListener('turbolinks:load', event => {
           if (typeof gtag === 'function') {
             gtag('config', 'UA-XXXXXXXXX-X', {
               'page_location': event.data.url
             });
           }
         });
        </script>

    <% end %>
</head>
{% endhighlight %}

A noter que ce principe est valable lorsque vous souhaitez recharger un code javascript
dans votre site web ou application.

---

### *Liens et documentations*

- [Turbolinks](https://github.com/turbolinks/turbolinks) pour la documentation
