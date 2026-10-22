# Laboratoriya 5. Kotlin maxsus kalit so'zlari

Fan: Mobile application development (SE-26-III)
Ma'ruza: 11, Special Keywords
Bajardi: Tursunniyazov Asliddinbek

## 1-savol. inline

Oddiy higher-order funksiyaga lambda bersak, Kotlin bu lambdani alohida obyekt qilib yaratadi. Agar lambda tashqaridagi o'zgaruvchini ishlatsa (ro'yxat elementi, so'rov nomi va hokazo), har chaqiruvda yangi obyekt ajratiladi. Keyin yordamchi funksiya shu obyektning invoke metodini chaqiradi, bu ham alohida chaqiruv. Bir marta bo'lsa hech narsa emas. Lekin foydalanuvchi uzun ro'yxatni skroll qilganda o'lchash funksiyasi har bir element uchun ishlaydi, minglab mayda obyekt paydo bo'ladi, garbage collector tez-tez ishga tushadi va ro'yxat qotib qoladi.

inline qo'ysak kompilyator funksiyaning tanasini, lambdani ham qo'shib, chaqirilgan joyga ko'chirib qo'yadi. Vaqtni olish, kodni bajarish, logga yozish, hammasi chaqirgan funksiyaning ichida turadi. Obyekt ham yo'q, qo'shimcha chaqiruv ham yo'q. Lambda parametri bo'lmagan funksiyaga inline qo'yishdan esa foyda yo'q, u yerda tejaladigan narsa bitta oddiy chaqiruv, xolos, JVM buni o'zi ham qiladi. Kompilyator bunga warning ham beradi.

Qachon qochgan bo'lardim. Katta funksiyalarni inline qilmasdim, ayniqsa ko'p joydan chaqirilsa, tana har joyga nusxalanadi va bytecode kattalashadi, APK ham o'sadi. Yana bir narsa, inline funksiya ichidagi xatoni tuzatsam, uni ishlatgan modullarni ham qayta build qilish kerak. Shu ilovada men inline ni faqat kichik, lambda oladigan va tez-tez chaqiriladigan funksiyalarga qo'yardim. O'lchash funksiyasi aynan shunaqa, lekin masalan butun ekranni chizadigan yoki tarmoq bilan ishlaydigan katta funksiyaga qo'ymasdim.

## 2-savol. non-local return, break, continue

forEach inline funksiya. Uning lambdasi chaqirgan funksiyaning ichiga ko'chiriladi, shu sababli lambda ichida oddiy return yozsam, u lambdadan emas, butun tashqi funksiyadan chiqib ketadi. Buni non-local return deyiladi. Form tekshiruvida bu qulay: birinchi noto'g'ri maydon topildi, return, tekshiruv funksiyasi tugadi va xato qaytdi.

Oddiy, inline bo'lmagan higher-order funksiyada esa shu qator kompilyatsiya bo'lmaydi. U yerda lambda obyekt, uni saqlab qo'yib tashqi funksiya tugagandan keyin ham chaqirish mumkin. "Tashqi funksiyadan chiq" degan buyruq u paytda ma'nosiz, kompilyator shuni oldindan to'sadi. U yerda faqat return@forEach kabi label bilan yozilgan return ishlaydi, u esa faqat hozirgi lambda chaqiruvidan chiqadi. Label shu ikki holatni ajratib beradi. return@forEach aslida continue ga o'xshab ishlaydi, hozirgi elementni tashlab keyingisiga o'tadi. break va continue ning o'zi esa oldin faqat for va while ichida ishlardi, Kotlin 2.2 dan boshlab ularni inline lambdalar ichida ham yozish mumkin bo'ldi.

Men form tekshiruvini forEach bilan emas, oddiy for sikli bilan yozgan bo'lardim, yoki firstOrNull orqali birinchi xato maydonni topardim. O'qigan odamga "birinchi xatoda to'xtaydi" degani darrov ko'rinadi. SMS ekranida avval bo'sh va buzuq raqamlarni filter bilan tashlab, keyin take(5) qilardim. E'tibor beradigan narsa: buzuq raqam beshtalik hisobiga kirmasligi kerak va beshta yuborilganda sikl rostdan to'xtashi kerak, yo'qsa ortiqcha SMS ketadi. Yana raqamni "yaroqli" deb hisoblash qoidasi bitta joyda bo'lgani ma'qul, keyin uni o'zgartirish oson bo'ladi.

## 3-savol. noinline

