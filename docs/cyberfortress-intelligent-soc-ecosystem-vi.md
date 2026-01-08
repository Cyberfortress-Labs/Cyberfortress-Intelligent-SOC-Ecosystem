# Hệ sinh thái SOC Thông minh - Cyberfortress Labs

> Tóm lược Khóa luận: Hệ sinh thái SOC Thông minh cho Giám sát, Phát hiện và Ứng phó Tấn công Mạng

![SIEM Architecture](../img/KLTN_SOC-Ecosystem-LogicFlow.drawio.png)


## Tóm tắt Báo cáo

Khóa luận này trình bày việc nghiên cứu, thiết kế và triển khai một **Hệ sinh thái Trung tâm điều hành an ninh mạng (SOC) Thông minh** toàn diện. Trọng tâm của đề tài là giải quyết các thách thức cố hữu của mô hình SOC truyền thống, bao gồm tình trạng quá tải cảnh báo, thiếu ngữ cảnh phân tích và sự phụ thuộc vào các quy trình thủ công.

Giải pháp đề xuất là một kiến trúc hợp nhất, tích hợp các nền tảng **SIEM, SOAR, OpenXDR, và Tình báo Mối đe dọa (CTI)**, được tăng cường bởi công nghệ **Trí tuệ Nhân tạo (AI/ML/LLM)** để tự động hóa toàn diện chu trình giám sát, phát hiện và ứng phó sự cố an ninh mạng.

Thành phần đột phá của hệ thống là **SmartXDR**, một lớp điều phối thông minh do nhóm tác giả tự phát triển. SmartXDR không thay thế các công cụ hiện có mà đóng vai trò là "bộ não" trung tâm, thực hiện phân tích ngữ nghĩa, làm giàu dữ liệu, chấm điểm rủi ro và hỗ trợ ra quyết định, qua đó giảm thiểu cảnh báo giả và tối ưu hóa hiệu suất cho chuyên viên phân tích.

Kiến trúc này được hiện thực hóa hoàn toàn bằng các **công nghệ mã nguồn mở** (Elastic Stack, Wazuh, DFIR-IRIS, Suricata, n8n) trên một môi trường giả lập doanh nghiệp, chứng minh tính khả thi về mặt chi phí và kỹ thuật.

Hiệu quả của hệ sinh thái được kiểm chứng thông qua **năm kịch bản tấn công thực tế**, được ánh xạ theo khung **MITRE ATT&CK**. Kết quả thực nghiệm cho thấy hệ thống có khả năng phát hiện chính xác các mối đe dọa, đồng thời rút ngắn đáng kể **Thời gian Phát hiện Trung bình (MTTD)** và **Thời gian Phản ứng Trung bình (MTTR)**.


## 1. Bối cảnh và Vấn đề Cốt lõi

### 1.1. Bối cảnh An toàn Thông tin Hiện đại

Sự bùng nổ của chuyển đổi số, điện toán đám mây và IoT đã làm gia tăng đáng kể bề mặt tấn công của các tổ chức. Các mối đe dọa mạng ngày càng tinh vi, với các hình thức tấn công có chủ đích (APT), mã độc tống tiền (ransomware) và khai thác lỗ hổng zero-day trở nên phổ biến, đòi hỏi năng lực giám sát và phản ứng liên tục, kịp thời.

### 1.2. Thách thức của Mô hình SOC Truyền thống

Các trung tâm SOC truyền thống đang đối mặt với nhiều hạn chế nghiêm trọng:

| Thách thức                                | Mô tả                                                                                                                                                                                                        |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Quá tải Cảnh báo (Alert Fatigue)**      | Các hệ thống SIEM và IDS/IPS thường tạo ra một lượng lớn cảnh báo, trong đó có nhiều cảnh báo giả (false positives), khiến các chuyên viên phân tích bị quá tải và có nguy cơ bỏ sót các mối đe dọa thực sự. |
| **Thiếu Ngữ cảnh Phân tích**              | Dữ liệu từ các công cụ rời rạc, phân tán khiến việc tương quan sự kiện và hiểu được bức tranh toàn cảnh của một cuộc tấn công trở nên khó khăn.                                                              |
| **Quy trình Thủ công**                    | Sự phụ thuộc lớn vào các thao tác thủ công làm kéo dài thời gian phản ứng, thiếu tính nhất quán và dễ gây ra sai sót.                                                                                        |
| **Chi phí Cao và Phụ thuộc Nhà cung cấp** | Các giải pháp XDR thương mại tuy mạnh mẽ nhưng thường có chi phí đầu tư cao và hạn chế khả năng tích hợp linh hoạt với hạ tầng đa dạng của doanh nghiệp.                                                     |

