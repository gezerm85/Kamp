

# SQL Örnekleri




## İlçe bilgisi olmayan kayıtları siliniz

``` sql
DELETE FROM referandum WHERE ilce='';
```

## İl ve ilçeye göre sıralı liste

``` sql
SELECT il, ilce FROM referandum ORDER BY il, ilce;
```

## Türkiye toplam seçmen adedi

``` sql
SELECT sum(kayitli) as "Kayitli" FROM referandum 
```

## Türkiye toplam geçerli oy adedi

``` sql
SELECT SUM(gecerli) as "Geçerli", 
```

## Türkiye geneli özet (toplam seçmen, toplam geçerli, vb.)

``` sql
SELECT SUM(kayitli) as "Kayıtlı", sum(gecerli) as "Geçerli", sum(gecersiz) as "Geçersiz", sum(evet) as "Evet", sum(hayir) as "Hayır" FROM referandum
```



## İlçe nüfusu 1500 kişiden daha az ilçeler

```sql
SELECT * FROM referandum WHERE kayitli < 1500;

```

## En çok geçersiz oy kullanan il ve geçersiz oy sayısı

```sql
SELECT il, SUM(gecersiz) AS 'Geçersiz Oy Sayısı' FROM referandum
GROUP BY il
ORDER BY 2 DESC
LIMIT 1;
```

