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
---
## 🔐 Ma'lumotlarni Shifrlash

### ✨ Nima uchun shifrlash zarur?

Ilovada ishlatiladigan ba'zi muhim ma'lumotlar — masalan, **backend server URL’lari**, **directory manzillar**, **tokenlar**, **hash qiymatlar**, va **ilovaning imzo (signature) ma'lumotlari** — maxfiy va xavfsizlik talablariga javob beruvchi shaklda saqlanishi kerak.

Agar bu ma’lumotlar shifrlanmagan holda apk ichida yoki fayl tizimida saqlansa, ular tahlil qilinib (reverse engineering), ilovaga hujum qilish, soxta so‘rov yuborish yoki serverdan noto‘g‘ri foydalanish uchun ishlatilishi mumkin.

Shuning uchun **shifrlash yordamida bu ma’lumotlarni himoyalash** va ularni faqat kerakli paytda, kerakli joyda yechib olish (deshifrovka qilish) lozim bo‘ladi.

#

### 🔒 Shifrlanadigan ma’lumotlar
- 🌐 Backend URL manzillari  
- 📁 Server path / directory strukturalari  
- 🔑 API tokenlar, maxfiy kalitlar  
- 🧮 Hashlangan qiymatlar (masalan, SHA256)  
- 🖋 Ilovaning imzo sertifikati (signature)  

#

### 📌 Shifrlash va foydalanish ketma-ketligi
Quyidagi bosqichlar orqali ilova uchun maxfiy ma’lumotlarni shifrlash va undan xavfsiz foydalanish mumkin.
### 1. 📄 JSON fayl tayyorlash

Birinchi bosqichda maxfiy ma'lumotlar `data.json` faylga quyidagi formatda yoziladi:

```json
{
    "domainlar": ["https://jsonplaceholder.typicode.com", "https://httpbin.org"],
    "havolalar": ["posts", "post"],
    "hashlar": ["sha256///UzJAZYxLBnEpBwXAcmd4WHi7f8aYgfMExGnoyp5B04=", "sha256//IFG+z/oQKXfpUYOHgWHy5axgkT9B01XSxwb2AHDyN34="],
    "tokenlar": ["abc123", "def456", "ghi789"],
    "imzo": "B83BC82F4E631114B79E5E01DB8590CAF76D3384B2CFCFEE27F31B0C886262B1"
}
```
---


## 📁 `faylManzili()` Funksiyasi

Ushbu funksiya kutubxonaga **`assets` papkasidagi faylga kirish imkonini beradi**.  
Funksiyani chaqirish orqali kutubxona ichida joylashgan faylga to‘g‘ridan-to‘g‘ri kirish va uni o‘qish imkoniyati paydo bo‘ladi.

> 📌 **Eslatma:** Faylga kirish uchun Android ilovasidagi `assets` obyektini funksiya ichiga uzatishingiz kerak.

#

### 💻 Foydalanish namunasi

Quyidagi kod `BaseActivity` klassida `onCreate()` ichida `faylManzili()` funksiyasini qanday chaqirishni ko‘rsatadi:

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    
    // assets papkasiga kirish uchun kutubxonaga kontekst beriladi
    ZirhMilliy.faylManzili(assets)
}
```

#

### 📂 Qayerdagi faylni o‘qiydi?

`assets/` papkasida joylashgan fayllarni o‘qish uchun mo‘ljallangan.  
Masalan:

```
app/
├── src/
│   └── main/
│       └── assets/
│           └── config.json
```

Yuqoridagi holatda `faylManzili()` yordamida `config.json` fayl o‘qilishi mumkin.

---
## 🚦 `vpnniAniqlash()` Funktsiyasi

Ushbu funksiya ilova ishga tushgan qurilmada **VPN ulanishi mavjud yoki yo‘qligini** aniqlash uchun ishlatiladi.  
Agar qurilmada faol VPN ulanishi mavjud bo‘lsa, funksiya `true` qiymat qaytaradi, aks holda `false`.

> 🔒 **VPN mavjudligi xavfsizlik talablariga zid bo‘lishi mumkin.**  
> Foydalanuvchiga ogohlantiruvchi xabar berish va ilovani ishlashini to‘xtatish yoki yopish tavsiya etiladi.

**Eslatma:** Funksiya ishlashi uchun internetga ruxsat kerak.
#

### 💻 Foydalanish namunasi

Quyidagi kod `BaseActivity` klassida `onResume()` ichida `vpnniAniqlash()` funksiyasini qanday chaqirishni ko‘rsatadi:

```kotlin
  override fun onResume() {
        super.onResume()
        val isVpn = lib.vpnniAniqlash()
        if (isVpn) {
            Toast.makeText(this, "Sizning qurulmangizda vpn aniqlandi", Toast.LENGTH_LONG).show()
            finishAffinity()
            System.exit(0)
        }
    }
```
---

## 🧪 `emulyatorniAniqlash()` Funktsiyasi

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
#

### 💻 Foydalanish namunasi

Quyidagi kod `BaseActivity` klassida `onResume()` ichida `emulyatorniAniqlash()` funksiyasini qanday chaqirishni ko‘rsatadi:

```kotlin
 override fun onResume() {
        super.onResume()
        val isEmulyator = lib.emulyatorniAniqlash(this)
        if (isEmulyator) {
            Toast.makeText(this, "Ilova emulyatorda ishga tushdi", Toast.LENGTH_LONG).show()
            finishAffinity()
            System.exit(0)
        }
    }
```
---
## ⚠️ `rootniAniqlash()` Funktsiyasi

Ushbu funksiya ilova ishga tushgan qurilmaning **root qilinganligini** aniqlash uchun ishlatiladi.  
Agar qurilma root qilingan bo‘lsa, funksiya `true` qiymat qaytaradi, aks holda `false`.

> 🔐 **Root qilingan qurilma xavfsizlik talablariga javob bermaydi.**  
> Bunday qurilmalarda foydalanuvchi yoki zararli dastur tizimga chuqur kirib, ilova ma’lumotlarini o‘zgartirishi yoki o‘g‘irlashi mumkin.

> ✅ **Eslatma:**  
> Agar qurilma root qilingan bo‘lsa (`true` qaytsa), foydalanuvchiga ogohlantiruvchi xabar chiqarish va ilovani darhol yopish kerak:
>
> ```kotlin
> finishAffinity() 
> System.exit(0)  
> ```
#

### 💻 Foydalanish namunasi

Quyidagi kod `BaseActivity` klassida `onResume()` ichida `rootniAniqlash()` funksiyasini qanday chaqirishni ko‘rsatadi:

```kotlin
 override fun onResume() {
        super.onResume()
        val isRoot = lib.rootniAniqlash()
        if (isRoot) {
            Toast.makeText(this, "Ilova root qurulmada ishga tushdi", Toast.LENGTH_LONG).show()
            finishAffinity()
            System.exit(0)
        }
    }
```
---




