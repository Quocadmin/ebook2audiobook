# ebook2audiobook — Chuyển eBook thành audiobook có chương & metadata 📚🎙️

## Mô tả ngắn

Công cụ chuyển eBook thành audiobook (có phân chương và metadata). Hỗ trợ nhiều TTS engine: XTTSv2, Bark, VITS, Fairseq, YourTTS, Tacotron… Có cloning giọng và hỗ trợ hơn 1110 ngôn ngữ (mặc định tiếng Anh).

## Lưu ý pháp lý (Important)

* Chỉ sử dụng với eBook hợp pháp, không DRM.
* Tác giả không chịu trách nhiệm nếu dùng sai mục đích hoặc vi phạm pháp luật.
* Sử dụng tuân thủ luật hiện hành.

## Tính năng chính

* Tự tách eBook theo chương để dễ nghe và quản lý.
* TTS chất lượng cao (Coqui XTTSv2, Fairseq, …).
* Tuỳ chọn voice cloning từ file giọng mẫu.
* Hỗ trợ +1110 ngôn ngữ (tiếng Việt: `vi`).
* Chạy được trên máy 4GB RAM (khuyến nghị 8GB).

## Ngôn ngữ hỗ trợ (trích)

ar, zh, en, es, fr, de, it, pt, pl, tr, ru, nl, cs, ja, hi, bn, hu, ko, vi, sv, fa, yo, sw, id, sk, hr, ta, da … và hơn 1100 ngôn ngữ/biến thể.

## Yêu cầu phần cứng

* Tối thiểu 4GB RAM (khuyến nghị 8GB).
* CPU: Intel/AMD/ARM; GPU: NVIDIA/AMD*/Intel* (khuyến nghị); Apple Silicon MPS.
* Windows dùng Docker cần bật ảo hoá.
  (* một số GPU sắp hỗ trợ/tuỳ bản build)

## Cài đặt & chạy nhanh

### Cách 1: Gradio Web (khuyến nghị)

```bash
git clone https://github.com/DrewThomasson/ebook2audiobook.git
cd ebook2audiobook
# Linux/Mac
./ebook2audiobook.sh
# macOS (double-click)
Mac Ebook2Audiobook Launcher.command
# Windows
ebook2audiobook.cmd
```

