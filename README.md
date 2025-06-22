# whatsapp_stickers_injector

A Flutter plugin for adding stickers to WhatsApp.


forked from [tomerof/whatsapp_stickers_injector](https://pub.dev/packages/whatsapp_stickers_injector) ,which is forked from [applicazza/whatsapp_stickers_plus](https://pub.dev/packages/whatsapp_stickers_plus)

fixed it to work with newer android versions and also be able to update an existing sticker pack.

## Notes

* ```trayImageFileName``` uses PNG data whereas stickers use WebP data.

## Usage

To use this plugin, add `whatsapp_stickers_injector` as a [dependency in your pubspec.yaml file](https://flutter.io/platform-plugins/):
```
whatsapp_stickers_injector:
    git:
      url: https://github.com/Equirinya/whatsapp_stickers_injector.git
```

### Android

Add the following option to your `app\build.gradle` file. This will prevent all WebP files from being compressed.

```
android {
    aaptOptions {
        noCompress "webp"
    }
}
```
also add the following to your AndroidManifest.xml inside the <manifest> block. Otherwise the plugin won't be able to discover Whatsapp.
```
<queries>
        <package android:name="com.whatsapp" />
        <package android:name="com.whatsapp.w4b" />
    </queries>
```

### iOS

Do not forget to add following entry to ```Info.plist``` with ```Runner``` target.

```xml
<key>LSApplicationQueriesSchemes</key>
 <array>
  <string>whatsapp</string>
 </array>
```

## Examples

### Local assets

Put your stickers in any folder, for example, `assets`. Do not forget to [add this folder to pubspec.yaml](https://flutter.dev/docs/development/ui/assets-and-images).

```dart
const stickers = {
  '01_Cuppy_smile.webp': ['☕', '🙂'],
  '02_Cuppy_lol.webp': ['😄', '😀'],
  '03_Cuppy_rofl.webp': ['😆', '😂'],
  '04_Cuppy_sad.webp': ['😃', '😍'],
  '05_Cuppy_cry.webp': ['😭', '💧'],
  '06_Cuppy_love.webp': ['😍', '♥'],
  '07_Cuppy_hate.webp': ['💔', '👎'],
  '08_Cuppy_lovewithmug.webp': ['😍', '💑'],
  '09_Cuppy_lovewithcookie.webp': ['😘', '🍪'],
  '10_Cuppy_hmm.webp': ['🤔', '😐'],
  '11_Cuppy_upset.webp': ['😱', '😵'],
  '12_Cuppy_angry.webp': ['😡', '😠'],
  '13_Cuppy_curious.webp': ['❓', '🤔'],
  '14_Cuppy_weird.webp': ['🌈', '😜'],
  '15_Cuppy_bluescreen.webp': ['💻', '😩'],
  '16_Cuppy_angry.webp': ['😡', '😤'],
  '17_Cuppy_tired.webp': ['😩', '😨'],
  '18_Cuppy_workhard.webp': ['😔', '😨'],
  '19_Cuppy_shine.webp': ['🎉', '✨'],
  '20_Cuppy_disgusting.webp': ['🤮', '👎'],
  '21_Cuppy_hi.webp': ['🖐', '🙋'],
  '22_Cuppy_bye.webp': ['🖐', '👋'],
};

Future installFromAssets() async {
  var stickerPack = WhatsappStickers(
    identifier: 'cuppyFlutterWhatsAppStickers',
    name: 'Cuppy Flutter WhatsApp Stickers',
    publisher: 'John Doe',
    trayImageFileName: WhatsappStickerImage.fromAsset('assets/tray_Cuppy.png'),
    publisherWebsite: '',
    privacyPolicyWebsite: '',
    licenseAgreementWebsite: '',
  );

  stickers.forEach((sticker, emojis) {
    stickerPack.addSticker(WhatsappStickerImage.fromAsset('assets/$sticker'), emojis);
  });

  try {
    await stickerPack.sendToWhatsApp();
  } on WhatsappStickersException catch (e) {
    print(e.cause);
  }
}

```

### Remote assets

```dart
const stickers = {
  '01_Cuppy_smile.webp': ['☕', '🙂'],
  '02_Cuppy_lol.webp': ['😄', '😀'],
  '03_Cuppy_rofl.webp': ['😆', '😂'],
  '04_Cuppy_sad.webp': ['😃', '😍'],
  '05_Cuppy_cry.webp': ['😭', '💧'],
  '06_Cuppy_love.webp': ['😍', '♥'],
  '07_Cuppy_hate.webp': ['💔', '👎'],
  '08_Cuppy_lovewithmug.webp': ['😍', '💑'],
  '09_Cuppy_lovewithcookie.webp': ['😘', '🍪'],
  '10_Cuppy_hmm.webp': ['🤔', '😐'],
  '11_Cuppy_upset.webp': ['😱', '😵'],
  '12_Cuppy_angry.webp': ['😡', '😠'],
  '13_Cuppy_curious.webp': ['❓', '🤔'],
  '14_Cuppy_weird.webp': ['🌈', '😜'],
  '15_Cuppy_bluescreen.webp': ['💻', '😩'],
  '16_Cuppy_angry.webp': ['😡', '😤'],
  '17_Cuppy_tired.webp': ['😩', '😨'],
  '18_Cuppy_workhard.webp': ['😔', '😨'],
  '19_Cuppy_shine.webp': ['🎉', '✨'],
  '20_Cuppy_disgusting.webp': ['🤮', '👎'],
  '21_Cuppy_hi.webp': ['🖐', '🙋'],
  '22_Cuppy_bye.webp': ['🖐', '👋'],
};

Future installFromRemote() async {
  var applicationDocumentsDirectory = await getApplicationDocumentsDirectory();
  var stickersDirectory = Directory('${applicationDocumentsDirectory.path}/stickers');
  await stickersDirectory.create(recursive: true);

  final dio = Dio();
  final downloads = <Future>[];

  stickers.forEach((sticker, emojis) {
    downloads.add(
      dio.download(
        'https://github.com/applicazza/whatsapp_stickers_plus/raw/master/example/assets/$sticker',
        '${stickersDirectory.path}/$sticker',
      ),
    );
  });

  await Future.wait(downloads);

  var stickerPack = WhatsappStickers(
    identifier: 'cuppyFlutterWhatsAppStickers',
    name: 'Cuppy Flutter WhatsApp Stickers',
    publisher: 'John Doe',
    trayImageFileName: WhatsappStickerImage.fromAsset('assets/tray_Cuppy.png'),
    publisherWebsite: '',
    privacyPolicyWebsite: '',
    licenseAgreementWebsite: '',
  );

  stickers.forEach((sticker, emojis) {
    stickerPack.addSticker(WhatsappStickerImage.fromFile('${stickersDirectory.path}/$sticker'), emojis);
  });

  try {
    await stickerPack.sendToWhatsApp();
  } on WhatsappStickersException catch (e) {
    print(e.cause);
  }
}

```
