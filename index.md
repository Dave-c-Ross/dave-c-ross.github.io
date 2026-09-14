---
layout: default
title: Home
published: true
# permalink: /testa
---

# This is a test

[This](/ref/article/openshift-acm-import_non_openshift_cluster.md)


[That](/ref/article/openshift-acm-import_non_openshift_cluster)



{% for repository in site.github.public_repositories %}
  * [{{ repository.name }}]({{ repository.html_url }}) => {{ repository.contributors }} [ZIP]({{ repository.zip_url }})
  ```
  {{ repository }}
  ```
{% endfor %}


![H]({{ site.github.owner_gravatar_url }})

# Recent Updates

<ul>
  {% for post in site.posts %}
    <li>
      <span>{{ post.date | date: "%b %d, %Y" }}</span> — 
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>