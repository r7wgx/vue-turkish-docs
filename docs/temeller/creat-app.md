# Vue Uygulaması Oluşturma

## Uygulama örneği

Her Vue uygulaması createApp fonksiyonu ile yeni bir uygulama örneği oluşturarak başlar:

```javascript
import { createApp } from 'vue'

const app = createApp({
/* kök bileşen seçenekleri */
})

```

## Kök Bileşen

createApp'e aktardığımız nesne aslında bir bileşendir. Her uygulama, diğer bileşenleri alt bileşenleri olarak içerebilen bir "kök bileşen" gerektirir.

Tek Dosyalı Bileşenler kullanıyorsanız, genellikle kök bileşeni başka bir dosyadan içe aktarırız:

```javascript
import { createApp } from 'vue'
// kök bileşen App'i tek dosyalı bir bileşenden içe aktarın.
import App from './App.vue'

const app = createApp(App)
```

Bu kılavuzdaki birçok örnek yalnızca tek bir bileşene ihtiyaç duysa da, çoğu gerçek uygulama iç içe geçmiş, yeniden kullanılabilir bileşenlerden oluşan bir ağaç şeklinde düzenlenir. Örneğin, bir Todo uygulamasının bileşen ağacı aşağıdaki gibi görünebilir:

```
App (root component)
├─ TodoList
│  └─ TodoItem
│     ├─ TodoDeleteButton
│     └─ TodoEditButton
└─ TodoFooter
   ├─ TodoClearButton
   └─ TodoStatistics
```

Kılavuzun ilerleyen bölümlerinde, birden fazla bileşenin nasıl tanımlanacağını ve birlikte nasıl oluşturulacağını tartışacağız. Bundan önce, tek bir bileşenin içinde neler olduğuna odaklanacağız.

## Uygulamanın Montajı

Bir uygulama örneği, .mount() yöntemi çağrılana kadar hiçbir şey oluşturmaz. Bu yöntem, gerçek bir DOM öğesi ya da bir seçici dizesi olabilen bir "container" argümanı bekler:

```html
<div id="app"></div>
```

```javascript
app.mount('#app')
```

Uygulamanın kök bileşeninin içeriği konteyner öğesinin içinde oluşturulur. Konteyner öğesinin kendisi uygulamanın bir parçası olarak kabul edilmez.

`.mount()` yöntemi her zaman tüm uygulama yapılandırmaları ve varlık kayıtları tamamlandıktan sonra çağrılmalıdır. Ayrıca, varlık kayıt yöntemlerinin aksine, geri dönüş değerinin uygulama örneği yerine kök bileşen örneği olduğunu unutmayın.

## In-DOM Kök Bileşen Şablonu

Vue'yu derleme adımı olmadan kullanırken, kök bileşenimizin şablonunu doğrudan mount container'ın içine yazabiliriz:

```html
<div id="app">
  <button @click="count++">{{ count }}</button>
</div>
```

```javascript
import { createApp } from 'vue'

const app = createApp({
  data() {
    return {
      count: 0
    }
  }
})

app.mount('#app')
```

Kök bileşenin zaten bir şablon seçeneği yoksa Vue otomatik olarak kapsayıcının `innerHTML`'sini `template` olarak kullanacaktır.

## Uygulama Yapılandırmaları

Uygulama örneği, örneğin tüm alt bileşenlerden gelen hataları yakalayan uygulama düzeyinde bir hata işleyicisi tanımlamak gibi birkaç uygulama düzeyinde seçeneği yapılandırmamıza olanak tanıyan bir `.config` nesnesi sunar:

```javascript
app.config.errorHandler = (err) => {
  /* error yakalama */
}
```

Uygulama örneği, uygulama kapsamındaki varlıkları kaydetmek için birkaç yöntem de sağlar. Örneğin, bir bileşenin kaydedilmesi:

```javascript
app.component('TodoDeleteButton', TodoDeleteButton)
```

Bu, `TodoDeleteButton`'ı uygulamamızın herhangi bir yerinde kullanılabilir hale getirir. Bileşenler ve diğer varlık türleri için kayıt konusunu kılavuzun ilerleyen bölümlerinde ele alacağız. Ayrıca API referansında uygulama örneği API'lerinin tam listesine göz atabilirsiniz.

Uygulamayı monte etmeden önce tüm uygulama konfigürasyonlarını uyguladığınızdan emin olun!

## Çoklu uygulama örnekleri

Aynı sayfada tek bir uygulama örneği ile sınırlı değilsiniz. `createApp` API'si, her biri yapılandırma ve global varlıklar için kendi kapsamına sahip birden fazla Vue uygulamasının aynı sayfada birlikte var olmasına olanak tanır:

```javascript
const app1 = createApp({
  /* ... */
})
app1.mount('#container-1')

const app2 = createApp({
  /* ... */
})
app2.mount('#container-2')
```

Sunucu tarafından oluşturulan HTML'yi geliştirmek için Vue kullanıyorsanız ve büyük bir sayfanın yalnızca belirli bölümlerini kontrol etmek için Vue'ya ihtiyacınız varsa, tüm sayfaya tek bir Vue uygulama örneği yüklemekten kaçının. Bunun yerine, birden fazla küçük uygulama örneği oluşturun ve bunları sorumlu oldukları öğelere monte edin.
