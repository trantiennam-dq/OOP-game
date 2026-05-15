# Cấu trúc dự án và Phân tích thiết kế Candy Crush

## 1. Chức năng của các thư mục (Packages)
- `src/main`: Thư mục gốc chứa các lớp khởi chạy ứng dụng và quản lý luồng trò chơi cốt lõi.
- `src/main/animation`: Chịu trách nhiệm quản lý hiệu ứng chuyển động hình ảnh của game, giúp kẹo di chuyển mượt mà (rơi xuống, hoán đổi, phát nổ) thay vì xuất hiện/biến mất ngay lập tức.
- `src/main/logic`: Chứa các bộ quy tắc và thuật toán cốt lõi của game (như cách xét điều kiện ăn điểm, trọng lực rơi kẹo, xử lý kẹo đặc biệt). Việc tách riêng package này giúp logic game hoàn toàn độc lập với giao diện.
- `src/main/model`: Chứa các cấu trúc dữ liệu, định nghĩa thực thể (kẹo, bảng lưới) và trạng thái hiện tại của game. Lớp này không quan tâm đến đồ họa mà chỉ lưu trữ trạng thái.
- `src/main/model/enums`: Nơi lưu trữ các hằng số liệt kê (enum) nhằm chuẩn hóa dữ liệu đầu vào (ví dụ phân loại kẹo và loại kết hợp), giúp code dễ đọc và tránh lỗi typing.
- `src/main/ui`: Quản lý giao diện người dùng (hiển thị hình ảnh đồ hoạ) và nhận các tương tác từ người chơi (nhấn chuột).

## 2. Các file trong từng thư mục và chức năng

### Thư mục `src/main`
- `Main.java`: Điểm bắt đầu của ứng dụng (Entry point). Khởi tạo cửa sổ màn hình (JFrame), khởi tạo các thành phần cốt lõi và bắt đầu chạy trò chơi.
- `GameManager.java`: "Bộ não" trung tâm của game. Quản lý trạng thái vòng lặp game (nhàn rỗi, đang đổi chỗ, đang xét điểm, đang rơi), điều phối tương tác giữa Logic, Model, UI và Animation.

### Thư mục `src/main/animation`
- `AnimationSystem.java`: Hệ thống quản lý danh sách các hoạt ảnh đang diễn ra và tự động cập nhật tọa độ hình ảnh của kẹo sau mỗi khung hình.
- `Animation.java`: Lớp cơ sở (có thể là abstract class/interface) đại diện cho một hoạt ảnh bất kỳ.
- `SwapAnimation.java`: Hoạt ảnh hoán đổi vị trí của 2 viên kẹo khi người chơi thực hiện thao tác.
- `FallAnimation.java`: Hoạt ảnh điều khiển kẹo rớt từ trên xuống để lấp vào khoảng trống sau khi kẹo bị ăn.
- `DestroyAnimation.java`: Hoạt ảnh hiệu ứng khi kẹo phát nổ/bị phá hủy.

### Thư mục `src/main/logic`
- Các interfaces (`IMatchLogic.java`, `IGravityLogic.java`, `ISpecialCandyLogic.java`): Định nghĩa bộ khung chức năng cho thuật toán xét điểm, rơi kẹo và sử dụng kẹo đặc biệt, giúp dễ dàng mở rộng.
- `BasicMatchLogic.java`: Thuật toán duyệt lưới tìm các dãy kẹo giống màu nhau (từ 3, 4 hoặc 5 viên), và quyết định kẹo đặc biệt nào được sinh ra ở vị trí đó.
- `BasicGravityLogic.java`: Thuật toán xử lý việc dồn vị trí kẹo xuống các ô trống dưới cùng và sinh ngẫu nhiên các loại kẹo mới ở hàng trên cùng.
- `BasicSpecialCandyLogic.java`: Logic xử lý kích hoạt sức mạnh của kẹo đặc biệt (như nổ ngang/dọc, nổ bom diện rộng) khi chúng tham gia vào một chuỗi kết hợp.

### Thư mục `src/main/model` & `enums`
- `Board.java`: Đại diện cho lưới trò chơi 2 chiều, cung cấp hàm tiện ích như hoán đổi (`swap`), đọc/ghi kẹo tại một ô nhất định.
- `Candy.java`: Lớp mô tả thực thể viên kẹo, chứa toạ độ logic trong lưới (hàng, cột), toạ độ hình ảnh để render (pixel), màu sắc và loại đặc biệt (nếu có).
- `MatchResult.java`: Đối tượng lưu trữ thông tin về kết quả của một lần nối kẹo (danh sách kẹo bị ăn, loại kẹo đặc biệt mới cần sinh ra).
- Các Enums (`CandyType.java`, `MatchType.java`, `SpecialType.java`): Định nghĩa hằng số các màu kẹo, hình dạng của cú ăn điểm (3 ngang, 4 dọc...) và loại sức mạnh kẹo.

### Thư mục `src/main/ui`
- `GamePanel.java`: Nơi thực hiện code vẽ đồ họa (Swing JPanel). Tô màu bảng game, vẽ các viên kẹo lên màn hình theo tọa độ ảnh, vẽ điểm số và hiệu ứng chọn kẹo.
- `InputHandler.java`: Tiếp nhận sự kiện click chuột, tính toán chuyển đổi tọa độ trên màn hình pixel thành toạ độ lưới logic (hàng, cột) để báo cho `GameManager`.

---

## 3. Đánh giá tính tuân thủ nguyên tắc SOLID

