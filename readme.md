# Design pattern

### Design pattern là gì:
- **Định nghĩa**: Là  giải pháp tổng thể cho những vấn đề phổ biến mà chúng ta hay găp đi lặp lại trong thiết kế phần mềm. Design pattern không thể được chuyển đổi trực tiếp thành code mà nó chỉ là một khuôn mẫu cho một vấn đề cần được giải quyết

- **Ưu điểm**:
  -   **Code readability**: giúp chúng ta viết code dễ hiểu hơn bằng việc sử dụng những tên biến liên quan đến những gì chúng ta đang thực hiện
  -   **Communication**: giúp cho các lập trình viên có thể dễ dàng tiếp cận trao đổi về giái pháp cho một vấn đề bằng việc sử dụng một design pattern bất kỳ
  -  **Code reuse**:  Code được viết theo design pattern có thể được sử dụng lại nhiều lần
  
- **Nhược điểm**:
  - **More complexity**: Làm phức tạp hóa quá trình code, khiến lập trình viên vất vả hơn  khi phải suy nghĩ nên dùng mấu thiết kế nào để giải quyết vấn đề của mình khi mà anh ta chưa thực sự viết được 1 dòng code nào trong khi deadline đang cận kề
  - **Different variants**: Có nhiều biến thể cho mỗi mấu đối tượng khiến cho việc áp dụng các mẫu thiết kế đôi khi không đông nhất nên gây bất đồng khi làm việc giữa các lập trình viên
  
