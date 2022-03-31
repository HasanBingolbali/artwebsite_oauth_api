# Macromusic_user_relations
   Bu taskte 
-Postgres 
-Redis
-Golang
-Kafka 
-Grpc
-Fiber
 kullanılarak makromusik ilişkiler yapısının simulasyonu oluşturuldu
## Çalıştırmak için requirements
  1) Redis localhost:????'da çalışmalı
  2) kafka locahost:????'da çalışmalı ve quickstart-events adlı bir topic olusturulmalı
  3) Database çalışmalı ve bu iki query postgreste calıstırmalı ve iki adet database-table oluşturulmalı
```bash
pip install foobar
```
```bash
pip install foobar
```
4) Port: 1000 boş olmalı çünkü Grpc serverimiz orada olucak.
5) Env variables aşağıdaki set edilmeli çünkü database id ve şifremiz env variable olarak tutulucak.
```bash
pip install foobar
```

# Kullanım

  ##ENDPOINTLER  
  Öncelikle databasemizide 5 adet endpoint var.
  ### /ping
  sistemimiz çalışıyorsa pong döndürüyor.
  ### /connect/:receiverid
  Bu endpointimiz için body kısmında user'ın id ve passwordu yollanıyor. Eğer receiverId user databasemizde yoksa not found döndürüyoruz.
  Eğer UserId ve passwordumuz daha önce cache'e set edilmişse cache'den get ediliyor ve ortalama 5-6 ms'de sonuc döndürülüyor.
  Eğer cache yoksa databaseden veri çekiyoruz(tabi burada encrypted şekilde tutuluyor ve encyrpted şifrelerin eşleşmesi pahalı olduğu için) yaklaşık 1 saniye sürüyor.Buradan Cache'in çalıştığını görüyoruz.
  
  Postman görünümü:
  
  Database görünümü:
  
  ### /accept/:receiverid
    Bu endpointimiz için body kısmında tekrardan user'ın id ve passwordu yollanıyor.
    Bu endpointimizin çağrılması için önceden connect edilmesi lazım ve default olarak Database'da relations kısmı Pending olarak görüküyor.
  Connect' kısmındaki receiverId burada kabul eden taraf olucağı için onun username ve şifresi'ni body'de yollaması lazım.
    Bu auth işlemi tekrardan cache'de tutuluyor
  
   ### /reject/:receiverid
    Accept'in aynısı fakat status column'u PENDING'TEN DENIED'A çeviriyor
  
  ###/getRelation/:senderid/:receiverid
    Burada auth kısmını eklemedim. Böylece istediği şekilde çekebiliyor yoksa da bad request gönderiyor.

  ##IMPLEMENTATION
  Öncelikle REST kısmında MVC uyguladım böylece controllerimden geçen inputlar service folderıma gidiyor.Ordan da Grpc'ye geçiyor ve Grpc'Den gelen yanıta göre 
  

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)
