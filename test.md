---
#layout: page
title: "PAGE-TITLE"
permalink: /testa
published: true
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


<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a> {{ post.summary }}
    </li>
  {% endfor %}
</ul>