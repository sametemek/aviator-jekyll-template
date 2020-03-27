---
title: /login
position_number: 1.1
type: post
description: Login
parameters:
  - name: email
    content: Kullanıcı E-Posta Adresi
  - name: gsm
    content: Kullanıcı Cep Telefonu Numarası
  - name: tck
    content: Kullanıcı T.C. Kimlik Numarası
  - name: password
    content: Kullanıcı şifre bilgisi
  - name: clientIp
    content: Kullanıcı Client IP adresi
content_markdown: |-

Doküman Kontrolü
================

| **Tarih**  | **Hazırlayan** | **Versiyon** | **Açıklama**                              |
|------------|----------------|--------------|-------------------------------------------|
| 12.09.2014 | Çağlar Parıltı | 1.0.0        | İlk doküman                               |
| 16.06.2016 | Çağlar Parıltı | 1.1.0        | Servis dönüş parametreleri değişiklikleri |
|            |                |              |                                           |

1. Login
========

1.1.Tanım Bilgisi
-----------------

Ortak Profil Yönetimi(OPY) sistemine herhangi bir kanaldan kayıt olmuş bireysel
kullanıcıların E-Posta, Cep Telefonu numarası veya T.C. Kimlik numarası ile
şifrelerini alarak kanallara girişlerini sağlayan servistir.

1.2.Güvenlik Bilgisi
--------------------

API servislerinde temel iki güvenlik özelliği kullanılmaktadır. Bunlar “public /
private key criptography” kapsamında iletilen verinin hashlenmesi ve ip bazlı
kısıtlama olarak özetlenebilir.

• Public / Private Key Criptography

Web servis isteklerinde her bir istemcinin bir public key i ve bir private key i
olmalıdır. Private key sadece istemci ve sunucu tarafından bilinen ve
paylaşılmaması gereken bir güvenlik anahtarıdır. Public key ise veri iletimi ile
beraber gönderilen ve istemcinin tanınmasını sağlayan güvenlik anahtarıdır. Bu
özellik “man on the middle” veri güvenlik sorununa çözüm olması açısından
oluşturulmuştur. Yani üçüncü bir kişi iletilen veriyi bu özellik sayesinde
manipüle edememektedir. API servisinde bu güvenlik özelliğini sağlayabilmek için
her bir istekle beraber servis metoduna “token” key değerine sahip http request
header bilgisi gönderilmelidir. Bu header parametresi aşağıdaki formülle
oluşturulmalıdır.

token =public key + ‘:’ + base64[ sha1[(1.3. başlığında belirtilen hash input
string’i)]]

Hash bilgisini oluşturan örnek kodlar 4 no’lu Hash Oluşturma Örnekleri
başlığında verilmiştir.

Servis entegrasyonu sırasında kullanacağınız Public ve Private keyler tarafınıza
paylaşılacaktır.

-   IP Bazlı Kısıtlama

API servisleri ip veya ip aralığı bazlı kısıtlanabilmektedir. Yani örneğin
sadece X istemcisinin erişebilmesi için yazılan bir API servisi X istemcisinin
API’ı sorgulayacağı statik ip ye göre kısıtlanabilmektedir. Bu bir ip değil ip
aralığı da olabilir.

Örneğin; X servisi 172.23.4.56 ip sine veya 172.23.4.50-55 ip aralığına göre
sınırlandırılabilir.

**Önemli:** Canlı ortamda, servis erişiminde IP kısıtlaması bulunduğundan servis
canlı ortama alınmadan önce istek yapacak olan canlı ortam sunucu ip bilgisi
iletilmelidir.

2. Servis Bilgileri
===================

2.1.Servis Giriş Parametreleri
------------------------------

| **İsim** | **Tipi** | **Açıklama**                    | **Zorunlu** | **Örnek Değer**          |
|----------|----------|---------------------------------|-------------|--------------------------|
| email    | string   | Kullanıcı E-Posta Adresi        | E           | “ahmetyilmaz\@gmail.com” |
| gsm      | string   | Kullanıcı Cep Telefonu Numarası |             | “5551231212”             |
| tck      | string   | Kullanıcı T.C. Kimlik Numarası  |             | “25457469874”            |
| password | string   | Kullanıcı şifre bilgisi         | E           | “1234qwer”               |
| clientIp | string   | Kullanıcı Client IP adresi      | E           | “127.0.0.1”              |

2.2.Servis Header Bilgisi Güvenlik Parametreleri
------------------------------------------------

