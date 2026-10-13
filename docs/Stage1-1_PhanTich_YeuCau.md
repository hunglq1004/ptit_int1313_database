## 1. Giới thiệu Đồ án và Phạm vi Đồ án (Introduction & Project Scope)

Phần 1 được cấu trúc theo mô hình *Software Requirements Specification* của tiêu chuẩn **ISO/IEC/IEEE 29148** (thay thế IEEE 830), như yêu cầu của tài liệu *Industrial Documentation Standards and Frameworks* của học phần: tách **yêu cầu người dùng** khỏi **năng lực hệ thống** (yêu cầu chức năng hướng dữ liệu và yêu cầu phi chức năng đặc thù CSDL), và liệt kê tường minh các **quy tắc nghiệp vụ** (mục 1.2).

Theo Elmasri & Navathe (2016, tr. 61–62), bước đầu của thiết kế CSDL là *thu thập và phân tích yêu cầu*, cho ra **yêu cầu dữ liệu** (đầu vào của thiết kế khái niệm) và **yêu cầu chức năng** – các thao tác truy xuất và cập nhật của người dùng (dùng để kiểm chứng lược đồ khái niệm). Các mục a)–i) dưới đây là kết quả của bước này.

#### a) Mục đích tài liệu

Tài liệu xác lập yêu cầu về cơ sở dữ liệu (*Database Requirement Specification*) và lược đồ khái niệm EER cho hệ thống theo dõi tài sản nghiên cứu và phòng thí nghiệm (PTN) của một trường đại học. Người đọc: giảng viên hướng dẫn, thành viên nhóm, và người cài đặt CSDL/ứng dụng ở các giai đoạn sau.

#### b) Bối cảnh – mô tả "thế giới thu nhỏ" (miniworld)

Trường đại học có nhiều **phòng thí nghiệm**; mỗi PTN có mã, tên, vị trí (tòa nhà, phòng), **cấp an toàn** và do **một giảng viên làm trưởng PTN**.

Người sử dụng PTN là **giảng viên** và **sinh viên**; một người có thể vừa là giảng viên vừa là sinh viên (ví dụ giảng viên đang làm nghiên cứu sinh tại trường). Mỗi người có một **thẻ ra vào**. Trưởng PTN cấp **quyền ra vào** PTN cho từng người trong một khoảng thời gian; mỗi lần quẹt thẻ vào PTN được ghi vào **nhật ký ra vào** (kể cả lượt bị từ chối).

Nhà trường cấp các **chứng chỉ an toàn** (ví dụ: xử lý hóa chất độc, hóa chất dễ cháy) có thời hạn hiệu lực và có thể bị thu hồi. **Hóa chất nguy hiểm** yêu cầu người sử dụng phải có một loại chứng chỉ tương ứng.

Các **đề tài/khoản tài trợ nghiên cứu** có một **chủ nhiệm** là giảng viên, có cơ quan tài trợ, thời gian thực hiện, ngân sách và nhiều **thành viên**. Kinh phí đề tài được dùng để mua **tài sản**, gồm hai loại: **thiết bị PTN** và **chai/lọ hóa chất**. Mỗi tài sản đặt tại một PTN. Mỗi chai chứa một **hóa chất**; mỗi lần lấy hóa chất ra dùng đều được ghi nhận (ai, khi nào, bao nhiêu) để tính **lượng tồn**.

**Thách thức thiết kế** (đề tài 4 trong *Đồ án Môn học*): thiết lập ràng buộc toàn vẹn nghiêm ngặt – **chỉ nhân sự được cấp chứng chỉ mới được tiếp cận vật liệu nguy hiểm** – và cưỡng chế bằng **SQL Trigger** ngay tại tầng CSDL.

Bảng dưới cho thấy mỗi ý trong bối cảnh của đề bài được đáp ứng bởi thành phần nào của mô hình:

