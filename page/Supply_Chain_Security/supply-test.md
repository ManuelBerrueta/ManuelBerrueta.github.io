---
layout: page

---

# Supple Collections Test

{% for spc_page in site.supply_chain_security %}
  <h2>
    <a href="<{{ spc_page.url }}>"> {{ spc_page.title }} </a>
  </h2>
  <p>{{ spc_page.content | markdownify }}</p>
{% endfor %}