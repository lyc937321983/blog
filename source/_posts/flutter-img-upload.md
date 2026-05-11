---
title: Flutter 图片上传
date: 2026-02-02 16:52:40
tags: Flutter Image upload
comment: 'valine'
categories: 
- Flutter
---

# Flutter  图片上传

## 一、使用示例

```less
/// 没有默认数据
// var selectedModels = <AssetUploadModel>[];

/// 有默认数据
var selectedModels = [  "https://yl-prescription-share.oss-cn-beijing.aliyuncs.com/beta/Health_APP/20230825/fb013ec6b90a4c5bb1059b003dada9ee.jpg",  "https://yl-prescription-share.oss-cn-beijing.aliyuncs.com/beta/Health_APP/20230825/ce326143c5b84fd9b99ffca943353b05.jpg",].map((e) => AssetUploadModel(url: e, entity: null)).toList();

/// 获取图片链接数组
List<String> urls = [];
buildBody() {
  return SingleChildScrollView(
    child: Column(
      mainAxisSize: MainAxisSize.min,
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        NText(data: "AssetUploadBox", fontSize: 16,),
        Container(
          padding: EdgeInsets.symmetric(horizontal: 20),
          child: AssetUploadBox(
            maxCount: 8,
            // rowCount: 4,
            items: selectedModels,
            // canEdit: false,
            // showFileSize: true,
            onChanged: (items){
              debugPrint("onChanged items.length: ${items.length}");
              selectedModels = items.where((e) => e.url?.startsWith("http") == true).toList();
              urls = selectedModels.map((e) => e.url ?? "").toList();
              setState(() {});
            },
          ),
        ),
      ],
    ),
  );
}
```

## 二、源码

```dart
/// 图片实体模型
class AssetUploadModel {

  AssetUploadModel({
    required this.entity,
    this.url,
    this.file,
  });

  final AssetEntity? entity;
  /// 上传之后的文件 url
  String? url;
  /// 压缩之后的文件
  File? file;
}
```

#### 1、AssetUploadBox 源码，整个图片区域

