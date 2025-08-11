<ul>
  {% for business in site.businesses %}
    <li><a href="{{ business.url }}">{{ business.title }}</a> - Category: {{ business.category }}</li>
  {% endfor %}
</ul>
