# axplorer

axplorer (Android explorer) is a static analysis tool to study
Android's application framework's internals. It generates high-precision call-graphs
for middleware services that are subsequently analyzed via control-flow slicing.
As one of the results, axplorer can generate very accurate Android API to permission mappings.<br>

Note: As for now we haven't released the source code yet, due to time constraints that
prevent us from publishing well written and documented code.

## Android Permission Mappings
For each API version, we currently provide three different mappings (SDK, framework, and ContentProviders).
The SDK mapping includes the documented API, i.e. everything the app developer is supposed to use when implementing against the SDK.
The framework mapping includes any permission-protected API that is exposed by the framework via IPC. The ContentProvider mappings finally include any system ContentProviders that protect read/write operations on the entire CP or paths with permissions (e.g. contacts).<br>

In addition, we provide the original AndroidManifest files for the framework and the system apps.<br>
Currently we provide mappings for the following versions:
 * API 24/25 soon..
 * API 23 (Android 6.0)
 * API 22 (Android 5.1)
 * API 21 (Android 5.0)
 * API 19 (Android 4.4)
 * API 18 (Android 4.3)
 * API 17 (Android 4.2, 4.2.2)
 * API 16 (Android 4.1, 4.1.1)

Currently missing:<br>
 * Maps for SDK/Framework 23+ might be incomplete due to the fact that parts of the permission checking have been moved to the AppOpsManager (runtime permission checks).
   This requires some additional anaylsis which we have not implemented yet.
 * Maps are currently missing APIs with permissions checked in native code (Camera, etc.). We hope to provide these soon.


## Scientific publication

<b>Abstract</b><br>
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

<b>Paper</b><br>
For technical details and evaluation results, please refer to our publication:<br>
> On Demystifying the Android Application Framework: Re-Visiting Android Permission Specification Analysis<br>
> Usenix Security 2016<br>
> https://www.infsec.cs.uni-saarland.de/~derr/publications/pdfs/derr_sec16.pdf