> [!IMPORTANT]
> Trước những thách thức này, việc xây dựng một mô hình SOC thông minh, tích hợp và tự động hóa là một nhu cầu cấp thiết.


## 2. Kiến trúc Hệ sinh thái SOC Thông minh Đề xuất

Để giải quyết các vấn đề trên, khóa luận đề xuất một kiến trúc hợp nhất, biến các công cụ riêng lẻ thành một hệ sinh thái vận hành liền mạch.

### 2.1. Triết lý Thiết kế

Hệ thống được xây dựng dựa trên nguyên tắc kết hợp các thành phần cốt lõi của một SOC hiện đại:

- **SIEM**: Nền tảng thu thập, chuẩn hóa và lưu trữ log tập trung
- **OpenXDR**: Mở rộng phạm vi giám sát đa lớp từ mạng (network) đến thiết bị đầu cuối (endpoint)
- **SOAR**: Tự động hóa quy trình ứng phó và quản lý sự cố
- **CTI**: Làm giàu dữ liệu cảnh báo bằng thông tin tình báo về mối đe dọa
- **AI/ML**: Lớp thông minh hỗ trợ phân tích và ra quyết định

### 2.2. Các Công nghệ Mã nguồn mở được Sử dụng

Hệ thống tận dụng sức mạnh của các giải pháp mã nguồn mở để tối ưu hóa chi phí và đảm bảo tính linh hoạt.

| Nhóm chức năng                | Giải pháp công nghệ                                    | Vai trò chính                                                                                              |
| ----------------------------- | ------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------- |
| Quản lý Log & SIEM            | Elastic Stack (Elasticsearch, Logstash, Kibana, Fleet) | Lưu trữ, chuẩn hóa log tập trung (theo chuẩn ECS), trực quan hóa dữ liệu và phát hiện dựa trên quy tắc     |
| An ninh Đầu cuối (XDR)        | Wazuh                                                  | Giám sát toàn vẹn tệp (FIM), phát hiện mối đe dọa và thực thi phản ứng chủ động trên thiết bị cuối         |
| Giám sát Mạng (NSM/IDPS)      | Suricata & Zeek                                        | Phát hiện và ngăn chặn xâm nhập mạng (IDPS); phân tích sâu giao thức mạng để điều tra                      |
| Tường lửa & WAF               | pfSense & ModSecurity                                  | Kiểm soát và lọc lưu lượng mạng; bảo vệ ứng dụng web khỏi các tấn công phổ biến                            |
| Tự động hóa & Phản ứng (SOAR) | DFIR-IRIS, n8n, IntelOwl                               | Quản lý quy trình xử lý sự cố, tự động hóa quy trình (workflow automation), và làm giàu thông tin tình báo |
| Tình báo Mối đe dọa (CTI)     | MISP                                                   | Nền tảng lưu trữ, quản lý và chia sẻ các chỉ số tấn công (IoCs)                                            |
| Lõi Điều phối Thông minh      | SmartXDR (Tự phát triển)                               | "Bộ não" trung tâm, tích hợp AI/ML để phân tích ngữ nghĩa, xếp hạng rủi ro và điều phối toàn bộ hệ thống   |


## 3. Thành phần Đột phá: SmartXDR

![SmartXDR Architecture](../img/KLTN_SOC-Ecosystem-SmartXDR.drawio.png)

**SmartXDR** là hạt nhân của hệ sinh thái, được thiết kế như một lớp dịch vụ trung gian thông minh, nâng cao năng lực cho toàn bộ hệ thống thay vì thay thế các thành phần hiện có.

### Vai trò

SmartXDR không phải là một SIEM hay SOAR khác, mà là một **lớp điều phối và phân tích thông minh**, kết nối các "đảo" công nghệ lại với nhau.

### Kiến trúc

Được xây dựng trên nền tảng **Python Flask**, SmartXDR bao gồm các module chức năng chuyên biệt:

