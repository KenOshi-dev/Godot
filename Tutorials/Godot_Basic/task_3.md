# Chương 3: Giao Diện Editor

**Ý chính:** editor chia thành vài **dock** (bảng) cố định; biết mỗi bảng dùng để làm gì là đủ dùng trôi chảy.

### 1. Các khu vực

| Khu vực                           | Nằm ở      | Dùng để                                        |
| --------------------------------- | ---------- | ---------------------------------------------- |
| **Scene** (dock cây)              | trái-trên  | xem/sửa **cây node** của scene đang mở         |
| **FileSystem**                    | trái-dưới  | duyệt file project, kéo thả asset vào scene    |
| **Viewport (2D/3D)**              | giữa       | nhìn và kéo thả node trực quan                 |
| **Script**                        | giữa (tab) | viết code                                      |
| **Inspector**                     | phải       | sửa **thuộc tính** của node/resource đang chọn |
| **Node** (cạnh Inspector)         | phải       | tab **Signals** (nối signal) và **Groups**     |
| **Output / Debugger / Animation** | dưới       | log, lỗi, profiler, timeline animation         |

### 2. Thứ tự thao tác cơ bản

1. **Tạo node gốc** (nút _Other Node_ hoặc _2D Scene_) → **lưu scene** (Ctrl+S, đặt tên).
2. **Thêm node con** (chuột phải node > _Add Child Node_, hoặc Ctrl+A).
3. **Chỉnh thuộc tính** trong Inspector (vị trí, texture, độ lớn...).
4. **Gắn script** (chuột phải node > _Attach Script_).
5. **Chạy**: `F5` chạy cả dự án (bắt đầu từ **Main Scene**), `F6` chạy **scene đang mở** (rất tiện để thử từng phần).

### 3. Hai kỹ năng debug nên biết ngay

- Khi game đang chạy, chuyển sang **Remote** trong dock Scene để xem và sửa thuộc tính node **đang chạy** (kết quả không lưu lại).
- Menu **Debug**: _Visible Collision Shapes_ (hiện hình va chạm), _Visible Navigation_.

### 4. Sai lầm thường gặp

- Bấm `F5` mà chưa đặt **Main Scene** (Godot sẽ hỏi); muốn thử scene đang làm thì dùng `F6`.
- Sửa thuộc tính trong tab **Remote** rồi tưởng đã lưu (không lưu).
- Quên lưu scene trước khi chạy (chạy bản cũ).

### 5. Tự kiểm tra

1. Inspector, Scene dock, FileSystem mỗi bảng dùng để làm gì?
2. `F5` và `F6` khác nhau thế nào?
3. Tab Remote dùng khi nào và có lưu thay đổi không?
4. Signal nối ở tab nào của editor?

---

**Tiếp theo:** [Task 4](task_4.md)
