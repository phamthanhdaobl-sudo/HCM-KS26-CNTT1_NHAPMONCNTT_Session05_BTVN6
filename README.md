# BÀI TẬP: LỌC LOG VÀ TẠO BÁO CÁO LỖI BẰNG POWERSHELL

## 1. Mục tiêu

- Tìm kiếm các file `.log` trong thư mục hiện tại và toàn bộ thư mục con.
- Lọc các dòng chứa từ khóa `ERROR`.
- Trích xuất đường dẫn file, số dòng và nội dung lỗi.
- Ghi kết quả vào `bao_cao_loi.txt` mà không làm mất dữ liệu đã có.

## 2. Các câu lệnh thực hiện

### Bước 1: Tìm tất cả file `.log`

```powershell
Get-ChildItem -Path . -Filter *.log -File -Recurse
```

### Bước 2: Lọc lỗi và ghi nối vào báo cáo

```powershell
Get-ChildItem -Path . -Filter *.log -File -Recurse |
    Select-String -Pattern 'ERROR' |
    ForEach-Object {
        "$($_.Path):$($_.LineNumber): $($_.Line)"
    } |
    Add-Content -Path .\bao_cao_loi.txt -Encoding utf8
```

## 3. Giải thích câu lệnh

| Thành phần | Chức năng |
|---|---|
| `Get-ChildItem` | Liệt kê các file và thư mục. |
| `-Path .` | Bắt đầu tìm kiếm từ thư mục hiện tại. |
| `-Filter *.log` | Chỉ tìm các file có tên kết thúc bằng `.log`. |
| `-File` | Chỉ lấy file, không lấy thư mục. |
| `-Recurse` | Tìm kiếm cả trong các thư mục con. |
| `|` | Chuyển kết quả từ lệnh trước sang lệnh sau. |
| `Select-String -Pattern 'ERROR'` | Tìm những dòng chứa từ khóa `ERROR`. |
| `$($_.Path)` | Lấy đường dẫn file chứa dòng lỗi. |
| `$($_.LineNumber)` | Lấy số thứ tự của dòng lỗi. |
| `$($_.Line)` | Lấy nội dung của dòng lỗi. |
| `Add-Content` | Ghi thêm kết quả vào cuối file báo cáo. |
| `-Encoding utf8` | Chỉ định mã hóa UTF-8 khi ghi dữ liệu. |

## 4. Phân tích cách bảo vệ dữ liệu cũ

- `Add-Content` ghi nối nội dung mới vào cuối file.
- `Set-Content` thay thế nội dung hiện có bằng nội dung mới.
- Vì đề bài yêu cầu giữ nguyên báo cáo của ngày hôm trước nên sử dụng `Add-Content`.
- Nếu chạy lại cùng một tập log, báo cáo có thể xuất hiện các dòng lỗi trùng lặp.
- Cần có cơ chế phân biệt log mới hoặc đánh dấu ngày chạy nếu muốn tránh trùng dữ liệu khi tự động hóa.

## 5. Kết luận

PowerShell giúp tìm kiếm file, lọc nội dung và tự động ghi báo cáo bằng cách kết hợp nhiều lệnh qua pipeline. Việc lựa chọn `Add-Content` đáp ứng yêu cầu ghi nối dữ liệu và bảo vệ nội dung báo cáo cũ.
| Tiêu chí | PowerShell | Git Bash | CMD |
|---|---|---|---|
| **Môi trường** | Shell và ngôn ngữ kịch bản, tích hợp tốt với Windows; cũng có phiên bản chạy trên macOS và Linux. | Môi trường Bash được cung cấp cùng Git for Windows. | Trình thông dịch dòng lệnh truyền thống của Windows. |
| **Cách xử lý dữ liệu** | Pipeline truyền các đối tượng có thuộc tính và dữ liệu có cấu trúc. | Pipeline chủ yếu truyền dữ liệu dạng văn bản. | Chủ yếu xử lý văn bản và kết quả đầu ra của lệnh. |
| **Tìm file `.log`** | `Get-ChildItem -Recurse -Filter *.log -File` | `find . -type f -name "*.log"` | `dir /s /b *.log` |
| **Lọc dòng lỗi** | `Select-String -Pattern 'ERROR'` | `grep 'ERROR'` | `findstr /s /n /i "ERROR" *.log` |
| **Ghi nối báo cáo** | `Add-Content` | `>>` | `>>` |
| **Ghi đè báo cáo** | `Set-Content` | `>` | `>` |
| **Tự động hóa** | File script `.ps1` | File script `.sh` | File batch `.bat` hoặc `.cmd` |
| **Biến môi trường** | `$env:PATH` | `$PATH` | `%PATH%` |
| **Hệ điều hành** | Windows, macOS và Linux (PowerShell hiện đại). | Chủ yếu được sử dụng trên Windows thông qua Git for Windows; Bash cũng phổ biến trên Linux và macOS. | Chủ yếu dành cho Windows. |
| **Điểm mạnh** | Quản trị Windows, xử lý dữ liệu có cấu trúc và tự động hóa tác vụ phức tạp. | Sử dụng các lệnh kiểu Unix/Linux, thao tác Git và làm việc với dự án phần mềm. | Đơn giản, nhẹ, phù hợp với các lệnh Windows truyền thống và file batch. |
| **Hạn chế** | Cú pháp ban đầu có thể khó nhớ với người mới học. | Một số lệnh và đường dẫn khác với Windows; có thể cần chú ý khi kết hợp công cụ Windows. | Khả năng xử lý dữ liệu và tự động hóa phức tạp hạn chế hơn PowerShell. |
