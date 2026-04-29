# Tài liệu Hướng dẫn và Vận hành Chức năng Đăng ký Văn bản đi

Tài liệu này cung cấp cái nhìn chi tiết về chức năng "Đăng ký Văn bản đi" trong Hệ thống Quản lý Văn bản Điều hành, bao gồm hướng dẫn cho người dùng, luồng nghiệp vụ, chi tiết kỹ thuật cho nhà phát triển và hướng dẫn kiểm thử cho QA/Tester.

## 1. Giới thiệu chung

Chức năng "Đăng ký Văn bản đi" (Outgoing Document Registration) cho phép bộ phận văn thư hoặc người dùng có thẩm quyền chính thức hóa một văn bản dự thảo thành văn bản ban hành chính thức của tổ chức. Quá trình này bao gồm việc cấp số hiệu, cập nhật thông tin chính thức, tải lên bản scan có chữ ký (nếu có) và thay đổi trạng thái văn bản.

## 2. Hướng dẫn sử dụng (Dành cho Người dùng)

### 2.1. Các trường hợp đăng ký
Có hai cách tiếp cận để đăng ký một văn bản đi:
1.  **Đăng ký văn bản mới từ đầu:** Người dùng (thường là văn thư) trực tiếp tạo và đăng ký một văn bản đã có sẵn bên ngoài (không qua quy trình trình duyệt nội bộ của hệ thống). Truy cập qua menu "Văn bản đi" -> nút "Đăng ký văn bản đi".
2.  **Đăng ký từ một văn bản dự thảo đã được duyệt:** Quản lý một văn bản đã trải qua quá trình soạn thảo và phê duyệt trên hệ thống (Trạng thái: "Chờ đăng ký"). Chọn văn bản trong danh sách và bấm "Đăng ký".

### 2.2. Chi tiết các trường thông tin (Form nhập liệu)

Giao diện đăng ký (được định nghĩa trong `van_ban_di_form.html`) yêu cầu người dùng điền/kiểm tra các thông tin sau:

*   **Số văn bản đi:** Mã hệ thống tự sinh (VD: VBO00000001). Cấp tự động, không chỉnh sửa.
*   **Trạng thái:** Tình trạng hiện tại của văn bản (VD: Chờ đăng ký).
*   **Ngày ban hành:** Ngày hệ thống ghi nhận đăng ký (thường là ngày hiện tại). Tự động cập nhật.
*   **Ngày ký văn bản (*):** Ngày thực tế người có thẩm quyền ký vào văn bản. Người dùng chọn từ lịch (bắt buộc).
*   **Loại văn bản (*):** Quyết định, Công văn, Thông báo... 
    *   *Đăng ký mới:* Chọn từ danh sách.
    *   *Đăng ký từ dự thảo:* Hệ thống tự động lấy từ dữ liệu đã có.
*   **Mức độ ưu tiên:** Bình thường, Khẩn, Tối khẩn... (Mặc định hoặc chọn từ danh sách).
*   **Người soạn thảo / Người ký:** 
    *   *Đăng ký mới:* Chọn từ danh sách cán bộ/giáo viên.
    *   *Đăng ký từ dự thảo:* Tự động lấy từ quy trình trước đó.
*   **Nơi nhận:** Nơi sẽ gửi văn bản đến (nhập dạng văn bản).
*   **Số ký hiệu văn bản (*):** Số chính thức của văn bản (VD: 123/QD-THPT).
    *   Người dùng có thể nhập tay.
    *   Hoặc sử dụng nút **"Cấp số"** để hệ thống tự động sinh số dựa trên quy tắc cấu hình và loại văn bản.
*   **Trích yếu (*):** Tóm tắt nội dung chính của văn bản.
*   **File dự thảo hiện có:** Chỉ hiển thị nếu đăng ký từ một văn bản đã qua quy trình soạn thảo. Cho phép tải xuống hoặc xem trước.
*   **File chính thức (*):** Bản scan PDF hoặc file Word cuối cùng đã có chữ ký/đóng dấu. Tải lên từ máy tính.
*   **Tệp đính kèm:** Các phụ lục, tài liệu tham khảo kèm theo (tùy chọn).

*(Các trường đánh dấu (*) thường là bắt buộc nhập)*

### 2.3. Các thao tác (Nút bấm)
*   **Lưu (Đăng ký):** Xác nhận các thông tin và chuyển trạng thái văn bản sang "Đã đăng ký".
*   **Hủy:** Hủy bỏ các thay đổi và quay lại màn hình trước đó.
*   **Phát hành bên ngoài / Ban hành nội bộ / Luân chuyển:** Các hành động khả dụng *sau khi* văn bản đã được đăng ký thành công.

