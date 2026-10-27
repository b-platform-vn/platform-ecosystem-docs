# Phân loại khách hàng

| | |
|---|---|
| **Mã use case** | BPL-537 |
| **Tác giả** | B-Platform Backlog Manager |
| **Cập nhật** | 2026.10.27 |
| **Sản phẩm** | [CRM](/products/crm/README.md) |
| **Độ ưu tiên** | P1 |
| **Trạng thái** | Chờ xác nhận bộ phân loại mặc định |
| **Tracklogs** | Multica BPL-537 |

---

## 1. Tóm tắt

Sale Master cần phân loại khách hàng trong CRM BOF để nhanh chóng nhận biết nhóm khách hàng, lọc danh sách phục vụ chăm sóc và theo dõi hiệu quả bán hàng. Tính năng này bổ sung một thuộc tính **Phân loại** cho hồ sơ khách hàng; thuộc tính này độc lập với trạng thái xử lý và lịch sử tương tác của khách hàng.

Phiên bản đầu tập trung vào việc quản lý danh mục phân loại và gán thủ công một phân loại chính cho từng khách hàng. Việc tự động phân loại theo doanh thu, hành vi hoặc quy tắc từ bên ngoài chưa thuộc phạm vi phiên bản đầu.

## 2. Nhu cầu kinh doanh

Nếu tất cả khách hàng chỉ được hiển thị trong một danh sách phẳng:

- Sale Master mất nhiều thời gian tìm đúng nhóm cần chăm sóc.
- Các nhóm khách hàng có mục tiêu và cách tiếp cận khác nhau nhưng không thể phân biệt trên CRM.
- Báo cáo, lọc danh sách và các hoạt động follow-up không có cùng một ngữ cảnh phân loại.
- Việc thay đổi phân loại không có lịch sử nên khó kiểm tra trách nhiệm và lý do.

Tính năng cần hỗ trợ phân loại nhất quán nhưng không làm mất dữ liệu khách hàng hoặc ghi đè lên các thuộc tính nghiệp vụ khác.

## 3. Người dùng và quyền hạn

| Người dùng | Quyền trong phiên bản đầu |
|---|---|
| Sale Master / CS Agent nội bộ | Xem phân loại, lọc danh sách và gán phân loại nếu được cấp quyền trên khách hàng. |
| CS Agent thuê ngoài | Chỉ xem và cập nhật phân loại trong phạm vi khách hàng được phân công; không quản lý danh mục dùng chung. |
| Quản trị CRM | Tạo, đổi tên, sắp xếp, vô hiệu hóa danh mục phân loại và xem lịch sử thay đổi. |
| Hệ thống Rocket Agents | Có thể tạo khách hàng vào CRM nhưng không tự gán phân loại trong phiên bản đầu. |

Mọi kiểm tra quyền phải áp dụng theo phạm vi truy cập khách hàng hiện có của CRM. Không được dùng việc có quyền xem danh mục để suy ra quyền xem toàn bộ khách hàng.

## 4. Mục tiêu

- Cho phép CRM sử dụng một danh mục phân loại thống nhất.
- Cho phép người dùng có quyền gán hoặc thay đổi phân loại chính của một khách hàng.
- Cho phép tìm kiếm, lọc và hiển thị phân loại trong danh sách và hồ sơ khách hàng.
- Bảo toàn lịch sử thay đổi để truy vết người thực hiện, thời điểm và giá trị trước/sau.
- Giữ phân loại độc lập với trạng thái nguồn hoặc trạng thái tương tác như `Crawled`, `Verified`, `Touched` và `Engaged`.

## 5. Ngoài phạm vi phiên bản đầu

- Tự động phân loại bằng AI hoặc bằng quy tắc doanh thu, tần suất mua, điểm lead hay nội dung hội thoại.
- Tự động chuyển phân loại theo trạng thái `Crawled`, `Verified`, `Touched` hoặc `Engaged`.
- Phân loại theo nhiều tầng (nhóm, phân nhóm, cây phân cấp).
- Gán hàng loạt cho nhiều khách hàng trong một thao tác.
- Dùng phân loại để gửi chiến dịch hoặc tự động gửi tin nhắn.
- Xóa cứng lịch sử phân loại hoặc hồ sơ khách hàng.

