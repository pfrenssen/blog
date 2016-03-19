---
layout: post
title: Populating a vocabulary with provinces, towns and postal codes
created: 1314983762
categories:
- !ruby/string:Sequel::SQL::Blob drupal 7
- !ruby/string:Sequel::SQL::Blob location
- !ruby/string:Sequel::SQL::Blob drupal 7
- !ruby/string:Sequel::SQL::Blob location
---
I'm working on a website that required an easy way to associate locations with a content type. It should be possible to first select a Province from a dropdown list, and then choose from a list of the towns in that province.

I have implemented this using the following popular modules:
<ul><li><a href="http://drupal.org/project/hierarchical_select">Hierarchical Select</a>: Provides easy to use drill-down select boxes, exactly what the project needed.</li>
<li><a href="http://drupal.org/project/location">Location</a>: Associates location information with entities, including taxonomy terms. As a bonus it contains a list of Belgian towns and post codes.</li>
<li><a href="http://drupal.org/project/references">References</a>: Allows to add taxonomy reference fields to content types.</li></ul>

Setting it up was easy enough: I created a vocabulary with Locative Information enabled and a content type containing a reference field to this vocabulary and chose the Hierarchical Select widget for this field.

Now all I needed was to populate the vocabulary so it contains all Belgian towns, with the province as parent term. I imported the Belgian zipcodes database that is included in the Location module, and made a script that uses this data to populate the vocabulary.

If you have the same use case, you'll need to adapt the script so it matches your country's provinces / states, but I hope this will save you some time.

```php
<?php
/**
 * @file
 * Import location information into a taxonomy vocabulary.
 *
 * This script takes the Belgian 'zipcodes' table as supplied by the Location
 * module and uses it to populate a taxonomy vocabulary. The Belgian provinces
 * are used as parent items with the towns as children. Each town will get its
 * location information associated with it.
 *
 * This requires a taxonomy vocabulary to be set up with Location support. If
 * there are already terms present in the vocabulary they will be deleted
 * without warning.
 *
 * Usage:
 * - Enter the vocabulary id of the vocabulary you want to populate below and
 *   save the script in the Drupal root folder, or inside the 'scripts' folder.
 * - Run the script with drush:
 *   drush scr location_import.php
 */
// The ID of the vocabulary that will be populated with location information. 
define('LOCATION_VID', 1);

// Check if required modules are present.
if (!module_exists('location') || !module_exists('taxonomy') || !module_exists('location_taxonomy')) {
  drush_log('This script requires the following modules: Taxonomy, Location, Location Taxonomy.', 'error');
  exit;
}

// Check if vocabulary exists and has Locative Information enabled.
$settings = variable_get('location_taxonomy_' . LOCATION_VID, FALSE);
if (!isset($settings['multiple']['max']) || !$settings['multiple']['max']) {
  drush_log('Make sure the vocabulary has Locative Information enabled: set the "Maximum number of locations" to "1".', 'error');
  exit;
}

// A list of Belgian provinces mapped to their state codes.
$provinces = array(
  'BRU' => array('name' => 'Brussel'),
  'VAN' => array('name' => 'Antwerpen'),
  'VBR' => array('name' => 'Vlaams-Brabant'),
  'VLI' => array('name' => 'Limburg'),
  'VOV' => array('name' => 'Oost-Vlaanderen'),
  'VWV' => array('name' => 'West-Vlaanderen'),
  'WBR' => array('name' => 'Brabant Wallon'),
  'WHT' => array('name' => 'Hainaut'),
  'WLG' => array('name' => 'Liège'),
  'WLX' => array('name' => 'Luxembourg'),
  'WNA' => array('name' => 'Namur'),
);

// Delete all entries in the vocabulary.
$terms = taxonomy_get_tree(LOCATION_VID);
foreach ($terms as $term) {
  drush_print('Deleting term ' . $term->name);
  taxonomy_term_delete($term->tid);
}

// Create terms for the provinces.
foreach ($provinces as $key => $province) {
  drush_print('Creating parent term ' . $province['name']);
  $term = new stdClass();
  $term->vid = LOCATION_VID;
  $term->name = $province['name'];
  taxonomy_term_save($term);
  // Retain the tids of the newly created terms so they can be referenced when
  // saving the towns.
  $provinces[$key]['tid'] = $term->tid;
}

// Fetch all location information.
$query = db_select('zipcodes', 'zc');
$query->addField('zc', 'zip', 'postal_code');
$query->addField('zc', 'city');
$query->addField('zc', 'state', 'province');
$query->addField('zc', 'country');
$query->addField('zc', 'latitude');
$query->addField('zc', 'longitude');
$query->orderBy('postal_code', 'ASC');
$locations = $query->execute()->fetchAll(PDO::FETCH_ASSOC);

// Populate the terms and locations.
foreach ($locations as $location) {
  // The city names might contain accented characters. 
  $location['city'] = utf8_decode($location['city']);

  drush_print($location['postal_code'] . ' ' . $location['city']);

  // Create a new taxonomy term with the province as parent.
  $term = new stdClass();
  $term->vid = LOCATION_VID;
  $term->name = $location['city'];
  $term->parent = $provinces[$location['province']]['tid'];
  taxonomy_term_save($term);

  // Add the location to the database. location_save_locations() expects this to
  // be wrapped in an array.
  $location = array($location);
  location_save_locations($location, array('genid' => 'taxonomy:' . $term->tid));
}

drush_log('Locations imported successfully.', 'success');
```
