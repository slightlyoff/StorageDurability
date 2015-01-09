<h2>Storage Durability Explained</h2>

## What's All This About?

Modern web applications can engage users as deeply as native/desktop apps. These apps frequently store large amounts of client-side state, e.g. a list of previously recieve messages or documents/movies/songs to be saved or edited. This information may be expensive to re-synchronize (e.g., movies or game levels) and users may not expect such applications to simply disappear when they naively clear cookies.

The web currently lacks any ability to provide durable storage in a storage-system neutral way, instead providing (best case) a [single durable storage interface][persistent_filesystem]. Current approaches require an application to either attempt to shoehorn all storage into a [broadly-incompatible storage type][persistent_filesystem] (and suffer losing all state stored in IDB, Cookies, Service Worker registrations, Caches, and LocalStorage) or never assume any storage durability. The first is hardly better than the second: what is an application without its Service Workers, Caches, Databases, Cookies, and all the rest? How is a developer meant to reason about having some but not all of these persistently?

Native application platforms do not suffer these indignities, instead providing durable storage as the default.

We propose a new API which extends grants durability to _all_ storage at an origin, fully independent of the [Quota API][quota_api]. This is coupled with a new document-side API for registering (and unregistering) interest in `onsync`. Together, these APIs form the basis of a powerful new capability for rich web apps.

## Requesting Storage Durability

```html
<!DOCTYPE html>
<!-- https://example.com/index.html -->
<html>
  <head>
    <script>
      var log = console.log.bind(console);
      var err = console.error.bind(console);

      // See how much space has been given to temporary quota
      navigator.storageQuota.queryInfo("temporary").then(
        function(temporaryStorageInfo) {
          // Request that all storage become persistent
          navigator.requestStorageDurability().then(
            function() {
              // Check to see how much is now charged to persistent quota
              navigator.storageQuota.queryInfo("persistent").then(
                function (storageInfo) {
                  log(storageInfo.quota == temporaryStorageInfo.quota); // true
                  log(storageInfo.usage == temporaryStorageInfo.usage); // true
                }
            },
            err
          );
        },
        err
      );
    </script>
  </head>
  <body> ... </body>
</html>
```

## Handling Durability Changes

TODO(slightlyoff)


[persistent_filesystem]: http://www.html5rocks.com/en/tutorials/file/filesystem/
[quota_api]: http://www.w3.org/TR/quota-api
