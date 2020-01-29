---
title: Facet Panel
kind: documentation
description: "Slice and dice"
aliases:
    - /logs/search
further_reading:
- link: "logs/explorer/analytics"
  tag: "Documentation"
  text: "Perform Log Analytics"
- link: "logs/explorer/patterns"
  tag: "Documentation"
  text: "Detect patterns inside your logs"
- link: "logs/processing"
  tag: "Documentation"
  text: "Learn how to process your logs"
- link: "logs/explorer/saved_views"
  tag: "Documentation"
  text: "Automatically configure your Log Explorer"
---


## What are facets for?

Facets are the special fields (tags and attributes) of indexed logs you use for [search][1], [patterns][3] and [analytics][2] in the Log Explorer, in [log monitors][4], or log widgets in [dashboards][5] and [notebooks][6].


*Note: Facets support fast search and analytics capabilities around all **indexed** logs. You don't need facets to support [log processing][7], or [livetail search][8], [archive][9] forwarding and rehydration, or [metric generation][10] from logs. Neither for routing logs through to [Pipelines][11] and [Indexes][12] with filters, or excluding or sampling logs from indexes with [exclusion filters][13]. In all these context, autocomplete capabilities rely on existing facets but any input matching incoming logs would work.

Facets are meant for either qualitative and quantitative data. For quantitative data, Datadog specifically uses the word "Measure" instead of "Facet". For qualitative data, facets can be seen as "Dimensions". 


### Dimensions (qualitative facets)

Use dimensions when you need:

* to **filter** your logs against specific value(s). For instance, create a facet on an `environment` [tag][14] to scope troubleshooting down to development, staging or production environments.
* to **get relative insights** per values. For instance, create a facet on a `http.network.client.geoip.country.iso_code` to see what are the top countries most impacted per number of 5XX errors on your [NGINX][15] web acess logs enriched with our Datadog [GeoIP Processor][21].
* to **count unique values**. For instance create a facet on a `user.email` from your [Kong][16] logs, to know how many users connect every day to your website.

Dimensions can be of string or numerical (integer) type. While assigning string type to a dimension work in any case, using integer type on a dimension enables range filtering on top of all aforementioned capabilities. For instance, `http.status_code:[200 TO 299]` is a valid query to use on a integer-type dimension. See [search syntax][17] for reference.


### Measures (quantitative facets)

Use measures when you need:

* to **aggregate values** from multiple logs. For instance, create a measure on the size of tiles served by the [Varnish cache][18] of a map server and keep track of the **average** daily throughput, or top-most referrers per **sum** of tile size requested.
* to **range filter** your logs. For instance, create a measure on the execution time of [Ansible][19] taks, and see the list of servers having most runs taking more than 10s. 
* to **sort logs** against that value. For instance, create a measure on the amount of payments performed with your [Python][20] microservice. And search all the logs, starting with the one with highest amount. 


Measures come with either (long) integer or double value, for equivalent capabilities.

**Units**

Measures support units (Time or Size) for easier handling of orders of magnitude at query time and display time. Unit is a property of the measure itself, not of the field. 


Meaning in the example of the a `duration` measure in nanoseconds: if you have logs from `service:A` where `duration:1000` stands for 1000 milliseconds, and other logs from `service:B` where `duration:500` stands for 500 microseconds:

1. Scale duration into nanoseconds for all logs flowing in with the [arithmetic processor][22]. Use a `*1000000` multiplier on logs from `service:A`, and a `*1000` multiplier on logs from `service:B`. 
1. Use `duration:>20ms` (see [search syntax][17] for reference) to consistently query logs from both services at once, and see an aggregated result of max `1 min`.



## The Facet Panel


The search bar provides the most comprehensive set of interactions to slice and dice your data. However, for most cases, the facet panel is likely to be a more straightforward way to throught to your data. 


Open a facet to see a summary of its content for the scope of the current query. 

**Dimensions** come with a top list of unique values, and a count of logs matching each of them. Scope the search query clicking on either value. Clicking on a value toggles the search on this unique value and all values. Clicking on checkboxes adds or removes this specific value from the list of all values.

**Measures** come with a slider indicating minimum and maximum values. Use the slider, or input numerical values, to scope the search query to different bounds.


{{< img src="logs/explorer/facets_demo.png" alt="Facets demo"  style="width:80%;">}}


