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
            <a href="https://social.ssbx.dev/@archit3ct" class="button">
              <small>Follow me on</small>Mastodon
            </a>
{% for page in site.page_list %}
   <p class="repo-owner"> <a href="{{ page[1] }}" class="previous">
      {{ page[0] }}
    </a></p>
{% endfor %}
<p class="repo-owner">Posts:</p>
{% for post in site.posts limit:20 %}
    <p style="margin-bottom: 0px"><a href="{{ post.url }}" class="previous">
      {{ post.date | date_to_string }} - {{ post.title }}
    </a></p>
{% endfor %}


        </aside>
