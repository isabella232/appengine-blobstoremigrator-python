# Google App Engine Blobstore to Google Cloud Storage Migration Tool

This tool is a drop-in module that allows you to migrate
all of your Blobstore blobs into a Google Cloud Storage (GCS) bucket.
In doing so, the tool will store a Datastore mapping from the existing
blob key to the GCS filename. Additionally, it is possible to create
a blob key for the new GCS file that can be used with other existing
Google App Engine APIs (like Image API); this new blob key is also
stored in the Datastore mapping.

## Getting started

This tool will upload to App Engine as a new module named `blob-migrator`.
Unless you happen to already have a module of that name, you can simply
upload the code without impacting your existing application. (If you do
happen to have a module of that name, you can change this tool by editing
`src/app.yaml`; search for "`module: blob-migrator`".)

Upload the `src` directory of this repository to your App Engine application
using `appcfg.py`:

```
    $ appcfg.py update src -A [application-id] -V migrator
```

If you are a user of [Cloud SDK](https://cloud.google.com/sdk/),
you can use the following commands in place of the above command:

```
  $ gcloud auth login

  $ gcloud config set project [application-id]

  $ gcloud preview app deploy src/app.yaml --version migrator
```

Once the application is uploaded, simply hit the home page of the module:

```
  https://migrator.blob-migrator.[application-id].appspot.com
```

Follow the on-screen instructions, but effectively, all you have to do
is enter the name of a GCS bucket that your application has write access
to and click **Start migration**.

## Re-migrating and catch-up migrations

The migration tool will skip over blobs that have already been migrated,
so it is safe and efficient to run multiple migrations. This is helpful
because the BlobInfo query is eventually consistent, so if your
application is still writing to Blobstore, or you later find some obscure
code that was continuing to write to Blobstore, you can safely re-run
this migration to catch-up blobs.

## Configuration settings

See details in `appengine_config.py` for settings that can be made.
For example, you can set the root directory of all the migrated files
on GCS (`ROOT_GCS_FOLDER`) or the name of the kind that stores the key
mappings on Datastore (`MAPPING_DATASTORE_KIND_NAME`).
Of course, if you edit these settings, you will need to re-upload the tool.

## Files API

The test code of this application uses the deprecated Files API to create
test blobs. If you get a notice from Google that your application is
still using this deprecated API, please remember that this tool is using
this API.

This tool uses the Files API only for test code; it is not part of the
mainline migration facility.
