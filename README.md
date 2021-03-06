# advanced-csv-importer

[![Build Status](https://travis-ci.org/miya0001/advanced-csv-importer.svg?branch=master)](https://travis-ci.org/miya0001/advanced-csv-importer)

Import posts, pages, custom fields, categories, tags and more from a CSV file.

Advanced CSV Importer is fully unit-tested. The plugin is stable and ready to be used in large projects like enterprise applications.

## WP-CLI support

### Importing CSV

```
$ wp csv import tests/_data/wp/sample.csv
+------+-----------------------+------+---------+------------+---------------------+
| ID   | Title                 | Type | Status  | Author     | Date                |
+------+-----------------------+------+---------+------------+---------------------+
| 1720 | CSV Import Test       | post | publish | admin      | 2013-09-13 00:00:00 |
| 1721 | define author test    | post | publish | admin      | 2014-12-27 18:44:46 |
| 1722 | define author id test | post | publish | themedemos | 2014-12-27 18:44:46 |
| 1    | Hello world! Updated! | post | publish | admin      | 2014-12-27 18:44:46 |
+------+-----------------------+------+---------+------------+---------------------+
```

### History

```
$ wp csv log
+----------+-----------------------------+---------------------+---------+---------+
| ID       | Title                       | Date                | Success | Failure |
+----------+-----------------------------+---------------------+---------+---------+
| e0a66344 | Imported from WP-CLI.       | 2014-12-27 18:44:46 |       4 |       0 |
| 43c47af6 | Imported from admin screen. | 2014-12-27 16:53:17 |       4 |       0 |
| df0f140b | Imported from WP-CLI.       | 2014-12-27 16:21:42 |       4 |       0 |
+----------+-----------------------------+---------------------+---------+---------+
```

### Details of the history

```
$ wp csv log e0a66344
+------+-----------------------+------+---------+------------+---------------------+
| ID   | Title                 | Type | Status  | Author     | Date                |
+------+-----------------------+------+---------+------------+---------------------+
| 1720 | CSV Import Test       | post | publish | admin      | 2013-09-13 00:00:00 |
| 1721 | define author test    | post | publish | admin      | 2014-12-27 18:44:46 |
| 1722 | define author id test | post | publish | themedemos | 2014-12-27 18:44:46 |
| 1    | Hello world! Updated! | post | publish | admin      | 2014-12-27 18:44:46 |
+------+-----------------------+------+---------+------------+---------------------+
```

### Delete imported posts

```
$ wp post delete $(wp csv log e0a66344 --format=ids)
Success: Trashed post 1720.
Success: Trashed post 1721.
Success: Trashed post 1722.
Success: Trashed post 1.
```

## How to customize

### Default CSV field names

* ID
* post_content
* post_name
* post_title
* post_status
* post_type
* post_author
* ping_status
* post_parent
* menu_order
* to_ping
* pinged
* post_password
* guid
* post_content_filtered
* post_excerpt
* post_date
* post_date_gmt
* comment_status
* post_category
* tags_input
* page_template

You can change field name via `acsv_post_object_keys` hook like following.

```
add_filter( 'acsv_post_object_keys', function( $post_object_keys ){
    $post_object_keys['title'] = 'post_title';
    $post_object_keys['content'] = 'post_content';
    return $post_object_keys;
} );
```

Other columns will be saved to the custom field.

### Post thumbnail support

```
add_action( 'acsv_after_insert_post', function( $post_id, $post, $helper ){
    $id = $helper->add_media( $post['post_meta']['post_thumbnail'] );
    update_post_meta( $post_id, '_thumbnail_id', $id );
}, 10, 3 );
```

CSV format should be like below.

```
post_title,post_thumbnail
This is my cool photo!,http://example.com/path/to/photo.jpg
```

### Converting charset

```
add_filter( 'acsv_csv_format', function( $format ){
    $format['from_charset'] = 'SJIS-win';
    return $format;
} );
```

## Contributions

```
$ git clone git@github.com:miya0001/advanced-csv-importer.git
$ cd advanced-csv-importer
$ composer install
```

### Running the phpunit.

```
$ bin/install-wp-tests.sh <db-name> <db-user> <db-pass> [db-host] [wp-version]
```

Then run tests.

```
$ phpunit
```
