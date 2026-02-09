# Dərs Cədvəli Pano

Məktəb koridorundakı TV üçün tam ekran “Dərs Cədvəli Pano”. Yalnız `HTML + CSS` və minimal `vanilla JS`. GitHub Pages-də işləyir (JSON faylları `fetch` ilə oxunur).

**Fayl strukturu**
- `index.html`
- `styles.css`
- `data/schedule.json`
- `data/lunch.json`
- `README.md`

**Tez başlama**
1. Repo kökünə bu faylları yerləşdirin.
2. `data/schedule.json` və `data/lunch.json`-u doldurun.
3. GitHub Pages-də publish edin və ya lokalda sadə serverlə açın.

**Qeyd**: Brauzerdə `fetch` lokal fayldan işləməsi üçün faylları serverlə açın. GitHub Pages-də avtomatik işləyir.

## Schedule formatı (`data/schedule.json`)
Sistem iki formatı dəstəkləyir:
- Sinif mərkəzli format (`periods` + `week`)
- Müəllim mərkəzli format (`schedule_data`) - sənin verdiyin IMSA datası bu formadadır

Minimal nümunə:
```json
{
  "school_name": "IMSA - Maarif Schools Kampüsü",
  "term": "2. Dönem",
  "timezone": "Asia/Baku",
  "periods": [
    { "id": 1, "start": "08:30", "end": "09:10" },
    { "id": 2, "start": "09:20", "end": "10:00" }
  ],
  "week": {
    "Mon": {
      "classes": {
        "5A": [
          { "subject": "Riyaziyyat", "teacher": "Kəmalə Əkbərova" },
          { "subject": "Azərbaycan dili", "teacher": "Solmaz Mikayılzadə" }
        ],
        "5B": {
          "1": { "subject": "Riyaziyyat", "teacher": "Kəmalə Əkbərova" },
          "2": { "subject": "Azərbaycan dili", "teacher": "Solmaz Mikayılzadə" }
        }
      }
    }
  }
}
```

Qaydalar:
- `periods` verilərsə prioritetdir. Verilməzsə sistem period sayını datadan tapıb default saatlarla qurur.
- `week` açarları: `Mon`, `Tue`, `Wed`, `Thu`, `Fri` (istəsəniz `Pa`, `Sa`, `Ça`, `Pe`, `Cu` da yazıla bilər).
- `classes` daxilində hər sinif ya **array**, ya da **periodId->lesson** obyekt ola bilər.
- Boş dərs üçün `null` və ya `""` yazıla bilər (UI-də `—` görünür).

Alternativ (müəllim mərkəzli) format nümunəsi:
```json
{
  "school_name": "IMSA - Maarif Schools Kampüsü",
  "generated_date": "30.01.2026",
  "term": "2. Dönem",
  "schedule_data": [
    {
      "teacher": "Fidan Nəzirova",
      "schedule": {
        "Pa": { "1": "Turkish Grade 2A" },
        "Ça": { "2": "Turkish Grade 1A" }
      }
    }
  ]
}
```

## Lunch formatı (`data/lunch.json`)
Sadə və tarixə bağlı menyu formatı dəstəklənir.

Minimal nümunə:
```json
{
  "lunch_start": "12:30",
  "lunch_end": "13:30",
  "preview_time": "11:35",
  "menu": {
    "2026-02-09": {
      "items": [
        "Şorba: Düyülü toyuq şorbası",
        "Əsas yemək: Mantı, qatıq",
        "Qarnir: Sadə plov",
        "Salat: Kələm turşusu",
        "Desert: Meyvə",
        "İçki: Kompot"
      ]
    },
    "Mon": {
      "items": [
        "Şorba: Pomidor şorbası",
        "Əsas yemək: Sobada toyuq tikələri"
      ]
    }
  }
}
```

Dəstəklənən əlavə format (sizdəki aylıq cədvələ uyğun):
```json
{
  "period": "Fevral 2026",
  "menus": {
    "lunch_menu": [
      {
        "date": "9 fevral-Bazar ertəsi",
        "soup": "Düyülü toyuq şorbası",
        "main_course": "Mantı, qatıq",
        "garnish": "Sadə plov",
        "salad": "Kələm turşusu",
        "dessert": "Meyvə",
        "drink": "Kompot"
      }
    ]
  }
}
```

Qaydalar:
- `lunch_start`/`lunch_end` vaxt aralığıdır (12:30–13:30).
- `preview_time` 11:35-də tənəffüs zamanı nahar ön baxışı göstərmək üçündür.
- `menu` daxilində `YYYY-MM-DD` və ya `Mon/Tue/...` açarları ola bilər.

## Davranışlar
- Sağ panel avtomatik **Rotation Mode** ilə 10-15 saniyədən bir aktiv siniflər arasında keçir.
- Sinifə klik edəndə **Pinned Mode** aktiv olur, 60 saniyə toxunulmasa yenidən rotation-a qayıdır.
- Tənəffüs zamanı **5 dəqiqə qalmış** “Dərs başlayır” xəbərdarlığı çıxır.
- Nahar saatında tam ekran overlay göstərilir, bitəndə avtomatik bağlanır.
- Hər 5 dəqiqədən bir data yenilənir; fetch uğursuz olsa 30 saniyədən bir retry edilir.

## Konfiqurasiya
`index.html` içində `CONFIG` obyektindən rahat dəyişə bilərsiniz:
- `timezone` (default `Asia/Baku`)
- `rotationMs`, `pinnedTimeoutMs`, `refreshMs`
- `lunchDefaults` (start/end/preview)

## Qeyd
- Şənbə/Bazar avtomatik “Bu gün dərs yoxdur” göstərilir.
- 24 saatlıq format istifadə olunur.
- Azərbaycanca/Türkcə simvollar üçün UTF‑8 istifadə edilir.
