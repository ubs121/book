# Пакет

Энэ хүртэл бид багахан хэмжээний, зөвхөн ганц эх файлтай програм бичиж байсан. Програмын хэмжээ өсөж том болохын хэрээр түүнийг олон файл, пакетад хуваах хэрэгтэй болдог. Програмыг олон файлд хувааж, тус тусад нь хөгжүүлэн дараа нь нэг програм руу нийлүүлэх нь оновчтой байдаг.

Ингэхдээ холбоо хамааралтай функц, обектуудыг нэг пакетад байрлуулах нь зүйтэй. Жишээ нь, нэг пакет нь өгөгдөл дээр хайлт, эрэмбэлэлт хийдэг байхад нөгөө нь комплекс тоо боловсруулах функцүүд агуулж болно.

Go файл нь ямар нэг пакетад харъяалагдах ёстой. Пакетын нэрийг файлын эхний мөрөнд бичдэг.

Жишээ нь:

```go
package main
```

Ажиллуулах боломжтой Go програм бүр `main` нэртэй ядаж нэг пакеттай байдаг. Энэ пакетад `main()` буюу эхлэлийн функц байрладаг болохыг бид мэднэ. Харин сан байдлаар ашиглагдах бол `main` пакет байхгүй.

## Пакетыг нэрлэх

Пакетын зохиомж хийх үед нэр сонгох, зөв нэрлэх нь маш чухал байдаг.

Нэрийг эхэндээ зөв онооход хэцүү байдаг. Тодорхой хугацааны дараа програм бүрэлдэх явцад програмын хэсгүүд, тэдгээрийн уялдааг сайн ойлгосны дараа илүү оносон нэр өгөх нь олонтаа байдаг. Таны сонгосон нэр нь та кодынхоо талаар ямар бодолтой байгааг илэрхийлж байдаг учраас нэр сонгохдоо анхаарах хэрэгтэй.

Пакетыг интернэтэд байрлуулах бол пакетын бүтэн зам нь мөн чухал байдаг. Бүтэн замыг бусад пакеттай үл давхардахаар бодож сонгох хэрэгтэй.

Жишээлбэл `websocket` пакетын бүтэн зам нь `code.google.com/p/go.net/websocket` гэж бичигддэг.

Бүтэн замын сүүлийн хэсэг нь ихэвчлэн пакетын нэр байдаг. Жишээ нь `net/http` замаас пакетын нэр нь `http` болно гэх мэт.

Энэ нь хатуу дүрэм биш боловч дагавал зохих хэрэгтэй дүрэм юм. Үгүй бол `foo/bar` бүтэн замтай боловч `quux` нэртэй пакет байвал хөгжүүлэгчийг төөрөгдүүлэх болно.

## Пакетын зохиомж

Пакетын зохиомж хийх үед мэдээллийн урсгалыг анхаарах хэрэгтэй. Мэдээллийн урсгал нь пакетын зохиомж эхлэхээс өмнө сайтар судлагдсан байх ёстой. Ямар мэдээлэл ашиглагдаж байна, мэдээлэл дээр ямар боловсруулалт хийгдэж байна гэдгийг олж харах нь чухал байдаг.

Пакетын сайн зохиомж хийхэд дараах 2 зүйлийг анхаарах хэрэгтэй:

* Өгөгдлийн далдлалт \(пакетын дотор\):  
  Пакет дахь дотоод функцүүдийн уялдаа холбоо сайн байх хэрэгтэй. Пакет дотор аль болох хоорондоо холбоотой зүйлс байрлуулах хэрэгтэй. Хэрэв тухайн пакетэд хамаарал багатай зүйл бол өөр пакет руу шилжүүлэх хэрэгтэй.

* Цомхон интерфэйс \(пакетын гадна\)  
  Пакетын интерфэйс цомхон байх хэрэгтэй. Өөрөөр хэлбэл пакетын гаднаас ашиглаж болох нээлттэй функц, хувьсагчдын тоо цөөн байх хэрэгтэй. Цомхон интерфэйс нь энгийн байдаг, мөн турших, ашиглах, арчлахад хялбар, асуудал, алдаа багатай байдаг.

![](res/packet.png)

Өгөгдлийн далдлалт, интерфэйсийн зохиомж нь хоёулаа сайн пакет зохиох гол түлхүүр байдаг.

Энэ хоёр ухагдахууныг ойлгуулах зорилгоор кофе шоп ба үйлчлүүлэгчийн жишээ авая. Үйлчлүүлэгч арын гал тогоонд юу болох, яаж кофе чанах талаар мэдэхгүй. Тэдгээрийн хооронд ямар нэг харилцаа байхгүй, үйлчлэгч тэдний хооронд зуучлана. Объект хандлагат ертөнцөд тэд бие биенээ олж харахгүй. Тодорхой “үүдээр” тэд кофе, мөнгөө солилцоно.

