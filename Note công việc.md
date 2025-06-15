# 16/6/2025

 - [x] 1.  Set up PEP8 cho project
 - [x] 2. Set up CI/CD
 - [ ] 3. Viết luồng logic đồng bộ dữ liệu cold và warm
 - [x]  4. Tìm hiểu Dagster, APScheduler thay cho Airflow
 - [ ] 5. Thêm retry và timeout cho task worker (cân nhắc thêm circle breaker khi API sportmonk bị lỗi)

***Note prefect***
```bash
pip install 'prefect>=2.14.0,<2.15'
pip install "griffe<0.37" --force-reinstall
```

***Sử dụng prefect có vẻ ngon hơn***
```bash
match_scheduler/
├── flows/
│   └── monitor_match.py       # Flow xử lý từng trận đấu
├── deploy_match.py            # Script tạo dynamic deployment
├── deploy_match.py            # Script tạo dynamic deployment
├── requirements.txt
└── prefect.yaml               # (auto-generated nếu dùng `prefect init`)

```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM4MzE3MzM5OV19
-->