Inline funksiyaning lambdasi obyekt emas. Kompilyator uning kodini chaqiruv joyiga shunchaki ko'chiradi, runtime da uning "o'zi" yo'q. Demak uni o'zgaruvchiga saqlab bo'lmaydi, boshqa funksiyaga berib bo'lmaydi, keyinroq chaqirish uchun eslab qolib bo'lmaydi, shunday qilsangiz kompilyator xato beradi. noinline bitta parametr uchun buni o'zgartiradi: o'sha lambda oddiy obyekt bo'lib yaratiladi va uni saqlash mumkin, funksiyaning o'zi va boshqa lambdalar inline bo'lib qolaveradi.

Profil ekranidagi yordamchida ikkita lambda bor, hozir bajariladigan yuklash bloki va retry tugmasi uchun eslab qolinadigan amal. Men retry amalini noinline qilardim, u tugma bosilguncha saqlanib turishi kerak. Yuklash bloki darhol yordamchining ichida ishlaydi, u inline qolgani yaxshi. Agar teskarisini qilsam, ya'ni yuklash blokini noinline qilsam, retry inline bo'lib qoladi va uni saqlab bo'lmaydi, kod umuman kompilyatsiya bo'lmaydi. Ikkalasini noinline qilsam kod ishlaydi, lekin inline qilinadigan hech narsa qolmaydi, unda funksiyaga inline deb yozishdan ma'no yo'q va kompilyator warning beradi.

## 4-savol. crossinline

Debounce yordamchisida asl amal yordamchining o'z tanasida chaqirilmaydi, u qaytarilgan yangi click handler ichidan, keyinroq, tugma bosilganda chaqiriladi. Agar shu amal ichida non-local return ga ruxsat bo'lsa, u debounce ni chaqirgan funksiyadan chiqmoqchi bo'lardi. Lekin tugma bosilgan paytda o'sha funksiya allaqachon tugagan, chiqadigan joyning o'zi yo'q. Kompilyator buni oldindan taqiqlashi kerak.

crossinline shuni qiladi. Lambda inline bo'lib qoladi, kodi handler ichiga ko'chiriladi, alohida obyekt yaratilmaydi, lekin ichida non-local return yozib bo'lmaydi.

noinline dan farqi menimcha shunda: noinline lambdani butunlay obyektga aylantiradi, uni istagancha saqlash mumkin, lekin inline foydasi ketadi. crossinline esa inline ni saqlab qoladi, faqat tashqi funksiyadan qaytishni to'sadi. Lambda boshqa joydan chaqiriladi, lekin uni saqlab qo'yish shart bo'lmasa, crossinline yetarli.

Erta chiqish kerak bo'lsa label bilan return yoziladi. U faqat lambdaning o'zidan chiqadi, tashqi funksiyaga tegmaydi, masalan shart bajarilmasa amalni o'tkazib yuborish uchun.

## 5-savol. reified

JVM da generic tur runtime da o'chiriladi, buni type erasure deyishadi. Kompilyatsiyadan keyin funksiya ichida T aslida qaysi tur ekani ma'lum emas, T::class ni olib bo'lmaydi, is T deb tekshirib bo'lmaydi. Activity ochish, Bundle dan Parcelable o'qish, JSON ni modelga aylantirish uchun esa klass obyekti kerak. Natijada dasturchi turni ikki marta yozadi, bir marta burchakli qavsda, bir marta klass qilib parametrda. Bu ortiqcha, xatoga ham olib keladi, ikkalasi bir-biriga mos kelmay qolishi mumkin.

reified shuni yechadi. Funksiya inline bo'lgani uchun tanasi har chaqiruv joyiga ko'chiriladi, o'sha joyda kompilyator T nima ekanini aniq biladi va T o'rniga haqiqiy turni qo'yib yuboradi. Shu sababli reified faqat inline bilan ishlaydi, oddiy funksiyaning bitta umumiy tanasi bor, u har chaqiruv uchun alohida tur ma'lumotini ololmaydi. Nothing ni reified qilib bo'lmaydi, uning qiymati ham yo'q, JVM da mos keladigan klassi ham yo'q, undan klass olish yoki tur tekshirish ma'noga ega emas.

O'z loyihamda reified yordamchilar yozardim, masalan Activity ochish yoki argument o'qish uchun, kod qisqaradi va tur xatosi kamayadi. Faqat ularni kichik qilardim, inline bo'lgani uchun har chaqiruv joyiga nusxalanadi.

## 6-savol. inline property

Property accessor ni (getter yoki setter) inline qilish uchun property da backing field bo'lmasligi kerak, ya'ni u qiymatni o'zida saqlamaydi, har safar hisoblab beradi. Extension property larda bu tabiiy, ularda backing field bo'lmaydi.

