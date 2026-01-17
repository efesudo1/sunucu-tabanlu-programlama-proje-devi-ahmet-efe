# ER DİYAGRAMI
## Araç Filo Yönetim ve Karar Destek Sistemi

**Öğrenci:** Ahmet EFE  
**Öğrenci No:** 2022469142

---

## Veritabanı Entity-Relationship Diyagramı

```
┌─────────────────────────────────────┐
│         admin_kullanicilar          │
├─────────────────────────────────────┤
│ ● id (PK) INT AUTO_INCREMENT        │
│   kullanici_adi VARCHAR(50) UNIQUE  │
│   sifre_hash VARCHAR(255)           │
│   ad_soyad VARCHAR(100)             │
│   email VARCHAR(100)                │
│   olusturma_tarihi DATETIME         │
│   son_giris_tarihi DATETIME         │
└─────────────────────────────────────┘



┌─────────────────────────────────────┐
│            guzergahlar              │
├─────────────────────────────────────┤
│ ● id (PK) INT AUTO_INCREMENT        │
│   guzergah_adi VARCHAR(100)         │
│   aciklama TEXT                     │
│   sure_gun INT                      │
│   aktif TINYINT(1)                  │
│   olusturma_tarihi DATETIME         │
└──────────────────┬──────────────────┘
                   │
                   │ 1
                   │
                   │ N
                   ▼
┌─────────────────────────────────────┐
│              turlar                 │
├─────────────────────────────────────┤
│ ● id (PK) INT AUTO_INCREMENT        │
│ ○ guzergah_id (FK) INT              │───────┐
│ ○ arac_id (FK) INT                  │───┐   │
│   baslangic_tarihi DATETIME         │   │   │
│   bitis_tarihi DATETIME             │   │   │
│   yolcu_sayisi INT                  │   │   │
│   fiyat_tl DECIMAL(12,2)            │   │   │
│   notlar TEXT                       │   │   │
│   dis_arac_mi TINYINT(1)            │   │   │
│   olusturma_tarihi DATETIME         │   │   │
└─────────────────────────────────────┘   │   │
                   ▲                      │   │
                   │ N                    │   │
                   │                      │   │
                   │ 1                    │   │
┌──────────────────┴──────────────────┐   │   │
│              araclar                │◄──┘   │
├─────────────────────────────────────┤       │
│ ● id (PK) INT AUTO_INCREMENT        │       │
│   plaka VARCHAR(15) UNIQUE          │       │
│   kapasite INT                      │       │
│   marka VARCHAR(50)                 │       │
│   model VARCHAR(50)                 │       │
│   satin_alma_yili INT               │       │
│   durum ENUM('aktif','pasif',       │       │
│         'bakimda')                  │       │
│   olusturma_tarihi DATETIME         │       │
└─────────────────────────────────────┘       │
                                              │
              ┌───────────────────────────────┘
              │
              ▼
      guzergahlar tablosuna bağlı
```

---

## İlişki Açıklamaları

| İlişki | Tablo 1 | Tablo 2 | Kardinalite | Açıklama |
|--------|---------|---------|-------------|----------|
| **FK1** | turlar | guzergahlar | N:1 | Her tur bir güzergaha aittir |
| **FK2** | turlar | araclar | N:1 | Her tur bir araç ile yapılır |

---

## Foreign Key Tanımları

```sql
-- turlar -> guzergahlar ilişkisi
CONSTRAINT fk_turlar_guzergah 
    FOREIGN KEY (guzergah_id) 
    REFERENCES guzergahlar(id)
    ON DELETE RESTRICT 
    ON UPDATE CASCADE

-- turlar -> araclar ilişkisi  
CONSTRAINT fk_turlar_arac 
    FOREIGN KEY (arac_id) 
    REFERENCES araclar(id)
    ON DELETE SET NULL 
    ON UPDATE CASCADE
```

---

## Veritabanı Kısıtlamaları (Constraints)

| Constraint | Tablo | Açıklama |
|------------|-------|----------|
| `chk_tarih_sirasi` | turlar | Bitiş tarihi başlangıç tarihinden büyük olmalı |
| `chk_yolcu_araligi` | turlar | Yolcu sayısı 1-50 arasında olmalı |

```sql
CONSTRAINT chk_tarih_sirasi 
    CHECK (bitis_tarihi > baslangic_tarihi)

CONSTRAINT chk_yolcu_araligi 
    CHECK (yolcu_sayisi BETWEEN 1 AND 50)
```

---

## Tablo Özeti

| Tablo | Kayıt Sayısı | Açıklama |
|-------|--------------|----------|
| admin_kullanicilar | 1 | Sistem yöneticisi |
| guzergahlar | 4 | Tur güzergahları |
| araclar | 70 | Araç filosu |
| turlar | ~6000+ | Tur kayıtları |

---

**Veritabanı:** MySQL 8.0  
**Charset:** utf8mb4  
**Collation:** utf8mb4_turkish_ci
