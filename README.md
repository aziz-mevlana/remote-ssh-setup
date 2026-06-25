# Remote Linux Server & SSH Configuration Project

Bu proje, bulut ortamında (DigitalOcean) çalışan uzak bir Ubuntu Linux sunucusunun güvenli bir şekilde yapılandırılması, asimetrik şifreleme mantığıyla birden fazla SSH anahtar çiftinin root hesabına entegre edilmesi ve SSH konfigürasyon dosyalarıyla bağlantı süreçlerinin optimize edilmesini içermektedir. Ayrıca kaba kuvvet (brute-force) saldırılarını engellemek adına Fail2Ban entegrasyonu sağlanmıştır.

## 🛠️ Gerçekleştirilen Adımlar

### 1. Asimetrik SSH Anahtarlarının Üretilmesi
Yerel makinede (Host) daha güvenli ve modern olan `ed25519` algoritması kullanılarak iki farklı anahtar çifti üretilmiştir:
    
    ssh-keygen -t ed25519 -f ~/.ssh/key_devops -C "devops_auth"
    ssh-keygen -t ed25519 -f ~/.ssh/key_backend -C "backend_auth"

### 2. Uzak Sunucu Yetkilendirmesi & Çoklu Anahtar Tanımlama
* İlk sunucu kurulumunda `key_devops.pub` açık anahtarı bulut paneline tanıtılarak sunucu ayağa kaldırılmıştır.
* Sunucuya erişim sağlandıktan sonra, `key_backend.pub` açık anahtarı da sunucu üzerindeki yetkili anahtarlar listesine (`~/.ssh/authorized_keys`) eklenerek çift anahtarlı giriş mimarisi doğrulanmıştır.

### 3. Manuel Bağlantı Doğrulaması
Farklı özel anahtarlar (Private Keys) işaret edilerek sunucuya root kullanıcısı ile erişim başarıyla test edilmiştir:

    ssh -i ~/.ssh/key_devops root@SUNUCU_IP
    ssh -i ~/.ssh/key_backend root@SUNUCU_IP

### 4. SSH Takma Ad (Alias) Konfigürasyonu
Bağlantı sürecini pratikleştirmek amacıyla yerel makinedeki `~/.ssh/config` dosyası şu şekilde yapılandırılmıştır:

    Host sunucum
        HostName 188.166.164.249
        User root
        IdentityFile ~/.ssh/key_devops

Bu sayede sadece `ssh sunucum` komutuyla güvenli bağlantı sağlanmıştır.

### 5. 🛡️ Güvenlik Sıkılaştırması (Fail2Ban)
Sunucuya yönelik kaba kuvvet saldırılarını engellemek amacıyla Fail2Ban kurulmuş ve SSH portu için maksimum 3 hatalı denemede ilgili IP adresini 10 dakika boyunca engelleyecek şekilde (`/etc/fail2ban/jail.local`) yapılandırılmıştır.

## 💡 Kazanımlar
* Bulut altyapısı (DigitalOcean Droplet) üzerinde Linux sunucu yönetimi.
* Asimetrik şifreleme altyapısı, yetki sınırlandırmaları ve anahtar yönetimi güvenliği.
* SSH config mekanizması ile terminal üretkenliğinin artırılması.
* Fail2Ban ile proaktif sunucu güvenliği.
