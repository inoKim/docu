## Interface

* 타 OOP의 interface와 다른점 
> interface에 구현된 내용을 모두 구현하지 않아도 사용이 가능하다.(=묵시적으로 사용 가능하다)

#### Goal!!!
1. 인터페이스 기본 메커니즘
2. 인터페이스를 통한 일반화 방법

#### What is the "interface"? 
* 구상 타입 vs 추상 타입

|      | 추상 타입                                        | 구상 타입                                          |   
|------|--------------------------------------------------|----------------------------------------------------|---|---|
| 예)  | interface                                        | var, slice, struct...                                      |   
| 구분 | 기본 연산을 드러내지 않고 큰 그림을 가지고 있다. | 해당 값이 무엇인지, 무엇을 할 수 있는지 알수 있다. |   


#### golang 내부에서 사용하는 interface는? 
```go
package fmt

func Fprintf(w io.Writer, format string, args ... interface{} ) (int, error)

func Printf(format string, args ... interface{}) (int, error) {
   return Fprintf(os.Stdout, format, args ..)
}

func Sprintf(format string, args ...interface{}) string {
    var buf bytes.Buffer
    Fprintf(&buf, format, args...)
    return buf.String()
}
```

* 여기에서 Fprintf()에 첫 번째 인자도 io.Writer처럼 interface형이다. 
**Buffer와 os.Stdout 둘다 io.Writer를 interfacing하고 있다.**
* 이와 같이 interface를 통해 일반화를 해서 효율적인 코드를 작성 할 수 있다.
> 이 처럼 인터페이스를 상속(?) 하는 타입을 자유롭게 변경하는것이 대체 가능성(substitutability)라고 한다. 


```go 
package main
import (
	"fmt"
)
type ByteCounter int 
func (c *ByteCounter) Write(p []byte) (int, error) {
	*c += ByteCounter(len(p))
	return len(p), nil
}
func main() {
	var c ByteCounter 
	c.Write([]byte("hello"))
	fmt.Println(c)
	c = 0
	var name ="Dolly"
	fmt.Fprintf(&c, "hello, %s" , name)
	fmt.Println(c)
}

```
> \>5
> \>12

* 이해가 안가서 string도 똑같이 실행되는지 보려고 해봤지만 에러가 발생.

```go
    var str string
    fmt.Fprintf(&str, "hello, %s" , name)
```
> \>Error. 

**?????**

![](./img/type_writer)
