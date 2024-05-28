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

### Python Kullanarak Ağ Tarayıcı Oluşturma

* Kullanıcıdan ip adresini almak için get_user_input() isimde bir fonksiyon oluşturalım.
* Daha sonra instance oluşturalım.(parse_object)
* Sonra add option diyerek optionları ekleyelim.(Burada içerisine istediğimiz kadar arg(kullanıcıdan alınacak input belirtmek için kullanılır).
* Biz burada kullanıcadan ip adresi istiyoruz, eğer ip adresi girmediğiyse ip adresi istediğimizi söylüyoruz.
  
```cadence
def get_user_input():
    parse_object = optparse.OptionParser()
    parse_object.add_option("-i","--ipaddress",dest = "ip_address",help="Enter IP address")
    (user_input,arguments) = parse_object.parse_args()

    if not user_input.ip_address:
        print("Enter ip address")
    return user_input
```

* İşlemi üç ana adımda gerçekleştirebilirsiniz: ARP isteği (ARP request), yayılım (broadcast), ve yanıt (response).
* Kali Linux'ta scapy kullanarak scapy.ls(scapy.ARP()) komutunu çalıştırdığınızda, ARP (Address Resolution Protocol) sınıfındaki tüm alanları (fields) ve bu alanların varsayılan değerlerini görebilirsiniz. Bu komut, ARP paketinin hangi alanlara sahip olduğunu ve bu alanların ne işe yaradığını gösterir.
* Kali Linux'ta scapy.ls(scapy.ARP()) komutunu çalıştırdığınızda alacağınız çıktıda, psrc: Kaynak IP adresini belirtir,pdst: Hedef IP adresini belirtir. Biz burada pdst kullanıcıdan ip adreseni kullanıcağız.
* scapy.ls(scapy.Ether()) komutu, Ethernet çerçevesinin başlık alanlarını ve bu alanların varsayılan değerlerini listeler. Bu alanlar arasında yayılım (broadcast) adresi olan 'ff:ff:ff:ff:ff:ff' hedef MAC adresi bulunmaktadır.
* Broadcast MAC Address: ff:ff:ff:ff:ff:ff olarak tanımlanır. Bu adres, ağdaki tüm cihazlara verinin gönderileceği anlamına gelir.
* Destination MAC Address (dest): Eğer bir Ethernet çerçevesi oluştururken dst (destination) alanını belirtmezseniz, bu alan varsayılan olarak ff:ff:ff:ff:ff:ff olur. Bu, yayılım (broadcast) yapacağınız anlamına gelir.
* Daha sonra biz iki tane istekte bulunduk. Bu istekleri scapy ile birleştiren bir methodu vardır.oda aşağıdaki şekilde yapılır.(combined_packet)
* SRP(birleştirilmiş paket yollamak) ve sr(tek tek pakaet yollamak) bu fonksiyondan cevap verilen verilmeyen içerikleri döndürür. Yani mesala 10.0.2.2 şuna ait yada 10.0.2.4 cevap verilmemiş olanbilir
* Sarp yazıp göndermek istediğimiz paketi yazarız ve bir tane daha input ekleriz bu da timeout  cevap verilmez ise bekleme devam et çünkü biz bütün range tarayacağız.![image]

  
```cadence
def scan_my_network(ip):

    arp_request_packet = scapy.ARP(pdst=ip)
    #scapy.ls(scapy.ARP())
    broadcast_packet = scapy.Ether(dst="ff:ff:ff:ff:ff:ff")
    #scapy.ls(scapy.Ether())
    combined_packet = broadcast_packet/arp_request_packet
    (answer_list, unaswer_list) = scapy.srp(combined_packet,timeout =1)
    answer_list.summary()
```

* Daha sonra oluşturduğumuz fonksiyonları çağırırız.
