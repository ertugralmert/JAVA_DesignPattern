# Design Pattern

# Design Pattern

#### Eager Singleton

İlgili dışarıyı new'leme işlemini dışarıya kapatıyoruz.

İki class oluşturalım.

```java
public class EagerSingleton{
    //uygulama ayağa kalkarken oluşmasını sağladık. Sadece 1 referans üretiriz.
    private static EagerSingleton INSTANCE = new EagerSingleton(); // new'leme işlemini kapatacağız
    public static EagerSingleton getInstance(){
        return INSTANCE;
    }
    
    public int toplam(int... sayilar){
        int result = 0;
        for(int sayi : sayilar) result += sayi;
        return result;
    }
}
```

Yukarıda bir tane class oluşturduk ve method tanımladık.

Şimdi bunu başka bir sınıfta kullanalım.

```java
public class KullaniciService{
    private EagerSingleton eagerSingleton;
    public void islemler(){
        eagerSingleton = EagerSingleton.getInstance(); // böylece new'leme işlemi yapmadan referans adresini kullanıyoruz.
        System.out.println(eagerSingleton.toplam(32,323,3));
    }
}
```

### Lazy Singleton

Bunu java class'ında gösterelim.

```java
public class LazySingleton{
    private static LazySingleton INSTANCE;
    private LazySingleton(){
        count++;
    }
    public static LazySingleton getInstance(){
        if(INSTANCE == null)
            INSTANCE = new LazySingleton();
        return INSTANCE;
    }
    //nesne oluşturma adedi için tanımladık
    public static int count;
    public int toplam(int... sayilar){
        int result = 0;
        for (int sayi : sayilar) result += sayi;
        return result;
    }
}
```

Yukarıdaki kodda eğer INSTANCE yaratılmışsa yeniden yaratma nesnenin referansını kullan diyoruz.

---

### Thread Safe Singleton

Raise condition -> çoklu kullanıcılı veya çoklu işlemcili yapıları tarif etmek için kullanılır.

Belli bellek bölgelerindeki verilere aynı anda erişimi tarif eder ve bu erişimin yaşattığı sorunları ifade eder.

Burada olan sorunları düşündüğümüzde çözüm olarak geliştirilen başlıca konular şunlardır;

- mutex
- semaforlar
- kilitlenmeler

```java
public class ThreadSafeSingleton {
    private static ThreadSafeSingleton INSTANCE;
    public static int count;
    private ThreadSafeSingleton(){
        count++;
    }
    public static ThreadSafeSingleton getInstance(){
        synchronized (ThreadSafeSingleton.class){} //senkronize yapmış olacağız. Thread'lerde bu yapılmalı
        if (INSTANCE == null)
            INSTANCE = new ThreadSafeSingleton();
        return INSTANCE;
    }
}
```

```java
public class ThreadSafeRunner {
    public static void main(String[] args){
        for (int i = 0; i < 10; i++) {
            new Thread(()->{
                ThreadSafeSingleton threadSafeSingleton = ThreadSafeSingleton.getInstance();
                System.out.println("nesne referansı: " + threadSafeSingleton);
            }).start();
        }
        try {
            Thread.sleep(4000L);
        }catch (Exception e){
            
        }
    }
}
```

### Double Check Locking

```java
public class DoubleCheckLocking{
    private static volatile DoubleCheckLocking INSTANCE;
    private static int count;
    private DoubleCheckLocking(){
        count++;
    } 
    public static DoubleCheckLocking getInstance(){
        if(INSTANCE == null){
            synchronized (DoubleCheckLocking.class){
                if (INSTANCE == null)
                    INSTANCE = new DoubleCheckLocking();
            }
        }
        return INSTANCE;
    }
}
```

### BillPugh

```java
public class BillPugh{
    public static int count;
    private BillPugh(){
        count++;
    }
    private static class BillPughHelper{
        private static final BillPugh INSTANCE = new BillPugh(); 
    }
    public static BillPugh getInstance(){
        return BillPughHelper.INSTANCE;
    }
}
```