| Bối cảnh trong đề bài | Thành phần mô hình (mục 2.1) |
|---|---|
| Theo dõi các khoản tài trợ nghiên cứu | RESEARCH_GRANT, LEADS, PARTICIPATES_IN, FUNDS |
| Thiết bị phòng thí nghiệm | ASSET → EQUIPMENT, LOCATED_IN |
| Kho hóa chất | ASSET → CHEMICAL_CONTAINER, CHEMICAL, CONTAINS, CHEMICAL_USAGE |
| Nhật ký ra vào của sinh viên/giảng viên | PERSON → {FACULTY, STUDENT}, AUTHORIZED_FOR, ACCESS_LOG |
| Chỉ nhân sự có chứng chỉ mới tiếp cận vật liệu nguy hiểm (Trigger) | CERTIFICATE, CERTIFICATION_TYPE, REQUIRES + trigger TR-01 |

#### c) Phạm vi hệ thống

| Trong phạm vi (In scope) | Ngoài phạm vi (Out of scope) |
|---|---|
| Hồ sơ tối thiểu của giảng viên và sinh viên | Quản lý đào tạo, nhân sự – tiền lương; kỹ thuật viên, khách ngoài trường |
| Loại chứng chỉ an toàn; cấp và thu hồi chứng chỉ | Tổ chức đào tạo, sát hạch an toàn |
| PTN, quyền ra vào, nhật ký ra vào | Điều khiển phần cứng khóa cửa (hệ thống chỉ *nhận* sự kiện quẹt thẻ) |
| Đề tài, chủ nhiệm, thành viên, cơ quan tài trợ | Kế toán chi tiết (giải ngân, hóa đơn), quy trình mua sắm |
| Tài sản: thiết bị và chai hóa chất, nguồn kinh phí, vị trí PTN | Lịch đặt thiết bị, lịch sử bảo trì; quản lý tủ/kệ trong PTN |
| Danh mục hóa chất, mức nguy hại, ghi nhận sử dụng và tồn kho | Xử lý chất thải, sinh phẩm, động vật thí nghiệm |

#### d) Thuật ngữ và viết tắt

| Thuật ngữ | Giải thích |
|---|---|
| PTN | Phòng thí nghiệm (LABORATORY) |
| Chủ nhiệm đề tài (PI) | *Principal Investigator* – giảng viên chịu trách nhiệm chính về một đề tài |
| Loại chứng chỉ / Chứng chỉ | Loại đào tạo an toàn (CERTIFICATION_TYPE) / văn bằng cụ thể cấp cho một người (CERTIFICATE) |
| Chứng chỉ còn hiệu lực tại *t* | Trạng thái `ACTIVE` và Issue_date ≤ *t* ≤ Expiry_date |
| Hóa chất nguy hiểm | Hóa chất có Hazard_class khác `NONE` (dễ cháy, độc, ăn mòn…) |
| Chai/lọ hóa chất | Một đơn vị chứa vật lý của một hóa chất, quản lý như một tài sản (CHEMICAL_CONTAINER) |
| Số CAS | Mã định danh duy nhất của một chất hóa học |
| Quyền ra vào | Sự cho phép một người vào một PTN trong khoảng [Valid_from, Valid_to] |
| Ràng buộc ngữ nghĩa | Ràng buộc không biểu diễn được trực tiếp trong lược đồ, cần trigger/assertion (Elmasri & Navathe, 2016, tr. 157–158, 165) |
| ECA | *Event – Condition – Action*, cấu trúc của một trigger (Elmasri & Navathe, 2016, tr. 227) |

#### e) Nhóm người dùng (User classes)

| Mã | Nhóm người dùng | Tương tác chính với CSDL |
|---|---|---|
| U1 | Quản trị CSDL (DBA) | Tạo hồ sơ người dùng, phân quyền, sao lưu |
| U2 | Cán bộ Phòng Quản lý Khoa học | Quản lý đề tài, thành viên; báo cáo tài sản theo đề tài |
| U3 | Cán bộ An toàn PTN | Quản lý loại chứng chỉ, cấp/thu hồi chứng chỉ, khai báo mức nguy hại của hóa chất |
| U4 | Trưởng PTN (giảng viên) | Cấp/thu hồi quyền ra vào; nhập tài sản và theo dõi tồn kho của PTN mình |
| U5 | Giảng viên, sinh viên | Ghi nhận sử dụng hóa chất; xem chứng chỉ, quyền ra vào của bản thân |
| U6 | Hệ thống đầu đọc thẻ | Gửi sự kiện vào/ra; nhận kết quả cho phép/từ chối |

