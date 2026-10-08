# Báo cáo lab: chọn tracker cho 5 video

**Nhóm:** 2A202602791 – 2A202602473
**Thành viên:** Tống Trần Tiến Dũng (2A202602791), Nguyễn Hoàng Cường (2A202602473)

Detector cố định: `yolo26n.pt`, ảnh 640 px, Re-ID `osnet_x0_25_msmt17`. Không đổi các mục này trong bài nộp chính.

## 1. Cấu hình đã chọn

Mỗi video: tracker bạn nộp, conf, iou, điều bạn nhìn thấy trên video, và một cấu hình đã thử rồi loại.

| Video | Tracker | conf | iou | Quan sát khi xem video | Đã thử nhưng loại |
|---|---|---|---|---|---|
| video_1 (quảng trường, tĩnh, ban ngày) | botsort | 0.3 | 0.5 | Có thêm ID cho người phía xa so với baseline; vẫn đứt danh tính khi người đi sát nhau: người áo tím từ ID 3 ở frame 150 sang ID 37 ở frame 225 và 300. Chọn theo HOTA của lượt đủ 600 frame, không coi Re-ID là bảo đảm giữ ID. | ByteTrack, 0.3 / 0.5: ít hộp người xa hơn, HOTA 26.912 thấp hơn BoT-SORT 29.460. BoT-SORT, 0.15 / 0.5: hộp giả tăng từ 337 lên 505 và HOTA giảm nhẹ. |
| video_2 (phố đêm, tĩnh, rất đông) | botsort | 0.15 | 0.5 | Người áo trắng bên trái cột đèn có ID sớm hơn ByteTrack trong đoạn thử; giữ ID 8 ở các frame 50, 100, 150 và vẫn mang ID 8 ở frame 525. Nhóm người nhỏ ở xa và vùng đèn lóe vẫn bị bỏ sót. | ByteTrack, 0.3 / 0.5: người áo trắng chưa có ID ở frame 50; BoT-SORT, 0.5 / 0.5: bỏ thêm các người nhỏ ở phía trên cảnh. |
| video_3 (camera di động, ảnh nhỏ) | botsort | 0.15 | 0.5 | Ba người gần camera giữ ID 1, 2, 17 tại các frame 50, 100, 150; có thêm hộp người nhỏ ở khoảng trống giữa các người phía trước. Camera di chuyển và ảnh mờ vẫn làm các hộp xa ngắt quãng. | ByteTrack, 0.3 / 0.5: các người chính cũng ổn định nhưng ít hộp cho người nhỏ ở xa hơn; BoT-SORT, 0.5 / 0.5: hộp xa dễ biến mất. |
| video_4 (trong nhà, camera di chuyển) | bytetrack | 0.15 | 0.5 | Người áo đỏ ID 2 và áo trắng ID 5 giữ danh tính ở các frame 50, 100, 150; người nhỏ phía trái giữ ID 7 giữa frame 100 và 150 khi hạ conf. Tuy nhiên, người áo đỏ từ ID 2 ở frame 450 thành ID 75 ở frame 900; bản đầy đủ vẫn có lỗi. | BoT-SORT, 0.15 / 0.5, đủ 900 frame: người áo đỏ cũng từ ID 2 ở frame 450 thành ID 93 ở frame 900, chưa khắc phục lỗi trên người chính. ByteTrack, 0.3 / 0.5: người nhỏ phía trái đổi ID 69 ở frame 100 sang 76 ở frame 150. |
| video_5 (trên xe bus, giao lộ đông) | botsort | 0.15 | 0.5 | Người áo đỏ bên phải giữ ID 2 ở frame 50 và 100; nhiều người trong bóng râm bên trái có hộp hơn ByteTrack. Ở frame 375, nhóm người gần góc giao lộ bên phải có ID; tới frame 750, nhiều người rất xa vẫn không có hộp. | ByteTrack, 0.3 / 0.5: ít hộp ở vỉa hè trong bóng râm; BoT-SORT, 0.5 / 0.5: mất thêm các hộp nhỏ và tối. |



