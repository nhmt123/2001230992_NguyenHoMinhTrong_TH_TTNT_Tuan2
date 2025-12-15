## Triển khai Thuật Toán Tìm Kiếm AKT và A\* (8-Puzzle/15-Puzzle)

* **Họ và tên:** Nguyễn Hồ Minh Trọng
* **MSSV:** 2001230992
* **Mã nguồn colab:** https://colab.research.google.com/drive/1NQAQLO31kP3QgKgiYoHRwqgwna-cnQhK?usp=sharing

Dự án này triển khai hai thuật toán tìm kiếm có thông tin (Informed Search Algorithms) là **AKT (Đếm số ô sai vị trí)** và **A\* (Khoảng cách Manhattan)** để giải bài toán trượt ô (như 8-Puzzle, 15-Puzzle).

---

### Phần 1: Class Abstract `SearchAlgorithm`

`SearchAlgorithm` là lớp trừu tượng (`abstract class`) định nghĩa khung chung cho tất cả các thuật toán tìm kiếm. Các thuật toán cụ thể (AKT, A\*) phải kế thừa và cài đặt các phương thức cơ bản này.

#### 1.1. Hàm Khởi Tạo `__init__(self, n=3)`

* **`self.n`**: Kích thước của bảng ($n \times n$). Ví dụ: $n=3$ là bài toán 8-Puzzle.
* **`self.rows` và `self.cols`**: Định nghĩa 4 hướng di chuyển của ô trống.
    * `[1, 0]`: Xuống dưới
    * `[0, -1]`: Sang trái
    * `[-1, 0]`: Lên trên
    * `[0, 1]`: Sang phải

#### 1.2. Các Hàm Trừu Tượng (Bắt buộc cài đặt)

| Phương thức | Mục đích |
| :--- | :--- |
| `calculate_heuristic(self, state, goal)` | Tính hàm heuristic $h(n)$. |
| `solve(self, initial, empty_pos, goal)` | Giao diện chính để giải bài toán. |

#### 1.3. Hàm Kiểm Tra Hợp Lệ `is_safe(self, x, y)`

Kiểm tra tọa độ $(x, y)$ có nằm trong phạm vi bảng $0 \le x < n$ và $0 \le y < n$ hay không, nhằm tránh truy cập vượt biên mảng.

---

### Phần 2: Thuật Toán AKT (Misplaced Tiles)

AKT là thuật toán tìm kiếm sử dụng hàm heuristic đơn giản: **đếm số ô sai vị trí**.
Công thức đánh giá: $f(n) = h(n)$ (chỉ dựa vào heuristic).

#### 2.1. Class `Node_AKT`

Node của AKT bao gồm các thuộc tính chính:
* `parent`: Node cha (để truy vết)
* `matrix`: Trạng thái hiện tại của bảng
* `cost`: Chi phí (số ô sai vị trí)
* `level`: Độ sâu (số bước đã đi)

Hàm so sánh `__lt__` ưu tiên node có `cost` (tức $h(n)$) nhỏ nhất.

#### 2.2. Hàm Tính Heuristic `calculate_heuristic()`

Hàm này đếm số lượng ô (khác 0) không nằm ở vị trí đích của nó.

*Ví dụ minh họa tính $h(n)$:*
| Trạng thái hiện tại | Trạng thái đích | Ô sai vị trí | $h(n)$ |
| :---: | :---: | :---: | :---: |
| `1 2 3 / 5 6 0 / 7 8 4` | `1 2 3 / 5 8 6 / 0 7 4` | Ô 6, Ô 8, Ô 7 | 3 |

#### 2.3. Hàm Giải Bài Toán `solve()`

1. Khởi tạo hàng đợi ưu tiên (`open_list`).
2. Lặp: Lấy node có **cost nhỏ nhất**.
3. Kiểm tra đích: Nếu `current.cost == 0` (đã đến đích).
4. Mở rộng node: Tạo 4 node con (4 hướng di chuyển).
5. Đưa node con **hợp lệ** vào hàng đợi.

---

### Phần 3: Thuật Toán A\* (Manhattan Distance)

A\* là thuật toán tìm kiếm tối ưu, sử dụng cả chi phí thực tế và chi phí ước lượng.
Công thức đánh giá: $f(n) = g(n) + h(n)$.
* $g(n)$: Chi phí thực tế (số bước đã đi)
* $h(n)$: Chi phí ước lượng (Khoảng cách Manhattan)
* $f(n)$: Tổng chi phí dự kiến

#### 3.1. Class `Node_Astar`

Node A\* lưu trữ cả `g`, `h`, và `f`. Hàm so sánh `__lt__` ưu tiên node có **$f$ nhỏ nhất**.

#### 3.2. Hàm Tính Heuristic `calculate_heuristic()`

Hàm này tính tổng khoảng cách Manhattan cho tất cả các ô (trừ ô trống).
**Khoảng cách Manhattan:** $|\text{hiện tại}_x - \text{đích}_x| + |\text{hiện tại}_y - \text{đích}_y|$.

*Ví dụ minh họa tính $h(n)$:*
| Ô | Vị trí hiện tại | Vị trí đích | Khoảng cách Manhattan |
| :---: | :---: | :---: | :---: |
| 6 | (1, 1) | (1, 2) | $|1-1| + |1-2| = 1$ |
| 8 | (2, 1) | (1, 1) | $|2-1| + |1-1| = 1$ |
| 7 | (2, 0) | (2, 1) | $|2-2| + |0-1| = 1$ |
| **Tổng** | | | **$h(n) = 3$** |

#### 3.3. Hàm Giải Bài Toán `solve()`

1. Khởi tạo node gốc: $g=0$, $h=h(initial)$.
2. Lặp: Lấy node có **$f$ nhỏ nhất**.
3. Kiểm tra đích: Nếu `current.h == 0` (đã đến đích).
4. Tạo node con: `g` của node con bằng `g` của node cha cộng thêm 1, và tính lại `h` mới.

---

### Phần 4: Kết Quả Kiểm Thử (Tests)

#### 4.1. Test với $n=3$ (8-Puzzle)

| Thuật toán | Tổng số bước | Chi phí thực tế $g(n)$ |
| :---: | :---: | :---: |
| AKT | 3 | - |
| A\* | 3 | 3 |

**Kết quả A\*:** $f(n)$ duy trì là 3 trong suốt quá trình tìm kiếm tối ưu.

* Bước 0: $[g=0, h=3, f=3]$
* Bước 1: $[g=1, h=2, f=3]$
* Bước 2: $[g=2, h=1, f=3]$
* Bước 3: $[g=3, h=0, f=3]$

#### 4.2. Test với $n=4$ (15-Puzzle)

| Thuật toán | Tổng số bước | Chi phí thực tế $g(n)$ |
| :---: | :---: | :---: |
| AKT | 2 | - |
| A\* | 2 | 2 |

**Kết quả A\*:**

* Bước 0: $[g=0, h=2, f=2]$
* Bước 1: $[g=1, h=1, f=2]$
* Bước 2: $[g=2, h=0, f=2]$