#### f) Yêu cầu người dùng (User Requirements)

| Mã | Bên liên quan | Nhu cầu |
|---|---|---|
| UR-01 | U3 | Chỉ người có **chứng chỉ còn hiệu lực** mới được sử dụng hóa chất nguy hiểm – kể cả khi dữ liệu được nhập từ ứng dụng khác. |
| UR-02 | U4, U6 | Kiểm soát ai được vào PTN, biết **ai đang có mặt** và xem lại lịch sử ra vào. |
| UR-03 | U4 | Biết chính xác **lượng tồn** từng chai hóa chất, hạn dùng và tình trạng thiết bị của PTN. |
| UR-04 | U2 | Theo dõi đề tài, thành viên và **tài sản mua từ kinh phí đề tài**. |
| UR-05 | U3, U4 | **Truy vết** không thể chỉnh sửa: ai đã dùng hóa chất gì, khi nào, bao nhiêu; ai đã vào PTN nào. |

#### g) Yêu cầu chức năng (Functional Requirements, hướng dữ liệu)

| Mã | Hệ thống phải cho phép … | Loại | Truy vết |
|---|---|---|---|
| FR-01 | DBA tạo/cập nhật hồ sơ người dùng và gán vai trò giảng viên và/hoặc sinh viên. | Cập nhật | UR-01 |
| FR-02 | Cán bộ an toàn quản lý loại chứng chỉ; cấp và thu hồi chứng chỉ cho một người. | Cập nhật | UR-01 |
| FR-03 | Cán bộ an toàn quản lý danh mục hóa chất: mức nguy hại và loại chứng chỉ yêu cầu. | Cập nhật | UR-01 |
| FR-04 | Trưởng PTN cấp, gia hạn, thu hồi quyền ra vào PTN cho một người. | Cập nhật | UR-02 |
| FR-05 | Hệ thống đầu đọc thẻ ghi một lượt vào PTN và nhận ngay kết quả `GRANTED`/`DENIED`; ghi thời điểm ra. | Cập nhật | UR-02, UR-05 |
| FR-06 | Phòng QLKH tạo/cập nhật đề tài, chủ nhiệm, thành viên. | Cập nhật | UR-04 |
| FR-07 | Trưởng PTN ghi nhận tài sản mới (thiết bị hoặc chai hóa chất), PTN đặt tài sản, đề tài cấp kinh phí; cập nhật trạng thái tài sản. | Cập nhật | UR-03, UR-04 |
| FR-08 | Giảng viên/sinh viên ghi nhận lượng hóa chất đã lấy từ một chai; lượng tồn được cập nhật tự động. | Cập nhật | UR-01, UR-03 |
| FR-09 | Truy xuất danh sách người **đang có mặt** trong một PTN; lịch sử ra vào theo người/PTN (kể cả lượt bị từ chối). | Truy xuất | UR-02, UR-05 |
| FR-10 | Báo cáo tồn kho hóa chất theo PTN; danh sách chai sắp hết hạn trong *n* ngày. | Truy xuất | UR-03 |
| FR-11 | Báo cáo tài sản và tổng giá trị tài sản theo đề tài, so với ngân sách. | Truy xuất | UR-04 |
| FR-12 | Truy vết lịch sử sử dụng hóa chất nguy hiểm theo hóa chất/người/khoảng thời gian. | Truy xuất | UR-05 |
| FR-13 | Người dùng xem chứng chỉ, quyền ra vào và đề tài của chính mình. | Truy xuất | UR-01 |

#### h) Yêu cầu phi chức năng (Non-Functional Requirements, đặc thù CSDL)

