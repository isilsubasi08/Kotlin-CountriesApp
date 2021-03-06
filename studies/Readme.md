# Countries App

<h2>Kotlin : Layout</h2>

->Feed Fragment UI<br>
->Yüzdesel Ağırlıklarla Çalışmak<br>
->Ülke Sayfası<br>

<hr>

SwipeRefreshLayout kullanacağız.
>SwipeRefreshLayout : Dikey kaydırmayı algılayarak çalışan, kullanıcıya sürekli dönen bir Progress Bar gösteren yardımcı bir kütüphanedir. Örneğin yüksek skorlar sayfanız olduğunu varsayalım, kullanıcı son skorları görmek için sürekli bu sayfaya gir çık yapmayı tercih etmeyecektir. Bunun yerine daha kolay bir yol sunmak gerekir. Kolay yolu da bu kütüphanedir diyebiliriz.
Aşağı doğru çekince güncelleme yapabilmek için bu layoutu kullanıyoruz.
feed_fragment.xml kısmına RecyclerView,Progress Bar ve SwipeRefreshLayoutumuzu ekledik.

RecyclerView itemlerinın görünümlerini ayarlamak için item_country.xml oluşturuyoruz.
İçerindeki itemları yaparken responsif tasarım için imageview içerindeki boyutlarda 
<b>layout_weight</b> özelliğini kullanacağız.
<hr>

<h2>Kotlin : MVVM MIMARI YAPISI</h2>

->MVVM Nedir?<br>
->Modeli Yazmak<br>
->Adapter Yazımı<br>
->ViewModel ve LiveData<br>
->ViewModel Oluşturmak<br>
->Verileri Göstermek<br>
->Country ViewModel<br>

<hr>

İstek yapacağımız apiden dönecek jsonunun içerisinde aşağıdaki gibi veriler olacaktır.

```
{
    "name" : "Algeria"
    "capital" : "Algiers"
    "region" : "Africa"
    "flag" : "resim_url"
    "language" : "Arabic"

}
```


 Bu verileri alacağımız bir model oluşturacağız.Modelden sonra MVVM dedidiğimiz bir mimari yapıda kodlarımızı yazacağız.(Model-View-viewmodel)<br>
><b>MVVM :</b> 3 ana bileşen vardır. Model,View,View Model.Business mantıkla kullanıcı arayüzü mantığıın birbirinden ayırmak için kullanılan bir mimari yapıdır.
Gelen datanın işlenmesi ,arka planda gösterilmesi , arka planda yapılacak işlemler olsun , database işlemleri olsun bunun gibi kullanıcının ne olduğuyla hiç bir ilgisi olmayan işlemleri biz ViewModel de yapacağız.
Kullanıcının göreceği, arayüzü ilgilendiren işlemleri View içerisinde yapacağız.
Ve tüm bunlar modelden beslenecek.

+ View üzerindeki fazla koddan kurtulacağız.
+ Fonksiyonlarla uğraştığım tüm işlermleri ViewModelde yapacağım. Ve modelleyerek yapacağım.
Modelden beslenecek.
+ Test yapmak daha kolay olacak.
+ Kodlar daha temiz olacaktır. 


## Model 

+ Model paketiniz içerisinde Model adında bir kotlin file açıyoruz.
Ve içerisine data class() yazıyoruz. Data Class'ın farkı verilerin tutulduğu , verilerin modellenmesi için böyle bir sınıf oluşturulmuştur.

```
data class Country(
    val countryName : String?,
    val countryRegion : String?,
    val countryCapital : String?,
    val countryCurrency : String?,
    val countryLanguage : String?,
    val imageUrl : String?,

)
```
## Adapter
+ Adapter paketi oluşturup içerisine CountryAdapter adında sınıf oluşturuyoruz.
Bu sınıf parametre olarak bir ArrayList alacak ve Country modeli şeklinde.
Ve bize bir adapter döndürecektir.
```
RecyclerView.Adapter<CountryAdapter.CountryViewHolder>()
```
+ Yukarıdaki değer de bizden bir ViewHolder sınıfı istiyor. O sınıfı oluşturuyoruz.
```

    class CountryViewHolder(var view : View) : RecyclerView.ViewHolder(view) {

    }
 ```
 + Parametre olarak view elemanı veriyoruz. Ve bize ViewHolder döndürecektir.

 + implement etmem gereken metotları implement ediyorum.

