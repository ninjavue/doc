# Zirh kutubxonasini ishlatish qo‘llanmasi
Zirh kutubxonasidan foydalanishni boshlash uchun avvalo o'z loyihangizga
ushbu kutubxonani qo'shishingiz kerak. Buning uchun loyihangizdagi app
papkasining ichida yangi libs nomli papka yarating va unga .aar formatidagi
Zirh kutubxona faylini joylashtiring. Shu tariqa kutubxona
loyihangizga qo‘shilgan bo‘ladi.
#
settings.gradle.kts faylini oching, dependencyResolutionManagement bo'limidagi repositories qismiga libs papkasining joylashuvini
ko'rsatishingiz kerak bo'ladi. Bu orqali loyiha kutubxonalarni to'g'ri topadi. Masalan: 
<pre lang="md"> <code>
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
</code> </pre>
#
Bu kodni dasturingizga qo'shganingizdan keyin build.gradle.kts(app) faylni ichiga kutubxonani implementation qilib yozib qo'ying. Masalan:
<pre lang="md"> <code> 
  dependencies {
    implementation(":zirhlib-release@aar")
  }
</code> </pre>
#
Bu kodni yozib bo'lganingizdan keyin tafsiya etiladi asosiy Activity class massalan BaseActivity.java/.kt nomi bilan alohida umummiy Activitylar uchun ota(parent) class yaratib olishni bu orqali kod takrorlanish oldi olinadi. AndroidManifest.xml faylliga internet uchun ruxsat(permission) qo'shing.
<pre lang="md"> <code> 
    <uses-permission android:name="android.permission.INTERNET" />
</code> </pre>
#
**#vpnniAniqlash Funksiyasi**
Ushbu funksiya ilova ishga tushgan qurilmada VPN ulanishi mavjud yoki
yo‘qligini aniqlash uchun ishlatiladi. Agar qurilmada faol VPN ulanishi mavjud
bo‘lsa, funksiya true qiymat qaytaradi, aks holda false. VPN mavjudligi xavfsizlik
talablariga zid bo‘lishi mumkin. Foydalanuvchiga ogohlantiruvchi xabar berish va
ilovani ishlashini to‘xtatish yoki yopish tavsiya etiladi. Funksiya ishlashi uchun
internetga ruxsat kerak.

#
**#emulyatorniAniqlash Funksiyasi**
Ushbu funksiya ilova ishga tushgan qurilmaning emulyator (soxta qurilma)
ekanligini aniqlash uchun mo‘ljallangan. Agar ilova real qurilmada ishlayotgan
bo‘lsa, funksiya false qaytaradi, aks holda emulyator aniqlansa true qiymat
qaytaradi.

#
**#rootniAniqlash Funksiyasi**
Ushbu funksiya ilova ishga tushgan qurilmaning root qilinganligini aniqlash
uchun ishlatiladi. Agar qurilma root qilingan bo‘lsa, funksiya true qiymat qaytaradi,
aks holda false. Agar funksiya true qiymat qaytarsa, bu qurilma xavfsizlik talablariga
javob bermasligini bildiradi. Shuning uchun foydalanuvchiga ogohlantiruvchi xabar
chiqarish tavsiya etiladi va ilovani darhol yopish kerak (finishAffinity() yoki
System.exit(0) yordamida).