### Phân loại
- #### Creational pattern
    
    | Pattern | Mô tả | Status |
    |:-------:|:----------- |:------:|
    | [Singleton]( #Singleton) | Chỉ duy nhất một đối tượng của một type bất kì được khởi tạo trong suốt quá trình hoạt động của một chương trình | ✔ |
    | [Builder](#Builder) | Tách biệt phần construction của một đối tượng phức tạp khỏi phần representation của nó để các tiến trình construction giống nhau có thể tạo ra các representation khác nhau | ✔ |
    | [Factory Method](#Factory-method) | Sứ dụng trung gian là ``factory`` để khởi tạo các đối tượng có liên quan đến nhau| ✔ |
    | [Object Pool]() | Khởi tạo và quản lý một nhóm các đối tượng cùng kiểu dữ liệu | ✘ |
    | [Prototype]() | Sử dụng clone cho các đối tượng giống nhau để tránh việc khởi tạo lại đối tượng | ✘ |
    
    
- #### Structural pattern
    | Pattern | Mô tả | Status |
    |:-------:|:----------- |:------:|
    | [Adapter](#Adapter) | Cho phép các interface không liên quan đến nhau có thể làm việc cùng nhau  | ✔ |
    | [Bridge]() |  | ✘ |
    | [Composite]() | | ✘ |
    | [Decorator]() |  | ✘ |
    | [Facade]() |  | ✘ |
    | [Flyweight]() |  | ✘ |
     | [Proxy]() |  | ✘ |
- #### Behavioral pattern
---
  
  ### Singleton
  - **Các ứng dụng**:
    - Chúng ta muốn sử dụng lại kết nối đến database khi truy vấn
    - Khi chúng ta mở một kết nối SSH đến một server để thực hiện một số công việc và chúng ta không muốn mở một kết nối mà sử dụng lại kết nối trước đó
    - Khi chúng ta cần hạn chế sự truy cập đến một vài biến số, chúng ta sử dụng mẫu singleton là trung gian để truy cập biến này
    
```go
package singleton

type singleton struct {}

var (
  once sync.Once
  instance *singleton
)

func GetInstance() *singleton  {
  once.Do(func() {
    instance  = make(singleton)
  })
  return singleton
}
```

- **Lưu ý**:  Là mấu thiết kế phổ biến nhất nhưng đôi khi bị lạm dụng. Nên tránh việc sử dụng mẫu singleton vì khiến cho việc test cụ thể là việc tạo mock/stub trở nên khó khăn hơn

### Builder
-  Nhiệm vụ của nó là không chỉ khởi tạo đối tượng mà còn thiết lập giá trị trước rồi truyền vào đối tượng. Hay nói cách khác, nó tách biệt phần construction của một đối tượng phức tạp khỏi phần representation (thể hiện) của nó để các tiến trình construction giống nhau có thể tạo ra các thể hiện khác nhau.

* Sơ đồ tạo mẫu thiết kế builder
![332b7bd4.png](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/uml-of-builedr.jpg)
- Các thành phần:
    - **Builder**: định nghĩa một lớp trừu tượng (abstract class) để tạo ra một hoặc nhiều phần của đối tượng
    - **ConcreateBuilder**: Đây là thành phần triển khai, cụ thể hóa các lớp trừu tượng cho để tạo ra các thành phần và tập hợp các thành phần đó với nhau. Nó sẽ xác định và nắm giữ các thể hiện mà nó tạo ra. Đồng thời nó cũng cung cấp phương thức để trả các các thể hiện mà nó đã tạo ra trước đó.

     - **Product** :Đại diện cho đối tượng phức tạp phải tạo ra.

     - **Director**: Khởi tạo đối tượng phức tạp sử dụng Builder Interface

- Ví dụ: Bạn cần encode một tập dữ liệu nào đó dưới dạng XML hoặc JSON để có thể truyền đến các máy khác thông qua internet

###
```go
// Message is Product in builder design pattern
type Mesage struct {
  Body []byte
  Format string
}

// MessageBuilder is the interface that every concrete implementation should obey
type MessageBuilder interface  {
	SetRecipient(recipient string)
	SetText(text string)
	Message() (*Message, error)
}

type OriginalMessage struct {
  messageRecipient string
	messageText      string
}
// JSON Message Builder is concrete builder
type JSONMessageBuilder struct {
  OriginalMessage
}

type XMLMessageBuilder struct {
  OriginalMessage
}


func (this *OriginalMessage) SetRecipient(recipient string) {
	this.messageRecipient = recipient
}

func (this *OriginalMessage) SetText(text string) {
  this.messageText = text
}

func (b *JSONMessageBuilder) Message() (*Message, error) {
	m := make(map[string]string)
	m["recipient"] = b.messageRecipient
	m["message"] = b.messageText

	data, err := json.Marshal(m)
	if err != nil {
		return nil, err
	}

	return &Message{Body: data, Format: "JSON"}, nil
}

func NewJSONMsgBuilder() *JSONMessageBuilder {
	return &JSONMessageBuilder{&OriginalMessage{}}
}

func (b *XMLMessageBuilder) Message() (*Message, error) {
	type XMLMessage struct {
		Recipient string `xml:"recipient"`
		Text      string `xml:"body"`
	}

	m := XMLMessage{
		Recipient: b.messageRecipient,
		Text:      b.messageText,
	}

	data, err := xml.Marshal(m)
	if err != nil {
		return nil, err
	}

	return &Message{Body: data, Format: "XML"}, nil
}

func NewXMLMsgBuilder() *XMLMessageBuilder {
	return &XMLMessageBuilder{&OriginalMessage{}}
}

// Sender is the Director in Builder Design Pattern
type EmailSender struct{}

// Build a concrete message via MessageBuilder
func (s *Sender) BuildWelcomeEmailMsg(builder MessageBuilder, email string) (*Message, error) {
  builder.SetText(fmt.Sprintf("welcome %s ", email))
  builder.SetRecipient(email)
  return  builder.Message()
}

// usage
sender := &EmailSender{}
recipient := "johndoe@gmail.com"

jsonMsg, _ := sender.BuildWelcomeMsg(NewJSONMsgBuilder(), recipient)
xmlMsg, _ := sender.BuildWelcomeMsg(NewXMLMsgBuilder(), recipient)


```
-  Ưu điểm:
    - Ẩn các logic phức tạp trong quá trình xây dựng đối tượng đối với người dùng
    - Người dùng có thể tùy ý thêm mới 1 builder mới mà không cần phải viết lại quá nhiều code
- Nhược điểm:
  - Phải viết builder cho từng class
  - Sự thay đổi nhỏ trong Interface sẽ ảnh hưởng đến toàn bộ các builder

#### Reference: [Desing Patterns in Golang: Builder · Software adventures and thoughts](http://blog.ralch.com/tutorial/design-patterns/golang-builder/)

###  Factory method
- Tạo một đối tượng mà không cần thiết chỉ ra một cách chính xác lớp nào sẽ được tạo bằng cách nhóm các đối tượng liên quan đến nhau và sử dụng 1 đối tượng trung gian để khởi tạo đối tượng cần tạo
- ví dụ: chúng ta sẽ tạo các phương thức thanh toán cho 1 shop bằng factory method
- 
```go
type PaymentMethod interface {
  Pay(amount float32) string
}

type PaymentType int

const (
  Cash PaymentType = iota
  DebitCard 
)

type CashPM struct {}
type DebitCardPM struct{}

func (c *CashPM) Pay(amount float32) string {
  return ""
}

func (c *DebitCardPM) Pay(amount float32) string {
  return ""
}

func GetPaymentMethod(t  PaymentType) PaymentMethod {
  siwtch t {
  case Cash:
    return new(CashPM)
  case DebitCard:
    return new(DebitCardPM)
  }
}

// usage
payment := GetPaymentMethod(DebitCard)
payment.Pay(20)
payment := GetPaymentMethod(Cash)
payment.Pay(50)

```
---
## Structural Patterns
### Adapter
- Cho phép các interface không liên quan đến nhau có thể làm việc cùng nhau 

- Liên tưởng: Giả sử bạn cần cắm sạc điện thoại vào một ổ cắm điện không tương thích khi đó bạn cần phải dùng một bộ chuyển đổi(adapter)
- Ví dụ: Mèo và cá sấu

```go
package animal

type Animal interface {
  Move()
}

// Cat is a concrete animal since it implements the method Move
type Cat struct {}

func (c *Cat) Move() {}

// and somewhere in the code we need to use the crocodile type which is often not our code and this Crocodile type does not implement the Animal interface
// but we need to use a crocodile as an animal

type Crocodile struct {}

func (c *this.Crocodile) Slither() {}

// we create an CrocodileAdapter struct that dapts an embeded crocodile so that it can be usedd as an Animal

type CrocodileAdapter struct {
  *Crocodile
}

func NeweCrocodile() *CrocodileAdapter {
  return &CrocodileAdapter{new(Crocodile)}
}

func (this *CrocodileAdapter) Move() {
  this.Slither()
}

// usage
import "animal"

var animals []animal.Animal
animals = append(animals, new(animals.Cat))
animals = append(animals, new(animals.NewCrocodile()))

for  entity := range animals {
  entity.Move()
} 

```
Reference: [Design Patterns Explained: Adapter Pattern With Code Examples - DZone Java](https://dzone.com/articles/design-patterns-explained-adapter-pattern-with-cod)

---
## Other references:
- [GitHub - tmrts/go-patterns: Curated list of Go design patterns, recipes and idioms](https://github.com/tmrts/go-patterns)
- [How important are Design Patterns really? - Stack Overflow](https://stackoverflow.com/a/978509)
- [Evaluating the GO Programming Language with Design Patterns] PDF (https://ecs.victoria.ac.nz/foswiki/pub/Main/TechnicalReportSeries/ECSTR11-01.pdf)
- [Tutorials · Software adventures and thoughts](http://blog.ralch.com/tutorial/)
- [Software design pattern - Wikipedia](https://en.wikipedia.org/wiki/Software_design_pattern)