### Hide facets

Your organisation has a whole collection of facets to address its comprehensive set of use cases across all different teams using logs. But most likely, only a subset of these facets is valuable to you in a specific troubleshooting context. 


Hide facets you don't need, so that you can stay focused on the ones you need on a routine basis.  


Hidden facets are still visible in the facet search (see #filter-facets hereafter) in case you punctually need it. Unhide hidden facet from there.


**Hidden facets and teammates**

Hiding facets is specific to your own troubleshooting context and won't impact your teammates' view, unless you update a [Saved View][23] (hidden facets is part of the context saved in a saved view).


**Hidden facets in other contexts**

Hidden facets are also hidden from auto-complete in the search bar, and drop down in analytycs (measure, group-by) for the Log Explorer. However, hidden facets are still valid for search queries (in case you copy-paste a log-explorer link for instance).

Hidden facets have no impact aside from the log explorer (livetail, monitors or widget definition in dashboards for instance). 


### Group facets

Facets are grouped into meaningful thematics, to ease navigation in the facet list.


Assigning or reassigning a group for a facet (see #curate-facets hereafter) is only a matter of display in the facet list, and has no impact on search and analytics capabilities. 


### Filter facets

Use the search box on facets to scope down the whole facet list and jump more quickly to the very one you need to interact with. Facet search use both facet display name and facet field name to scope down results.

In case multiple facets match your filter, Datadog helps you figure out which facet is most likely of interest for you with a facet relevance indicator:

* Facets relying on [standard attributes][24] are considered more relevant
* Facets relying on aliased attributes are considered less relevant



## Curate Facets

### Out-of-the-box facets

Most common facets such as `Host`, `Service` or `URL Path` or `Duration` come out-of-the-box to start troubleshooting right away once your logs are flowing into log indexes.

Facets on [Reserved Attributes][25] and most [Standard Attributes][26] are available by default.


### Create Facets

As a matter of good practice, always consider using an existing facet rather than creating a new one (see #alias-facets hereafter). Using a unique facet for information of similar nature fosters cross-team collaboration. 

Note: Once a facet is created, its content is populated **for all new logs** flowing in **either** index.


**Create facet from log side panel**

The easiest way to create a facet is to it from the side panel. Doing so, most of the facet details such as the field name or the underlying type of data are pre-filled and it's only a matter of double-checking.

Navigate in the [Log Explorer][1] to whichever log of interest bearing the field to create a facet on. Open the side-panel for this log, click on the corresponding field (either in tags or in attributes), on create a facet from there:

* if the field has a string value, only facet (dimension) creation is available
* if the field has a numerical value, both facet (dimension) and measure creation are available.

{{< img src="logs/explorer/create_facet.png" style="width:50%;" alt="Create Facet"  style="width:30%;">}}


**Create facet from the facet list**

In case finding a matching log is not an option, create a new facet directly from the facet panel using the *add facet* button.

Define the underlying field (key) name for this facet:

* Use tag group name for tags.
* Use the attribute path for attributes, with `@` prefix. 

Autocomplete based on the content in logs of the current views helps you defining the proper field name. But you can use virtually any value here, specifically in case you don't have mathcing logs (yet) flowing in indexes.


### Alias Facets





[1] /logs/explorer/search/
[2] /logs/explorer/patterns/
[3] /logs/explorer/analytics/
[4] /monitors/monitor_types/log/
[5] /dashboards/widgets/
[6] /notebooks/

[7] /logs/processing/processors
[8] /logs/live_tail/
[9] /logs/archives/
[10] /logs/logs_to_metrics/

[11] /logs/processing/pipelines/
[12] /logs/indexes#indexes-filters
[13] /logs/indexes#exclusion-filters

[14] /tagging/assigning_tags
[15] /integrations/nginx/
[16] /integrations/kong/
[17] /logs/explorer/search/#search-syntax

[18] /integrations/varnish/
[19] /integrations/ansible/
[20] /integrations/python/

[21] /logs/processing/processors/?tab=ui#geoip-parser
[22] /logs/processing/processors/?tab=ui#arithmetic-processor
[23] /logs/explorer/saved_views/

[24] /logs/processing/attributes_naming_convention/

[25] /logs/processing/#reserved-attributes
[26] /logs/processing/attributes_naming_convention/#default-standard-attribute-list
