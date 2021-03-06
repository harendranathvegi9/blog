---
layout: post
title: Social media rich snippets with AngularJS and ngMeta
categories: ["tech"]
description: Show fallback meta title and description in social media preview snippets for your Angular + ngMeta site
date: 2016-11-26T12:22:06+01:00
---

[ngMeta](https://github.com/vinaygopinath/ngMeta), my Angular1 SEO meta tags library, has frequently seen issues related to the preview snippets or rich snippets generated by Facebook, Twitter, Skype, Whatsapp and others when a URL of an Angular site using ngMeta is shared. I've [addressed it](https://github.com/vinaygopinath/ngMeta/issues/16) on Github [several times](https://github.com/vinaygopinath/ngMeta/issues/20), so I thought I would explain the issue in greater detail here.

Sites like Facebook and Twitter use crawlers to fetch URLs and extract the [Open Graph](http://ogp.me/) meta values. When Open Graph data is not available, they fall back on the basic meta tags (`title`, `description` and others). Social media crawlers generally do not execute Javascript, meaning that they pick up the values provided in meta tags as-is. For sites that use ngMeta, meta tags might look like this:

<div class="scrollable-code">
{% highlight html%}
{% raw %}
<title ng-bind="ngMeta.title"></title>
<meta name="description" content="{{ngMeta.description}}" />
<meta property="og:title" content="{{ngMeta.title}}" />
<meta property="og:description" content="{{ngMeta.description}}" />
{% endraw %}
{% endhighlight %}
</div>

As a result, the rich snippet generated by social media sites is not pleasant (Remember, no Javascript!):
![Facebook rich snippet](http://i.imgur.com/wSNMYNF.png){:class="img-centered"}

However, Google search's crawlers *do* execute Javascript and the search result snippet uses the title, description and other tags set through ngMeta, as expected.

It is technically impossible for a front-end framework library like ngMeta to force crawlers to execute Javascript and pick up the meta content values set through Javascript. Instead, solutions include serving pre-rendered pages, or redirecting requests by crawlers to a specific service that serves a static page with meta tags relevant to the requested URL. For more on the latter, check out [angular-social-demo](https://github.com/michaelbromley/angular-social-demo) on GitHub.

Another option: If you're willing to forsake the customization of meta tags for different pages to get rid of uninterpolated Angular expressions like `ngMeta.title` in your social media snippets, consider adding fallback meta tags that provide a title, description and image for your site. These fallback meta tags must have `ng-if="false"` so that they are removed in a Javascript-enabled environment:

<div class="scrollable-code">
{% highlight html%}
{% raw %}
<title ng-bind="ngMeta.title"></title>
<meta name="description" content="{{ngMeta.description}}" />
<meta property="og:title" content="Site name or other general title" ng-if="false" />
<meta property="og:description" content="Site description" ng-if="false" />
<meta property="og:image" content="https://your-site.com/fallback-snippet-image.jpg" ng-if="false"/>
{% endraw %}
{% endhighlight %}
</div>

Feel free to check out the source code of the [ngMeta demo site](https://vinaygopinath.github.io/ngMeta/#/). I've just updated it with fallback meta tags.