| Mã | Nhóm | Yêu cầu | Tiêu chí kiểm chứng |
|---|---|---|---|
| NFR-01 | Toàn vẹn | Các quy tắc ở mục 1.2 được cưỡng chế **tại tầng CSDL** (khóa, `CHECK`, trigger), không chỉ ở ứng dụng. | Mỗi quy tắc có ca kiểm thử chèn dữ liệu sai bị từ chối (mục 5.1) |
| NFR-02 | Bảo mật | Phân quyền theo vai trò (RBAC) bằng `GRANT`/`REVOKE`; dữ liệu cá nhân (email, số điện thoại, mã thẻ) được mã hóa khi lưu trữ. | Kịch bản kiểm thử quyền (mục 5.2) |
| NFR-03 | Kiểm toán | Nhật ký ra vào và nhật ký sử dụng hóa chất là **chỉ-thêm** (không sửa, không xóa). | `UPDATE`/`DELETE` trái phép bị từ chối |
| NFR-04 | Hiệu năng | Ghi một lượt quẹt thẻ (kèm trigger kiểm tra) và truy vấn "ai đang có mặt" hoàn tất **dưới 200 ms**. | Đo trên dữ liệu thử ≥ 100 000 lượt ra vào |
| NFR-05 | Đồng thời | Hai người lấy cùng một chai hóa chất cùng lúc không làm lượng tồn âm (giao dịch ACID). | Kiểm thử giao dịch song song |
| NFR-06 | Chất lượng thiết kế | Lược đồ quan hệ đạt 3NF/BCNF; script SQL theo SQLStyle.guide (`snake_case`, từ khóa viết hoa). | Mục 2.3, 4.1 |

#### i) Giả định

1. Mỗi người có **một** mã định danh toàn trường và **một** thẻ ra vào; người vừa là giảng viên vừa là sinh viên vẫn chỉ là một PERSON.
2. Các tài khoản quản trị (DBA, Phòng QLKH, cán bộ an toàn) là **vai trò người dùng CSDL** (RBAC), không phải đối tượng dữ liệu được theo dõi.
3. Mỗi cặp (người, PTN) chỉ có **một** quyền ra vào hiện hành; gia hạn = cập nhật Valid_to.
4. Mỗi hóa chất nguy hiểm yêu cầu **đúng một** loại chứng chỉ; hóa chất không nguy hiểm không yêu cầu chứng chỉ.
5. Tài sản được quản lý vị trí **ở mức PTN**; lượng hóa chất trong một chai đo bằng **một** đơn vị duy nhất.
6. Đồng hồ đầu đọc thẻ và máy chủ CSDL được đồng bộ; DBMS dùng ở Giai đoạn 3 hỗ trợ trigger mức dòng và `CHECK`.

---

### 1.1 Mục tiêu Hệ thống (System Objectives)

| Mã | Mục tiêu | Tiêu chí thành công | Truy vết |
|---|---|---|---|
| SO-01 | **Cưỡng chế an toàn tại tầng CSDL**: không ghi nhận được việc sử dụng hóa chất nguy hiểm của người thiếu chứng chỉ còn hiệu lực. | 100 % ca kiểm thử vi phạm (thiếu chứng chỉ, chứng chỉ hết hạn/bị thu hồi, không có quyền vào PTN) bị trigger từ chối, kể cả khi chèn trực tiếp bằng SQL. | UR-01; BR-21, BR-22 |
| SO-02 | **Kiểm soát và truy vết ra vào**: mọi lượt quẹt thẻ được ghi nhận và đánh giá tự động; biết ngay ai đang có mặt trong PTN. | Lượt vào không có quyền hiệu lực luôn có kết quả `DENIED`; truy vấn "đang có mặt" < 200 ms. | UR-02, UR-05; BR-08 – BR-10 |
| SO-03 | **Tồn kho hóa chất chính xác**: lượng tồn của mọi chai luôn bằng lượng ban đầu trừ tổng lượng đã dùng và không âm. | Truy vấn đối soát cho 0 sai lệch. | UR-03; BR-23 |
| SO-04 | **Quản lý tài sản theo nguồn kinh phí**: mỗi tài sản truy về đúng PTN và đề tài đã cấp kinh phí (nếu có). | Báo cáo tài sản theo đề tài khớp dữ liệu. | UR-04; BR-13 – BR-15 |
| SO-05 | **Thiết kế đúng phương pháp**: mô hình EER thể hiện đủ quy tắc nghiệp vụ; ánh xạ đúng quy tắc Elmasri; đạt 3NF/BCNF. | Đáp ứng tiêu chí *Độ chính xác của mô hình hóa dữ liệu* và *Chuẩn hóa & tối ưu hóa*. | NFR-06; mục 2 |

