# Editor Scripting Giriş
## Teorik Ders

---------------------------------------------------------------------------------------
### Phase 0 (Genel Tanım)

 #### [CustomEditor(typeof(HedefScriptimiz))]
* Class ismimizin üst satırına tanımlanarak kullanılan bu Attribute ile classlarımıza özgün editör scripting yapabiliyoruz. Daha açık bir dille konuşmak gerekirse, tamamen bize özgü ve editlenebilir boş alan oluşturup hedef olarak belirlediğimiz scriptin içeriğine de bağlı kalarak inspectordaki görünümünü düzenliyoruz. 

* Fakat **genel olarak** kullanım alanı var olan bir scriptin görünümünü manipüle etmekten ziyade ihtiyacımız doğrultusunda editör scriptleri yazmak. Yani nesneler üstünde toplu işlevler gerçekleştirmek, el ile yaptığımız bazı işlemleri metotlar haline getirdikten sonra editörümüzde bir Buton'a bağlayıp işlem kolaylığı sağlamak gibi durumlar. Örneğin sahadaki "Enemy" tagına sahip tüm objelerin rengini tek bir buton ile değiştirebilir veya transform değerleri ile oynayabiliriz.

* Tersi durumda ise yani sadece bir scriptimizin görünümüne ilave yapmak istediğimiz durumlar ise bir metodumuzu anında execute etmek yani çalıştırmak için inspector'a bir buton yardımı ile bağlamak olabilir. Bir örnek ile; karakterimiz oyun sonuna vardığında gerçekleşecekleri test etmek için oyunu her seferinde bitirmek yerine orada çalışan metodu execute edip anında sonuçları gözlemleyebiliriz.

* Bu gibi işlemleri gerçekleştirmek için main scriptimizin barındırdığı değişken veya metotlar ile, bizim iletişim kurmamızı sağlayan görsel öğeleri kullanacağız. **Bu işlemleri main scriptimizden ürettiğimiz bir nesne yardımı ile birbirleri ile ilişkilendireceğiz.** 

