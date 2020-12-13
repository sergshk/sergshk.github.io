        <aside id="sidebar">
          {% if site.show_downloads %}
            <a href="{{ site.github.zip_url }}" class="button">
              <small>Download</small>
              .zip file
            </a>
            <a href="{{ site.github.tar_url }}" class="button">
              <small>Download</small>
              .tar.gz file
            </a>
          {% endif %}

          {% if site.github.is_project_page %}
            <p class="repo-owner"><a href="{{ site.github.repository_url }}">{{ site.github.repository_name }}</a> is maintained by <a href="{{ site.github.owner_url }}">{{ site.github.owner_name }}</a>.</p>
          {% endif %}
## Pages
{% for page in site.page_list %}
{{ page }}
{% endfor %}
## Recent posts
{% for post in site.posts limit:4 %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
{% endfor %}

        </aside>