---

### 1.2 Các quy tắc và Ràng buộc Nghiệp vụ (Business Rules & Constraints)

Các quy tắc trong thế giới thu nhỏ quyết định ràng buộc của lược đồ được gọi là **quy tắc nghiệp vụ** (Elmasri & Navathe, 2016, tr. 21–22, 80). Theo Elmasri & Navathe (2016, tr. 157–158), ràng buộc gồm ba loại: (1) **vốn có của mô hình**; (2) **dựa trên lược đồ** – khai báo được bằng DDL (khóa, `NOT NULL`, khóa ngoại, `CHECK`); (3) **ràng buộc ngữ nghĩa** – không biểu diễn trực tiếp được trong lược đồ, phải dùng **trigger/assertion** hoặc ứng dụng. Ngoài ràng buộc trạng thái còn có **ràng buộc chuyển trạng thái**, thường cưỡng chế bằng trigger (tr. 165).

Cột **Cơ chế**: **EER** – thể hiện bằng cấu trúc lược đồ khái niệm (sẽ thành PK/FK/`NOT NULL`/`UNIQUE`); **CHECK** – ràng buộc miền giá trị hoặc trên một bộ; **Trigger** – ràng buộc ngữ nghĩa nhiều bảng; **RBAC** – cưỡng chế bằng `GRANT`/`REVOKE`. Dấu ⚠️ đánh dấu thách thức chính của đề tài.

#### A. Người dùng và chứng chỉ an toàn

| Mã | Quy tắc nghiệp vụ | Loại ràng buộc | Cơ chế | Thành phần mô hình |
|---|---|---|---|---|
| BR-01 | Mỗi người có **Person_id** duy nhất; **Email** và **Card_no** (mã thẻ) cũng duy nhất; Account_status ∈ {ACTIVE, INACTIVE}. | Khóa; miền | EER + CHECK | PERSON |
| BR-02 | Mỗi người là **giảng viên, sinh viên, hoặc cả hai**; không có người nào ngoài hai nhóm này. | Chuyên biệt hóa chồng lấn, toàn phần | EER | S1: PERSON → {FACULTY, STUDENT} |
| BR-03 | Program_level của sinh viên ∈ {UNDERGRAD, MASTER, PHD}. | Miền | CHECK | STUDENT |
| BR-04 | Mỗi chứng chỉ có **số hiệu duy nhất**, thuộc **đúng một** người và **đúng một** loại chứng chỉ; **Expiry_date > Issue_date**; Cert_status ∈ {ACTIVE, REVOKED}. | Khóa; tham gia toàn phần; trên bộ | EER + CHECK | CERTIFICATE, HOLDS, OF_TYPE |
| BR-05 | Chứng chỉ **còn hiệu lực** tại *t* ⇔ Cert_status = ACTIVE và Issue_date ≤ *t* ≤ Expiry_date. Thu hồi chứng chỉ không xóa bản ghi. | Thuộc tính suy dẫn | EER (Is_valid) | CERTIFICATE |

#### B. Phòng thí nghiệm và nhật ký ra vào