## 3. Luồng Nghiệp vụ (Business Logic)

### 3.1. Luồng 1: Đăng ký Văn bản Dự thảo (Đã có sẵn trên hệ thống)

**Dành cho người dùng:**
1.  **Đăng nhập** vào hệ thống bằng tài khoản có quyền của bộ phận **Văn thư**.
2.  **Tìm văn bản cần đăng ký.** Truy cập vào menu **"Văn bản đi"** (Danh sách văn bản đi). Tìm các văn bản đang ở trạng thái **"Chờ đăng ký"**. Đây là những văn bản đã được chuyên viên soạn thảo, gửi duyệt và được Lãnh đạo (Hiệu trưởng/Phó Hiệu trưởng) phê duyệt trên hệ thống phần mềm.
3.  **Mở form đăng ký.** Nhấn vào nút hành động **"Đăng ký văn bản đi"** (thường là biểu tượng cái bút hoặc nút Đăng ký) nằm trên chính dòng của văn bản đó trong bảng danh sách.
4.  **Kiểm tra thông tin.** Màn hình form đăng ký hiện ra. Khác với Luồng 2, lúc này hầu hết các thông tin **đã được điền sẵn và bị khóa (readonly)** (như: Loại văn bản, Người soạn thảo, Người ký, Trích yếu...) vì dữ liệu này kế thừa từ quá trình trình duyệt trước đó, tránh việc văn thư tự ý sửa đổi nội dung đã được sếp duyệt.
5.  **Cấp số hiệu.** Tại trường **"Số ký hiệu văn bản"**, người dùng có thể nhập số tay hoặc tiện lợi nhất là nhấn nút **"Cấp số"**. Hệ thống sẽ tự động tính toán và điền số hiệu tiếp theo theo đúng chuẩn (Ví dụ: 125/QĐ-THPT).
6.  **Bổ sung Ngày ký.** Chọn **"Ngày ký văn bản"** thực tế từ lịch.
7.  **Đính kèm tệp.**
    *   Hệ thống sẽ hiển thị **"File dự thảo hiện có"** để văn thư có thể xem lại, tải về in ra giấy đưa đi đóng dấu.
    *   Sau khi đã đóng dấu xong (hoặc có bản ký số), văn thư tải bản scan PDF/Word đó lên ô **"File chính thức"**.
8.  **Hoàn tất.** Nhấn nút **"Lưu"**. Văn bản sẽ chuyển sang trạng thái "Đã đăng ký". Sau bước này, văn thư có thể tiếp tục thao tác "Phát hành bên ngoài", "Luân chuyển" hoặc "Ban hành nội bộ".

**Dành cho Developer (Dưới nền):**
1.  **Tiền điều kiện:** Một văn bản đã được tạo (`tao_van_ban_view`) và trải qua quy trình duyệt. Trạng thái hiện tại phải là `CHO_DANG_KY`.
2.  **Truy cập:** Người dùng (văn thư) mở chi tiết văn bản thông qua URL chứa mã văn bản (VD: `/van-ban-di/VBO00000005/dang-ky/`). Hàm `dang_ky_van_ban_di_view(request, so_vb_di)` trong `views.py` được gọi.
3.  **Kiểm tra quyền:** Hệ thống kiểm tra người dùng hiện tại có quyền văn thư hay không (`is_van_thu`). Nếu không, từ chối truy cập.
4.  **Kiểm tra trạng thái:** Đảm bảo văn bản thực sự ở trạng thái `CHO_DANG_KY`. Nếu văn bản đã ở trạng thái sau đăng ký, màn hình sẽ chỉ hiển thị chế độ xem (không cho sửa).
5.  **Render giao diện:** Nếu hợp lệ, hệ thống nạp dữ liệu cũ từ Database (`instance=document`) vào `VanBanDiDangKyForm`. Trong file HTML `van_ban_di_form.html`, các thẻ `{% if not is_new_registration %}` sẽ phát huy tác dụng để render các trường thành dạng `<input readonly>` (chỉ xem) thay vì form nhập liệu bình thường.
6.  **Xử lý "Cấp số" (Ajax):** Nếu người dùng nhấn nút "Cấp số" trên giao diện, một Ajax request sẽ gọi đến API `cap_so_van_ban_di_view`. API này dùng hàm `generate_so_ky_hieu` (trong `models.py`) để tính toán, tìm số thứ tự lớn nhất trong năm của loại văn bản đó, cộng 1 và ráp chuỗi trả về giao diện.
7.  **Xử lý Form (POST):**
    *   Hệ thống khởi tạo `VanBanDiDangKyForm` với dữ liệu nhập vào và file tải lên.
    *   Xác thực dữ liệu (`form.is_valid()`).
    *   Nếu chưa có `ngay_ban_hanh`, gán bằng ngày hiện tại (`timezone.localdate()`).
    *   Gọi hàm `generate_registration_number` (trong `models.py`) một lần nữa để sinh tự động Số thứ tự và Số ký hiệu (nếu chưa nhập tay) để chốt cứng vào CSDL, phòng trường hợp 2 người cùng bấm cấp số 1 lúc.
    *   Đổi `trang_thai_vb_di` sang `DA_DANG_KY`.
    *   Lưu các thay đổi vào Database (`registered_document.save()`).
    *   Lưu các file vật lý (`form.save_uploaded_files()`).
