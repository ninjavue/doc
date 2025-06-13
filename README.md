# Zirh kutubxonasini ishlatish qo‘llanmasi
Zirh kutubxonasidan foydalanishni boshlash uchun avvalo o'z loyihangizga
ushbu kutubxonani qo'shishingiz kerak. Buning uchun loyihangizdagi app
papkasining ichida yangi libs nomli papka yarating va unga .aar formatidagi
Zirh kutubxona faylini joylashtiring. Shu tariqa kutubxona
loyihangizga qo‘shilgan bo‘ladi.
#
settings.gradle.kts faylini oching, dependencyResolutionManagement bo'limidagi repositories qismiga libs papkasining joylashuvini
ko'rsatishingiz kerak bo'ladi. Bu orqali loyiha kutubxonalarni to'g'ri topadi va
ulaydi. Masalan: 
<pre lang="md"> <code>```
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
  ```</code> </pre>
