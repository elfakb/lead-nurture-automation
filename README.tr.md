# Lead Nurture Otomasyonu (n8n)

Bu proje, bir Google Form üzerinden gelen potansiyel müşteri (lead) 
kayıtlarını otomatik olarak segmentlere ayırıp kişiselleştirilmiş 
e-postalar gönderen, tıklama takibi yapan ve etkileşimsiz kullanıcılara 
otomatik hatırlatma maili gönderen uçtan uca bir pazarlama otomasyon 
sistemidir. n8n workflow otomasyon aracı kullanılarak geliştirilmiştir.

## Projenin Amacı

Şirketler genelde form doldurdukları için gelen leadleri tek tip bir 
maille karşılar. Bu proje, kullanıcının form üzerinde belirttiği ilgi 
alanına (Pricing / Features / Demo) göre farklı, kişiselleştirilmiş 
bir e-posta göndererek dönüşüm oranını artırmayı; ayrıca kullanıcı 
maildeki linke tıklamazsa belirli bir süre sonra otomatik hatırlatma 
göndererek kullanıcıyı geri kazanmayı hedefler.

## Sistem Mimarisi

1. Kullanıcı Google Form'u doldurur (Name, Email, Interest)
2. Form yanıtı otomatik olarak Google Sheets'e düşer
3. n8n, yeni satırı yakalar (Google Sheets Trigger)
4. Switch node, kullanıcının ilgi alanına göre 3 branch'ten birine 
   yönlendirir: Pricing / Features / Demo
5. İlgili branch'te kişiye özel bir welcome e-postası gönderilir. 
   Mail içindeki link, doğrudan hedef sayfaya değil, önce n8n'deki 
   bir Webhook'a gider
6. Kullanıcı linke tıklarsa, ayrı bir "Click Tracker" workflow'u 
   tetiklenir ve Google Sheets'teki ilgili satır güncellenir 
   (clicked: TRUE, lead_score: 10, status: engaged)
7. Ana workflow'da bir Wait node, belirli bir süre bekler (gerçek 
   senaryoda 2 gün)
8. Bekleme sonunda sistem Sheets'i tekrar kontrol eder: kullanıcı 
   hâlâ tıklamadıysa otomatik bir hatırlatma maili gönderilir

## Kullanılan Teknolojiler

- n8n (workflow otomasyon platformu)
- Google Forms
- Google Sheets (veritabanı olarak)
- Gmail API (e-posta gönderimi)
- Webhook (tıklama takibi)

## Ekran Görüntüleri

### Google Form
![Google Form](screenshots/01-form.png)

### Google Sheets — Veri Tabanı
![Google Sheet](screenshots/02-google-sheet.png)

### Ana Workflow — Genel Görünüm
![Ana Workflow](screenshots/03-main-workflow-overview.png)

### Gelen Welcome E-postası
![Gelen Mail](screenshots/06-received-email.png)

### Click Tracker Workflow'u
![Click Tracker](screenshots/07-click-tracker-workflow.png)


## Kurulum ve Çalıştırma

1. n8n'i kendi bilgisayarınıza kurun (`npm install n8n -g` ya da Docker ile)
2. `workflows/` klasöründeki iki JSON dosyasını n8n arayüzünden import edin
3. Google Sheets ve Gmail için kendi OAuth2 credential'larınızı oluşturun 
   (Google Cloud Console üzerinden)
4. Kendi Google Form'unuzu oluşturup Sheets bağlantısını yapın, form 
   yapısını Sheet sütunlarıyla eşleştirin
5. Her iki workflow'u da "Active" hale getirin

## Production Notu

Bu proje demo/portfolyo amaçlı geliştirildiği için Webhook URL'i 
localhost üzerinde çalışmaktadır ve dışarıdan (internetten) erişilemez. 
Gerçek bir production ortamında bu URL'in dışarıdan erişilebilir olması 
için ngrok gibi bir tünel servisi kullanılmalı ya da n8n.cloud gibi 
barındırılan bir n8n sürümüne geçilmelidir.

Ayrıca demo'da Wait node 1 dakika olarak ayarlanmıştır, gerçek 
senaryoda bu süre 2 gün olarak kullanılacaktır.

## Geliştirici Notları

Bu proje, form tabanlı lead toplama süreçlerinin nasıl uçtan uca 
otomatikleştirilebileceğini, kod yazmadan (no-code/low-code) 
kişiselleştirilmiş pazarlama akışlarının nasıl kurulabileceğini 
göstermek amacıyla geliştirilmiştir.