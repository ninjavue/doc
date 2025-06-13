# Zirh kutubxonasini ishlatish qo‘llanmasi
Zirh kutubxonasidan foydalanishni boshlash uchun avvalo o'z loyihangizga
ushbu kutubxonani qo'shishingiz kerak. Buning uchun loyihangizdagi app
papkasining ichida yangi libs nomli papka yarating va unga .aar formatidagi
Zirh kutubxona faylini joylashtiring. Shu tariqa kutubxona
loyihangizga qo‘shilgan bo‘ladi.
#
settings.gradle.kts faylini oching, dependencyResolutionManagement bo'limidagi repositories qismiga libs papkasining joylashuvini
ko'rsatishingiz kerak bo'ladi. Bu orqali loyiha kutubxonalarni to'g'ri topadi. Masalan: 
```kotlin
  dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        flatDir{
            dirs("app/libs")
        }
    }
}
```
#
Bu kodni dasturingizga qo'shganingizdan keyin build.gradle.kts(app) faylni ichiga kutubxonani implementation qilib yozib qo'ying. Masalan:
```kotlin
  dependencies {
    implementation(":zirhlib-release@aar")
  }
```
#
Bu kodni yozib bo'lganingizdan keyin tafsiya etiladi asosiy Activity class massalan BaseActivity.java/.kt nomi bilan alohida umummiy Activitylar uchun ota(parent) class yaratib olishni bu orqali kod takrorlanish oldi olinadi. AndroidManifest.xml faylliga internet uchun ruxsat(permission) qo'shing.
```xml
<uses-permission android:name="android.permission.INTERNET" />
```

#
## 🚦 VPNni Aniqlash Funktsiyasi

Ushbu funksiya ilova ishga tushgan qurilmada **VPN ulanishi mavjud yoki yo‘qligini** aniqlash uchun ishlatiladi.  
Agar qurilmada faol VPN ulanishi mavjud bo‘lsa, funksiya `true` qiymat qaytaradi, aks holda `false`.

> 🔒 **VPN mavjudligi xavfsizlik talablariga zid bo‘lishi mumkin.**  
> Foydalanuvchiga ogohlantiruvchi xabar berish va ilovani ishlashini to‘xtatish yoki yopish tavsiya etiladi.

**Eslatma:** Funksiya ishlashi uchun internetga ruxsat kerak.

---

## 🧪 Emulyatorni Aniqlash Funktsiyasi

Ushbu funksiya ilova ishga tushgan qurilmaning **emulyator (soxta qurilma)** ekanligini aniqlash uchun mo‘ljallangan.  
Agar ilova real qurilmada ishlayotgan bo‘lsa, funksiya `false` qaytaradi, aks holda emulyator aniqlansa `true` qiymat qaytaradi.

> ⚠️ **Emulyatorda ishlayotgan ilova xavfsizlik jihatidan ishonchsiz hisoblanadi.**  
> Bu holatda ilova buzilishi, teskari tahlil (reverse engineering) qilinishi yoki yolg‘on ma’lumotlar bilan test qilinishi mumkin.

> ✅ **Eslatma:**  
> Agar emulyator aniqlansa, foydalanuvchiga ogohlantiruvchi xabar chiqaring va ilovani yopish uchun quyidagi funksiyalardan foydalaning:
>
> ```kotlin
> finishAffinity() 
> System.exit(0)  
> ```

---
## ⚠️ Rootni Aniqlash Funktsiyasi

Ushbu funksiya ilova ishga tushgan qurilmaning **root qilinganligini** aniqlash uchun ishlatiladi.  
Agar qurilma root qilingan bo‘lsa, funksiya `true` qiymat qaytaradi, aks holda `false`.

> 🔐 **Root qilingan qurilma xavfsizlik talablariga javob bermaydi.**  
> Bunday qurilmalarda foydalanuvchi yoki zararli dastur tizimga chuqur kirib, ilova ma’lumotlarini o‘zgartirishi yoki o‘g‘irlashi mumkin.

> ✅ **Eslatma:**  
> Agar qurilma root qilingan bo‘lsa (`true` qaytsa), foydalanuvchiga ogohlantiruvchi xabar chiqarish va ilovani darhol yopish kerak:
>
> ```kotlin
> Toast.makeText(context, "Root qilingan qurilmada ilova ishlamaydi", Toast.LENGTH_LONG).show()
> finishAffinity() 
> System.exit(0)  
> ```

---




