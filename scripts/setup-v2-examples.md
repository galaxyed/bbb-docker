# Setup Script v2 - Command Line Usage Examples

Script `setup-v2` đã được cập nhật để hỗ trợ cả command line arguments và interactive mode.

## Cách sử dụng

### 1. Interactive Mode (như cũ)

```bash
./scripts/setup-v2 --interactive
```

hoặc

```bash
./scripts/setup-v2
```

(nếu không có arguments nào được cung cấp, script sẽ chạy ở interactive mode)

### 2. Command Line Arguments Mode

#### Ví dụ cơ bản:

```bash
./scripts/setup-v2 --domain example.com
```

#### Ví dụ đầy đủ:

```bash
./scripts/setup-v2 \
  --domain example.com \
  --greenlight y \
  --https-proxy y \
  --letsencrypt-email admin@example.com \
  --recording y \
  --remove-old-recording y \
  --recording-max-age-days 30 \
  --prometheus-exporter y \
  --prometheus-optimization y \
  --override-secrets y \
  --bbb-secret "myCustomBBBSecret123" \
  --external-ipv4 192.168.1.100 \
  --external-ipv6 2001:db8::1
```

#### Ví dụ với một số tùy chọn:

```bash
./scripts/setup-v2 \
  --domain myserver.com \
  --greenlight y \
  --https-proxy y \
  --letsencrypt-email webmaster@myserver.com \
  --override-secrets n
```

#### Ví dụ ghi đè file .env hiện có:

```bash
./scripts/setup-v2 \
  --domain example.com \
  --greenlight y \
  --force
```

#### Ví dụ giữ nguyên secrets hiện có:

```bash
./scripts/setup-v2 \
  --domain example.com \
  --greenlight y \
  --override-secrets n
```

#### Ví dụ với BBB secret tùy chỉnh:

```bash
./scripts/setup-v2 \
  --domain example.com \
  --greenlight y \
  --bbb-secret "mySecretKey123456789"
```

#### Ví dụ BBB secret tùy chỉnh + giữ nguyên secrets khác:

```bash
./scripts/setup-v2 \
  --domain example.com \
  --greenlight y \
  --override-secrets n \
  --bbb-secret "mySecretKey123456789"
```

(Trong trường hợp này: SHARED_SECRET sẽ được thay bằng "mySecretKey123456789", các secrets khác giữ nguyên)

## Tham số có sẵn

| Tham số                     | Mô tả                                 | Giá trị | Bắt buộc                      |
| --------------------------- | ------------------------------------- | ------- | ----------------------------- |
| `--domain`                  | Tên miền                              | string  | Có                            |
| `--greenlight`              | Bao gồm Greenlight                    | y/n     | Không (mặc định: n)           |
| `--https-proxy`             | Proxy HTTPS tự động                   | y/n     | Không (mặc định: n)           |
| `--letsencrypt-email`       | Email cho Let's Encrypt               | email   | Có nếu https-proxy=y          |
| `--recording`               | Tính năng ghi âm                      | y/n     | Không (mặc định: n)           |
| `--remove-old-recording`    | Xóa recording cũ                      | y/n     | Không (mặc định: n)           |
| `--recording-max-age-days`  | Số ngày giữ recording                 | số      | Có nếu remove-old-recording=y |
| `--prometheus-exporter`     | Prometheus exporter                   | y/n     | Không (mặc định: n)           |
| `--prometheus-optimization` | Tối ưu Prometheus                     | y/n     | Không (mặc định: n)           |
| `--override-secrets`        | Ghi đè secrets hiện có                | y/n     | Không (mặc định: y)           |
| `--bbb-secret`              | BBB shared secret tùy chỉnh (độc lập) | string  | Không (theo override-secrets) |
| `--external-ipv4`           | IP IPv4 external                      | IP      | Không (tự động phát hiện)     |
| `--external-ipv6`           | IP IPv6 external                      | IP      | Không (tự động phát hiện)     |
| `--force`                   | Ghi đè file .env hiện có              | -       | Không                         |
| `--interactive`             | Chế độ tương tác                      | -       | Không                         |
| `--help`                    | Hiển thị trợ giúp                     | -       | Không                         |

