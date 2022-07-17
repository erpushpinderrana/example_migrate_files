# Drupal Example Migrate Files Example - Module 
This is just a basic Example of Migrating Drupal 7 Files into Drupal 9 Media entities. Though there are some really good contributed modules such as [Media Migration](https://www.drupal.org/project/media_migration), [Migrate File To Media](https://www.drupal.org/project/migrate_file_to_media) and [Migrate File](https://www.drupal.org/project/migrate_file) which does most of the stuff automatically/out of the box once we configure them correctly. I think for begineers sometimes it's hard to understand what's the underlyine process, hence I've created this example module to get an understanding of files migration from D7 to D9. 

## Basic Idea
Files migration is two step process. First, migrate files from D7 to D9. Second, migrate D7 files to D9 Media entities.

## Example
Let's migrate one article from D7 to D9 which include one file as well. Now first migrate associated file and subsequently the article. Assume in D7 the example article node nid is 1234 and the associated file (fid) to this node is 12.

Instead of migrating all the D7 files in single shot, we can also migrate only one file at a time. It would help to determine the workflow of migration process.

```
drush migrate:import example_files --idlist=12
```
```
[notice] Processed 1 item (1 created, 0 updated, 0 failed, 0 ignored) - done with 'example_files'
```
Once you run the above file migration then in your D9 site, you can check the files. For example, for my local env the path is http://drupal9.docksal/admin/content/files. That file would be there plus the actual physical D7 file/asset would be in your D9 destination directory as well.

The second step is to create the Media entity using Media Migration.
```
drush migrate:import example_media_image --idlist=12
```
```
[notice] Processed 1 item (1 created, 0 updated, 0 failed, 0 ignored) - done with 'example_media_image'
```

Once you run the above migration then it will create the media entity too. The same can be checked at Media path - http://drupal9.docksal/admin/content/media

Lastly, migrate the example article entity.
```
drush migrate-import example_article --idlist=1234 --migrate-debug
```
```
[notice] Processed 1 item (1 created, 0 updated, 0 failed, 0 ignored) - done with 'example_article'
```

## Rollback commands 
```
drush migrate-rollback example_files --idlist=12
```
```
[notice] Rolled back 1 item - done with 'example_files'
```
```
drush migrate-rollback example_media_image --idlist=12
```
```
[notice] Rolled back 1 item - done with 'example_media_image'
```
```
drush migrate-rollback example_article --idlist=1234
```
```
[notice] Rolled back 1 item - done with 'example_article'
```

## Handy Drush Commands. The below examples are using fin because I'm using docksal locally.
```
fin drush pmu example_migrate_files -y && fin drush en example_migrate_files -y
```
```
fin drush migrate-import example_files
```
```
fin drush migrate-reset example_files
```
```
fin drush migrate-rollback example_files
```
```
fin drush migrate-import example_media_image
```
```
fin drush migrate-reset example_media_image
```
```
fin drush migrate-rollback example_media_image
```
```
fin db cli --db=default 'select * from migrate_map_example_files'
```
```
fin db cli --db=default 'select * from migrate_map_example_media_image'
```
```
fin db cli --db=default 'show tables'
```
## How to Debug, if migration fails.
Enable commented dump variable in the source file to see what file path and query is building from D7. It really helps me a lot while debugging but there are other ways too.
 - Ensure the D7 file path is valid.
 - Ensure the destination path has correct permissions.
 
 ## References
 * https://www.computerminds.co.uk/articles/migrating-drupal-7-files-drupal-8-9-media-entities
 * https://antistatique.net/en/blog/migrate-your-drupal-7-site-to-drupal-9
 * https://evolvingweb.ca/blog/drupal-8-migration-migrating-files-images-part-3
 * https://www.drupal.org/docs/drupal-apis/migrate-api/migrate-destination-plugins-examples/migrating-files-and-images
 * https://understanddrupal.com/lessons/migrating-files-and-images-drupal/
 * https://gist.github.com/jibran/8e7cd2319e873858dd49a272227a4fd2
 
