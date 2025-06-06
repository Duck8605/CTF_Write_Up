# Applying Homomorphic Encryption in Finance Service (Credit Scoring)

## 1. Bối cảnh
Trong lĩnh vực tài chính, Ngân hàng A cần xử lý dữ liệu nhạy cảm của khách hàng để tính điểm tín dụng. Để tận dụng chuyên môn bên ngoài, ngân hàng hợp tác với Fintech Partner. Tuy nhiên, việc chia sẻ dữ liệu thô gây rủi ro bảo mật và vi phạm quyền riêng tư. Hệ thống này giải quyết bài toán cho phép Fintech Partner tính toán trên dữ liệu mã hóa mà không biết dữ liệu gốc, nhờ dịch vụ mã hóa đồng cấu (Homomorphic Encryption - HE) chuyên biệt, có xác thực truy cập.

## 2. Phương pháp truyền thống
Các giải pháp mã hóa truyền thống chỉ bảo vệ dữ liệu khi lưu trữ/truyền tải, nhưng vẫn phải giải mã khi xử lý tại Fintech Partner, làm mất tính riêng tư. Không thể thực hiện các phép toán phức tạp trực tiếp trên dữ liệu mã hóa.

## 3. Đề xuất giải pháp mới
Chúng tôi đề xuất xây dựng một "Hệ thống Chấm điểm Tín dụng Bảo toàn Quyền riêng tư" sử dụng kỹ thuật Mã hóa Đồng cấu Hoàn toàn (Fully Homomorphic Encryption - FHE), được hỗ trợ bởi một thư viện mã hóa đồng cấu chuyên biệt dưới dạng file `.pyd` (HE Service). Thay vì triển khai một dịch vụ HTTP, HE Service được đóng gói thành file `he_crypto.cp313-win_amd64.pyd` để các hệ thống khác (Bank System, Fintech Partner) có thể import và sử dụng trực tiếp các hàm mã hóa/giải mã.

Luồng hoạt động chính của hệ thống:

- **Bank System** import module `.pyd` (`he_crypto`) để sinh khóa, mã hóa, giải mã dữ liệu.
- **Fintech Partner** chỉ nhận ciphertext và public key, thực hiện tính toán trên ciphertext.
- **Không có service HTTP trung gian**: Việc xác thực, sinh khóa, mã hóa, giải mã đều thực hiện qua các hàm Python trong `.pyd`.

### 3.1. Kiến trúc hệ thống
Hệ thống được thiết kế với ba thành phần chính:

**Bank System (Hệ thống Ngân hàng):**
- Sở hữu một cặp khóa định danh (Bank_Identity_SK, Bank_Identity_PK).
- Quản lý và bảo vệ HE Secret Key (HE_SK).
- Tương tác với SQL Server để quản lý dữ liệu khách hàng.
- Import và sử dụng trực tiếp các hàm của `he_crypto.pyd` để sinh khóa, mã hóa, giải mã. Giao tiếp với Fintech Partner.

**Fintech Partner System (Hệ thống Đối tác Fintech - Mô phỏng):**
- Nhận HE Public Key (HE_PK) và ciphertext từ Bank System.
- Thực hiện tính toán điểm tín dụng trên ciphertext.
- Không bao giờ có quyền truy cập vào HE_SK hoặc dữ liệu gốc.

**HE Service (Thư viện Mã hóa Đồng cấu):**
- Được đóng gói thành file `.pyd` (`he_crypto.cp313-win_amd64.pyd`). Không còn là một service HTTP.
- Cung cấp các hàm: generate_keys, encrypt_data, decrypt_data.
- Không lưu trữ HE_SK của Bank System. HE_SK do Bank System quản lý và sử dụng khi cần giải mã.

### 3.2. Kiểm soát truy cập và Bảo mật
- Bank System giữ toàn quyền kiểm soát HE_SK. Fintech Partner chỉ có HE_PK.
- Mã hóa Đồng cấu Hoàn toàn (FHE) với TenSEAL: Sử dụng thư viện TenSEAL với lược đồ CKKS.

