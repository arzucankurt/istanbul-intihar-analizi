# Metropol İnsanı Gerçekten Yorgun mu? İstanbul, Ankara ve İzmir'de İntihar Hızı (2009-2025)

**Proje raporu** | Kasım 2026

> **Özet.** "Büyük şehirlerde intihar hızı Türkiye ortalamasından yüksektir" hipotezini, TÜİK'in "olay yerine göre" intihar tablolarıyla 2009-2025 yılları için sınadım. İstanbul'un kaba intihar hızı 17 yılın hiçbirinde Türkiye'nin üstüne çıkmadı (ortalama 3,42, Türkiye 4,39). İzmir 17 yılın 15'inde Türkiye'nin üstünde, Ankara Türkiye düzeyinde. Yani "metropol" tek bir hikâye anlatmıyor. Analiz Python'da yapıldı, sonuçlar Power BI'da tek sayfalık etkileşimli bir raporla sunuldu ve iki araçla bulunan sayılar birbirini doğruladı.

## 1. Konu

Çalışmanın konusu, Türkiye'nin üç büyük şehri olan İstanbul, Ankara ve İzmir'in 2009-2025 yılları arasındaki kaba intihar hızlarının Türkiye ortalamasıyla karşılaştırılmasıdır. Karşılaştırmaya ek olarak İstanbul için vakaların cinsiyete, yaş grubuna ve TÜİK'in kayda geçirdiği nedene göre dağılımı incelenmiştir.

Konu hassas olduğu için bu çalışma bir **tanımlayıcı veri analizidir**: kişilerin ya da olayların nedenlerini açıklamaz, yöntem bilgisi içermez ve nedensellik iddia etmez. Destek arayan ya da çevresinde destek arayan biri olan okuyucular için **112 Acil Çağrı Merkezi** her zaman aranabilir.

Çalışma iki aşamadan oluşur ve aynı soruyu iki araçla yanıtlar:

- **Python (pandas, numpy, matplotlib):** Ham TÜİK dosyalarının temizlenmesi, hipotezin sınanması, grafikler.
- **Power BI:** Veri modeli (yıldız şeması), DAX ölçüleri ve tek sayfalık etkileşimli rapor.

## 2. Önermeler / Araştırma Konusu

**Ana hipotez:** "İstanbul, metropol stresi ve yaşam pahalılığı nedeniyle Türkiye ortalamasından daha yüksek bir intihar riskine (kaba intihar hızı) sahiptir."

Hipotezi sayıya dökmek için şu kuralı koydum: Şehrin kaba hızı Türkiye'den **en az yüzde 10 yüksek** olmalı ve bunu 17 yılın **en az 9 yılında** göstermeli. "Metropol" yalnızca İstanbul anlamına gelmediği için aynı sınama Ankara ve İzmir için de yapıldı.

| No | Araştırma sorusu |
|---|---|
| 1 | İstanbul'un kaba intihar hızı Türkiye ortalamasından yüksek mi? |
| 2 | Ankara ve İzmir de Türkiye'den yüksek mi, yoksa İstanbul sıra dışı bir örnek mi? |
| 3 | İstanbul'da vakalarda erkek/kadın oranı zaman içinde nasıl değişmiş? |
| 4 | Vakalar hangi yaş grubunda yoğunlaşıyor? |
| 5 | TÜİK'in kayda geçirdiği en sık intihar nedeni hangisi? |

**Sonuç özeti**

| Şehir | Ortalama hız (100 bin kişide) | Türkiye ortalaması | Türkiye'nin üstünde olduğu yıl | Hipotez |
|---|---|---|---|---|
| İstanbul | 3,42 | 4,39 | 0 / 17 | Çürütüldü |
| Ankara | 4,30 | 4,39 | 6 / 17 | Desteklenmedi (Türkiye düzeyinde) |
| İzmir | 5,06 | 4,39 | 15 / 17 | Destekleniyor (kural sağlanıyor) |


Diğer bulgular: İstanbul'da vakaların yaklaşık yüzde 78'i erkek (yüzde 78,2); vakalar en çok 25-29 yaşında yoğunlaşıyor; kaydedilen nedenler arasında hastalık, 17 yılın 17'sinde ilk sırada (tüm vakaların yüzde 25,6'sı). Yaş ve neden sonuçları **vaka payıdır, risk oranı değildir**.

## 3. Referanslar / Bağlantılar

**Veri seti**