8.  **Kết quả:** Reload lại trang đăng ký, lúc này form sẽ bị khóa hoàn toàn (vì đã đăng ký xong) và hiện thêm các nút chức năng luân chuyển/phát hành.

### 3.2. Luồng 2: Đăng ký Văn bản Mới (Không qua quy trình dự thảo)

**Dành cho người dùng:**
1.  **Đăng nhập** vào hệ thống bằng tài khoản có quyền hạn của bộ phận **Văn thư**.
2.  **Truy cập trang Danh sách** bằng cách nhấn vào menu **"Văn bản đi"** (hoặc "Danh sách văn bản đi").
3.  **Mở form đăng ký mới.** Tại màn hình danh sách, nhìn sang thanh công cụ (sidebar) hoặc thanh menu phụ, nhấn vào nút/tab có tên là **"Đăng ký văn bản đi"**. *(Lưu ý: Không bấm vào nút "Đăng ký" của một dòng văn bản cụ thể nào trong bảng, vì đó là Luồng 1).*
4.  **Nhập liệu vào Form.** Màn hình form đăng ký trống sẽ hiện ra. Người dùng cần điền đầy đủ các thông tin:
    *   **Ngày ký văn bản** (Chọn từ lịch)
    *   **Loại văn bản** (Chọn từ danh sách thả xuống như Quyết định, Công văn...)
    *   **Mức độ ưu tiên** (Chọn Khẩn, Bình thường...)
    *   **Người soạn thảo** và **Người ký** (Chọn tên cán bộ/giáo viên)
    *   **Nơi nhận**
    *   **Số ký hiệu văn bản:** Nhập tay hoặc nhấn nút **"Cấp số"** để hệ thống tự động điền.
    *   **Trích yếu:** Tóm tắt nội dung.
    *   **File chính thức:** Bấm vào biểu tượng tải lên để đính kèm bản scan PDF/Word đã có chữ ký.
5.  **Hoàn tất.** Nhấn nút **"Lưu"**. Nếu dữ liệu hợp lệ, hệ thống sẽ thông báo thành công và văn bản được chính thức đưa vào hệ thống với trạng thái "Đã đăng ký".

**Dành cho Developer (Dưới nền):**
1.  **Truy cập:** Việc click vào nút "Đăng ký văn bản đi" ở sidebar có chứa link trỏ tới URL pattern `dang_ky_van_ban_di_tu_danh_muc`. Gọi hàm `dang_ky_van_ban_di_tu_danh_muc_view` trong `views.py` (request GET).
2.  **Khởi tạo:** Hệ thống tự động sinh một mã `so_vb_di` tạm thời (VD: sinh bằng `predict_next_prefixed_code`) và tạo một Object `VanBanDi` giả lập ở trạng thái `CHO_DANG_KY` để truyền ra giao diện bằng một form trống (`VanBanDiDangKyForm(create_mode=True)`).
3.  **Xử lý Form (POST):** 
    *   Khi người dùng nhấn "Lưu", view này lại được gọi để nhận dữ liệu.
    *   Form được khởi tạo với cờ `create_mode=True` và được validate.
    *   Sau khi validate thành công, hệ thống gán `ngay_ban_hanh = ngày hiện tại`.
    *   Cập nhật trạng thái thành `DA_DANG_KY` (Bỏ qua bước `CHO_DUYET` hay `CHO_DANG_KY`).
    *   Xóa trống trường `ban_du_thao` (vì đây là văn bản đăng ký trực tiếp).
    *   Gọi `generate_registration_number` để sinh số thứ tự và số ký hiệu chính thức.
    *   Lưu bản ghi mới vào CSDL và lưu file đính kèm.
4.  **Kết quả:** Chuyển hướng người dùng sang trang chi tiết của chính văn bản vừa đăng ký thành công (`redirect("dang_ky_van_ban_di", so_vb_di=...)`).

