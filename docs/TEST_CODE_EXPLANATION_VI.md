# Giải thích code test CRBS (tiếng Việt)
Tài liệu này giải thích cách các test trong thư mục `tests/` được tổ chức và mỗi test kiểm tra điều gì. **Phạm vi:** tất cả test chạy trên prototype `src/crbs.py` (Python + SQLite trong bộ nhớ), không phải hệ thống triển khai thật.
## 1. Cách chạy
```bash
pip install -r requirements.txt
python -m pytest -q                              # chạy tất cả: 54 passed
python -m pytest tests/test_campus_booking.py -v # 7 acceptance test
python -m pytest tests/test_crbs.py -v           # 40 regression test
```
## 2. Ba nhóm test
| File | Số test | Mục đích |
|---|---|---|
| `tests/test_campus_booking.py` | 7 | Acceptance test theo test case TC-xx. Dữ liệu đầu vào nằm trong `test_data/acceptance_test_data.json` (định nghĩa dữ liệu trước, viết code sau). |
| `tests/test_crbs.py` | 40 | Regression test PT-01…PT-40, mỗi test gắn với một REQ/BR/DR/UC. |
| `tests/test_catalog_contract.py` | 7 | Kiểm tra cấu trúc của 42 test case trong catalogue (ID duy nhất, đủ trường setup/input/expected/assertion). Không phải test nghiệp vụ. |
## 3. Cấu trúc mỗi test (Arrange – Act – Assert)
1. **Arrange**: tạo CSDL SQLite mới trong bộ nhớ, thêm người dùng và tài nguyên theo `base_fixture` trong file JSON.
2. **Act**: gọi hàm của prototype, ví dụ `create_booking`, `decide_booking`, `cancel_booking`.
3. **Assert**: so sánh trạng thái booking, thông báo lỗi hoặc số bản ghi với kết quả mong đợi trong JSON.
## 4. Acceptance test (test_campus_booking.py)
| Test ID | Hàm test | Kiểm tra |
|---|---|---|
| TC-01 | `test_TC01_available_room_booking_creates_pending` | a valid room request should be created in Pending. |
| TC-02 | `test_TC02_overlapping_room_booking_is_rejected` | an overlapping request must not create a second booking. |
| TC-03 | `test_TC03_facility_staff_can_approve_pending_booking` | authorized Facility Staff can approve a Pending booking. |
| TC-05 | `test_TC05_maintenance_equipment_cannot_be_booked` | equipment under maintenance blocks the whole bundle request. |
| TC-06 | `test_TC06_student_cannot_book_restricted_vip_room` | a Student outside the allow-list cannot book the VIP room. |
| TC-11 | `test_TC11_owner_can_cancel_more_than_two_hours_before_start` | the owner can cancel a valid booking three hours before its start. |
| TC-26 | `test_TC26_room_and_bundled_equipment_are_one_request` | the room and selected equipment are linked to one booking ID. |

## 5. Regression test (test_crbs.py)
Cột *Liên kết* cho biết yêu cầu (REQ), quy tắc nghiệp vụ (BR), quy tắc quyết định (DR), use case (UC) hoặc test case (TC) mà test kiểm chứng.