Энэ далдлалт нь үйлчлүүлэгчид тааламжтай байх болно. Үйлчлүүлэгч кофе чанах процесст санаа тавихгүй. Үйлчлэгч ч мөн адил. Энэ харилцаа нь гал тогоо орвонгоороо дахин шинэчилэгдсэн ч, үйлчлэгч өөрөө кофегоо чанадаг байсан ч хэвээр үлдэх болно. Үйлчлүүлэгч энэ өөрчлөлтийг мэдэхгүй, мэдэх ч шаардлагагүй. Харин “үүд” буюу "кофе захиалах болон мөнгөө төлөх протокол" нь урьдын хэвээр байх хэрэгтэй. Энэ нь өгөгдлийн далдлалт, интерфэйсийн ач холбогдлыг харуулж байна.

### Пакетын дотор

Пакетад хамаарах бүх функц, обектуудыг нэг файлд “овоолох” хэрэггүй. Үүний оронд олон жижиг go файлууд үүсгэж болно. Тэдгээр файлуудын эхний мөрөнд ижил пакетын нэр зааснаар \(`package` түлхүүр үгээр\) ижил пакетад харяалагдана гэдгийг илэрхийлнэ. Файлуудыг хөрвүүлэх үед хөрвүүлэгч ижил толгойтой файлуудыг цуглуулж нэг машины кодонд хөрвүүлдэг.

Цөөн тохиолдолд эсрэгээр байх нь ч бий. Тухайлбал `main` пакет байна. `main` пакет ихэвчлэн том хэмжээтэй байдаг. Програмын гол командууд `main` пакетад байрлах ба тэдгээрийг гаднаас ашиглах шаардлага төдийлөн гарахгүй, тийм учраас зүгээр `main` пакет дотор оруулсан нь энгийн байдаг.

Пакетын дотоод зохиомжийн талаар сайн судлая гэвэл Go хэлний стандарт сангуудын эх кодыг үзэх хэрэгтэй. Зарим пакет нь их том, зарим нь жижиг байгаа. Жишээлбэл `http` пакет 17 go файлтай, 109 нэр \(тогтмол, хувьсагч, төрөл, функц зэргийн нэр\) экспортлодог байхад `hash` пакет 1 файлтай, 3 нэр экспортлодог.

### Пакетын гадна

Нэр том үсгээр эхэлсэн бол тухайн нэрийн дор буй обект \(функц, хувьсагч г.м\) пакетын гадна талаас ашиглах боломжтой болдог. Үүнийг өөрөөр _экспортлох_ гэж нэрлэдэг. Жижиг үсгээр эхэлсэн нэртэй обектууд пакетаас гадна харагдахгүй, гэхдээ пакет дотроо ашиглах боломжтой байдаг.

Экспорт хийсэн обектыг өөр пакет дотор ашиглахдаа пакетын нэрийг цэг \(.\) тэмдэгтэй хамт хэрэглэдэг. Жишээ нь `pack1` пакет доторх `Thing` хувьсагчид хандах бол `pack1.Thing` гэж бичнэ. Хэрэв `pack2` пакет дотор мөн ижил `Thing` нэртэй обект байсан бол `pack2.Thing` гэж бичнэ. Эндээс пакетын нэр нь мөн нэр давхардах асуудлыг шийдэж байгаа нь харагдаж байна. Өөрөөр хэлбэл бусад C\#, Java гэх мэт програмчлалын хэлэнд байдаг нэймспэйсийн үүрэгтэй адил байна.

Програмыг олон пакетад хуваах нь мөн хүмүүст ажил хувиарлах нэг тохиромжтой арга юм. Програм томрохын хэрээр олон програм зохиогч оролцох хэрэгтэй болдог. Энэ үед програм зохиогч болгонд нэг нэг пакетыг хувиарлаж болно. Ингэснээр програм зохиогч бүр өөрийн пакетын дотоод зохиомжид илүү анхаарч ажиллана.

## Пакет, кодын баримтжуулалт

Баримт, тайлбар нь програмыг ойлгомжтой, засвар үйлчилгээ хийхэд хялбар болгодог. Програмын чухал хэсгүүдэд тайлбар заавал хийж сурах хэрэгтэй. Тайлбар нь энгийн, уншихад ойлгомжтой, товч тодорхой байх ёстой.

Тайлбар нь дараах агуулгатай байж болно.

* Програм, кодын хэсэг юу хийдэг болох тухай товч тодорхойлолт
* Яах гэж энэ кодыг бичсэн, юу хийдэг болох тухай
* Кодыг ашиглах жишээ, товч заавар