| Mã | Quy tắc nghiệp vụ | Loại ràng buộc | Cơ chế | Thành phần mô hình |
|---|---|---|---|---|
| BR-06 | Mỗi PTN có mã duy nhất, **đúng một** giảng viên làm trưởng PTN; một giảng viên có thể phụ trách nhiều PTN; Safety_level ∈ {1, 2, 3, 4}. | Khóa; 1:N toàn phần; miền | EER + CHECK | LABORATORY, DIRECTS |
| BR-07 | Quyền ra vào được cấp cho từng cặp (người, PTN) với **Valid_from ≤ Valid_to**; chỉ trưởng PTN của PTN đó được cấp/thu hồi. | M:N có thuộc tính; trên bộ; phân quyền | EER + CHECK + RBAC | AUTHORIZED_FOR |
| BR-08 ⚠️ | Mỗi lượt quẹt thẻ vào PTN tạo một bản ghi nhật ký. **Access_result = GRANTED** chỉ khi tại Entry_time người đó có **quyền ra vào còn hiệu lực** với PTN và tài khoản ACTIVE; ngược lại hệ thống ghi **DENIED**. | Ngữ nghĩa (nhiều bảng) | Trigger | ACCESS_LOG, AUTHORIZED_FOR, PERSON |
| BR-09 | Exit_time (nếu có) **> Entry_time**; lượt DENIED không có Exit_time; Access_result ∈ {GRANTED, DENIED}. | Trên bộ; miền | CHECK | ACCESS_LOG |
| BR-10 | Nhật ký ra vào **chỉ được thêm**; chỉ tài khoản của hệ thống đầu đọc thẻ được ghi Exit_time; không ai được xóa. | Kiểm toán | RBAC | ACCESS_LOG |

#### C. Đề tài nghiên cứu và tài sản

| Mã | Quy tắc nghiệp vụ | Loại ràng buộc | Cơ chế | Thành phần mô hình |
|---|---|---|---|---|
| BR-11 | Mỗi đề tài có **mã số duy nhất** và **đúng một** chủ nhiệm là **giảng viên**; Start_date < End_date; Total_budget > 0. | Khóa; 1:N toàn phần; trên bộ | EER + CHECK | RESEARCH_GRANT, LEADS |
| BR-12 | Một người có thể tham gia nhiều đề tài, một đề tài có nhiều thành viên; mỗi tư cách thành viên có một vai trò (Project_role). | M:N có thuộc tính | EER | PARTICIPATES_IN |
| BR-13 | Mỗi tài sản là **thiết bị hoặc chai hóa chất**, không thể là cả hai và không có loại khác (Asset_type). | Tổng quát hóa rời rạc, toàn phần | EER (+ CHECK) | G1: {EQUIPMENT, CHEMICAL_CONTAINER} → ASSET |
| BR-14 | Mỗi tài sản đặt tại **đúng một** PTN; được mua bằng kinh phí của **tối đa một** đề tài; Purchase_cost ≥ 0. | N:1 toàn phần; N:1 một phần; miền | EER + CHECK | LOCATED_IN, FUNDS |
| BR-15 | *(Mở rộng)* Tài sản mua bằng kinh phí đề tài phải có Acquisition_date trong [Start_date, End_date] của đề tài, và tổng Purchase_cost các tài sản của đề tài **≤ Total_budget**. | Ngữ nghĩa (tổng hợp, hai bảng) | Trigger | ASSET, FUNDS, RESEARCH_GRANT |

#### D. Thiết bị, hóa chất và vật liệu nguy hiểm

