---
layout: post
title:  "Sécurisez des informations de votre base de données dans vos projets Rails 6 avec attr_encrypted"
date:   2019-04-03 13:37:00 -0500
categories: rails heroku database
---

Si vous devez stocker de l'information sensible, il peut
être parfois utile de ne pas la garder en clair dans votre base de données. Si celle
ci est compromise, cela peut ralentir ou limiter les dégâts.

Dans votre projet Rails, vous pouvez utiliser la gem [attr_encrypted](https://github.com/attr-encrypted/attr_encrypted) qui vous
permettra de chiffrer des données en base lors d'un enregistrement.

## Installer la gem attr_encrypted

Dans votre gemfile et l'habituel **bundle install** pour l'installer:

`Gemfile`
{% highlight ruby %}
gem "attr_encrypted"
{% endhighlight %}

## Préparer la configuration

Afin de rendre le chiffrement/déchiffrement opérationnel, vous devez définir une
clé de chiffrement. Plusieurs options sont disponibles ici. Dans notre cas, nous allons
utiliser **une clé** et **un grain de sel (salt)**

Pour ce faire, nous devons définir deux valeurs dans le système de [gestion de secret de rails](https://edgeguides.rubyonrails.org/security.html#custom-credentials). Pour cela, utiliser la commande suivante afin d'ouvrir un éditeur et y ajouter les secrets :
{% highlight ruby %}
EDITOR="subl3 --wait" rails credentials:edit
{% endhighlight %}


`config/credentials.yml.enc`
{% highlight ruby %}
# aws:
#   access_key_id: 123
#   secret_access_key: 345

# Used as the base secret for all MessageVerifiers in Rails, including the one protecting cookies.
secret_key_base: 14626f689452080d8c7d35b68d01392af9affa758f61705f4b762ad36a2bljqsd24905831a42209f1a0105
attr_encrypted_key: aFG179ef8uiUptgufhbCjOYViSKRp72u
attr_encrypted_salt: 5p4DZSDqPgFx3khFuqPONK8sOzeeZs4p
{% endhighlight %}

Votre fichier de secret devra contenir les éléments suivant :  **attr_encrypted_key** et **attr_encrypted_salt**.
Pour générer des valeurs, vous pouvez utiliser le site [randomkeygen.com](https://randomkeygen.com/)

## Mettre en place le chiffrement dans votre table

Pour générer une table avec l'un des champs chiffré, ajoutez votre champ puis le même suivi de **_iv**, comme suit :

{% highlight ruby %}
rails generate model Document name:string secret_text:text secret_text_iv:string
{% endhighlight %}

Votre migration devrait ressembler à cela :

`db/migrate/20190222185500_create_documents.rb`
{% highlight ruby %}
class CreateDocuments < ActiveRecord::Migration[6.0]
  def change
    create_table :documents do |t|
      t.string :name
      t.text :secret_text
      t.text :secret__text_iv
      t.timestamps
    end
  end
end
{% endhighlight %}

Après avoir exécuté la migration avec **rails db:migrate**, il faut spécifier au
model que l'on veut utiliser le chiffrement sur les champs.

`app/models/document.rb`
{% highlight ruby %}
class Document < ApplicationRecord

    validates :name, presence: true
    validates :secret_url, presence: true

    # --------------------------------------------------------------------------
    # Encryption
    # --------------------------------------------------------------------------

    super_key = Rails.application.credentials.attr_encrypted_key
    super_salt = Rails.application.credentials.attr_encrypted_salt

    attr_encrypted :secret_text, key: super_key, salt: super_salt

end
{% endhighlight %}

Par la suite, vous pouvez utiliser normalement vos champs dans votre application.
Le chiffrement et le déchiffrement se fera automatique lors des interactions avec
la base de données.

---
### *Liens et documentations*

- [gem attr_encrypted](https://github.com/attr-encrypted/attr_encrypted) pour la documentation
- [randomkeygen.com](https://randomkeygen.com/) pour générer des clés
- [Rails credentials](https://edgeguides.rubyonrails.org/security.html#custom-credentials) pour gérer les secrets dans Rails 6+
