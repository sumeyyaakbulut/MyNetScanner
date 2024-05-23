# Ağ Tarayıcısı

* Aynı ağ üzerindeki diğer cihazlara saldırı yapılabilir.
* Kendilerini modem gibi gösterip  request ve response kendileri üzerinden geçmesini sağlayabilir.
* Bunu  yapabilmek için  arp spoofing yapıyorlar

#### ARP Nedir
ARP(Adres Çözümleme Protokolü) mac adresi ile ip adresini eşleştirmek için kullanılır.Broadcast request ile yayın isteği yolluyor.Mesela ip adresi 192.168.1.12 olan cihazın mac adresini öğrenmek istiyorum.Eğer ben bunu broadcast request yaparsam , bu ağdaki herkese yollanacaktır. Daha sonra bu arkadaş da ben bu ip sahibim ve mac adresim bu diyecektir.


Kali linux de hazır olarak bulunan bir metwork tarayıcı bulunmaktadır.

Bunun için linux de root terminalinde aşağıdaki işlemleri yapabiliriz.
```cadence
netdiscover -r 10.0.2.1/24
```
Yukarıda netdiscover aracı, ağdaki cihazları keşfetmek için kullanılır. -r seçeneği ile belirli bir IP aralığını tarar. 10.0.2.1/24 ise taranacak IP aralığını( 10.0.2.1 ila 10.0.2.254 arasındaki tüm IP adreslerini tarar) belirtir. Bu komutu çalıştırarak, belirtilen alt ağdaki tüm aktif cihazları ve onların MAC adreslerini keşfedebilirsiniz.

