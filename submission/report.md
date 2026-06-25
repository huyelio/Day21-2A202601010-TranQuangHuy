# Báo Cáo Lab MLOps - Day 21

Họ tên: Trần Quang Huy

MSSV: 2A202601010

Repo GitHub: `<điền URL repo GitHub của bạn>`

Cloud provider: AWS

## 1. Mục Tiêu

Lab này xây dựng một pipeline MLOps cho bài toán phân loại Wine Quality. Hệ thống bao gồm thực nghiệm cục bộ bằng MLflow, quản lý phiên bản dữ liệu bằng DVC với AWS S3, pipeline CI/CD bằng GitHub Actions, và triển khai model lên EC2 bằng FastAPI.

## 2. Thực Nghiệm Cục Bộ Với MLflow

Em đã chạy nhiều lần thực nghiệm với `RandomForestClassifier` và theo dõi các lần chạy bằng MLflow. Mỗi run ghi lại các tham số huấn luyện và hai chỉ số đánh giá:

- `accuracy`
- `f1_score`

Bộ tham số được chọn:

```yaml
n_estimators: 300
max_depth: 20
min_samples_split: 2
class_weight: balanced
```

Lý do chọn: đây là bộ tham số cho kết quả tốt nhất trong các lần thử nghiệm cục bộ trên tập eval của lab.

## 3. Quản Lý Dữ Liệu Với DVC Và AWS S3

Em sử dụng DVC để quản lý các file dữ liệu:

- `data/train_phase1.csv`
- `data/eval.csv`
- `data/train_phase2.csv`

DVC remote được cấu hình trên AWS S3:

```text
s3://mlops-lab-huy-2026/dvc
```

Sau khi cấu hình remote, em đã chạy `dvc push` để đưa dữ liệu lên S3. Git chỉ commit các file `.dvc`, không commit trực tiếp file CSV.

## 4. Pipeline CI/CD

Pipeline GitHub Actions gồm bốn job:

1. `Unit Test`: chạy unit tests trong thư mục `tests/`.
2. `Train`: pull dữ liệu từ S3 bằng DVC, train model, ghi `outputs/metrics.json`, upload model lên S3.
3. `Eval`: kiểm tra `accuracy >= 0.70`.
4. `Deploy`: SSH vào EC2, restart service FastAPI và health check.

Ở lần train đầu tiên, model đạt accuracy khoảng `0.6860`, nhỏ hơn ngưỡng `0.70`, nên job `Eval` dừng deploy. Đây là hành vi đúng của eval gate.

## 5. Huấn Luyện Liên Tục Với Dữ Liệu Mới

Em chạy script:

```bash
python add_new_data.py
```

Script này ghép `train_phase2.csv` vào `train_phase1.csv`, tăng số mẫu train từ 2998 lên 5996. Sau đó em cập nhật DVC:

```bash
dvc add data/train_phase1.csv
dvc push
git push origin main
```

Commit dữ liệu mới kích hoạt lại pipeline. Sau khi train với dữ liệu mới, model vượt qua eval gate và được deploy lên EC2.

## 6. Triển Khai Và Kiểm Thử API

Model mới nhất được upload lên:

```text
s3://mlops-lab-huy-2026/models/latest/model.pkl
```

EC2 chạy FastAPI service `mlops-serve`, cung cấp các endpoint:

- `GET /health`
- `POST /predict`

Kết quả kiểm tra:

```bash
curl http://<EC2_PUBLIC_IP>:8000/health
```

Trả về:

```json
{"status":"ok"}
```

```bash
curl -X POST http://<EC2_PUBLIC_IP>:8000/predict \
  -H "Content-Type: application/json" \
  -d '{"features": [7.4, 0.70, 0.00, 1.9, 0.076, 11.0, 34.0, 0.9978, 3.51, 0.56, 9.4, 0]}'
```

Trả về JSON có dạng:

```json
{"prediction": <0|1|2>, "label": "<thap|trung_binh|cao>"}
```

## 7. Kết Quả Cuối Cùng

Điền từ artifact `metrics.json` của GitHub Actions run cuối cùng:

```text
accuracy = <điền accuracy>
f1_score = <điền f1_score>
```

## 8. Bằng Chứng Kèm Theo

Ảnh minh chứng nằm trong thư mục `submission/screenshots/`:

- `01-mlflow-runs.png`
- `02-github-actions-success.png`
- `03-s3-dvc-data.png`
- `04-s3-latest-model.png`
- `05-curl-health.png`
- `06-curl-predict.png`
- `07-data-commit-actions.png`