## Lưu ý

1. **Tham số bắt buộc**: `--domain` là bắt buộc khi chạy ở command line mode
2. **Tham số phụ thuộc**:
   - `--letsencrypt-email` bắt buộc khi `--https-proxy=y`
   - `--recording-max-age-days` bắt buộc khi `--remove-old-recording=y`
3. **Giá trị mặc định**: Các tham số không được cung cấp sẽ có giá trị mặc định là "n" (trừ `--override-secrets` mặc định là "y")
4. **Override secrets**:
   - `--override-secrets=y` (mặc định): Tạo mới các secrets ngẫu nhiên (SHARED_SECRET, ETHERPAD_API_KEY, RAILS_SECRET, FSESL_PASSWORD, POSTGRESQL_SECRET, TURN_SECRET)
   - `--override-secrets=n`: Giữ nguyên các giá trị secrets hiện có trong file .env
5. **Custom BBB Secret**:
   - `--bbb-secret "your_secret"`: Luôn sử dụng giá trị tùy chỉnh cho SHARED_SECRET (hoạt động độc lập với --override-secrets)
   - Nếu không cung cấp: SHARED_SECRET sẽ được tạo ngẫu nhiên (khi override-secrets=y) hoặc giữ nguyên (khi override-secrets=n)
   - **Lưu ý**: Option này hoạt động độc lập, không phụ thuộc vào --override-secrets
   - **Các trường hợp**:
     - `--bbb-secret "abc" --override-secrets y`: SHARED_SECRET="abc", các secrets khác tạo mới
     - `--bbb-secret "abc" --override-secrets n`: SHARED_SECRET="abc", các secrets khác giữ nguyên
     - `--override-secrets y` (không có --bbb-secret): Tất cả secrets tạo mới
     - `--override-secrets n` (không có --bbb-secret): Tất cả secrets giữ nguyên
6. **IP tự động**: Nếu không cung cấp `--external-ipv4` hoặc `--external-ipv6`, script sẽ tự động phát hiện
7. **Ghi đè file .env**:
   - Sử dụng `--force` để tự động ghi đè file .env hiện có
   - Trong interactive mode, script sẽ hỏi có muốn ghi đè không
   - Trong command line mode không có `--force`, script sẽ dừng nếu file .env đã tồn tại
8. **Validation**: Script sẽ kiểm tra tính hợp lệ của các tham số và báo lỗi nếu thiếu thông tin bắt buộc

## Ví dụ sử dụng trong CI/CD

```bash
# Trong script deployment
./scripts/setup-v2 \
  --domain $DOMAIN_NAME \
  --greenlight $ENABLE_GREENLIGHT \
  --https-proxy $ENABLE_HTTPS \
  --letsencrypt-email $ADMIN_EMAIL \
  --recording $ENABLE_RECORDING \
  --prometheus-exporter $ENABLE_MONITORING \
  --override-secrets $OVERRIDE_SECRETS \
  --bbb-secret "$BBB_SHARED_SECRET"
```

## Troubleshooting

1. **Lỗi "domain is required"**: Cần cung cấp `--domain` khi chạy command line mode
2. **Lỗi "letsencrypt-email is required"**: Cần cung cấp email khi bật HTTPS proxy
3. **Lỗi "configuration file .env already exists"**:
   - Sử dụng `--force` để ghi đè tự động
   - Hoặc xóa file `.env` thủ công trước khi chạy script
   - Hoặc sử dụng `--interactive` để được hỏi có muốn ghi đè không
4. **Lỗi "Unknown option"**: Kiểm tra lại tên tham số, sử dụng `--help` để xem danh sách đầy đủ
