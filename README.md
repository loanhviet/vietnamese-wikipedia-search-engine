# Hệ thống tìm kiếm Wikipedia tiếng Việt

Ứng dụng **Streamlit** tìm kiếm toàn văn và phân tích trên dữ liệu Wikipedia, kết hợp **Hadoop MapReduce** (xử lý trên HDFS) và **Elasticsearch** (lưu trữ, truy vấn).

## Tính năng

| Trang | Mô tả |
|--------|--------|
| **Search** | Tìm kiếm theo tiêu đề / nội dung |
| **ES Stats** | Thống kê index Elasticsearch |
| **Trend Analysis** | Xu hướng từ khóa theo thời gian |
| **WordCount** | Tần suất từ khóa |
| **Categories** | Phân tích theo danh mục |
| **Info** | Kiến trúc, workflow, trạng thái cluster |

## Yêu cầu

- **Python** 3.9+ (khuyến nghị 3.10+)
- **Elasticsearch** 8.x (mặc định `http://localhost:9200` — cấu hình trong `utils/es_client.py`)
- **Hadoop** + HDFS (khi chạy pipeline MapReduce và script index lấy dữ liệu từ HDFS)

## Cài đặt

```bash
git clone https://github.com/loanhviet/bigdata_project.git
cd bigdata_project
python -m venv venv

# Windows
venv\Scripts\activate

# Linux / macOS
source venv/bin/activate

pip install -r requirements.txt
```

Khởi động Elasticsearch và đảm bảo cluster sẵn sàng trước khi chạy app hoặc script index.

## Chuẩn bị dữ liệu và index

1. Tải dump Wikipedia tiếng Việt, đưa lên HDFS (xem chi tiết trên trang **Info** trong app hoặc `pages/06_Info.py`).
2. Chạy các job MapReduce trong thư mục `mapreduce/` (streaming Hadoop) theo pipeline của đề tài.
3. Nạp dữ liệu vào Elasticsearch (script đọc kết quả trên HDFS và tạo các index `wiki_docs`, `wiki_wordcount`, `wiki_trend`, …):

```bash
python elasticsearch/index_all_data.py
```

> **Lưu ý:** Script index giả định dữ liệu đã có trên HDFS đúng đường dẫn trong code (ví dụ `/data/wiki/clean/docs/part-*`). Chỉnh sửa `elasticsearch/index_all_data.py` nếu layout HDFS khác.

## Chạy ứng dụng web

Từ thư mục gốc dự án:

```bash
streamlit run streamlit_app.py
```

Trình duyệt mở giao diện nhiều trang; sidebar chọn từng chức năng.

## Cấu trúc thư mục

```
bd-project/
├── streamlit_app.py      # Trang chủ Streamlit
├── pages/                # Các trang con (Search, Stats, …)
├── utils/
│   └── es_client.py      # Client Elasticsearch dùng chung
├── elasticsearch/
│   ├── index_all_data.py # Tạo index & bulk dữ liệu
│   └── requirements.txt  # Bộ phụ thuộc tối thiểu (tham khảo)
├── mapreduce/            # Mapper / reducer Hadoop Streaming
├── tools/                # Tiện ích (ví dụ chuyển XML)
└── data/                 # Dữ liệu cục bộ (file lớn thường được .gitignore)
```

## Giấy phép và đề tài

Dự án phục vụ môn **Big Data**; dữ liệu Wikipedia tuân theo điều khoản sử dụng của Wikimedia Foundation.
