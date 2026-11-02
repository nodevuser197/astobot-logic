# Devir Notu: Astobot Logic

İlk sürüm: 2026-11-01 · Son güncelleme: 2026-11-02
Repo: `nodevuser197/astobot-logic`, dal `ccr-b33e23c0-c4wffs`
İlk sürüm PR #1 ile `master`'a merge edildi (merge commit `07e165b`).

## 1. Kapsam

- Bu repo bir KiCad 9 projesidir (`logic.kicad_pro`). Kart üretildi ve testleri tamamlandı.
- Bu oturumda donanımda değişiklik yapılmadı.
- Yapılan iş: şematik ve PCB netlist analizi, 2209-A proje çerçevesi, kart üzerine kurulabilecek araştırma konularının çıkarılması.

## 2. Üretilen çıktılar

| Çıktı | Konum | İçerik |
| --- | --- | --- |
| Araştırma yol haritası dokümanı | https://claude.ai/artifact/JPfrGVv9jTu8V66jFifvnz | Platform profili, v1 kısıtları, 8 astronomi konusu, 6 robotik/gömülü konu, 5 projelik kolektif 2209-A programı (şemalı), 2209-A form iskeleti |
| Bu devir notu | `devir.md` | Oturum özeti, teknik referans, TÜBİTAK program eşleşmesi, açık sorular |
| PR #1 | https://github.com/nodevuser197/astobot-logic/pull/1 | `devir.md` ilk sürümü, merge edildi |

## 3. Şematik hiyerarşisi

```
logic.kicad_sch (kök: konnektörler, RPi header)
├── uC.kicad_sch            STM32F446RC, kristal, SWD, BOOT, LED
├── periphals.kicad_sch     USB-UART (CY7C65213), USB-OTG, CAN (SN65HVD232), W25Q16 flash, 3x USB-C
├── esg.kicad_sch           encoderSignaling: 74AHC541 enkoder tamponu
├── analogFilter.kicad_sch  3x RC filtre (PCB'de yer almıyor)
├── SIFilter.kicad_sch      SPI2/I2C2 seri dirençleri + SP0505 ESD
├── Power.kicad_sch         ters polarite, TVS, e-sigorta, UVLO, durum LED'leri
│   └── buckg.kicad_sch x3  TPS565247 buck (5VP yük, 5V lojik, 3V3)
└── isoio.kicad_sch
    ├── isinput.kicad_sch x2  FODM217A izole giriş
    └── isout.kicad_sch x2    TLP175A izole çıkış
```

Kullanılmayan veya artık dosyalar: `untitled.kicad_sch`, `_autosave-untitled.kicad_sch`, `~logic.kicad_sch.lck`, `esg.kicad_sch-bak`. Kök dizindeki Gerber dosyaları `gerber/` klasörünün kopyasıdır.

## 4. PCB özeti

- Katman: 2 (F.Cu, B.Cu), kalınlık 1.6 mm
- Boyut: 90 × 56 mm, köşe yarıçapı 3.5 mm
- 177 footprint, 550 via, 1595 iz segmenti, 25 zone
- Gerber v2: `gerber/logic-v2.zip`

## 5. MCU pin haritası (U1, STM32F446RCT, LQFP-64)

