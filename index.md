<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}<small>{{ post.date | date: "%-d %B %Y" }}</small></a>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>
