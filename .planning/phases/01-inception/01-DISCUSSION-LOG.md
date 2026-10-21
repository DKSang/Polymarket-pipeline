# Phase 1: Inception - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-10-21
**Phase:** 1-Inception
**Areas discussed:** SLO batch và fallback, KPI và consumer giả thuyết, Owner và escalation, SLO độ tươi live

Vùng xám được đề xuất ban đầu: 4 (Owner và escalation, SLO độ tươi live, SLO batch và fallback, KPI và consumer giả thuyết). Người dùng chọn hai vùng sau cùng, rồi yêu cầu thảo luận nốt hai vùng còn lại ở bước "Tiếp theo".

---

## SLO batch và fallback

### Hạn hoàn tất batch
| Option | Description | Selected |
|--------|-------------|----------|
| Trước 08:00 (60 phút) | Thận trọng, chưa đo scheduler/phân trang của trial; chỉ là mục tiêu | ✓ |
| Trước 07:30 (30 phút) | Chặt hơn, rủi ro đặt số khi chưa có phép đo | |
| Chưa chốt | Ghi chưa chốt, đề xuất lại sau khi đo | |

### Tuổi tối đa metadata fallback
| Option | Description | Selected |
|--------|-------------|----------|
| Tối đa 48 giờ | Cho phép lỡ một batch | |
| Tối đa 72 giờ | Cho phép lỡ hai batch | |
| Không đặt tuổi tối đa | Chỉ cảnh báo độ cũ, dựa vào kiểm tra lại market | ✓ |

### Cách đánh giá SLO batch
| Option | Description | Selected |
|--------|-------------|----------|
| Mọi lần chạy theo lịch | Mỗi lần phải xong trước 08:00; trễ/lỗi là sự cố | ✓ |
| Tỷ lệ phần trăm số ngày | Ví dụ ≥90%, nhiễu trên mẫu ~30 ngày | |
| Chưa chốt | | |

### Market không hợp lệ khi dùng fallback
| Option | Description | Selected |
|--------|-------------|----------|
| Loại market, chạy tiếp | Loại kèm lý do, đăng ký phần còn lại | |
| Dừng cả phiên | Chỉ chạy khi toàn bộ watchlist hợp lệ | |
| Để thiết kế Phase 2 quyết | Câu hỏi mở cho DATA-SPEC | ✓ |

**User's choice:** 60 phút; không tuổi tối đa; đánh giá từng lần chạy; để Phase 2 quyết.
**Notes:** Lựa chọn "không đặt tuổi tối đa" được ghi là quyết định có chủ ý (D-04), kèm rủi ro và cảnh báo về cổng G1.

---

## KPI và consumer giả thuyết

### Mức xếp hạng movers
| Option | Description | Selected |
|--------|-------------|----------|
| Mỗi outcome riêng | Theo asset ID, khớp WebSocket; cần quy tắc làm gọn cặp đối xứng | ✓ |
| Chỉ outcome 'Yes' | Một dòng mỗi market, cần định nghĩa outcome chính | |
| Để Phase 2 quyết | | |

### "Sôi động"
| Option | Description | Selected |
|--------|-------------|----------|
| Hai trạng thái, không ngưỡng số | Có trade observation hay chỉ quote; hiển thị số liệu để tự đánh giá | ✓ |
| Đặt ngưỡng số ngay | Ví dụ ≥1 trade/phút | |
| Hai trạng thái, ngưỡng số sau khi có dữ liệu | Ngưỡng cấu hình đề xuất ở Phase 4 | |

### Consumer giả thuyết
| Option | Description | Selected |
|--------|-------------|----------|
| Dùng bản nháp này | Ba quyết định giả thuyết cho Movers, Activity, Shock | ✓ |
| Chỉ ghi quyết định cho Movers và Shock | Activity chỉ là ngữ cảnh độ tin cậy | |

**User's choice:** Mỗi outcome riêng; hai trạng thái không ngưỡng; dùng bản nháp consumer.
**Notes:** Quy tắc làm gọn cặp outcome nhị phân để Phase 2/4 thiết kế.

---

## Owner và escalation

| Option | Description | Selected |
|--------|-------------|----------|
| Một người giữ cả ba vai | Duong Kim Sang; không ghi email/liên hệ riêng tư | ✓ |
| Owner khác hoặc tách vai | Người dùng tự nhập tên và vai | |

**User's choice:** Một người giữ cả ba vai.

---

## SLO độ tươi live

### Tỷ lệ đạt
| Option | Description | Selected |
|--------|-------------|----------|
| ≥95% mẫu ≤30s, báo cáo cả max | Báo cáo p50/p95/max, liệt kê mẫu vượt | ✓ |
| 100% mẫu ≤30s | Không mẫu nào vượt; chưa biết khả thi | |
| Chưa chốt | | |

### Điểm đo
| Option | Description | Selected |
|--------|-------------|----------|
| Tự động + kiểm tay một lần | Dấu thời gian tự động, đối chiếu ingestion time, một lần quay màn hình | ✓ |
| Chỉ dấu thời gian tự động | | |
| Chỉ quan sát thủ công | | |

### Lúc lỗi/recovery
| Option | Description | Selected |
|--------|-------------|----------|
| Tính mọi bản ghi live, tách gap và replay | Không bỏ mẫu chậm; replay báo cáo riêng | |
| Tách đo bình thường và recovery | Hai tỷ lệ, chỉ bình thường xét đạt | |
| Chưa chốt | Để lại cho DATA-SPEC | ✓ |

**User's choice:** ≥95% mẫu ≤30s; tự động + kiểm tay; chưa chốt lúc lỗi/recovery.
**Notes:** Mục lỗi/recovery ghi ở CONTEXT.md là O-01, không tự điền.

---

## Claude's Discretion

- Vị trí/định dạng bản ghi owner, ngôn ngữ và cách trình bày DATA-SPEC mục 1, cách diễn đạt giới hạn portfolio.

## Deferred Ideas

None.
