

# MySQL 101 Notları

## 1. Giriş

* Varsayılan kullanıcı: **root**
* Varsayılan parola: genelde **root** veya **boş** olabilir.

Bağlanmak için:

```bash
mysql -u root -p
```

---

## 2. Veritabanı İşlemleri

**Veritabanı oluşturma:**

```sql
CREATE DATABASE ornekdb 
CHARACTER SET utf8mb4 
COLLATE utf8mb4_general_ci;
```

* `utf8mb4` → Tüm karakterleri + emoji desteğini içerir.
* `utf8` (aslında `utf8mb3`) → Emoji desteği yoktur.

---

## 3. Tablo İşlemleri

**Tablo oluşturma:**

```sql
USE ornekdb;

CREATE TABLE kullanicilar (
    id INT AUTO_INCREMENT PRIMARY KEY,
    ad VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    yas INT,
    kayit_tarihi TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Tablo listeleme:**

```sql
SHOW TABLES;
```

**Tablo yapısını görüntüleme:**

```sql
DESCRIBE kullanicilar;
```

---

## 4. Veri İşlemleri

**Kayıt ekleme:**

```sql
INSERT INTO kullanicilar (ad, email, yas) 
VALUES ('Ali Veli', 'ali@example.com', 25);
```

**Kayıtları listeleme:**

```sql
SELECT * FROM kullanicilar;
```

**Filtreleme:**

```sql
SELECT ad, email 
FROM kullanicilar 
WHERE yas > 20;
```

**Güncelleme:**

```sql
UPDATE kullanicilar 
SET yas = 26 
WHERE id = 1;
```

**Silme:**

```sql
DELETE FROM kullanicilar 
WHERE id = 1;
```

---

## 5. Kullanıcı ve Yetki Yönetimi

**Yeni kullanıcı oluşturma:**

```sql
CREATE USER 'yeniuser'@'localhost' IDENTIFIED BY 'sifre123';
```

**Yetki verme:**

```sql
GRANT ALL PRIVILEGES ON ornekdb.* TO 'yeniuser'@'localhost';
FLUSH PRIVILEGES;
```

**Kullanıcı silme:**

```sql
DROP USER 'yeniuser'@'localhost';
```

---

## 6. Faydalı Komutlar

```sql
SHOW DATABASES;       -- Tüm veritabanlarını listele
USE veritabani_adi;   -- Belirli veritabanına geç
SHOW TABLES;          -- Tablo listesini göster
SELECT VERSION();     -- MySQL sürümünü öğren
```

---

## 7. NULL Kavramı

* `NULL` → “Boş / bilinmeyen değer” demektir.
* **0 değildir.**
* **"" (boş string) değildir.**
* Anlamı: “Veri yok / bilinmiyor.”

**Örnek:**

```sql
INSERT INTO ogrenciler (ad, yas) 
VALUES ('Ali', NULL);
```

Burada `yas` sütunu **bilinmiyor** olarak kaydedilir.

---

## 8. CSV / TSV Çıkış

Sonuçları dosyaya aktarmak için:

**CSV (virgülle ayrılmış):**

```sql
SELECT * 
FROM kullanicilar
INTO OUTFILE '/var/lib/mysql-files/kullanicilar.csv'
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```

**TSV (tab ile ayrılmış):**

```sql
SELECT * 
FROM kullanicilar
INTO OUTFILE '/var/lib/mysql-files/kullanicilar.tsv'
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n';
```

⚠️ Not: `INTO OUTFILE` için MySQL’in yazma izni olması gerekir (`/var/lib/mysql-files/` genelde güvenli klasördür).

---









# DNS NOTLARI

* DNS = **Domain Name System**
* İnternetin temeli. Alan adlarını IP adreslerine çevirir.

---

## 1. DNS Çalışma Mantığı

1. Tabii ağam 🙌 Senin için deftere geçecek şekilde **DNS şeması notu** hazırlayayım. Hem yazıyla hem de küçük şema çizimiyle olsun:

---

# DNS Şeması Notu

## 1. Adım Adım DNS Çözümleme

1. **Tarayıcı Cache** → Daha önce açılmışsa burada bakılır.
2. **İşletim Sistemi Cache** → OS DNS önbelleği kontrol edilir.
3. **Hosts Dosyası** → Manuel tanımlama varsa kullanılır.
4. **Recursive Resolver (Genelde ISS veya 1.1.1.1 / 8.8.8.8 gibi DNS sunucusu)**

   * Resolver gerekli bilgiyi bulmak için diğer sunuculara sırayla sorar:

     * **Root DNS** → "Bu uzantıyı kim yönetiyor?" (IP bilmez, sadece yönlendirir).
     * **TLD DNS (.com, .org, .tr …)** → "Bu alan adı için hangi otorite yetkili?"
     * **Authoritative DNS** → Alan adının **gerçek IP adresini** verir.
5. **Cevap geri döner** → Resolver → OS → Tarayıcı → Kullanıcı.

---

## 2. Basit Şema

```
[Tarayıcı] 
    ↓