## 4. Triển khai Hệ thống Thực tế (Proof of Concept - PoC)
Đồ án sẽ tập trung vào việc xây dựng một Proof-of-Concept (PoC) với các thành phần sau:

**Bank System (Triển khai Backend):**
- Framework: FastAPI (Python).
- Database: SQL Server (sử dụng SQLAlchemy).
- Import trực tiếp thư viện `he_crypto.pyd` để sinh khóa, mã hóa, giải mã.
- Thư viện: TenSEAL (quản lý cấu trúc khóa/ciphertext HE), PyJWT, thư viện cho khóa bất đối xứng (ví dụ: cryptography).

**Fintech Partner System (Mô phỏng Backend):**
- Framework: FastAPI (Python).
- Tính toán HE: Sử dụng TenSEAL và HE_PK.

**HE Service (Thư viện):**
- Được đóng gói thành `.pyd`, không cần deploy service riêng.

## 5. Công nghệ sử dụng
| Thành phần      | Công nghệ chính                      | Mục đích                                                                        |
| --------------- | ------------------------------------ | ------------------------------------------------------------------------------- |
| Bank System     | Python, FastAPI, SQLAlchemy, TenSEAL | Xây dựng API backend, quản lý dữ liệu, mã hóa/giải mã, import he_crypto.pyd     |
| Fintech Partner | Python, FastAPI, TenSEAL             | Nhận ciphertext, tính toán trên ciphertext, không có HE_SK                      |
| HE Service      | TenSEAL, Python                      | Cung cấp các hàm mã hóa, giải mã, sinh khóa chuyên biệt dưới dạng thư viện .pyd |

## 6. Mục tiêu và các bước thực hiện
- Nghiên cứu FHE (TenSEAL/CKKS).
- Thiết kế, triển khai PoC kiến trúc 3 thành phần.
- Chứng minh luồng: lấy dữ liệu, mã hóa, gửi đi tính toán, nhận lại, giải mã.
- Đánh giá hiệu năng.

**Các bước:**
1. Thiết lập môi trường, cài đặt thư viện.
2. Bank System import `he_crypto.pyd`, sinh khóa, mã hóa, giải mã.
3. Kết nối SQL Server, lấy dữ liệu, mã hóa, gửi ciphertext cho Fintech Partner.
4. Fintech Partner nhận ciphertext, tính toán, trả kết quả.
5. Bank System giải mã kết quả.
6. Kiểm thử toàn bộ luồng.

## 7. Khó khăn và thách thức
- Độ phức tạp FHE (tham số, noise, độ chính xác).
- Quản lý HE_SK an toàn.
- Hiệu năng FHE.
- Quản lý vòng đời khóa.
- Tích hợp SQL Server.

## 8. Hướng phát triển tương lai
- Tối ưu hiệu suất HE Service.
- Nâng cao quản lý HE_SK.
- Mô hình tính điểm phức tạp hơn.
- Giám sát, logging, alerting.
- Đăng ký/quản lý Bank_Identity_PK động cho HE Service.

## 9. Hướng dẫn sử dụng he_crypto.pyd
### 9.1. Chuẩn bị
- Đảm bảo file `he_crypto.cp313-win_amd64.pyd` nằm trong thư mục `HE_service`.
- Cài Python 3.13 64-bit (Windows).

### 9.2. Import và sử dụng
- Thêm thư mục chứa `.pyd` vào PYTHONPATH:

```powershell
$env:PYTHONPATH="$PWD\HE_service"; python .\bank_system\app\infrastructure\test_core_pyd.py
```

- Trong code Python:
```python
import he_crypto

# Sinh khóa
keys = he_crypto.generate_keys()
public_context = keys["public_context"]
secret_context = keys["secret_context"]

# Mã hóa
data = [800.0, 1.0, 2.0, 3.0]
encrypted = he_crypto.encrypt_data(public_context, data)

# Giải mã
decrypted = he_crypto.decrypt_data(secret_context, encrypted)
print("Kết quả giải mã:", decrypted)
```

- Kết quả sẽ được ghi vào file `bank_system/test_core_pyd_result.txt` nếu dùng script mẫu.

---
Nếu cần hỗ trợ thêm, vui lòng liên hệ đội phát triển.
