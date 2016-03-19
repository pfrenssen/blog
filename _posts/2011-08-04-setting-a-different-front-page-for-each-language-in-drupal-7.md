---
layout: post
title: Setting a different front page for each language in Drupal 7
created: 1312461451
categories:
- !ruby/string:Sequel::SQL::Blob drupal 7
---
The use case: a multilingual Drupal 7 site, with each language having a different node to serve as front page. Each of the nodes have the same URL alias <em>"home"</em>. Setting the homepage to the "home" URL does not work, since the Path module is returning the first match it finds, causing the wrong translation to be shown for all but one language.

The solution is in the `i18n_variables` module which is a part of the <a href="http://drupal.org/project/i18n">Internationalization module</a>. This module allows to customize the front page variable for every language separately.

We have to let Drupal know that the front page URL needs to be a multilingual variable. This can be done in <em>Configuration » Regional and language » Multilingual settings » Variables</em> (`admin/config/regional/i18n/variable`). The variable is called "Default front page"; place a checkmark in front of it and save.

Now when you go to the Site Information administration page at <em>Configuration » System » Site information</em> (`admin/config/system/site-information`) you can switch between all available languages and set the front page for each one separately. Make sure you use the original node URLs (eg. `node/24`), not any aliases you might have set.