## 4. Chi tiết Kỹ thuật (Dành cho Developer)

### 4.1. Models (`models.py`)
Dữ liệu được lưu trữ chủ yếu ở Model `VanBanDi`. Các trường quan trọng:
*   `so_vb_di`: Khóa chính (Primary Key), mã định danh hệ thống (Char).
*   `so_thu_tu`: Số nguyên, dùng để theo dõi thứ tự cấp số trong năm.
*   `so_ky_hieu`: Chuỗi số hiệu chính thức (VD: 12/QĐ-THPT).
*   `trang_thai_vb_di`: Sử dụng Choices (`VanBanDi.TrangThai`). Quá trình này chuyển từ `CHO_DANG_KY` -> `DA_DANG_KY`.
*   `ban_chinh_thuc`: FileField lưu trữ tệp scan cuối cùng.

**Hàm hỗ trợ quan trọng trong `models.py`:**
*   `generate_registration_number(van_ban_di_model, document)`: Logic cốt lõi để tính toán `so_thu_tu` và `so_ky_hieu` dựa trên loại văn bản, năm hiện tại và sổ đăng ký.

### 4.2. Forms (`forms.py`)
Sử dụng `VanBanDiDangKyForm` kế thừa từ `forms.ModelForm`.
*   **Trường:** Giới hạn chỉ cập nhật các trường được phép: `ngay_ky`, `so_ky_hieu`, `ma_loai_vb`, `ma_muc_do`, `nguoi_tao`, `nguoi_ky`, `noi_nhan`, `trich_yeu`.
*   **Widgets:** Định nghĩa HTML class (như `form-control`) và thuộc tính (`readonly` cho `so_ky_hieu`).
*   **Init logic:** Form có tham số `create_mode` và `editable`. Trong chế độ tạo mới (`create_mode=True`), danh sách `ma_loai_vb` được giới hạn chỉ các loại văn bản áp dụng cho văn bản đi (`OUTGOING_AP_DUNG_VALUES`).

### 4.3. Views (`views.py`)
Có 3 View chính liên quan đến quy trình này:
1.  **`dang_ky_van_ban_di_view(request, so_vb_di)`:** Xử lý đăng ký cho văn bản đã tồn tại. Xử lý phân quyền (`is_van_thu`), kiểm tra trạng thái logic (chỉ cho phép khi ở trạng thái `CHO_DANG_KY`).
2.  **`dang_ky_van_ban_di_tu_danh_muc_view(request)`:** Xử lý tạo và đăng ký ngay một văn bản mới tinh.
3.  **`cap_so_van_ban_di_view(request, so_vb_di)`:** View API (trả về JsonResponse) để phục vụ nút "Cấp số" tự động bằng Ajax trên giao diện.

### 4.4. Templates & Frontend (`van_ban_di_form.html` & JS)
*   **HTML Structure:** Chia thành các dòng (form-row) và lưới (form-grid).
*   **Conditional Rendering:** Sử dụng Jinja/Django template tags (`{% if is_new_registration %}`) để hiển thị/ẩn các trường như "File dự thảo" hoặc render form input (cho tạo mới) thay vì text readonly (cho đăng ký từ dự thảo).
*   **Tương tác động (JavaScript):** File `van_ban_di_form.js` (cần tham khảo thêm trong static) sẽ chứa logic:
    *   Lắng nghe sự kiện click nút "Cấp số" (id="btn-cap-so") -> Gọi Ajax lên `cap-so-url`.
    *   Preview tệp tin (PDF, ảnh, Word - sử dụng thư viện `mammoth.js` được include).
    *   Quản lý danh sách file đính kèm.

## 5. Hướng dẫn Kiểm thử (Dành cho QA/Tester)

Mục này liệt kê các nhóm Test Case cơ bản và nâng cao để đảm bảo chất lượng cho chức năng Đăng ký Văn bản đi.

### 5.1. Phân quyền và Bảo mật (Security & Roles)
*   **TC_SEC_01:** Đăng nhập bằng tài khoản KHÔNG phải Văn thư (VD: Giáo viên bình thường) -> Cố gắng truy cập URL `/van-ban-di/dang-ky/` hoặc `/van-ban-di/{so_vb}/dang-ky/`. KẾT QUẢ: Hệ thống báo lỗi (403/Forbidden) hoặc chuyển hướng, không cho phép truy cập.
*   **TC_SEC_02:** Đăng nhập bằng tài khoản Văn thư -> Cố gắng mở form đăng ký của một văn bản ở trạng thái `CHO_DUYET` (Chưa duyệt xong). KẾT QUẢ: Hệ thống báo lỗi hoặc chỉ hiển thị chế độ Read-only.