## 6. Định nghĩa dữ liệu

### 6.1. Danh mục phân loại

Mỗi phân loại dùng chung trong CRM có tối thiểu các thuộc tính sau:

| Thuộc tính | Yêu cầu |
|---|---|
| `id` | Định danh ổn định, không tái sử dụng sau khi vô hiệu hóa. |
| `name` | Tên hiển thị, bắt buộc, không trùng trong cùng một danh mục. |
| `code` | Mã ổn định, dùng cho API và báo cáo; không thay đổi khi đổi tên. |
| `description` | Mô tả tùy chọn để người dùng hiểu tiêu chí áp dụng. |
| `sortOrder` | Thứ tự hiển thị trong bộ lọc và biểu mẫu. |
| `isActive` | Cho biết có thể gán mới hay không. |
| `createdAt`, `createdBy` | Dữ liệu truy vết tạo danh mục. |
| `updatedAt`, `updatedBy` | Dữ liệu truy vết lần cập nhật gần nhất. |

Danh mục mặc định cần được Product Owner chốt trước khi triển khai. Bộ giá trị đề xuất để thảo luận gồm: **Tiềm năng**, **Mới**, **Đang giao dịch**, **Trung thành** và **Không hoạt động**. Đây là đề xuất nghiệp vụ, không phải giá trị được tự suy luận khi chưa có quyết định chính thức.

### 6.2. Phân loại trên hồ sơ khách hàng

- Mỗi khách hàng có tối đa **một phân loại chính** trong phiên bản đầu.
- Khách hàng mới hoặc khách hàng chưa được phân loại có giá trị `Chưa phân loại`; đây là trạng thái hiển thị an toàn, không phải một bản ghi danh mục có thể xóa.
- Phân loại không thay thế `status`, nguồn khách hàng, kênh kết nối, lịch sử giao tiếp hoặc các trường thuế.
- Khi phân loại bị vô hiệu hóa, các khách hàng đang dùng phân loại đó vẫn giữ giá trị lịch sử nhưng không được gán cho khách hàng khác. Người quản trị phải chọn phân loại thay thế trước khi xóa khỏi giao diện gán.
- Không cho phép xóa cứng phân loại đã từng được sử dụng; chỉ cho phép vô hiệu hóa để bảo toàn dữ liệu lịch sử.

## 7. Yêu cầu chức năng

### 7.1. Quản lý danh mục phân loại

- **[P0]** Quản trị CRM có thể xem danh sách danh mục đang hoạt động và danh mục đã vô hiệu hóa.
- **[P0]** Quản trị CRM có thể tạo danh mục với tên, mã và mô tả tùy chọn.
- **[P0]** Hệ thống phải từ chối tên hoặc mã trùng trong cùng phạm vi danh mục, không phân biệt chữ hoa/chữ thường sau khi chuẩn hóa khoảng trắng.
- **[P0]** Quản trị CRM có thể đổi tên, cập nhật mô tả và thứ tự hiển thị của danh mục đang hoạt động.
- **[P0]** Quản trị CRM có thể vô hiệu hóa danh mục; thao tác phải yêu cầu xác nhận và lý do.
- **[P0]** Hệ thống không được cho phép xóa cứng danh mục đã được sử dụng.
- **[P0]** Hệ thống phải ghi audit log cho tạo, sửa, sắp xếp và vô hiệu hóa danh mục.
- **[P1]** Người dùng không có quyền quản trị chỉ được đọc danh mục trong phạm vi được cấp.

### 7.2. Gán phân loại cho khách hàng

- **[P0]** Người dùng có quyền cập nhật khách hàng có thể xem phân loại hiện tại trong hồ sơ khách hàng.
- **[P0]** Người dùng có quyền cập nhật khách hàng có thể gán hoặc thay đổi một phân loại chính.
- **[P0]** Chỉ được chọn danh mục đang hoạt động; API phải kiểm tra lại giá trị, không chỉ dựa vào lựa chọn trên giao diện.
- **[P0]** Khi thay đổi phân loại, hệ thống phải lưu người thực hiện, thời điểm, giá trị trước, giá trị sau và lý do nếu có.
- **[P0]** Hệ thống phải hiển thị kết quả thành công hoặc lỗi rõ ràng và không làm mất giá trị cũ khi thao tác cập nhật thất bại.
- **[P0]** Người dùng không có quyền trên khách hàng không được gán phân loại bằng cách gọi trực tiếp API.
- **[P1]** Người dùng có thể bỏ phân loại để đưa khách hàng về `Chưa phân loại`, nếu chính sách nghiệp vụ cho phép.