## 2. Số liệu video_1

 Dưới đây là các hàng kết quả của TrackEval; các điểm HOTA / MOTA / IDF1 được in trên thang 0–100.

```
HOTA: dung_cuong_video1-pedestrian HOTA      DetA      AssA      DetRe     DetPr     AssRe     AssPr     LocA      OWTA      HOTA(0)   LocA(0)   HOTALocA(0)
video_1                            29.46     18.095    48.223    18.596    78.888    51.153    82.992    83.666    29.899    35.813    78.45     28.095

CLEAR: dung_cuong_video1-pedestrianMOTA      MOTP      MODA      CLR_Re    CLR_Pr    MTR       PTR       MLR       sMOTA     CLR_TP    CLR_FN    CLR_FP    IDSW      MT        PT        ML        Frag
video_1                            19.811    81.471    19.945    21.759    92.306    12.903    19.355    67.742    15.779    4043      14538     337       25        8         12        42        99

Identity: dung_cuong_video1-pedestrianIDF1      IDR       IDP       IDTP      IDFN      IDFP
video_1                            29.354    18.137    76.941    3370      15211     1010
```

`video_2` đến `video_5` không có nhãn trong gói lab. Không điền số cho các video đó.

Đối chiếu ba lượt **đủ 600 frame** của `video_1`:

| Cấu hình | HOTA | MOTA | IDF1 | Bỏ sót (FN) | Hộp giả (FP) | Đổi ID (IDSW) |
|---|---:|---:|---:|---:|---:|---:|
| ByteTrack, conf 0.3, iou 0.5 | 26.912 | 17.292 | 25.713 | 15.249 | 107 | 12 |
| **BoT-SORT, conf 0.3, iou 0.5 — nộp** | **29.460** | **19.811** | **29.354** | **14.538** | **337** | **25** |
| BoT-SORT, conf 0.15, iou 0.5 | 29.343 | 20.731 | 29.561 | 14.197 | 505 | 27 |

Giảm `conf` xuống 0.15 cải thiện MOTA và IDF1 nhẹ, nhưng tăng hộp giả và số lần đổi ID, còn HOTA thấp hơn 0.117 điểm. Chọn 0.3 vì HOTA cao nhất trong ba lượt đủ frame này và ít hộp giả hơn BoT-SORT 0.15; đây là lựa chọn trong phạm vi đã thử, không khẳng định tối ưu toàn bộ không gian tham số. Log chấm bản nộp lưu ở `runs/logs/danh_gia_nop_bai.log`.

## 3. Phân tích

**Giả thuyết trước thí nghiệm (CP1):** Ở `video_1`, camera tĩnh và người đi cắt ngang nhau; ByteTrack có thể giữ ID ở đoạn ít che khuất, còn BoT-SORT có Re-ID có thể nối lại người sau khi bị che. Ở `video_2`, nhiều người nhỏ đứng gần nhau dưới ánh đèn ban đêm; dự kiến giảm `conf` giúp bớt bỏ sót nhưng có thể tăng hộp giả, còn Re-ID cần được kiểm tra khi hai người mặc giống nhau đi sát nhau. Đây là giả thuyết cần đối chiếu với kết quả chạy, chưa phải kết luận.

**video_1:** Camera tĩnh và các người gần máy có chuyển động khá đều, nên ByteTrack giữ ID tốt trong baseline 150 frame; người áo tím còn giữ ID 3 tới frame 300 ở lượt ByteTrack đầy đủ. BoT-SORT theo dõi thêm một số người phía xa và có HOTA / IDF1 cao hơn, nhưng người áo tím đổi từ ID 3 sang 37 khi đi sát các người khác, cho thấy Re-ID vẫn có thể ghép sai hoặc tạo track mới. Số IDSW của BoT-SORT là 25, cao hơn 12 của ByteTrack, nên không kết luận rằng BoT-SORT tốt hơn ở mọi trường hợp giữ ID. Với 14.538 lượt bỏ sót trên 18.581 hộp nhãn người sau xử lý của bộ chấm, phát hiện người nhỏ ở xa vẫn là hạn chế lớn của cấu hình cố định 640 px.