### 5.2. Kiểm tra Ràng buộc Dữ liệu (Validation) - Cho cả Luồng 1 & 2
*   **TC_VAL_01 (Bỏ trống bắt buộc):** Để trống một hoặc nhiều trường bắt buộc (Ngày ký, Loại văn bản, Số ký hiệu, Trích yếu...) và nhấn "Lưu". KẾT QUẢ: Hệ thống chặn lại và báo lỗi yêu cầu nhập tại chính xác các trường bị thiếu.
*   **TC_VAL_02 (Định dạng Ngày ký):** Nhập Ngày ký không hợp lệ (VD: 31/02/2024, chữ cái). KẾT QUẢ: Báo lỗi định dạng.
*   **TC_VAL_03 (Giới hạn Trích yếu):** Nhập đoạn văn bản siêu dài (VD: > 1000 ký tự) vào ô Trích yếu. KẾT QUẢ: Hệ thống cắt bớt hoặc báo lỗi vượt quá số ký tự cho phép.

### 5.3. Kiểm thử Tệp Đính kèm (File Upload)
*   **TC_FILE_01 (Happy Path):** Upload file chính thức đúng định dạng (.pdf, .docx). KẾT QUẢ: Upload thành công, có thể xem lại hoặc tải xuống.
*   **TC_FILE_02 (File quá lớn):** Upload file có dung lượng vượt mức cấu hình (VD: > 10MB). KẾT QUẢ: Báo lỗi dung lượng vượt quá mức cho phép.
*   **TC_FILE_03 (Sai định dạng):** Upload file sai định dạng (VD: .exe, .sh, .bat). KẾT QUẢ: Báo lỗi định dạng file không được hỗ trợ.

### 5.4. Kiểm thử Chức năng "Cấp Số" (Rất quan trọng)
*   **TC_CAPSO_01 (Happy Path):** Click nút "Cấp số" trên giao diện. KẾT QUẢ: Ô "Số ký hiệu" tự động được điền đúng định dạng cấu hình cho loại văn bản đó (Ví dụ: `[Số thứ tự]/[Ký hiệu loại]`).
*   **TC_CAPSO_02 (Luồng đăng ký mới):** Ở Luồng 2 (chưa có văn bản), chưa chọn Loại Văn Bản mà bấm Cấp số. KẾT QUẢ: Báo lỗi yêu cầu chọn loại văn bản trước khi cấp số.
*   **TC_CAPSO_03 (Concurrency - Xử lý đồng thời):** Tạo 2 văn bản khác nhau cùng Loại văn bản. Dùng 2 cửa sổ/máy tính mở form đăng ký của 2 văn bản đó. Bấm nút "Lưu" CÙNG MỘT LÚC (nhập tay để bỏ trống trường số ký hiệu để hệ thống tự cấp lúc Lưu). KẾT QUẢ: Hệ thống xử lý tuần tự, cấp cho văn bản 1 số N, văn bản 2 số N+1. KHÔNG được xảy ra tình trạng trùng số (Duplicate key).

### 5.5. Kiểm thử Chuyển Đổi Trạng Thái (State Transition)
*   **TC_STATE_01:** Đăng ký thành công văn bản. KẾT QUẢ: Trạng thái trên UI đổi từ "Chờ đăng ký" sang "Đã đăng ký". Các nút mới như "Phát hành bên ngoài", "Luân chuyển" xuất hiện.
*   **TC_STATE_02:** Mở lại form đăng ký của một văn bản *đã* được đăng ký thành công. KẾT QUẢ: Form chỉ hiển thị dữ liệu (Read-only), các nút Lưu/Hủy/Cấp số bị vô hiệu hóa hoặc ẩn đi.

## 6. Các Lỗi Thường Gặp & Khắc phục
*   **Không thấy nút Đăng ký / Báo lỗi không có quyền:** Kiểm tra lại User hiện tại có được cấp vai trò Văn thư trong hệ thống chưa (`is_van_thu` trả về True).
*   **Cấp số bị lỗi (Trùng số/Lỗi logic):** Kiểm tra lại hàm `generate_registration_number`. Đảm bảo hệ thống phân tách đúng số thứ tự theo từng năm và loại sổ văn bản.
*   **Văn bản ở trạng thái "Đã đăng ký" nhưng form vẫn cho sửa:** Logic trong view `dang_ky_van_ban_di_view` đã có đoạn check `editable = can_register`. Nếu lỗi xảy ra, kiểm tra lại cờ `can_register` có bị ghi đè sai không.
