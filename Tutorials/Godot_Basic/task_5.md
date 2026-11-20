# Chương 5: Các Node 2D Cốt Lỗi, Tọa Độ & Thứ Tự Vẽ

**Ý chính:** gần như mọi thứ nhìn thấy trong game 2D là một node kế thừa `CanvasItem`. Biết transform (vị trí/xoay/tỉ lệ), thứ tự vẽ và vài node nền tảng là đủ dựng được hầu hết cảnh 2D.

### 1. Mô hình tư duy

`Node2D` là **một tấm bảng trong suốt có toạ độ**. Đặt thứ gì lên tấm bảng thì thứ đó đi theo khi bạn dời, xoay hoặc phóng to tấm bảng. Cây node là chồng các tấm bảng lồng nhau: tấm con lồng trong tấm cha.

### 2. Transform: vị trí, xoay, tỉ lệ

| Thuộc tính       | Ý nghĩa                   | Lưu ý                                                        |
| ---------------- | ------------------------- | ------------------------------------------------------------ |
| `Position`       | vị trí **so với cha**     | trục **Y hướng xuống**, gốc ở góc trên-trái                  |
| `GlobalPosition` | vị trí thật trên thế giới | dùng khi so khoảng cách giữa node khác cha                   |
| `Rotation`       | góc xoay                  | **radian** trong code (Inspector hiện độ)                    |
| `Scale`          | tỉ lệ (âm = lật)          | ảnh hưởng cả con; **đừng scale node va chạm** (xem chương 9) |
| `Skew`           | nghiêng                   | ít dùng                                                      |

Con **thừa hưởng** transform của cha: xoay cha 90° thì con cũng xoay quanh cha, và `Position` của con vẫn là toạ độ trong hệ của cha. Muốn vị trí thật dùng `GlobalPosition`.

### 3. Thuộc tính chung của mọi vật vẽ (CanvasItem)

| Thuộc tính      | Tác dụng                                                    |
| --------------- | ----------------------------------------------------------- |
| `Visible`       | ẩn/hiện (ẩn cha thì ẩn cả con)                              |
| `Modulate`      | nhân màu cho node **và con** (hiệu ứng mờ, nhuộm màu, nháy) |
| `SelfModulate`  | nhân màu chỉ cho **chính nó**, không ảnh hưởng con          |
| `ZIndex`        | số lớn vẽ sau, nằm trên                                     |
| `YSortEnabled`  | các con được vẽ theo Y (dưới thấp đè lên trên cao)          |
| `TextureFilter` | `Nearest` (giữ nét pixel) hay `Linear` (mịn)                |
| `Material`      | gắn shader/hiệu ứng                                         |

### 4. Các node nền tảng

| Node                  | Dùng để                                            |
| --------------------- | -------------------------------------------------- |
| `Node2D`              | nhóm/điểm đứng có transform (không vẽ gì)          |
| `Sprite2D`            | vẽ **một ảnh**                                     |
| `AnimatedSprite2D`    | vẽ ảnh **chuyển động theo khung** (`SpriteFrames`) |
| `Camera2D`            | camera: theo nhân vật, phóng to, giới hạn bản đồ   |
| `CollisionShape2D`    | hình dạng va chạm (con của body/area)              |
| `Marker2D`            | **điểm đánh dấu** (nơi sinh quái, đầu súng)        |
| `Line2D`, `Polygon2D` | vẽ đường, đa giác                                  |
| `Timer`               | đếm giờ (là `Node`, không có vị trí)               |
| `TileMapLayer`        | bản đồ ô lưới (chương 11)                          |

### 5. Camera2D

Chỉ **một Camera2D** được bật (`Enabled`) tại một thời điểm trong một viewport. Làm con của nhân vật thì camera đi theo. Các thuộc tính hay dùng: `Zoom`, **Position Smoothing** (mượt), **Limit** Left/Right/Top/Bottom (không ra ngoài bản đồ). Đặt camera ở **scene Player** là cách dễ nhất cho game cuộn màn.

### 6. Thứ tự vẽ

Quy tắc, từ ưu tiên thấp đến cao:

1. **Thứ tự trong cây:** node ở **dưới** trong danh sách (hoặc con sau) vẽ **sau**, nằm trên.
2. **`ZIndex`:** số lớn hơn vẽ sau (đè lên), kể cả khác nhánh.
3. **`YSortEnabled` ở node cha:** các con được sắp theo toạ độ **Y**, con có Y lớn hơn (thấp hơn trên màn hình) vẽ sau. Cực hợp game nhìn từ trên xuống.
4. **`CanvasLayer`:** một lớp vẽ riêng, **không bị camera ảnh hưởng**; dùng cho HUD/menu.

Quy tắc Y-sort: **điểm gốc (origin) của sprite nên đặt ở chân** nhân vật/cây; nếu gốc ở giữa người, thứ tự sẽ lệch.

### 7. Node 2D và Control: đừng nhầm

- **`Node2D`** (và con) dành cho **thế giới game**, đặt bằng toạ độ và chịu ảnh hưởng của camera.
- **`Control`** (Button, Label, Panel...) dành cho **giao diện**, đặt bằng **anchor/container**, thường nằm dưới `CanvasLayer` để không dính camera.
- Không nhét `Control` vào giữa thế giới game trừ khi có chủ đích (ví dụ nhãn tên trên đầu nhân vật).

### 8. Bảng quyết định

| Muốn...                                | Dùng                        |
| -------------------------------------- | --------------------------- |
| hiện một ảnh tĩnh                      | `Sprite2D`                  |
| hiện ảnh hoạt hình theo khung          | `AnimatedSprite2D`          |
| camera đi theo nhân vật                | `Camera2D` làm con nhân vật |
| đánh dấu điểm sinh quái/đầu nòng       | `Marker2D`                  |
| HUD không bị camera kéo                | `CanvasLayer` + `Control`   |
| vật thấp hơn đè vật cao hơn (top-down) | cha bật `YSortEnabled`      |

### 9. Sai lầm thường gặp

- Nhầm `Position` với `GlobalPosition` khi tính khoảng cách.
- Đặt gốc sprite ở giữa người nên Y-sort sai.
- Bật hai `Camera2D` cùng lúc.
- Gắn HUD thẳng vào scene thế giới nên nó trôi theo camera.
- Scale node có va chạm (hình va chạm méo, vật lý sai).
- Dùng độ thay vì radian khi gán `Rotation` trong code.

### 10. Tự kiểm tra

1. `Position` và `GlobalPosition` khác nhau ra sao? Trục Y hướng nào?
2. `Modulate` và `SelfModulate` khác nhau ở đâu?
3. Kể bốn yếu tố quyết định thứ tự vẽ.
4. Vì sao HUD đặt trong `CanvasLayer`?
5. Vì sao điểm gốc sprite nên ở chân khi dùng Y-sort?
6. Khi nào dùng `Marker2D`?

---

**Tiếp theo:** [Task 6](task_6.md)
