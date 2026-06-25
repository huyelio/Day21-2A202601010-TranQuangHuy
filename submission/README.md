# Hướng Dẫn Nộp Bài

Thư mục này dùng để gom bằng chứng nộp lab.

## 1. Ảnh Cần Chụp

Lưu các ảnh vào `submission/screenshots/` theo đúng tên sau:

1. `01-mlflow-runs.png`
   - Nội dung: MLflow UI hiển thị nhiều runs.
   - Cần thấy cột `accuracy`, `f1_score`, và params như `n_estimators`, `max_depth`.

2. `02-github-actions-success.png`
   - Nội dung: GitHub Actions run cuối cùng thành công.
   - Cần thấy các job `Unit Test`, `Train`, `Eval`, `Deploy` đều màu xanh.

3. `03-s3-dvc-data.png`
   - Nội dung: AWS S3 bucket `mlops-lab-huy-2026`.
   - Cần thấy dữ liệu DVC trong prefix `dvc/`.

4. `04-s3-latest-model.png`
   - Nội dung: AWS S3 bucket `mlops-lab-huy-2026`.
   - Cần thấy file `models/latest/model.pkl`.

5. `05-curl-health.png`
   - Nội dung: terminal chạy `curl http://<EC2_PUBLIC_IP>:8000/health`.
   - Kết quả cần thấy: `{"status":"ok"}`.

6. `06-curl-predict.png`
   - Nội dung: terminal chạy request `/predict`.
   - Kết quả cần thấy JSON có `prediction` và `label`.

7. `07-data-commit-actions.png`
   - Nội dung: GitHub Actions run được kích hoạt bởi commit thêm dữ liệu phase2.
   - Cần thấy commit message dạng như `data: add phase2 training data`.

## 2. File Artifact Cần Lưu

Tải artifact `metrics` từ GitHub Actions run cuối cùng, giải nén và lưu vào:

```text
submission/artifacts/metrics.json
```

Sau đó mở file này, copy giá trị `accuracy` và `f1_score` vào `submission/report.md`.

## 3. Lệnh Test API

Thay `<EC2_PUBLIC_IP>` bằng IP thật của EC2:

```bash
curl http://<EC2_PUBLIC_IP>:8000/health
```

```bash
curl -X POST http://<EC2_PUBLIC_IP>:8000/predict \
  -H "Content-Type: application/json" \
  -d '{"features": [7.4, 0.70, 0.00, 1.9, 0.076, 11.0, 34.0, 0.9978, 3.51, 0.56, 9.4, 0]}'
```

## 4. Gợi Ý Cách Chụp Màn Hình

Trên Windows:

- Dùng `Win + Shift + S`.
- Chọn vùng cần chụp.
- Mở Paint hoặc ứng dụng bất kỳ, paste ảnh, save vào `submission/screenshots/`.

Trên Ubuntu/WSL desktop nếu có giao diện:

- Dùng phím `PrtSc` hoặc app Screenshot.
- Save vào `submission/screenshots/`.

Nếu chụp terminal, nên phóng to terminal để thấy rõ lệnh và kết quả.

