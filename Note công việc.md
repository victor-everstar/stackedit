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

@flow(name="monitor_match_flow_vuotnh")
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
from prefect.client import get_client
from datetime import datetime, timedelta
from prefect.server.schemas.schedules import IntervalSchedule
import asyncio
from prefect.states import Scheduled

MATCH_ID = "match_123"
START_TIME = datetime.utcnow() + timedelta(seconds=10)
END_TIME = START_TIME + timedelta(minutes=5)

async def create_deployment():
    from prefect.deployments import Deployment
    from flows.monitor_match import monitor_match_flow

    schedule = IntervalSchedule(
        interval=timedelta(seconds=10),                # Lặp lại mỗi 1 phút
        anchor_date=START_TIME                        # Bắt đầu từ thời điểm này
    )

    deployment = await Deployment.build_from_flow(
        flow=monitor_match_flow,
        name=f"monitor-{MATCH_ID}",
        parameters={"match_id": MATCH_ID, "end_time": END_TIME.isoformat()},
        schedule=schedule,
        work_queue_name="default",
    )
    await deployment.apply()

    async with get_client() as client:
        registered_deployment = await client.read_deployment_by_name(
            name=f"monitor_match_flow_vuotnh/monitor-{MATCH_ID}"
        )

        print(registered_deployment.id)

        print(f"✅ Flow run scheduled at {START_TIME} for match {MATCH_ID}")

if __name__ == "__main__":
    asyncio.run(create_deployment())
```

### 1. Cài đặt
```bash
python -m venv venv && source venv/bin/activate
pip install 'prefect>=2.14.0,<2.15'
pip install "griffe<0.37" --force-reinstall
pip install -r requirements.txt
```

### 2. Khởi tạo project Prefect
```bash
prefect init
```

### 3. Chạy Prefect server + agent (cửa sổ riêng)
```bash
prefect server start

# (Cửa sổ khác)
prefect agent start --pool default-agent-pool
```

### 4. Tạo deployment và lên lịch trận đấu
```bash
python deploy_match.py
```

# 17/6/2025

 - [ ] 1. Thêm retry và timeout cho task worker (cân nhắc thêm circle breaker khi API sportmonk bị lỗi)
```python
import asyncio
import logging

MAX_PROCESS_RETRIES = 3
RETRY_DELAY_SECONDS = 2

async def process_task(self, task: Dict[str, Any]) -> bool:
    """Process a Sportmonks task with retry"""
    for attempt in range(1, MAX_PROCESS_RETRIES + 1):
        try:
            task_data = task['data']
            provider = ast.literal_eval(task_data.get('provider'))
            provider_secret_token = provider.get('secret_token', "")
            provider_endpoint = provider.get('base_endpoint', "")
            entity_type = task_data.get('entity_type')

            if not entity_type:
                raise ValueError("Entity type not specified in task")

            process_data_method_name = f"_process_data_{entity_type}"
            process_data_method = getattr(self.api_processor, process_data_method_name, None)
            if not process_data_method:
                process_data_method = getattr(self, "_process_data_default", None)

            all_data = await process_data_method(
                base_endpoint=provider_endpoint,
                provider_secret_token=provider_secret_token,
                entity_type=entity_type
            )
            await self._store_data(entity_type, {"data": all_data})
            return True

        except (httpx.RequestError, httpx.HTTPStatusError, ConnectionError) as e:
            self.logger.warning(f"[Attempt {attempt}] Temporary error: {e}")
            if attempt == MAX_PROCESS_RETRIES:
                break
            await asyncio.sleep(RETRY_DELAY_SECONDS * attempt)

        except Exception as e:
            self.logger.error(f"Non-retryable error processing task: {e}")
            break  # Không retry các lỗi không mong muốn (logic/code lỗi)

    return False