- TÜİK, *İntihar İstatistikleri*, "olay yerine göre" tabloları: <https://veriportali.tuik.gov.tr/tr/press/58233>. Kullanılan tablolar: kaba intihar hızı (sayfa 37_t38), cinsiyet (37_t37), yaş grubu (37_t39), neden (37_t40). Veri dönemi: 2009-2025 (dört tablonun ortak yılları).

**Teorik kaynaklar**

- Durkheim, É. (1897). *Le Suicide: Étude de sociologie*. Paris: Félix Alcan. (Hipotezin kavramsal arka planı olan toplumsal bütünleşme ve modernleşme tartışması için. Bu çalışma teoriyi test etmez, yalnızca hipotezin sonucunu veriyle sınar.)
- World Health Organization (2014). *Preventing suicide: A global imperative*. Geneva: WHO. <https://sprc.org/online-library/preventing-suicide-a-global-imperative/> (İntihar istatistiklerinin yorumlanması ve sorumlu raporlama için.)

## 4. Veri Seti Sözlüğü

### 4.1 Anahtarlar

| Anahtar | Anlamı | Kullanıldığı yer |
|---|---|---|
| `Il` / `il` | İl adı (İstanbul, Ankara, İzmir, Türkiye ve 81 il) | Olgu tabloları ve `Dim_Il` arasındaki ilişki |
| `Yil` / `yil` | Yıl (2009-2025) | Olgu tabloları ve `Dim_Yil` arasındaki ilişki |
| `YasGrubu` | Yaş aralığı (`<15`, `15-19` ... `75+`, `Bilinmeyen`) | `Fact_Yas` ve `Dim_Yas` |
| `Neden` | TÜİK'in kayda geçirdiği neden kategorisi | `Fact_Neden` ve `Dim_Neden` |

`Fact_IlYil` tablosunda her satır tek bir **il-yıl** kombinasyonudur, bu yüzden birleştirmede ortak anahtar `Il` ve `Yil` birlikte kullanıldı.

### 4.2 Lejant (işaretler ve tanımlar)

| İşaret / terim | Anlamı |
|---|---|
| `-` | TÜİK tablolarında "bilgi yoktur" işareti. Sayıya çevrilemez, bu çalışmada boş (null / NaN) bırakıldı, sıfır yazılmadı. |
| `2024(r)` | TÜİK'in "revize edilmiş" yıl işareti. `(r)` atılarak yıl 2024 olarak okundu. |
| Kaba hız | 100.000 kişide intihar sayısı. Yaş ve cinsiyete göre standartlaştırılmamıştır. |
| Olay yerine göre | Vakaların kişinin ikamet ettiği yere değil, olayın gerçekleştiği yere göre sayılması. |
| Oran | Şehrin ortalama hızı / Türkiye'nin ortalama hızı. 1'den büyükse şehir Türkiye'nin üstündedir. |
| Skor | Şehrin Türkiye'nin üstünde olduğu yıl sayısı / toplam yıl sayısı (örn. 15 / 17). |
| Vaka payı | Bir kategorinin toplam vakalar içindeki yüzdesi. Risk oranı değildir (grup nüfusları bilinmiyor). |
| Diğer / Bilinmeyen | Nedeni belirli bir başlığa koyulamayan vakalar. Nedenlerin yaklaşık yüzde 56'sı bu iki kategoridedir. |

### 4.3 Analiz tablosu: `istanbul_intihar_2009_2025.csv` (17 satır, 32 sütun)

| Sütun | Anlamı |
|---|---|
| `yil` | Yıl (2009-2025) |
| `istanbul_toplam`, `istanbul_erkek`, `istanbul_kadin` | İstanbul'da toplam, erkek ve kadın vaka sayısı |
| `turkiye_toplam` | Türkiye'de toplam vaka sayısı |
| `istanbul_hiz`, `turkiye_hiz`, `ankara_hiz`, `izmir_hiz` | 100 bin kişide kaba intihar hızı |
| `yas_0_14` ... `yas_70_74`, `yas_75_ustu`, `yas_bilinmeyen` | İstanbul'da yaş grubuna göre vaka sayısı (15 sütun) |
| `neden_hastalik`, `neden_aile_gecimsizligi`, `neden_gecim_zorlugu`, `neden_ticari_basarisizlik`, `neden_hissi_iliski`, `neden_ogrenim_basarisizligi`, `neden_diger`, `neden_bilinmeyen` | İstanbul'da TÜİK'in kayda geçirdiği nedene göre vaka sayısı (8 sütun) |

Boş (eksik) değerler yalnızca küçük kategorilerdedir (`yas_bilinmeyen`, `neden_ogrenim_basarisizligi`, `neden_diger`). Hız ve toplam vaka sütunlarında eksik değer yoktur.