## ViewModel ve LiveData

+ Her view'ın ayrı bir viewmodeli olması gerekiyor. Clean code yazmak amaçlı bir mimari yapı olduğu için fragmentler için ayrı ayrı viewmodel oluşturacağız.

+ MVVM kullanmanın diğer bir artısı veya özelliği lifecycle. Normal bir fragmentte veya aktivitede oncreate gibi başlarken oluşan metotlar , durdurulurken cihazı yana çevirdiğimiz çalışacak metotlar vardır. 
Ama ViewModel'ın bir tane lifecycle vardır. İster yan çevir ister başka şeyler yap sadece tek bir scopeda gösteriliyor. onCleared() bitince belli eden bir metotdur.

+ LiveData (Canlı Veri) -> Observable olması. Yani gözlemlenebilir olması. <br>
Observer - Gözlemci demek. Bizde gözlemlenebilir veriler oluşturacağız. Bu veriye observerların yani gözlemcilerin bu veriye her zaman erişimi sağlanıyor. 

+ ViewModelimiz oluşturduk ve içerisindeki datalarımızı livedata olarak kullanacağız.

```
class FeedViewModel : ViewModel(){

    val countries = MutableLiveData<List<Country>>()
    val countryError = MutableLiveData<Boolean>()
    val countryLoading = MutableLiveData<Boolean>()
    
}
```
## ViewModel Oluşturmak

+ refreshData() metodu oluşturuyoruz. Bunu kullanarak ilerde datalarımızı çekeceğiz ve refresh edeceğiz. Oluşturduktan sonra View tarafını yazmaya geçeceğiz.FeedFragment içerinde viewmodelimizi oluşturacağız.


## Retrofit ve RxJava Nedir ? 

+ Retrofit -> İnternetten veri indirirken , özellikle apiyle çalışırken GET,POST,UPDATE gibi işlemleri yapmamızı sağlayan bir kütüphanedir.Arka planda indirmesi, asenkron çalışması, kullanıcının uı bloklamaması önemli özelliktir.

+ RxJava -> Yine çok güçlü bir yardımcı kütüphanedir.Rxjavayı kullanarak gözlemlenebilir yapabiliriz.Başka threadlerde yapam gibi başka araçlara sahip olabiliriz. 

+ Basit işlemler için Retrofiti tercih ederken daha kapsamlı uygulamalarda RxJavayı tercih etmeliyiz. 

### Interface Yazmak 

>Öncelikle interface oluşturuyoruz. Ve burda hangi işlemleri yapacağımızı söylüyoruz.GET,POST  (CountryAPI)

>Uygulamamızda verileri indireceğiz. Ve indirdikten sonra yerel veritabanımıza kaydedeceğiz. Şöyle bir algoritma izleyeceğiz. Eğer 10dk geçtiyse tekrar apiden indir. Geçmediyse halihazırda sqlitedan kullanabilirsin. 

> Call işlemleri için rxjavda single özelliğini kullanıyoruz. Observabla diye gözlemlenebilir call de vardır. Değişk call çağrılıarı vardır. 

### Servis Yazmak 

> Servisimizi yazmamız retrofit objemizi oluşturmamız gerekiyor. 


```
    private val BASE_URL = "https://raw.githubusercontent.com/"
    private val api = Retrofit.Builder()
        .baseUrl(BASE_URL)
        .addConverterFactory(GsonConverterFactory.create())
        .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
        .build()
        .create(CountryAPI::class.java)

    fun getData() : Single<List<Country>>{
        return api.getCountries()
    }
```