### 7.3. Hiển thị và lọc

- **[P0]** Danh sách khách hàng phải hiển thị phân loại chính hoặc `Chưa phân loại`.
- **[P0]** Sale Master có thể lọc danh sách theo một phân loại hoặc `Chưa phân loại`.
- **[P0]** Bộ lọc phải tôn trọng quyền truy cập khách hàng và không làm lộ số lượng của các khách hàng ngoài phạm vi.
- **[P0]** Hồ sơ khách hàng phải hiển thị tên phân loại, mô tả nếu có và thời điểm cập nhật gần nhất.
- **[P1]** Danh sách có thể sắp xếp theo tên phân loại và hỗ trợ tìm kiếm theo tên hoặc mã phân loại.
- **[P0]** Khi không có kết quả, giao diện phải hiển thị empty state phân biệt với lỗi tải dữ liệu.

### 7.4. Audit và lịch sử

- **[P0]** Lịch sử phải phân biệt ít nhất các sự kiện: tạo danh mục, thay đổi danh mục, vô hiệu hóa danh mục, gán phân loại và bỏ phân loại.
- **[P0]** Mỗi sự kiện phải có actor, timestamp UTC, đối tượng bị thay đổi, giá trị trước/sau và kết quả thao tác.
- **[P0]** Người dùng chỉ được xem lịch sử theo quyền truy cập đối tượng tương ứng.
- **[P1]** Hệ thống nên cho phép mở lịch sử phân loại từ hồ sơ khách hàng mà không rời khỏi ngữ cảnh CRM.

## 8. Luồng nghiệp vụ chính

1. Quản trị CRM mở phần **Cấu hình phân loại khách hàng**.
2. Quản trị viên tạo hoặc xác nhận các danh mục phân loại, mã, mô tả và thứ tự hiển thị.
3. Sale Master mở danh sách khách hàng và chọn bộ lọc phân loại khi cần làm việc với một nhóm.
4. Sale Master mở hồ sơ một khách hàng; hệ thống hiển thị phân loại hiện tại hoặc `Chưa phân loại`.
5. Sale Master chọn một danh mục đang hoạt động, nhập lý do nếu chính sách yêu cầu và xác nhận lưu.
6. API kiểm tra quyền, sự tồn tại và trạng thái hoạt động của danh mục, sau đó cập nhật khách hàng trong cùng một thao tác nguyên tử với audit log.
7. CRM hiển thị phân loại mới trong hồ sơ và danh sách; bộ lọc mới có hiệu lực từ lần truy vấn tiếp theo.

## 9. Luồng thay thế và lỗi

- **Chưa có danh mục hoạt động:** hiển thị hướng dẫn cho quản trị viên; khách hàng vẫn có thể tồn tại ở trạng thái `Chưa phân loại`.
- **Danh mục vừa bị vô hiệu hóa:** API từ chối thao tác gán với lỗi có thể hiểu được; người dùng phải tải lại danh sách danh mục.
- **Hai người cùng cập nhật:** áp dụng kiểm tra phiên bản hoặc điều kiện cập nhật; không ghi đè im lặng thay đổi mới hơn và phải báo người dùng thử lại.
- **Không có quyền:** trả lỗi phân quyền nhất quán, không tiết lộ khách hàng hoặc danh mục ngoài phạm vi.
- **Mất kết nối khi lưu:** giữ nguyên dữ liệu đã hiển thị, không hiển thị thành công giả và cho phép thử lại an toàn.
- **Danh mục có khách hàng đang sử dụng:** không cho xóa cứng; chỉ cho vô hiệu hóa và giữ nguyên các giá trị đã lưu.
- **Danh sách lọc rỗng:** hiển thị empty state phù hợp, không coi là lỗi hệ thống.
- **Khách hàng do Rocket Agents tạo chưa phân loại:** hiển thị `Chưa phân loại`, không tự gán dựa trên nguồn hoặc trạng thái crawler.