Nhìn chung, dự án hiện tại đã áp dụng cấu trúc hướng đối tượng rất tốt (tách riêng logic, dữ liệu, hiển thị và hình ảnh động). Tuy nhiên, đối chiếu với bộ nguyên tắc thiết kế SOLID, hệ thống **chưa đáp ứng được toàn bộ** và có một số vi phạm. Dưới đây là phân tích và hướng khắc phục:

### 1. Vi phạm Single Responsibility Principle (SRP - Nguyên lý Đơn trách nhiệm)
> [!WARNING]
> Có 2 nơi vi phạm rõ ràng nhất về sự ôm đồm trách nhiệm: `GameManager` và `GamePanel`.

- **Vấn đề ở `GameManager`:** Lớp này hiện đang quản lý quá nhiều tác vụ: tính toán điểm, điều khiển vòng lặp game, máy trạng thái (GameState), gọi `repaint()` trên UI, và thậm chí *tự sinh ngẫu nhiên kẹo mới trong hàm `refillAndAnimate`*. Đáng lý việc sinh kẹo ngẫu nhiên phải là nhiệm vụ của Gravity Logic.
- **Khắc phục:** 
  - Đưa toàn bộ code khởi tạo kẹo mới về `BasicGravityLogic`.
  - Tách cỗ máy trạng thái (Switch-case State) ra một class như `GameController` hoặc `StateController`.
  - Xóa bỏ việc `GameManager` phải nhận trực tiếp tham chiếu của `GamePanel` để gọi `repaint`. Hãy sử dụng **Observer Pattern** (GameManager phát sự kiện StateChanged, UI tự lắng nghe và vẽ lại).

- **Vấn đề ở `GamePanel`:** Hàm `drawCandy()` chứa một câu lệnh `switch-case` khổng lồ kiểm tra `candy.getType()` để quyết định tô màu sắc gì. Nếu thêm kẹo mới, bắt buộc phải chỉnh sửa lại mã UI này.
- **Khắc phục:** Thay vì UI chứa logic về màu sắc cứng, hãy tạo một `CandyRendererFactory` giúp ánh xạ từng `CandyType` ra cách vẽ (hay asset hình ảnh) tương ứng, hoặc bản thân enum `CandyType` có thể giữ thuộc tính màu hiển thị của nó.

### 2. Vi phạm Open/Closed Principle (OCP - Nguyên lý Đóng/Mở)
> [!CAUTION]
> Logic tìm kết nối kẹo (`BasicMatchLogic`) chưa sẵn sàng để mở rộng với các kiểu chơi mới mà không cần chỉnh sửa trực tiếp.

- **Vấn đề:** Lớp `BasicMatchLogic` sử dụng cấu trúc `if-else` cứng để xét nếu là kích cỡ = 4 thì tạo kẹo sọc ngang/dọc, nếu >= 5 thì tạo kẹo gói. Nếu trong tương lai muốn thêm kiểu match dạng chữ T hoặc chữ L sinh ra kẹo đặc biệt khác, bạn phải vào hàm `findMatches` và sửa đổi/thêm điều kiện `if`.
- **Khắc phục:** Áp dụng **Pattern Chain of Responsibility** hoặc **Strategy**. Bạn tạo một Interface `IMatchPattern` và các lớp con: `LineOfFourPattern`, `LShapePattern`, `TShapedPattern`. Hàm `findMatches` chỉ cần vòng lặp duyệt qua danh sách các Pattern này, hệ thống sẽ dễ dàng mở rộng chức năng mà không phải sửa lại file cốt lõi.

### 3. Vi phạm Dependency Inversion Principle (DIP - Nguyên lý Đảo ngược phụ thuộc)
> [!IMPORTANT]
> Code đang vi phạm vì Module cấp cao đang tự đi tạo instance (new) cho module cấp thấp.

- **Vấn đề:** `GameManager` (module cấp cao) được thiết kế khéo léo khi tham chiếu đến các Interface như `IMatchLogic`, `IGravityLogic`. Nhưng sai lầm nằm ở chỗ trong Constructor của nó lại trực tiếp khởi tạo class cụ thể (Concretion):
  ```java
  this.matchLogic = new BasicMatchLogic(); // Vi phạm DIP
  this.gravityLogic = new BasicGravityLogic(); // Vi phạm DIP
  ```
  Điều này khiến `GameManager` bị kết dính chặt chẽ vào các thuật toán cụ thể này.
- **Khắc phục:** Áp dụng **Dependency Injection (Tiêm phụ thuộc)**. Truyền thẳng các Logic Interfaces này qua tham số của hàm khởi tạo (Constructor). Việc quyết định dùng Logic loại nào sẽ được đẩy ra ngoài lớp `Main.java` tự tạo và inject vào.

### 4. Liskov Substitution Principle (LSP) và Interface Segregation Principle (ISP)
> [!TIP]
> Dự án tuân thủ hai nguyên lý này rất tốt!

- **ISP:** Các Interfaces được chia nhỏ (Segregated) vô cùng hợp lý, không bắt ép các class cài đặt những logic không liên quan. (VD: Kẹo đặc biệt tách riêng với Trọng lực).
- **LSP:** Code dựa trên Interface hoàn toàn có thể thay thế (Substitute) bằng bất kỳ implementation nào khác (chẳng hạn thay `BasicGravityLogic` bằng `AdvancedGravityLogic`) mà luồng ứng dụng không hề bị phá vỡ.
