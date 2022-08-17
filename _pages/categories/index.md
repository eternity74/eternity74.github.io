---
layout: archive
permalink: categories
author_profile: true
sidebar_main: true
---
{% for category in site.categories %}
<div id={{category[0]}} style="display:none;" >
<h1> {{ category[0] }} </h1>
{% assign posts = category[1] %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}
</div>
{% endfor %}
<script>
var show_category = window.location.search.substr(1);
if (show_category == "") {
{% for category in site.categories %}
    document.getElementById("{{category[0]}}").style.display = "block";
{% endfor %}
} else {
    document.getElementById(show_category).style.display = "block";
}
</script>
