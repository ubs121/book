# Дебааг хийх

TODO: засах [https://blog.gopheracademy.com/advent-2015/debugging-with-delve/](https://blog.gopheracademy.com/advent-2015/debugging-with-delve/)

Ихэнхи бааг зүгээр эх код дээр нь ажиглалт хийгээд илрэхээргүй байдаг. Тиймээс тусгай дебааг хийх багажийг ашиглах хэрэгтэй.

Дебааг хийгч багаж нь програмыг тусгай орчинд ачаалан, ажиллагаан дээр нь хяналт тавьж чаддаг. Жишээлбэл, програмын заавруудыг алхам алхамаар нь биелүүлэх, биелэлтийг зогсоож хувьсагчийн утгыг унших, функцийн дуудалтыг шинжлэх, санах ойн байрлал, програмын төлөв, хувьсагчийн өөрчлөлтийг ажиглах, харах боломжтой байдаг.

Хамгийн түгээмэл хэрэглэгддэг дебааг хийгч багаж бол GNU debugger буюу GDB гэж нэрлэгдэх багаж юм. GDB нь C, C++ програмыг дебааг хийх зориулалттай боловч Go програмыг дебааг хийхэд мөн ашиглаж болно. Гэхдээ  GDB нь зарим Go хэлний онцлогтой хэсгийг дебааг хийж чаддаггүй.

Go хэлэнд зориулсан Delve нэртэй дебааг хийх багаж бий. Энэ багажийг дараах командаар татаж суулгаж болно.

```sh
$ go get -u github.com/derekparker/delve/cmd/dlv
```

Жишээ болгон алдаатай програмыг энэ багажаар хэрхэн шинжлэхийг үзэе. Дараах debug\_example.go жишээ програм нь логикийн алдаатай байгаа. Энэ програмын алдааг Delve багажаар илрүүлэе.

```go
package main
import "fmt"

// 2 бүхэл тоон хувьсагчийн утгыг хооронд нь солино.
func swap(p1 *int, p2 *int) {
  tmp := p1
  p1 = p2
  p2 = tmp
}

func main( ) {
 a:=10 ; b:=20

 fmt.Printf( "Анхны утгууд: a = %d; b = %d\n", a, b )
 swap( &a, &b )
 fmt.Printf( "Шинэ утгууд: a = %d; b = %d\n", a, b )
}
```

Юуны өмнө програмын эх кодыг хөрвүүлж машины код үүсгэх хэрэгтэй.

```sh
$ go build -gcflags "-N -l" debug_example.go
```

Одоо програмыг ажиллуулая.

```sh
$ ./debug_example
```

Програм нь дараах мэдээллийг дэлгэцэнд гаргана:

```sh
Анхны утгууд: a = 10; b = 20
Шинэ утгууд: a = 10; b = 20
```

`swap()` функцийг дуудсан боловч `a` болон `b` хувьсагчдын утга солигдоогүй байна. Үүний шалтгааныг Delve багажаар олоё.

Дебааг хийхийн тулд командын мөрнөөс GDB багажид турших гэж буй програмын биелэх файлыг параметр болгон зааж эхлүүлнэ:

```sh
$ gdb ./gdb_example
(gdb)
```

GDB нь програмыг санах ойд ачаалах боловч ажиллуулалгүй хүлээлтийн төлөвт оруулаад `(gdb)` гэсэн урилгын мэдээллийг харуулна. Энэ нь таныг ямар заавар өгөхийг хүлээж байгаа хэрэг юм.

Хамгийн эхлээд `list` буюу товчоор `l` командаар эхлэе. Энэ команд нь туршиж буй програмын эх кодоос эхний 10 мөрийг дэлгэцэнд харуулна. Жишээ нь дараах байдалтай:

```sh
(gdb) l
4     
5    // 2 бүхэл тоон хувьсагчийн утгыг хооронд нь солино.
6 func swap(p1 *int, p2 *int) {
7      tmp := p1
8      p1 = p2
9      p2 = tmp
10    }
11     
12     
13 func main( ) {
`
```

Дахин `list` команд өгвөл эх кодын дараагийн мөрүүдийг харуулна.

Дараагийн алхамд програмын ажиллагааг ямар цэг дээр зогсоохыг GDB-д хэлэх хэрэгтэй. Энэ цэгийг зогсолтын цэг \(breakpoint\) гэж нэрлэдэг. Програмын биелэлт зогсолтын цэг дээр ирэхэд GDB нь програмын ажиллагааг таслаж тухайн цэг дээр програмыг шинжлэх боломжийг олгодог. Цаашлаад зогсолтын цэгээс үргэлжлүүлэн програмын заавруудыг нэг нэгээр нь ажиллуулж програмын ажиллагааг шинжилж болно.

Зогсолтын цэгийг заахын тулд `breakpoint` буюу `b` командыг ашиглана. Дараах команд нь эх кодын 7-р мөр буюу  утгуудыг хооронд нь сольж байгаа мөр дээр програмыг зогсоохоор тохируулж байна:

```sh
(gdb) b 7
Breakpoint 1 at 0x400c0d: file go-book/src/gdb_example.go, line 7.
```

Ингээд програмыг ажиллуулахад run \(r\) командыг өгнө:

```sh
(gdb) r
Starting program: go-book/src/gdb_example
Анхны утгууд: a = 10; b = 20

Breakpoint 1, main.swap (p1=0x400daa <main.main+378>, p2=0xc210000018)
    at go-book/src/gdb_example.go:7
7   tmp := p1
```

Бид `swap( )` функц дээр алдаа байгаа гэж таамаглаж байгаа учраас энэ цэгээс цааш алхам алхамаар шинжлэх болно. Үүний тулд `next буюу n`  болон `step буюу s` командуудыг ашиглана. Дараагийн заавар функцийн дуудалт эсэхээс хамааруулан `next` эсвэл `step` командыг сонгоно. `next` команд нь дараагийн мөрийг биелүүлээд програмын ажиллагааг тухайн мөр дээр зогсооно. `step` команд нь функцийн дуудалт дээр хэрэглэгдэх ба програмын ажиллагааг зогсоож уг функцийн хамгийн эхний заавар дээр биелэлтийг шилжүүлнэ. Бидний жишээнд `step` команд нь `swap( )` функцийн эхний заавар руу аваачих болно:

```sh
(gdb) s
8      p1 = p2
```

Энэ цэгт \(8-р мөр\) функцийн параметрүүдийн утга зөв дамжигдсан эсэхийг жишээлбэл шалгаж болно. Үүний тулд `print` буюу `p` командыг ашиглана:

```sh
(gdb) p *p1
$1 = 10
(gdb) p *p2
$2 = 20
```

`p1`, `p2` параметрүүд харгалзан 10, 20 утгатай байна. Хэрэв `next` командыг цааш үргэлжлүүлэн өгвөл  утга солих заавруудыг гүйцэтгэнэ:

```sh
(gdb) n
9   p2 = tmp;
(gdb)
```

Биелэлтийн удирдлага `swap( )` функцээс гарахын өмнө `p1`, `p2` хувьсагчдын утгыг `print` командаар харж болно. Энэ үед `print` командаар `p1` ба `p2` заагчдын хаягууд солигдсон,  харин тэдгээрийн зааж байгаа санах ойн байрлал дахь утгууд \(`p1` ба `p2`\) солигдоогүй болохыг харж болно. Тэгэхлээр `swap()` функц дотор заагчийн алдаа байсан байна. Функц дотор `p1` ба `p2` утгуудыг хооронд нь солихоор засах хэрэгтэй:

```sh
func swap( p1 *int, p2 *int) {
   tmp := *p1
   *p1 = *p2
   *p2 = tmp
}
```

Ингээд алдаагаа олсон учраас gdb багажийг зогсоож болно. Үүний тулд `quit` буюу `q` командыг өгөх хэрэгтэй.

```sh
(gdb) q
The program is running.  Exit anyway? (y or n) y
$
```
