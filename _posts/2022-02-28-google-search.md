---
layout: post
title: 깃허브 블로그 구글 검색 결과에 노출시키기
subtitle: Google Webmaster
tags: [Github, Google]
---
기본적인 깃허브 블로그 세팅이 끝났고, 원래 쓰던 블로그에서 글도 몇 개 옮겨와서 포스트도 조금 채워놨으니 이제는 실제로 블로그를 노출시킬 차례가 왔다.

일단 [Google Search Console](https://search.google.com/search-console/about)에 들어가자

![](/assets/img/add-sitemap.png)|![](/assets/img/add-robots.png)

이렇게 블로그 파일의 root 위치, 즉 가장 바깥 폴더(_config.yml 있는 곳 맞음)에 sitemap.xml, robots.xml을 만들어준다.  
그리고 각각의 파일들에 아래의 내용을 써주면 됩니다.

## sitemap.xml  
``` html
---
layout: null
---

<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9 http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd"
        xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
    {% for post in site.posts %}
    <url>
        <loc>{{ site.url }}{{ post.url }}</loc>
        {% if post.lastmod == null %}
        <lastmod>{{ post.date | date_to_xmlschema }}</lastmod>
        {% else %}
        <lastmod>{{ post.lastmod | date_to_xmlschema }}</lastmod>
        {% endif %}

        {% if post.sitemap.changefreq == null %}
        <changefreq>weekly</changefreq>
        {% else %}
        <changefreq>{{ post.sitemap.changefreq }}</changefreq>
        {% endif %}

        {% if post.sitemap.priority == null %}
        <priority>0.5</priority>
        {% else %}
        <priority>{{ post.sitemap.priority }}</priority>
        {% endif %}

    </url>
    {% endfor %}
</urlset>
```


![](/assets/img/localhost-sitemap.xml.png)

![](assets/../../assets/img/google-search-console-domain-inpu.png)

![](/assets/img/google-search-console-auto-checked.png)

![](/assets/img/sitemap-success.png)