## 10. Tiêu chí chấp nhận

| # | Tiêu chí | Cách kiểm thử |
|---|---|---|
| 1 | Quản trị viên tạo được danh mục hợp lệ và thấy danh mục trong biểu mẫu gán. | Tạo danh mục mới, mở hồ sơ khách hàng và kiểm tra danh sách lựa chọn. |
| 2 | Hệ thống từ chối tên/mã trùng và giữ nguyên dữ liệu đã có. | Tạo hai danh mục trùng sau chuẩn hóa chữ hoa/khoảng trắng. |
| 3 | Người có quyền gán được một phân loại chính cho khách hàng. | Gán phân loại, tải lại hồ sơ và danh sách. |
| 4 | Người không có quyền không thể cập nhật bằng giao diện hoặc API. | Gọi API với tài khoản ngoài phạm vi và kiểm tra dữ liệu không đổi. |
| 5 | Bộ lọc trả đúng khách hàng trong phạm vi truy cập, bao gồm bộ lọc `Chưa phân loại`. | Kiểm tra với khách hàng ở nhiều nhóm và hai phạm vi quyền khác nhau. |
| 6 | Vô hiệu hóa danh mục không làm mất lịch sử hoặc giá trị đang có trên khách hàng. | Vô hiệu hóa danh mục đang được sử dụng, kiểm tra hồ sơ và lịch sử. |
| 7 | Lịch sử chứa actor, thời điểm UTC, trước/sau và lý do khi có. | Thực hiện tạo, gán, đổi và vô hiệu hóa; kiểm tra audit log. |
| 8 | Xung đột cập nhật không ghi đè im lặng và hiển thị kết quả nhất quán. | Cập nhật cùng khách hàng từ hai phiên đồng thời. |
| 9 | Các trạng thái `Crawled`, `Verified`, `Touched`, `Engaged` không bị thay đổi khi gán phân loại. | Gán/đổi phân loại và đối chiếu trạng thái trước/sau. |

## 11. Phụ thuộc và triển khai

- [CRM / Customers](/products/crm/features/01-customers.md) cung cấp hồ sơ khách hàng, quyền cập nhật và audit cơ bản.
- `api-service-crm` sở hữu API, kiểm tra quyền, lưu phân loại và truy vấn bộ lọc.
- `portal.b-plarform.vn` cung cấp màn hình danh sách, hồ sơ và cấu hình dành cho quản trị viên.
- Mô hình dữ liệu phải có khóa ngoại hoặc tham chiếu ổn định tới khách hàng và phân loại; thao tác cập nhật khách hàng và audit phải bảo đảm tính nhất quán.
- Cần xác nhận danh mục mặc định, quyền tạo danh mục và chính sách cho phép bỏ phân loại trước khi chuyển sang thiết kế chi tiết.

## 12. Chỉ số thành công

- Tỷ lệ hồ sơ khách hàng có phân loại hợp lệ sau khi triển khai.
- Thời gian trung bình để lọc và tìm đúng nhóm khách hàng.
- Tỷ lệ cập nhật phân loại thành công so với lỗi phân quyền hoặc dữ liệu không hợp lệ.
- Tỷ lệ thay đổi phân loại có audit log đầy đủ.
- Số lần phân loại bị thay đổi ngoài quyền hoặc bị từ chối do danh mục đã vô hiệu hóa.

## 13. Rủi ro và câu hỏi cần chốt

- Bộ phân loại mặc định và tiêu chí của từng nhóm chưa được Product Owner xác nhận; nếu không chốt, các nhóm có thể được dùng không nhất quán.
- Cần chốt phân quyền: CS Agent nội bộ nào được quản lý danh mục, và Agent thuê ngoài có được bỏ phân loại hay chỉ được thay đổi trong nhóm được phép.
- Cần chốt phân loại có bắt buộc khi tạo khách hàng mới hay cho phép `Chưa phân loại` lâu dài.
- Cần chốt có cần lưu một lịch sử phân loại độc lập với audit log tổng quát của CRM hay dùng chung một cơ chế audit.