| PT ID | Hàm test | Liên kết | Kiểm tra |
|---|---|---|---|
| PT-01 | `test_PT01_maintenance_blocks_checkout_retest_of_TC05` | REQ-04 / historical TC-05 defect retest | maintenance blocks checkout |
| PT-02 | `test_PT02_valid_booking_pending_and_acknowledgement` | REQ-02 | valid booking creates Pending |
| PT-03 | `test_PT03_overlapping_booking_rejected` | REQ-01 | overlapping booking is blocked |
| PT-04 | `test_PT04_adjacent_slot_allowed` | REQ-01 | adjacent slots allowed |
| PT-05 | `test_PT05_invalid_interval_rejected` | DR-03 | invalid interval rejected |
| PT-06 | `test_PT06_staff_approves_pending_booking` | REQ-03 | staff approves and notifies |
| PT-07 | `test_PT07_student_cannot_approve` | REQ-09 | student cannot approve |
| PT-08 | `test_PT08_cannot_decide_booking_twice` | REQ-03 | only Pending can be decided |
| PT-09 | `test_PT09_restricted_resource_hidden_from_student` | REQ-09 | student cannot view restricted resource |
| PT-10 | `test_PT10_restricted_resource_booking_denied` | REQ-09 | student cannot book restricted resource |
| PT-11 | `test_PT11_student_cannot_book_beyond_7_days` | BR-01 | Student lead time <=7 days |
| PT-12 | `test_PT12_lecturer_can_book_within_30_days` | BR-01 | Lecturer can book within 30 days |
| PT-13 | `test_PT13_lecturer_seminar_over_4h_rejected` | NFR-D10 | Lecturer seminar duration max 4h |
| PT-14 | `test_PT14_student_weekly_quota_enforced` | BR-05 | student weekly quota enforced |
| PT-15 | `test_PT15_cancel_exactly_two_hours_before` | REQ-06 | exact 2 hours cancellation allowed |
| PT-16 | `test_PT16_cancel_under_two_hours_rejected` | REQ-06 | less than 2 hours rejected |
| PT-17 | `test_PT17_other_user_cannot_cancel` | DR-09 | only owner can cancel |
| PT-18 | `test_PT18_checkout_valid_approved_equipment` | REQ-04 | valid checkout records staff and timestamp |
| PT-19 | `test_PT19_good_checkin_closes_transaction` | REQ-05 | good check-in closes transaction |
| PT-20 | `test_PT20_damaged_checkin_opens_maintenance` | REQ-05, REQ-07 | damaged return creates maintenance incident |
| PT-21 | `test_PT21_maintenance_resource_excluded_from_search` | REQ-07 | maintenance removes resource from search |
| PT-22 | `test_PT22_close_incident_restores_resource` | REQ-07 | close incident clears maintenance |
| PT-23 | `test_PT23_audit_update_delete_blocked` | REQ-08 | audit immutable |
| PT-24 | `test_PT24_notification_outage_does_not_block_booking` | REQ-10 | notification outage doesn't block booking |
| PT-25 | `test_PT25_admin_assigns_role_and_audit` | UC-14, UC-15 | admin can assign role and audit |
| PT-26 | `test_PT26_staff_cannot_assign_role` | REQ-09 | non-admin cannot assign role |
| PT-27 | `test_PT27_room_and_bundled_equipment_are_one_request` | DR-16 | one booking contains the room and bundled equipment |
| PT-28 | `test_PT28_register_and_login` | REQ-11, UC-16 | registration and login are demonstrable |
| PT-29 | `test_PT29_five_failed_logins_lock_account` | REQ-11 | repeated invalid logins trigger temporary lockout |
| PT-30 | `test_PT30_conflicting_bundled_equipment_rejects_whole_booking` | DR-16 | bundled equipment conflict rejects the entire request |
| PT-31 | `test_PT31_invalid_checkin_condition_rejected` | DR-08 | invalid check-in condition is rejected |
| PT-32 | `test_PT32_maintenance_bundled_item_rejects_request` | DR-16, REQ-07 | maintenance on a bundled item rejects whole request |
| PT-33 | `test_PT33_resource_catalogue_create_update_is_authorized_and_audited` | REQ-13 / UC-18 | catalogue maintenance is restricted to Facility Staff and audited. |
| PT-34 | `test_PT34_booking_requires_purpose` | TC-10 / REQ-02 | purpose is mandatory. |
| PT-35 | `test_PT35_booking_list_is_scoped_to_requester` | TC-09 and TC-13 | users see their own booking records only. |
| PT-36 | `test_PT36_lecturer_seminar_within_four_hours_succeeds` | TC-16 / BR-03 | a lecturer seminar within the provisional 4-hour limit is accepted. |
| PT-37 | `test_PT37_approval_rechecks_maintenance_on_bundled_item` | TC-17 / HZ-01-HZ-06 | approval revalidates bundled equipment. |
| PT-38 | `test_PT38_invalid_login_password_rejected` | TC-25 / REQ-11 | invalid password is rejected at login. |
| PT-39 | `test_PT39_admin_disables_account_and_audits` | TC-27 / UC-14 | admin can disable an account and the action is audited. |
| PT-40 | `test_PT40_lecturer_can_cancel_own_booking` | TC-11 / UC-05 | Lecturer, as Booking Owner, can cancel their own booking. |

## 6. Lưu ý về bằng chứng
- Test đã chạy là test trên prototype. Các test hiệu năng, đồng thời trên CSDL thật, bảo mật triển khai vẫn ở trạng thái **Planned**.
- Một số chính sách (thời hạn đặt trước, ranh giới hủy, timeout giảng viên) là giả định nháp chờ xác nhận.
- `PT-14` bật quota tuần chỉ để minh họa; prototype mặc định tắt quota.