| Fonksiyon | Pinler | Çevre birimi |
| --- | --- | --- |
| Motor PWM (MCP1–4) | PA0, PA1, PA2, PA3 | TIM2 CH1–4 |
| Motor SPI (MC) | PA4 CS, PA5 CK, PA6 SI, PA7 SO | SPI1 |
| Enkoder 1 | PA8 / PA9 | TIM1 |
| Enkoder 2 | PB4 / PB5 | TIM3 |
| Enkoder 3 | PB6 / PB7 | TIM4 |
| Enkoder 4 | PC6 / PC7 | TIM8 |
| I2C1 (J4) | PB8 SD, PB9 CK | I2C1 |
| I2C2 (J6) | PB3 SD, PB10 CK | I2C2 |
| SPI2 (J6) | PB1 CS, PB14 SO, PB15 SI, saat PB10 (I2C2 ile ortak) | SPI2 |
| SPI3 (flash) | PA15 CS, PC10 CK, PC11 SO, PC1 SI | SPI3 |
| CAN | PB12 RX, PB13 TX | CAN2 |
| UART (RPi + USB-UART) | PC12 TX, PD2 RX | UART5 |
| USB OTG | PA11 DN, PA12 DP | OTG_FS |
| İzole giriş | PC5 ISI1, PC4 ISI2 | GPIO |
| İzole çıkış | PC8 ISO1, PC9 ISO2 | GPIO |
| ADC | PC0, PC2, PC3 | ADC1–3 (konnektöre çıkmıyor) |
| IO1–3 (LED) | PC13, PC14, PC15 | GPIO |
| IO4, IO5 | PB0, PA10 | bağlantısız |
| Debug | PA13 SWDIO, PA14 SWCLK | SWD (J11) |
| Saat | PH0/PH1, 12 MHz HSE | LSE yok |

## 6. Konnektörler

| Ref | Tip | Pinler |
| --- | --- | --- |
| J1 | Würth 4P | GND, GND, +BATT, +BATT |
| J3 | 2x6 2.54 mm | 6x +BATT, 6x GND |
| J4 | 2x6 2.54 mm (motor) | 3V3, GND, MC.CS/CK/SI/SO, MCP1–4, I2C1 SD/CK |
| J6 | 2x9 1.27 mm SMD (sensör/enkoder) | 1A–4A, 1B–4B, SPI2 (CS/SI/SO/CLK), I2C2 DATA, 3V3, 5V, GND |
| J5 | DF13 3P (CAN) | CANH, GND, CANL |
| J9 | 8P (izole G/Ç) | OUTCH1/2, PCH1/2, INCH1/2, REFCH1/2 |
| J2 | RPi 4 2x20 | Pin 2/4 HL5V, 8 UART.RX, 10 UART.TX, GND; I2C/SPI/GPIO bağlı değil |
| J11 | 2x3 | SWD debug |

## 7. Güç zinciri

```
+BATT → Q3 DMP2008 (ters polarite, kapı SMBJ26 + 68k) → VDC
VDC → U12/U13/U14 TPS565247 → +5VP (yük), +5V (lojik), +3V3
+5VP → U17 TPS2421 e-sigorta (ISET 91 Ω) → HL5V (Raspberry Pi)
U15 REF3025 + U16 LM397 → +5VP düşük gerilim kilidi → U17 EN
U17 ~FAULT / ~PG → Q1/Q2 MMBTA94 → LED (MCU'ya gitmiyor)
```

TPS565247 giriş üst sınırı 17 V. Bu sınır 4S LiPo'nun tam dolu gerilimine (16.8 V) çok yakın.

## 8. v1 bulguları

| # | Bulgu | Kanıt | Etki |
| --- | --- | --- | --- |
| 1 | ADC1–3 yalnızca U1'e bağlı; R33–35 ve C23–25 PCB'de yok | Netlerde U1 dışında pad yok | Analog sensör kullanılamaz |
| 2 | IO4 (PB0) ve IO5 (PA10) bağlantısız | Netlerde U1 dışında pad yok | Serbest GPIO yok |
| 3 | VBAT +3V3'e bağlı, PC14/15 GPIO olarak kullanılmış | U1 pad 1, 3, 4 | Yedekli RTC ve LSE yok |
| 4 | J4'te tek CS hattı var | J4 pinout | Birden fazla SPI sürücü için daisy-chain gerekir |
| 5 | E-sigortanın FAULT/PG çıkışları MCU'ya gitmiyor; HLCTRL/HLFAULT/HLPG etiketleri kökte bağlı değil | ~FAULT neti: yalnızca U17 + Q1 | Güç arızası yazılımca algılanamaz |
| 6 | Batarya gerilimini ölçen bir bölücü yok | +BATT/VDC netlerinde ADC yok | Enerji metriği ölçülemez |
| 7 | MC.SI PA6'ya (SPI1_MISO) bağlı; SI2/SI3'te SI = MOSI | U1 pad 22/23 | İsimlendirme tutarsız; karşı kartta doğrulanmalı |
| 8 | RPi ile yalnızca UART bağlantısı var | J2 pad listesi | Pi tarafında I2C/SPI erişimi yok |
| 9 | J6'daki CLK hattı (pin 3) PB10'a bağlı. Bu pin hem I2C2_SCL hem SPI2_SCK; SPI2 için ayrı saat hattı yok | R58: /uC/I2C2.CK ↔ /CLK | J6 aynı anda ya SPI2 ya I2C2 modunda çalışır; SPI IMU ile I2C sensör birlikte kullanılamaz |
| 10 | I2C1 ve I2C2 hatlarında kart üzerinde pull-up direnci yok (yalnızca 33 Ω seri direnç var) | I2C netlerinde direnç yalnızca R58/R60 | Pull-up harici modülde olmalı; yoksa bus çalışmaz |

