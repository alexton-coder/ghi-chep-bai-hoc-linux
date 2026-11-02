# Ghi chép bài học trên Linux — Hermes

Skill độc lập **`ghi-chep-bai-hoc-linux`** cho Hermes: nghe bài học cục bộ,
đối chiếu phụ đề/slide/demo và ghi HTML tiếng Việt theo diễn biến. Giữ các
bước thực hành, lần sửa, kết quả và phần còn thiếu. Không cần cài skill gốc.

## Tải và cài

[Tải gói ghi-chep-bai-hoc-linux.zip](./ghi-chep-bai-hoc-linux.zip?raw=true)

[Checksum SHA-256](./SHA256SUMS.txt)

```text
4ecfacd2a6645020545e5b15cbe4f05764d9e7da19243b369cb231031576404a
```

ZIP chứa thư mục `ghi-chep-bai-hoc-linux/`, gồm `SKILL.md`, hai tài liệu
trong `references/` và hai chương trình trong `scripts/`.
Giải nén và cài **cả thư mục**, không chỉ riêng SKILL.md. Đây là gói ZIP
để Hermes tải/giải nén rồi cài bằng cơ chế local skill của phiên đang chạy;
không giả định lệnh cài trực tiếp từ repository tự hiểu file ZIP.

Đọc README/SKILL.md và quét an toàn trước khi cài. Xác định đúng profile và
kho skill Hermes; sao lưu nếu đã có bản cùng tên. Không ghi đè skill cũ
`ghi-chep-bai-hoc`. Không dùng force hoặc tắt bộ kiểm tra an toàn.

## Prompt gửi Hermes

> Cài skill riêng `ghi-chep-bai-hoc-linux` từ repository
> https://github.com/alexton-coder/ghi-chep-bai-hoc-linux . Đọc README, tải
> ZIP và đối chiếu SHA-256 trong SHA256SUMS.txt; giải nén an toàn, giữ đủ
> SKILL.md, references và scripts. Đọc hướng dẫn và kiểm tra mã bằng cơ chế
> an toàn của Hermes, rồi cài vào đúng kho skill của profile đang chạy.
> Sao lưu bản cùng tên nếu có, giữ nguyên skill ghi-chep-bai-hoc cũ. Kiểm
> tra phiên hiện tại nhận được skill; nếu cần tải lại phiên thì báo rõ.
> Báo đường dẫn thực tế, hệ điều hành, công cụ/model còn thiếu và khả năng
> truy cập phiên âm thanh. Trong bước cài chỉ kiểm tra chỉ đọc, chưa bắt
> đầu ghi âm, đổi đường tiếng, tải model hoặc cài dependency. Không dùng
> force hay vô hiệu hóa kiểm tra. Phân biệt cài thành công với đã thử thu
> live thành công; nếu bị chặn, báo đúng bước và lý do.

## Đường âm thanh Linux

Ứng dụng bài học → sink riêng → monitor riêng → FFmpeg chia WAV liên tục
→ Whisper.cpp tiếng Việt + Silero VAD → Hermes đọc/đối chiếu → HTML.

- **Live:** Linux có PulseAudio hoặc PipeWire + `pipewire-pulse`, pactl hỗ
  trợ JSON, Python 3.9+, FFmpeg có input pulse, ffprobe, whisper-cli và model
  Whisper/Silero có sẵn. PipeWire thuần không có backend trực tiếp trong gói.
- **File:** `transcribe_file.py` đọc file audio/video được cung cấp hợp lệ;
  không cần pactl hoặc quyền thu trực tiếp. CPU mặc định; GPU tùy chọn.
- Hermes phải kết nối được audio server của đúng phiên đang phát bài.
  Hermes trên Linux khác máy không tự nghe được Zoom đang chạy trên Mac.
- `listen_linux.py --check` là kiểm tra chỉ đọc, không định tuyến hay thu.
  `--start` mới tạo sink tạm, chuyển **đúng sink-input đã chọn**, thu và
  khôi phục. Không thay default sink, không lấy mic hoặc video.
- Có tùy chọn loopback đến loa/tai nghe đã xác minh để người dùng nghe cùng.
  Không hứa cô lập tab chỉ từ tên/PID trình duyệt. Có độ trễ kiểm tra nguồn.
- `STOP` ở output gốc dừng recorder và nhận dạng con, giữ audio, khôi phục
  luồng hợp lệ, chỉ gỡ module tự tạo. Phải đọc trạng thái/cleanup_issues;
  không gọi đã khôi phục hết nếu còn lỗi. SIGKILL/mất điện cần kiểm tra lại
  tài nguyên từ routing.json/ledger, không gỡ mù theo ID cũ.

Helper không tự cài/tải gì, gọi API ngoài, tham gia lớp hay gửi host.
TXT/SRT không tự thành ghi chép: Hermes phải đọc và cập nhật HTML.
Xem `references/linux-audio.md` trong ZIP để vận hành và nghiệm thu.

## Đã kiểm tra gì?

Ngày 02/11/2026, trên máy macOS làm gói:

- 10 kiểm tra đạt: logic phục hồi/nhận dạng luồng, tránh thay đổi luồng lạ,
  giữ sink khi không thể khôi phục, đọc CSV đoạn đã đóng, kiểm tra chỉ đọc,
  FFmpeg thật chia file âm thanh tổng hợp, silence không gọi Whisper,
  STOP hủy tác vụ, backlog dừng thu và giữ audio.
- Audio server được mô phỏng; kiểm tra nhận dạng dùng công cụ giả cho luồng
  điều phối. **Chưa kiểm tra live trên Linux và chưa xác minh chất lượng
  nhận dạng tiếng Việt/model trên máy Hermes.** Trước buổi thật phải thử
  nguồn ngắn và đối chiếu lời/slide, độ trễ, tiếng nghe cùng và cleanup.
- YAML frontmatter, cú pháp Python và liên kết nội bộ được kiểm tra. Bộ
  quick_validate của Codex không chạy được vì môi trường thiếu PyYAML;
  frontmatter được kiểm tra riêng bằng trình đọc YAML có sẵn.

Gói không chứa audio, video lớp học, model, binary, thông tin đăng nhập
hoặc đường dẫn người dùng. Bản skill gốc và repository gốc được giữ nguyên.

## Nguồn kỹ thuật

- [PulseAudio modules](https://wiki.freedesktop.org/www/Software/PulseAudio/Documentation/User/Modules/)
- [PipeWire PulseAudio compatibility](https://pipewire.pages.freedesktop.org/pipewire/page_man_pipewire-pulse_1.html)
- [FFmpeg pulse input](https://ffmpeg.org/ffmpeg-devices.html#pulse)
- [FFmpeg segment muxer](https://ffmpeg.org/ffmpeg-formats.html#segment)
