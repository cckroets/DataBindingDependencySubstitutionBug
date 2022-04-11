Demo of Data Binding with local vs remote dependency

1. `./gradlew clean`
2. `./gradlew :lib2:publishToMavenLocal`
3. `./gradlew :app:assembleDebug` Succeeds (uses local project dependency `project(':lib2')`)
4. `./gradlew :app:assembleDebug -PuseAar` Compilation fails (uses dependency `com.ckroetsch:lib2.1.0`)

Notice that running `clean :app:assembleDebug` and `clean :app:assembleDebug -PuseAar` will succeed independently. 
Just switching directly from using a local project dependency to using a remote dependency will fail the build 
due to databinding tasks being incorrectly up-to-date.

Relevant code is in `lib1/build.gradle`
```
afterEvaluate {
    configurations.all {
        resolutionStrategy.dependencySubstitution {
            if (project.hasProperty("useAar")) {
                substitute project(':lib2') using module('com.ckroetsch:lib2:1.0')
            }
        }
    }
}
```

Failure:
```
> Task :lib1:compileDebugJavaWithJavac FAILED
/.../DataBindingDependencySubstitutionBug/lib1/build/generated/ap_generated_sources/debug/out/com/ckroetsch/lib1/databinding/ConsumerBindingImpl.java:33: error: incompatible types: View cannot be converted to ProducerBinding
            , (android.view.View) bindings[1]
```
