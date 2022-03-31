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
CREATE TABLE IF NOT EXISTS public.macromusic_user
(
    id integer NOT NULL DEFAULT nextval('user_id_seq'::regclass),
    username text COLLATE pg_catalog."default" NOT NULL,
    password text COLLATE pg_catalog."default" NOT NULL,
    CONSTRAINT macromusic_user_pkey PRIMARY KEY (id)
)
```
```bash
CREATE TABLE IF NOT EXISTS public.macromusic_relation
(
    id integer NOT NULL DEFAULT nextval('macromusic_relation_id_seq'::regclass),
    status text COLLATE pg_catalog."default" NOT NULL,
    receiverid integer NOT NULL,
    senderid integer NOT NULL,
    daterequested date,
    dateanswered date,
    CONSTRAINT macromusic_relation_pkey PRIMARY KEY (id),
    CONSTRAINT order_unique UNIQUE (receiverid, senderid)
)
```
4) Port: 1000 boş olmalı çünkü Grpc serverimiz orada olucak.
5) Env variables aşağıdaki set edilmeli çünkü database id ve şifremiz env variable olarak tutulucak.
```bash
export dbname="macromusic_relation_database"
export db_password="hasannaber123" 
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
  
  ### /getRelation/:senderid/:receiverid
    Burada auth kısmını eklemedim. Böylece istediği şekilde çekebiliyor yoksa da NotFound gönderiyor.

  ## IMPLEMENTATION
  Öncelikle REST kısmında MVC uyguladım böylece controllerimden geçen inputlar service folderıma gidiyor.Ordan da Grpc'ye geçiyor ve Grpc'Den gelen yanıta göre user'a sonuc gönderiyor. Bu arada GRPC serveri Kafka'nın topiclerine gerekli [KAFKA]RELATION CREATED gibi bilgiler yolluyor ve bu sırada Rest serverinde topicleri consume eden kafkaConsumer bulunuyor. Aynı zamanda Redis'ten veri çekildiğinde [REDİS]Key Found gibi loglar da print ediliyor.
  ## EK BILGILER
  User's tablosuna veri eklemek için RestKısmında database_test ekledim. Bu kısım test amaçlı değil de istedğiniz bir user'ı user tablosuna aktarmak için kullanılabilir.

## License
[MIT](https://choosealicense.com/licenses/mit/)
