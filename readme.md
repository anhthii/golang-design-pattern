# Design pattern

### References:
- [GitHub - tmrts/go-patterns: Curated list of Go design patterns, recipes and idioms](https://github.com/tmrts/go-patterns)
- [How important are Design Patterns really? - Stack Overflow](https://stackoverflow.com/a/978509)
- [Evaluating the GO Programming Language with Design Patterns PDF](https://ecs.victoria.ac.nz/foswiki/pub/Main/TechnicalReportSeries/ECSTR11-01.pdf)
- [Tutorials · Software adventures and thoughts](http://blog.ralch.com/tutorial/)
- [Software design pattern - Wikipedia](https://en.wikipedia.org/wiki/Software_design_pattern)
- https://www.oodesign.com/proxy-pattern.html

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
    | [Bridge](#Bridge) | Tách 1 class lớp thành 2 phần interface và reprensentation để 2 phần này không bị phụ thuộc vào nhau và có thể được phát triển song song | ✔ |
    | [Composite](#Composite) |cho phép tương tác với tất cả các đối tượng tương tự nhau giống như là 1 đối tượng đơn hoặc 1 nhóm các đối tượng | ✔ |
     | [Proxy](#Proxy) | Cung cấp một đại diện cho một đối tượng để điều khiển việc truy nhập đối tượng đó | ✔ |
- #### Behavioral pattern
---
  
  ### Singleton
  - **Các ứng dụng**:
    - Chúng ta muốn sử dụng lại kết nối đến database khi truy vấn
    - Khi chúng ta mở một kết nối SSH đến một server để thực hiện một số công việc và chúng ta không muốn mở một kết nối mà sử dụng lại kết nối trước đó
    - Khi chúng ta cần hạn chế sự truy cập đến một vài biến số, chúng ta sử dụng mẫu singleton là trung gian để truy cập biến này
    
```go
package DB

var (
  once sync.Once
  singleton *db.Connection
)

func GetDBConnection(config *mysql.Config) *db.Connection {
  once.Do(func() {
    singleton  = mysql.Dial(config)
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
### Composite
- cho phép tương tác với tất cả các đối tượng tương tự nhau giống như là 1 đối tượng đơn hoặc 1 nhóm các đối tượng

- Hình dung: Đối tượng File sẽ là 1 đối tượng đơn nếu bên trong nó không có file nào khác, nhưng đối tượng file sẽ được đối xử giống như 1 collection nếu bên trong nó lại có những File khác.

- Sơ đồ:
![332b7bd4.png](https://upload.wikimedia.org/wikipedia/commons/thumb/5/5a/Composite_UML_class_diagram_%28fixed%29.svg/600px-Composite_UML_class_diagram_%28fixed%29.svg.png)
- Cấu trúc:
  - **Component** (Thành phần):
    - Khai báo interface hoặc abstract chung cho các thành phần đối tượng.
    - Chứa các method thao tác chung của các thành phần đối tượng.
  - **Leaf** (Lá):
    - Biểu diễn các đối tượng lá (ko có con) trong thành phần đối tượng.
  - **Composite** (Hỗn hợp):
    - Định nghĩa một thao tác cho các thành phần có thành phần con.
    - Lưu trữ và quản lý các thành phần con

- Ví dụ khác: Khi vẽ, chúng ta được cung cấp các đối tượng Square, Circle, các đối tượng này đều là Shape. Giả sử chúng ta muốn vẽ nhiều loại hình cùng 1 lúc chúng ta sẽ tạo một Layer chứa các Shape này và thực hiện vòng lặp để vẽ chúng. Ở đây composite được hiểu là chúng ta có thể sử dụng các đối tượng Square, Circle riêng biệt nhưng khi cần chúng ta có thể gom chúng lại thành 1 nhóm
```go
// Shape is the component
type Shape interface {
	Draw(drawer *Drawer) error
}

// Square and Circle are leaves
type Square struct {
	Location Point
	Size float64
}

func (square *Square) Draw(drawer *Drawer) error {
	return drawer.DrawRect(Rect{
		Location: square.Location,
		Size: Size{
			Height: square.Side,
			Width:  square.Side,
		},
	})
}

type Circle struct {
	Center Point
	Radius float64
}

func (circle *Circle) Draw(drawer *Drawer) error {
	rect := Rect{
		Location: Point{
			X: circle.Center.X - circle.Radius,
			Y: circle.Center.Y - circle.Radius,
		},
		Size: Size{
			Width:  2 * circle.Radius,
			Height: 2 * circle.Radius,
		},
	}

	return drawer.DrawEllipseInRect(rect)
}

// Layer is the composite
type Layer struct {
	Shapes []Shape
}

func (layer *Layer) Draw(drawer *Drawer) error {
	for _, shape := range layer.Shapes {
		if err := shape.Draw(drawer); err != nil {
			return err
		}
		fmt.Println()
	}

	return nil
}

// usage
circle := &photoshop.Circle{
	Center: photoshop.Point{X: 100, Y: 100},
	Radius: 50,
}

square := &photoshop.Square{
	Location: photoshop.Point{X: 50, Y: 50},
	Side:     20,
}

layer := &photoshop.Layer{
	Elements: []photoshop.Shapes{
		circle,
		square,
	},
}

circle.Draw(&photoshop.Drawer{})
square.Draw(&photoshop.Drawer{})
// or
layer.Draw(&photoshop.Drawer{})
```
### Bridge
- Tách 1 class lớp thành 2 phần interface và reprensentation để 2 phần này không bị phụ thuộc vào nhau và có thể được phát triển song song
- **Vấn đề**: Giả sử chúng ta cần xây dựng 1 package UI hỗ trợ vẽ nhiều hình dạng trên màn hình bằng cả 2 công nghệ rendering direct2d và opengl. Trong trường hợp này là vẽ hình tròn dùng cả 2 công nghệ. Ta tách struct Circle khỏi phần drawing
```go
type Circle struct {
  DrawingContext drawer
  Center Point
  Radius float64
}

func (circle *Circle) Draw() error {
  rect := Rect{
    Location: Point{
      X: circle.Center.X - circle.Radius,
      Y: circle.Center.Y - circle.Radius,
    },
    Size: Size{
      Width: 2 * circle.Radius,
      Height: 2 * circle.Radius,
    }
  }
  return circle.DrawingContext.DrawEllipseInRect(rect)
}

type Drawer interface {
  DrawEllipseInRect(Rect) error
}

// OpenGL drawer
type OpenGL struct{}
// DrawEllipseInRect draws an ellipse in rectangle
func (gl *OpenGL) DrawEllipseInRect(r Rect) error {
	fmt.Printf("OpenGL is drawing ellipse in rect %v", r)
	return nil
}

// Direct2D drawer
type Direct2D struct{}

// DrawEllipseInRect draws an ellipse in rectangle
func (d2d *Direct2D) DrawEllipseInRect(r Rect) error {
	fmt.Printf("Direct2D is drawing ellipse in rect %v", r)
	return nil
}

// usage
openGL := &uikit.OpenGL{}
direct2D := &uikit.Direct2D{}

circle := &uikit.Circle{
	Center: uikit.Point{X: 100, Y: 100},
	Radius: 50,
}

circle.DrawingContext = openGL
circle.Draw()

circle.DrawingContext = direct2D
circle.Draw()
```

### Proxy
- Mục đích
  - Kiểm soát quyền truy suất các phương thức của đối tượng
  - Bổ xung thêm chức năng trước khi thực thi phương thức gốc
  - Tạo ra đối tượng mới có chức năng cao hơn đối tượng ban đầu 
  - Giảm chi phí  khi có nhiều truy cập vào đối tượng có chi phí khởi tạo ban đầu lớn

```go
type Image interface {
	display()
}

type HighResolutionImage struct {
	imageFilePath string
}

type ImageProxy struct {
	imageFilePath string
	realImage     Image
}

func (this *HighResolutionImage) loadImage(path string) {
	// load image from disk into memory
	// this is a heavy and costly operation
	fmt.Printf("load image %s from disk\n", path)
}

func (this *HighResolutionImage) display() {
	this.loadImage(this.imageFilePath)
	fmt.Printf("display high resolution image\n")
}

func (this *ImageProxy) display() {
	this.realImage = &HighResolutionImage{imageFilePath: this.imageFilePath}
	this.realImage.display()
}

func NewImageProxy(path string) Image {
	return &ImageProxy{imageFilePath: path}
}

// usage

highResolutionImage := NewImageProxy("sample/img1.png")
// the realImage won't be loaded until user calls display
// later
highResolutionImage.display()

```
#### Reference: https://www.oodesign.com/proxy-pattern.html

