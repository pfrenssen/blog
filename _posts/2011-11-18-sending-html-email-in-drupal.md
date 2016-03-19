---
layout: post
title: Sending HTML email in Drupal
created: 1321636762
categories:
- !ruby/string:Sequel::SQL::Blob drupal 6
- !ruby/string:Sequel::SQL::Blob drupal 7
- !ruby/string:Sequel::SQL::Blob drupal 7
- !ruby/string:Sequel::SQL::Blob drupal 6
- !ruby/string:Sequel::SQL::Blob drupal 6
- !ruby/string:Sequel::SQL::Blob drupal 7
- !ruby/string:Sequel::SQL::Blob drupal 7
- !ruby/string:Sequel::SQL::Blob drupal 6
---
> These instructions are for Drupal 7 but should work equally well under Drupal 6.

By default Drupal sends out all its emails in plain text, but there are some good modules available that make it easy to send out HTML mails. The two most popular ones are <a href="http://drupal.org/project/htmlmail">HTML Mail</a> and <a href="http://drupal.org/project/mimemail">Mime Mail</a>. Both can achieve similar results, but this tutorial will explain how to do it with HTML Mail.

## What we want to achieve

To send all Drupal emails in HTML format, using a HTML template that is adapted from a free template as can be found in abundance on the internet.

## Installation

Download and install <a href="http://drupal.org/project/htmlmail">HTML Mail</a> and the <a href="http://drupal.org/project/mailsystem">Mail System</a> module on which it depends. The Mail System module provides some additional flexibility and robustness that the core Drupal mail system lacks.

## Adapting the HTML template

A default template file is supplied with HTML Mail that can be used as a base. This needs to be copied to the theme folder and edited to put in the custom HTML.

* Copy the htmlmail.tpl.php file from the HTML Mail module folder into your theme.
* Edit the file in the theme folder and find the following section:

```php
<div class="htmlmail-body">
  <?php echo $body; ?>
</div>
```

* Replace these three lines with the entire HTML code from your custom template. Take good care not to add an empty line at the top of your code, especially if your template starts with a doctype declaration or HTML head section.
* The next step is to find the spot where the body text is placed in the email template. Most of the time this will contain some dummy text. It might look something like this:

```html
<table border="0" cellpadding="0" cellspacing="0" width="600">
  <tr>
    <td valign="top">
      <table border="0" cellpadding="20" cellspacing="0" width="100%">
        <tr>
          <td valign="top">
            <div class="body">
              <h1>Title</h1>
              <strong>Lorem ipsum dolor sit amet:</strong> consectetur adipisicing elit, sed do eiusmod tempor incididunt ut <em>labore et dolore</em> magna aliqua.
              <br />
              Ut enim ad minim veniam, quis <a href="#">nostrud exercitation</a> ullamco laboris nisi ut aliquip ex ea commodo consequat.
            </div>
          </td>
        </tr>
      </table>
    </td>
  </tr>
</table>
```

* Replace the dummy text with the line `<?php echo $body; ?>`. In our example it would now look like this:

```php
<table border="0" cellpadding="0" cellspacing="0" width="600">
  <tr>
    <td valign="top">
      <table border="0" cellpadding="20" cellspacing="0" width="100%">
        <tr>
          <td valign="top">
            <?php echo $body; ?>
          </td>
        </tr>
      </table>
    </td>
  </tr>
</table>
```

* Save the file.
* Make a copy of the file inside your theme folder and name it `htmlmail--user--password_reset.tpl.php`. This is a small peculiarity of the HTML Mail module (<a href="http://drupal.org/node/1170576">issue</a>).

## Configuration

* First configure the Mail System module. Go to `admin/config/system/mailsystem` and set the <em>Site-wide default MailSystemInterface class</em> option to <em>HTMLMailSystem</em>.
* Now configure HTML Mail. Go to `admin/config/system/htmlmail` and select your theme underneath step 2.

Next up we will need to create a text format that will be responsible for processing the body text of the mails. You can use this to filter any user generated content, embed images, transform relative links into absolute ones and other nice tricks. For this example we will just use some simple filters, mainly to convert linebreaks in the emails into HTML-tags so that the standard Drupal mails don't come out as a wall of text.

* Create a new text formatter at `admin/config/content/formats/add`
* Name: `HTML email` (so the machine name will be `html_email` - we'll be needing this later).
* Enabled filters:
    * Convert URLs into links
    * Convert line breaks into HTML
    * Correct faulty and chopped off HTML
* Save the formatter, we're done with the configuration :)

You might be tempted to select this text format in the settings of HTML Mail, but this causes the <em>entire</em> email template to be filtered. We want the wrapping HTML to be left untouched, this text format should only be applied on the body text. To achieve this we need to do one last thing.

## Edit template.php

* Open the `template.php` file which lives in your theme folder.
* Add the following code, replacing MYTHEME with your theme name:

```php
/**
 * Process variables to format email messages.
 *
 * @see htmlmail.tpl.php
 */
function MYTHEME_preprocess_htmlmail(&$variables) {
  // Run the default preprocess function.
  template_preprocess_htmlmail($variables);

  // Use the 'HTML Email' text format for the message body.
  $variables['body'] = check_markup($variables['body'], 'html_email');
}
```

* Clear the cache and you're done! Have fun testing :)