| Mã | Quy tắc nghiệp vụ | Loại ràng buộc | Cơ chế | Thành phần mô hình |
|---|---|---|---|---|
| BR-16 | Equipment_status ∈ {OPERATIONAL, UNDER_MAINTENANCE, RETIRED}. | Miền | CHECK | EQUIPMENT |
| BR-17 | Mỗi hóa chất có **số CAS** duy nhất; Hazard_class ∈ {NONE, FLAMMABLE, TOXIC, CORROSIVE, OXIDIZING, CARCINOGENIC}. | Khóa; miền | EER + CHECK | CHEMICAL |
| BR-18 | Hóa chất **nguy hiểm** (Hazard_class ≠ NONE) phải yêu cầu **đúng một** loại chứng chỉ; hóa chất NONE không yêu cầu chứng chỉ. | N:1 một phần + ràng buộc trên bộ | EER + CHECK (sau ánh xạ) | CHEMICAL, REQUIRES |
| BR-19 | Mỗi chai chứa **đúng một** hóa chất; Initial_quantity > 0; Unit ∈ {g, kg, mL, L}; Container_status ∈ {SEALED, OPENED, EMPTY, DISPOSED}. | N:1 toàn phần; miền | EER + CHECK | CHEMICAL_CONTAINER, CONTAINS |
| BR-20 | Mỗi lần sử dụng được xác định bởi **chai + Usage_time**; Quantity_used > 0; ghi nhận **người sử dụng**. | Thực thể yếu; miền | EER + CHECK | CHEMICAL_USAGE, USAGE_OF, USED_BY |
| BR-21 ⚠️ | **Chỉ nhân sự có chứng chỉ mới được tiếp cận vật liệu nguy hiểm**: một lần sử dụng của người *p* với chai chứa hóa chất *c* tại thời điểm *t* chỉ hợp lệ nếu *c* không yêu cầu chứng chỉ, **hoặc** *p* có **ít nhất một** chứng chỉ thuộc loại mà *c* yêu cầu và còn hiệu lực tại *t*. | Ngữ nghĩa (4 bảng) | Trigger | CHEMICAL_USAGE, CONTAINS, REQUIRES, CERTIFICATE |
| BR-22 ⚠️ | Người sử dụng phải có **quyền ra vào còn hiệu lực** tại *t* đối với PTN đặt chai hóa chất. | Ngữ nghĩa (nhiều bảng) | Trigger | CHEMICAL_USAGE, LOCATED_IN, AUTHORIZED_FOR |
| BR-23 | **Current_quantity = Initial_quantity − Σ Quantity_used**, luôn ≥ 0: lượng dùng không vượt lượng tồn; chỉ dùng chai SEALED/OPENED chưa quá Expiry_date. Sau khi ghi nhận: lần dùng đầu ⇒ OPENED; tồn = 0 ⇒ EMPTY. | Suy dẫn + ngữ nghĩa + hành động tự động | Trigger | CHEMICAL_CONTAINER, CHEMICAL_USAGE |

#### E. Đặc tả trigger ở mức khái niệm (ECA)

Theo Elmasri & Navathe (2016, tr. 226–227), một trigger gồm **sự kiện** (thao tác cập nhật kích hoạt), **điều kiện** và **hành động**. Giai đoạn 3 yêu cầu tối thiểu 2 trigger/view; nhóm cài đặt **TR-01 – TR-03** (bắt buộc), TR-04 làm thêm nếu còn thời gian.

| Mã | Quy tắc | Sự kiện | Điều kiện | Hành động |
|---|---|---|---|---|
| TR-01 ⚠️ | BR-21, BR-22, BR-23 (kiểm tra) | Trước khi chèn CHEMICAL_USAGE | Hóa chất trong chai yêu cầu loại chứng chỉ *k* mà người dùng **không có** chứng chỉ loại *k* còn hiệu lực tại Usage_time; **hoặc** không có quyền vào PTN đặt chai; **hoặc** lượng dùng vượt tồn / chai hết hạn / sai trạng thái | Từ chối thao tác, báo lỗi nêu rõ lý do |
| TR-02 | BR-23 (cập nhật) | Sau khi chèn CHEMICAL_USAGE | Luôn | Trừ lượng tồn; SEALED → OPENED ở lần dùng đầu; tồn = 0 → EMPTY |
| TR-03 ⚠️ | BR-08 | Trước khi chèn ACCESS_LOG | Không có quyền ra vào hiệu lực tại Entry_time, hoặc tài khoản không ACTIVE | Đặt Access_result = DENIED; ngược lại GRANTED |
| TR-04 | BR-15 *(mở rộng)* | Trước khi chèn/cập nhật ASSET có đề tài | Ngày mua ngoài thời hạn đề tài, hoặc tổng giá trị vượt ngân sách | Từ chối thao tác |

> [!note] Ghi chú thiết kế
> Điều kiện chứng chỉ và quyền ra vào được kiểm tra **tại thời điểm xảy ra sự kiện**. Vì vậy khi chứng chỉ bị thu hồi hoặc hết hạn, không cần xóa dây chuyền dữ liệu nào: lần sử dụng kế tiếp sẽ tự bị từ chối, còn lịch sử cũ được giữ nguyên để truy vết.