```
```python
async def _sportmonk_client(self, endpoint: str, provider_secret_token: str, custom_params: Dict[str, str] = {}) -> Dict[str, Any]:
        if self.circuit_open_until:
            now = asyncio.get_event_loop().time()
            if now < self.circuit_open_until:
                raise RuntimeError("Circuit breaker is open. Skipping request.")
            else:
                self.failure_count = 0
                self.circuit_open_until = None

        headers = {'Authorization': provider_secret_token}
        page = 1
        all_data = []
        params = {
            "page": page,
            "per_page": DEFAULT_PER_PAGE_SPORTMONK,
        }
        final_params = {**params, **custom_params}

        try:
            async with httpx.AsyncClient(timeout=DEFAULT_API_TIMEOUT_SPORTMONK, headers=headers) as client:
                while True:
                    final_params['page'] = page
                    response = await self._request_with_retry(client, endpoint, final_params)
                    result = response.json()

                    data = result.get("data", [])
                    all_data.extend(data)

                    pagination = result.get("pagination", {})
                    next_page = pagination.get('next_page', None)
                    if not next_page:
                        break

                    page += 1
            return all_data

        except Exception as e:
            self.failure_count += 1
            logger.error(f"API call failed ({self.failure_count}): {e}")
            if self.failure_count >= CIRCUIT_BREAKER_THRESHOLD:
                self.circuit_open_until = asyncio.get_event_loop().time() + CIRCUIT_BREAKER_TIMEOUT
                logger.warning(f"Circuit breaker activated for {CIRCUIT_BREAKER_TIMEOUT} seconds.")
            raise

    async def _request_with_retry(self, client: httpx.AsyncClient, url: str, params: Dict[str, Any]) -> httpx.Response:
        for attempt in range(1, MAX_RETRIES + 1):
            try:
                response = await client.get(url, params=params)
                response.raise_for_status()
                return response
            except (httpx.RequestError, httpx.HTTPStatusError) as e:
                logger.warning(f"Attempt {attempt} failed: {e}")
                if attempt == MAX_RETRIES:
                    raise
                await asyncio.sleep(RETRY_BACKOFF * attempt)
```
```python
from aiobreaker import CircuitBreaker

breaker = CircuitBreaker(fail_max=3, reset_timeout=60)  # 3 lần lỗi, chờ 30s

class SportmonksAPIProcessor:
    ...
    @breaker
    async def _sportmonk_client(self, ...):
        ...
```
```bash
pip install aiobreaker tenacity
```
```python
import httpx
import logging
from typing import Dict, Any
from aiobreaker import CircuitBreaker
from tenacity import retry, stop_after_attempt, wait_exponential, retry_if_exception_type

from prisma.client import Prisma
from common.constants import (
    SPORTMONKS_ENDPOINT_PATH_MAPPING,
    DEFAULT_PER_PAGE_SPORTMONK,
    DEFAULT_API_TIMEOUT_SPORTMONK
)

# Logger
logger = logging.getLogger("SportmonksAPIProcessor")

# Circuit Breaker config: max 3 lỗi → mở trong 60s
breaker = CircuitBreaker(fail_max=3, reset_timeout=60)

class SportmonksAPIProcessor:

    def __init__(self, prisma_client: Prisma):
        self.prisma_client = prisma_client

    def get_url_endpoint(self, base_url: str, entity_type: str):
        base_entity_path = SPORTMONKS_ENDPOINT_PATH_MAPPING.get(entity_type)
        if not base_entity_path:
            raise ValueError("Entity is not supported")
        return f'{base_url}{base_entity_path}'

    @breaker
    @retry(
        stop=stop_after_attempt(3),  # Retry 3 lần
        wait=wait_exponential(multiplier=1, min=2, max=10),  # exponential backoff
        retry=retry_if_exception_type((httpx.TimeoutException, httpx.HTTPStatusError)),
        reraise=True
    )
    async def _sportmonk_client(self, endpoint: str, provider_secret_token: str, custom_params: Dict[str, str] = {}) -> Dict[str, Any]:
        headers = {'Authorization': provider_secret_token}
        page = 1
        all_data = []
        params = {
            "page": page,
            "per_page": DEFAULT_PER_PAGE_SPORTMONK,
        }
        final_params = {**params, **custom_params}

        async with httpx.AsyncClient(timeout=DEFAULT_API_TIMEOUT_SPORTMONK, headers=headers) as client:
            while True:
                final_params['page'] = page
                try:
                    response = await client.get(endpoint, params=final_params)
                    response.raise_for_status()
                    result = response.json()
                except httpx.HTTPError as e:
                    logger.error(f"HTTP error during Sportmonks API call: {e}")
                    raise
                except Exception as e:
                    logger.exception("Unexpected error during API call")
                    raise

                data = result.get("data", [])
                all_data.extend(data)

                pagination = result.get("pagination", {})
                next_page = pagination.get('next_page', None)
                if not next_page:
                    break
                page += 1

        return all_data

```

 - [ ] Nghiên cứu dữ liệu scheduler

# Ngày 18/5/2025

 - [ ] Bổ sung lấy dữ liệu Types, Teams, Player, Team Squats
 - [ ] Từ Type, mapping sang bảng Event hoặc Score để lấy thông tin của các sự kiện trong trận đấu
 - [ ] Update cơ chế schedule linh hoạt hơn hỗ trợ nhiều worker cùng lấy m

<!--stackedit_data:
eyJoaXN0b3J5IjpbMjEyMDAxMzAzNyw4MzQyNjgzMCwtNTA1Nj
IwNzA5LC0xODAzMTIzMTYsLTE2ODQ0NDk2NDUsMjAwNTY2MDEw
OSwyMDg1MDcxODkyXX0=
-->