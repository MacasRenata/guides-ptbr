---
layout: default
title: Funcionalidades de uma área de comentários para a app Rails Girls
permalink: commenting
---
# Área de comentários para a app Rails Girls

*Criado por Janika Liiv, [@janikaliiv](https://twitter.com/janikaliiv)*

*Traduzido por Maujor, [site do Maujor](http://www.maujor.com)*

*Atualizado por Tayllan, [@mtayllan](https://github.com/mtayllan)*

Vamos aprender como criar uma área de comentários para nossa app *railsgirls* ideas.

As instruções para criar a app foram descritas [neste documento](/app).

## _1._ Criando o scaffold para a área de comentários

Crie o scaffold para a área de comentários fazendo nele constar o nome de quem comenta, o corpo do comentário (conteúdo do comentário) e uma referência para a tabela ideas (`idea_id`).
{% highlight sh %}
rails g scaffold comment user_name:string body:text idea_id:integer
{% endhighlight %}
Esta ação criará um arquivo de migração que permitirá ao banco de dados conhecer a nova tabela de comentários.  A seguir migre as alterações feitas no banco de dados digitando no terminal o seguinte:
{% highlight sh %}
rails db:migrate
{% endhighlight %}

## _2._ Adicione relacionamentos aos modelos

Você precisa assegurar que Rails seja capaz de identificar a conexão entre objetos (ideas e comentários). De vez que uma idea pode ter vários comentários precisamos assegurar que o modelo ideas sabe disso. Abra `app/models/idea.rb` e logo após a linha:
{% highlight ruby %}
class Idea < ApplicationRecord
{% endhighlight %}
acrescente o seguinte:
{% highlight ruby %}
has_many :comments
{% endhighlight %}

 Um comentário precisa saber que ele pertence a uma idea. Assim, abra `app/models/comment.rb` e logo após a linha:
{% highlight ruby %}
class Comment < ApplicationRecord
{% endhighlight %}

acrescente a seguinte linha:
{% highlight ruby %}
belongs_to :idea
{% endhighlight %}

## _3._ Mostrar o formulário de comentários e os comentários existentes

Abra `app/views/ideas/show.html.erb` é depois da tag de imagem (image_tag)
{% highlight erb %}
<%= image_tag(@idea.picture_url, :width => 600) if @idea.picture.present? %>
{% endhighlight %}

acrescente a seguinte linha:
{% highlight erb %}
<h3>Comentários</h3>
<% @comments.each do |comment| %>
  <div>
    <strong><%= comment.user_name %></strong>
    <br>
    <p><%= comment.body %></p>
    <p><%= link_to 'Delete', comment_path(comment), method: :delete, data: { confirm: 'Tem certeza?' } %></p>
  </div>
<% end %>
<%= render partial: 'comments/form', locals: { comment: @comment } %>
{% endhighlight %}

Em `app/controllers/ideas_controller.rb` acrescente a ação mostrar logo após a linha:
{% highlight ruby %}
@comments = @idea.comments.all
@comment = @idea.comments.build
{% endhighlight %}

Abra `app/views/comments/_form.html.erb` e logo após a linha:
{% highlight erb %}
  <div class="field">
    <%= form.label :body %><br>
    <%= form.text_area :body, id: :comment_body %>
  </div>
{% endhighlight %}

acrescente a seguinte linha:
{% highlight erb %}
<%= form.hidden_field :idea_id %>
{% endhighlight %}

a seguir remova
{% highlight erb %}
<div class="field">
  <%= form.label :idea_id %><br>
  <%= form.number_field :idea_id %>
</div>
{% endhighlight %}

Pronto! Visualize uma idea que você tenha acrescentado à sua aplicação e você verá ali o formulário para inserir um comentário bem como a funcionalidade para deletar comentários.
