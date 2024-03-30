## PROJE VE UYGULAMAYI OLUŞTURALIM
Projeyi aşağıdaki komut ile oluşturalım:
```console
$ django-admin startproject mysite 
```
Uygulamayı aşağıdaki komut ile oluşturalım:
```console
$ python manage.py startapp polls
```

## GÖRÜNÜM OLUŞTURMA
Yazdığımız komutları web tarayıcısına aktarmak için views.py dosyası üzerinde işlem gerçekleştiriyoruz. Aşağıda örnek bir görünüm kodu mevcut:

```python
from django.http import HttpResponse 
 
 
def index(request): 
    return HttpResponse("Hello, world. You're at the polls index.")
```

Django da oluşturabileceğimiz en basit görünüm diğer programlama dillerinde de olduğu gibi “Hello World”. Yazılan bu görünümü web tarayıcısında url adresini girerek çağırmak için urls.py dosyasını kullanacağız. Oluşturulan her uygulamanın kendine ait bir urls.py dosyası olması, kodun okunabilirliği ve karmaşıklığını önleyici bir önlem olacaktır. Uygulama klasörü içerisine urls.py dosyası oluşturacağız, iskelet aşağıdaki gibi olacaktır:
 
- polls/
    - \_\_init__.py
    - admin.py
    - apps.py
    - migrations/
        - \_\_init__.py
    - models.py
    - tests.py
    - urls.py
    - views.py 
 
Dosyayı oluşturduktan sonra aşağıdaki komutları urls.py dosyamıza ekleyerek projemizin root URL'sine index fonksiyonumuzun ürettiği görünümü aktaracağız:

```python
from django.urls import path 
 
from . import views 
 
urlpatterns = [ 
    path('', views.index, name='index'), 
] 
```
 
polls/urls.py dosyamızda yaptığımız değişiklikleri projemiz yukarıdaki güncellemeler ile görmeyecektir. Bu yüzden projenin ana urls.py dosyası içerisinde aşağıdaki tanımlamayı gerçekleştireceğiz:

```python
from django.contrib import admin 
from django.urls import include, path 
 
urlpatterns = [ 
    path('polls/', include('polls.urls')), 
    path('admin/', admin.site.urls), 
] 
```

include() fonksiyonu diğer URL konfigürasyonlarına referans vermemizi sağlıyor. Diğer bir kolaylığı da tak-çalıştır yapısı sunması. Herhangi bir uygulamayı kullanmayacak isek ve bu uygulamanın url'lerini kullanmayacak isek, dosyayı silmek yerine root urls.py dosyasından ilgili satırı silerek işlem gerçekleştirebiliriz.

Görünümü test etmek amacı ile önce sunucuyu aşağıdaki komut ile tekrar çalıştıralım ve web tarayıcısından http://localhost:8000/polls/ linkini ziyaret edelim:


```console
$ python manage.py runserver 
```

Her şey yolunda gitti ise web tarayıcısında “Hello, world. You’re at the polls index.” yazısını göreceğiz. 

Linklerimizi oluştururken path() fonksiyonunu kullanıyoruz. Bu fonksiyon route ve view olmak üzere 2 adet zorunlu parametre alıyor. Zorunlu olmayan parametreler ise kwargs ve name parametreleri.
- route parametresi URL pattern'i içeren parametre. Django parametreleri okurken yukarıdan aşağıya doğru gidiyor ve uyumlu olan url'i buluyor ve çalıştırıyor.
- view parametresi uyumlu url bulunduktan sonra çalıştıracağı view fonksiyonunu tanıyor.
- kwargs parametresi hedef görünümü bulmak için bir dictionary oluşturmamıza yarıyor.
- name parametresi şablon oluştururken içerisinde tanımladığımız linklerin hard-coded, yani her url tanımı değiştirildiğinde şablon üzerinden değiştirilmesi gerektiği hususunda tam tersine yardımcı olacaktır.

## VERİTABANI KURULUMU
Django varsayılan olarak SQLite veri tabanı kullanır. SQLite geliştirme aşaması ve basit web sitelerinde kullanmak üzere yeterli olacaktır, ancak büyük ölçekli web sitelerinde PostreSQL gibi daha ölçeklenebilir veri tabanları daha yardımcı olacaktır.

Proje ayarlarımızı gerçekleştirdiğimiz settings dosyası içerisinde DATABASE tanımlı olarak otomatik gelmektedir. 

Ayarlar dosyamız içerisinde TIME_ZONE tanımını Europe/Istanbul olarak günceller isek lokal zamandan faydalanırız. Yine bu dosya içerisinde INSTALLED_APPS bölümünde django projemizde kullandığımız uygulamalar mevcut. Bir uygulama birden fazla projede kullanılabilecek şekilde tasarlanır. Tak-çıkar yapısı diyebiliriz. Django da yeni bir proje yarattığımızda ön tanımlı olarak aşağıdaki uygulamalar gelir:
- django.contrib.admin – web sitesinin içeriği ve kullanıcıların yönetildiği platform.
- django.contrib.auth – kullanıcı hesapları üzerinde kimlik doğrulama gerçekleştiren uygulama.
- django.contrib.contenttypes – içerik tiplerinin yönetildiği bir çerçeve.
- django.contrib.sessions – oturumların yönetildiği çerçeve.
- django.contrib.messages – mesajların yönetildiği çerçeve.
- django.contrib.staticfiles – statik dosyaların yönetildiği çerçeve.