### Verileri Almak

+ Verileri almak için viewmodelimiz içerisinde servisimizin objesini oluşturmalıyız.

```
    //servis objemizi oluşturmamız gerekiyor.
    private val countryApiService =CountryAPIService()
    //disponsablemızı oluşturacağız.
    private val disposable = CompositeDisposable()
```

> CompositDisposable() -> Biz bu tarz Call'lar yaparken , internetten veri indirirken Retrofitle ya da RxJava ile her yaptığımız Call hafızada bir yer tutuyor. Fragmentler clear edildiğinde ya da başka bir fragmente geçildiğinde kapatıldıgında bu Callardan kurtulmamız gerekiyor. Kurtulmazsak eğer hafızada yer ediyor ve sıkıntılara sebep olabiliyor. CompositDisposibla() tam olarak bu işe yarıyor. Bir tane büyük bir obje oluşturuyor. Call yaptıkça internetten veri indirdikçe bunun içerisine atıyoruz. disposable (Kullan at demek ). Bu kullan at objemize bunları dolduruyoz. En sonunda temizleyerek hafızamızı verimli kullanabiliyoruz. Bu çok faydali bir şeydir. Birden fazla Call işlemi yaparsak hepsini bunun içerisine koyuyoruz. 


### Refresh Layout

```
        //Kullanıcı refresh ettiğinde ne olacak ?
        swipeRefreshLayout.setOnRefreshListener {
            countryList.visibility=View.GONE
            countryError.visibility=View.GONE
            countryLoading.visibility=View.VISIBLE
            viewModel.refreshData()
            swipeRefreshLayout.isRefreshing=false
        }
```

## Kotlin : Extensions


### KTX Nedir ? 
+ Resimleri göstermek için <b>"Glide"</b> kütüphanesinden yararlanacağız.


> KTX : Kotlin extensions(Eklentiler). Kotlin bize eklenti yapmaya izin veren bir dildir. Herhangi bir sınıfa istediğim eklentiyi yapabiliyorum. 
```
//Extension
//Hangi sınıfa extend edeceksem onu yazıyorum.
//Mesela String sınıfına bir eklenti oluşturmak istiyorum. Aşağıdaki gibi yapabilirim ;
//gelen veriyi yazdıracak bir extension yazmış olduk.
fun String.myExtensions(myParameter : String){
println(myParameter)

}
```
```
        val myString = "Isil"
        myString.myExtensions(myString)
```


+ Her imageview objesine glide için bir fonksiyon tanımlamak. Ve glideım içinde yapılan tüm ayarlamaları o fonksiyon içerisinde yapmak , bundan sonra herhangi bir imageviewdan bu fonksiyonu çağırabilmek. Böylece ben uygulamamda ister row içerisinden ister adapter içerisinden ister başka fragmentten glide tekrar tekrar tanımlamadan bir seferde ulaşılabilir hale geleceğim.  


### Görselleri getirmek

+ ImageView bir extension fonksiyonu yazıyorum. 
+ Glide özelliklerini tanımlıyorum.

+ Placaholder -> Görseller gelene kadar nasıl bir resim gözüksün demektir.

```
fun ImageView.downloadFromUrl(url : String? , progressDrawable: CircularProgressDrawable){

    val options = RequestOptions()
        .placeholder(progressDrawable)
        .error(R.mipmap.ic_launcher_round)


    Glide.with(context)
        .setDefaultRequestOptions(options)
         .load(url)
         .into(this)
    
}
```

## Kotlin : ROOM

> Jetpackin en popüler kütüphenlerinden biridir. Yerel veritabanlarıyla çalışmayı bizim için kolay bir hale getiren frameworktür.

>İnternetten indirdiğimiz verileri yerel veritabanımızda saklayacağız. Belli bir süre geçmediyse veritabanında belli bir süre geçtikten sonra yine apıdan çekilmesini sağlayacağız. 