## 9. Önerilen araştırma yönü (özet)

- Çekirdek projeler:
  1. 21 cm HI radyo teleskobu konumlandırıcısı ve otomatik gökyüzü taraması
  2. Enkoderli ekvatoryal kundakta periyodik hata düzeltmesi (PEC) ve işaretleme modeli
- Diğer projeler:
  3. Gözlemevi güvenlik düğümü
  4. Otomatik odak ve çiy kontrolü
  5. PPS zamanlama (isteğe bağlı)
- Ortak v2 revizyonu: ADC konnektörü, FAULT/PG ve batarya ölçümü, VBAT + LSE, PPS girişi, J4'e ikinci CS hattı.
- Mevcut kart 2209-A başvurusunda "ön çalışma" olarak sunulmalı, projenin kendisi olarak değil.

Ayrıntılar dokümanda.

## 10. TÜBİTAK program eşleşmesi

Kart üretilip test edildiği için en doğrudan uyan program 2209-A değil, 2242'dir. 2209-A yapılacak araştırmayı fonlar; 2242 ise TEKNOFEST kapsamında düzenlenen ve tamamlanmış özgün projelerin yarıştığı bir programdır.

| Program | Başvuran | Kartın rolü | Kısıt |
| --- | --- | --- | --- |
| 2242 Üniversite Öğrencileri Araştırma Proje Yarışmaları | Ön lisans/lisans; bireysel veya en fazla 3 kişilik takım; danışman isteğe bağlı | Mevcut kart ve test sonuçları doğrudan proje | Özgün olmalı; ticarileşmiş ürünle başvurulamaz; bir öğrenci tek başvuruda yer alabilir; yılda bir çağrı |
| 2209-A | Lisans + akademik danışman | Ön çalışma; proje yeni araştırma | Dokümanda işlendi |
| 2209-B | Lisans + akademik danışman + sanayi danışmanı | Firma problemine uyarlanmış kontrol kartı | Sanayi kuruluşu şartları; iki danışman aynı kişi olamaz; sanayi onay yazısı |
| 1001 / 1002 / 3501 | Öğretim üyesi veya doktoralı yürütücü; öğrenci bursiyer | Deney altyapısı | Öğrenci yürütücü olamaz |
| 1512 BiGG | Girişimci | Ürün prototipi (INDI uyumlu, CAN'lı kundak/gözlemevi kontrol kartı) | Aşamalı; başvuru tarihinde şirket ortaklığı olmamalı; Ar-Ge içeriği şart |

Güncel çağrı koşulları ve tarihleri tubitak.gov.tr'den doğrulanmalı. Arama sonuçlarındaki en yeni resmî çağrı belgeleri 2022–2023 tarihli.

Kaynaklar:
- 2242: https://tubitak.gov.tr/en/node/14075 · https://tto.gumushane.edu.tr/media/uploads/tto/files/tubitak-2242-universite-ogrencileri-arastrma-proje-yarsmalar.pdf
- 2209-B: https://tubitak.gov.tr/tr/burslar/lisans-onlisans/burs-programlari/2209-b-universite-ogrencileri-sanayiye-yonelik-arastirma-projeleri-destegi-programi
- BiGG: https://tubitak.gov.tr/sites/default/files/2023-09/bigg_2023_yili_1._cagri_duyurusu_230324.pdf

## 11. Ek konu önerileri (dokümanda yok)

| # | Konu | Kartta kullanılanlar | Metrik | Program |
| --- | --- | --- | --- | --- |
| 1 | CubeSat reaksiyon tekerleği yönelim kontrolü test düzeneği | 3–4 motor + enkoder, J6'da IMU, CAN, Pi kamera | Yönelim hatası, oturma süresi, tekerlek doyması | 2209-A, 2242, 1001 |
| 2 | Yıldız izleyici (star tracker) doğrulaması | Pi kamera + plate-solving, referans olarak enkoderli kundak | Kestirim ile enkoder açısı farkı (ark-saniye) | 2209-A, 2242 |
| 3 | CAN hattında gömülü saldırı tespiti | CAN, Cortex-M4 (zamanlama analizi / TinyML), Pi saldırgan ve kayıt cihazı | Tespit oranı, yanlış alarm, gecikme, bellek | 2209-A, 1001, 2209-B |
| 4 | Ağ gecikmeli teleoperasyon | MCU'da yerel kontrol, Pi üzerinden ağ; Smith öngörücü / dalga değişkenleri | Gecikmeye karşı takip hatası, kararlılık sınırı | 2209-A, 2242 |
| 5 | Uzaktan erişimli kontrol laboratuvarı | Ters sarkaç / top-kiriş, Pi web arayüzü | Gecikme altında güvenli deney; eğitim araştırmasında öğrenme çıktısı (etik kurul gerekir) | 2209-A, 1001/1002 |
| 6 | Sera içi bitki fenotipleme robotu | 4 motor + enkoder, Pi kamera, izole çıkışla valf | Konum tekrarlanabilirliği, görüntüden ölçüm hatası | 2209-B, 1001 |
| 7 | Güneş radyo patlaması izleme | HI konumlandırıcısının Güneş takibine genişletilmesi | e-CALLISTO tipi ağ verisiyle karşılaştırma | 2209-A, 2242 |

Konu seçerken bulgu 9 (J6'da SPI2/I2C2 ortak saat), bulgu 10 (pull-up yok) ve kartta akım ölçümünün olmaması dikkate alınmalı. Akım gerekiyorsa J4'teki sürücü kartından veya harici sensörden alınmalı.

## 12. Açık sorular

Dokümana yorum olarak bırakılan üç soru 2026-11-02'de yanıt yazılmadan "çözüldü" olarak kapatıldı. Yanıtlar kayıtlı değil:

1. Ekip kaç öğrenciden oluşuyor? Kolektif program 5 başvuruya göre kurgulandı.
2. Robotik bölümü dokümanda kalacak mı, yoksa doküman yalnızca astronomiye mi odaklanacak?
3. Hazır bir teleskop kundağı var mı, yoksa mekanik sıfırdan mı yapılacak?

## 13. Sonraki adımlar

- [ ] Açık soruları yanıtla, dokümanı güncelle
- [ ] Bulgu 9 ve 10'u dokümanın "v1 kart kısıtları" tablosuna ekle (doküman şu an 7 kısıt listeliyor)
- [ ] Güncel 2209-A çağrı metninden bütçe üst limitini ve danışman başına proje sınırını doğrula
- [ ] 2242'nin bir sonraki çağrı takvimini ve proje rehberini kontrol et; mevcut kartla başvuru için kategori seç
- [ ] Aynı çalışmanın hem 2209-A'da hem 2242'de kullanılabilirliğini iki çağrı metninden doğrula
- [ ] Test kayıtlarını (ripple, e-sigorta limiti, enkoder, CAN/USB/UART) ön çalışma formatında topla
- [ ] v2 revizyon listesini şematiğe işle (ayrı dalda)
- [ ] Repo temizliği: `untitled*`, `.lck` ve `-bak` dosyaları, kökteki yinelenen Gerber'ler, `logic-backups/`