```php
import 'dart:ffi';

import 'package:dio/dio.dart';
import 'package:flutter/material.dart';
import 'package:flutter_screenutil/flutter_screenutil.dart';
import 'package:flutter_templet_project/basicWidget/n_image_preview.dart';
import 'package:flutter_templet_project/basicWidget/upload/asset_upload_button.dart';
import 'package:flutter_templet_project/basicWidget/upload/asset_upload_config.dart';
import 'package:flutter_templet_project/basicWidget/upload/asset_upload_model.dart';
import 'package:flutter_templet_project/extension/overlay_ext.dart';
import 'package:flutter_templet_project/extension/widget_ext.dart';
import 'package:wechat_assets_picker/wechat_assets_picker.dart';
import 'package:wechat_camera_picker/wechat_camera_picker.dart';


/// 上传图片单元(基于 wechat_assets_picker)
class AssetUploadBox extends StatefulWidget {

  AssetUploadBox({
    Key? key,
    required this.items,
    required this.onChanged,
    this.maxCount = 9,
    this.rowCount = 4,
    this.spacing = 3,
    this.runSpacing = 3,
    this.canCameraTakePhoto = false,
    this.canEdit = true,
    this.imgBuilder,
    this.onTap,
    this.showFileSize = false,
  }) : super(key: key);


  List<AssetUploadModel> items;
  /// 全部结束(有成功有失败 url="")或者删除完失败图片时会回调
  ValueChanged<List<AssetUploadModel>> onChanged;
  /// 做大个数
  int maxCount;
  /// 每行个数
  int rowCount;
  /// 水平间距
  double spacing;
  /// 垂直间距
  double runSpacing;
  /// 可以 拍摄图片
  bool canCameraTakePhoto;
  /// 可以编辑
  bool canEdit;
  /// 网络图片url转为组件
  Widget Function(String url)? imgBuilder;
  /// 图片点击事件
  Void Function(List<String> urls, int index)? onTap;
  /// 显示文件大小
  bool showFileSize;

  @override
  _AssetUploadBoxState createState() => _AssetUploadBoxState();
}

class _AssetUploadBoxState extends State<AssetUploadBox> {

  late List<AssetUploadModel> selectedModels = widget.items;
  // List<AssetEntity> selectedEntitys = [];

  @override
  void initState() {
    // TODO: implement initState
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return photoSection(
      items: selectedModels,
      maxCount: widget.maxCount,
      rowCount: widget.rowCount,
      spacing: widget.spacing,
      runSpacing: widget.runSpacing,
      canEdit: widget.canEdit,
    );
  }

  photoSection({
    List<AssetUploadModel> items = const [],
    int maxCount = 9,
    int rowCount = 4,
    double spacing = 10,
    double runSpacing = 10,
    bool canEdit = true,
  }) {
    return LayoutBuilder(
      builder: (BuildContext context, BoxConstraints constraints){
        var itemWidth = ((constraints.maxWidth - spacing * (rowCount - 1))/rowCount).truncateToDouble();
        // print("itemWidth: $itemWidth");
        return Wrap(
          spacing: spacing,
          runSpacing: runSpacing,
          alignment: WrapAlignment.start,
          children: [
            ...items.map((e) {
              // final size = await e.length()/(1024*1024);

              final index = items.indexOf(e);

              return Container(
                child: Column(
                  children: [
                    ClipRRect(
                      borderRadius: BorderRadius.all(Radius.circular(8)),
                      child: SizedBox(
                        width: itemWidth,
                        height: itemWidth,
                        child: InkWell(
                          onTap: (){
                            // debugPrint("onTap: ${e.url}");
                            final urls = items.where((e) => e.url?.startsWith("http") == true)
                              .map((e) => e.url ?? "").toList();
                            final index = urls.indexOf(e.url ?? "");
                            // debugPrint("urls: ${urls.length}, $index");
                            FocusScope.of(context).unfocus();

                            if (widget.onTap != null) {
                              widget.onTap?.call(urls, index);
                              return;
                            }
                            showEntry(
                              child: NImagePreview(
                                urls: urls,
                                index: index,
                                onBack: (){
                                  hideEntry();
                                },
                              ),
                            );
                          },
                          child: AssetUploadButton(
                            model: e,
                            urlBlock: (url){
                              // e.url = url;
                              // debugPrint("e: ${e.data?.name}_${e.url}");
                              final isAllFinished = items.where((e) =>
                              e.url == null).isEmpty;
                              // debugPrint("isAllFinsied: ${isAllFinsied}");
                              if (isAllFinished) {
                                final urls = items.map((e) => e.url).toList();
                                debugPrint("isAllFinsied urls: ${urls}");
                                widget.onChanged(items);
                              }
                            },
                            onDelete: canEdit == false ? null : (){
                              debugPrint("onDelete: $index, lenth: ${items[index].file?.path}");
                              items.remove(e);
                              setState(() {});
                              widget.onChanged(items);
                            },
                            showFileSize: widget.showFileSize,
                          ),
                        ),
                      ),
                    ),
                  ],
                ),
              );
            }).toList(),
            if (items.length < maxCount && canEdit)
              InkWell(
                onTap: () {
                  onPicker(maxCount: maxCount);
                },
                child: Container(
                  margin: EdgeInsets.only(top: 10, right: 10),
                  width: itemWidth - 10,
                  height: itemWidth - 10,
                  decoration: BoxDecoration(
                    color: Colors.black.withOpacity(0.1),
                    // border: Border.all(width: 1),
                    borderRadius: BorderRadius.all(Radius.circular(4)),
                  ),
                  // child: Icon(Icons.camera_alt, color: Colors.black12,),
                  child: Center(
                    child: Image(
                      image: AssetImage("assets/images/icon_camera.png"),
                      width: 24.w,
                      height: 24.w,
                    ),
                  ),
                ),
              )
          ]
        );
      }
    );
  }

  onPicker({
    int maxCount = 4,
    // required Function(int length, String result) cb,
  }) async {
    try {
      final tmpUrls = selectedModels.map((e) => e.url).where((e) => e != null).toList();
      final tmpEntitys = selectedModels.map((e) => e.entity).where((e) => e != null).toList();
      final selectedEntitys = List<AssetEntity>.from(tmpEntitys);

      final result = await AssetPicker.pickAssets(
        context,
        pickerConfig: AssetPickerConfig(
          requestType: RequestType.image,
          specialPickerType: SpecialPickerType.noPreview,
          selectedAssets: selectedEntitys,
          maxAssets: maxCount - tmpUrls.length,
          specialItemPosition: SpecialItemPosition.prepend,
          specialItemBuilder: (context, AssetPathEntity? path, int length,) {
            if (path?.isAll != true) {
              return null;
            }
            if (!widget.canCameraTakePhoto) {
              return null;
            }

            const textDelegate = AssetPickerTextDelegate();
            return Semantics(
              label: textDelegate.sActionUseCameraHint,
              button: true,
              onTapHint: textDelegate.sActionUseCameraHint,
              child: GestureDetector(
                behavior: HitTestBehavior.opaque,
                onTap: () async {
                  Feedback.forTap(context);
                  final takePhoto = await CameraPicker.pickFromCamera(
                    context,
                    pickerConfig: const CameraPickerConfig(enableRecording: true),
                  );
                  if (takePhoto != null) {
                    selectedModels.add(AssetUploadModel(entity: takePhoto));
                    debugPrint("selectedEntitys:${selectedEntitys.length} ${selectedModels.length}");
                    setState(() {});
                  }
                },
                child: const Center(
                  child: Icon(Icons.camera_enhance, size: 42.0),
                ),
              ),
            );
          },
        ),
      ) ?? [];

      // BrunoUtil.showLoading("图片处理中...");
      final same = result.map((e) => e.id).join() == selectedEntitys.map((e) => e.id).join();
      if (result.isEmpty || same) {
        debugPrint("没有添加新图片");
        return;
      }

      for (final e in result) {
        if (!selectedEntitys.contains(e)) {
          selectedModels.add(AssetUploadModel(entity: e));
        }
      }
      debugPrint("selectedEntitys:${selectedEntitys.length} ${selectedModels.length}");
      setState(() {});
    } catch (err) {
      debugPrint("err:$err");
      // BrunoUtil.showToast('$err');
      showToast(message: '$err');
    }
  }

  showToast({required String message}) {
    Text(message).toShowCupertinoDialog(context: context);
  }
}
```