## MODEL OLUŞTURMAK
Modeller veri tabanı içerisindeki tablolara referans edecek alanları oluşturmamıza yarıyor. Django DRY (Don't Repeat Yourself) prensiplerini izliyor. Veri modellerimizi oluşturduktan sonra verilerimizi bu modellerden üretiyoruz. 

Anket uygulamasında iki adet modelimiz olacak: Question ve Choice. Question bir soru ve bir de yayınlanma tarihi alanlarını içerecek. Choice ise hem cevap metnini hem de oy verme özelliği içerecek. Polls uygulaması içerisindeki models.py modellerimizi oluşturmak için üretildi. Aşağıdaki komutları models.py dosyası içerisinde tanımlayalım:


```python
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```


Her model django.db.models.Model'i alan bir class ile temsil ediliyor. Her modelin kendine ait sınıf değişkenleri mevcut, ve her bir değişken bir veri tabanı alanına karşılık geliyor. Her alan Field sınıfından örnekleniyor. Örneğin CharField karakterleri girdiğimiz alan, DateTimeField ise tarih ve saatleri girdiğimiz ya da otomatik olarak ürettiğimiz alana karşılık geliyor. Oluşturulan her alanın adı veri tabanı tablosu adı olarak kaydediliyor. Örneğin question_text ve pub_date. Veri tabanı isimlerini kendimiz de oluşturabiliyoruz. Yukarıdaki örnekte pub_date alanı için ‘date published’ tablo ismi olarak oluşturuldu.

Her alanın kendilerine ait argümanları oluyor. CharField için max_length argümanının verilmesi zorunlu, vermez isek hata alıyoruz. Ayrıca alanlar içerisine bir çok opsiyonel argüman girebiliyoruz. Choice sınıfı içerisinde yer alan votes alanı için default=0 tanımı opsiyonel bir tanımdır. 

ForeignKey tanımı many-to-one ilişkisi oluşturmamızı sağlıyor. Yukarıdaki tanım "her Choice sadece bir Question ile bağlantılı olabilir" ilişkisini belirlemektedir.

## MODELLERİN VERİ TABANINA EKLENMESİ
Yukarıdaki tanımlamalarla hem bir veri tabanı tasarımı gerçekleştirdik hem de Question ve Choice nesnelerine ulaşmak için bir Python veri tabanı - erişim uygulama programlama arayüzü (API) oluşturduk.

İlk aşamada projenin oluşturduğumuz uygulamayı tanıması gerekiyor. Tanımlamayı settings.py dosyası içerisinde INSTALLED_APPS içerisine ‘polls.apps.PollsConfig’ yazarak yapıyoruz: 

```python
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

Eklemeyi gerçekleştirdikten sonra polls uygulamasının modellerini veri tabanına eklemek için aşağıdaki komutu çalıştırıyoruz:

```console
$ python manage.py makemigrations polls
```

Komutu verdikten sonra aşağıdaki gibi bir bildirim almamız gerekiyor:

```python
Migrations for 'polls':
  polls/migrations/0001_initial.py:
    - Create model Choice
    - Create model Question
    - Add field question to choice
```

**makemigrations** komutu ile Django'ya modellerimiz üzerinde değişiklik yaptığımızı ya da yeni bir model sınıfı eklediğimizi ve gerçekleştirdiğimiz değişiklikleri yazmak istediğimizi söylüyoruz. 

Eğer manuel olarak müdahale etmek istersek yukarıdaki çıktıdan görüldüğü gibi 0001_initial.py dosyamız üzerinde değişiklik gerçekleştirebiliriz. Ayrıca veri tabanı üzerinde gerçekleştirilecek değişiklikleri SQL komutları olarak görmek için aşağıdaki komutu giriyoruz:

```console
$ python manage.py sqlmigrate polls 0001
```

Yukarıdaki komutu girdikten sonra aşağıdaki gibi bir bildirim almamız gerekiyor:

```sql
BEGIN;
--
-- Create model Choice
--
CREATE TABLE "polls_choice" (
        "id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, 
        "choice_text" varchar(200) NOT NULL, 
        "votes" integer NOT NULL
);
--
-- Create model Question
--
CREATE TABLE "polls_question" (
        "id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, 
        "question_text" varchar(200) NOT NULL, 
        "pub_date" datetime NOT NULL
);
--
-- Add field question to choice
--
ALTER TABLE "polls_choice" RENAME TO "polls_choice__old";
CREATE TABLE "polls_choice" (
        "id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, 
        "choice_text" varchar(200) NOT NULL,
        "votes" integer NOT NULL,
        "question_id" integer NOT NULL REFERENCES "polls_question" ("id") DEFERRABLE INITIALLY DEFERRED
);
INSERT INTO "polls_choice" (
        "id", "choice_text",
        "votes", "question_id"
) SELECT "id", "choice_text", "votes", NULL FROM "polls_choice__old";
DROP TABLE "polls_choice__old";
CREATE INDEX "polls_choice_question_id_c5b4b260" ON "polls_choice" ("question_id");
COMMIT;
```

Oluşabilecek problemleri önceden belirlemek aşağıdaki komut opsiyonel olarak kullanılabilir:

```console
$ python manage.py check
```

Artık oluşturduğumuz modelleri veri tabanımıza migrate komutu ile yazdırmaya hazırız:

```console
$ python manage.py migrate
```

Herhangi bir sorun yoksa aşağıdaki bildirim ile veri tabanımıza başarılı bir şekilde yazdırmış olacağız:

```python
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Rendering model states... DONE
  Applying polls.0001_initial... OK
```
Migration kullanımı veri tabanını veya tabloları silmek yerine bunların yerine yenilerini oluşturmamızı, ayrıca veri tabanını canlı ortamda da veri kaybı yaşamadan değiştirmemizi sağlar. Migration kullanmamız gereken durumlar sıralı olarak:
- Modelleri değiştiriyoruz (models.py dosyası üzerinden),
- python manage.py makemigrations komutu ile gerçekleştirdiğimiz değişikler için migration oluşturuyoruz,
- python manage.py migrate komutu ile değişiklikleri veri tabanı üzerine yazıyoruz.

## API İLE NESNE İŞLEMLERİ
Django içerisinde tanımlı gelen manage.py dosyası ile proje klasörümüz içerisinde bir python kabuğu çalıştırabiliriz:

```console
$ python manage.py shell
```

Kabuğu çalıştırdıktan sonra aşağıdaki komutlarla nesneler üzerinde işlemler gerçekleştirebiliyoruz:

```python
>>> from polls.models import Question, Choice   # Model sınıflarını al.

# Henüz bir sorumuz yok.
>>> Question.objects.all()
<QuerySet []>

# Yeni bir soru oluşturacağız.
# Öncelikle zaman dilimi otomatik olarak settings dosyası içerisinde aktif
# hale getirilmişti. Django pub_date için tzinfo içeren datetime girmemizi
# bekleyecek. timezone.now() kullanımı datetime.datetime.now() nazaran daha
# doğru bir sonuç verecektir.
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# Oluşturulan soru nesnesini save() komutu ile veri tabanına kaydediyoruz.
>>> q.save()

# Oluşturulan soruya otomatik olarak bir IDNow it has an ID.
>>> q.id
1

# Model alanı değerlerine python nitelikleri kullanarak erişebiliriz.
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)

# Değerlerin niteliklerini değiştirebiliriz. Kaydetmek için save() komutu.
>>> q.question_text = "What's up?"
>>> q.save()

# objects.all() komutu veri tabanındaki tüm soruları gösterecek
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]>
```

<Question: Question object (1)> çıktısı nesnenin hangisi olduğunu görebilmemiz adına pek faydalı bir çıktı değil. Nesnelerin string olarak karşılıklarını görebilmek adına models.py dosyası içerisinde aşağıdaki tanımlamaları gerçekleştiriyoruz: 

```python
from django.db import models

class Question(models.Model):
    # ...
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    # ...
    def __str__(self):
        return self.choice_text
```

Str metodu python tarafından sunulmaktadır. Örnek olması adına bir tane de kendi oluşturduğumuz metodu nasıl oluşturacağımızı görelim:

```python
import datetime

from django.db import models
from django.utils import timezone


class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```

Değişiklikleri kaydettikten sonra tekrar python kabuğuna dönelim:

```python
>>> from polls.models import Question, Choice

