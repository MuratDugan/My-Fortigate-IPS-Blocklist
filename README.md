# Muronun FortiGate 80E IPS Blocklist
(Kurumsal IPS/UTM lisansları pahalı malum, bende gittim blocklist'imi çıkardım)

Bu repository, FortiGate üzerinde IPS tarafından tespit edilen ve engellenen IP adreslerinden oluşan kişisel blocklist’imdir.
Ayda yaklaşık 2 milyon IPS olayı görünce, “madem lisans alamıyorum, bari blocklist’i kendim çıkarayım” diyerek ortaya çıkmıştır.
(evet gerçekten 2 milyon falan Threat Type blocked by firewall policy)

<img width="390" height="416" alt="image" src="https://github.com/user-attachments/assets/b0ee2276-2d18-48b5-8ca0-d3d6d8bf8135" />


---

## Blocklist Dosyası

Ham IP listesi:

https://raw.githubusercontent.com/MuratDugan/My-Fortigate-IPS-Blocklist/refs/heads/main/blocklist.txt

Dosya sade bir şekilde IP adreslerinden oluşur:

1.2.3.4
5.6.7.8
9.9.9.9

---

## Bu Liste Nasıl Ortaya Çıktı?

Bu blocklist, FortiAnalyzer’dan elde edilen gerçek saldırı denemeleri ve FortiGate’in IPS tetiklemeleri sonucunda oluşan adreslerle manuel olarak güncellenmiştir.

FortiGate üzerinde IPS olayları şu mantıkla sınıflandırılır:

- İlk saldırı: “Seviye 1”
- İkinci saldırı: “Seviye 2”
- Üçüncü saldırı: Kalıcı yasaklama → AUTO-BLOCK

Aşağıda kullanılan Firewall Address Group otomasyonları bulunmaktadır.

---

## FortiGate Tarafındaki Otomasyon Kuralları
<img width="819" height="166" alt="image" src="https://github.com/user-attachments/assets/f18b2c89-26c9-4be1-8052-157e7ff39326" />



### 1) IP nesnesi oluşturma

```
config firewall address
    edit "%%log.srcip%%"
        set subnet %%log.srcip%% 255.255.255.255
    next
end
```
2) İlk tespit → Seviye 1
```
config firewall addrgrp
    edit "IPS_Uyari_Seviye1"
        append member "%%log.srcip%%"
    next
end
```
4) İkinci tespit → Seviye 2’ye yükseltme

```
config firewall addrgrp
    edit "IPS_Uyari_Seviye1"
        unselect member "%%log.srcip%%"
    next
    edit "IPS_Uyari_Seviye2"
        append member "%%log.srcip%%"
    next
end
```
6) Üçüncü tespit → Kalıcı yasaklama (AUTO-BLOCK)
```
config firewall addrgrp
    edit "IPS_Uyari_Seviye2"
        unselect member "%%log.srcip%%"
    next
    edit "AUTO-BLOCK"
        append member "%%log.srcip%%"
    next
end
```
Bu sayede saldıran IP önce izlemeye alınır, ardından gerekirse kalıcı olarak engellenir.




Tamamen kişisel kullanım amacıyla oluşturulmuş bir listedir.

Her IP gerçek loglara ve tekrar eden saldırılara göre eklenir.

Katkı
Pull request gönderebilir, yeni IP önerilerinde bulunabilir veya liste yapısı hakkında fikir verebilirsiniz.
Bu tamamen kişisel bir proje olup herhangi bir ticari amaç taşımamaktadır.

