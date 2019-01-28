# Understanding How EMRFS Consistent View Tracks Objects in Amazon S3<a name="emrfs-files-tracked"></a>

EMRFS creates a consistent view of objects in Amazon S3 by adding information about those objects to the EMRFS metadata\. EMRFS adds these listings to its metadata when:
+  An object written by EMRFS during the course of an Amazon EMR job\.
+  An object is synced with or imported to EMRFS metadata by using the EMRFS CLI\.

Objects read by EMRFS are not automatically added to the metadata\. When EMRFS deletes an object, a listing still remains in the metadata with a deleted state until that listing is purged using the EMRFS CLI\. To learn more about the CLI, see [EMRFS CLI Reference](emrfs-cli-reference.md)\. For more information about purging listings in the EMRFS metadata, see [EMRFS Consistent View Metadata](emrfs-metadata.md)\.

For every Amazon S3 operation, EMRFS checks the metadata for information about the set of objects in consistent view\. If EMRFS finds that Amazon S3 is inconsistent during one of these operations, it retries the operation according to parameters defined in `emrfs-site` configuration properties\. After EMRFS exhausts the retries, it either throws a `ConsistencyException` or logs the exception and continue the workflow\. For more information about retry logic, see [Retry Logic](emrfs-retry-logic.md)\. You can find `ConsistencyExceptions` in your logs, for example:
+  listStatus: No Amazon S3 object for metadata item `/S3_bucket/dir/object`
+  getFileStatus: Key `dir/file` is present in metadata but not Amazon S3

If you delete an object directly from Amazon S3 that EMRFS consistent view tracks, EMRFS treats that object as inconsistent because it is still listed in the metadata as present in Amazon S3\. If your metadata becomes out of sync with the objects EMRFS tracks in Amazon S3, you can use the sync sub\-command of the EMRFS CLI to reset metadata so that it reflects Amazon S3\. To discover discrepancies between metadata and Amazon S3, use the diff\. Finally, EMRFS only has a consistent view of the objects referenced in the metadata; there can be other objects in the same Amazon S3 path that are not being tracked\. When EMRFS lists the objects in an Amazon S3 path, it returns the superset of the objects being tracked in the metadata and those in that Amazon S3 path\.