Mở trình duyệt tại: [http://localhost:7860/](http://localhost:7860/)
Chia sẻ public link: thêm `--share` (mọi HĐH).

> Lưu ý: nếu dừng script và chạy lại, hãy refresh trang Gradio để kết nối socket mới.

### Cách 2: Chạy headless (CLI)

```bash
# Linux/Mac
./ebook2audiobook.sh --headless --ebook <đường_dẫn_ebook> \
  --voice <đường_dẫn_file_giọng_tùy_chọn> --language <mã_ngôn_ngữ>

# Windows
ebook2audiobook.cmd --headless --ebook <đường_dẫn_ebook> \
  --voice <đường_dẫn_file_giọng_tùy_chọn> --language <mã_ngôn_ngữ>
```

* `--ebook`: đường dẫn file eBook
* `--voice`: file giọng để clone (tùy chọn)
* `--language`: mã ISO-639-3 (vd: `eng`, `ita`, `deu`) hoặc ISO-639-1 (`en`, `it`, `de`). Mặc định `eng`.

### Tải mô hình TTS tuỳ chỉnh (ví dụ XTTSv2, .zip)

Bên trong zip phải có: `config.json`, `model.pth`, `vocab.json`, `ref.wav`

```bash
# Linux/Mac
./ebook2audiobook.sh --headless --ebook <ebook> \
  --voice <voice.wav> --language <lang> --custom_model <path/to/model.zip>

# Windows
ebook2audiobook.cmd --headless --ebook <ebook> \
  --voice <voice.wav> --language <lang> --custom_model <path/to/model.zip>
```

### Trợ giúp (liệt kê tham số)

```bash
# Linux/Mac
./ebook2audiobook.sh --help
# Windows
ebook2audiobook.cmd --help
# Hoặc mọi HĐH
python app.py --help
```

Các tham số đáng chú ý (trích):

* `--device {cpu,gpu,mps}` chọn thiết bị xử lý
* `--tts_engine {XTTSv2,BARK,VITS,FAIRSEQ,TACOTRON2,YOURTTS}`
* `--output_format {m4b,m4a,mp4,webm,mov,mp3,flac,wav,ogg,aac}`
* Tham số XTTSv2: `--temperature`, `--length_penalty`, `--num_beams`, `--repetition_penalty`, `--top_k`, `--top_p`, `--speed`, `--enable_text_splitting`
* Tham số Bark: `--text_temp`, `--waveform_temp`
* `--ebooks_dir` để xử lý cả thư mục
* `--output_dir` để chọn thư mục xuất

Mẹo dừng/ngắt: trong giao diện Gradio, bấm [X] tại ô upload eBook.
Mẹo chèn ngắt nghỉ 1.4s: chèn `###` hoặc `[pause]` trong văn bản.

## Docker

### Chạy ảnh dựng sẵn

```bash
# CPU
docker run --pull always --rm -p 7860:7860 athomasson2/ebook2audiobook
# GPU NVIDIA
docker run --pull always --rm --gpus all -p 7860:7860 athomasson2/ebook2audiobook
```

Mặc định mở Gradio tại `localhost:7860`.

### Build thủ công

```bash
docker build -t athomasson2/ebook2audiobook .
# Tham số build:
# --build-arg TORCH_VERSION={cuda121|cuda118|cuda128|rocm|xpu|cpu}
# --build-arg SKIP_XTTS_TEST=true  # giảm kích thước image
```

### Headless trong Docker (mount I/O)

```bash
mkdir input-folder && mkdir Audiobooks
docker run --pull always --rm \
  -v $(pwd)/input-folder:/app/input_folder \
  -v $(pwd)/audiobooks:/app/audiobooks \
  athomasson2/ebook2audiobook \
  --headless --ebook /input_folder/YOUR_EBOOK_FILE
```

### Docker Compose

* Sửa `docker-compose.yml`: đổi giữa `*gpu-disabled` ↔ `*gpu-enabled`.
* Chạy:

```bash
docker-compose up -d           # Docker
podman compose -f podman-compose.yml up -d
```

Truy cập: [http://localhost:7860](http://localhost:7860)

### Sự cố Docker thường gặp (trích)

* Không nhận GPU NVIDIA → xem GPU ISSUES Wiki.
* Bị kẹt tải mô hình fine-tuned: thử tắt progress bar:

```bash
docker run --pull always --rm --gpus all \
  -e HF_HUB_DISABLE_PROGRESS_BARS=1 -e HF_HUB_ENABLE_HF_TRANSFER=0 \
  -p 7860:7860 athomasson2/ebook2audiobook
```

## Định dạng eBook vào

`.epub, .pdf, .mobi, .txt, .html, .rtf, .chm, .lit, .pdb, .fb2, .odt, .cbr, .cbz, .prc, .lrf, .pml, .snb, .cbc, .rb, .tcr`
Khuyến nghị: `.epub` hoặc `.mobi` để tự phát hiện chương tốt nhất.

## Định dạng âm thanh xuất

Tạo file kèm metadata & chương: `m4b, m4a, mp4, webm, mov, mp3, flac, wav, ogg, aac` (cấu hình trong `./lib/conf.py`).

## Cập nhật/rollback

```bash
# Cập nhật
git pull
docker pull athomasson2/ebook2audiobook:latest

# Quay về bản cũ (ví dụ v25.7.7)
git checkout tags/v25.7.7
# Ảnh prebuild:
athomasson2/ebook2audiobook:v25.7.7
```

## Lưu ý & kinh nghiệm

* Trước khi tạo issue, hãy tìm kỹ issues mở/đã đóng xem đã có chưa.
* Chuẩn văn bản eBook không thống nhất (chương, lời nói đầu…), hãy xoá thủ công phần không muốn đọc.
* CPU có thể chậm; GPU NVIDIA gần thời gian thực.
* Lỗi audio bị cắt/truncation: vui lòng tạo issue để cộng đồng tinh chỉnh tách câu.

## Demo & GUI

* Web GUI: `demo_web_gui` (xem hình minh hoạ trong repo).
* Demo giọng mặc định mới: Sherlock.mp4 (trong repo).

## Cảm ơn & Ghi công

* Coqui TTS, Calibre, FFmpeg.
* @shakenbake15: cải tiến lưu chương.

## Nguồn

Nội dung do bạn cung cấp từ mô tả dự án ebook2audiobook (GitHub: DrewThomasson/ebook2audiobook).