Backing field nega muammo. Field bu klassning yopiq ichki holati. Accessor kodi boshqa klassga yoki modulga ko'chirilsa, u yerdan bu yopiq fieldga to'g'ridan-to'g'ri kirib bo'lmaydi, field o'zgarsa ko'chirilgan kodlar ham eskiradi. Inline accessor da kompilyator getter yoki setter metodini chaqirmaydi, ichidagi hisobni property ishlatilgan joyga yozib qo'yadi, inline funksiyadagi kabi.

Qaysi yordamchiga arziydi. dp ni px ga aylantirish kabi kichik hisob, u yuzlab joyda, chizish kodida ham ishlatiladi, bunga arziydi. Qorong'i rejimni tekshirish ham kichik, lekin kam chaqiriladi, foydasi sezilmaydi. SharedPreferences dan flag o'qish va yozish uchun inline ortiqcha, u yerda asosiy vaqt diskka murojaatga ketadi, bitta chaqiruvni tejash hech narsani o'zgartirmaydi, faqat kod har joyga tarqalib keyin o'zgartirish qiyin bo'ladi.

## 7-savol. public inline va @PublishedApi

Public inline funksiyaning tanasi uni ishlatgan boshqa modullarga, masalan app yoki feature modulga ko'chiriladi. Agar u analytics kutubxonasining internal funksiyasini chaqirsa, ko'chirilgan kod endi boshqa modul ichida turib o'zi ko'rmasligi kerak bo'lgan narsaga murojaat qiladi, kompilyator xato beradi.

Agar bunga ruxsat berilsa nima bo'lardi. Kutubxona muallifi internal funksiyani "bu ichki, istagancha o'zgartiraman" deb hisoblaydi va nomini yoki parametrini o'zgartiradi. Lekin eski versiyada bu chaqiruv allaqachon app modulining kompilyatsiya qilingan kodiga kirib ketgan. Kutubxona yangilanganda app mavjud bo'lmagan funksiyani chaqiradi va runtime da qulaydi. Binary compatibility buziladi.

@PublishedApi internal funksiyani kompilyatsiya qilingan kodda public qiladi, public inline funksiya undan foydalana oladi. Manba kodida esa u internal bo'lib qoladi, boshqa modullar uni to'g'ridan-to'g'ri chaqira olmaydi. Bu muallifning va'dasi: bu funksiya endi amalda public API ning qismi, uni ehtiyotsiz o'zgartirmayman.

Umumiy kutubxonada men faqat lambda oladigan kichik funksiyalarni va reified kerak bo'lganlarni inline qilardim, qolgani oddiy funksiya bo'lsin. Har bir public inline funksiya kutubxonaning ichki tuzilishini ilovalarga muhrlab qo'yadi, keyin o'zgartirish qiyin. Analytics kutubxonasida masalan event yuborish funksiyasi oddiy bo'lishi kerak, u tarmoq bilan ishlaydi va tezligi inline ga bog'liq emas.

## 8-savol. typealias

typealias mavjud turga qisqaroq nom beradi, boshqa hech narsa qilmaydi, yangi tur yaratmaydi. Kompilyator alias ni ko'rganda uni darhol asl tur bilan almashtiradi. Mahsulot click handler i yoki kategoriya map i uchun alias kodni ancha qisqartiradi, lekin kompilyator uchun u o'sha uzun funksiya yoki map turi bo'lib qolaveradi.

Shu sababli buyurtmani bekor qilishdagi xato topilmadi. Foydalanuvchi ID si va buyurtma ID si uchun ikki xil alias nomi bor edi, lekin ikkalasi ham oddiy String. Kompilyator uchun bu ikkita String parametr, o'rni almashsa ham turlar mos keladi. Xato faqat runtime da, noto'g'ri buyurtma bekor qilinganda yoki topilmaganda chiqadi.

Jamoada bunday ID lar uchun alias o'rniga value class ishlatardim. U haqiqiy alohida tur, foydalanuvchi ID sini buyurtma ID si o'rniga bersangiz kompilyator xato beradi. Runtime da esa ko'pincha oddiy String bo'lib saqlanadi, qo'shimcha xarajati deyarli yo'q. Yana bir xil turdagi argumentlar ko'p bo'lgan funksiyalarda named argument ishlatishni qoida qilardim, code review da ham shunga qarardim.

Alias uzun funksiya turlarini va murakkab generic turlarni o'qishni osonlashtirsa foydali. Lekin bir xil asosiy turni ma'nosi bo'yicha ajratish uchun ishlatilsa, u faqat xavfsizlik illyuziyasini beradi, kod "bular har xil tur" deb ko'rsatadi, kompilyator esa hech narsa tekshirmaydi.