#### 2、AssetUploadButton 源码，单个图片组件

```php
import 'dart:io';

import 'package:dio/dio.dart';
import 'package:flutter/material.dart';
import 'package:flutter_screenutil/flutter_screenutil.dart';
import 'package:flutter_templet_project/basicWidget/n_network_image.dart';
import 'package:flutter_templet_project/basicWidget/n_text.dart';
import 'package:flutter_templet_project/basicWidget/upload/asset_upload_config.dart';
import 'package:flutter_templet_project/basicWidget/upload/asset_upload_model.dart';
import 'package:flutter_templet_project/basicWidget/upload/image_service.dart';
import 'package:flutter_templet_project/extension/num_ext.dart';
import 'package:flutter_templet_project/extension/string_ext.dart';


/// 上传图片单元(基于 wechat_assets_picker)
class AssetUploadButton extends StatefulWidget {

  AssetUploadButton({
    Key? key,
    required this.model,
    this.urlBlock,
    this.onDelete,
    this.radius = 8,
    this.imgBuilder,
    this.urlConvert,
    // this.isFinished = false,
    this.showFileSize = false,
  }) : super(key: key);

  final AssetUploadModel model;

  /// 上传成功获取 url 回调
  final ValueChanged<String>? urlBlock;
  /// 返回删除元素的 id
  final VoidCallback? onDelete;
  /// 圆角 默认8
  final double radius;

  /// 网络图片url转为组件
  Widget Function(String url)? imgBuilder;

  /// 上传网络返回值转为 url
  String Function(Map<String, dynamic> res)? urlConvert;

  /// 显示文件大小
  final bool showFileSize;

  // bool isFinished;

  @override
  _AssetUploadButtonState createState() => _AssetUploadButtonState();
}

class _AssetUploadButtonState extends State<AssetUploadButton> with AutomaticKeepAliveClientMixin {
  /// 防止触发多次上传动作
  var _isLoading = false;
  /// 请求成功或失败
  final _successVN = ValueNotifier(true);
  /// 上传进度
  final _percentVN = ValueNotifier(0.0);


  @override
  void initState() {
    super.initState();

    onRefresh();
  }

  @override
  void didUpdateWidget(covariant AssetUploadButton oldWidget) {
    // TODO: implement didUpdateWidget
    super.didUpdateWidget(oldWidget);
    if (widget.model.entity?.id == oldWidget.model.entity?.id) {
      // BrunoUtil.showInfoToast("path相同");
      return;
    }
    // onRefresh();
  }

  @override
  Widget build(BuildContext context) {
    super.build(context);

    Widget img = Image(image: "img_placehorder.png".toAssetImage());
    if (widget.model.url?.startsWith("http") == true) {
      final imgUrl = widget.model.url ?? "";
      img = widget.imgBuilder?.call(imgUrl) ?? NNetworkImage(url: imgUrl);
    }

    if (widget.model.file != null) {
      img = Image.file(
        File(widget.model.file?.path ?? ""),
        fit: BoxFit.cover,
      );
    }

    var imgChild = ClipRRect(
      borderRadius: BorderRadius.all(Radius.circular(widget.radius)),
      child: Padding(
        padding: EdgeInsets.only(top: 0, right: 0),
        child: img,
      ),
    );
    
    return Stack(
      fit: StackFit.expand,
      children: [
        Padding(
          padding: EdgeInsets.only(top: 10, right: 10),
          child: imgChild,
        )
            // .toColoredBox()
        ,
        if(widget.model.url == null || widget.model.url == "")Positioned(
          top: 0,
          right: 0,
          bottom: 0,
          left: 0,
          child: buildUploading(),
        ),
        if (widget.showFileSize) Positioned(
          top: 0,
          right: 0,
          bottom: 0,
          left: 0,
          child: buildFileSizeInfo(length: widget.model.file?.lengthSync(),),
        ),
        Positioned(
          top: 0,
          right: 0,
          child: buildDelete(),
        ),
      ],
    );
  }

  /// 右上角删除按钮
  Widget buildDelete() {
    if (widget.onDelete == null) {
      return SizedBox();
    }
    return Container(
      decoration: const BoxDecoration(
        color: Colors.white,
        shape: BoxShape.circle,
      ),
      child: IconButton(
        padding: EdgeInsets.zero,
        constraints: BoxConstraints(),
        onPressed: widget.onDelete,
        icon: const Icon(Icons.cancel, color: Colors.red,),
      ),
    );
  }

  Widget buildUploading() {
    return AnimatedBuilder(
      animation: Listenable.merge([
        _successVN,
        _percentVN,
      ]),
      builder: (context, child) {
        if (_successVN.value == false) {
          return buildUploadFail();
        }
        final value = _percentVN.value;
        if (value >= 1) {
          return const SizedBox();
        }
        return Container(
          alignment: Alignment.center,
          margin: const EdgeInsets.only(top: 10, right: 10),
          decoration: BoxDecoration(
            color: Colors.black45,
            borderRadius: BorderRadius.all(Radius.circular(widget.radius)),
          ),
          child: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              // if (value <= 0)NText(
              //   data: "处理中",
              //   fontSize: 14,
              //   color: Colors.white,
              // ),
              if(widget.model.file != null && (widget.model.file!.lengthSync() > 2 * 1024 * 1024) == true)NText(
                data: value.toStringAsPercent(2),
                fontSize: 12.sp,
                color: Colors.white,
              ),
              NText(
                data: "上传中",
                fontSize: 12.sp,
                color: Colors.white,
              ),
            ],
          ),
        );
      }
    );
  }

  Widget buildUploadFail() {
    return Stack(
      children: [
        InkWell(
          onTap: (){
            debugPrint("onTap");
            onRefresh();
          },
          child: Container(
            alignment: Alignment.center,
            margin: EdgeInsets.only(top: 10, right: 10),
            decoration: BoxDecoration(
              color: Colors.black45,
              borderRadius: BorderRadius.all(Radius.circular(widget.radius)),
            ),
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                Icon(Icons.refresh, color: Colors.red),
                NText(
                  data: "点击重试",
                  fontSize: 14,
                  color: Colors.white,
                ),
              ],
            ),
          ),
        ),
        Positioned(
          top: 0,
          right: 0,
          child: buildDelete(),
        ),
      ],
    );
  }

  Future<String?> uploadFile({
    required String filePath,
    ProgressCallback? onSendProgress,
    ProgressCallback? onReceiveProgress,
    String Function(Map<String, dynamic> res)? urlConvert,
  }) async {
    final url = AssetUploadConfig.uploadUrl;
    assert(url.startsWith("http"), "请设置上传地址");

    final formData = FormData.fromMap({
      'files': await MultipartFile.fromFile(filePath),
    });
    final response = await Dio().post<Map<String, dynamic>>(
      url,
      data: formData,
      onSendProgress: onSendProgress,
      onReceiveProgress: onReceiveProgress,
    );
    final res = response.data ?? {};
    final result = urlConvert?.call(res) ?? res['result'];
    return result;
  }
  
  onRefresh() {
    // debugPrint("onRefresh ${widget.entity}");
    final entityFile = widget.model.entity?.file;
    if (entityFile == null) {
      return;
    }

    if (_isLoading) {
      debugPrint("_isLoading: $_isLoading ${widget.model.entity}");
      return;
    }
    _isLoading = true;
    _successVN.value = true;

    entityFile.then((file) {
      if (file == null) {
        throw "文件为空";
      }
      return ImageService().compressAndGetFile(file);
    }).then((file) {
      if (file == null) {
        throw "文件为空";
      }
      widget.model.file = file;
      setState(() {});

      final path = widget.model.file?.path;
      if (path == null) {
        throw "文件路径为空";
      }
      // return "";//调试代码,勿删!!!
      return uploadFile(
        filePath: path,
        onSendProgress: (int count, int total){
          _percentVN.value = (count/total);
          // debugPrint("${count}/${total}_${_percentVN.value}_${_percentVN.value.toStringAsPercent(2)}");
        },
        urlConvert: widget.urlConvert,
      );
    }).then((value) {
      final url = value;
      if (url == null || url.isEmpty) {
        _successVN.value = false;
        throw "上传失败 ${widget.model.file?.path}";
      }
      _successVN.value = true;
      widget.model.url = url;
    }).catchError((err){
      debugPrint("err: $err");
      widget.model.url = "";
      _successVN.value = false;
    }).whenComplete(() {
      _isLoading = false;
      widget.urlBlock?.call(widget.model.url ?? "");
    });
  }

  Widget buildFileSizeInfo({required int? length}) {
    if (length == null) {
      return SizedBox();
    }
    final result = length/(1024 *1024);
    final desc = "${result.toStringAsFixed(2)}MB";
    return Align(
      child: Container(
        color: Colors.red,
        child: Text(desc)
      )
    );
  }

  
  @override
  bool get wantKeepAlive => true;
}
```

