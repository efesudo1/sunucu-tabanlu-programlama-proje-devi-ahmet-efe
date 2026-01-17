# API ENDPOİNT LİSTESİ
## Araç Filo Yönetim ve Karar Destek Sistemi

**Öğrenci:** Ahmet EFE  
**Öğrenci No:** 2022469142  
**Base URL:** `http://localhost:3000`

---

## REST API Prensipleri

Bu projede REST (Representational State Transfer) prensipleri uygulanmıştır:

| Prensip | Uygulama |
|---------|----------|
| **Kaynak tabanlı URL'ler** | `/api/analytics/tour-volume` |
| **HTTP metotları** | GET (okuma), POST (yazma) |
| **Stateless iletişim** | JWT token ile kimlik doğrulama |
| **JSON yanıtlar** | Tüm API yanıtları JSON formatında |

---

## 1. Authentication Endpoints

### POST /auth/login
Kullanıcı girişi yapar ve JWT token döndürür.

**Request:**
```json
POST /auth/login
Content-Type: application/json

{
  "username": "admin",
  "password": "Admin123!"
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Giriş başarılı",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 1,
    "username": "admin",
    "name": "Sistem Yöneticisi"
  }
}
```

---

### POST /auth/logout
Oturumu sonlandırır.

**Request:**
```
POST /auth/logout
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Çıkış başarılı"
}
```

---

### GET /auth/me
Mevcut kullanıcı bilgisini döndürür.

**Request:**
```
GET /auth/me
Authorization: Bearer <JWT_TOKEN>
```

**Response (200 OK):**
```json
{
  "success": true,
  "user": {
    "id": 1,
    "username": "admin",
    "name": "Sistem Yöneticisi"
  }
}
```

---

## 2. Analytics Endpoints (JWT Korumalı)

> **Not:** Bu endpoint'ler JWT token gerektirir.  
> Header: `Authorization: Bearer <token>`

---

### GET /api/analytics/summary
Dashboard özet verilerini döndürür.

**Request:**
```
GET /api/analytics/summary?from=2024-01-01&to=2024-12-31
Authorization: Bearer <JWT_TOKEN>
```

**Parameters:**
| Parametre | Tip | Zorunlu | Açıklama |
|-----------|-----|---------|----------|
| from | date | Hayır | Başlangıç tarihi (YYYY-MM-DD) |
| to | date | Hayır | Bitiş tarihi (YYYY-MM-DD) |

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "summary": {
      "toplamTur": 2100,
      "toplamGelir": 126000000,
      "ortalamaYolcu": 7
    },
    "routeStats": [
      { "guzergah_adi": "Kapadokya", "tur_sayisi": 580 },
      { "guzergah_adi": "İstanbul", "tur_sayisi": 540 }
    ],
    "fleetData": {
      "peak_eszamanli_arac": 45,
      "filo_kapasitesi": 70
    }
  },
  "meta": {
    "from": "2024-01-01",
    "to": "2024-12-31"
  }
}
```

---

### GET /api/analytics/tour-volume
Tur yoğunluğu raporunu döndürür.

**Request:**
```
GET /api/analytics/tour-volume?from=2024-01-01&to=2024-12-31&group=month
Authorization: Bearer <JWT_TOKEN>
```

**Parameters:**
| Parametre | Tip | Zorunlu | Açıklama |
|-----------|-----|---------|----------|
| from | date | Evet | Başlangıç tarihi |
| to | date | Evet | Bitiş tarihi |
| group | string | Hayır | Gruplama (day/week/month/year) |

**Response (200 OK):**
```json
{
  "success": true,
  "data": [
    { "ay_yil": "2024-01", "tur_sayisi": 95, "toplam_gelir": 5700000 },
    { "ay_yil": "2024-02", "tur_sayisi": 88, "toplam_gelir": 5280000 }
  ],
  "meta": {
    "from": "2024-01-01",
    "to": "2024-12-31",
    "group": "month"
  }
}
```

---

### GET /api/analytics/route-volume
Güzergah bazlı hacim raporunu döndürür.

**Request:**
```
GET /api/analytics/route-volume?from=2024-01-01&to=2024-12-31&group=month
Authorization: Bearer <JWT_TOKEN>
```

**Parameters:**
| Parametre | Tip | Zorunlu | Açıklama |
|-----------|-----|---------|----------|
| from | date | Evet | Başlangıç tarihi |
| to | date | Evet | Bitiş tarihi |
| group | string | Hayır | Gruplama (day/week/month/year) |

**Response (200 OK):**
```json
{
  "success": true,
  "data": [
    { "guzergah_adi": "Kapadokya", "tur_sayisi": 150, "toplam_gelir": 9000000 },
    { "guzergah_adi": "İstanbul", "tur_sayisi": 140, "toplam_gelir": 8400000 }
  ],
  "meta": { "from": "2024-01-01", "to": "2024-12-31", "group": "month" }
}
```

---

### GET /api/analytics/fleet-concurrency
Filo eşzamanlılık (peak concurrent) raporunu döndürür.

**Request:**
```
GET /api/analytics/fleet-concurrency?from=2024-01-01&to=2024-12-31
Authorization: Bearer <JWT_TOKEN>
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "toplam_tur_sayisi": 2100,
    "dis_kiralama_sayisi": 150,
    "benzersiz_arac_sayisi": 68,
    "peak_eszamanli_arac": 75,
    "filo_kapasitesi": 70,
    "arac_yetersizligi": 5,
    "arac_fazlaligi": 0
  },
  "meta": { "from": "2024-01-01", "to": "2024-12-31", "fleetCapacity": 70 }
}
```

---

### GET /api/analytics/monthly-fleet-balance
Aylık filo dengesi (shortage/surplus) raporunu döndürür.

**Request:**
```
GET /api/analytics/monthly-fleet-balance?year=2024
Authorization: Bearer <JWT_TOKEN>
```

**Parameters:**
| Parametre | Tip | Zorunlu | Açıklama |
|-----------|-----|---------|----------|
| year | integer | Evet | Analiz yılı |

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "allMonths": [
      { "ay": 1, "ay_adi": "Ocak", "tur_sayisi": 95, "peak_eszamanli_arac": 35 },
      { "ay": 4, "ay_adi": "Nisan", "tur_sayisi": 250, "peak_eszamanli_arac": 72 }
    ],
    "shortageMonths": [
      { "ay": 4, "ay_adi": "Nisan", "arac_yetersizligi": 2 }
    ],
    "surplusMonths": [
      { "ay": 1, "ay_adi": "Ocak", "arac_fazlaligi": 35 }
    ]
  }
}
```

