# README.md

## Kerangka Uji Otomatis UI Web dengan Cucumber, Selenium, Java, dan Gradle

### Deskripsi
Proyek ini adalah kerangka kerja (framework) untuk pengujian otomatis tampilan dan fungsi website menggunakan pendekatan **Behavior-Driven Development (BDD)**. Framework ini dirancang untuk:
- Menulis skenario uji dalam format cerita (Gherkin) menggunakan **Cucumber**.
- Menjalankan test di browser nyata menggunakan **Selenium WebDriver**.
- Mengelola dependensi dan build dengan **Gradle**.
- Mengimplementasikan **Page Object Model (POM)** untuk menjaga kode tetap rapi, modular, dan mudah dipelihara.

Contoh penggunaan: Framework ini menguji halaman login dari situs demo [The Internet](https://the-internet.herokuapp.com/login) (kredensial valid: username `tomsmith`, password `SuperSecretPassword!`). Anda dapat menyesuaikannya untuk website lain dengan mengubah lokator elemen dan URL.

Framework mendukung:
- Tes positif (sukses).
- Tes negatif (gagal).
- Tes batas (edge cases, seperti input kosong atau panjang).

### Prasyarat
Sebelum memulai, pastikan Anda memiliki:
- **Java JDK 11 atau lebih tinggi** (disarankan JDK 17). Cek dengan `java -version`.
- **Gradle 7.0+** (akan diinstal otomatis via wrapper jika menggunakan `gradlew`).
- **Browser**: Chrome (default). Pastikan Chrome terinstal (versi terbaru).
- **IDE**: IntelliJ IDEA, Eclipse, atau VS Code dengan ekstensi Java/Gradle (opsional, tapi direkomendasikan untuk development).
- Akses internet untuk download dependensi (Maven Central).

### Instalasi
1. **Clone atau Buat Proyek**:
   - Jika dari Git: `git clone <repo-url>`.
   - Atau buat folder baru `website-testing-framework` dan salin file-file dari panduan ini (build.gradle, src/, dll.).

2. **Setup Gradle**:
   - Buka terminal di root folder proyek.
   - Jalankan:
     ```
     gradle wrapper  # Buat Gradle Wrapper jika belum ada (opsional)
     gradle build    # Download dependensi dan build proyek
     ```
   - Ini akan mengunduh Cucumber, Selenium, JUnit, dan WebDriverManager secara otomatis.

3. **Konfigurasi Browser**:
   - Framework menggunakan ChromeDriver via WebDriverManager (otomatis setup, tidak perlu download manual).
   - Untuk browser lain (misalnya Firefox), ubah di Step Definitions: Ganti `ChromeDriver` menjadi `FirefoxDriver` dan tambah `WebDriverManager.firefoxdriver().setup()`.

### Struktur Proyek
Struktur folder standar Gradle untuk proyek testing:
```
website-testing-framework/
├── build.gradle          # Konfigurasi dependensi dan build
├── settings.gradle       # Nama proyek
├── gradlew               # Gradle Wrapper (Unix/Mac)
├── gradlew.bat           # Gradle Wrapper (Windows)
├── src/
│   └── test/
│       ├── java/         # Kode Java untuk test
│       │   ├── pages/           # Page Object Model (e.g., LoginPage.java)
│       │   ├── stepdefinitions/ # Implementasi step Gherkin (e.g., LoginSteps.java)
│       │   └── runners/         # Test Runner (e.g., TestRunner.java)
│       └── resources/
│           └── features/        # File skenario Gherkin (e.g., Login.feature)
└── build/                # Output build (laporan, classes) - di-generate otomatis
    └── cucumber-reports/ # Laporan HTML/JSON
```

- **build.gradle**: Dependensi utama (Cucumber 7.14.0, Selenium 4.15.0, JUnit 5.10.0).
- **Page Object Model**: Setiap halaman website punya kelas terpisah (misalnya `LoginPage.java` dengan fungsi seperti `enterUsername()`).
- **Gherkin Files**: Skenario test dalam bahasa alami (Given-When-Then).
- **Step Definitions**: Jembatan antara Gherkin dan Selenium (menggunakan POM).

### Menjalankan Test
1. **Jalankan Semua Test**:
   ```
   gradle clean test
   ```
   - `clean`: Hapus build lama.
   - `test`: Jalankan TestRunner, buka browser, eksekusi skenario, dan tutup browser.

2. **Jalankan Test Spesifik**:
   - Hanya satu kelas: `gradle test --tests runners.TestRunner`.
   - Dengan tag (jika ditambahkan di Gherkin, misalnya `@smoke`): `gradle test --tests runners.TestRunner -Dcucumber.filter.tags="@smoke"`.

3. **Output Console**:
   - Cucumber akan tampilkan skenario Gherkin dengan status (Passed/Failed).
   - Contoh:
     ```
     Feature: Login Functionality
     Scenario: Positive Test - Successful login with valid credentials ✓
     4 Scenarios (4 passed)
     20 Steps (20 passed)
     0m10.500s
     ```

4. **Headless Mode (Tanpa UI Browser)**:
   - Tambah opsi di Step Definitions (di `ChromeDriver`):
     ```java
     ChromeOptions options = new ChromeOptions();
     options.addArguments("--headless");
     driver = new ChromeDriver(options);
     ```

### Laporan Test
Cucumber menghasilkan laporan otomatis setelah test selesai.

1. **Generate Laporan**:
   - Otomatis via plugin di `TestRunner.java` (HTML dan JSON).
   - Lokasi: `build/cucumber-reports/`.

2. **Melihat Laporan HTML**:
   - Buka `build/cucumber-reports/cucumber.html` di browser.
   - Isi: Dashboard overview, detail skenario, step-by-step, dan error (jika ada).
   - Screenshot otomatis untuk test gagal (jika diaktifkan di hooks).

3. **Laporan JSON** (untuk Integrasi CI/CD):
   - File: `build/cucumber-reports/cucumber.json`.
   - Bisa dikonversi ke PDF/HTML custom menggunakan tools seperti Cucumber HTML Reporter.

4. **Custom Laporan**:
   - Untuk laporan lebih advanced, integrasikan Allure: Tambah dependensi di build.gradle dan jalankan `allure serve build/allure-results`.

### Menambahkan Test Baru
1. **Buat Page Object Baru**:
   - Tambah kelas di `pages/` (misalnya `DashboardPage.java`) dengan @FindBy untuk elemen.

2. **Update Gherkin**:
   - Tambah skenario di `features/NewFeature.feature` menggunakan Given-When-Then.

3. **Implementasikan Steps**:
   - Tambah method di `stepdefinitions/` yang link ke POM (misalnya `@When("I click button")`).

4. **Update Runner** (jika perlu):
   - Tambah glue path jika package baru.

5. **Jalankan dan Verifikasi**:
   - `gradle test` untuk test ulang.

Contoh Ekstensi: Tambah tes untuk halaman dashboard setelah login sukses.

### Troubleshooting
- **Error "WebDriver not found"**: Pastikan WebDriverManager setup di steps. Jalankan `gradle clean` dan retry.
- **Elemen Tidak Ditemukan**: Periksa lokator (@FindBy) di POM. Gunakan Inspector browser (F12) untuk ID/CSS/XPath.
- **Timeout**: Naikkan wait time di POM (Duration.ofSeconds(10) → 20).
- **Cucumber Step Undefined**: Pastikan glue path di TestRunner cocok dengan package stepdefinitions.
- **Gradle Error**: Update Gradle (`gradle wrapper --gradle-version 8.5`) atau cek Java version.
- **Browser Tidak Buka**: Cek Chrome terinstal; coba mode headless.
- **Laporan Kosong**: Pastikan plugin di TestRunner benar dan test selesai tanpa crash total.

Jika error spesifik, cek log console (`gradle test --info`) dan bagikan untuk bantuan lebih lanjut.

### Kontribusi
- Fork repo ini dan buat Pull Request untuk fitur baru.
- Tambah lebih banyak skenario atau page untuk website lain.
- Laporkan issue di GitHub jika ada bug.

### Lisensi
Proyek ini open-source di bawah MIT License. Gunakan bebas untuk tujuan edukasi atau komersial, tapi kreditkan sumber jika dimodifikasi.

### Credits
- Dibuat berdasarkan best practices Cucumber-Selenium.
- Situs demo: [The Internet by Sauce Labs](https://github.com/tourdedave/the-internet).
- Dokumentasi: [Cucumber](https://cucumber.io/docs/), [Selenium](https://www.selenium.dev/documentation/).

Selamat menggunakan framework ini! Jika ada pertanyaan, hubungi maintainer. 🚀
