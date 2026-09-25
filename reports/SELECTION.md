# Vì sao chọn lô này?

Nếu chỉ được sửa 5 ảnh, tôi chọn frame_0182.jpg (hạng 1, điểm 0.9591), frame_0369.jpg (hạng 2, điểm 0.9324), frame_0380.jpg (hạng 3, điểm 0.9170), frame_0326.jpg (hạng 4, điểm 0.9155) và frame_0331.jpg (hạng 5, điểm 0.9154). Năm ảnh này đứng đầu danh sách và đều có nhiều xe bị model bỏ sót hoặc gộp nhầm; trong cùng lô, các ảnh này cũng có lượng xe rõ ràng và phân bố khác nhau, nên sửa chúng giúp tăng thông tin học hơn so với chọn các ảnh quá gần nhau.

Trong 12 ảnh AI đã chọn, tôi nhìn kỹ frame_0182.jpg, frame_0099.jpg và frame_0107.jpg. Cả ba đều có điểm trên 0.88 và đều có nhiều box lưỡng lự: frame_0182.jpg có nhiều xe đuôi cắt mép và xe rất xa, frame_0099.jpg có các xe ở góc trên trái và góc dưới phải dễ bị bỏ sót, còn frame_0107.jpg có vệt đèn và xe xa làm model dễ nhầm. Đây là các ca mà ảnh cần rà lại để giảm lỗi pre-label và tăng độ tin cậy của nhãn học tiếp.

frame_0372.jpg hạng 6, điểm 0.9101, cao hơn một số ảnh đã được chọn như frame_0312.jpg (0.9100) và frame_0392.jpg (0.8874), nhưng tôi không chọn vì nó nằm sát thời điểm với frame_0369.jpg, tức là gần như cùng một cảnh. Hai ảnh rất gần nhau không tạo thêm nhiều biến thể học, nên chọn một trong hai là hợp lý hơn. Tương tự, frame_0182.jpg và frame_0187.jpg cũng rất gần nhau, nên tôi chọn frame_0182.jpg để tránh lặp lại thông tin.

Điểm cao chỉ cho biết ảnh đó có nhiều bất định, không chứng minh sửa ảnh đó sẽ làm mô hình tốt hơn ngay. Mục tiêu của lựa chọn không phải “ăn theo top score”, mà là chọn các cảnh có nhiều xe khó, nhiều xe mép ảnh và nhiều trường hợp AI lưỡng lự, đồng thời tránh các ảnh gần trùng để tiết kiệm công rà nhãn. Vì vậy, lô 12 ảnh này phù hợp để đánh giá khả năng giảm bỏ sót và gộp nhầm, nhưng chưa phải là bằng chứng chắc chắn về chất lượng mô hình sau fine-tune.