#### 3、ImageService 源码，图片处理工具类

```dart
import 'dart:io';

import 'package:flutter/cupertino.dart';
import 'package:flutter_image_compress/flutter_image_compress.dart';
import 'package:flutter_templet_project/cache/cache_asset_service.dart';
import 'package:flutter_templet_project/extension/num_ext.dart';



/// 图片处理工具类
class ImageService{

  /// 图片压缩
   Future<File?> compressAndGetFile(File file, [String? targetPath]) async {
    try {
      var fileName = file.absolute.path.split('/').last;

      // Directory tempDir = await getTemporaryDirectory();
      // Directory assetDir = Directory('${tempDir.path}/asset');
      // if (!assetDir.existsSync()) {
      //   assetDir.createSync();
      //   debugPrint('assetDir 文件保存路径为 ${assetDir.path}');
      // }

      Directory? assetDir = await CacheAssetService().getDir();
      var tmpPath = '${assetDir.path}/$fileName';
      targetPath ??= tmpPath;
      // debugPrint('fileName_${fileName}');
      // debugPrint('assetDir_${assetDir}');
      // debugPrint('targetPath_${targetPath}');

      final compressQuality = file.lengthSync().compressQuality;

      var result = await FlutterImageCompress.compressAndGetFile(
        file.absolute.path, targetPath,
        quality: compressQuality,
        rotate: 0,
      );
      final path = result?.path;
      if (result == null || path == null || path.isEmpty) {
        debugPrint("压缩文件路径获取失败");
        return file;
      }
      final lenth = await result.length();

      final infos = [
        "图片名称: $fileName",
        "压缩前: ${file.lengthSync().fileSize}",
        "压缩质量: $compressQuality",
        "压缩后: ${lenth.fileSize}",
        "原路径: ${file.absolute.path}",
        "压缩路径: $targetPath",
      ];
      debugPrint("图片压缩: ${infos.join("\n")}");

      return File(path);
    } catch (e) {
      debugPrint("compressAndGetFile:${e.toString()}");
    }
    return null;
  }

  /// 图片压缩
  Future<String> compressAndGetFilePath(String imagePath, [String? targetPath,]) async {
    try {
      final file = File(imagePath);
      final fileNew = await compressAndGetFile(file, targetPath);
      final result = fileNew?.path ?? imagePath;
      return result;
    } catch (e) {
      debugPrint("compressAndGetFilePath:${e.toString()}");
    }
    return imagePath;
  }

}
```

