# Fish Imagıne Classfication  with ANN Model
Kaggle platformu üzerinden A Large Scale Fish Dataset içinde bulunan 9 farklı balık türününü sınıflandıran ANN derin öğrenme modeli ile sınıflandırma tahmin edilmiştir.

# Kaggle Proje Linki

# A Large Scale Fish Dataset
Veri seti 9 farklı deniz ürünü türü içermektedir. Her sınıf için 1000 artırılmış görüntü ve bunların eşleştirilmiş artırılmış ground truth'ları bulunmaktadır. Her sınıf, ground truth etiketleriyle birlikte "Fish_Dataset" dosyasında bulunabilir. Her sınıftaki tüm görüntüler "00000.png"den "01000.png"ye kadar sıralanmıştır.

# Data Preprocessing 

Model seçiminden önce veri ön işleme kısımda  verilerimizi train ve test olarak ayırdıktan sonra train, validation ve test **agumentasyon(veri arttırma)** kullandım bunun sebebi her sınıfın (balık türü) farklı varyasyonlarını öğrenmesine olanak sağlamaktadır ve augmentasyon kullanarak bu sayıyı artırmak, modelin overfitting riskini azaltır. Ayrıca Gerçek dünya senaryolarında görüntüler genellikle farklı açılardan, ışık koşullarında ve arka planlarda gelir. Augmentasyon ile modelinizi bu tür değişkenliklere hazırlayarak daha dayanıklı hale getirmesine olanak sağlamıştır.

Ardından **ImageDataGenerator** sınıfı kullaınılarak bir veri çerçevesinden (dataframe) görüntü verilerini yüklemek için kullanılır. Özellikle derin öğrenme projelerinde, görüntü verilerini yüklemek ve ön işlemek için sıklıkla kullanılan bir yöntemdir. Ayrıca train_images ve val_images kısımlarında Karıştırma (Shuffle), **suffle=True** ile her epoch'ta farklı veri düzenleri ile karşılaşmasını sağlar. Eğitim, doğrulama ve test verilerin eğitim için hazır hale gelmesi ve eğitim süresini azaltmak için resim boyutlarını **target_size**=28,28 olarak belirledim. **Batch size** 64 olarak belirledim bu da bizlere 64 gibi bir değer, genellikle iyi bir denge sağlar ve hem bellek kullanımı hem de işlem hızı açısından etkilidir.

# ANN Model Seçimi

Model tasarımında Flatten, Dense, BathcNormalization ve Dropout katmanlarını kullandım. **Flatten** ile çok boyutlu girdiyi tek boyutlu bir vektöre dönüştürmek için kullandım. Dense katmanını başlangıç olarak 512,256,128,64,9 ile başladım yavaş öğrenme ve düşük başarı oranlarından dolayı bir katman daha ekledim 1024,512,256,128,64,9 olarak yeniden düzenledim. Overfitting için genel olarak **Dropout**, overfittingi önlemek için, eğitim esnasında 0.2 yerine 0.4' e arttırdım overfitting olma olasılığını azaltmayı hedefledim. L2 regularizasyonu (ya da L2 cezası), modelin aşırı öğrenmesini (overfitting) önlemek için kullanılan bir tekniktir. Aşırı öğrenme, bir modelin eğitim verisine çok iyi uyum sağlaması, ancak yeni, görülmemiş verilerde kötü performans göstermesi durumunu sağladım ve ağırlığını düşük kullanarak daha az karmaşık bir yapının oluşmasına olanak sağladı.**BatchNormalization**, aktivasyonları normalleştirir ve optimize eder. Eğitim sürecini hızlandırır ve kararlılık sağlamaktadır.Katmanı eklemek, modelin her mini-batch sırasında öğrenmesini stabilize edebilir. Bu da modelin hem training hem validation accuracy'sini daha dengeli hale getirebilmesine olanak sağladığı iin kullandım. Ayrıca **early_stopping** ile modelin eğitimi halinde herhangi bir iyileşme olmaması halinde erkenden durduurmaya olanak sağlamaktadır.**patience=5**, modelin overfitting'i önlemesi için yeterli olabilir ancak validation accuracy'deki dalgalanmalara karşı daha uzun bir patience değeri kullanmak faydalı olabilir. patience değerini artırma yaptım **patience=20** ile denğesizliği gidermeyi düşündüm.
**LearningRateScheduler**, belirli bir epoch sayısından sonra öğrenme oranını kademeli olarak düşürerek modelin daha hassas bir şekilde öğrenmesini sağlamaktadır. Modelim de 0.01 yerine 0.001 ile değişiklik ile overfitting engellemyi düşündüm. **learning_rate** hızını 0.001 yerine 0.0001 veya daha küçük bir değere ayarlayarak modelin daha istikrarlı öğrenmesini sağlamayı düşünüyorum.
