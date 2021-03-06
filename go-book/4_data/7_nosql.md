# NoSQL төрлийн баазууд

NoSQL төрлийн өгөгдлийн сангуудад хэд хэдэн нийтлэг шинж байдаг

* схем байхгүй
* ихэнхи нь уялдаат бус  \(non-relational\)
* хэвтээ чиглэлт өргөтгөх, тэлэх боломжтой учраас тархсан орчинд \(distributed\) сайн ажилладаг 
* асар их хэмжээний өгөгдлийг хадгалах чадвартай
* нээлттэй эхтэй

Гэхдээ зарим ялгаа бий. Эдгээрийг өгөгдлийг хэрхэн хадгалж байгаа байдлаар нь Key-Value, Column Store, Document Store, Graph гэж ангилж болно.

* _Key-Value_:  BoltDB, LevelDB, OpenLDAP
* _Column Store_:  Hadoop, Cassandra
* _Document Store_:  MongoDB, CouchDB
* _Graph_: Cayley, Neo4j, Titan

Тус бүрээс төлөөлөл болгон BoltDB, MongoDB, Cayley өгөгдлийн санг үзэе.

## MongoDB-тэй ажиллах

MongoDB нь Document Store төрлийн өгөгдлийн сангийн систем юм. Схемгүй учраас тойм загвар гаргах, хөгжүүлэлт эхлүүлэхэд маш хялбар, хурдан байдаг.

Өгөгдлийг JSON маягийн баримтууд болгон хадгалдаг, энэ бүтцээ BSON гэж нэрлэдэг. Олон BSON баримтыг нийтэд нь цуглуулга гэж нэрлэнэ.

BSON өгөгдөл:

```json
{
        "_id" : "502fbbd6fec1300be858767e",
        "нэр" : "Сүхбаатар",
        "эцгийн_нэр" : "Ууганбаяр",
        "бүртгэсэн_огноо" : ISODate("2014-04-18T15:59:18.646Z")
}
```

MongoDB-тэй ажиллах дараалал:

* Шаардлагатай сангуудыг импортлох. MongoDB-тэй ажиллахад зориулсан `mgo` нэртэй сан бий.
* `mgo.Dial()` функцээр MongoDB өгөгдлийн сан руу холбогдох
* Холболтоор дамжуулан өгөгдөлтэй ажиллана: хадгалах, хайх, устгах гэх мэт

### Шаардлагатай санг импортлох

Юуны өмнө дараах командаар `mgo` санг татаж суулгах хэрэгтэй

```sh
$ go get gopkg.in/mgo.v2
```

Дараа нь програмдаа импортлоно.

```go
import (
        "gopkg.in/mgo.v2"
        "gopkg.in/mgo.v2/bson"
)
```

### Өгөгдлийн санд холбогдох

```go
session, err := mgo.Dial("server1.example.com")
if err != nil {
  panic(err)
}
defer session.Close()

// нэмэлт тохиргоо хийж болно
session.SetSafe(&mgo.Safe{})
```

`session.SetSafe()` функц нь аюулгүй бичилтийн горимийг тохируулна. `mgo.Safe` нь бичилтийг аюулгүй, найдвартай гүйцэтгэх шаардлагатай болохыг заана. Бичилтийн аюулгүй байдлыг дээшлүүлэх үед бичих хурд багасдаг сөрөг талтай.  

MongoDB нь өндөр хурдан, тасралтгүй ажиллагааг маш сайн хангасан байдаг. Тухайлбал найдвартай байдлыг дээшлүүлэх зорилгоор олон машин дээр тархан байрласан MongoDB баазууд ашиглах боломжтой байдаг. Ийм зохион байгуулалттай өгөгдлийн сангуудийн системийг кластер гэж нэрлэдэг.

Дараах жишээнд server1.example.com болон server2.example.com серверүүд рүү зэрэг холболт үүсгэж байна.

```go
session, err := mgo.Dial("server1.example.com,server2.example.com")
```

