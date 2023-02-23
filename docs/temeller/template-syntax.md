# Template Syntax

Vue, işlenen DOM'u temel bileşen örneğinin verilerine bildirimsel olarak bağlamanıza olanak tanıyan HTML tabanlı bir şablon sözdizimi kullanır. Tüm Vue şablonları, teknik özelliklerle uyumlu tarayıcılar ve HTML ayrıştırıcıları tarafından ayrıştırılabilen sözdizimsel olarak geçerli HTML'dir.

Vue, şablonları yüksek düzeyde optimize edilmiş JavaScript koduna derler. Reaktivite sistemi ile birlikte Vue, uygulama durumu değiştiğinde yeniden işlenecek minimum sayıda bileşeni akıllı bir şekilde belirleyebilir ve minimum miktarda DOM manipülasyonu uygulayabilir.

Virtual DOM kavramlarına aşina iseniz ve JavaScript'in ham gücünü tercih ediyorsanız, isteğe bağlı JSX desteği ile şablonlar yerine doğrudan render fonksiyonları da yazabilirsiniz. Ancak, şablonlarla aynı düzeyde derleme zamanı optimizasyonlarına sahip olmadıklarını unutmayın.

## Metin Enterpolasyonu

Veri bağlamanın en temel şekli "Mustache" sözdizimini (çift küme parantezi) kullanan metin enterpolasyonudur:

```html
<span>Message: {{ msg }}</span>
```

Bıyık etiketi, ilgili bileşen örneğindeki msg özelliğinin değeriyle değiştirilecektir. Ayrıca msg özelliği her değiştiğinde güncellenecektir.

## Ham HTML

Çift bıyıklar verileri HTML olarak değil düz metin olarak yorumlar. Gerçek HTML çıktısı almak için `v-html` yönergesini kullanmanız gerekecektir:

```html
<p>Metin enterpolasyonu kullanma: {{ rawHtml }}</p>

<p>v-html yönergesi kullanılıyor:
<span v-html="rawHtml"></span>
</p>
```

> Metin enterpolasyonu kullanma: <span style="color: red">Bu kırmızı olmalı.</span>
> v-html yönergesi kullanılıyor: Bu kırmızı olmalıdır.

Burada yeni bir şeyle karşılaşıyoruz. Gördüğünüz `v-html` niteliğine yönerge denir. Yönergeler, Vue tarafından sağlanan özel nitelikler olduklarını belirtmek için `v-` ile ön ek alır ve tahmin edebileceğiniz gibi, işlenen DOM'a özel reaktif davranış uygularlar. Burada temel olarak "bu öğenin iç HTML'sini geçerli etkin örnek üzerindeki `rawHtml` özelliği ile güncel tut" diyoruz.

`Span`'ın içeriği rawHtml özelliğinin değeriyle değiştirilecek ve düz HTML olarak yorumlanacaktır - veri bağları yok sayılır. Şablon parçalarını oluşturmak için `v-html` kullanamayacağınızı unutmayın, çünkü Vue dize tabanlı bir şablonlama motoru değildir. Bunun yerine, UI yeniden kullanımı ve bileşimi için temel birim olarak bileşenler tercih edilir.

::: warning Güvenlik Uyarısı
Web sitenizde dinamik olarak rastgele HTML oluşturmak çok tehlikeli olabilir çünkü kolayca XSS güvenlik açıklarına yol açabilir. Sadece güvenilir içerikte v-html kullanın ve asla kullanıcı tarafından sağlanan içerikte kullanmayın.
:::

## Attribute Bindings

süslü parentez attribute içinde kullanılamaz. Bunun yerine, bir `v-bind` yönergesi kullanın:

```html
<div v-bind:id="dynamicId"></div>
```

`v-bind`yönergesi Vue'ya elemanın id niteliğini bileşenin `dynamicId` özelliği ile senkronize tutması talimatını verir. Bağlanan değer `null` veya tanımsızsa, öznitelik işlenen öğeden kaldırılır.

### Shorthand

`v-bind` çok yaygın olarak kullanıldığından, özel bir kısaltma sözdizimine sahiptir:

```html
<div :id="dynamicId"></div>
```

ile başlayan öznitelikler normal HTML'den biraz farklı görünebilir, ancak aslında öznitelik adları için geçerli bir karakterdir ve Vue destekli tüm tarayıcılar bunu doğru şekilde ayrıştırabilir. Ayrıca, son işlenen biçimlendirmede görünmezler. Kısaltma sözdizimi isteğe bağlıdır, ancak daha sonra kullanımı hakkında daha fazla bilgi edindiğinizde muhtemelen takdir edeceksiniz.

