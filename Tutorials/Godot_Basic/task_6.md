## 6. Resource & Asset

**Ý chính:** **Resource là dữ liệu** (không phải đối tượng sống trong cây). Ảnh, âm thanh, hình va chạm, bảng ô lưới, theme và cả dữ liệu bạn tự thiết kế đều là Resource; nhiều node có thể dùng chung một Resource.

### 1. Mô hình tư duy

Resource là **bản vẽ và nguyên liệu trong kho**. Node là **thợ** làm việc trong cây. Thợ lấy bản vẽ ra dùng; nhiều thợ cùng dùng một bản vẽ thì sửa bản vẽ là ảnh hưởng cả đám.

### 2. Từ file asset đến Resource: quy trình import

Thả file `.png`, `.wav`, `.ogg`, `.ttf` vào project → Godot **tự nhập (import)** thành Resource nội bộ và tạo file `.import` bên cạnh. Mỗi loại file có **thiết lập nhập** (chọn file, mở dock **Import**, đổi tuỳ chọn, bấm **Reimport**):

| Loại          | Thiết lập hay chỉnh                                                               |
| ------------- | --------------------------------------------------------------------------------- |
| Ảnh (texture) | preset **2D Pixel** cho pixel art (giữ nét, không làm mịn); nén cho ảnh lớn       |
| Âm thanh      | bật **Loop** cho nhạc nền (`.ogg`/`.wav`); `.wav` cho tiếng ngắn, `.ogg` cho nhạc |
| Font          | bật hỗ trợ ký tự cần thiết (tiếng Việt cần font có đủ dấu)                        |

Sửa thiết lập import xong **phải Reimport** mới có hiệu lực.

### 3. Các Resource bạn sẽ gặp

| Resource                                                             | Là gì                                                              |
| -------------------------------------------------------------------- | ------------------------------------------------------------------ |
| `Texture2D` (và `AtlasTexture`)                                      | ảnh; Atlas là một vùng cắt trong ảnh lớn                           |
| `SpriteFrames`                                                       | các animation (dãy khung hình) của `AnimatedSprite2D`              |
| `Shape2D` (`RectangleShape2D`, `CircleShape2D`, `CapsuleShape2D`...) | hình va chạm                                                       |
| `TileSet`                                                            | bộ ô lưới dùng cho tilemap                                         |
| `AudioStream`                                                        | âm thanh                                                           |
| `Font`, `Theme`                                                      | chữ và giao diện                                                   |
| `PackedScene`                                                        | **scene đã lưu** (chính là file `.tscn` khi nạp)                   |
| `Material` / `Shader`                                                | hiệu ứng vẽ                                                        |
| **Resource tự định nghĩa**                                           | dữ liệu game của bạn (`ItemData`, `EnemyData`): kế thừa `Resource` |

### 4. Nhúng hay tách file

Resource có thể **nhúng** (nằm trong file scene `.tscn`) hoặc **tách** thành file `.tres`/`.res` riêng. Nhúng tiện cho thứ chỉ dùng một chỗ (hình va chạm của một node). **Tách file** khi muốn **dùng chung** hoặc chỉnh một nơi áp dụng mọi nơi (một `SpriteFrames`, một `Theme`). Trong Inspector, mở menu của Resource để **Save As...** (tách) hoặc **Make Unique** (tạo bản sao riêng).

### 5. Chia sẻ: lợi và bẫy

- Hai node trỏ cùng một Resource thì **sửa một bên đổi cả hai**. Tốt cho dữ liệu chung (cùng `Theme`), nguy hiểm khi bạn muốn mỗi node một giá trị riêng.
- Ví dụ bẫy kinh điển: 5 kẻ địch dùng chung một `CircleShape2D`, sửa bán kính cho một con là cả 5 đổi. Dùng **Make Unique** hoặc bật **Local to Scene**.
- **Dữ liệu tĩnh** (chỉ số cơ bản) hợp với Resource; **trạng thái đang chạy** (máu hiện tại) để trong node, không ghi vào Resource dùng chung.

### 6. Dùng Resource trong code (C#)

```csharp
[Export] public Texture2D Icon;                 // kéo thả trong Inspector (khuyên dùng)
[Export] public PackedScene BulletScene;
var tex = GD.Load<Texture2D>("res://assets/icon.png");   // nạp lúc chạy
```

Nạp cùng đường dẫn hai lần trả về **cùng một đối tượng** (engine lưu cache). Muốn bản riêng thì `Duplicate()`.

### 7. Bảng quyết định

| Muốn...                                   | Dùng                                               |
| ----------------------------------------- | -------------------------------------------------- |
| nhiều node cùng một giao diện             | một `Theme` tách file                              |
| mỗi kẻ địch hình va chạm riêng            | **Make Unique** hoặc Local to Scene                |
| định nghĩa vật phẩm, kẻ địch trong editor | Resource tự định nghĩa (`ItemData`)                |
| pixel art không bị mờ                     | import preset 2D Pixel + `TextureFilter = Nearest` |
| nhạc lặp                                  | import có **Loop**                                 |

### 8. Sai lầm thường gặp

- Đổi thiết lập import mà quên **Reimport**.
- Sửa một Resource dùng chung mà tưởng chỉ đổi một node.
- Ghi trạng thái động vào Resource dùng chung.
- Dùng font không có dấu tiếng Việt nên chữ bị lỗi.
- Tải texture khổng lồ không cần thiết gây tốn bộ nhớ.

### 9. Tự kiểm tra

1. Resource khác Node ở bản chất nào? Lấy hai ví dụ mỗi loại.
2. File `.import` sinh ra khi nào và vì sao phải Reimport?
3. Khi nào nên tách Resource ra file riêng? Make Unique làm gì?
4. Hai node cùng dùng một Resource, sửa một bên thì sao?
5. `[Export]` một Resource khác `GD.Load` ở điểm nào?

---

**Tiếp theo:** [Task 7](task_7.md)