| **İsim**        | **Tipi** | **Açıklama**                                                                                                                                                                                                                                                                                                    | **Zorunlu** | **Örnek Değer**                                |
|-----------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------|------------------------------------------------|
| token           | string   | “publicKey:hash” bilgisidir. Hash bilgisi; "*privateKey + email + gsm + tck + password + transactionDate*" alanlarını birbirlerine, verilen sıra ile ekleyerek, SHA1 kriptografik hash fonksiyonun base64 methodu ile encode edilmesi sonucunda oluşur. Null değerler “” (Empty String) olarak kullanılmalıdır. | E           | “TX5JLO1O8XBTI1G:7bVWUITWzkKsSXL3Ok/YCTN8X6c=” |
| transactionDate | string   | İstek zamanı. İşlem zaman aşımına uğramış ise istek reddedilir. “yyyy-MM-dd HH:mm:ss“ formatındadır.                                                                                                                                                                                                            | E           | “2014-08-12 23:59:59”                          |
| version         | string   | Servis versiyon bilgisidir. “1.0” olarak gönderilmelidir.                                                                                                                                                                                                                                                       | E           | “1.0”                                          |
| language        | string   | Dil bilgisidir. \*Dil alanı boş gönderilirse varsayılan dil Türkçe olarak ayarlanmaktadır. \*Desteklenen diller ayrı dokümanda belirtilmiştir.                                                                                                                                                                  | H\*         | “tr-TR”                                        |

2.3.Servis Giriş Parametreleri Client Side Validasyon Bilgileri
---------------------------------------------------------------

| **Parametre**  | **Validasyon**                                                                                                                                       |
|----------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| email          | \\b(\^['_A-Za-z0-9-]+(\\.['_A-Za-z0-9-]+)\*\@([A-Za-z0-9-])+(\\.[A-Za-z0-9-]+)\*((\\.[A-Za-z0-9]{2,})\|(\\.[A-Za-z0-9]{2,}\\.[A-Za-z0-9]{2,}))\$)\\b |
| gsm            | [5][0-9]{2}[0-9]{7}                                                                                                                                  |
| tck            | [1-9][0-9]{10}                                                                                                                                       |
| password       | (password.matches(".\*[a-z]+.\*") \|\| password.matches(".\*[A-Z]+.\*")) && password.matches(".\*\\\\d+.\*"). Minimum 8, Maksimum 20 karakter        |

2.4.Servis Dönüş Parametreleri
------------------------------

| **Parametre**          | **Tipi** | **Açıklama**                                                                                                        | **Örnek Değer**                                        |
|------------------------|----------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------|
| result                 | int      | 1 - Başarılı 0 - Başarısız                                                                                          | 0                                                      |
| responseMessage        | string   | Servis isteği sonucunda kullanıcıya gösterilmesi gereken mesajdır.                                                  | Lütfen cep telefonu numaranızı 10 hane olarak giriniz. |
| errorCode              | int      | result 1 ise boş gelir.                                                                                             | 22                                                     |
| errorMessage           | string   | result 1 ise boş gelir.                                                                                             | Lütfen cep telefonu numaranızı 10 hane olarak giriniz. |
| id                     | int      | Kullanıcı ID                                                                                                        | 1                                                      |
| name                   | string   | Kullanıcı isim bilgisi                                                                                              | “Ahmet”                                                |
| surname                | string   | Kullanıcı soy isim bilgisi                                                                                          | “Yılmaz”                                               |
| email                  | string   | Kullanıcı E-Posta Adresi                                                                                            | “ahmetyilmaz\@gmail.com”                               |
| gsm                    | string   | Kullanıcı Cep Telefonu Numarası                                                                                     | “5551231212”                                           |
| tck                    | string   | Kullanıcı T.C. Kimlik Numarası                                                                                      | “25457469874”                                          |
| birthDate              | string   | Kullanıcı doğum tarihi bilgisi, “yyyy-MM-dd” formatında gönderilecektir.                                            | “1986-10-05”                                           |
| gender                 | int      | Kullanıcı cinsiyet bilgisi 1 - Erkek 2 - Kadın                                                                      | 1                                                      |
| cityCode               | string   | Kullanıcı adres şehir plaka kodu                                                                                    | “06”,”34”                                              |
| address                | string   | Kullanıcının adresi                                                                                                 | “Fulya Mah. Mevlüt Pehlivan Sok. Şişli”                |
| emailVerified          | bit      | E-Posta adresi onay durumu 1 - Onaylı 0 - Onaysız                                                                   | 0                                                      |
| gsmVerified            | bit      | Cep Telefonu numarası onay durumu 1 - Onaylı 0 - Onaysız                                                            | 1                                                      |
| tckVerified            | bit      | T.C. Kimlik numarası onay durumu 1 - Onaylı 0 - Onaysız                                                             | 1                                                      |
| pinCodeProtected       | int      | 1 - Pin kodu kontrolü açık. 0 - Pin kodu kontrolü kapalı.                                                           | 1                                                      |
| pinCodeThresholdAmount | string   | Pin kodu kontrolü için minimum tutar. Kuruş ayracı olmadan gönderilecektir. 1 TL 100 olarak gönderilecektir.        | “100”                                                  |
| contractVersion        | string   | Kullanıcının onayladığı sozleşme versiyonu bilgisidir.                                                              | “v_1.0”                                                |
| sessionToken           | string   | Kullanıcıya ait oturum token bilgisi. SessionToken parametresi zorunlu olan servislerde gönderilecek olan değerdir. | “7bVWUITWzkKsSXL3Ok/YCTN8X6c=”                         |