> Kılavuzun geri kalanında, Vue geliştiricileri için en yaygın kullanım bu olduğundan, kod örneklerinde steno sözdizimini kullanacağız.

### Boolean Attributes

Boolean nitelikleri, bir öğe üzerinde bulunmalarıyla doğru/yanlış değerlerini gösterebilen niteliklerdir. Örneğin, `disabled` en sık kullanılan boolean niteliklerinden biridir.

`v-bind` bu durumda biraz farklı çalışır:

```html
<button :disabled="isButtonDisabled">Button</button>
```

`isButtonDisabled` doğru bir değere sahipse disabled niteliği dahil edilecektir. `<button disabled="">` ile tutarlılığı korumak için değer boş bir dizeyse de dahil edilecektir. Diğer hatalı değerler için öznitelik atlanacaktır.

### Birden Çok Attribute Dinamik Olarak Bağlama

Birden fazla özniteliği temsil eden ve aşağıdaki gibi görünen bir JavaScript nesneniz varsa:

```javascript
data() {
  return {
    objectOfAttrs: {
      id: 'container',
      class: 'wrapper'
    }
  }
}
```

Bağımsız değişken olmadan `v-bind` kullanarak bunları tek bir öğeye bağlayabilirsiniz:

```html
<div v-bind="objectOfAttrs"></div>
```

## JavaScript İfadelerini Kullanma

Şimdiye kadar şablonlarımızda yalnızca basit özellik anahtarlarına bağlama yaptık. Ancak Vue aslında tüm veri bağlamaları içinde JavaScript ifadelerinin tüm gücünü destekler:

```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```

Bu ifadeler, geçerli bileşen örneğinin veri kapsamında JavaScript olarak değerlendirilecektir.

Vue şablonlarında, JavaScript ifadeleri aşağıdaki konumlarda kullanılabilir:

- Metin içi enterpolasyonlar (süslü parentez)
- Herhangi bir Vue atr öznitelik değerinde (`v-` ile başlayan özel öznitelikler)

### Yalnızca İfadeler

Her bağlama yalnızca tek bir ifade içerebilir. İfade, bir değere değerlendirilebilen bir kod parçasıdır. Basit bir kontrol, `return` sonra kullanılıp kullanılamayacağıdır.

Bu nedenle, aşağıdakiler ÇALIŞMAYACAKTIR:

```template
<!-- this is a statement, not an expression: -->
{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
{{ if (ok) { return message } }}
```

### fonksiyon Çağırma

Bir bağlama ifadesinin içinde bileşene açık bir yöntemi çağırmak mümkündür:

```html
<span :title="toTitleDate(date)">
  {{ formatDate(date) }}
</span>
```

Functions called inside binding expressions will be called every time the component updates, so they should not have any side effects, such as changing data or triggering asynchronous operations.

::: info TIP
Bağlama ifadeleri içinde çağrılan işlevler, bileşen her güncellendiğinde çağrılacağından, verileri değiştirme veya eşzamansız işlemleri tetikleme gibi herhangi bir yan etkiye sahip olmamalıdır.
:::

### Kısıtlı Küresel Erişim

Şablon ifadeleri korumalıdır ve yalnızca kısıtlı bir global listesine erişebilir. Liste, `Math` ve `Date` gibi yaygın olarak kullanılan yerleşik globalleri gösterir.

Listeye açıkça dahil edilmeyen globaller, örneğin pencereye kullanıcı tarafından eklenen özellikler, şablon ifadelerde erişilebilir olmayacaktır. Bununla birlikte, app.config.globalProperties'e ekleyerek tüm Vue ifadeleri için ek globalleri açıkça tanımlayabilirsiniz.

## Directives

Yönergeler, `v-` önekine sahip özel niteliklerdir. Vue, yukarıda tanıttığımız `v-html` ve `v-bind` dahil olmak üzere bir dizi yerleşik yönerge sağlar.

Yönerge öznitelik değerlerinin tek JavaScript ifadeleri olması beklenir (daha sonra ilgili bölümlerinde ele alınacak olan `v-for`, `v-on` ve `v-slot` hariç). Bir yönergenin görevi, ifadesinin değeri değiştiğinde DOM'a tepkisel olarak güncellemeler uygulamaktır. Örnek olarak `v-if`'i ele alalım:

```html
<p v-if="seen">Now you see me</p>
```

Burada, `v-if` yönergesi, görülen ifadenin değerinin doğruluğuna bağlı olarak `<p>` öğesini kaldırır/ekler.

### Arguments