Дээрх маягийн холболт үүсгэхэд програм бүх хүсэлтийг хоёр сервер рүү зэрэг илгээнэ. Аль нэг унтарсан эсвэл эвдэрсэн үед програм үлдсэн сервертэй нь ажиллана.

Энэ үед тархан байрласан өгөгдлийн сангуудын хооронд өгөгдлийн зөрүү үүсэх болно. Үүнийг гар аргаар ижилсүүлэх эсвэл sharding тохируулга хийх замаар автоматаар шийдэх хувилбарууд бий.

### Өгөгдөл хадгалах, засах

```go
db := session.DB("testDb")
c:=db.C("Person")

err = c.Insert(&Person{"Ale", "+55 53 8116 9639"},
          &Person{"Cla", "+55 53 8402 8510"})
if err != nil {
    panic(err)
}
```

`Insert()` функцээс гадна `Save()`,  `UpdateId()` функцүүдийг өгөгдөл засахад ашиглаж болно.

### Өгөгдөл хайх

Нэг мөр унших

```go
result := Person{}

c:=db.C("Person")
err = c.Find(bson.M{"нэр": "Ууганбаяр"}).One(&result)
if err != nil {
  panic(err)
}

fmt.Println("Эцгийн нэр:", result.LastName)
```

### Id-аар нэг мөр унших

```go
c := db.C("Person")
q := c.FindId("502fbbd6fec1300be858767e")

// bson.M төрлөөр өгөгдөл уншиж байна
var resp bson.M
if err = q.One(&resp); err!=nil {
  panic(err)
}

fmt.Println("Эцгийн нэр:", resp["LastName"])
```

### Олон мөр өгөгдөл унших \(TODO: жишээг засах\)

```go
// args бүтцээр шаардлагатай аргументийг дамжуулна

// ...
q := c.Find(args.Query)

// талбарууд сонгох
if args.Select != nil {
  q.Select(args.Select)
}

// алгасах
if args.Skip > 0 {
  q.Skip(args.Skip)
}

// хязгаарлах
if args.Limit > 0 {
  q.Limit(args.Limit)
}

// эрэмбэлэх
if len(args.Sort) > 0 {
  q.Sort(args.Sort...)
}

var resp []bson.M
err = q.All(&resp)
```

### Өгөгдөл дээр Javascript код ажиллуулах

Өгөгдлийн сан нь өгөгдөл хадгалах үндсэн үүрэгтэй байдаг. Гэхдээ ихэнхи SQL төрлийн өгөгдлийн сангууд өгөгдлөөс гадна програм хадгалах боломжийг олгодог.

Бааз дээр хадгалсан програмыг ихэвчлэн stored procedure гэж нэрлэдэг. Үүнтэй төстэйгээр MongoDB өгөгдлийн санд javascript код үүсгэж хадгалаад өгөгдөл боловсруулахад ашиглаж болно. Үүнийг сервер талын javascript гэж нэрлэдэг. Мөн eval функцийн тусламжтайгаар javascript кодыг заавал баазад хадгалахгүйгээр шууд ажиллуулж болно.

Дараах жишээнд `hello()` нэртэй javascript кодыг MongoDB дээр ажиллуулж байна.

```go
// javascript эх код
src:=`
function hello(p) {
  var s = p.name;
  return "Hello " + s;
}
`

// дамжуулах параметрүүд
params:= bson.M{"name": "Solongo"}

var runResult bson.M
err := _db.Run(bson.D{
    {"eval", src},
    {"args", []interface{}{params}},
    {"nolock", true}},
    &runResult)

// runResult үр дүнг ашиглах
```

### Өгөгдлийн схем үүсгэх

Өгөгдлийн схем үүсгэж болно, гэхдээ ихэнхидээ шаардлагагүй. Схем үүсгэхгүйгээр `bson.M` төрлийг ямар ч төрлийн мэдээлэлд ашиглаж болно, энэ нь map төрөлтэй ижилхэн.

`Person` баримтад \(BSON\) харгалзах Go бүтэц:

```go
type Person struct {
    Id         bson.ObjectId   "_id,omitempty"
    FirstName  string          "нэр"
    LastName   string          "эцгийн_нэр"
    Inserted   time.Time       "бүртгэсэн_огноо"
}
```