## 9-savol. Any, equals va hashCode

Any Kotlin dagi barcha null bo'lmagan turlarning ildizi, har bir klass undan meros oladi. U har klassga uchta metod beradi: equals (solishtirish), hashCode (xesh), toString (matn ko'rinishi). Any? bunga null ni ham qo'shadi, analytics parametrlari yoki Intent extra lari uchun istalgan qiymatni saqlash mumkin bo'ladi.

Ro'yxat yangilanganda tanlov nega yo'qoladi. Tarmoqdan kelgan mahsulotlar ma'noda o'sha mahsulotlar, lekin ular yangi obyektlar. Mahsulot klassi equals va hashCode ni override qilmagan bo'lsa, Any dagi standart xatti-harakat ishlaydi: ikki obyekt faqat xotirada aynan bitta obyekt bo'lsa teng. Set yangi obyektlarni eski tanlov bilan solishtiradi, "boshqa" deb topadi, tanlov ketadi.

equals va hashCode doim birga va bir xil maydonlar bo'yicha yozilishi kerak, masalan mahsulot ID si bo'yicha. Qoida: teng obyektlarning xesh kodi ham teng bo'lishi shart. Set avval xesh bo'yicha "qutini" topadi, keyin equals bilan solishtiradi. Faqat equals yozilsa, teng obyektlar har xil qutiga tushadi va baribir topilmaydi. Mahsulotni data class qilish kerak yoki ikkala metodni ID bo'yicha yozish kerak. data class da esa boshqa muammo chiqishi mumkin: narx yoki qoldiq o'zgargan bo'lsa, hamma maydon bo'yicha solishtirilgani uchun yana "boshqa" chiqadi, shuning uchun ID bo'yicha solishtirgan yaxshiroq.

Any? map dan o'qishda as? yoki is tekshiruvi ishlatardim, tur mos kelmasa default qiymat qaytarardim, oddiy majburiy cast dan qochardim. Yaxshisi bu map ni har bir kalit uchun aniq turdagi funksiyalar bilan o'rab qo'yish, masalan getString(key), getInt(key) kabi.

Katta loyihada Any xavfli. Kompilyator tur tekshiruvini to'xtatadi, xatolar runtime da chiqadi va ko'pincha butunlay boshqa joyda qulash bo'lib ko'rinadi, uni qaysi joydan kelganini topish qiyin.

## 10-savol. Nothing

Nothing bu hech qachon qiymatga ega bo'lmaydigan tur. Qaytish turi Nothing bo'lgan funksiya hech qachon normal tugamaydi, yo exception tashlaydi, yo cheksiz ishlaydi, shu bois bu turdagi obyekt hech qachon bo'lmaydi.

U har bir turning subtype i. Bu mantiqan to'g'ri, bunday ifoda hech qachon qiymat qaytarmaydi, demak uni istalgan turdagi qiymat kutilgan joyga qo'yish xavfsiz, u yerga noto'g'ri qiymat yetib bormaydi. Majburiy argumentni olishda "argument yoki error" ifodasini yozsam, error tarmog'i Nothing qaytaradi, kompilyator esa bu qatordan keyin dastur faqat argument bor bo'lganda davom etishini tushunadi va qiymatni null bo'lmagan deb oladi. Argument yo'q bo'lsa ilova darhol aniq xabar bilan to'xtaydi.

UI holatida Nothing ma'lumotsiz holatlar uchun qulay. Success holati ma'lumot tashiydi, Loading va Error esa ro'yxat tashimaydi. Ularni Nothing turidagi ma'lumot bilan e'lon qilsam, ular istalgan ma'lumot turiga mos keladi, mahsulot ro'yxati bo'ladimi, profil bo'ladimi, har ekran uchun qayta yozish shart emas. Buning uchun holat turi out bilan kovariant bo'lishi kerak.

Qachon to'xtatish yaxshi. Dasturchi xatosi bo'lsa, masalan majburiy argument berilmagan yoki "bo'lishi mumkin emas" degan holat yuz bergan bo'lsa, ilovani darhol to'xtatgan ma'qul. Jimgina o'tkazib yuborish xatoni yashiradi va keyinroq boshqa joyda, noto'g'ri ma'lumot bilan qulaydi, uni topish ancha qiyin. Tarmoq yo'qligi yoki foydalanuvchi noto'g'ri kiritgan ma'lumot kabi holatlarda esa to'xtatmasdan, ekranda tushunarli xabar ko'rsatish kerak.