# Str metodumuzun çalışıp çalışmadığını görelim.
>>> Question.objects.all()
<QuerySet [<Question: What's up?>]>

# Django veri tabanı üzerinde kullanacağımız zengin bir API sunmakta.
>>> Question.objects.filter(id=1)
<QuerySet [<Question: What's up?>]>
>>> Question.objects.filter(question_text__startswith='What')
<QuerySet [<Question: What's up?>]>

# Bu sene yayınlanan soruları görmek için aşağıdaki gibi bir tanım gireriz.
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
<Question: What's up?>

# Mevcut olmayan bir ID'yi çağırmak hata verecektir.
>>> Question.objects.get(id=2)
Traceback (most recent call last):
    ...
DoesNotExist: Question matching query does not exist.

# Django primary-key aramaları için bir kısayol sunmaktadır. 
# Yapı olarak Question.objects.get(id=1) ile aynıdır.
>>> Question.objects.get(pk=1)
<Question: What's up?>

# Kendi oluşturduğumuz metodun çalışıp çalışmadığını test edelim.
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
True

# Create komutu yeni bir Choice objesi oluşturacak, INSERT komutunu
# çalıştıracak ve oluşturulan choice mevcut ve ulaşılabilir bir choice
# havuzu içerisine koyacak. Ardından yeni oluşturulan Choice nesnesini
# ekrana bildirim olarak verecek. Django ForeignKey ilişkisinde one tarafta
# kalan nesne için oluşturulan kümeyi oluşturuyor.
>>> q = Question.objects.get(pk=1)

# Herhangi bir choice var mı yok mu kontrol edelim.
>>> q.choice_set.all()
<QuerySet []>

# Üç adet choice oluşturalım..
>>> q.choice_set.create(choice_text='Not much', votes=0)
<Choice: Not much>
>>> q.choice_set.create(choice_text='The sky', votes=0)
<Choice: The sky>
>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# Choice nesneleri Question nesnelerine erişmek için bir API'ye mevcut.
>>> c.question
<Question: What's up?>

# Question nesneleri Choice nesnelerine erişebilir.
>>> q.choice_set.all()
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
>>> q.choice_set.count()
3

# İlişkileri birbirinden ayırmak için iki alttan çizgi kullanırız.
# Ayırmanın sonu yok, istediğimiz kadar birbiri ardına ekleyebiliyoruz.
# Yayınlanma tarihi bu yıl olan tüm soruların cevaplarını yazdır dediğimizde:
>>> Choice.objects.filter(question__pub_date__year=current_year)
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# Oluşturduğumuz choice nesnelerinden birini delete() komutu kullanarak silelim.
>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>> c.delete()
```

## DJANGO ADMIN ARAYÜZÜ - KULLANICI OLUŞTURMA
Admin arayüzü kullanıcı hesaplarının yönetimi ve admin kullanıcısı tarafından site içeriğini yönetmek amacı ile otomatik olarak oluşturulmaktadır. Aslen web sitesi yönetimi için oluşturulmuş özel alan diyebiliriz.

Admin arayüzüne giriş yapmak için bir admin kullanıcısı oluşturmamız gerekmektedir. Aşağıdaki komut ile bir admin kullanıcısı oluştururuz:

```python
$ python manage.py createsuperuser
Enter your desired username and press enter.
Username: admin
You will then be prompted for your desired email address:
Email address: admin@example.com
The final step is to enter your password. You will be asked to enter your password twice, the second time as a confirmation of the first.
Password: **********
Password (again): *********
Superuser created successfully.
```

Kullanıcı oluşturduktan sonra sunucumuzu tekrar çalıştıralım ve web tarayıcımızı açarak http://127.0.0.1:8000/admin/ adresini ziyaret edelim. Web sitesi yönetim ekranın erişmek için bizden kullanıcı adı ve şifre talep edilmektedir.

Giriş yaptıktan sonra AUTHENTICATION AND AUTHORIZATION tablosu bizi karşılayacaktır. Auth uygulaması ile bu tablo ve içerikleri oluşturulmaktadır.

Model nesnelerini admin konsolu üzerinden yönetebilmek mümkün. Bunun için admin.py dosyası içerisine aşağıdaki komutları eklememiz yeterli olacaktır:

```python
from django.contrib import admin

from .models import Question

admin.site.register(Question)
```

Admin konsolu üzerinden Question'a tıkladığımızda sağ yukarıda Add Question butonu görürüz. Daha önceden oluşturduğumuz “What's up?" sorusunun ise solunda yer alan kutuya tıklayıp yukarısında yer alan Action: kutucuğuna basarak soruları silebiliriz. Kısacası bu ekran CRUD (Create, Read, Update, Delete) işlemlerini gerçekleştirebileceğimiz alan.

“What's up?” sorusuna bastığımızda soruyu güncelleyeceğimiz alan içerisinde her bir alan tipinin farklı bir HTML input widget özelliği ile oluşturulduğunu görürüz. Date published alanı takvim alanı olarak gelmekte ve yanındaki takvim ikonu ile full özellikli tarih seçebilmekteyiz.

Soru oluşturma tarihi time zone üzerinden beslenmekte, doğru tarihi ve saati yansıtması adına ayarlar dosyası içerisinde TIME_ZONE değişkenini "Europe/Istanbul" yapmamız gerekmektedir.

## GÖRÜNÜM ÖZELLİKLERİ
Anket uygulamasına yönelik 4 adet görünüm oluşturacağız:
- Question “index” sayfası – en son yayınlanan soruları göreceğiz.
- Question “detail” sayfası – soruyu göreceğiz, oy verebileceğimiz bir form olacak.
- Question “results” sayfası – ilgili soru için sonuçları göreceğiz
- Vote action – ilgili sorunun ilgili yanıtlarına kaç adet oy verildiğini göreceğiz.

Django da web sayfaları ve diğer tüm görünümler views.py dosyası aracılığı ile yönetiliyor. Her bir görünüm basit bir python fonksiyonu ile yönetiliyor (ya da sınıf bazlı görünümler ile bir metod ile). Django seçtiğimiz görünümü URL'i inceleyerek önümüze döküyor. Web sayfalarında gezerken “ME2/Sites/dirmod.asp?sid=&type=gen&mod=Core+Pages&gid=A6CD4967199A42D9B65B1B” gibi URL uzantılarına denk geliyoruz. Django URL patternleri gördüğümüz bu formattan daha düzenli bir şekilde oluşturabilmemize olanak sağlıyor. URL pattern basit olarak URL'in bir formu olarak tanımlanabilir. Örneğin: /newsarchive/\<year>/\<month>/.

Bir URL'i görünüme aktarmak için URLconfs kullanıyoruz. URLconf'un asli görevi URL patternlerini görünüme maplemek..

## YENİ GÖRÜNÜM EKLEME
Yeni görünümlerimizi polls uygulamamızın views.py dosyası üzerinde tanımlayacağız. Önceki dosyamıza aşağıdaki komutları ekleyelim:

```python
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

Tanımladığımız bu görünümleri polls uygulamamız içerisindeki urls.py dosyası içerisinde tanımlamamız gerekecektir:

```python
from django.urls import path

from . import views

urlpatterns = [
    # ex: /polls/
    path('', views.index, name='index'),
    # ex: /polls/5/
    path('<int:question_id>/', views.detail, name='detail'),
    # ex: /polls/5/results/
    path('<int:question_id>/results/', views.results, name='results'),
    # ex: /polls/5/vote/
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

Web tarayıcısından örneğin /polls/34/ uzantısı detail fonksiyonunu çalıştırmaktadır. /polls/34/results/ ve /polls/34/vote/ linkleri de ilgili görünüm tarafından çağırılmaktadır.

Ayarlar dosyamız içerisinde ROOT_URLCONF diye bir ayar mevcut ve bu değişken ‘mysite.urls’ ile tanımlanıyor. Yukarıdaki tanımlamalarımızın çalışması bu tanımlama ile mevcut oluyor. Bu ayar urlpatterns adlı değişkeni buluyor ve pattern içerisindeki tanımları yukarıdan aşağı geziyor. Öncelikle proje içerisinde yer alan urls.py içerisinden polls/'u buluyor ve geri kalan kısım için polls uygulaması içerisinden devam ediyor. 34'ü detail isimli path'e giriyor ve views.py dosyası içerisinden detail fonksiyonunu çalıştırıyor. 

Her görünümün aslında iki görevi vardır; birincisi HttpResponse ile sayfa içeriğinde yer alan nesneyi kullanıcıya sunmak ya da nesneyi bulamadığında Http404 hatası iletmek. Geri kalan tüm tanımlamalar kullanıcının kendi isteğine kalmaktadır. Veri tabanı üzerinden kayıtları ekrana yansıtabiliriz ya da sadece görünüm ile kullanıcıya değişken olmayan bir kısım iletebiliriz. Django içerisinde tanımlı olarak gelen şablon sistemini kullanabiliriz, bir pdf dosyası, xml veya zip dosyası iletebiliriz. Tüm bu tanımlamalar python içerisinde yer alan kütüphaneleri kullanarak mümkün olmaktadır.

Daha önceden tanımladığımız index fonksiyonunu veri tabanı kayıtlarını çağıracak şekilde tekrar yazalım:

```python
from django.http import HttpResponse

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)
```

Yukarıdaki tanım aslında hard-coded yazılmış. Çünkü görünümü her değiştirmek istediğimizde python kodu üzerinde güncelleme yapmamız gerekecek. Django ile yüklü gelen şablon sistemi bu noktada yardımımıza koşacak. Öncelikle polls uygulamasının yer aldığı klasör içerisine “templates” adlı bir klasör oluşturacağız. Django otomatik olarak polls uygulaması içerisinde yer alan templates klasörüne bakacak. Projenin TEMPLATES ayarları bunu mümkün kılmakta. Proje ayar dosyası APP_DIRS opsiyonu True olan sunucu uygulamaları için bir DjangoTemplates ayarı uygulamaktadır. Diğer bir deyişle DjangoTemplates ayarı INSTALLED_APPS içerisindeki uygulamalar içerisinde “templates” alt klasörüne bakmaktadır.

Oluşturduğumuz templates klasörü içerisine “polls” klasörü tanımlayıp, bu klasör içerisinde index.html dosyası oluşturuyoruz. Şablon yazımında bu dosyayı polls/index.html olarak alacağız. Ayrıca index.html içeren başka bir uygulama içerisinde de index.html dosyası mevcut ise polls klasörünü oluşturmazsak hangi index dosyasının aktarılacağı konusunda Django'nun kafasını karıştırmış olacağız.

## ŞABLON OLUŞTURMA
Django şablon dili köşeli parantez ve yüzde simgesi ile açılıp bunlarla birlikte kapanacak şekilde oluşturuluyor. Değişken yazmak istediğimizde ise iki adet köşeli parantez kullanıyoruz. index.html dosyası içerisine aşağıda yer alan kodları yazalım:


```python
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```

Yazdığımız bu şablonu views.py içerisinde yer alan index fonksiyonumuza aşağıdaki şekilde bağlayabiliriz:

```python
from django.http import HttpResponse
from django.template import loader

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```

Yukarıdaki tanımlama polls/index.html dosyasını çağırıyor ve web sayfasına aktarıyor. Web tarayıcıdan /polls/ uzantısına gidersek daha önceden oluşturduğumuz soruları göreceğiz.

Django da görünümleri yazarken bir şablonun oluşturulması, context değişkeni ile aktarılacak içeriğin belirlenmesi ve render edilen şablon ile bir HttpResponse döndürülmesi çok rastlanan bir işlem silsilesi olduğundan dolayı bunun için render() fonksiyonu oluşturulmuş. Aşağıda render() kullanımı ile index fonksiyonumuzu tekrar yazıyoruz:

```python
from django.shortcuts import render

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

Kısayol olarak eklenen render() fonksiyonu ilk parametre olarak request nesnesini alıyor, ikinci parametre olarak çalıştıracağı şablonun ismini alıyor ve opsiyonel olarak değişkenleri içeren bir sözlük alıyor. Daha sonrasında HttpResponse nesnesi döndürüyor.

## 404 HATASI TASARIMI
Eğer fonksiyonun döndürebileceği bir nesne yok ise fonksiyonun 404 hatası vermesini sağlayacağız. Daha önceden views.py dosyası içerisinde tanımladığımız detail fonksiyonunu aşağıdaki gibi güncelliyoruz:

```python
from django.http import Http404
from django.shortcuts import render

from .models import Question
# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```

Yukarıdaki fonksiyonda detail.html şablonunu referans verdik. Hata almamak için bu şablonu da boş bile olsa index.html'in yanında oluşturmamız gerekecek. İlk aşamada dosya içerisine {{question}} tanımını eklemek işimizi görecektir.

Genel kullanıma bir başka örnekte nesneyi get() ile çağırmak ve nesne mevcut değil ise Http404 hatası göndermek. Django da bunun için de bir kısayol bulunmaktadır. Aşağıda detail() fonksiyonunu bu kısayolu kullanarak güncelliyoruz:

```python
from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

get_object_or_404 kullanımı ile model katmanını görünüm katmanına bağlıyoruz. Django bağlantı odaklı bir dizayn sunmaktadır. django.shortcuts modülü bu bağlantıları içermektedir. Nesne dışında filter() opsiyonu ile kullanılan get_list_or_404() kısayolu da bulunmaktadır. Eğer liste boş ise Http404 hatası gönderiyor.

## ŞABLON SİSTEMİNİN KULLANIMI
Görünüm dosyamız içerisinde oluşturduğumuz detail fonksiyonu içerisinde bahsedilen detail.html dosyamızı şablon dili kullanarak dolduralım:

```python
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```

Şablon sistemi nokta ile çağırma yapısı kullanmaktadır. question.question_text tanımında Django önce tanımlı sözlüğe bakıyor. Eğer başarılı olamaz ise niteliklere bakıyor. Eğer yine başarılı olamaz ise list-index arıyor. 

Daha önceden oluşturduğumuz index.html içerisinde hard-coded şablon kullanmıştık. Bu şablon \<li>\<a href="/polls/{{ question.id }}/">{{ question.question_text }}\</a>\</li> tanımı idi. Burada yaşayacağımız problem URL değiştirdiğimizde bu alan çalışmayacaktır. path() fonksiyonu içerisinde tanımladığımız isim parametresi ile hard-coded dizayndan kurtulmuş olacağız: \<li>\<a href="{% url 'detail' question.id %}">{{ question.question_text }}\</a>\</li>

## URL İSİMLERİNE İSİM ALANI TANIMLAMAK
Daha önceden Django'nun URL ararken INSTALLED_APPS üzerinden sırayla gittiğinden bahsetmiştik. Kafa karışıklığı olmaması adına templates klasörü altında bir de polls klasörü oluşturmuştuk. Anket uygulaması için tek uygulama oluşturduk, ancak daha fazla uygulaması olan bir web projesinde bu kafa karışıklığını önlemenin kolay bir yöntemi mevcut.

Bu yöntem URLconf içerisinde namespace yani isim alanı tanımlamak. Polls uygulaması içerisinde yer alan urls.py dosyası içerisinde aşağıdaki tanımlamayı yapalım:

```python
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/results/', views.results, name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

app_name = ‘polls’ tanımını gerçekleştirdikten sonra index.html içerisinde yer alan  "{% url 'detail' question.id %}" tanımını "{% url 'polls:detail' question.id %}" olarak güncelliyoruz. Böylece ilgili görünümün şablonu çalıştırırken hangi html dosyasına bakacağını tanımlamış oluyoruz. 

## BASİT BİR FORM OLUŞTURMAK
Anket uygulamamız için radio butonu içeren basit bir form oluşturacağız. Böylece web sayfası üzerinden yapılan her işlemi kayıt altına alabilecek duruma geleceğiz. İlgili güncellemeyi detail.html dosyası üzerinden gerçekleştiriyoruz:

```python
<h1>{{ question.question_text }}</h1>

{% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}

<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
{% for choice in question.choice_set.all %}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
{% endfor %}
<input type="submit" value="Vote" />
</form>
```

Yukarıda yazılan koda ilişkin detay bilgiler:
- Şablon her sorunun cevabı için bir radio butonu oluşturuyor. Her butonun değeri sorunun cevabının ID'si oluyor. Her bir radio butonunun adı “choice” olarak tanımlanıyor, böylece herhangi bir buton seçilip OK'ye basıldığında POST verisi ilgili cevaba iletilmiş olacak.
- Form aksiyon değişkeni {% url 'polls:vote' question.id %} tanımına atanıyor ve metod “post” olarak belirleniyor. Metodun get yerine post olarak tanımlanması genel olarak web uygulama geliştirme aşamasında tercih edilen bir yöntem. Post ile veri sunucu tarafına iletildiğinden dolayı güvenlik sağlanıyor.
- forloop.counter temel olarak for etiketinin ne kadar süre döndürüldüğü bilgisini veriyor.
- POST formu oluştururken aslında verinin de modifiye edilmesi mümkün olabilmektedir. Bu yüzden CSRF (Cross Site Request Forgeries) konusunda dikkatli olmalıyız. csrf_token Django içerisinde tanımlı olarak gelmektedir. İç URL'leri hedefleyen tüm POST formları csrf_token ile tanımlanmalıdır.

Web formu aracılığı ile gelen veriyi alan ve bu veriyi işleyen bir görünüm tasarlamamız gerekecek. Bu görünümü daha önce vote() fonksiyonu olarak tanımladık. Aşağıdaki güncellemeleri vote() fonksiyonu üzerinde gerçekleştiriyoruz:

```python
from django.shortcuts import get_object_or_404, render
from django.http import HttpResponseRedirect, HttpResponse
from django.urls import reverse

from .models import Choice, Question
# ...
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        # Redisplay the question voting form.
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        # Always return an HttpResponseRedirect after successfully dealing
        # with POST data. This prevents data from being posted twice if a
        # user hits the Back button.
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```

Yukarıdaki fonksiyona ilişkin detaylar:

- request.POST sözlük işlevi gösteren bir objedir ve gönderilen verinin anahtar ismi ile erişilmesini sağlar. Yukarıdaki tanımda request.POST['choice'] tanımı seçilen cevabın ID'sini string olarak vermektedir. request.POST her zaman bir string döndürür. Ayrıca Django request.GET tanımı ile POST üzerinde gerçekleştirilen işlemlerin aynısını gerçekleştirmemizi sağlıyor.
- Eğer herhangi bir choice'e basılmadı ise request.POST['choice'], KeyError hatası verecektir. Yukarıdaki except yapısının altına tekrar return render() tanımı girmemiz, hata aldığımızda aynı ekranda kalmamıza yarayacaktır.
- Herhangi bir sorun yaşanmadan devam ettiğinde choice üzerindeki vote sayısı artacak ve HttpResponseRedirect ile kullanıcıyı işlem tamamlandıktan sonra tanımlanan sayfaya aktaracak. Burdaki tanımda kullanıcıyı direk results sayfasına aktarıyor.
- HttpResponseRedirect kullanımı ile geri tuşuna basıp aynı veriyi iki kere submit etmeyi önlemiş de oluyoruz. Yalnızca Django özelinde değil, web uygulama geliştirme sürecinde edinilmesi gereken bir alışkanlık.
- reverse() fonksiyonu görünüm fonksiyonlarında tanımladığımız URL'leri hard-coded tanımlamaktan kaçınmamızı sağlıyor. Burdaki tanımda kullanıcıyı results sayfasına aktaıp URL pattern'e ilgili değişkenin ID'sini de ekliyor. Daha önceden tanımladığımız URLconf üzerinden yukarıdaki reverse kullanımı örnek olarak '/polls/3/results/' gibi bir sayfaya yönlenecek.

Kullanıcı vote ettikten sonra results fonksiyonumuza aktarıyoruz. results() fonksiyonunu aşağıdaki şekilde güncelliyoruz:

```python
from django.shortcuts import get_object_or_404, render

def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {'question': question})
```

Görünümde tanımladığımız results.html dosyasını oluşturalım ve içeriğini aşağıdaki şekilde güncelleyelim:

```python
<h1>{{ question.question_text }}</h1>

<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
{% endfor %}
</ul>

<a href="{% url 'polls:detail' question.id %}">Vote again?</a>
```

Tasarımı yaptıktan sonra /polls/1/ adresini ziyaret edip soruya yönelik bir cevap seçebilecek duruma gelmiş oluyoruz. Her oy verildiğinde results sayfasına gidilecek ve results sayfası güncellenecektir. Eğer herhangi bir cevap seçilmeden ilerlenmeye çalışılırsa hata mesajı çıkacaktır.

vote() fonksiyonu içerisinde yazdığımız kodda ufak bir sorun var. Bu fonksiyon önce veri tabanına gidip selected_choice nesnesini alıyor, vote alındığında yeni bir değer hesaplıyor ve veri tabanına tekrar yazdırıyor. Eğer iki kullanıcı aynı anda oy verirse verilen oy sayısı 2 artması gerekirken 1 artıyor. Bu duruma race condition deniyor, detaylı bilgi almak için: Avoiding race conditions using F().

GENERIC GORUNUMLER - DJANGO İLE HAZIR GELEN GORUNUMLER
Görünüm dosyamız içerisinde yazdığımız detail() ve results() fonksiyonları basit görünümler sunmaktadır. Ayrıca index() fonksiyonu da aslında sıralı olarak anketleri vermekte. Bu görünümler veriyi URL'e aktarılan parametreye bağlı olarak veri tabanından almakta, bir şablon çalıştırmakta ve render edilen şablonu geri döndürmekte. Django da bu işler “generic views" adıyla anılan kısayollar ile daha hızlı ve efektif bir şekilde yapılmaktadır. Generic görünümler ekleme yapılmadan kullanıldığında herhangi bir python kodu bile yazmaya gerek duyulmayacaktır. Gerçekleştireceğimiz işlemler sırasıyla:
1. URLconf'u değiştireceğiz.
2. Gereksiz olan görünümleri sileceğiz. 
3. Django generic görünümleri ekleyeceğiz.

Öncelikle URLconf tanımlamalarını içerdiğimiz polls uygulamamız içerisindeki urls.py dosyamızı açıp aşağıdaki şekilde güncelliyoruz:


```python
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
    path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

Yukarıda detail ve results için URLconf question_id'den pk'ya çevrildi. Ayrıca daha sonra görünüm dosyamız içerisinde oluşturacağımız fonksiyonları örneğin IndexView.as_view() olarak tanımladık. Bir sonraki aşamada görünümlerin yer aldığı views.py dosyasından index, detail ve results görünümlerini tamamen sileceğiz ve bu görünümler için generic görünümler oluşturacağız:

```python
from django.shortcuts import get_object_or_404, render
from django.http import HttpResponseRedirect
from django.urls import reverse
from django.views import generic

from .models import Choice, Question

class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5]

class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'

class ResultsView(generic.DetailView):
    model = Question
    template_name = 'polls/results.html'

    def vote(request, question_id):
        ... # same as above, no changes needed.
```

ListView ve DetailView olmak üzere iki adet generic görünüm kullandık. Sırasıyla iki görünüm hem nesne listesini görünüme sunma hem de her nesne için detaylı bir görünüm elde etmemize yarıyor. Her bir generic görünüm hangi modeli kullanacağını model niteliği ile öğreniyor. DetailView tarafında da bu generic görünüm bir pk elde edecek şekilde tasarlanmış, bu yüzden yukarıda da bahsettiğimiz üzere question_id'yi pk'ya çevirdik. 

Varsayılan olarak DetailView \<app name>/\<model name>_detail.html olacak şekilde bir tanım kullanıyor. Bizim tanımladığımız şekilde kullanılan şablon "polls/question_detail.html". Burada template_name niteliği Django'ya spesifik olarak kullanılacak olan template ismini aktarmamıza yarıyor. Biz yapmaz isek şablon adı otomatik olarak oluşturuluyor. Ek olarak template_name niteliğini results için de tanımlıyoruz. Amacımız iki görünümün de birbirinden farklı çıktılar sunabilmesi. Çünkü arka planda ikisi de DetailView olarak render edilecek.

Benzer olarak ListView de varsayılan olarak  \<app name>/\<model name>_list.html şablonunu kullanıyor. Template ismini kullanarak kendi oluşturduğumuz şablon üzerinden işlem yapmasını söylüyoruz.

DetailView question değişkenini modelden otomatik olarak çekiyor. Django context değişkeni için hangi ismin kullanılacağını otomatik olarak tanımlıyor. ListView için otomatik olarak oluşturulan context değişkeni question_list. Otomatik olarak tanımlanan bu işlemi değiştirmek için context_object_name niteliği tanımlıyoruz ve yukarıdaki örnekte olduğu gibi latest_question_list niteliği üzerinden işlem yapılmasını sağlıyoruz.

## OTOMATİK TESTLER
Testleri yazdığımız kodun nasıl işlediğini kontrol etmek amacıyla yazıyoruz. Test çalışmaları çeşitli seviyelerde oluşturulabilir. Bazı testler ufak detayları kontrol etmemize yarar (model metodunun nasıl bir değer döndürdüğü gibi), bazıları ise uygulamanın tamamının nasıl sonuçlar ürettiğini test etmemize yarar (kullanıcı girdilerinin istenilen değerleri üretip üretmediği gibi). Daha önce shell komutu ile python kabuğunda model sınıflarımızı denemiştik, gerçekleştirdiğimiz çalışmalar test çalışmaları kapsamındaydı.

Otomatik testler, kabukta gerçekleştirdiğimiz testlere nazaran sistem tarafından gerçekleştirilmektedir. Testleri bir kere oluşturduktan sonra gerçekleştirdiğimiz her değişiklikten sonra kodun istediğimiz sonuçları verip vermediğini hızlıca kontrol edebiliyoruz. Uygulamanın çeşitli kısımları birbirleri ile etkileşim halinde olabilmektedir, gerçekleştirilen herhangi bir değişiklik uygulamanın çeşitli kısımlarında hataya yol açabilir. Manuel olarak kontrol etmek uzun vakit alacaktır, ancak otomatik testler bunu saniyeler içerisinde gerçekleştirmektedir. Yanlış giden bir durumda hangi kısımda hata alındığını da verecektir. 

Test yazmaya yönelik sektörde çeşitli yaklaşımlar mevcut. Önce testleri yazıp daha sonra kod yazmak bir yaklaşımdır: Önce sorun oluşturup, daha sonra sorunu tanımlayıp kod yazarak bu sorunun önüne geçme işlemleri gerçekleştirilir. Kod yazdıktan sonra koda yönelik testleri de yazmak bir yaklaşımdır, ancak testler ve kodlar birlikte ilerlemelidir. Satırlarca kodu tekrar okuyup test yazmak vakit kaybı olacaktır.

## ANKET UYGULAMASI TESTLERİ
Daha önceden yazdığımız testte ufak bir bug mevcut. Question sınıfı altında tanımladığımız was_published_recently() metodu, soru bir gün önce tanımlansa da True değeri döndürüyor, ileri bir tarihte yayınlanacak olarak belirlense de True değeri döndürüyor. Python kabuğunda aşağıdaki şekilde bir deneme yapalım: 

```python
>>> import datetime
>>> from django.utils import timezone
>>> from polls.models import Question
>>> # create a Question instance with pub_date 30 days in the future
>>> future_question = Question(pub_date=timezone.now() + datetime.timedelta(days=30))
>>> # was it published recently?
>>> future_question.was_published_recently()
True
```

Yukarıda belirlenen bug'ı daha sonraki zamanlarda tekrar tekrar manuel olarak denemek yerine otomatik testimizi oluşturalım. Test sistemi test ile başlayan dosyalara bakmaktadır, otomatik testimizi yeni bir uygulama oluşturduğumuzda otomatik olarak gelen tests.py dosyası içerisinde tanımlayacağız:

```python
import datetime

from django.utils import timezone
from django.test import TestCase

from .models import Question

class QuestionModelTests(TestCase):

    def test_was_published_recently_with_future_question(self):
        """
        was_published_recently() returns False for questions whose pub_date
        is in the future.
        """
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.
        
        Is(future_question.was_published_recently(), False)
```

Yukarıda django.text.TestCase alt sınıfı ile bir metod oluşturduk. Bu metod yayınlanma tarihi ileri tarihli bir soru oluşturdu. Daha sonra ise was_published_recently() fonksiyonunun çıktısının False olması gerektiğini tanımladık.

Testleri çalıştırmak için aşağıdaki komutu terminale girelim:

```console
$ python manage.py test polls
```

Terminal ekranında aşağıdakine benzer bir çıktı almamız gerekecektir:

```python
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_was_published_recently_with_future_question (polls.tests.QuestionModelTests)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/path/to/mysite/polls/tests.py", line 16, in test_was_published_recently_with_future_question
    self.assertIs(future_question.was_published_recently(), False)
AssertionError: True is not False

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

Yukarıdaki aşamalar sırasıyla:
1. Girdiğimiz komut polls uygulaması içerisinde tanımlı testleri aradı.
2. django.test.Testcase sınıfının alt sınıfını buldu.
3. Testi gerçekleştirmek amacı ile özel bir veri tabanı oluşturdu.
4. İsmi test ile başlayan test metodları aradı.
5. test_was_published_recently_with_future_question fonksiyonu ile pub_date değeri 30 gün sonra olan bir soru oluşturdu.
6. assertIs() metodu ile testin sonucunun True olduğunu gördü, ancak biz False olması gerektiğini tanımlamıştık.

Problemi bulduğumuza göre, Question modeli içerisinde yer alan was_published_recently() fonksiyonunu soru yayınlanmış ise True verecek şekilde güncellememiz gerekecektir:

```python
def was_published_recently(self):
    now = timezone.now()
    return now - datetime.timedelta(days=1) <= self.pub_date <= now
```

Testi tekrar çalıştırdığımızda aşağıdaki bildirimi alacağız:

```python
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
Destroying test database for alias 'default'...
```

Problemi bulduktan sonra testi yazdık ve kodu güncelledik. Program karmaşık olmaya başladığında artık bu hatanın bir daha oluşmayacağını, oluşsa bile test sonucu ile birlikte hemen aksiyon alabileceğimizi netleştirmiş olduk.

was_published_recently() testi ile problemi çözmemiz, bir başka hataya da neden olabilir. Aşağıda 2 adet test metodu daha ekliyoruz:

```python
def test_was_published_recently_with_old_question(self):
    """
    was_published_recently() returns False for questions whose pub_date
    is older than 1 day.
    """
    time = timezone.now() - datetime.timedelta(days=1, seconds=1)
    old_question = Question(pub_date=time)
    self.assertIs(old_question.was_published_recently(), False)

def test_was_published_recently_with_recent_question(self):
    """
    was_published_recently() returns True for questions whose pub_date
    is within the last day.
    """
    time = timezone.now() - datetime.timedelta(hours=23, minutes=59, seconds=59)
    recent_question = Question(pub_date=time)
    self.assertIs(recent_question.was_published_recently(), True)
```

Yukarıdaki tanımlamalarla birlikte fonksiyonumuz geçmişte yayınlanan, o an yayınlanan ve ileride yayınlanacak olan sorular için daha hassas ve doğru sonuçlar verecektir. 

## GÖRÜNÜMLERİ TEST ETME
Polls uygulaması içerisinde yazdığımız görünümler, yayınlanma tarihi daha sonraki bir tarih olan soruları da son kullanıcıya sunacaktır. Daha ileri bir tarihte yayınlanmasını istediğimiz soruların şimdiki zamanda görünür olmamasına yönelik bir tasarım yapmamız gerekecek. 
 
Daha önceki yaklaşımda önce testimizi yazdık, sonra sorunu kod yazarak giderdik. Şimdiki testimizde yazdığımız kodun davranışını gözlemleyip daha sonra sorunumuzu gidereceğiz. 
 
Django, görünüm seviyesinde kullanıcının gördüğü kısmı test etmemize yarayacak bir test Client sunmakta. Önce kabuk üzerinde aşağıdaki şekilde bir kontrol gerçekleştirelim: 
 
```python
>>> from django.test.utils import setup_test_environment 
>>> setup_test_environment() 
setup_test_environment() installs a template renderer which will allow us to examine some additional attributes on responses such as response.context that otherwise wouldn’t be available. Note that this method does not setup a test database, so the following will be run against the existing database and the output may differ slightly depending on what questions you already created. You might get unexpected results if your TIME_ZONE in settings.py isn’t correct. If you don’t remember setting it earlier, check it before continuing. 
Next we need to import the test client class (later in tests.py we will use the django.test.TestCase class, which comes with its own client, so this won’t be required): 
>>> from django.test import Client 
>>> # create an instance of the client for our use 
>>> client = Client() 
With that ready, we can ask the client to do some work for us: 
>>> # get a response from '/' 
>>> response = client.get('/') 
Not Found: / 
>>> # we should expect a 404 from that address; if you instead see an 
>>> # "Invalid HTTP_HOST header" error and a 400 response, you probably 
>>> # omitted the setup_test_environment() call described earlier. 
>>> response.status_code 
404 
>>> # on the other hand we should expect to find something at '/polls/' 
>>> # we'll use 'reverse()' rather than a hardcoded URL 
>>> from django.urls import reverse 
>>> response = client.get(reverse('polls:index')) 
>>> response.status_code 
200 
>>> response.content 
b'\n    <ul>\n    \n        <li><a href="/polls/1/">What&#39;s up?</a></li>\n    \n    </ul>\n\n' 
>>> response.context['latest_question_list'] 
<QuerySet [<Question: What's up?>]> 
```

Testimizi gerçekleştirdikten sonra görünümümüzü aşağıdaki şekilde güncelleyelim: 
 
```python
class IndexView(generic.ListView): 
    template_name = 'polls/index.html' 
    context_object_name = 'latest_question_list' 
 
    def get_queryset(self): 
        """Return the last five published questions.""" 
        return Question.objects.order_by('-pub_date')[:5] 
```

Bir sonraki aşama için get_queryset() metodumuzu timezone.now() ile kontrol edilecek şekilde düzenliyoruz:

```python
from django.utils import timezone

def get_queryset(self):
    """
    Return the last five published questions (not including those set to be
    published in the future).
    """
    return Question.objects.filter(
        pub_date__lte=timezone.now()
    ).order_by('-pub_date')[:5]
Question.objects.filter(pub_date__lte=timezone.now())
```

Yukarıdaki tanım pub_date'i timezone.now()'a eşit veya daha önce olanları sorguluyor.

Görünümümüzü düzelttikten sonra testlerimizi tests.py dosyası içerisine ekleyelim:

```python
from django.urls import reverse

def create_question(question_text, days):
    """
    Create a question with the given `question_text` and published the
    given number of `days` offset to now (negative for questions published
    in the past, positive for questions that have yet to be published).
    """
    time = timezone.now() + datetime.timedelta(days=days)
    return Question.objects.create(question_text=question_text, pub_date=time)


class QuestionIndexViewTests(TestCase):
    def test_no_questions(self):
        """
        If no questions exist, an appropriate message is displayed.
        """
        response = self.client.get(reverse('polls:index'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])

    def test_past_question(self):
        """
        Questions with a pub_date in the past are displayed on the
        index page.
        """
        create_question(question_text="Past question.", days=-30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )

    def test_future_question(self):
        """
        Questions with a pub_date in the future aren't displayed on
        the index page.
        """
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])

    def test_future_question_and_past_question(self):
        """
        Even if both past and future questions exist, only past questions
        are displayed.
        """
        create_question(question_text="Past question.", days=-30)
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )

    def test_two_past_questions(self):
        """
        The questions index page may display multiple questions.
        """
        create_question(question_text="Past question 1.", days=-30)
        create_question(question_text="Past question 2.", days=-5)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question 2.>', '<Question: Past question 1.>']
        )
```

İlk fonksiyonumuz yeni bir soru oluşturduğumuz kısayol fonksiyonumuz, testleri yazar iken her defasında yeni soru oluşturmamak için en başta tanımladık. 

test_no_questions fonksiyonu yeni bir soru oluşturmuyor, No polls are available mesajını kontrol ediyor ve latest_question_list sorgusunun boş olup olmadığına bakıyor. django.test.Testcase ek assertion metodları sağlıyor, assetContains() ve assertQuerysetEqual() assertion metodlarını kullandık.

test_past_question testi ile yeni bir soru oluşturuyoruz ve listede yer alıp almadığına bakıyoruz.

test_future_question testi ile yayınlanma tarihi gelecek tarih olan bir soru oluşturuyoruz. Veri tabanı her test metodu için kendini yeniliyor, bu yüzden ilk oluşturulan soru veri tabanında yer almıyor.

Genel olarak, testleri admin girdileri veya kullanıcı deneyimlerine yönelik oluşturuyoruz, böylece ihtiyacımız olanları kullanıcıya sunuyoruz. 

## DetailView'i Test Etmek
Testlerimiz istediğimiz sonuçları veriyor, ancak oluşturulan her soru indekslendiği için kullanıcılar URL'i tahmin ederek sorulara ulaşibilir. DetailView'e bir kısıtlayıcı eklememiz gerekecek.

```python
class DetailView(generic.DetailView):
    ...
    def get_queryset(self):
        """
        Excludes any questions that aren't published yet.
        """
        return Question.objects.filter(pub_date__lte=timezone.now())
```

Yukarıdaki kısıtlayıcımız için de testlerimizi yazalım:

```python
class QuestionDetailViewTests(TestCase):
    def test_future_question(self):
        """
        The detail view of a question with a pub_date in the future
        returns a 404 not found.
        """
        future_question = create_question(question_text='Future question.', days=5)
        url = reverse('polls:detail', args=(future_question.id,))
        response = self.client.get(url)
        self.assertEqual(response.status_code, 404)

    def test_past_question(self):
        """
        The detail view of a question with a pub_date in the past
        displays the question's text.
        """
        past_question = create_question(question_text='Past Question.', days=-5)
        url = reverse('polls:detail', args=(past_question.id,))
        response = self.client.get(url)
        self.assertContains(response, past_question.question_text)
```

Benzer get_queryset metodunu ResultsView içerisinde de belirtmemiz ve ResultsView için yeni bir test sınıfı oluşturmamız gerekecektir. Kendimizi çok fazla tekrarlayacağız.

Ayrıca uygulamanın çeşitli yaklaşımlarını geliştirebiliriz. Mesela, cevabı olmayan sorular da şu an için yayınlanabilir durumda. Görünüm sınıflarımız bu tarz soruları bulmalı ve bu soruları yayınlamamalıdır. Admin kullanıcının bu soruları görmesi uygun olacaktır ancak siteye giren diğer kullanıcılar için uygulanabilir olmamalıdır. Bir zaman sonra tüm ihtiyaçlarımıza yönelik testler gerçekleştirdiğimizde yazdığımız kod test havuzu içinde boğulacaktır. 

Bir noktadan sonra yazılan testler uygulama kodundan daha fazla olacak noktaya gelebilecektir. Bazı noktalarda da yazdığımız testleri güncellememiz gerekecektir. Test sürecinde fazla test yazmak sürecin etkin olduğu anlamına gelecektir. Testler sıralı olarak yazıldığında yönetimi her zaman kolay olacaktır. Test yaklaşımının iyi olarak kurgulanmasına yönelik aşağıdakiler dikkate alınmalıdır:

Her model veya görünüm için ayrı bir TestClass oluşturulması
Her kondüsyon için farklı test metodlarının oluşturulması
Fonksiyon belirleyici test metodu isimlerinin kullanılması

## UYGULAMA ARAYÜZÜNÜN TASARIM
Django da yazdığımız uygulamalar arttıkça statik dosya olarak bahsettiğimiz css, image gibi dokümanları tek bir yerde toplayabileceğimiz bir kütüphane mevcut: “django.contrib.staticfiles”

Öncelikle polls uygulaması içerisinde static isimli bir klasör oluşturalım. Django static dosyaları “static” isimli klasörler içerisinde arıyor. Django STATICFILES_FINDERS, uygulama listesinin bulunduğu INSTALLED_APPS içerisinde bu aramayı gerçekleştiriyor.

Oluşturduğumuz static klasörü içerisinde polls adlı bir başka klasör oluşturalım ve bu klasör içerisinde style.css adlı bir dosya oluşturalım ve aşağıdaki gibi güncelleyelim.

```css
li a {
    color: green;
}
```

Daha sonra index.html dosyamızı aşağıdaki gibi güncelleyelim:

```python
{% load static %}

<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
```

Uygulamaya arka plan resmi koymak üzere static isimli klasörün altına polls klasörü, onun da altına images klasörü açıp resmi içine koyuyoruz ve css dosyamızı aşağıdaki şekilde güncelliyoruz:

```css
body {
    background: white url("images/background.gif") no-repeat;
}
```

## ADMİN EKRANININ KONFİGÜRE EDİLMESİ
admin.site.register ile modellerimizi admin sayfasına yüklediğimizde Django otomatik olarak görünümleri oluşturmuştur. Admin sayfası konfigüre edilebiliyor. Admin konsolu üzerindeki Question modelimizin görünümünü değiştirmek için admin.py dosyamızda aşağıdaki değişikliği yapıyoruz:

```python
from django.contrib import admin

from .models import Question


class QuestionAdmin(admin.ModelAdmin):
    fields = ['pub_date', 'question_text']

admin.site.register(Question, QuestionAdmin)
```

Yukarıdaki dizilim ile yayınlanma tarihini soru alanının üstüne çektik. Çoğu zaman çok alanımız olduğunda alanların kategorisel olarak birbirinden ayrılmalarını sağlamak üzere alan kümeleri oluşturacağız: 

```python
from django.contrib import admin

from .models import Question


class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date']}),
    ]

admin.site.register(Question, QuestionAdmin)
```

## NESNELERİ BAĞLAMAK
Sorulara yanıtlar oluştururken ilgili soruyu çekebilmek üzere Choice modelimizi admin.py dosyamıza ekleyelim:

```python
from django.contrib import admin

from .models import Choice, Question
# ...
admin.site.register(Choice)
```

Admin sayfasında cevap oluştururken yukarıda soru combo box'ı çıkacak. Veri tabanımızdaki tüm soruları gösterir ve her soru için cevap oluşturmamızı sağlar. FoerignKey tanımı ile elde etmiş olduk. Şimdi her soru için birden fazla seçenek ekleyebileceğimiz şekilde admin.py dosyamızı güncelleyelim:

```python
from django.contrib import admin

from .models import Choice, Question


class ChoiceInline(admin.StackedInline):
    model = Choice
    extra = 3


class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
    ]
    inlines = [ChoiceInline]

admin.site.register(Question, QuestionAdmin)
```

Yukarıdaki güncellemeyi yaptığımızda admin.site.register(Choice) kısmına ihtiyacımız kalmayacak. Yukarıdaki tanımlamalardan sonra Choice kısmı aşağı doğru çok yer kaplayacak, bunu önlemek üzere admin.py üzerinde aşağıdaki güncellemeleri yapalım:


```python
class ChoiceInline(admin.TabularInline):
    #...
```

TabularInline tanımı ilişkili nesneleri daha derli toplu ve tablo formatında veriyor.

Admin sayfası içerisinde tüm sorularımızın bulunduğu listeyi kendi isteklerimize göre düzenleyebiliyoruz. Varsayılan olarak str() fonksiyonu ile tanımladığımız kısımlar görünüyor sadece. Daha fazla alan göstermek için list_display seçeneğini kullanabiliyoruz. admin.py dosyası içerisinde aşağıdaki güncellemeleri yapalım:

```python
class QuestionAdmin(admin.ModelAdmin):
    # ...
    list_display = ('question_text', 'pub_date')
```

Bir de was_published_recently() fonksiyonumuzun ürettiği sonucu ekleyelim:

```python
class QuestionAdmin(admin.ModelAdmin):
    # ...
    list_display = ('question_text', 'pub_date', 'was_published_recently')
```

was_published_recently fonksiyonunun admin sayfasında yer aldığı kısmın başlığını kendimiz belirlemek üzere models.py dosyamız içerisinde aşağıdaki güncellemeleri yapıyoruz:

```python
class Question(models.Model):
    # ...
    def was_published_recently(self):
        now = timezone.now()
        return now - datetime.timedelta(days=1) <= self.pub_date <= now
    was_published_recently.admin_order_field = 'pub_date'
    was_published_recently.boolean = True
    was_published_recently.short_description = 'Published recently?'
```

Admin sayfamıza filter tablosu koyabiliriz. “list_filter = ['pub_date']" tanımını admin.py dosyasına eklediğimizde karşımıza çıkacaktır.

Admin sayfamıza arama alanını da “search_fields = ['question_text']” eklenmesi ile sağlayabiliriz.

## ADMİN GÖRÜNÜMÜNÜN DEĞİŞTİRİLMESİ
Proje klasörünün içerisine templates adlı bir klasör oluşturacağız ve settings.py dosyamızda TEMPLATES alanı içerisinde bu klasörü görecek şekilde tanımlamamızı gerçekleştireceğiz:

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

Uygulamaların template klasörlerini uygulama içerisinde oluşturmak daha efektif bir kullanım. Proje klasörü için oluşturduğumuz templates klasörünü yalnızca proje amaçlı kullanacağız. 

Templates klasörü içerisine admin klasörü oluşturacağız ve django admin klasörü içerisinde base_site.html dosyasını alacağız (django/contrib/admin/templates). Daha sonra dosya içerisinde {{ site_header|default:_('Django administration') }} kısmında sitemizin ismini belirtebiliriz:

```python
{% block branding %}
<h1 id="site-name"><a href="{% url 'admin:index' %}">Polls Administration</a></h1>
{% endblock %}
```

Değişikliği gerçekleştirmenin başka bir yöntemi de django.contrib.admin.AdminSite.site_header 

## TEKRAR KULLANILABİLİR UYGULAMALAR OLUŞTURMAK
Tekrar kullanılabilir uygulamalar, farklı projelere aktarıp bu projeler içerisinde de kullanabilmek üzere işimizi oldukça kolaylaştırıyor. Python Package Index (PyPI) içerisinde Django Packages alanında kullanılabilir bir çok uygulama mevcut.

Son görünümde proje klasörleri ve alt klasörler aşağıdaki gibi olacaktır:

- mysite/
    - manage.py
    - mysite/
        - \_\_init__.py
        - settings.py
        - urls.py
        - wsgi.py
    - polls/
        - \_\_init__.py
        - admin.py
        - migrations/
            - \_\_init__.py
            - 0001_initial.py
        - models.py
        - static/
            - polls/
                - images/
                    - background.gif
                - style.css
        - templates/
            - polls/
                - detail.html
                - index.html
                - results.html
        - tests.py
        - urls.py
        - views.py
    - templates/
        - admin/
            - base_site.html

## GEREKLİLİKLERİN YÜKLENMESİ VE PAKETLEME
Python içerisinde paketleme için çok uygulama mevcut, bu kısımda setuptools uygulamasını kullanacağız:

1. Öncelikle polls klasörü için Django projesi dışında django-polls adında bir klasör oluşturacağız.
2. Polls klasörünü yeni klasöre taşıyalım.
3. django-polls klasörü altında README.rst adlı bir dosya oluşturacağız:


```text
Polls
=====

Polls is a simple Django app to conduct Web-based polls. For each
question, visitors can choose between a fixed number of answers.

Detailed documentation is in the "docs" directory.

Quick start
-----------

1. Add "polls" to your INSTALLED_APPS setting like this::

    INSTALLED_APPS = [
        ...
        'polls',
    ]

2. Include the polls URLconf in your project urls.py like this::

    path('polls/', include('polls.urls')),

3. Run `python manage.py migrate` to create the polls models.

4. Start the development server and visit http://127.0.0.1:8000/admin/ to create a poll (you'll need the Admin app enabled).

5. Visit http://127.0.0.1:8000/polls/ to participate in the poll.
```

4. django polls klasörü altında LICENSE dosyası oluşturalım. Django ve bir çok Django uyumlu uygulamalar genelde BSD lisansı altında yayınlanıyor.

5. Daha sonra setup.py dosyası oluşturup uygulamanın nasıl yükleneceğini tanımlayacağız. django-polls klasörü altında setup.py dosyası oluşturup içeriğini aşağıdaki gibi düzenleyeceğiz:
 
```python
import os
from setuptools import find_packages, setup

with open(os.path.join(os.path.dirname(__file__), 'README.rst')) as readme:
    README = readme.read()

# allow setup.py to be run from any path
os.chdir(os.path.normpath(os.path.join(os.path.abspath(__file__), os.pardir)))

setup(
    name='django-polls',
    version='0.1',
    packages=find_packages(),
    include_package_data=True,
    license='BSD License',  # example license
    description='A simple Django app to conduct Web-based polls.',
    long_description=README,
    url='https://www.example.com/',
    author='Your Name',
    author_email='yourname@example.com',
    classifiers=[
        'Environment :: Web Environment',
        'Framework :: Django',
        'Framework :: Django :: X.Y',  # replace "X.Y" as appropriate
        'Intended Audience :: Developers',
        'License :: OSI Approved :: BSD License',  # example license
        'Operating System :: OS Independent',
        'Programming Language :: Python',
        'Programming Language :: Python :: 3.5',
        'Programming Language :: Python :: 3.6',
        'Topic :: Internet :: WWW/HTTP',
        'Topic :: Internet :: WWW/HTTP :: Dynamic Content',
    ],
)
```

6. Yalnızca python modülleri ve paketlerine bu dosya içerisinde yer verdik. Ek dosyalar eklemek için MANIFEST.in dosyası oluşturmamız gerekecektir. MANIFEST.in dosyasının içeriğini aşağıdaki şekilde düzenliyoruz:

```python
include LICENSE
include README.rst
recursive-include polls/static *
recursive-include polls/templates *
```

7. Uygulama hakkında detaylı bir dokümanyasyon oluşturmak uygulamamızı tanıtmamıza yarayacaktır. django-polls klasörü altında docs klasörü oluşturuyoruz ve MANIFEST.in dosyası içerisine bir satır daha ekliyoruz:

```python
recursive-include docs *
```

8. django-polls klasörü içerisinden python setup.py sdist komutu ile uygulamamızı derliyoruz. Bu komut django-polls-0.1.tar.gz adında yeni bir paket oluşturuyor. 