Талбарын нэрийн ард мета өгөгдөл тодорхойлсон байна. Жишээ нь `FirstName string` тодорхойлолтын ард `нэр` гэж бичсэн байна. Энэ нь `Firstname` талбарыг хадгалах, дамжуулах үед `нэр` гэж нэрлэгдэнэ, харин go бүтэц дотор `FirstName` гэж нэрлэгдэнэ гэдгийг заана.

## Key-value төрлийн бааз

Key-value бүтэц нь өгөгдөл хадгалах хамгийн энгийн хэлбэрийн нэг юм. Бараг бүх програмчлалын хэлэнд key-value бүтэц байдаг, ихэвчлэн map, HashTable, dictionary гэх мэтээр нэрлэдэг.

Key-value бүтцийн нийтлэг шинжүүд нь:

* `Set( key, value )`:  `value` утгыг `key` түлхүүрт харгалзуулан санах ойд хадгална, дараа нь энэ `key`-ээр дамжуулан өгөгдлийн олж болно. Хэрэв `key` түлхүүр өмнө нь байсан бол түүнийг дарж бичнэ.
* `Get( key )`: Өгөгдсөн `key`-д харгалзах өгөгдлийг уншина
* `Delete( key )`: `key`-д харгалзах өгөгдлийг устгана.

Key-value бүтцийн цаана ихэвчлэн hash table, B-Tree, Red-black tree зэрэг алгоритм ашигладаг учраас өгөгдөл уншихад маш хурдан байдаг.

Жишээлбэл дараах өгөгдөл байна:

```
Өнгө      Мөнгөлөг
Хэмжээ    13 инч
Нэр       MacBook Air
```

Зүүн талд түлхүүр \(key\), баруун талд утга \(value\) байна. Утга хэсэгт текст, тоо зэрэг ямар ч төрлийн өгөгдөл байж болно.

Түлхүүр нь үл давхардах утга \(ихэвчлэн тэмдэгт мөр\) байх ёстой. Дээрх `Өнгө` гэсэн түлхүүр нь давхардах магадлал маш өндөр. Давхардлаас сэргийлэхийн тулд өмнө нь үг залгаж болно.

```
laptop1_Өнгө      Мөнгөлөг
laptop1_Хэмжээ    13 инч
laptop1_Нэр       MacBook Air

laptop2_Өнгө      Цагаан
laptop2_Хэмжээ    13 инч
laptop2_Нэр       Acer
```

Түлхүүр зохиохдоо өгөгдлийн онцлогоо сайн бодож тооцоолох хэрэгтэй. Ерөнхийдөө аль болох богино, гэхдээ мэдээлэл агуулсан түлхүүр үүсгэхийг хичээх хэрэгтэй.

Түлхүүрийг нь мэдэж байгаа бол өгөгдлийг олж болно, харин түлхүүрийг мэдэхгүй эсвэл утга талбараар хайлт хийх шаардлагатай үед яах вэ гэсэн асуулт гарна.

Түлхүүрийг мэдэхгүй бол бүх мөрөөр давтах хэрэгтэй болно, ингэж давтах явцдаа утгуудыг шалгаад хэрэгтэй өгөгдлөө ялгаж авна. Гэхдээ энэ үйлдэл нь их хэмжээний өгөгдөл дээр маш удаан болно. Үүнийг өөр сагс үүсгэх замаар шийдэж болно. RDBMS өгөгдлийн санд үүнийг _индекс_ гэж нэрлэдэг. Энэ нь  өгөгдлийн тухай нэмэлт өгөгдөл үүсгэж ашиглана гэсэн санаа юм.

## BoltDB

BoltDB бол Go хэл дээр бичигдсэн key-value  төрлийн өгөгдөл хадгалах шийдэл юм.

Bolt нь маш энгийн, ердөө Go хэлний нэг пакет байдаг. Ямар нэг сервер систем, програм суулгах шаардлагагүй, тохиргоо хийх хэрэггүй. `bolt` пакетыг `go get` командаар суулгаад л програмдаа ашиглахад болно.

