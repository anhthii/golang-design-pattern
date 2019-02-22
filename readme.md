# Design pattern

### References:
- [GitHub - tmrts/go-patterns: Curated list of Go design patterns, recipes and idioms](https://github.com/tmrts/go-patterns)
- [How important are Design Patterns really? - Stack Overflow](https://stackoverflow.com/a/978509)
- [Evaluating the GO Programming Language with Design Patterns PDF](https://ecs.victoria.ac.nz/foswiki/pub/Main/TechnicalReportSeries/ECSTR11-01.pdf)
- [Tutorials · Software adventures and thoughts](http://blog.ralch.com/tutorial/)
- [Software design pattern - Wikipedia](https://en.wikipedia.org/wiki/Software_design_pattern)
- [Guru design pattern](https://refactoring.guru/design-patterns)

### Design pattern là gì:
- **Định nghĩa**: Là giải pháp tổng quát cho những vấn đề mà lập trình viên hay gặp phải. Design pattern không thể được chuyển đổi trực tiếp thành code mà nó chỉ là một khuôn mẫu cho một vấn đề cần được giải quyết

- **Ưu điểm**:
  -   **Code readability**: giúp chúng ta viết code dễ hiểu hơn bằng việc sử dụng những tên biến liên quan đến những gì chúng ta đang thực hiện
  -   **Communication**: giúp cho các lập trình viên có thể dễ dàng tiếp cận trao đổi về giái pháp cho một vấn đề bằng việc sử dụng một design pattern bất kỳ
  -  **Code reuse**:  Code được viết theo design pattern có thể được sử dụng lại nhiều lần
  
- **Nhược điểm**:
  - **More complexity**: Làm phức tạp hóa quá trình code, khiến lập trình viên vất vả hơn  khi phải suy nghĩ nên dùng mấu thiết kế nào để giải quyết vấn đề của mình khi mà anh ta chưa thực sự viết được 1 dòng code nào trong khi deadline đang cận kề
  - **Different variants**: Có nhiều biến thể cho mỗi mấu đối tượng khiến cho việc áp dụng các mẫu thiết kế đôi khi không đông nhất nên gây bất đồng khi làm việc giữa các lập trình viên
  
### Phân loại
- #### Creational pattern
    
    | Pattern |
    |:-------:|
    | [Singleton]( #Singleton) |
    | [Builder](#Builder) |
    | [Factory Method](#Factory-method) |

- #### Behavioral pattern
 	| Pattern |
  |:-------:|
  | [Observer](#Observer) |
  | [Strategy](#Strategy-pattern) |
  | [Iterator](#Iterator-pattern) |
  | [State](#State-pattern) |
    
- #### Structural pattern
    | Pattern |
    |:-------:|
    | [Adapter](#Adapter) |
    | [Bridge](#Bridge) |
    | [Composite](#Composite) |
    | [Proxy](#Proxy) |
---
  
  ### Singleton

 Chỉ duy nhất một đối tượng của một type bất kì được khởi tạo trong suốt quá trình hoạt động của một chương trình

  - **Các ứng dụng**:
    - Chúng ta muốn sử dụng lại kết nối đến database khi truy vấn
    - Khi chúng ta mở một kết nối SSH đến một server để thực hiện một số công việc và chúng ta không muốn mở một kết nối khác mà sử dụng lại kết nối trước đó
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
-  Có thể tạo ra nhiều biến thể khác nhau cho cùng một đối tượng 
- Ví dụ:
	Có 1 anh chàng nọ đang làm việc trong một team phát triển các hệ thống cho một ngân hàng và anh ta được giao nhiệm vụ cần tạo một kiểu dữ liệu BankAccout để chứa thông tin các khách đã mở tài khoản tại ngân hàng này
```go
type BankAccount struct {
	ownerName string
	ownerIdentificationNumber uint64
	balance int64
}

func NewBankAccount(ownerName string, idNo uint64, balance int64) {
	return &BankAccount{ownerName, idNo, balance}
}
```
Rất dễ dàng để mở một tài khoản
```go
var account BankAccount = NewBankAccount("Tuan", 123456789, 10000);
```
tuy nhiên đời không như mơ, Sếp lớn xỉ vả anh ta vì kiểu BankAccout thiếu chi nhánh ngân hàng và tỉ lệ lãi suất. Thế là anh ta lại hớt hải chỉnh sủa lại
```go
type BankAccount struct {
	ownerName string
	ownerIdentificationNumber uint64
	balance int64
	interestRate float64 // ti suat
	branch string // chi nhanh
}

func NewBankAccount(ownerName string, idNo uint64, balance int64, interestRate float64, branch string) {
	return &BankAccount{ownerName, idNo, balance, interestRate, branch}
}
```
Một ngừời khác sử dụng code của anh ta viết để thực hiện task của mình và anh ta đã sử dụng như sau
```go
var account BankAccount = NewBankAccount("Tuan",1000, 123456789, , 0.8, "Sai Gon");
```
anh này đã vô tình đổi số CMND(123456789), ra sau số dư(1000) và khiến ngân hàng thất thoát tiền. anh ta vô tình nhưng compiler không phát hiện ra vì cả 2 đều có kiểu dữ liệu uint64 và int64. Anh này đã đổ lỗi cho cho người đã thiết kế kiểu dữ liệu BankAccount chính là anh chàng trước đó. 

- Cách giải quyết được đặt ra:
	- Tạo các setter: Tuy nhiên người dùng đôi khi sẽ quên gọi các setter này

Anh chàng bị sếp trừ lương và quá uất ức anh sử dụng bí kíp design pattern Builder
```go
type bankAccount struct {
	ownerName            string
	identificationNumber uint64
	branch               string
	balance              int64
} 

type BankAccount interface {
	WithDraw(amt uint64)
	Deposit(amt uint64)
	GetBalance() uint64
}

type BankAccountBuilder interface {
	WithOwnerName(name string) BankAccountBuilder
	WithOwnerIdentity(identificationNumber uint64) BankAccountBuilder
	AtBranch(branch string) BankAccountBuilder
	OpeningBalance(balance uint64) BankAccountBuilder
	Build() BankAccount
}

func (acc *bankAccount) WithDraw(amt uint64) {

}

func (acc *bankAccount) Deposit(amt uint64) {

}

func (acc *bankAccount) GetBalance() uint64 {
	return 0
}

func (acc *bankAccount) WithOwnerName(name string) BankAccountBuilder {
	acc.ownerName = name
	return acc
}

func (acc *bankAccount) WithOwnerIdentity(identificationNumber uint64) BankAccountBuilder {
	acc.identificationNumber = identificationNumber
	return acc
}

func (acc *bankAccount) AtBranch(branch string) BankAccountBuilder {
	acc.branch = branch
	return acc
}

func (acc *bankAccount) OpeningBalance(balance uint64) BankAccountBuilder {
	acc.balance = int64(balance)
	return acc
}

func (acc *bankAccount) Build() BankAccount {
	return acc
}

func NewBankAccountBuilder() BankAccountBuilder {
	return &bankAccount{}
}

func main() {
	account := NewBankAccountBuilder().
		WithOwnerName("Tuan").
		WithOwnerIdentity(123456789).
		AtBranch("Sai Gon").
		OpeningBalance(1000).Build()

	account.Deposit(10000)
	account.WithDraw(50000)
}
```
Bằng việt sử dụng builder pattern, code của anh chàng tuy có hơi dai dòng hơn nhưng đã khiến code trở nên dễ đọc hơn rất nhiều

#### Reference: https://dzone.com/articles/design-patterns-the-builder-pattern

###  Factory method
- Tạo một đối tượng mà không cần thiết chỉ ra một cách chính xác lớp nào sẽ được tạo bằng cách nhóm các đối tượng liên quan đến nhau và sử dụng 1 đối tượng trung gian để khởi tạo đối tượng cần tạo
- ví dụ: chúng ta sẽ tạo các phương thức thanh toán cho 1 shop bằng factory method, hình thức thanh toán có thể bằng tiền mặt hoặc bằng thẻ debit
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
## Behavioural Patterns
### Observer
- Tạo mối liên hệ one-to-many giữa subject và các observer với nhau(chẳng hạn 1 subject sẽ có thuộc tính là một mảng bao gồm nhiều observer) nên khi trạng thái của subject thay đổi, tất cả các observer liên kết với subject này sẽ được thông báo và tự động cập nhật
- Gồm 2 thành phần chính là **Subject** và **Observer**
- sơ đồ: 

![I'm an inline-style link](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a8/Observer_w_update.svg/500px-Observer_w_update.svg.png)
- Ví dụ: Giả sử chúng ta có 1 kênh youtube và mối khi chúng ta release 1 video mới, các subscriber đều được thông báo về thông tin của video mới này. Ta coi youtube channel là 1 subject và những subscriber trong channel này là các observer. Khi các observer nhận được thông tin về video mới, app của các subscriber sẽ chịu trách nhiệm update lại UI để người dùng có thể click vào những video vừa được đăng tải

- Code:

```go
type Observer interface {
	update(interface{})
}

type Subject interface {
	registerObserver(obs Observer)
	removeObserver(obs Observer)
	notifyObservers()
}

type Video struct {
	title string
}

// YoutubeChannel is a concrete implementation of Subject interface
type YoutubeChannel struct {
	Observers []Observer
	NewVideo  *Video
}

func (yt *YoutubeChannel) registerObserver(obs Observer) {
	yt.Observers = append(yt.Observers, obs)
}

func (yt *YoutubeChannel) removeObserver(obs Observer) {
	//
}

// notify to all observers when a new video is released
func (yt *YoutubeChannel) notifyObservers() {
	for _, obs := range yt.Observers {
		obs.update(yt.NewVideo)
	}
}

func (yt *YoutubeChannel) ReleaseNewVideo(video *Video) {
	yt.NewVideo = video
	yt.notifyObservers()
}

// UserInterface is a concrete implementation of Observer interface
type UserInterface struct {
	UserName string
	Videos   []*Video
}

func (ui *UserInterface) update(video interface{}) {
	ui.Videos = append(ui.Videos, video.(*Video))
	for video := range ui.Videos {
		View.AddChildNode(NewVideoComponent(video))
	}
	fmt.Printf("UI %s - Video: '%s' has just been released\n", ui.UserName, video.(*Video).title)
}

func NewUserInterface(username string) Observer {
	return &UserInterface{UserName: username, Videos: make([]*Video, 0)}
}

// usage

func main() {
	var ytChannel Subject = &YoutubeChannel{}
	ui1 := NewUserInterface("Bob")
	ui2 := NewUserInterface("Peter")
	ytChannel.registerObserver(ui1)
	ytChannel.registerObserver(ui2)
	ytChannel.(*YoutubeChannel).ReleaseNewVideo(&Video{title: "Avatar 2 trailer"})
	ytChannel.(*YoutubeChannel).ReleaseNewVideo(&Video{title: "Avengers Endgame trailer"})
}

```
### Result
```
UI Bob - Video: 'Avatar 2 trailer' has just been released
UI Peter - Video: 'Avatar 2 trailer' has just been released
UI Bob - Video: 'Avengers Endgame trailer' has just been released
UI Peter - Video: 'Avengers Endgame trailer' has just been released

```
## Strategy pattern
- Là mẫu thiết kế cho phép chọn thuật toán trong 1 nhóm các thuật toán liên quan đến nhau ngay tại lúc chương trình đang chạy(at runtime) để thực hiện một hoạt động nào đó

- Giả sử: chúng ta cần xây dựng 1 thư viện để mã hóa một đoạn tin bằng các phương pháp asymmetric chúng ta có thể sử dụng 1 trong 2 thuật toán sau: RSA hoặc Elliptic curve
```go
package encryption

type AsymEncryptionStrategy interface {
	Encrypt(data interface{}) (byte[] cipher, error)
}

type EllipticCurvestrategy struct {} 
type RSA struct {}

func (strat *EllipticCurvestrategy) Encrypt(data interface{}) (byte[] cipher, error) {
	// some complex math
	...
	return cipher, err 
}

func (strat *RSAstrategy) Encrypt(data interface{}) (byte[] cipher, error) {
	// some complex math
	...
	return cipher, err 
} 

func encryptMessage(msg string, strat AsymEncryptionStrategy) (byte[] cipher, error) {
	return strat.Encrypt(msg)
}

// usage
msg := "this is a confidential message"
cipher, err := encryptMessage(msg, encryption.EllipticCurvestrategy)
cipher, err := encryptMessage(msg, encryption.RSAstrategy)
```
## Iterator pattern
- Mẫu này được sử dụng để truy cập vào các phần tử của 1 collection(array, map, set) một cách tuần tự mà không cần phải hiểu biết về nó.
- Iterate 1 collection sử dụng callback:
```go
func iterateEvenNumbers(max int, cb func(n int) error) error {
    if max < 0 {
        return fmt.Errorf("'max' is %d, must be >= 0", max)
    }
    for i := 2; i <= max; i += 2 {
        err := cb(i)
        if err != nil {
            return err
        }
    }
    return nil
}

func printEvenNumbers(max int) {
    err := iterateEvenNumbers(max, func(n int) error {
        fmt.Printf("%d\n", n)
        return nil
    })
    if err != nil {
        log.Fatalf("error: %s\n", err)
    }
}

printEvenNumbers(10)
```
> Pattern này được sử dụng trong go standard library: [filepath.Walk](https://golang.org/pkg/path/filepath/#Walk)
- Iterate với `Next()` 
```go
// EvenNumberIterator generates even numbers
type EvenNumberIterator struct {
    max       int
    currValue int
    err       error
}

// NewEvenNumberIterator creates new number iterator
func NewEvenNumberIterator(max int) *EvenNumberIterator {
    var err error
    if max < 0 {
        err = fmt.Errorf("'max' is %d, should be >= 0", max)
    }
    return &EvenNumberIterator{
        max:       max,
        currValue: 0,
        err:       err,
    }
}

// Next advances to next even number. Returns false on end of iteration.
func (i *EvenNumberIterator) Next() bool {
    if i.err != nil {
        return false
    }
    i.currValue += 2
    return i.currValue <= i.max
}

// Value returns current even number
func (i *EvenNumberIterator) Value() int {
    if i.err != nil || i.currValue > i.max {
        panic("Value is not valid after iterator finished")
    }
    return i.currValue
}

// Err returns iteration error.
func (i *EvenNumberIterator) Err() error {
    return i.err
}

func printEvenNumbers(max int) {
	iter := NewEvenNumberIterator(max)
	for iter.Next() {
		fmt.Printf("n: %d\n", iter.Value())
	}
	if iter.Err() != nil {
		log.Fatalf("error: %s\n", iter.Err())
	}
}

func main() {
	fmt.Printf("Even numbers up to 8:\n")
	printEvenNumbers(8)
	fmt.Printf("Even numbers up to 9:\n")
	printEvenNumbers(9)
	fmt.Printf("Error: even numbers up to -1:\n")
	printEvenNumbers(-1)
}
```
- Pattern này được sử dụng nhiều trong go standard library
	- [Rows.Next](https://golang.org/pkg/database/sql/#Rows.Next):  iterate các kết quả thu được từ SQL SELECT statement
	- [Scanner.Scan](https://golang.org/pkg/database/sql/#Rows.Next): iterate text
	- [Decoder.Token](https://golang.org/pkg/encoding/xml/#Decoder.Token): XML parsing
	- [Reader.Read](https://golang.org/pkg/encoding/csv/#Reader.Read): CSV reader
## References: https://blog.kowalczyk.info/article/1Bkr/3-ways-to-iterate-in-go.html

## State pattern
- Mỗi đối tượng có 1 trạng thái gắn với nó và trạng thái có thể được thay đổi thông qua `SetState` method
- Ví dụ: Giả sử điện thoại có 2 trạng thái nhắc nhở: Im lặng hoặc Rung
- code
```go
type MobileAlertState interface {
	alert()
}

type AlertStateContext struct {
	currentState MobileAlertState
}

func NewAlertStateContext() *AlertStateContext {
	return &AlertStateContext{currentState: &Vibration{}}
}

func (ctx *AlertStateContext) SetState(state MobileAlertState) {
	ctx.currentState = state
}

func (ctx *AlertStateContext) Alert() {
	ctx.currentState.alert()
}

type Vibration struct{}

func (v *Vibration) alert() {
	fmt.Println("vibrating....")
}

type Silence struct{}

func (s *Silence) alert() {
	fmt.Println("silent ....")
}

func main() {
	stateContext := NewAlertStateContext()
	stateContext.Alert()
	stateContext.Alert()
	stateContext.Alert()
	stateContext.SetState(&Silence{})
	stateContext.Alert()
	stateContext.Alert()
	stateContext.Alert()
}

// result
vibrating....
vibrating....
vibrating....
silent ....
silent ....
silent ....
```
### Reference: https://www.geeksforgeeks.org/state-design-pattern/
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



	

