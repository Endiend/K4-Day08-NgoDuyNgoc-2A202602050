# Báo cáo Lab Ngày 08: Học chủ động cho bộ phát hiện xe

Họ và tên: Ngo Duy Ngoc

Công cụ gán nhãn đã dùng: CVAT

## 1. Dữ liệu và cách chia tập

Tập chưa gán nhãn và tập kiểm thử được chia theo trục thời gian vì camera đứng yên nên các ảnh liên tiếp gần như cùng một cảnh. Nếu chia ngẫu nhiên, cùng một chiếc xe hoặc một đoạn đường có thể xuất hiện đồng thời ở train và test, khiến mô hình “nhìn thấy” thông tin nhầm và đánh giá quá lạc quan. Chính vì vậy, AP50 sẽ bị ảo hóa, vì cùng cảnh gần như được học lại trong test. Việc có vùng đệm giữa hai tập giúp tránh rò rỉ thời gian và phản ánh đúng khả năng tổng quát của mô hình khi gặp cảnh mới.

## 2. Mô hình khởi đầu lạnh (cold start)

Dòng vòng 0 trong rounds_table.md cho thấy: AP50 = 0.7714, precision = 0.9249, recall = 0.4888, F1 = 0.6396. Recall theo kích thước là 0.1818 cho xe nhỏ, 0.5473 cho xe vừa và 0.5610 cho xe lớn. Điều này cho thấy mô hình khởi đầu lạnh chủ yếu bỏ sót xe rất xa và xe nhỏ, trong khi xe gần và xe lớn còn tệ hơn một chút nhưng không quá nghiêm trọng. Trong compare_round0.jpg, các xe ở góc trên trái và mép dưới phải bị thiếu khung hoặc khung lệch; đó là trường hợp cần người rà lại nhãn tham chiếu trước khi kết luận mô hình sai, vì chính BLIND_SCAN.md đã chỉ ra có nhiều xe chỉ còn đèn hoặc chỉ hiện một phần thân, nên nhãn tham chiếu cũng có thể không hoàn chỉnh.

## 3. Chiến lược chọn mẫu

     Chiến lược chọn ảnh dựa trên điểm score = W_U·U + W_A·A + W_D·D, trong đó U đo độ bất định của model, A đo độ lưỡng lự trên box, còn D phản ánh sự khác biệt hoặc độ khó của cảnh. Ngoài ra, MIN_GAP_S = 2 giây giúp tránh chọn hai ảnh cùng một cảnh gần như nhau, vì hai ảnh kề nhau trên camera đứng yên thường mang rất ít thông tin mới. Tôi ưu tiên frame_0182.jpg (0.9591), frame_0369.jpg (0.9324) và frame_0380.jpg (0.9170) vì đây là những cảnh có nhiều xe khó, xe mép ảnh và xe xa; tôi cũng giữ frame_0099.jpg (0.9063) và frame_0107.jpg (0.8876) vì cả hai là ca “sửa khung để giảm bỏ sót”. Điểm bất định không chứng minh ảnh đó chắc chắn sẽ cải thiện mô hình, nhưng nó cho biết ảnh đó chứa nhiều box lưỡng lự và chi phí rà nhãn cao hơn; chỉ khi kết hợp với khoảng cách thời gian và mức độ khác biệt cảnh thì lựa chọn mới có giá trị học tập.

## 4. Các vòng học chủ động (active learning)

Vòng 0 và vòng 1 trong rounds_table.md như sau: vòng 0 AP50 = 0.7714; vòng 1 AP50 = 0.4363, giảm 0.3351 so với cold start. Trong round1_diff.md, model đề xuất 169 box nhưng sau khi sửa còn 312 box; số box accepted = 134, edited = 20, deleted = 15, added = 158, accept rate = 79%. Tức là tôi đã giữ gần 80% box cũ, đồng thời thêm rất nhiều box thiếu và xóa các box sai, nhưng điều này không giúp mô hình tốt lên trên tập test. Recall theo kích thước cho thấy mọi nhóm xe xấu đi: xe nhỏ từ 0.1818 xuống 0.0000, xe vừa từ 0.5473 xuống 0.0270, xe lớn từ 0.5610 xuống 0.1707. Đây là rõ ràng một vòng học không hiệu quả.

Một ca đổi rõ sau fine-tune là frame_0099.jpg: trước đó model đã bỏ sót các xe ở góc trên trái và mép dưới phải; sau khi sửa pre-label, hình ảnh này có 26 box sau khi rà lại, nhưng model sau train lại không khôi phục được nhiều box đó. round1_diff.md cho thấy trên frame_0099.jpg, model đề xuất 13 box, sau sửa còn 26 box, trong đó accepted = 9, edited = 3, deleted = 1, added = 14. Kết quả này cho thấy sự khác biệt giữa quan sát độc lập trong BLIND_SCAN.md, lỗi pre-label đã sửa trong round1_diff.md và output mô hình sau train là rất rõ. Một ca khó theo guideline là xe chỉ còn phần đuôi và đèn hậu ở góc dưới phải, vì nếu khoanh quá rộng hay quá nhỏ, model rất dễ sai và rất khó cho AI học được đúng nhãn từ dữ liệu quá ít.

## 5. Kết luận và giới hạn

Vòng 1 thấp hơn vòng 0 nhiều: AP50 giảm từ 0.7714 xuống 0.4363, nên tôi không tiếp tục train thêm ngay mà sẽ dừng lại và kiểm tra lại nhãn trước khi đưa vào vòng sau. Hai điểm yếu rõ nhất là xe quá nhỏ hoặc xe ở mép ảnh và hai kiệt thức gần trùng; những trường hợp này rất tốn công rà nhãn và ít đem lại nhiều thông tin mới nếu chọn hai ảnh cùng cảnh. Tập kiểm thử chỉ có 20 ảnh, còn quy tắc bỏ qua xe quá nhỏ và nhãn tham chiếu do mô hình tạo chưa được người rà thủ công làm tăng độ không chắc chắn của kết luận, nên không nên coi AP50 như “đúng tuyệt đối”. Nếu AP50 giảm, trước khi train thêm tôi sẽ kiểm tra lại các box đã sửa, ưu tiên sửa xe mép ảnh và xe xa, đồng thời bỏ các ảnh gần trùng để tránh cung cấp nhãn lặp và sai lệch cho mô hình.