```sh
$ go get github.com/boltdb/bolt/...
```

Бааз нээхийн тулд `bolt.Open()` функцийг дуудах хэрэгтэй. Энэ функц нь заасан баазын файлыг нээх эсвэл файл үүсээгүй байвал үүсгэнэ.

```go
package main

import (
  "log"
  "github.com/boltdb/bolt"
)

func main() {
    // my.db бааз нээж байна. Ийм файл байхгүй бол үүсгэнэ
    db, err := bolt.Open("my.db", 0600, nil)
    if err != nil {
        log.Fatal(err)
    }
    defer db.Close()

    ...
}
```

Bolt нь баазын файлыг түгжиж бусад процес хандах боломжгүй болгодог. Ингэж түгжигдсэн буюу нээлттэй бааз руу хандалт хийхийг оролдвол програм төгсгөлгүй хүлээлтэд орно. Үүнээс сэргийлэхийн тулд хүлээх хугацааг дараах байдлаар тохируулж өгөх хэрэгтэй:

```go
opts := &bolt.Options{Timeout: 3 * time.Second}
db, err := bolt.Open("my.db", 0600, opts)
```

Bolt баазад өгөгдлүүд _сагс_ гэж нэрлэгдэх хэсгүүдэд хуваагдана. Сагс гэдэг нь key-value олонлогуудыг бүлэглэж нэр оноосоныг хэлнэ. Сагс нь өгөгдлийг бүлэглэж зохион байгуулахад хэрэгтэй байдаг.

Bolt баазын бүх үйлдэл транзакцаар хийгддэг. Bolt транзаагц нь SQL өгөгдлийн сангийн транзаагцтэй ижил ойлголт юм.

Дараах жишээнд `world` нэртэй сагс үүсгээд дотор нь `hello → Hello World!` гэсэн хосыг бичиж байна, дараа нь энэ өгөгдлийг уншиж дэлгэцэнд хэвлэн харуулж байна.

```go
package main

import (
  "fmt"
  "log"
  "github.com/boltdb/bolt"
)

var world = []byte("world")

func main() {
    db, err := bolt.Open("my.db", 0600, nil)
    if err != nil {
        log.Fatal(err)
    }
    defer db.Close()

    key := []byte("hello")
    value := []byte("Hello World!")

    // өгөгдөл бичих
    err = db.Update(func(tx *bolt.Tx) error {
        bucket, err := tx.CreateBucketIfNotExists(world)
        if err != nil {
            return err
        }

        err = bucket.Put(key, value)
        if err != nil {
            return err
        }
        return nil
    })

    if err != nil {
        log.Fatal(err)
    }

    // өгөгдөл унших
    err = db.View(func(tx *bolt.Tx) error {
        bucket := tx.Bucket(world)
        if bucket == nil {
            return fmt.Errorf("Bucket %q not found!", world)
        }

        val := bucket.Get(key)
        fmt.Println(string(val))

        return nil
    })

    if err != nil {
        log.Fatal(err)
    }
}
```

Програмын үр дүн:

```sh
$ Hello World!
```

### BoltDB жишээ: Танилын сүлжээ

Дараах танилуудын мэдээлэл өгөгдсөн байна. Энэ мэдээллийг хадгалах, буцаан хайлт хийхэд зориулсан key-value бааз зохион байгуулая.

![](/5_data/res/relation_net.png)

Атрибут холбоосыг ялгахын тулд урд нь `:` тэмдэг тавьсан байна. Жишээлбэл Цэцгээгийн `:нас`, `:хүйс` гэх мэт.

Зурагт харуулсан мэдээллийг key-value дүрслэлд ойртуулахын тулд дараах гурвалууд болгон хувааж болно.

| Subject \(s\) | Predicate \(p\) | Object \(o\) |
| --- | --- | --- |
| Бат | таньдаг | Дорж |
| Болд | таньдаг | Дорж |
| Цэцгээ | таньдаг | Болд |
| Цэцгээ | таньдаг | Бат |
| Дорж | дүү | Цэцгээ |
| Болд | аав | Сандаг |
| Цэцгээ | :нас | 23 |
| Цэцгээ | :хүйс | эм |

