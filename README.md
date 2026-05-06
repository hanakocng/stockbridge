# stockbridge

# 📦 StockBridge System - Dokumentasi Trigger

StockBridge adalah sistem manajemen gudang IT yang memanfaatkan konsep **Trigger (pemicu)** untuk mengotomatisasi aliran data antara perangkat mobile (HP), aplikasi desktop (C#), dan database SQLite. Sistem ini dirancang untuk mengurangi input manual, meningkatkan efisiensi kerja, serta menjaga konsistensi dan integritas data secara menyeluruh.

----------

## ⚙️ Arsitektur Trigger

Dalam sistem ini terdapat tiga jenis trigger utama:

1.  Remote Request Trigger (Network Level)
    
2.  Manager Approval Trigger (Application Level)
    
3.  SQL Integrity Trigger (Database Level)
    

Ketiga trigger ini saling terhubung dan membentuk sistem yang otomatis dan real-time.

----------

## 📡 1. Remote Request Trigger (HP → Laptop)

**Event:**

-   Staf melakukan scan Barcode / QR Code menggunakan HP
    

**Trigger:**

-   HP mengirim HTTP request ke IP laptop (port 8080)
    
-   Contoh: `http://172.18.8.191:8080/?id=4`
    

**Proses Otomatis:**

-   `HttpListener` menerima request
    
-   Mengambil ID barang dari URL
    
-   Menjalankan `OpenUpdateFromMobile()`
    

**Hasil:**

-   Pop-up **Update Stok** muncul otomatis di laptop
    

----------

## ✅ 2. Manager Approval Trigger (Application Level)

**Event:**

-   Manager menekan tombol **Approve**
    

**Trigger:**

-   Method `BtnApprove_Click` dijalankan
    

**Proses Otomatis:**

1.  Update `requests` → `Approved`
    
2.  Hitung perubahan stok
    
3.  Update tabel `products`
    
4.  Simpan ke `stock_logs`
    

**Hasil:**

-   Stok berubah hanya setelah validasi manager
    

----------

## 🗄️ 3. SQL Integrity Trigger (Database Level)

**Event:**

-   Terjadi `UPDATE` pada `products.base_stock`
    

**Trigger:**

-   `AFTER UPDATE` di SQLite
    

**Proses Otomatis:**

-   Database mendeteksi perubahan
    
-   Insert otomatis ke `stock_logs`
    

**Logika SQL:**

```sql
CREATE TRIGGER log_stok_otomatis
AFTER UPDATE OF base_stock ON products
BEGIN
    INSERT INTO stock_logs (product_id, type, total_affected, created_at)
    VALUES (new.id, 'Update', new.base_stock, DATETIME('now'));
END;

```

**Hasil:**

-   Riwayat stok tercatat otomatis dan akurat
    


## 📊 Perbandingan Trigger

| Jenis Trigger     | Lokasi Eksekusi    | Media Pemicu      | Fungsi Utama                                     |
|-------------------|--------------------|--------------------|--------------------------------------------------|
| Mobile Trigger    | C# (Application)   | HTTP / WiFi        | Scan HP ke laptop secara real-time              |
| Approval Trigger  | C# (Logic)         | UI (Klik tombol)   | Validasi perubahan stok oleh manager            |
| SQL Trigger       | SQLite (Database)  | Query internal     | Logging stok otomatis                           |
## 🔄 Alur Sistem

1.  Staf scan barang menggunakan HP
    
2.  Data dikirim ke laptop via HTTP
    
3.  Laptop membuka form update stok
    
4.  Manager melakukan approval
    
5.  Sistem update stok
    
6.  Database mencatat log otomatis
    

----------

## 🎯 Kesimpulan

StockBridge menggunakan 3 layer trigger:

-   **Network Trigger** → komunikasi HP ke laptop
    
-   **Application Trigger** → logika bisnis & validasi
    
-   **Database Trigger** → integritas & logging
    

Hasilnya:

-   Sistem otomatis & real-time
    
-   Minim input manual
    
-   Data tervalidasi
    
-   Terintegrasi antar perangkat
    

StockBridge bukan sekadar aplikasi, tetapi sistem manajemen gudang yang efisien dan terstruktur.

<img width="1199" height="782" alt="erd stockbridge drawio" src="https://github.com/user-attachments/assets/c462efd1-09ab-453e-ac96-c35bd0d9cda7" />

