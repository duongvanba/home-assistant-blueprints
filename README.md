# Home Assistant Blueprints

Bộ sưu tập [Home Assistant](https://www.home-assistant.io/) automation blueprints cá nhân.

## Blueprints

### 1. Sonoff SwitchMan R5 BLE Remote

File: [switchman-r5-esphome.v1.yaml](switchman-r5-esphome.v1.yaml)

Automation cho remote BLE 6 nút **Sonoff SwitchMan R5**, đọc qua một ESPHome
text sensor và map 18 tổ hợp (6 nút × {single, double, long press}) sang các
action tuỳ chỉnh trong UI.

**Yêu cầu phía ESPHome:** firmware phải cập nhật text sensor với giá trị dạng
`<button>_<press_type>`, ví dụ `1_single`, `3_double`, `6_long`. Sau khi xử lý
nên reset sensor về chuỗi rỗng (`""`) — blueprint đã bỏ qua state rỗng nên
không bị trigger nhầm.

### 2. Daily Low Battery Check

File: [low-battery-daily-check.v3.yaml](low-battery-daily-check.v3.yaml) (mới
nhất). Các bản cũ:
- [low-battery-daily-check.v2.yaml](low-battery-daily-check.v2.yaml) — chạy 1
  lần/ngày tại 1 giờ cố định, đa đích notify.
- [low-battery-daily-check.v1.yaml](low-battery-daily-check.v1.yaml) — chỉ hỗ
  trợ 1 dịch vụ notify duy nhất.

Mỗi sáng quét toàn bộ thiết bị có thông tin pin và gửi thông báo nếu có pin
yếu. Nguồn pin được quét gồm:

- `sensor` có `device_class: battery`
- Bất kỳ entity nào có attribute `battery_level` (Zigbee / Z-Wave / BLE
  tracker thường gắn attribute này lên `device_tracker`, `binary_sensor`...)

**Input chính (v3):**

- `interval_pattern` — chu kỳ chạy: mỗi 1/2/3/4/6/8/12/24 giờ (mặc định mỗi
  1 giờ). Trigger là `time_pattern` nên chạy ở phút 0 đầu mỗi chu kỳ.
- `battery_threshold` — ngưỡng % pin yếu (mặc định `20`).
- `notify_targets` — **danh sách** tên service notify, **không** kèm tiền tố
  `notify.`. Thông báo sẽ gửi tới tất cả service trong danh sách (loop bằng
  `repeat.for_each`). Mặc định `[persistent_notification]`.
- `exclude_entities` — danh sách entity muốn bỏ qua.

> Lý do v2: `notify.notify` không phải broadcast — nó chỉ trỏ vào 1 platform
> duy nhất (thường là mobile_app đăng ký đầu tiên). Muốn gửi tới tất cả thiết
> bị thì phải hoặc tạo notification group trong `configuration.yaml`, hoặc
> dùng v2 này (loop) để tránh restart và cấu hình thêm.

## Cách cài đặt

### Cách 1 — Import qua URL (khuyến nghị)

Trong Home Assistant: **Settings → Automations & Scenes → Blueprints →
Import Blueprint**, dán URL raw của file `.yaml` từ repo này.

### Cách 2 — Copy thủ công

Copy file `.yaml` vào thư mục:

```
<config>/blueprints/automation/<your-folder>/
```

rồi reload blueprints trong UI.

## License

MIT.
