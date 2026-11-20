# Chương 7: Script, Vòng Đời, Autoload & Chuyển Cảnh

**Ý chính:** script gắn vào một node để cho nó **hành vi**. Godot gọi các hàm đặc biệt của script vào đúng thời điểm (vòng đời). Với thứ cần sống xuyên suốt game, dùng Autoload.

### 7.1 Mô hình tư duy

Script là **bộ não gắn vào mảnh Lego**. Một node có tối đa **một** script; script **mở rộng kiểu node** nó gắn vào (script gắn vào `CharacterBody2D` thừa hưởng mọi thuộc tính và hàm của `CharacterBody2D`).

### 7.2 Vòng đời của một node

```
 Node vào cây ──► _EnterTree()
   │ (con vào cây xong)
   ▼
 _Ready()                 ← chạy MỘT lần, node và mọi con đã sẵn sàng
   │
   ▼   mỗi frame:           _Process(delta)         (logic hiển thị, UI)
       mỗi bước vật lý:     _PhysicsProcess(delta)  (di chuyển, va chạm)
   │
   ▼
 Node rời cây ──► _ExitTree()   ← dọn dẹp, ngắt kết nối
```

- **`_Ready`:** nơi lấy tham chiếu node con và thiết lập ban đầu. Chạy **con trước, cha sau**.
- **`_Process(delta)`:** mỗi frame vẽ; `delta` là giây giữa hai frame, thay đổi theo FPS.
- **`_PhysicsProcess(delta)`:** nhịp **cố định** (mặc định 60 lần/giây). **Mọi logic di chuyển/va chạm đặt ở đây.**
- **`delta`** để làm game **không phụ thuộc FPS**: `Position += hướng * tốc_độ * delta`.

### 7.3 Cách một script "với tới" node khác

| Cách                | Ví dụ                                  | Khi nào                                |
| ------------------- | -------------------------------------- | -------------------------------------- |
| `[Export]` kéo thả  | `[Export] Sprite2D _sprite;`           | **ưu tiên**: không phụ thuộc đường dẫn |
| `GetNode<T>("Tên")` | `GetNode<Timer>("Timer")`              | node con cố định                       |
| `%UniqueName`       | `GetNode<T>("%Hp")`                    | node đặt Unique Name trong scene       |
| Group               | `GetTree().GetNodesInGroup("enemies")` | thao tác nhiều node                    |
| Autoload            | `GameManager.Instance`                 | dịch vụ toàn cục                       |

### 7.4 Biến hiện trong Inspector

Đánh dấu `[Export]` để biến hiện trong Inspector (số, chuỗi, enum, node, Resource, `PackedScene`...). Nhờ vậy người thiết kế chỉnh được tốc độ, sát thương, scene đạn **không cần sửa code**. Sau khi thêm/sửa `[Export]`, phải **Build** để Inspector cập nhật.

### 7.5 Tạo node lúc đang chạy

```csharp
var bullet = BulletScene.Instantiate<Node2D>();    // dựng một bản sao scene
bullet.GlobalPosition = GlobalPosition;
GetParent().AddChild(bullet);                      // thêm vào cây thì mới hoạt động
// xoá khi xong:
QueueFree();                                       // xoá cuối frame, an toàn
```

Node **chưa** thêm vào cây thì chưa chạy `_Ready`, `_Process`. Quên `AddChild` là lỗi "đã tạo mà không thấy".

### 7.6 Autoload: node sống suốt game

Project Settings > **Globals** (tab Autoload) > thêm script hoặc scene và đặt tên. Godot tạo node đó **ngay dưới root, trước mọi scene**, và **giữ nguyên khi đổi scene**. Dùng cho: trạng thái game (vàng, điểm), âm nhạc, quản lý lưu/tải, **event bus** (chỉ chứa signal toàn cục). **Đừng lạm dụng:** autoload là phụ thuộc ngầm, càng nhiều càng khó hiểu và khó thử riêng.

### 7.7 Đổi cảnh

```csharp
GetTree().ChangeSceneToFile("res://scenes/Level2.tscn");   // sang scene khác
GetTree().ReloadCurrentScene();                            // chơi lại màn hiện tại
GetTree().Paused = true;                                   // tạm dừng (node cần ProcessMode phù hợp)
GetTree().Quit();
```

Đổi cảnh **xoá scene cũ** (node của nó bị giải phóng) nhưng **autoload còn nguyên**. Muốn giữ dữ liệu qua màn thì để trong autoload (hoặc file save), không để trong scene cũ.

### 7.8 Việc gì đặt ở đâu

| Việc                        | Đặt ở                                       |
| --------------------------- | ------------------------------------------- |
| di chuyển nhân vật          | script của `Player` trong `_PhysicsProcess` |
| hiển thị điểm               | script của `HUD`, nghe signal của autoload  |
| tổng điểm, vàng, tiến trình | autoload `GameManager`                      |
| tiếng nhạc nền              | autoload `Music` hoặc scene riêng           |
| dữ liệu vật phẩm            | Resource tự định nghĩa                      |

### 7.9 Sai lầm thường gặp

- Dùng `GetNode` trước `_Ready` hoặc sai đường dẫn (kết quả `null`).
- Đặt logic di chuyển trong `_Process` thay vì `_PhysicsProcess`.
- Quên `AddChild` sau `Instantiate`.
- Để dữ liệu cần giữ qua màn trong scene (mất khi đổi cảnh).
- Quá nhiều Autoload, mọi thứ phụ thuộc nhau.
- Sửa `[Export]` mà không Build.

### 7.10 Tự kiểm tra

1. Vẽ lại vòng đời của một node. `_Ready` chạy khi nào và mấy lần?
2. `_Process` và `_PhysicsProcess` khác nhau thế nào? Logic nào ở đâu?
3. Vì sao `[Export]` được ưu tiên hơn `GetNode` bằng đường dẫn chuỗi?
4. Autoload là gì? Nó khác scene hiện tại ra sao khi đổi cảnh?
5. Vì sao node mới `Instantiate` mà không hiện/không chạy?
6. Muốn giữ số vàng qua nhiều màn, để ở đâu?
