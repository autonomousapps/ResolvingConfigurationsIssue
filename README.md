# ResolvingConfigurationsIssue
## Observed
You can resolve the configurations and artifacts of the Java project without issue.
But when attempting to do so (using the same code) in an Android project, it fails at configuration time.
Repro steps below.

## Expected
You can resolve the configurations and artifacts of the Android project without issue (and it should happen during task execution).

## To reproduce
First, observe that it works in the Java project:
1. `cd java`
1. `./gradlew app:run`

Second, observe that it fails in the Android project:
1. `cd android`
1. `./gradlew app:assembleDebug`

The output looks like this:

```
FAILURE: Build failed with an exception.

* Where:
Build file '/workspace/ResolvingDependenciesTest/android/app/build.gradle' line: 41

* What went wrong:
Could not determine the dependencies of task ':app:compileDebugJavaWithJavac'.
> Could not resolve all artifacts for configuration ':app:debugRuntimeClasspath'.
   > More than one variant of project :lib1 matches the consumer attributes:
       - Configuration ':lib1:debugRuntimeElements' variant android-assets:
           - Unmatched attributes:
               - Found artifactType 'android-assets' but wasn't required.
               - Found com.android.build.api.attributes.VariantAttr 'debug' but wasn't required.
           - Compatible attributes:
               - Required com.android.build.api.attributes.BuildTypeAttr 'debug' and found compatible value 'debug'.
               - Required com.android.build.gradle.internal.dependency.AndroidTypeAttr 'Aar' and found compatible value 'Aar'.
               - Required org.gradle.usage 'java-runtime' and found compatible value 'java-runtime'.
               - Required org.jetbrains.kotlin.platform.type 'androidJvm' and found compatible value 'androidJvm'.
...etc...
```

Something about the Android project is:
1. Resolving this configuration early (during conguration instead of execution) and
2. Causing the resolution to fail due to some variant-aware ambiguity (perhaps as a consequence of the first point).