> İnternetten indirdiğimiz verileri SQLite a kaydedeceğiz. Klasik SQLite komutları kullanmak yerine Jetpackin Room kütüphanesini kullanacağız. 

### Modeli Değiştirelim

> SQLitenın içerisinden Room Databasi kullanabilmek için <b>entity</b> dediğimiz bir yapıyı oluşturacağız. SQLite içerisinde bir sınıflandırma yapacaktır. Modelimizin başına yazacağız. Her entity de bir primaryKey kullanmamız gerekir. 

> Room kullanabilmek için bir arayüz ve servis yazmamız gerekmektedir.


> Room frameworkün dökümanını inceleyebiliriz.

+ Room veritatabınıda 3 tane önemli özelliğimiz var.

+ Database -> Veritabanımızın kendisi.

+ Entity 

+ DAO -> Verilere ulaşma objesidir. Data Access Object

Bu işlemleri yaparken arka planda yapacağız . Ve bunun içinde COROUTINE kullanacağız. 

> Coroutines -> Farklı threadlerde işlem yapmamızı kolaylaştırır. 
DAO oluşturduğumuzda bunun içindeki metotları coroutineler çağrırak yaptıgımız takdirde ana threadde değilde başka bir threadde yapıldıgını garanti altına alıyoruz. 
Asenkron işlemler için kullanılıyor.Tanım olarak lightweight thread olarak söyleniyor main thread’i blocklamadan bir çok işlem yapabiliyoruz.Şöyle bir örnek var çok sayıda(1000) thread işlemi başlatırsanız uygulamanız crash olur ama aynı sayıda coroutine işlemi başlatırsanız herhangi bir sorun olmayacaktır.

> Fonkisyonlar <b>suspend</b> keywordünü alır. Coroutineler içerisinde çalıştırılabilen ve gerektiğinde durdurulup sonradan devam ettirilebilir fonksiyonlardır. 

### Veritabanı Oluşturmak 

Entity - Model
DAO - DAO interface 
Database  

```
@Database(entities = arrayOf(Country::class),version = 1)
abstract class CountryDatabase {

    abstract fun countryDao() : CountryDAO
}
```

> Bu veritabanımızdan birden fazla obje oluşturmasını istemiyoruz. Bu veritabanımızdan sadece bir tane obje oluşturmak istiyoruz. 
Çünkü eğer farklı zamanlarda ya da aynı zamanlarda farklı threadlerden bizim veritabanımıza ulaşılmaya çalışılırsa bu çakışma oluşturacaktır. Bundan dolayı burda oluşturduğumuz databasemizi singleton mantığıyla oluşturacağız. 

> <b>Singleton :</b> İçerisinden tek bir obje oluşturulabilen bir sınıftır. Eğer daha önce oluşturulmuş bir obje yoksa oluşturuyorduk. Eğer varsa oluşturmuyorduk o oluşturulan objeyi çekiyorduk. Ve appin herhangibir tarafında buna ulaşabilirdik. Ve her yerde ulaşabilmek için <b>companion object{}</b> kullanıyoruz. 
Buda statik şekilde bir değişken oluşturup bu sınıfın scopu dışında da ulaşılmaya olanak sağlıyordu. 

```
    companion object{

       @Volatile private var instance : CountryDatabase? = null
    }
```

+ @Volatile -> Herhangi bir değişkeni Volatile olarak tanımladığımız zaman farklı threadlere de görünür hale getirilir. 

+ Oluşturduğumuz instance var mı yok mu kontrol etmem lazım . Ona göre işlem yapacağım. Var ise o instance yok ise oluşturmam gerekiyor. 

+ Bu sınıfımızdan sadece tek bir obje oluşturulur. O objenin adı instance'dır.  


### Coroutine

