---
title: "Cache management"
description: "Learn about the specific control over device map caches that the Mapbox Maps SDK for Android provides."
prependJs:
  - "import AndroidActivityToggle from '../../../components/context-dependent/android-activity-toggle';"
  - "import Note from '@mapbox/dr-ui/note';"
  - "import RelatedPage from '@mapbox/dr-ui/related-page';"
contentType: guide
language:
- Java
- Kotlin
---

When a map is loaded and viewed on a device, the tiles are placed in the device's **ambient cache**. The ambient cache stores tiles that have already been visited by an end user so that the next time the user returns, the map loads nearly instantaneously. The map loads quickly because the device doesn't have to communicate with a server to request and receive information to render the map.

The Maps SDK for Android has several different methods for managing the device cache. These methods can modify the ambient cache, offline cache, or the entire database that houses the offline and ambient cache.

{{<Note theme="warning" title="Cache specificity">}}
The cache management methods operate at the map tile level, rather than at the map resource level. For example, the methods do not delete specific `LatLngBounds`, map layers, or GeoJSON `Feature`s from the cache.
{{</Note>}}

## Recommended implementation options

There are three options for implementing any of the following cache management methods. You can build a user interface for the end user, a cloud notification system, or a remote configuration system. The option you choose will depend in part on whether you want end users to trigger the changes to the cache.

If end users are going to trigger these methods, then build an interface that users can interact with. This could be an option to clear the cache in an app's Settings page or a button near the Mapbox map.

<!--copyeditor ignore simple-->

If end users are not going to trigger these methods, then set up a backend cloud-based server to send instructions to the device to manipulate the cache. One option is using a notification system such as [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging), [Amazon’s Simple Notification Service](https://aws.amazon.com/sns/), [SendBird](https://sendbird.com/features/chat-messaging), [OneSignal](https://documentation.onesignal.com/docs/android-sdk-setup), or [Urban Airship](https://www.airship.com/).


{{
  <RelatedPage
    url="https://github.com/mapbox/mapbox-android-demo/blob/master/MapboxAndroidDemo/src/main/java/com/mapbox/mapboxandroiddemo/utils/CustomFirebaseMessagingService.java"
    title="Backend notification"
    contentType="example">
}}
See how Firebase Messaging can be hooked into the Maps SDK's cache management.
{{</RelatedPage>}}

The other backend cloud option is setting up a remote configuration system, also known as feature flagging. Firebase's remote configuration system is a popular choice for Android developers. Other systems that manage A/B testing and feature flagging include [Apptimize](https://apptimize.com/feature-flags/) and [LaunchDarkly](https://launchdarkly.com/). The remote configuration system will determine how and when a device should call a cache management method at runtime.

{{
  <RelatedPage
    url="/android/maps/examples/cache-management/"
    title="Cache management"
    contentType="example">
}}
See how Firebase Remote Config can be connected to the Maps SDK's cache management.
{{</RelatedPage>}}

<!--copyeditor enable ignore-->
## Management methods

**`invalidateAmbientCache()`**

Forces a revalidation of the tiles in the ambient cache and downloads a fresh version of the tiles from the tile server. **This is the recommend method for clearing the cache.** This is the most efficient method because tiles in the ambient cache are re-downloaded to remove outdated data from a device. It does not erase resources from the ambient cache or delete the database, which can be computationally expensive operations that may carry unintended side effects.

Note: Ambient cache tiles that overlap with offline regions will not be affected by this call.


{{
<AndroidActivityToggle
  id="invalidate-ambient-cache"

java={`
OfflineManager fileSource = OfflineManager.getInstance(this);

fileSource.invalidateAmbientCache(new OfflineManager.FileSourceCallback() {
	@Override
	public void onSuccess() {

	}
	
	@Override
	public void onError(@NonNull String message) {
	
	}
});
`}

kotlin={`
val fileSource = OfflineManager.getInstance(context)

fileSource.invalidateAmbientCache(object : OfflineManager.FileSourceCallback {
	override fun onSuccess() {

	}
	
	override fun onError(message: String) {


	}
})
`}

/>
}}

**`clearAmbientCache()`**

Erases resources from the ambient cache. This method clears the cache and decreases the amount of space that map resources take up on the device. However, this reduction in space is not permanent unless the size of the ambient cache is decreased (see `setMaximumAmbientCacheSize()`), because a device's ambient cache will fill up again as an end user uses the map. This method can be computationally expensive and may result in unintended side effects. **We do not recommend this method unless you are sure it is necessary for your use case.**


{{
<AndroidActivityToggle
  id="clear-ambient-cache"

java={`
OfflineManager fileSource = OfflineManager.getInstance(this);

fileSource.clearAmbientCache(new OfflineManager.FileSourceCallback() {
	@Override
	public void onSuccess() {

	}
	
	@Override
	public void onError(@NonNull String message) {
	
	}
});
`}

kotlin={`
val fileSource = OfflineManager.getInstance(context)

fileSource.clearAmbientCache(object : OfflineManager.FileSourceCallback {
	override fun onSuccess() {

	}
	
	override fun onError(message: String) {


	}
})
`}

/>
}}

**`setMaximumAmbientCacheSize()`**

Sets the maximum size of the ambient cache in bytes. Disables the ambient cache if set to `0`.

This method may be computationally expensive because it will erase resources from the ambient cache if its size is decreased.

Note that offline regions detract from the space available for ambient caching, but `setMaximumAmbientCacheSize()` does not block offline downloads. For example, if the maximum ambient cache size is 50 MB and 40 MB are already used by offline regions, the ambient cache size will be 10 MB.

{{
<AndroidActivityToggle
  id="set-ambient-cache-size"

java={`
OfflineManager fileSource = OfflineManager.getInstance(this);

long DESIRED_AMBIENT_CACHE_SIZE = 5000000;

fileSource.setMaximumAmbientCacheSize(DESIRED_AMBIENT_CACHE_SIZE, new OfflineManager.FileSourceCallback() {
	@Override
	public void onSuccess() {

	}
	
	@Override
	public void onError(@NonNull String message) {
	
	}
});
`}

kotlin={`
val fileSource = OfflineManager.getInstance(context)

val DESIRED_AMBIENT_CACHE_SIZE = 5000000

fileSource.setMaximumAmbientCacheSize(DESIRED_AMBIENT_CACHE_SIZE, object : OfflineManager.FileSourceCallback {
	override fun onSuccess() {

	}
	
	override fun onError(message: String) {
	
	}
})
`}

/>
}}

**`resetDatabase`**

Deletes the existing database that houses the ambient and offline cache, and reinitializes the database. This method may result in unintended side effects. **We do not recommend this method unless you are sure it is necessary for your use case.**

{{
<AndroidActivityToggle
  id="reset-database"

java={`
OfflineManager fileSource = OfflineManager.getInstance(this);

fileSource.resetDatabase(new OfflineManager.FileSourceCallback() {
	@Override
	public void onSuccess() {

	}
	
	@Override
	public void onError(@NonNull String message) {
	
	}
});
`}

kotlin={`
val fileSource = OfflineManager.getInstance(context)

fileSource.resetDatabase(object : OfflineManager.FileSourceCallback {
	override fun onSuccess() {

	}
	
	override fun onError(message: String) {
	
	}
})
`}

/>
}}