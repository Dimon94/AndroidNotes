
#Error:Execution failed for task ':app:transformResourcesWithMergeJavaResForDebug'.
>com.android.build.api.transform.TransformException: com.android.builder.packaging.DuplicateFileException: Duplicate files copied in APK META-INF/services/javax.annotation.processing.Processor
File1: C:\Users\*******\caches\modules-2\files-2.1\com.jakewharton\butterknife\7.0.1\d5d13ea991eab0252e3710e5df3d6a9d4b21d461\butterknife-7.0.1.jar
File2: C:\Users\*******\caches\modules-2\files-2.1\io.realm\realm-android\0.86.0\e49a43bcbbc3d5e5491dd2f4d3799a504b618e6b\realm-android-0.86.0.jar

```java
packagingOptions {
exclude 'META-INF/services/javax.annotation.processing.Processor' // butterknife
}

```
---
- 邮箱 ：[elisabethzhen@163.com](elisabethzhen@163.com)
- Good Luck!