Bazı yönergeler, yönerge adından sonra iki nokta üst üste ile gösterilen bir "argüman" alabilir. Örneğin, `v-bind` yönergesi bir HTML niteliğini tepkisel olarak güncellemek için kullanılır:

```html
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>
```

Burada href, `v-bind` yönergesine elemanın href özniteliğini url ifadesinin değerine bağlamasını söyleyen argümandır. Kısaltmada, argümandan önceki her şey (yani, `v-bind`) tek bir karaktere, `:`'ya sıkıştırılır.

Başka bir örnek de DOM olaylarını dinleyen `v-on` yönergesidir:

```html
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>
```

Burada argüman, dinlenecek olay adıdır: click. `v-on`'un buna karşılık gelen bir kısaltması vardır, yani `@` karakteri. Olay işleme hakkında daha ayrıntılı olarak konuşacağız.

### Dinamik Argümanlar

Bir JavaScript ifadesini köşeli parantezlerle sararak bir yönerge bağımsız değişkeninde kullanmak da mümkündür:

```html
<!--
Argüman ifadesinde bazı kısıtlamalar olduğunu unutmayın,
Aşağıdaki "Dinamik Bağımsız Değişken Değer Kısıtlamaları" ve "Dinamik Bağımsız Değişken Sözdizimi Kısıtlamaları" bölümlerinde açıklandığı gibi.
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- shorthand -->
<a :[attributeName]="url"> ... </a>

```

Burada, `attributeName` dinamik olarak bir JavaScript ifadesi olarak değerlendirilir ve değerlendirilen değeri bağımsız değişken için son değer olarak kullanılır. Örneğin, bileşen örneğinizin değeri `"href"` olan attributeName adlı bir veri özelliği varsa, bu bağlama `v-bind:href` ile eşdeğer olacaktır.

Benzer şekilde, bir işleyiciyi dinamik bir olay adına bağlamak için dinamik argümanlar kullanabilirsiniz:

```html
<a v-on:[eventName]="doSomething"> ... </a>

<!-- shorthand -->
<a @[eventName]="doSomething">

```

Bu örnekte, eventName'in değeri "focus" olduğunda, v-on:[eventName], v-on:focus ile eşdeğer olacaktır.

Dinamik Argüman Değeri Kısıtlamaları

Dinamik bağımsız değişkenlerin, `null` hariç olmak üzere, bir dize olarak değerlendirilmesi beklenir. Bağlamayı açıkça kaldırmak için özel `null` değeri kullanılabilir. Dize olmayan diğer tüm değerler bir uyarı tetikleyecektir.

### Dinamik Argüman Sözdizimi Kısıtlamaları

Dinamik Bağımsız Değişken Sözdizimi KısıtlamalarıDinamik bağımsız değişken ifadelerinde bazı sözdizimi kısıtlamaları vardır, çünkü boşluk ve tırnak işaretleri gibi belirli karakterler HTML öznitelik adları içinde geçersizdir. Örneğin, aşağıdaki geçersizdir:

```html
<!-- This will trigger a compiler warning. -->
<a :['foo' + bar]="value"> ... </a>
```

Karmaşık bir dinamik argüman geçirmeniz gerekiyorsa, kısa bir süre sonra ele alacağımız hesaplanmış bir özellik kullanmak muhtemelen daha iyidir.

`In-DOM` şablonları (doğrudan bir HTML dosyasına yazılan şablonlar) kullanırken, tarayıcılar öznitelik adlarını küçük harfe zorlayacağından, anahtarları büyük harflerle adlandırmaktan da kaçınmalısınız:

```html
<a :[someAttr]="value"> ... </a>
```

Yukarıdakiler DOM içi şablonlarda `:[someattr]` olarak dönüştürülecektir. Bileşeninizde someattr yerine someAttr özelliği varsa, kodunuz çalışmayacaktır. Tek Dosyalı Bileşenler içindeki şablonlar bu kısıtlamaya tabi değildir.

### Değiştiriciler

Değiştiriciler, bir yönergenin özel bir şekilde bağlanması gerektiğini belirten ve nokta ile gösterilen özel soneklerdir. Örneğin, .prevent değiştiricisi v-on yönergesine tetiklenen olayda event.preventDefault() işlevini çağırmasını söyler:

```html
<form @submit.prevent="onSubmit">...</form>
```

Daha sonra `v-on` ve `v-model`için diğer değiştirici örneklerini bu özellikleri incelerken göreceksiniz.

Ve son olarak, işte tam yönerge sözdiziminin görselleştirilmiş hali:

![img](https://vuejs.org/assets/directive.69c37117.png)