| Module                                        | Chức năng                                                                                                                                                                                                                                                                                           |
| --------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SmartXDR Ingest Pipeline & Classification** | Tiếp nhận log đã chuẩn hóa, sau đó sử dụng mô hình Machine Learning Bylastic (dựa trên DistilBERT) để tự động phân loại log theo ba cấp độ nghiêm trọng: INFO, WARNING, ERROR. Cơ chế này giúp lọc nhiễu và ưu tiên các sự kiện quan trọng ngay từ đầu vào.                                         |
| **SmartXDR Analysis**                         | Là lõi phân tích chuyên sâu, sử dụng Mô hình Ngôn ngữ lớn (LLM) kết hợp kỹ thuật Retrieval-Augmented Generation (RAG) tiên tiến. Nó có khả năng "hiểu" ngữ cảnh hệ thống, tổng hợp thông tin từ nhiều nguồn, đánh giá rủi ro (Risk Score) và đưa ra các đề xuất điều tra cho chuyên viên phân tích. |
| **SmartXDR Reporting**                        | Tự động tạo báo cáo sự cố chi tiết hoặc báo cáo tổng quan định kỳ, sau đó phân phối qua email, Telegram và lưu trữ trên OneDrive.                                                                                                                                                                   |
| **SmartXDR Assistant**                        | Một trợ lý ảo an ninh mạng tương tác qua chatbot Telegram. Nó cho phép chuyên viên phân tích truy vấn dữ liệu bằng ngôn ngữ tự nhiên, yêu cầu tóm tắt sự cố, và ra lệnh thực thi các hành động phản ứng ngay trên giao diện chat, hiện thực hóa mô hình vận hành ChatOps.                           |


## 4. Luồng Xử lý Sự cố End-to-End

![SOC Pipeline](../img/KLTN_SOC-Ecosystem-Pipeline.drawio.png)

Hệ thống vận hành theo một quy trình xử lý **6 giai đoạn (pipeline)** khép kín, đảm bảo dữ liệu được xử lý một cách logic và hiệu quả.

### Phase 1: Thu thập Log (Log Collecting)
Elastic Agent thu thập log đa nguồn (network, endpoint, application), sau đó chuẩn hóa theo định dạng chung **Elastic Common Schema (ECS)**.

### Phase 2: Tiền xử lý Log (Log Pre-processing)
Log đi qua SmartXDR Ingest Pipeline để lọc nhiễu và tạo trường dữ liệu đầu vào (`ml_input`) cho mô hình ML.

### Phase 3: Phân loại và Làm giàu (Classification & Enrichment)
Module SmartXDR Classification sử dụng mô hình **Bylastic** để gán nhãn mức độ nghiêm trọng (INFO, WARNING, ERROR) và chỉ số tin cậy cho từng bản ghi log.

### Phase 4: Tạo và Tương quan Cảnh báo (Alert Generation & Correlation)
Elastic Detection Rules, được tăng cường bởi kết quả phân loại ML, sẽ kích hoạt cảnh báo khi phát hiện hành vi bất thường. **ElastAlert2** sau đó chuyển tiếp các cảnh báo này đến nền tảng SOAR.

### Phase 5: Làm giàu Ngữ cảnh và Ứng phó (Contextual Enrichment & Incident Response)
Cảnh báo được tự động tạo thành một hồ sơ sự cố (Case) trên **DFIR-IRIS**. Hệ thống tự động phân tích IoCs bằng IntelOwl/MISP và kích hoạt SmartXDR Analysis để cung cấp nhận định sâu hơn.

### Phase 6: Phản ứng Tự động và Báo cáo (Automated Response & Reporting)
Dựa trên phân tích, chuyên viên có thể kích hoạt các kịch bản phản ứng tự động qua **n8n** (ví dụ: chặn IP trên pfSense, cô lập máy chủ qua Wazuh). Đồng thời, SmartXDR Reporting tạo báo cáo sự cố và SmartXDR Assistant gửi thông báo tóm tắt qua Telegram.


## 5. Thực nghiệm và Đánh giá

### 5.1. Thiết lập Môi trường và Kịch bản Tấn công

Hệ thống được kiểm thử trong một môi trường lab ảo hóa, mô phỏng mạng doanh nghiệp với các phân vùng riêng biệt (LAN, GUEST, SOC). Năm kịch bản tấn công tiêu biểu được thực hiện, ánh xạ theo khung **MITRE ATT&CK**:

