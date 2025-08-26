# NETBRANCH

## Amaç

Birden fazla şubesi bulunan bir bir şirket için ağ tasarımı yapıp, bunu olabildiğince CIA üçgeni stantdartlarına uygun şekilde tasarlamak.

## Hedefler

- Tüm kullanıcılar internete çıkabilmeli.
- Şubeler arası dosya ve servis erişimi olmalı.
- VLAN politikaları doğru uygulanmalı (izole VLAN erişimleri).
- DNS → Web/Mail erişimi isimle çözülebilmeli.
- Misafir ağı sadece internete çıkabilmeli.
- IT VLAN yöneticileri SSH ile cihazlara erişebilmeli.

## 1. Topoloji

- **Merkez Ofis**
    - Core Router + Firewall
    - Server Farm (Web, Mail, DNS, DHCP)
    - L3 Switch (VLAN Routing)
    - Wired kullanıcılar için Switchler
    - **Kablosuz Altyapı**:
        - 2× Wireless LAN Controller (yüksek erişilebilirlik için)
        - Lightweight Access Point’ler (her katta)
        - SSID’ler:
            - **CorpWiFi** (802.1X + RADIUS kimlik doğrulama)
            - **GuestWiFi** (Captive Portal + ACL ile yalnızca internete erişim)
                
- **Şube 1 & Şube 2**
    - Router, Switch, VLAN segmentasyonu
    - Kablosuz ağ için Access Point’ler (SSID’ler merkez ofisle aynı)
    - Kullanıcı PC’leri + mobil cihazlar
        
- **Bağlantı**
    - Ofisler **MPLS benzeri servis sağlayıcı omurgası** üzerinden birbirine bağlı.

## 2. IP Planlama
- Merkez Ofis: `10.0.0.0/24` → VLAN’lara bölünecek
- Şube 1: `10.1.0.0/24`
- Şube 2: `10.2.0.0/24`
- VLAN/Subnet Örnekleri:
    - VLAN 10 – Yönetim: `10.0.10.0/24`
    - VLAN 20 – Muhasebe: `10.0.20.0/24`
    - VLAN 30 – IT: `10.0.30.0/24`
    - VLAN 40 – Misafir: `10.0.40.0/24`
    - VLAN 50 – VoIP: `10.0.50.0/24`
    - VLAN 60 – Kablosuz Kurumsal: `10.0.60.0/24`
    - VLAN 70 – Kablosuz Misafir: `10.0.70.0/24`

## 3. VLAN & Routing
- VLAN’lar: Yönetim, Muhasebe, IT, Misafir, VoIP, Kablosuz (Kurumsal + Misafir).
- Inter-VLAN Routing → Merkez Ofis L3 Switch veya Router-on-a-Stick.
- Lokasyonlar arası Routing → **OSPF** veya **EIGRP**.

## 4. Servisler
- **DHCP Server**: Tüm VLAN’lara (özellikle kablosuz VLAN’lara) IP dağıtır.
- **DNS Server**: İç domain çözümlemesi.
- **Web Server**: HTTP/HTTPS ile kurumsal site.
- **Mail Server**: SMTP/POP3.
- **RADIUS Server**: Kablosuz kurumsal ağda 802.1X kimlik doğrulama için.
- **Captive Portal**: Misafir Wi-Fi kullanıcıları için internet öncesi yönlendirme sayfası.

## 5. Güvenlik
- Merkez Ofis Router’da **NAT Overload (PAT)** ile internet çıkışı.
- Firewall / ACL Politikaları:
    - **Guest VLAN (wired + wireless)** → sadece internete çıkabilir.
    - **Accounting VLAN** → yalnızca kendi sunucularına ve internete erişebilir.
    - **IT VLAN** → tüm kaynaklara erişebilir.
    - **Wireless Corp VLAN** → LAN kaynaklarına erişebilir, Guest VLAN’dan izole.
- Yönetim → sadece **SSH**, Telnet yasak.
- Wireless tarafında **WPA2-Enterprise** (802.1X + RADIUS).

## 6. Ekstra
- **QoS**: VoIP VLAN ve kurumsal kablosuz için önceliklendirme.
- **IPS/IDS Simulation**: ACL + basit intrusion detection denemeleri.
- **Redundancy**:
    - Merkez Ofis Router’larda **HSRP/GLBP**.
    - WLC’lerde yedeklilik.
- **Kablosuz Roaming**: Kullanıcılar ofis içinde AP’ler arası geçişte IP değişmeden devam edebilecek.
    

## Tasarlama


