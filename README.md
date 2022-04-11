Demo of Data Binding with local vs remote dependency

1. `./gradlew clean`
2. `./gradlew :lib2:publishToMavenLocal`
3. `./gradlew :app:assembleDebug` Succeeds (uses local project dependency `project(':lib2')`)
4. `./gradlew :app:assembleDebug -PuseAar` Fails (uses dependency `com.ckroetsch:lib2.1.0`)

Notice that running `clean :app:assembleDebug` and `clean :app:assembleDebug -PuseAar` will succeed independently. 
Just switching directly from using a local project dependency to using a remote dependency will fail the build 
due to databinding tasks being incorrectly up-to-date

```
Execution failed for task ':lib1:dataBindingMergeGenClassesDebug'.
> A failure occurred while executing com.android.build.gradle.internal.tasks.databinding.DataBindingMergeBaseClassLogRunnable
   > Failed to copy full contents from '/.../.gradle/caches/transforms-3/a4e6c90114c54a57c8011d04ede2330d/transformed/lib2-1.0/data-binding-base-class-log/com.ckroetsch.lib2-binding_classes.json' to '/.../AndroidStudioProjects/DataBindingDependencySubstitutionBug/lib1/build/intermediates/data_binding_base_class_logs_dependency_artifacts/debug/com.ckroetsch.lib2-binding_classes.json'
```
