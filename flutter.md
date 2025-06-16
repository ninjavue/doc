# Zirh-mobile kutubxonasini ishlatish bo'yicha yo'riqnoma
---
[![Android](https://img.shields.io/badge/Android-blue?style=for-the-badge)](https://github.com/ninjavue/doc/blob/main/README.md)
[![Flutter](https://img.shields.io/badge/Flutter-blue?style=for-the-badge)](https://github.com/ninjavue/doc/blob/main/flutter.md)

---
# Flutter

Zirh-mobile kutubxonasidan foydalanishni boshlash uchun uni o'z Flutter loyihangizga to'g'ri ulash lozim. Quyidagi bosqichlarni bajaring:

Flutter loyihamizni `android/app` papkani ichiga `libs` nomli kutubxona yaratib olamiz.

```
flutter_project/
├── android/
│   ├── app/
│   │   ├── libs/               
│   │   │  
│   │   ├── src/
│   │   └── build.gradle.kts
│   ├── build.gradle.kts
│   └── ...
├── lib/
├── pubspec.yaml
└── ...

```

Keyin esa libs papkani ichiga zirhlib-debug.aar kutubxonamizni joylashtirib olamiz
```
flutter_project/
├── android/
│   ├── app/
│   │   ├── libs/                            ← 📂 Bu yerga .aar fayl qo‘yiladi
│   │   │   └── zirh-mobil-lib-release.aar   ← 📦 JNI kutubxonani o‘z ichiga olgan .aar fayl
│   │   ├── src/
│   │   └── build.gradle.kts
│   └── ...
├── lib/
├── pubspec.yaml
└── ...
```
`settings.gradle.kts` faylida `flatDir` sozlamasini yozing
```
repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
        flatDir {
            dirs("app/libs")
        }
    }
```
app modulining build.gradle.kts faylida kutubxonani ulash
```
dependencies {
    implementation(files("libs/zirh-mobil-lib-release.aar"))
}
```
Eslatma:

Kutubxonaning nomi `.aar` fayl nomi bilan to'g'ri kelishi kerak `(zirh-mobil-lib-release.aar)`.

Yuqorida ko'rsatilgan quyidagi 2 faylni `assets/` ichiga joylashtiring:

```
app/
├── src/
│   └── main/
│       └── assets/
|           └── data.enc  ✅ Shifrlangan JSON
│           └── kalit.enc ✅ RSA bilan shifrlangan AES kalit
|            
```


`app/src/main/kotlin/.../MainActivity.kt` faylimizga `uz.zirh.zirhlib.ZirhMilliy` kutubxonani import qilib olamiz.
```dart
package <package nomi>

import io.flutter.embedding.android.FlutterActivity
import io.flutter.embedding.engine.FlutterEngine
import io.flutter.plugin.common.MethodChannel
...
import uz.zirh.zirhlib.ZirhMilliy
```
`MethodChannel`da kutubxona funksiyalaridan biridan foydalanish usuli.
```dart
class MainActivity : FlutterActivity() {

    private val CHANNEL = "com.example.zirh/root" /// com.exampe.zirh/root -> bu bizda kanal nomi yani kutubxonaga murojaat qilish uchun

    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)

        MethodChannel(flutterEngine.dartExecutor.binaryMessenger, CHANNEL).setMethodCallHandler { call, result ->
            when (call.method) {
                "rootlikkatekshirish" -> {
                    try {
                        val isRooted = ZirhMilliy().rootniAniqlash()
                        result.success(isRooted)
                    } catch (e: Exception) {
                        result.error("JNI_ERROR", "Failed to call native rootniAniqlash", e.message)
                    }
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
}
```

`app/src/main/kotlin/.../MainActivity.kt` faylimizning eng pastki qismiga quyidagicha kutubxonani yuklab olish funksiyasini kiritib qo'yamiz.

```dart
companion object {
        init {
            System.loadLibrary("mobil") // ← .so kutubxonangiz nomi (masalan, libzirh.so bo‘lsa "zirh" yoziladi)
        }
    }
```
Endi flutter uchun `bridge.dart` yaratib olamiz. bridge.dart orqali biz flutter loyihamizda istalgan fayl koddan turib foydalanish imkonini beradi.
```dart
import 'package:flutter/services.dart';

class ZirhMilliyNativeBridge {
  static const _channel = MethodChannel('com.example.zirh/root'); /// kanal nomi berilishi lozim

  static Future<bool> rootniAniqlash() async {
    try {
      final bool? result = await _channel.invokeMethod<bool>('rootlikkatekshirish');
      return result ?? false;
    } on PlatformException catch (e) {
      print('JNI method error: ${e.message}');
      return false;
    }
  }
}

```