Баримтыг програмаас тусад нь хөтөлж болох боловч код өөрчлөгдөх үед дагуулж өөрчлөх нь хүндрэлтэй байдаг. Тиймээс програмын кодон дотор тайлбараа хамт оруулж бичдэг болсон. Энэ нь нэг талаар хөгжүүлэгчдэд амар, нөгөө талаар баримтжуулалт хоцрохгүй ач холбогдолтой байдаг.

Сүүлийн үеийн програмчлалын хэлүүд бүгд кодон дотроос тайлбарыг түүвэрлэж баримт үүсгэдэг багажтай болсон. Go хэл энэ хандлагыг дагаад godoc нэртэй багажтай.

Godoc нь Go эх файлыг задлан шинжилж \(тайлбар, жишээ зэргийг\) HTML, текстэн баримт гаргаж авдаг. Энэ баримт нь кодтойгоо нягт холбоотой байдаг, жишээлбэл баримт дотроос ямар нэг функцийн дотоод кодыг сонирхож байвал дээр нь дараад л код руу дамжиж  болно.

Godoc багаж нь кодын тайлбар, жишээнүүдийг эх файлаас ялгаж авч баримт үүсгэнэ. Үүнд нэг дүрэм барих хэрэгтэй: төрөл, хувьсагч, тогтмол, функц, пакет зэргийн баримт, тайлбар үүсгэхдээ түүнд ойр байрлуулах хэрэгтэй, дунд нь сул мөр үлдээж болохгүй. Ингэвэл Godoc багаж тухайн тайлбарыг кодын баримтжуулалт гэж ойлгоод баримтад оруулах болно.

Жишээ нь `fmt.Fprint()` функцийн тайлбарыг харая:

```go
// Fprint formats using the default formats for its operands and
// writes to w.
// Spaces are added between operands when neither is a string.
// It returns the number of bytes written and any write error
// encountered.
func Fprint(w io.Writer, a ...interface{}) (n int, err error) {
```

Дээрх тайлбарын эхний өгүүлбэр нь функцийн нэрээр эхэлсэн байгааг ажиглаарай. Тайлбарын эхний өгүүлбэр нь чухал үүрэгтэй байдаг. Баримтыг текст, HTML, Unix man хуудас гэх мэт олон төрлийн форматаар үүсгэх үед янз бүрээр тайлбар таслагдах шаардлага гарна. Энэ үед эхний өгүүлбэр үлдэх магадлал өндөр байдаг.  
Пакетын тайлбар нь пакетын тухай товч мэдээлэл өгөхөөр байх хэрэгтэй.

```go
// Package sort provides primitives for sorting slices and
// user-defined collections.
package sort
```

Тайлбар хийх зарим хэлбэрийг доор харуулав:

```go
// Энгийн нэг мөр тайлбар

/* Дараагийн мөрийг тайлбарласан энгийн тайлбар */

/*
 * Кодыг тайлбарласан олон мөр тайлбар
 * Зузаан шрифтээр тэмдэглэж болохгүй ч
 * бид **чухал хэсэг** гэж бичиж болно.
 */
```

Тайлбарыг ямар форматтай бичих талаар хатуу дүрэм байхгүй. Гэхдээ Godoc багажид ойлгуулахын тулд зарим жижиг дүрмийг баримтлах хэрэгтэй:

* Цувуулж бичсэн өгүүлбэрүүдийг нэг фараграф гэж ойлгодог. Хэрэв тусдаа фараграф болгохыг хүсвэл дунд нь хоосон мөр оруулах хэрэгтэй.
* Форматтай текст \(жишээ код г.м\) оруулах бол тайлбар текстээс дотогшоо доголдож бичих хэрэгтэй.

  ```go
    /*  Фибоначийн тоон цуваа
    *     f(n) = f(n-1)+f(n-2)
    *   Энд n>1, f(1)=1, f(2)=1 байна.
    */
  ```

* URL буюу интернэт хаягийг HTML холбоос болгож оруулдаг.

Энэ дүрэм нь дагахад хэцүү биш. Go хэлний стандарт сангуудаас олон жишээ харж болно.

Ингээд godoc ашиглан эх файлаас баримт үүсгэе. Godoc багажийг эхлээд суулгах хэрэгтэй.

```sh
$ go get golang.org/x/tools/cmd/godoc
```

Баримт үүсгэхийн тулд терминал дээр дараах командыг өгөх хэрэгтэй:

```sh
$ godoc -http=":6060"
```

Ингээд браузер дээр дараах URL хаягийг бичнэ:

```
http://localhost:6060/pkg/
```

Браузер дээр бүх пакетуудын мэдээлэл харагдана. Үүнд системийн болон таны өөрийн үүсгэсэн пакетууд ч багтана. Зөвхөн өөрийн пакетыг харах бол дээрх хаягийн ард пакетын нэрээ бичиж болно.

Godoc багажаар үүссэн энэ вэб сайтаас өөрийн програм, пакетын тайлбар, баримт хэр болсоныг хялбар шалгаж болно.