#### 4、CacheAssetService 源码，媒体缓存工具类

```dart
import 'dart:async';
import 'dart:io';

import 'package:flutter/foundation.dart';
import 'package:path_provider/path_provider.dart';

///缓存媒体文件
class CacheAssetService {
  CacheAssetService._();

  static final CacheAssetService _instance = CacheAssetService._();

  factory CacheAssetService() => _instance;


  Directory? _dir;

  Future<Directory> getDir() async {
    if (_dir != null) {
      return _dir!;
    }
    Directory tempDir = await getTemporaryDirectory();
    Directory targetDir = Directory('${tempDir.path}/asset');
    if (!targetDir.existsSync()) {
      targetDir.createSync();
      debugPrint('targetDir 路径为 ${targetDir.path}');
    }
    _dir = targetDir;
    return targetDir;
  }

  /// 清除缓存文件
  Future<void> clearDirCache() async {
    final dir = await getDir();
    await deleteDirectory(dir);
  }

  /// 递归方式删除目录
  Future<void> deleteDirectory(FileSystemEntity? file) async {
    if (file == null) {
      return;
    }

    if (file is Directory) {
      final List<FileSystemEntity> children = file.listSync();
      for (final FileSystemEntity child in children) {
        await deleteDirectory(child);
      }
    }
    await file.delete();
  }

}
```

