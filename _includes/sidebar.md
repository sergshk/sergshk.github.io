		<div>
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
# Pages
{% for page in site.page_list %}
    <a href="{{ page[1] }}" class="previous">
      PP{{ page[0] }}
    </a>
{% endfor %}

## Posts 
{% for post in site.posts %}
    <a href="{{ post.url }}" class="previous">
      {{ post.title }}
    </a>
{% endfor %}


        </aside>
		</div>