| Kịch bản                    | Mô tả                                                                             | MITRE Technique                           |
| --------------------------- | --------------------------------------------------------------------------------- | ----------------------------------------- |
| 1. Kết nối C2               | Mã độc ẩn trong file PDF giả mạo, thiết lập kết nối ra máy chủ điều khiển         | T1071.001 (Application Layer Protocol)    |
| 2. Tấn công SQL Injection   | Kẻ tấn công khai thác lỗ hổng SQLi trên ứng dụng web (DVWA) để truy cập CSDL      | T1190 (Exploit Public-Facing Application) |
| 3. Tấn công Brute-force SMB | Dò đoán mật khẩu liên tục vào dịch vụ SMB của Windows Server                      | T1110.003 (Password Spraying)             |
| 4. Thực thi Mã độc          | Người dùng bị lừa tải và chạy file chứa mã độc, Wazuh Agent phát hiện và xóa      | T1204 (User Execution)                    |
| 5. Vận hành ChatOps         | Chuyên viên tương tác với SmartXDR Assistant qua Telegram để điều tra và phản ứng | -                                         |

### 5.2. Kết quả Đánh giá

Kết quả thực nghiệm cho thấy hệ thống hoạt động hiệu quả và đáp ứng các mục tiêu đề ra.

**Bảng kết quả đo lường hiệu năng trung bình trong môi trường thí nghiệm:**

| Chỉ số KPI                     | Giá trị ghi nhận                                   | Nhận xét                                                                      |
| ------------------------------ | -------------------------------------------------- | ----------------------------------------------------------------------------- |
| **MTTD** (Thời gian phát hiện) | 5-15 giây (tấn công mạng), 15-30 giây (endpoint)   | Hệ thống phát hiện gần như thời gian thực nhờ pipeline xử lý liên tục         |
| **MTTR** (Thời gian phản ứng)  | 1-3 phút (với kịch bản tự động)                    | Tự động hóa giúp rút ngắn đáng kể thời gian ngăn chặn mối đe dọa              |
| **Giảm thiểu Cảnh báo giả**    | Giảm ~25-30% sau khi áp dụng phân loại ML          | Mô hình ML giúp ưu tiên các sự kiện có rủi ro cao, giảm nhiễu cho chuyên viên |
| **Độ chính xác Phát hiện**     | ~92% sự kiện tấn công được phát hiện đúng ngữ cảnh | Chất lượng quy tắc phát hiện và dữ liệu đầu vào là yếu tố then chốt           |

> [!TIP]
> So với mô hình SOC truyền thống, hệ thống đề xuất vượt trội nhờ khả năng phân tích ngữ nghĩa, tự động hóa phản ứng và tích hợp AI, chuyển từ thế bị động sang chủ động trong việc bảo vệ an ninh mạng.



## 6. Kết luận và Hướng phát triển

### 6.1. Kết luận

Khóa luận đã xây dựng thành công một **Hệ sinh thái SOC thông minh** toàn diện, khả thi và hiệu quả dựa trên các công nghệ mã nguồn mở. Việc giới thiệu kiến trúc SmartXDR như một lớp điều phối thông minh đã giải quyết được bài toán thiếu ngữ cảnh và quá tải cảnh báo của SOC truyền thống.

Mô hình này không chỉ chứng minh hiệu quả qua các chỉ số định lượng (MTTD, MTTR) mà còn cung cấp một tài liệu tham khảo giá trị cho các tổ chức muốn xây dựng năng lực giám sát an ninh mạng hiện đại với chi phí tối ưu.

### 6.2. Hạn chế

- Hệ thống được triển khai trong môi trường lab, chưa phản ánh đầy đủ quy mô và sự phức tạp của môi trường sản xuất
- Hiệu quả của mô hình ML phụ thuộc nhiều vào chất lượng và sự đa dạng của dữ liệu huấn luyện
- Việc tích hợp và vận hành nhiều công cụ mã nguồn mở đòi hỏi chuyên môn kỹ thuật cao

### 6.3. Hướng phát triển Tương lai

