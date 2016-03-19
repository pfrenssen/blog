---
layout: post
title: Easy to use content editing with TinyMCE
created: 1264782452
categories:
- !ruby/string:Sequel::SQL::Blob web development
- !ruby/string:Sequel::SQL::Blob wysiwyg
- !ruby/string:Sequel::SQL::Blob web development
- !ruby/string:Sequel::SQL::Blob wysiwyg
---
Web site owners prefer to have a CMS that is tailored to their personal computer expertise level. TinyMCE is great for the less computer literate user, as its button driven interface resembles the common word processors they are familiar with and all confusing HTML markup is hidden.

TinyMCE can be customized, allowing you to pick only the functions that are needed. Still some things can be simplified even further for computer novices. I'll show how you can hide potentially confusing options such as link targets and titles using CSS.

In this article I will set up an extremely easy to use editor with support for paragraphs, links and bulleted lists, alongside with cut/copy/paste and undo/redo buttons for convenience.

## Javascript options

To start, download the TinyMCE package and unpack it in your /js folder. You can start tweaking the editor right away by adding a couple of lines of javascript. The editor will automatically replace all textarea form fields on the page.

```html
<script type="text/javascript" src="/js/tiny_mce/tiny_mce.js"></script>
<script type="text/javascript">
tinyMCE.init({
  mode : "textareas",
  theme : "advanced",
  theme_advanced_buttons1 : "bullist,link,unlink,separator,cut,copy,paste,separator,undo,redo",
  theme_advanced_buttons2 : "",
  theme_advanced_buttons3 : "", 
  theme_advanced_toolbar_location : "top",
  theme_advanced_toolbar_align : "left",
  theme_advanced_path_location : "bottom",
  plugins : 'inlinepopups',
  content_css : "/css/tinymce_content.css",
  popup_css : "/css/tinymce_popup.css"
});

function toggleEditor(id) {
  if (!tinyMCE.get(id))
    tinyMCE.execCommand('mceAddControl', false, id);
  else
    tinyMCE.execCommand('mceRemoveControl', false, id);
}
</script>
</code>
```

In the above code I have specified that the editor will <ul><li>replace existing textareas</li>
<li>use the <em>advanced</em> theme</li>
<li>which buttons are visible and where they are placed</li>
<li>use my own css files for the inside of the editor (`/css/tinymce_content.css`) and the popups (`/css/tinymce_popup.css`)</li>
</ul>

## Hiding options in the theme file

This setup is already very easy to use, but it can be simplified even more by removing the <em>target</em> and <em>title</em> options from the Insert/Edit Link popup. This can be done by hiding them using css.

The theming file of this popup dialog is `/js/tiny_mce/themes/advanced/link.htm`. Find the part that contains the <em>targetlist</em> and <em>linktitle</em> table rows, and hide them with `style="display:none;"` like this:

```html
<tr style="display:none;">
  <td><label id="targetlistlabel" for="targetlist">{#advanced_dlg.link_target}</label></td>
  <td><select id="target_list" name="target_list"></select></td>
</tr>

<tr style="display:none;">
  <td class="nowrap"><label for="linktitle">{#advanced_dlg.link_titlefield}</label></td>
  <td><input id="linktitle" name="linktitle" type="text" value="" style="width: 200px" /></td>
</tr>
```

To further style the appearance of the editor, you can edit the css files. The <em>content.css</em> file is responsible for the look of the text inside the editor, you can increase the default font size here. <em>dialog.css</em> styles the popup dialogs.

* Copy the dialog.css and content.css files from `/js/tiny_mce/themes/advanced/skins/default/` to your `/css` folder and rename them to `tinymce_popup.css` and `tinymce_content.css`.
* Copy all the images in `/js/tiny_mce/themes/advanced/skins/default/img/` to your `/css/img/` folder.


## Toggling between wysiwyg and HTML

You can switch between the wysiwyg editor and standard HTML by inserting this link:

```html
<a href="javascript:toggleEditor('PostBody');">Toggle editor</a>
```

Links:
<ul>
<li><a href="http://tinymce.moxiecode.com/">TinyMCE homepage</a></li>
<li><a href="http://tinymce.moxiecode.com/download.php">TinyMCE downloads</a></li>
</ul>