2.5.Servis Mesajları
--------------------

| **Kod**  | **Teknik Hata Mesajı**                                                              | **Kullanıcı Dostu Mesaj**                                                           |
|----------|-------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
| 1        | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 2        | İstek boş.                                                                          | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 4        | Token bilgisi boş.                                                                  | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 5        | Token bilgisi hatalı.                                                               | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 6        | Açık anahtarı boş.                                                                  | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 7        | Hash bilgisi boş.                                                                   | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 8        | Versiyon bilgisi boş.                                                               | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 9        | Versiyon desteklenmiyor.                                                            | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 10       | İstek zamanı bilgisi boş.                                                           | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 11       | İstek zamanı header bilgisi hatalı.                                                 | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 12       | İstek zamanı ile server zamanı uyuşmuyor.                                           | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 13       | Açık anahtarı hatalı.                                                               | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 14       | Göndermiş olduğunuz token doğru değil.                                              | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |
| 44       | Kullanıcı adı alanı boş olamaz.                                                     | Kullanıcı adı alanı boş olamaz.                                                     |
| 45       | E-posta adresiniz ya da şifreniz yanlış.                                            | E-posta adresiniz ya da şifreniz yanlış.                                            |
| 46       | Cep Telefonu numaranız ya da şifreniz yanlış.                                       | Cep Telefonu numaranız ya da şifreniz yanlış.                                       |
| 47       | T.C. kimlik numaranız ya da şifreniz yanlış.                                        | T.C. kimlik numaranız ya da şifreniz yanlış.                                        |
| 2010     | Dil alanı hatalı!                                                                   | İşleminiz gerçekleştirilirken beklenmedik bir hata oluştu. Lütfen tekrar deneyiniz. |

2.6.Servis İstek Adresleri
--------------------------

Canlı Ortam Adresi : https://opy.ipara.com/opy/user/login

Test Ortamı Adresi : https://opytest.ipara.com/opy/user/login

3.JSON Değerleri
================

3.1. Input
----------

| { "email": "ahmetyilmaz\@gmail.com", "gsm": "", "tck": "", "password": "1234qwer", "clientIp": "127.0.0.1" } |
|--------------------------------------------------------------------------------------------------------------|


**3.2. Output**

*Başarılı;*

| { "result": "1", "id": "17", "name": "Ahmet", "surname": "Yilmaz", "email": "ahmetyilmaz\@gmail.com", "gsm": "5559849271", "tck": "36527347292", "address": "Mevlüt Pehlivan Sokak Fulya Mahallesi Multinet Plaza No:12", "cityCode": "34", "birthDate": "1990-07-07", "gender": "1", "firstLogin": "0", "emailVerified":"0", "gsmVerified":"1", "tckVerified":"0", “pinCodeProtected”:”1”, “pinCodeThresholdAmount”:”2336”, “contractVersion”:”v1.0”, “sessionToken”: “7bVWUITWzkKsSXL3Ok/YCTN8X6c=” } |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


*Hatalı;*

| { "result": "0", "responseMessage": "E-posta adresiniz ya da şifreniz yanlış.", "errorCode": "45", "errorMessage": "E-posta adresiniz ya da şifreniz yanlış." } |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|


4.Hash Oluşturma Örnekleri
==========================

4.1. .Net
---------

| public static string GetSHA1(string SHA1Data) { SHA1 sha = new SHA1CryptoServiceProvider(); byte[] hashbytes = System.Text.Encoding.UTF8.GetBytes(SHA1Data); byte[] inputbytes = sha.ComputeHash(hashbytes); return Convert.ToBase64String(inputbytes); } |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


4.2. Java
---------

| public static String getSHA1Text(String text) throws Exception { MessageDigest sha1 = MessageDigest.getInstance("SHA-1"); return (new BASE64Encoder()).encode(sha1.digest(text.getBytes("UTF-8"))); } |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


---