> Coroutine kullanmak için bazı ayarlar yapmamız lazım . Bazi parametreleri vermemiz lazım. Arka planda mı çalıştıracağız main threadde mi çalıştıracağız bunun söylememiz gerekiyor. 

+ Öncelikle bir iş oluşturmam gerekiyor ve bu işin ne yapacağını söylemem gerekiyor. 
+ Farklı bir sınıfta kullanacağız. -> ViewModel modülünün içerisine BaseViewModel sınıfı oluşturuyorum.
+ AndroidViewModel() extend ediyorum. Ve benden application parametresi istiyor. Daha da güvenli bir sınıf oluşturmak için bunu kullanıyorum.
+ CoroutineScope extend etmem gerekiyor. Ve memberlere implement ediyorum.
+ get metoduna coroutine ne yapacağını söylemem gerekiyor.

```
 override val coroutineContext: CoroutineContext
        get() = 
```

+ Bir job oluşturacağım. Job iş demek. Arka planda yapılacak iş demek. 

+ get() =job + Dispatchers.Main -> Arka planda işlemi yap ve main threade geri dön anlamındadır. 

+ onClerade() fonksiyonun çağırıyorum. Eğer app kapatılırsa yani bir şekilde destroy olursa burdaki işi bitireceğim. 
+ Bundan sonra FeedViewModela extend ediyorum. Ve artık coroutineleri kullanabilirim. 

+ storeInSQLite() metoduna gelerek launch yazıyorum . Ve bu bana yeni bir coroutine oluşturacağını söyler. Bir joba ihtiyaç duyduğunu söyler.

+ Artık bu launchın içinde database işlemlerimi yapacağım. 

+ toTypedArray() listedeki verileri tekil hale getirecektir. Kotlinin bir özelliği. Listeyi tekil elemanlar haline getirmeye yarar. 

>dao.insertAll(*list.toTypedArray()) // list -> individual


### İleri Seviye SharedPreferences

+ 10 dakika geçtiyse verileri apiden al geçmediyse veritabanımdan al demek istiyordum. 
+ Veritabanına ne zaman kaydedildiğini kontrol etmeliyim. 
+ Ve süreyi kontrol etmeliyim.
+ Ve bu veriyi sharedprefrences de tutacağım.
 Singleton yapısıyla CustomSharedPreferences oluşturuyoruz. Ve İçerisine time değişkenini ekliyoruz. Süreyi burda tutacağız. 

 ### API ve SQLite

 + Sqlitedan mı çekicez apiden mi çekicez onunla ilgili bir fonksiyon oluşturacağız.


## KOTLİN : DATA BİNDİNG
<hr>

> UI elementlerini gözlemlenebilir verilerle bağlamak. Layouta koydugumuz bir görünümü değişken olarak kodda tanımlarken , aslında arka planda findViewById() kullanıyoruz.Kotlinde arka planda findViewById() çalıştırılıyor. Ama bu metot hafızayı çok şişiren bir metotdur. Hafızayı yoram bir metotdur.

> Biz direk xml içerisinden text vereceğimiz yere modelimizden çektiğimiz veriyi yazabiliriz. Bu değişiklikler xml tarafından gözlemlenebilir. Direk kullanıcıya yansıtılabilir. 

> item_country layoutunun içerisine data tagleri koyacağız.

```
    <data>
        <variable
            name="country"
            type="com.example.kotlincountries.model.Country" />
        
        
    </data>
```

+ name ve type veriyoruz. Modelin ismini ve tipini veriyoruz. Verileri direk bağlıyabiliyoruz. 
+ android:text="@{country.countryName}"  datanın ismini yazınca, modelin içindeki verilere erişebiliyoruz.

> xml içinde yaptığımız değişikliği adaptör içinde de değişiklikler yapılarak erişebiliriz. 

+ data binding yaptığımızı söylememiz gerekiyor. 

### Kenid Listener Arayüzümüz

+ Data binding de onclick listenet yapacağız. Kendimiz bir arayüz yazmamız gerekiyor.

+ 