* Inspector'ümüzü şekillendirirken;

 1-[GUI](https://docs.unity3d.com/ScriptReference/GUI.html) 
 
 2-[GUILayout](https://docs.unity3d.com/ScriptReference/GUILayout.html)
 
 3-[EditorGUI](https://docs.unity3d.com/ScriptReference/EditorGUI.html)  
 
 4-[EditorGUILayout](https://docs.unity3d.com/ScriptReference/EditorGUILayout.html) 
 
 Classlarındaki metotlardan faydalanacağız. Bunlar aklımıza gelebilecek her türlü GUI öğresini farklı kullanım şekillerinde barındırıyorlar. Gözlemlerim doğrultusunda bu classlardan kullanımı en kolay olanı ve bu olaya hizmet etmesi için tasarlanmış olanı **EditorGUILayout** fakat çeşitlilik ve gereksinim bakımından diğerlerini de inceleyeceğip test edeceğim. 
 
 + Edit: Yaptığım ufak bir araştırmaya göre *GUI* ve *EditorGUI* metotları parametre olarak [Rect](https://docs.unity3d.com/ScriptReference/Rect.html) alarak bizim istediğimiz noktadan istediğimiz kadar uzunlukta manuel pozisyon vermeye dayalı çalışıyor. **GUILayout ve EditorGUILayout ise bu konumlandırmaların otomatikleştirilmiş versiyonları olan metotları içeriyor. Basitçe bizim kodu yazdığımız sırayla GUI elemanlarını alt alta otamatik olarak hizalıyorlar.** Sonuç olarak özellikle elle konumlandırma ihtiyacımız olmadığı müddetçe, yolumuza Layout versiyonları ile devam edeceğiz. Yani kabaca şöyle bir tablo çıkıyor.
 
     Pozlama  | Manuel | Otomatik 
     ------------ | ------------- | ------------- | 
     GUI | X |  
     GUILayout |  | X 
     EditorGUI | X |  
     EditorGUILayout |  | X 
     
 ---------------------------------------------------------------------------------------
 
### Phase 1 (Requirements & Setup)
 
 * Proje paneli altında Editor klasörü oluşturalım.  ( Anladığım kadarıyla Best Practice, hatta zorunluluk. )
 
 * Editör scripti yazabilmek için **UnityEditor** namescape'ini implemente etmemiz ve MonoBehaviour yerine **Editor** inherit etmemiz gerekiyor.
 
 * Editör scriptimiz ile inspector görünümünü manipüle etmek istediğimiz hedef scriptin birbirlerinden farklı nesneler olduğunu unutmayınız.
 
![image](https://user-images.githubusercontent.com/75368035/116951571-ec05c000-ac90-11eb-9a49-2ccf317a7f38.png)

 * Editor klasörü altında editör scriptimizi oluşturduk. [CustomEditor(typeof(HedefScriptimiz))] attribute'ümüz ile bunun bir editör scripti olduğunu niteledik ve son olarak MonoBehaviour yerine Editor kalıtımını yaptıysak hazırız demektir.
 
 ---------------------------------------------------------------------------------------
### Phase 2 (Keyword, Method & Fieldslar ile Tanışma)

 * Scriptimizin Editördeki görünüşünü düzenlemekten sorumlu olan Durum Fonksiyonumuz (Update,OnEnable,Start gibi...) **OnInspectorGUI()**. Bu metodu override ederek scriptimizin inspectordaki görünüşünü düzenleyebiliriz.
 
![image](https://user-images.githubusercontent.com/75368035/113456610-5ae3c500-9416-11eb-915d-2c7276a157fd.png)

Override ettiğimizde düzenlediğimiz script'in -benim için 'CustomEditorTarget- sahip olduğu tüm elementlerin inspectorda ki görünümünün kaybolduğunu görebiliriz.
 
![image](https://user-images.githubusercontent.com/75368035/113456777-d9406700-9416-11eb-802f-0fa469585b7f.png)

 Metodumuzun içine **base.OnInspectorGUI();** çağrısı yaparak scriptimizi özgün görünümüne kavuşturabiliriz.

![image](https://user-images.githubusercontent.com/75368035/113456870-10af1380-9417-11eb-8027-5b121c2907d8.png)

![image](https://user-images.githubusercontent.com/75368035/113457267-12c5a200-9418-11eb-9bb2-01a3c1086f50.png)


**DrawDefaultInspector()** metodu da base alma işlemi ile aynı görevi görecektir. Özetle bütün Inspector'ü düzenlemek yerine sadece extradan 1-2 işlev (buton gibi) eklemek istediğimiz durumlarda başvuracağımız yöntemler **DrawDefaultInspector()** ve **base.OnInspectorGUI();**

---------------------------------------------------------------------------------------

### Buraya Kadar Neler Edindik?

  Şimdilik şu bağlamları kazandığımıza inanıyorum. Eğer bunlardan birini aklınızda canlandıramıyor veya cevaplayamıyorsanız üst kısıma kısa bir göz atmanızı ve kazanamadığınız bağlama yanıt aramanızı tavsiye ederim. Hızlıca bir özet geçelim;
 
  1. Editör Scriptleme nedir ve ne için kullanılır? [Phase 0 (Genel Tanım)](https://github.com/onurApphic/Editor-Scripting#phase-0-genel-tan%C4%B1m)
  
  2. Editör scriptlerken ihtiyacımız olan GUI öğelerini hangi classlardan üretebiliriz? [Phase 0 (Genel Tanım)](https://github.com/onurApphic/Editor-Scripting#phase-0-genel-tan%C4%B1m)
  
  3. Editör scripti hazırlamak için ne tür bir ön hazırlığa ihtiyacımız var?  [Phase 1 (Requirements & Setup)](https://github.com/onurApphic/Editor-Scripting#phase-1-requirements--setup)
  
  4. Editör scripti hazırlarken hazırladığım GUI elementleri ile metot veya değişkenlerimi birbirine nasıl bağlayabilirim? [Phase 0 (Genel Tanım)](https://github.com/onurApphic/Editor-Scripting#phase-0-genel-tan%C4%B1m)
  
---------------------------------------------------------------------------------------

### Phase 3 (Hedef Objemizi Belirleme)

 Yukarıda da bahsettiğim üzere, hazırladığımız editör yardımı ile bir veya birden çok nesne üzerinde operasyonlar yapabiliriz. Bu olayı kendi içinde tekil ve çoğul olarak ikiye ayıracağız, bu ayrımı, hiyerarşide bir objeyi seçmiş olmamız veya birden çok objeyi aynı anda seçmiş olmamıza göre yapıyoruz. 
 
 * Tekil seçimler için hedef classımızı değişkene atarken, Editor classına ait target objesinden faydalanacağız. Sonrasında bu değişken üzerinden ana scriptimizdeki değişken ve metotlara erişip inspectorumuza yansıtacağız. Değişken almamızın syntax'ı şu şekildedir. Temel mantığında type conversion yaparak target (gözlemlenen obje) değişkenimizi kendi kullanacağımız değişkene atamak vardır.
 
```C#
 CustomEditorTarget myTarget = (CustomEditorTarget)target;
```
veya as operatörü ile
```C#
 CustomEditorTarget myTarget = target as CustomEditorTarget;
```

![image](https://user-images.githubusercontent.com/75368035/117086868-7e23cc00-ad56-11eb-92f3-4e0e3c25f226.png)

 Hadi bunu yaptıktan sonra ne elde ettiğimizi bir test edelim. CustomEditorScript'ime yani editor scripti hazırladığım ana scriptime 1-2 adet değişken eklemek istiyorum. Ben bir float, int ve bool tanımladım.
 
 ```C#
 public class CustomEditorTarget : MonoBehaviour
{
    public int myTestValue;
    public float myTestSpeed;
    public bool myBool=true;
}
```

CustomEditorScript'imize geri dönelim ve Console penceremize, gerçekten aldığımız obje ile bağlantılı değerleri yazdıracak mı görmek için bir kaç Debug.Log() atalım.

```C#
Debug.Log("Objenin Adı: " + myTarget.gameObject.name);
Debug.Log("Objenin Pozisyonu: " + myTarget.transform.position);
Debug.Log("Objenin İçindeki int Değer: " + myTarget.myTestValue);
Debug.Log("Objenin İçindeki float Değer: " + myTarget.myTestSpeed);
Debug.Log("Objenin İçindeki bool Değer: " + myTarget.myBool);     
```

Şeklinde 5 adet Debug.Log() attım ve hiyerarşiden objeyi seçtiğim zaman Console'umda gördüğüm sonuç şu şekilde;

![image](https://user-images.githubusercontent.com/75368035/117087999-e32cf100-ad59-11eb-92e8-ef68a8602fca.png)

Inspectorda şu an görsel düzeyde bir şey göremesek bile OnInspectorGUI() içerisinde scriptimize eriştik gibi duruyor. Bu noktada dikkatimi çeken bir diğer şey Console penceremize bu çıktıların defalarca basılması. Bir iki ufak testten sonra şu sonuçlara vardım. Her bir değer değiştiğinde, faremi inspector penceresine getirip çıkardığımda, pencerelerin boyutuyla oynadığımda veya hiyerarşiden sırayla scripitimizin bulunduğu obje ile başka bir obje arasında gidip geldiğimde defalarca Consol'a bu Debug.Log()'ları yeniden bastığını farkettim. Bunun OnInspectorGUI() fonksiyonumuzun bağıl olduğu çağırılma durumları ve sayısı ile yani inspector'ün yeniden çizdirilme hızı ile alakalı olduğunu düşünüyorum.

---------------------------------------------------------------------------------------

 Evet, aslında buraya kadar her şey aklınızda oturduysa ve aklınızda soru işareti kalmadıysa Editor Scriptlemenin en zor kısmını kavradık diyebilirim. Geriye sadece bahsettiğimiz GUI kütüphanelerindeki metotların nasıl kullanıldığını öğrenmek ve pekiştirmek kalıyor. Bundan sonra öğreneceklerimiz aslında [Unity Script Reference](https://docs.unity3d.com/ScriptReference/index.html) 'dan alınmış metotlar ve bunların kullanım örneklerinden başka bir şey değildir. 
 
---------------------------------------------------------------------------------------

### Phase 4 (GUILayout & EditorGUILayout'tan Bir İki Metodu Tanıma ve Kullanım Şeklini Gözlemleme)

 Şimdi bir örnek ile başlayalım. En çok kullanılan, en sık ihtiyaç duyabileceğimiz GUI öğesinden yani Buton'dan başlamak istiyorum. Yukarıda yaptığımız hedef objemizi belirleme ve onun bilgilerini consola yazdırma kısmını bir metot haline getirip, sonrasında bu metotu inspector'ümüzdeki bir buton ile tetiklenebilir hale getirelim. 
 
 #### 1-Button

 Inspector'e bir button basmak için kullacağımız olan metot [GUILayout.Button()](https://docs.unity3d.com/ScriptReference/GUILayout.Button.html). Parametre olarak butonun resmini (Texture) veya buton üstünde gözükecek yazıyı (String) verebilirsiniz. Daha detaylı kullanımları için dökümantasyona göz atabilirsiniz. Kullanmadan önce tetiklemesini istediğimiz metotu ayarlayalım. Öncelikle debuglarımızı bir metot haline getirelim.
 
 ```C#
void ShowTargetInfo()
{
   Debug.Log("Objenin Adı: " + myTarget.gameObject.name);
   Debug.Log("Objenin Pozisyonu: " + myTarget.transform.position);
   Debug.Log("Objenin İçindeki int Değer: " + myTarget.myTestValue);
   Debug.Log("Objenin İçindeki float Değer: " + myTarget.myTestSpeed);
   Debug.Log("Objenin İçindeki bool Değer: " + myTarget.myBool);
}
```

 Sonrasında ise butona bu methodu bağlayalım.
 
 ```C#
if (GUILayout.Button("Show Target Info"))
{
  ShowTargetInfo();
}
```

Classımın ve Inspectorun son görünümü şu şekilde;

![image](https://user-images.githubusercontent.com/75368035/117586980-299c9a00-b124-11eb-818d-a23c79ff778a.png)
![image](https://user-images.githubusercontent.com/75368035/117587006-64063700-b124-11eb-808c-deb2853dcee9.png)

Artık Debug.Log()'larım butonuma her tıkladığımda çağırılacak. Bunun en güzel yanı; metodumun RunTime'da belirli bir yerde, belirli bir koşulda tetiklenmesini beklememe gerek olmaması. Benim için artık kolay erişilebilir ve anında tetiklenebilir bir kısayol var, sadece butona basılması. Hem Play modda hem de editör modunda istediğim zaman tetikleyip sonuçları gözlemleyebilirim. Bütün basit GUILayout ve EditorGUILayout metotlarını kullanarak amacınıza hizmet eden daha işlevsel daha büyük komplex editörler oluşturabilirsiniz. Nasıl bir editör oluşturacağınız tamamen sizin hayal gücünüze ve ihtiyacınıza kalmış durumda. Şimdi madde madde bütünü oluşturken ihtiyacımız olacak parçaları, yani bu metotları inceleyerek devam edelim.

#### 2-GUILayout.BeginHorizontal(); & GUILayout.BeginVertical();

 GUILayout.BeginHorizontal() ve GUILayout.BeginVerdical() kullanarak, ekleyeceğiniz GUI elementlerinin hiyerarşik olarak yan yana mı yoksa alt alta mı çizdirileceğini kontrol edebilirsiniz. Örneğin birbiri ile alakalı görevlere sahip buton veya öğeleri aşağıdaki gibi yan yana gruplandırmak isteyebilirsiniz. Tek dikkat etmeniz gereken başlattığınız pozlamaları GUILayout.EndHorizontal(); ve GUILayout.EndVertical(); ile sonlandırmak. Ayrıca Begin metotlarına parametre olarak başlattığınız bu alanın ne kadar yer kaplayacağını
 GUILayout.Height(100), GUILayout.Width(256) şeklinde yollayabilirsiniz. Tüm Layout fonksiyonlarının GUILayoutOption[] options şeklinde bir params parametresi vardır. Diğer GUILayoutOption parametrelerini [buradan](https://docs.unity3d.com/ScriptReference/GUILayoutOption.html) inceleyebilirsiniz.

![image](https://user-images.githubusercontent.com/75368035/117590269-4a221f80-b137-11eb-8acf-e9ec08770ebc.png)

 #### 3-GUILayout.Box();
 
  GUILayout.Box() kullanarak, inspector'a bir kutu çizdirebilirsiniz. Parametre olarak aynı buton gibi bir Texture veya String alabilir. Fakat buton gibi tıklanabilir bir öğe değildir. Statik bir görüntüden ibarettir.
  
  ![image](https://user-images.githubusercontent.com/75368035/117590428-14ca0180-b138-11eb-91dd-c021ef35fa5e.png)
  
 #### 4-GUILayout.VerticalSlider() & GUILayout.HorizontalSlider()
 
  VerticalSlider() ve HorizontalSlider() slider ile inspector'a yatay veya dikey doğrultuda bir slider çizdirebilirsiniz. Sırası ile değer, minValue ve maxValue parametrelerini almaktadır. 
  
   ```C#
float scrollPosition = 0;
public override void OnInspectorGUI()
{
  // Dikey Slider
  scrollPosition = GUILayout.VerticalSlider(scrollPosition, 0, 100, GUILayout.Height(50), GUILayout.Width(20));
  // Yatay Slider
  scrollPosition = GUILayout.HorizontalSlider(scrollPosition, 0f, 100f, GUILayout.Height(20), GUILayout.Width(100));
}
```
 Şeklinde kullanırsanız kendi aldığı değeri tutacaktır. Slider'dan alınan değeri daha sonra istediğiniz bir değişkene atayabilir veya metot içinde başka bir değişkenin buna bağıl yeni değerini hesaplayıp saklayabilir / inspector'a basabiliirsiniz.
 
 ### Phase 5 (Sonuç - Günün Sonu)
 
  Kavramsal olarak bir çok şeyi anladığımıza ve mantığını oturttuğumuza inanıyorum. Özellikle bir tool geliştirip bu tool'a has bir Window/Inspector vs. geliştirmeyecekseniz. Editor Scriptlemeye derinlemesine girmenize gerek yoktur. Odin - Inspector and Serializer pakedi ile tek bir [Button] attribute'ü atarak metodunuzu inspactora basabilirsiniz. Buraya kadar anladıklarınız ve öğrendiklerinizden yola çıkarak basit şeyleri, Unity dökümantasyondan da destek alarak halledebilirsiniz. Yine de temel atmanıza yardımcı olacak bir şeyler kattığıma inanıyorum.
  
   Onur Özdemir.
 
 
  
 
 

