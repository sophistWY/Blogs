---
title: Flutter 插件发布私服

date: 2022-2-13 11:31:43

tags:

  \- 方法总结

categories:

  \- Flutter

---



##  Flutter 插件发布私服

<!--more-->



#### 一.发布前检测

```shell
flutter pub publish --dry-run
```



#### 二.跳过谷歌验证

1. 下载项目：[https://github.com/ameryzhu/pub](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fameryzhu%2Fpub)

2. 打开终端 , cd 到项目根目录，执行

   ```dart
   flutter pub get
   ```

3. 根目录生成 mypub.dart.snapshot 文件

   ```dart
   dart --snapshot=mypub.dart.snapshot bin/pub.dart 
   ```

4. 复制之后放入${flutterSDK Path}/bin/cache/dart-sdk/bin/snapshots/ 目录下 

5. 用 VSCode 打开${flutterSDK Path}/bin/cache/dart-sdk/bin/pub文件

   将倒数第三行的：`pub.dart.snapshot` 替换为 `mypub.dart.snapshot`

   ```shell
   function follow_links() {
     file="$1"
     while [ -h "$file" ]; do
       # On Mac OS, readlink -f doesn't work.
       file="$(readlink "$file")"
     done
     echo "$file"
   }
   
   function array_contains() {
     local needle="$1"
     local element
     shift
     for element; do [ "$element" = "$needle" ] && return 0; done
     return 1
   }
   
   # Unlike $0, $BASH_SOURCE points to the absolute path of this file.
   PROG_NAME="$(follow_links "$BASH_SOURCE")"
   
   # Handle the case where dart-sdk/bin has been symlinked to.
   BIN_DIR="$(cd "${PROG_NAME%/*}" ; pwd -P)"
   
   
   unset VM_OPTIONS
   declare -a VM_OPTIONS
   
   # Allow extra VM options to be passed in through an environment variable.
   if [[ $DART_VM_OPTIONS ]]; then
     read -a OPTIONS <<< "$DART_VM_OPTIONS"
     VM_OPTIONS+=("${OPTIONS[@]}")
   fi
   
   # Run the pub snapshot.
   DART="$BIN_DIR/dart"
   if array_contains "--no-preview-dart-2" "${VM_OPTIONS[@]}"; then
     echo "Pub no longer supports Dart 1"
     exit -1
   else
     SNAPSHOT="$BIN_DIR/snapshots/mypub.dart.snapshot"
     exec "$DART" "${VM_OPTIONS[@]}" "$SNAPSHOT" "$@"
   fi
   ```



#### 三.发布

```dart
// 发布谷歌 Pub , https://pub.dev
flutter packages pub publish

// 发布私有仓库，127.0.0.1:8081 为仓库IP地址 http://120.55.90.16:10901
flutter packages pub publish --server=http://127.0.0.1:8081
flutter packages pub publish --server=http://120.55.90.16:10901

```



