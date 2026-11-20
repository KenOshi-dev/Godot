# Chương 4: Node và Scene (phần quan trọng nhất)

**Ý chính:** nắm chắc chương này là nắm chắc Godot. Node là việc nhỏ; scene là cách ghép việc nhỏ thành một đối tượng; cây của scene quyết định ai điều khiển ai.

### 1. Mô hình tư duy: scene là "class", instance là "object"

Nếu bạn đã biết lập trình: **scene giống một class**, mỗi lần kéo scene vào scene khác (instance) là **tạo một object** từ class đó. Sửa scene gốc thì mọi bản sao đều thay đổi. Nhờ đó bạn thiết kế `Enemy` một lần, dùng 50 lần.

### 2. Ba thứ dễ nhầm

| Thao tác                                                          | Nghĩa là                                                 | Khi nào dùng                          |
| ----------------------------------------------------------------- | -------------------------------------------------------- | ------------------------------------- |
| **Instance scene** (ấn biểu tượng xích hoặc kéo file `.tscn` vào) | thêm **một bản sao sống**, vẫn nối với scene gốc         | dùng lại Enemy, Coin, HUD trong level |
| **Inherited scene** (_New Inherited Scene_)                       | tạo scene **mới kế thừa** scene gốc, thêm/sửa phần riêng | biến thể: `BossEnemy` kế thừa `Enemy` |
| **Duplicate node** (Ctrl+D)                                       | **sao chép rời**, không còn nối với gốc                  | chỉ để nhân nhanh trong cùng scene    |

Mặc định, node con **bên trong** một scene đã instance bị khóa để bạn không sửa nhầm; bật _Editable Children_ nếu muốn sửa riêng bản sao đó (cẩn thận: sửa nhiều làm mất lợi ích dùng chung).

### 3. Quy tắc cây node

- Mỗi scene có **một node gốc**; mọi node khác là con (trực tiếp hoặc gián tiếp) của nó.
- **Cha điều khiển con:** di chuyển/xoay/ẩn cha thì con **đi theo**; xoá cha thì con **bị xoá**; vị trí con tính **so với cha**.
- **Tên node** trong cùng một cha là **duy nhất** (đường dẫn dựa vào tên).
- **Thứ tự** con trong cây quyết định thứ tự xử lý và (với node 2D) thứ tự vẽ mặc định: con ở dưới cùng của danh sách được vẽ **sau** (nằm trên).
- **Unique Name (`%Tên`)**: đặt tên duy nhất cho node trong scene để truy cập bằng `%Tên` không phụ thuộc vị trí.
- **Group:** gắn nhãn cho node ("enemies") để thao tác hàng loạt.

### 4. Ví dụ: thiết kế scene Player

```
Player (CharacterBody2D)        ← node gốc: là thân vật lý, có script điều khiển
 ├─ AnimatedSprite2D          ← hình ảnh đang hoạt động
 ├─ CollisionShape2D          ← hình dạng va chạm (bắt buộc phải có với body)
 ├─ Camera2D                  ← theo nhân vật
 └─ Hurtbox (Area2D)          ← vùng nhận sát thương
     └─ CollisionShape2D
```

Mỗi node con **có một nhiệm vụ**; node gốc được chọn theo **bản chất** của đối tượng (nhân vật điều khiển bằng code → `CharacterBody2D`; vùng phát hiện → `Area2D`; đồ họa thuần → `Node2D`). Chọn node gốc đúng là quyết định quan trọng nhất khi tạo scene.

### 5. Nguyên tắc thiết kế scene

1. **Scene nhỏ, tự đủ:** một scene làm một đối tượng và chạy được một mình khi bấm `F6` (để thử riêng).
2. **Ghép thay vì nhồi:** thay vì một node khổng lồ làm mọi thứ, chia thành nhiều node/scene nhỏ (`Health`, `Hitbox`, `Mover`).
3. **Gọi xuống, báo lên:** cha gọi hàm con; con phát signal cho cha. Con **không** nên `GetNode("../..")` leo lên cha.
4. **Không lồng sâu vô ích:** cây dưới \~4–5 tầng là dễ hiểu.

### 6. Sai lầm thường gặp

- Tạo một scene khổng lồ (cả game trong một scene) thay vì chia nhỏ.
- Duplicate node (Ctrl+D) rồi sửa mỗi bản, lạc mất lợi ích dùng chung của instance.
- `CollisionShape2D` thiếu hoặc chưa gán **Shape** (cảnh báo vàng, va chạm không hoạt động).
- Con gọi `GetParent()` trực tiếp để điều khiển cha, gắn chặt scene với nơi dùng nó.
- Đổi tên node mà code dùng đường dẫn chuỗi (đường dẫn gãy).

### 7. Tự kiểm tra

1. Vì sao nói scene giống class, instance giống object?
2. Instance, Inherited scene và Duplicate khác nhau thế nào? Khi nào dùng mỗi cái?
3. Di chuyển node cha thì node con ra sao? Vị trí con tính so với gì?
4. Vì sao `CharacterBody2D` cần có `CollisionShape2D` làm con?
5. Nguyên tắc "gọi xuống, báo lên" là gì?
6. Thứ tự con trong cây ảnh hưởng gì?

---

**Tiếp theo:** [Task 5](task_5.md)