### 4.4 Power BI veri modeli (yıldız şeması)

| Tablo | Türü | Sütunlar |
|---|---|---|
| `Fact_IlYil` | Olgu | `Il`, `Yil`, `Hiz`, `ToplamVaka`, `ErkekVaka`, `KadinVaka` |
| `Fact_Yas` | Olgu | `Il`, `Yil`, `YasGrubu`, `Vaka` |
| `Fact_Neden` | Olgu | `Il`, `Yil`, `Neden`, `Vaka` |
| `Dim_Il` | Boyut | `Il`, `Grup` (Metropol üçlüğü / Türkiye / Diğer iller) |
| `Dim_Yil` | Boyut | `Yil`, `Donem` (2012 öncesi / 2012 sonrası) |
| `Dim_Yas` | Boyut | `YasGrubu`, `Sira` (sıralama için) |
| `Dim_Neden` | Boyut | `Neden`, `Sira` (sıralama için) |

İlişkiler boyuttan olguya, bire-çok (1:*) ve tek yönlüdür: `Dim_Il` ve `Dim_Yil` üç olgu tablosuna, `Dim_Yas` `Fact_Yas`'a, `Dim_Neden` `Fact_Neden`'e bağlıdır (8 ilişki).

**DAX ölçüleri**

| Ölçü | Ne hesaplar |
|---|---|
| `Toplam Vaka` | Seçili şehir ve yıllarda vaka toplamı |
| `Ortalama Hız` | Seçili şehrin ortalama kaba hızı |
| `Türkiye Hızı` | Slicer'daki şehir ne olursa olsun Türkiye'nin ortalama hızı (`CALCULATE` ile) |
| `Oran` | `Ortalama Hız` / `Türkiye Hızı` |
| `Üstte Olduğu Yıl Sayısı` | Şehrin hızının Türkiye'nin üstünde olduğu yıl sayısı |
| `Skor` | "Üstte olduğu yıl / toplam yıl" metni (örn. 15 / 17) |
| `Erkek Payı`, `Yaş Payı`, `Neden Payı` | Erkek, yaş grubu ve neden payları |

## 5. Kullanılan Dosyaların Ad ve Açıklamaları

| Dosya | Türü | Açıklama |
|---|---|---|
| `proje_raporu.pdf`, `proje_raporu.md` | Rapor | Bu rapor (aynı içerik, iki biçimde) |
| `istanbul_intihar_analiz.ipynb` | Kod | Ana analiz defteri: veri tanıma, beş araştırma sorusu, grafikler, genel sonuç. Her kod bloğunun öncesinde amaç, altında yorum yazılıdır |
| `veri_olustur.py` | Kod | Dört TÜİK Excel dosyasından analiz CSV'sini üretir (veriyi oluşturan dosya) |
| `istanbul_metropol.pbix` | Kod / rapor | Power BI dosyası: Power Query dönüşümleri, veri modeli, DAX ölçüleri, tek sayfalık rapor |
| `istanbul_intihar_2009_2025.csv` | Veri | Python analizinde kullanılan birleşik tablo (17 satır, 32 sütun) |
| `illere_gore_kaba_intihar_hizi.xlsx` | Veri | TÜİK ham tablo: illere göre kaba intihar hızı (81 il ve Türkiye) |
| `il_ve_cinsiyete_gore_intiharlar.xlsx` | Veri | TÜİK ham tablo: il ve cinsiyete göre vaka sayısı |
| `il_ve_yas_grubuna_gore_intiharlar.xlsx` | Veri | TÜİK ham tablo: il ve yaş grubuna göre vaka sayısı |
| `il_ve_nedene_gore_intiharlar.xlsx` | Veri | TÜİK ham tablo: il ve nedene göre vaka sayısı |

## Ek A. Yöntem: Veri Hazırlama

Ham TÜİK dosyaları doğrudan analize uygun değildir. Karşılaşılan sorunlar ve iki araçtaki çözümleri:

| Sorun | Python (`veri_olustur.py`) | Power BI (Power Query) |
|---|---|---|
| Başlık satırları iki katmanlı, üstte açıklama satırları var | Tablo `header=None` ile okunup doğru satır konumlarından alındı | `Remove Top Rows` ve `Use First Row as Headers` |
| Yıl yalnızca her bloğun ilk satırında yazılı | `ffill()` ile aşağı doğru dolduruldu | `Fill Down` |
| Dipnot ve boş satırlar | İl adına göre süzüldü | `(null)` değerleri filtrelendi |
| `2024(r)` gibi revize işaretli yıllar | `yil_duzelt` fonksiyonu ilk 4 karakteri aldı | `Replace Values` |
| `-` işareti (bilgi yok) | `pd.to_numeric(errors="coerce")` ile boş yapıldı | `Replace Errors` ile null |
| Yıllar sütun, ama analiz için satır gerekiyor | Seçilen satırlar sütun olarak alındı | `Unpivot Other Columns` |
| Cinsiyet kategorisi alt alta (Toplam, Erkek, Kadın) | Satır konumlarıyla ayrı sütunlara alındı | `Pivot Column` (Don't Aggregate) |
| Hız ve cinsiyet tabloları ayrı | `merge(on="yil")` | `Merge Queries as New` (Left Outer, anahtar `Il` + `Yil`) |

**Neden Append değil Merge?** Append tabloları alt alta ekler. Bizde iki tablo farklı bilgi taşıyor (hız ve vaka sayısı) ve aynı il-yıl için yan yana gelmeleri gerekiyordu. Yaş ve neden tabloları satır düzeyi farklı olduğu için (il-yıl-yaş grubu, il-yıl-neden) birleştirilmedi, ayrı olgu tabloları olarak modellendi.

## Ek B. Bulgular ve Python-Power BI Doğrulaması


Aynı veriyi iki araçla bağımsız hesapladım. Sayılar tutuyor:

| Gösterge | Python | Power BI | Durum |
|---|---|---|---|
| İstanbul / Ankara / İzmir ortalama hız | 3,42 / 4,30 / 5,06 | 3,42 / 4,30 / 5,06 | Aynı |
| Türkiye ortalama hız | 4,39 | 4,39 | Aynı |
| İstanbul oran | 0,78 | 0,78 | Aynı |
| Ankara oran | 0,97 | 0,98 | Küçük fark, beklenen |
| İzmir oran | 1,16 | 1,15 | Küçük fark, beklenen |
| Türkiye'nin üstünde olduğu yıl (İstanbul / Ankara / İzmir) | 0 / 6 / 15 | 0 / 6 / 15 | Aynı |
| İstanbul toplam vaka | 8.578 | 8.578 | Aynı |
| İstanbul erkek payı | %78,2 | %78,2 | Aynı |

Ankara ve İzmir'deki 0,01'lik fark bir hata değildir: Python her yılın oranını alıp ortalamasını alır, Power BI ortalama hızları birbirine böler. İki hesap da geçerlidir.

Ayrıca hızların nüfusla tutarlılığı, vaka sayısı ve hızdan nüfusu geri hesaplayarak kontrol edildi (örneğin İstanbul için 2009'da yaklaşık 12,8 milyon, 2025'te yaklaşık 15,7 milyon). Bu değerler gerçek nüfus büyüklükleriyle örtüşüyor.

## Ek C. Sınırlılıklar

- **Kaba hız:** Yaş ve cinsiyete göre standartlaştırılmamıştır. İstanbul'un yaş yapısı Türkiye'den farklıysa farkın bir kısmı bundan kaynaklanıyor olabilir. Bu çalışma farkın nedenini açıklayamaz.
- **Neden verisi:** TÜİK'in kayıt kategorisidir, tanı ya da kesin bilgi değildir. Vakaların yaklaşık yüzde 56'sı "Diğer" ya da "Bilinmeyen" olarak kaydedilmiştir. Bu yüzden "en sık neden hastalıktır" yerine "nedeni kaydedilebilen vakalarda en sık görülen kategori hastalıktır" denmiştir.
- **Vaka payı, risk değil:** Yaş ve cinsiyet sonuçları paydır, grup nüfusları elimde olmadığı için risk oranı hesaplanamadı.
- **2012 kapsam değişikliği:** TÜİK 2012'de kayıt kapsamını genişletmiştir. 2009-2011 ile sonraki yılları tek bir uzun trend gibi okumamak gerekir. Power BI modelinde `Dim_Yil[Donem]` sütunu bunu taşımak için hazırlanmıştır.
- **Küçük seri:** 17 yıllık seriyle çalışıldığı için "ilk 5 yıl / son 5 yıl" karşılaştırmaları kesin kanıt değil, yaklaşık bir fikir verir.
- **Hipotezdeki nedenler:** Metropol stresi ve yaşam pahalılığı doğrudan ölçülemedi, yalnızca hipotezin sonucunun verideki karşılığı test edildi.

*Kendiniz ya da çevrenizde biri için destek arıyorsanız 112 Acil Çağrı Merkezi'ni arayabilirsiniz.*