## 总结

##### 1、为了效果和微信保持一致；图片选择库用的是 wechat_assets_picker，但是该组件获取的 AssetEntity 模型的 file 是异步方法

```dart
Future<File?> get file => _getFile();
```

只能将其下沉到 AssetUploadButton 中进行请求，否则 file 获取时间时间会比较漫长，非常影响用户体验。上传之前的灰色显示时间就是获取 file 时间。

##### 2、AssetEntity 的 file 有时会比原始图片大很多（用iPhone14模拟器测试，一张 11 m的图片，file 是 37M，originFile 是 11M）,问过 wechat_assets_picker 库作者 alex，以实际获为准；

##### 3、因为图片过大所以添加图片压缩功能；根据多年经验，根据图片体积，压缩系数实现阶梯化排布；20m 以上的图片基本都可以压缩在 2m 以下，也没有发现图片压缩后模糊的问题（大家可以根据实际情况进行调整）；

```dart
extension IntFileExt on int{
  /// length 转为 MB 描述
  String get fileSize {
    final result = this/(1024 *1024);
    final desc = "${result.toStringAsFixed(2)}MB";
    return desc;
  }

  /// 压缩质量( )
  int get compressQuality {
    int length = this;
    // var quality = 100;
    const mb = 1024 * 1024;
    if (length > 10 * mb) {
      return 20;
    }

    if (length > 8 * mb) {
      return 30;
    }

    if (length > 6 * mb) {
      return 40;
    }

    if (length > 4 * mb) {
      return 50;
    }

    if (length > 2 * mb) {
      return 60;
    }
    return 90;
  }
}
```

##### 4、图片压缩采用的是 flutter_image_compress 库的

```ini
final fileNew = await compressAndGetFile(file, targetPath);
```

文件压缩方法，从原始文件生成一个新的目标文件；临时文件多了，缓存会变大，所有需要清理缓存，所以就有了 CacheAssetService 工具类，负责临时文件清理功能；

##### 5、wechat_assets_picker 目前版本的UI在 iOS 和 andriod 手机上是不一致的；fork 了一份进行了修改：[wechat_assets_picker 双端 UI 一致版](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fshang1219178163%2Fflutter_wechat_assets_picker)

##### 6、本来想封装成 pub 库的，但是涉及的库太多了，担心大家使用时有各种版本冲突的问题，就直接分享源码了；

```涉及第三方库
// 图片选择
wechat_assets_picker

// 图片压缩
flutter_image_compress

// 负责缓存文件清理
path_provider

// 图片上传
dio

// 网络图片显示
extended_image

// 网络图片预览
photo_view

// 网络图片保存到相册
image_gallery_saver
```

[github](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fshang1219178163%2Fflutter_templet_project%2Fblob%2Fmain%2Flib%2FbasicWidget%2Fupload%2Fasset_upload_box_demo.dart)