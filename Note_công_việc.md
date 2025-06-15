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
├── cancel_match.py            # Script tạo dynamic deployment
├── requirements.txt
└── prefect.yaml               # (auto-generated nếu dùng `prefect init`)
```

```python
# monitor_match.py
from prefect import flow, task
from datetime import datetime, timedelta
import asyncio

@task(retries=3, retry_delay_seconds=10)
def check_match_status(match_id: str):
    print(f"⏱️ Checking status of match {match_id} at {datetime.now()}")
    # Giả lập lỗi ngẫu nhiên hoặc call API thật
    # raise Exception("Temporary error")  # test retry
    return "ongoing"

@flow(name="monitor_match_flow")
async def monitor_match_flow(match_id: str, end_time: str):
    end_dt = datetime.fromisoformat(end_time)

    while datetime.now() < end_dt:
        status = check_match_status(match_id)
        await asyncio.sleep(60)  # mỗi phút kiểm tra 1 lần

    print(f"✅ Match {match_id} finished at {datetime.now()}")
```
```python
# cancel_match.py
import asyncio
from prefect.client import get_client

DEPLOYMENT_ID = "3b1b302d-e14a-4831-a103-8de9f101a844"  # thay bằng UUID thật, ví dụ: "3b1b302d-e14a-4831-a103-8de9f101a844"

async def delete_deployment_by_id(deployment_id: str):
    async with get_client() as client:
        await client.delete_deployment(deployment_id)
        print(f"🗑️ Deleted deployment with id: {deployment_id}")

if __name__ == "__main__":
    asyncio.run(delete_deployment_by_id(DEPLOYMENT_ID))
```
```python

```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA4NTA3MTg5MiwzNzQ0NzA4NDhdfQ==
-->