Ингээд `key=Subject|Predicate, value=Object` байдлаар сагс үүсгэж болно.

**spo төрлийн сагс**

```
key=Бат|таньдаг value=Дорж
key=Болд|таньдаг value=Дорж
key=Цэцгээ|таньдаг value=Болд
key=Цэцгээ|таньдаг value=Бат
key=Дорж|дүү  value=Цэцгээ
key=Болд|аав value=Сандаг
key=Цэцгээ|:нас value=23
key=Цэцгээ|:хүйс value=эм
```

Одоо баазаас `Seek()` функцийг ашиглан Цэцгээгийн таньдаг хүмүүсийг ялгаж харуулахад маш хялбархан.

```go
b.View(func(tx *bolt.Tx) error {
  bucket := tx.Bucket(world)

  pre := []byte("Цэцгээ" + "|")

  for k, v := c.Seek(pre); bytes.HasPrefix(k, pre); k, v = c.Next() {
    println(string(v))
  }

  return nil
})
```

Хэрэв холбоосоор \(`Predicate` талбараар\) хайлт хийх бол анхны сагс тохиромжгүй болно. Тухайлбал ах, дүүгийн холбоотой хүмүүсийг олох бол бүх мөрөөр хайх хэрэгтэй болно. Үүнийг шийдэхийн тулд дахин шинэ `key=Predicate|Subject, value=Object` бүтэцтэй сагс нэмж үүсгэж болно.

**pso төрлийн сагс**

```
key=таньдаг|Бат value=Дорж
key=таньдаг|Болд value=Дорж
key=таньдаг|Цэцгээ value=Болд, Бат
key=дүү|Дорж  value=Цэцгээ
key=аав|Болд value=Сандаг
```

Хайлтыг хөнгөвчлөх зорилгоор үүсгэж байгаа энэ сагсыг индекс гэж нэрлэж болно. Энэ мэтээр өгөгдлийн онцлогтоо тохируулан олон бааз, сагс, индекс зохион байгуулах замаар ямар ч мэдээллийг key-value баазад хадгалж болох юм.

Мөн `Predicate`, `Object` талбаруудаар хайлт хийхгүй, зөвхөн `Subject`-аар мэдээллийг хайдаг бол `Predicate`, `Object` хэсгийг нь JSON текст бүтэцтэй болгон хадгалж болно.

```
key=Цэцгээ, value= { ":нас" :23, ":хүйс": "эм" }
```

Буцаан уншихдаа баазаас түлхүүрээр нь олоод value хэсгийн JSON текстийг анхны бүтэц рүү хувиргах юм.

Нэг мэдээллийг дагалдуулан олон туслах индекс, сагс үүсгэх нь өгөгдлийн давхардал үүсгэх, бичилтийн тоог нэмэгдүүлэх хийх зэрэг дутагдалтай талууд бий. Гэхдээ бусад төрлийн баазууд ч \(RDBMS, Document\) үүнтэй ижил аргыг цаанаа ашиглаж байгаа. Ялгаатай нь бусад баазууд индексийг автоматаар зохицуулдаг бол key-value баазын хувьд хөгжүүлэгч өөрөө үүсгэх, засварлах шаардлагатай байдаг. Тухайлбал үндсэн сагсанд шинэ мөр нэмэгдсэн үед харгалзах нэмэлт өгөгдлийг индекс руу бүртгэх хэрэгтэй болно.

Нөгөө талаас key-value төрлийн баазын бичих үйлдэл нь маш хурдан байдаг. Жишээлбэл энэ жишээний SPO маягын 10'000'000 мөрийг хэдхэн секундын дотор бааз руу оруулж бичиж чадна. Тиймээс нэг төрлийн мэдээллийг дагуулж 1-3 хүртэлх нэмэлт сагс үүсгэх нь хурданд онцын сөрөг нөлөө үзүүлдэггүй.

