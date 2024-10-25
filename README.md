# image_gallery_saver_plus


[![pub package](https://img.shields.io/pub/v/image_gallery_saver_plus.svg)](https://pub.dartlang.org/packages/image_gallery_saver_plus)
[![license](https://img.shields.io/github/license/mashape/apistatus.svg)](https://choosealicense.com/licenses/mit/)

We have developed an updated version of the `image_gallery_saver` plugin that allows users to download and save images and videos directly to their gallery, with enhanced performance, additional features for organizing media, and improved compatibility with various devices.

## Usage

To use this plugin, add `image_gallery_saver_plus` as a dependency in your pubspec.yaml file. For example:
```yaml
dependencies:
  image_gallery_saver_plus: '^3.0.5'
```

## iOS
Your project need create with swift.
Add the following keys to your Info.plist file, located in <project root>/ios/Runner/Info.plist:
  
 ``` 
<key>NSPhotoLibraryAddUsageDescription</key>
<string>We need permission to save photos and videos to your library for your convenience.</string>

<key>NSPhotoLibraryUsageDescription</key>
<string>We need permission to access your photo library to view and select your photos and videos.</string>
 ```	
 
 ##  Android
 You need to ask for storage permission to save an image to the gallery. You can handle the storage permission using [flutter_permission_handler](https://github.com/BaseflowIT/flutter-permission-handler).
 In Android version 10, Open the manifest file and add this line to your application tag
 ```
 <application android:requestLegacyExternalStorage="true" .....>
 ```

## Example
Saving an image from the internet, quality and name is option
``` dart
  _saveLocalImage() async {
    RenderRepaintBoundary boundary =
        _globalKey.currentContext!.findRenderObject() as RenderRepaintBoundary;
    ui.Image image = await boundary.toImage();
    ByteData? byteData =
        await (image.toByteData(format: ui.ImageByteFormat.png));
    if (byteData != null) {
      final result =
          await ImageGallerySaverPlus.saveImage(byteData.buffer.asUint8List());
      print(result);
    }
  }
  
  _saveNetworkImage() async {
    var response = await Dio().get(
        "https://ss0.baidu.com/94o3dSag_xI4khGko9WTAnF6hhy/image/h%3D300/sign=a62e824376d98d1069d40a31113eb807/838ba61ea8d3fd1fc9c7b6853a4e251f94ca5f46.jpg",
        options: Options(responseType: ResponseType.bytes));
    final result = await ImageGallerySaverPlus.saveImage(
        Uint8List.fromList(response.data),
        quality: 60,
        name: "hello");
    print(result);
  }
```

Saving file(ig: video/gif/others) from the internet
``` dart
  _saveNetworkGifFile() async {
    var appDocDir = await getTemporaryDirectory();
    String savePath = appDocDir.path + "/temp.gif";
    String fileUrl =
        "https://hyjdoc.oss-cn-beijing.aliyuncs.com/hyj-doc-flutter-demo-run.gif";
    await Dio().download(fileUrl, savePath);
    final result =
        await ImageGallerySaverPlus.saveFile(savePath, isReturnPathOfIOS: true);
    print(result);
  }

  _saveNetworkVideoFile() async {
    var appDocDir = await getTemporaryDirectory();
    String savePath = appDocDir.path + "/temp.mp4";
    String fileUrl =
        "https://s3.cn-north-1.amazonaws.com.cn/mtab.kezaihui.com/video/ForBiggerBlazes.mp4";
    await Dio().download(fileUrl, savePath, onReceiveProgress: (count, total) {
      print((count / total * 100).toStringAsFixed(0) + "%");
    });
    final result = await ImageGallerySaverPlus.saveFile(savePath);
    print(result);
  }
```