**video_2:** Camera trên cao đứng yên nhưng cảnh tối, ánh đèn lóe và nhiều người đi gần nhau khiến hộp nhỏ dễ mất hoặc chồng lên nhau. BoT-SORT 0.15 giữ ID 8 của người áo trắng bên trái cột đèn qua các frame 50, 100, 150 và 525; ByteTrack 0.3 chưa gán ID cho người này ở frame 50. Hạ `conf` hỗ trợ tiếp tục các track có phát hiện yếu, còn thông tin ngoại hình của BoT-SORT là một lý do để thử tracker này khi các người cắt ngang nhau. Ở các frame giữa và cuối vẫn thấy nhiều người phía xa không có hộp, nên lựa chọn này chỉ được đánh giá bằng mắt và chưa chứng minh giữ ID đúng cho mọi người trong đám đông.

**video_3:** Ảnh 640 × 480 và chuyển động camera làm vị trí người thay đổi nhanh so với nền; các người nhỏ hoặc bị che chỉ cung cấp ít chi tiết ngoại hình. Trong đoạn thử, cả ByteTrack và BoT-SORT giữ được ba người gần máy, nhưng BoT-SORT 0.15 còn giữ thêm hộp người nhỏ ở khoảng trống phía sau tại frame 150. Chọn BoT-SORT vì kết hợp chuyển động, ngoại hình và bù chuyển động camera phù hợp để thử trên cảnh này, đồng thời hạ `conf` để bớt ngắt track khi phát hiện yếu. Việc có Re-ID không khắc phục được người bị detector bỏ sót hoặc ảnh quá mờ; các kết luận về cảnh này là quan sát định tính.

**video_4:** Dù camera tiến về phía trước, hai người áo đỏ và áo trắng khá lớn, rõ, và có hướng đi liên tục nên ByteTrack giữ ID 2 và 5 ở đoạn thử. Hạ `conf` từ 0.3 xuống 0.15 giúp người nhỏ phía trái giữ ID 7 giữa frame 100 và 150, thay vì đổi ID 69 sang 76 ở cấu hình 0.3. Khi đối chiếu đủ 900 frame ở cùng `conf=0.15`, `iou=0.5`, người áo đỏ mang ID 2 ở frame 450 nhưng tới frame 900 là ID 75 với ByteTrack và ID 93 với BoT-SORT. Giữ ByteTrack vì nó đáp ứng đoạn chuyển động đều và thử thêm Re-ID chưa khắc phục được lỗi đứt ID trên người chính; lựa chọn này vẫn có hạn chế khi camera tiến tới và người che nhau. Kính và sàn bóng cần được xem kỹ để phân biệt người thật với ảnh phản chiếu, và không có nhãn để định lượng lỗi này.

**video_5:** Xe bus chuyển động làm toàn cảnh đổi vị trí, trong khi người trên hai vỉa hè nhỏ và có vùng nằm trong bóng râm. BoT-SORT 0.15 giữ người áo đỏ bên phải với ID 2 ở frame 50 và 100, đồng thời có thêm hộp ở vỉa hè tối so với ByteTrack 0.3; ngưỡng 0.5 bỏ thêm người nhỏ. Ở frame 375, nhóm người gần góc giao lộ bên phải có hộp/ID, nhưng frame 750 vẫn bỏ sót nhiều người rất xa nên chất lượng không đồng đều suốt clip. Bù chuyển động camera và Re-ID là các thành phần hợp với cảnh quay từ xe, nhưng khi người bị xe che hoặc ra khỏi khung hình thì không thể suy ra lỗi đổi ID chỉ từ việc hộp biến mất.

## 4. Nếu có thêm thời gian

Quét `conf` mịn hơn quanh 0.15–0.3 và thử OC-SORT / StrongSORT trên các đoạn cắt ngang, che khuất hoặc camera đổi hướng, vẫn giữ nguyên detector, kích thước ảnh và trọng số Re-ID của lab. Với `video_1`, so sánh HOTA / MOTA / IDF1 trên đủ frame; với bốn video còn lại, xem các frame trước và sau khi mất hộp để phân biệt bỏ sót, ra khỏi cảnh và đổi danh tính.