- **Tăng cường Năng lực AI/ML**: Mở rộng bộ dữ liệu huấn luyện với các kịch bản tấn công phức tạp hơn (APT, lẩn tránh) và cải tiến mô hình phân tích chuỗi sự kiện dài
- **Nâng cao Tự động hóa SOAR**: Xây dựng thêm các kịch bản (playbook) phản ứng phức tạp hơn, hướng tới tự động hóa hoàn toàn cho các mối đe dọa đã được định danh rõ ràng
- **Tối ưu hóa Hiệu năng SIEM**: Áp dụng các kỹ thuật quản lý vòng đời dữ liệu (ILM) và tối ưu hóa quy tắc tương quan để xử lý khối lượng log lớn hơn
- **Mở rộng sang Môi trường Cloud-Hybrid**: Thử nghiệm triển khai hệ thống trên các nền tảng đám mây để đánh giá khả năng mở rộng, chịu tải và giám sát các môi trường lai


## 7. Demo Hệ thống

> Xem Hệ sinh thái SOC Thông minh hoạt động thực tế thông qua các kịch bản tấn công mô phỏng

### Kịch bản 1: Kết nối C2 Outbound

Mô phỏng người dùng mở file PDF giả mạo trên máy trạm Windows 11 (192.168.85.150). File thực thi được ngụy trang với biểu tượng PDF, khởi chạy tiến trình nền và thiết lập kết nối beacon đến máy chủ Command & Control (C2) bên ngoài.

**[Xem Demo](https://youtu.be/XWCobYtvlBE)**


### Kịch bản 2: Tấn công SQL Injection

Trình diễn khai thác SQL Injection trên ứng dụng web DVWA phía sau ModSecurity WAF (192.168.85.111). Kẻ tấn công nhắm vào máy chủ cơ sở dữ liệu (192.168.85.112) để lộ dữ liệu nhạy cảm, thao túng bản ghi, hoặc leo thang đặc quyền.

**[Xem Demo](https://youtu.be/ajynzGKljqE)**



### Kịch bản 3: Brute-force NTLM over SMB

Kẻ tấn công từ mạng GUEST (192.168.95.100) sử dụng công cụ tự động để thực hiện brute-force mật khẩu vào dịch vụ SMB trên Windows Server (192.168.85.115), cố gắng vượt qua xác thực NTLM để di chuyển ngang (lateral movement).

**[Xem Demo](https://youtu.be/9ECwOKesfUM)**



### Kịch bản 4: Thực thi Mã độc

Mô phỏng tấn công social engineering khi người dùng tải về file cài đặt MediaPlayer giả (.zip) chứa mã độc. Wazuh Agent phát hiện mối đe dọa qua tích hợp VirusTotal, tự động xóa file độc hại và báo cáo lên hệ thống trung tâm.

**[Xem Demo](https://youtu.be/_wuKYfO0x_4)**



### Kịch bản 5: SmartXDR ChatOps

Trình diễn quy trình ChatOps thông qua tích hợp Telegram. Quản trị viên tương tác hai chiều với SmartXDR qua chatbot, yêu cầu phân tích sự cố bằng AI hoặc thực thi các hành động phản ứng trực tiếp từ nền tảng nhắn tin.

**[Xem Demo](https://youtu.be/2qiM6oSsuOg)**


## 8. Tài nguyên

> Truy cập video demo và tài liệu dự án

| Tài nguyên              | Mô tả                                    | Liên kết                                                                                           |
| ----------------------- | ---------------------------------------- | -------------------------------------------------------------------------------------------------- |
| **GitHub Organization** | Khám phá mã nguồn và các repository      | [Cyberfortress-Labs](https://github.com/Cyberfortress-Labs)                                        |
| **YouTube Playlist**    | Xem tất cả video demo trong một playlist | [Watch Playlist](https://www.youtube.com/playlist?list=PLshy_4RvrXflyh76ppECu-9yAvTgE2h3d)         |
| **Google Drive**        | Truy cập tài liệu và tập tin bổ sung     | [Open Drive](https://drive.google.com/drive/folders/1TnPI0jEuVOhzjaLv8Pt6tsFN6O2h-dR9?usp=sharing) |


## Nhóm Thực hiện

- **Lại Quan Thiên** - [WanThinnn](https://github.com/WanThinnn)
- **Hồ Diệp Huy** - [hohuyy](https://github.com/hohuyy)