[OS Cache]
    ↓
[Hosts Dosyası]
    ↓
[Recursive Resolver (ISS / Google / Cloudflare)]
    ↓
 [Root DNS] ──→ [TLD DNS] ──→ [Authoritative DNS]
                                ↓
                          [IP Adresi]
```

---

## 3. Özet

* **Root DNS** → IP bilmez, sadece hangi uzantıyı (TLD) kimin yönettiğini söyler.
* **TLD DNS** → Uzantıya (.com, .tr vs.) ait yetkili sunucuları gösterir.
* **Authoritative DNS** → Alan adının **gerçek IP adresini** döner.
* **Resolver** → Bu bilgiyi toplar, kullanıcıya iletir ve cache’e kaydeder.



---

## 2. Root DNS Sunucuları

* **Root DNS** sadece hangi **TLD (.com, .org, .tr, .han, vs.)** için hangi sunucuların yetkili olduğunu bilir.
* Root DNS → **Alan adının IP’sini bilmez**, sadece yönlendirme yapar.
* Örn: `.com` için **.com TLD sunucularına**, `.org` için **.org TLD sunucularına** yollar.

---

## 3. TLD ve Alan Adı Sağlayıcıları

* `.com`, `.net`, `.org` → ICANN denetiminde, farklı şirketler tarafından yönetiliyor.
* **ccTLD** (ülke uzantıları, ör: `.tr`, `.de`, `.uk`) → her ülkenin kendi kurumları yönetir.

  * `.tr` → **TRABİS (BTK)** tarafından yönetiliyor.
* **Yeni uzantılar (gTLD)** → özel firmalar alıp işletebilir (ör: `.app` → Google, `.xyz` → XYZ Registry).
* Senin dediğin gibi örnek:

  * `.han` uzantısı → ICANN onayıyla bir **registry** (kayıt işletmecisi) tarafından yönetilir.
  * Alan adını almak isteyen kullanıcı → **registrar** (GoDaddy, Namecheap, Google Domains, vs.) üzerinden satın alır.

👉 Yani:

* **Registry** → uzantının sahibi / işletmecisi
* **Registrar** → bize satış yapan aracı firma

---

## 4. Önemli DNS Sunucuları

* **Google DNS** → `8.8.8.8` / `8.8.4.4`
* **Cloudflare DNS** → `1.1.1.1` / `1.0.0.1`
* **OpenDNS (Cisco)** → `208.67.222.222` / `208.67.220.220`
* **Quad9 DNS (Security)** → `9.9.9.9` / `149.112.112.112`

---

## 5. Faydalı Bilgiler

* **TTL (Time To Live)** → Bir DNS kaydının cache’de ne kadar süre tutulacağını belirler.

* **Kayıt Türleri:**

  * `A` → Alan adı → IPv4 adresi
  * `AAAA` → Alan adı → IPv6 adresi
  * `CNAME` → Takma ad → Gerçek alan adı
  * `MX` → Mail sunucuları
  * `NS` → Alan adının yetkili DNS sunucuları
  * `TXT` → Doğrulama / SPF / DKIM kayıtları

* DNS sorgulama:

  ```bash
  nslookup example.com
  dig example.com
  ```






## ICANN ve Alan Adı Sağlayıcıları

* **ICANN (Internet Corporation for Assigned Names and Numbers)**

  * Alan adları, IP adresleri ve DNS’in küresel yönetiminden sorumlu **düzenleyici kurumdur**.
  * Yeni uzantılara (ör: `.app`, `.xyz`, `.han`) izin verir, hangi şirketin hangi uzantıyı işleteceğini belirler.
  * Kendisi **alan adı satışı yapmaz**.

* **Registry (Kayıt Operatörü)**

  * Her uzantının sahibi/işletmecisi olur.
  * Örn: `.com` → **Verisign**, `.app` → **Google**, `.xyz` → **XYZ Registry**.

* **Registrar (Alan Adı Satıcısı)**

  * Bize alan adı satan aracı firmalardır.
  * ICANN tarafından yetkilendirilmiş olmak zorundadır.
  * Örn: GoDaddy, Namecheap, Google Domains, Turhost, IHS vs.

---

👉 Yani zincir şöyle çalışır:

**ICANN → Registry (uzantı sahibi) → Registrar (satıcı) → Biz (kullanıcı)**

---

İ

---

