# NETBRANCH

## Amaç

GELİŞTİRİLME AŞAMASINDA. Birden fazla şehirde şubesi bulunan bir bir şirket için ağ tasarımı yapıp, bunu olabildiğince CIA üçgeni stantdartlarına uygun şekilde tasarlamak. Amaç bunu raporlamak değil günlük tutmak. O sebeple bu raporda o kadar da resmi olmayacağım.

## Hedefler

- Tüm kullanıcılar internete çıkabilmeli.
- Şubeler arası dosya ve servis erişimi olmalı.
- VLAN politikaları doğru uygulanmalı (izole VLAN erişimleri).
- DNS → Web/Mail erişimi isimle çözülebilmeli.
- Misafir ağı sadece internete çıkabilmeli.
- IT VLAN yöneticileri SSH ile cihazlara erişebilmeli.

## Planlama

Planmada bana gelen projede yapacağım şeyler şunlar.

1. **Topoloji**:
	 - **Merkez Ofis**: Core Router, Firewall, Server Farm (Web, Mail, DNS, DHCP), switchler, VLAN'lar
	 - **Şube 1 & Şube 2**: Router, Switch, VLAN segmentasyonu, kullanıcı PC'ler
	 - Ofisler  **MPLS benzeri bir servis sağlayıcı omurgası** üzerinden birbirine bağlı.

2. **IP Planlama**:
	- Merkez Ofis: 10.0.0.0/24 (VLAN Bağlı alt ağlara bölünecek)
	- Şube 1: 10.1.0.0/24
	- Şube 2: 10.2.0.0/24
	- Her VLAN için farklı subnet ataması yapılacak.

3. **VLAN & Routing**:
	 - VLAN’lar: Yönetim, Muhasebe, IT, Misafir Ağı.
	 - Inter-VLAN Routing için Merkez Ofis Router üzerinde **Router-on-a-Stick** ya da L3 Switch kullanılacak.
	 - Lokasyonlar arası routing için **OSPF veya EIGRP** uygulanacak.

4. **Servisler**:
	- **DHCP Server**: Her VLAN için IP dağıtacak.
	- **DNS Server**: İç ağda domain isim çözümlemesi.
	- **Web Server**: Firma web sayfası (HTTP/HTTPS).
	- **Mail Server**: SMTP/POP3 servisi.

5. **Güvenlik**:
	- Merkez Ofis Router’da **NAT Overload (PAT)** ile internet çıkışı sağla.
	- Firewall üzerinde **ACL’ler** ile:
		- Misafir VLAN → Sadece internete çıkabilsin (iç ağa erişim yok).
		- Muhasebe VLAN → Sadece kendi sunucularına ve internete erişebilsin.
		- IT VLAN → Tüm kaynaklara erişim.
	- SSH üzerinden yönetim erişimi. Telnet yasak.

6. **Ekstra**:
	- **QoS**: VoIP VLAN’ı için bant genişliği önceliği.
	- **IPS/IDS Simulation**: Basit ACL ile saldırı trafiğini engelleme.
	- **Redundancy**: HSRP veya GLBP kullanarak Merkez Ofis’te router yedekliliği.