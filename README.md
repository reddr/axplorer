# axplorer

axplorer (Android explorer) is a static analysis tool to study Android's application framework's internals. It generates high-precision call-graphs for middleware services that are subsequently analyzed via control-flow slicing.
As one of the results, axplorer can generate very accurate Android API to permission mappings.<br>

Note: As for now we haven't released the source code yet, due to time constraints that
prevent us from publishing well written and documented code.

## Android Permission Mappings
For the API levels 16 (4.1) - 25 (7.1) we currently provide three different mappings (SDK, Application Framework, and ContentProviders).<br>
For the SDK and framework mapping, the presence of multiple permissions does not necessarily mean that all permissions are required to execute the method. Often, permissions are only required for specific input arguments or execution paths. Hence, axplorer reports the upper threshold (a more fine-grained path-sensitive analysis is currently not available).<br>

Current shortcomings:<br>
 * Maps for SDK/Framework >23 might be incomplete due to the fact that parts of the permission checking have been moved to the AppOpsManager (runtime permission checks).
   This requires some additional anaylsis which we have not implemented yet.
 * Maps are currently missing APIs with permissions checked in native code (Camera, etc.). We hope to provide these soon.


### Application Framework Mapping
The framework permission mapping includes any permission-protected, public method/API of the application framework that is accessible via inter-process communication (IPC).
Apps might bypass the Android SDK to directly access these methods.

>     <method signature> :: <permission> [,<permissions>]


### Android SDK Mapping
The SDK mapping includes the documented API that requires at least one permission. The documented API comprises everything the app developer is supposed to use when implementing against the SDK.

>     <method signature> :: <permission> [,<permissions>]


### ContentProvider Mapping
The ContentProvider (CP) mappings includes any system ContentProviders that protect read/write operations on the entire CP or paths thereof with permissions (e.g. contact content provider).
The mapping follows the template below (derived from [Content-Provider Basics](https://developer.android.com/guide/topics/providers/content-provider-basics.html).
>  <classname> content://<authority> <path|pathPrefix|pathPattern:<path*>>  [R|W|RW|grant-uri-permission] <permission>
>
>There exist four different invariants:
>    1.  <classname>  content://<authority>  [R|W|RW]  <permission>
>    2.  <classname>  content://<authority>  [grant-uri-permission]
>    3.  <classname>  content://<authority>  <path|pathPrefix|pathPattern:<path*>>  [R|W|RW]  <permission>
>    4.  <classname>  content://<authority>  <path|pathPrefix|pathPattern:<path*>>  [grant-uri-permission]
>
>  Example (invariant 1)<br>
>  * com.android.providers.contacts.CallLogProvider  content://call_log  [R]  android.permission.READ_CALL_LOG
>  * com.android.providers.contacts.CallLogProvider  content://call_log  [W]  android.permission.WRITE_CALL_LOG
>
>  Example (invariant 3):<br>
>  * com.android.bluetooth.opp.BluetoothOppProvider  content://com.android.bluetooth.opp  <path:/btopp>  [RW]  android.permission.ACCESS_BLUETOOTH_SHARE
>
>  Example (invariant 3+4):<br>
>  * com.android.providers.downloads.DownloadProvider  content://downloads  <pathPrefix:/all_downloads/>  [grant-uri-permission] 
>  * com.android.providers.downloads.DownloadProvider  content://downloads  <pathPrefix:/all_downloads>  [RW]  android.permission.ACCESS_ALL_DOWNLOADS
>  * com.android.providers.downloads.DownloadProvider  content://downloads  <pathPrefix:/my_downloads>  [RW]  android.permission.INTERNET
>  * com.android.providers.downloads.DownloadProvider  content://downloads  <pathPrefix:/download>  [RW]  android.permission.INTERNET


## Permission Statistics
Number of permissions per API level by permission protection level.<br>
**API levels:**<br>
API 16 (Android 4.1, 4.1.1), API 17 (Android 4.2, 4.2.2), API 18 (Android 4.3), API 19 (Android 4.4),
API 21 (Android 5.0), API 22 (Android 5.1), API 23 (Android 6.0), API 24 (Android 7.0), API 25 (Android 7.1)<br>

**Abbreviations:**<br>
signature (**sig**), development (**dev**), privileged (**priv**), installer (**inst**), preinstalled (**pre**)<br>

| Protection level/API  | 16 | 17 | 18 | 19 | 21 | 22 | 23 | 24 | 25 |
| --------------------- | --:| --:| --:| --:| --:| --:| --:| --:| --:|
| normal                | 18 | 23 | 23 | 24 | 25 | 25 | 29 | 29 | 29 |
| dangerous             | 49 | 39 | 37 | 35 | 39 | 38 | 20 | 21 | 21 |
| signature (sig)       | 25 | 34 | 40 | 42 | 43 | 43 | 44 | 54 | 54 |
| sig\|system           | 32 | 38 | 36 | 46 | 61 | 64 |  5 |  1 |  2 |
| sig\|system\|dev      |  8 |  9 | 11 | 11 | 12 | 12 |  - |  - |  - |
| sig\|priv             |  - |  - |  - |  - |  - |  - | 60 | 71 | 72 |
| sig\|priv\|dev        |  - |  - |  - |  - |  - |  - | 12 | 13 | 12 |
| sig\|priv\|dev\|appop |  - |  - |  - |  - |  - |  - |  1 |  1 |  1 |
| sig\|priv\|inst       |  - |  - |  - |  - |  - |  - |  1 |  1 |  1 |
| sig\|inst             |  - |  - |  - |  - |  - |  - |  4 |  4 |  4 |
| sig\|inst\|verifier   |  - |  - |  - |  - |  - |  - |  2 |  3 |  3 |
| sig\|dev\|appop       |  - |  - |  - |  - |  1 |  1 |  - |  - |  - |
| sig\|pre\|appop\|pre23|  - |  - |  - |  - |  - |  - |  1 |  1 |  1 |
| **Total permissions:**|132 |143 |147 |158 |181 |183 |179 |199 |200 |



## Manifest files
The *manifests* directory contains the original AndroidManifest files for the framework (named: AndroidManifest-$APILEVEL.xml) and all system apps within the AOSP release.

## Scientific publication

**Abstract**<br>
In contrast to the Android application layer, Android's application framework's
internals and their influence on the platform security and user privacy are 
still largely a black box for us. In this paper, we establish a static runtime
model of the application framework in order to study its internals and provide
the first high-level classification of the framework's protected resources. We
thereby uncover design patterns that differ highly from the runtime model at
the application layer.<br>

We demonstrate the benefits of our insights for security-focused analysis of
the framework by re-visiting the important use-case of mapping Android
permissions to framework/SDK API methods. We, in particular, present a novel
mapping based on our findings that significantly· improves on prior results in
this area that were established based on insufficient knowledge about the 
framework's internals. Moreover, we introduce the concept of permission
locality to show that although framework services follow the principle of
separation of duty, the accompanying permission checks to guard sensitive
operations violate it.<br>

**Paper**<br>
For technical details and evaluation results, please refer to our publication:<br>
> On Demystifying the Android Application Framework: Re-Visiting Android Permission Specification Analysis<br>
> Usenix Security 2016<br>
> https://www.infsec.cs.uni-saarland.de/~derr/publications/pdfs/derr_sec16.pdf
