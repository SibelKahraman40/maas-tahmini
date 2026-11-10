# Maaş Tahmini

Bu projede yaş, deneyim yılı, iş unvanı ve eğitim seviyesinden maaş tahmini yaptım. Doğrusal regresyonu başlangıç modeli olarak kullandım ve Random Forest ile karşılaştırdım. Son model 300 ağaçtan oluşuyor.

## Veri ve yöntem

- Maaşı eksik satırları çıkardım. Kalan 6.699 kaydın 5.359'unu eğitim, 1.340'ını test için ayırdım.
- Eğitim seviyesindeki farklı yazımları birleştirdim ve metin sütunlarını one-hot encoding ile kodladım.
- Eğitimde beşten az görülen iş unvanlarını `Other` kategorisine aldım. Tahmin sırasında eğitimde hiç görülmeyen unvanlara da aynı kuralı uyguladım.
- Modelin yeni profillerdeki performansını ölçmek için yaş, deneyim yılı, iş unvanı ve eğitim seviyesi aynı olan satırları birlikte grupladım. Bu doğrulamada aynı profil hem eğitimde hem doğrulamada yer almadı.

Veri dosyasını [ders deposundaki `Salary_Data.csv` dosyasından](https://github.com/mustafagurbuz06/03.11.2026-class/blob/main/Salary_Data.csv) alıp `salary_experiments.ipynb` ile aynı klasöre koyun.

## Sonuçlar

| Model | Profil bazlı doğrulama MAE |
|---|---:|
| Doğrusal regresyon | 13.974,68 |
| Random Forest (300 ağaç) | 12.652,87 |
| Random Forest + nadir iş unvanlarını gruplama | **12.487,92** |
| Random Forest + gruplama + unvan seviyesi | 12.753,42 |

Nadir unvanları gruplamak, bu kuralla `Other` olarak kodlanan 60 doğrulama kaydında MAE'yi **12.621,75'ten 11.000,01'e** düşürdü. Unvandan çıkarılan `senior` ve `director` gibi seviyeleri ayrı bir sütun olarak eklemek hatayı artırdığı için son modelde bu sütunu kullanmadım.

Seçtiğim modeli 5.359 eğitim kaydının tamamıyla yeniden eğittim. Daha önce de kullanılan 1.340 test kaydında **MAE 3.309,71** ve **R² 0,9773** elde ettim. Profil bazlı doğrulama, modelin eğitimde birebir görmediği profilleri değerlendirdiği için bu sonucu test skorundan ayrı raporladım.

## Docker ile çalıştırma

Docker Desktop'ı açın. Windows PowerShell'de notebook ile CSV dosyasının bulunduğu klasöre girip şu komutu çalıştırın:

```powershell
docker run --rm -it -p 127.0.0.1:8888:8888 --mount "type=bind,source=$($PWD.Path),target=/work" -w /work python:3.12-slim sh -c "pip install notebook pandas scikit-learn seaborn matplotlib && jupyter notebook --ip=0.0.0.0 --no-browser --allow-root"
```

Paketler kurulunca terminalde çıkan `http://127.0.0.1:8888/` adresini `token=` kısmıyla birlikte tarayıcıda açın. `salary_experiments.ipynb` dosyasını açıp hücreleri sırayla çalıştırın. VS Code kullanıyorsanız aynı adresi **Select Kernel → Existing Jupyter Server** seçeneğine girebilirsiniz. Notebook çalışırken terminali açık tutun.