---

### GET /api/analytics/recommendations
Yönetici öneri paneli verilerini döndürür.

**Request:**
```
GET /api/analytics/recommendations?year=2024
Authorization: Bearer <JWT_TOKEN>
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "analiz_yili": 2024,
    "yogun_sezon_max_peak": 75,
    "yogun_sezon_ort_peak": 65,
    "dusuk_sezon_min_peak": 25,
    "mevcut_filo": 70,
    "yogun_sezon_onerisi": "Yoğun sezonda 5 adet ek araç kiralanması önerilir.",
    "dusuk_sezon_onerisi": "Düşük sezonda 45 araç kiraya verilebilir."
  }
}
```

---

### GET /api/analytics/daily-tours
Günlük tur verilerini döndürür (Takvim için).

**Request:**
```
GET /api/analytics/daily-tours?from=2024-04-01&to=2024-04-30&routeId=1
Authorization: Bearer <JWT_TOKEN>
```

**Parameters:**
| Parametre | Tip | Zorunlu | Açıklama |
|-----------|-----|---------|----------|
| from | date | Evet | Başlangıç tarihi |
| to | date | Evet | Bitiş tarihi |
| routeId | integer | Hayır | Güzergah filtresi |

**Response (200 OK):**
```json
{
  "success": true,
  "data": [
    { "tarih": "2024-04-01", "tur_sayisi": 8 },
    { "tarih": "2024-04-02", "tur_sayisi": 12 }
  ]
}
```

---

## 3. Admin Endpoints

---

### POST /api/admin/seed
Veritabanı seed işlemi yapar.

**Request:**
```
POST /api/admin/seed
X-Seed-Token: kds-seed-token-2024
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Veritabanı seed işlemi başarıyla tamamlandı.",
  "data": {
    "guzergahSayisi": 4,
    "aracSayisi": 70,
    "turSayisi": 6500,
    "adminSayisi": 1
  },
  "credentials": {
    "username": "admin",
    "password": "Admin123!"
  }
}
```

---

### GET /api/admin/db-status
Veritabanı durumunu döndürür.

**Request:**
```
GET /api/admin/db-status
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "guzergahSayisi": 4,
    "aracSayisi": 70,
    "turSayisi": 6500,
    "adminSayisi": 1,
    "ilkTurTarihi": "2022-04-10",
    "sonTurTarihi": "2025-11-08",
    "seedGerekli": false
  }
}
```

---

### POST /api/admin/test-overlap
Araç çakışma kontrolü testi yapar.

**Request:**
```json
POST /api/admin/test-overlap
Content-Type: application/json

{
  "routeId": 1,
  "vehicleId": 5,
  "startDate": "2024-04-15 09:00:00",
  "endDate": "2024-04-17 18:00:00",
  "paxCount": 8,
  "priceTl": 60000
}
```

**Response (200 OK - Çakışma Yok):**
```json
{
  "success": true,
  "message": "Tur başarıyla oluşturuldu (çakışma yok)",
  "data": { "tur_id": 6501 }
}
```

**Response (409 Conflict - Çakışma Var):**
```json
{
  "success": false,
  "message": "Çakışma tespit edildi!",
  "error": "Bu araç seçilen tarih aralığında başka bir turda kullanılmaktadır."
}
```

---

### GET /api/admin/tour-distribution
Aylık tur dağılımını döndürür.

**Request:**
```
GET /api/admin/tour-distribution
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "distribution": [
      { "yil": 2024, "ay": 1, "tur_sayisi": 95, "sezon_tipi": "NORMAL" },
      { "yil": 2024, "ay": 4, "tur_sayisi": 250, "sezon_tipi": "YOĞUN" }
    ],
    "averages": {
      "yogunSezonOrtalama": 245,
      "normalSezonOrtalama": 92
    }
  }
}
```

---

## HTTP Durum Kodları

| Kod | Durum | Açıklama |
|-----|-------|----------|
| 200 | OK | İstek başarılı |
| 400 | Bad Request | Geçersiz parametre |
| 401 | Unauthorized | Token gerekli veya geçersiz |
| 403 | Forbidden | Yetkisiz erişim |
| 404 | Not Found | Kaynak bulunamadı |
| 409 | Conflict | Çakışma (iş kuralı ihlali) |
| 500 | Internal Server Error | Sunucu hatası |

---

## Hata Yanıtı Formatı

```json
{
  "success": false,
  "message": "Hata açıklaması",
  "error": "Detaylı hata mesajı (development ortamında)"
}
```

---

**Hazırlayan:** Ahmet EFE  
**Tarih:** Ocak 2026
