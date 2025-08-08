# **REDIS - GRAFANA** #
## 1.Grafana ##
### 1.1. Tổng quan. ###
- Grafana là một nền tảng mã nguông mở để giám sát, trực quan hóa dữ liệu và phân tích chỉ số (metric analytics).
  * Được dùng phổ biến trong các hệ thống monitoring, DevOpts và IoT.
  * Hỗ trợ biểu đồ, dashboard, cảnh báo.
- Có thể dùng độc lập với các hệ thống như  Prometheus, InfluxDB, Elasticsearch, Loki, MySQL, PostgreSQL

### 1.2. Chức năng chính của Grafana. ###
- Dashboard và Visualization
  * Cho phép tạo dashboard tuỳ chỉnh, hiển thị các biểu đồ, gauge, heatmap, table...
  * Mỗi panel (biểu đồ) có thể cấu hình truy vấn riêng từ data source tương ứng.
  * Hỗ trợ nhiều loại biểu đồ: Line chart, Bar chart, Time series, Gauge, Stat, Pie, Table, Logs...

- Kết nối nhiều ngôn ngữ dữ liệu (Data Source). (Time series, Log, SQL, Cloud, IoT, ...)
- Query + Tranform dữ liệu.
  * Grafana có giao diện truy vấn mạnh mẽ tùy theo loại data source.
  * Hỗ trợ Query language riêng hoặc SQL tùy nguồn dữ liệu.
  * Có thể thực hiện Tranform dữ liệu, ví dụ: join, filter, math operation, ...

- Cảnh báo (Alert).
  * Hỗ trợ thiết lập cảnh báo khi điều kiện xảy ra (ví dụ CPU > 80% trong 5 phút).
  * Gửi cảnh báo qua:
  * Email, Slack, Telegram, Microsoft Teams, Webhook, Discord...
  * Có thể cài đặt thời gian, ngưỡng, kiểm tra chu kỳ, silence...

- Tổ chức và phân quyền
  * Tổ chức theo Workspace/Folder
  * Quản lý user, role, quyền truy cập dashboard
  * Hỗ trợ LDAP, OAuth, SAML, API Key

- Pluggin hệ thống - Grafana hỗ trợ hệ thống plugin mạnh mẽ.
  * Data Source Plugin: thêm hỗ trợ nguồn dữ liệu mới
  * Panel Plugin: biểu đồ, bảng mới
  * App Plugin: tích hợp hệ thống lớn (ví dụ: Kubernetes App)

- Cấu hình tự động (Provisioning):
  * Hỗ trợ quản lý dashboard, datasources, alert rules... bằng YAML / JSON → phù hợp CI/CD
  * Có thể deploy đồng bộ qua Git, Docker, Terraform...

### 1.3. Cấu hình và kết nối Grafana local ###
- Cài đặt Grafana qua apt:
```bash
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget

wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list

sudo apt-get update
sudo apt-get install grafana -y
```

- Khởi động Grafana:
```bash
sudo systemctl daemon-reexec         
sudo systemctl start grafana-server  
sudo systemctl enable grafana-server 
```

## 2. Redis ##
### 2.1. Tổng quan về Redis ###
Redis (viết tắt của REmote DIctionary Server) là một in-memory data structure store mã nguồn mở, được sử dụng như database, cache, message broker, hoặc streaming engine. Dưới đây là tổng quan các chức năng chính của Redis:

### 2.2. Chức năng của Redis ###
- In-Memory Database: Redis lưu trữ dữ liệu trực tiếp trên RAM, giúp truy xuất dữ liệu cực nhanh (tính bằng micro giây).
  * Hỗ trợ các cấu trúc dữ liệu: String, List, Set, Sorted Set, Hash, Bitmap, HyperLogLog, Stream, Geo, v.v.
  * Dữ liệu có thể được persist xuống đĩa (dạng snapshot hoặc append-only log).

- Cache (bộ nhớ đệm). Redis thường được dùng để làm cache layer phía trên database chậm hơn như MySQL, MongoDB...
  * Hỗ trợ TTL (Time-To-Live) cho key → tự động hết hạn.
  * Hỗ trợ các eviction policies: LRU, LFU, random, volatile, v.v.
  * Dễ tích hợp với các framework: Laravel, Spring, Django, Flask...

- Pub/Sub message system 
  * Hệ thống publish/subscribe dùng để xây dựng các ứng dụng real-time, ví dụ:
    * Chat app
    * Thông báo sự kiện
    * Log streaming

- Task Queue / Message Broker
  * Redis có thể dùng làm message broker đơn giản (thay thế RabbitMQ, Kafka trong vài use case nhỏ):
    - Dựa vào các cấu trúc List (với LPUSH + BRPOP) hoặc Stream.
    - Dễ dùng trong các hệ thống xử lý background job như:
      * Sidekiq (Ruby)
      * Celery (Python)
      * Bull (Node.js)

- Automic Operations & Transaction
  * Redis hỗ trợ các thao tác atomic và transactional:
    - MULTI, EXEC, WATCH để thực hiện nhiều lệnh như một transaction.
    - Các thao tác trên key đơn là atomic.

- Lua Scripting
  * Redis hỗ trợ chạy các đoạn Lua script thông qua lệnh EVAL:
    - Giúp xử lý logic phức tạp ngay trong Redis.
    - Gộp nhiều lệnh thành một bước atomic.

- High Availability & Clustering - Redis hỗ trợ các chế độ:
  * Replication (master-slave)
  * Sentinel → Tự động failover và monitor
  * Cluster → Horizontal scaling (phân mảnh dữ liệu)

- ACL & Security
  * Hỗ trợ quản lý user, phân quyền với ACL
  * Hỗ trợ TLS, password authentication

- Geo-Spatial Data
  * Redis hỗ trợ lưu trữ và truy vấn dữ liệu vị trí: Dùng các lệnh như GEOADD, GEORADIUS, GEODIST, v.v.

- Stream Processing:
  * Redis có kiểu dữ liệu Stream, giống như Kafka:
    * Dùng để xử lý log/event, lưu trữ theo thời gian
    * Hỗ trợ consumer group, ack, id, replay, v.v.

### 2.3. Cấu hình và kết nối Redis ###
- Cài đặt Redis:
```bash
sudo apt update
sudo apt install redis -y
```

- Khởi động và kết nối Redis:
```bash
sudo systemctl start redis
sudo systemctl enable redis
sudo redis-cli -h [host_address] -p [port] -a [password]
```

### 2.4. Export metric Redis cho Grafana. ###
- Flow:
```yaml
[Redis] --> [Redis_Exporter] --> [Prometheus] --> [Grafana](port 9121)
```

- Cài Redis Exporter chạy bằng Docker, sử dụng image "oliver006/redis_exporter".
  * Chạy docker.
```bash
docker run -d \
--name redis_exporter \
-p 9121:9121 \
oliver006/redis_exporter \
--redis.addr=redis://localhost:6379 \
--redis.password=password 
```
  * Kiểm tra exporter có đang chạy ko ở url:
```
http://localhost:9121/metrics
```

- Dựng Prometheus.
  * Config file "prometheus.yml" -> Cấu hình cho Prometheus bao gồm:
    - scrape_configs: Danh sách nơi lấy metric
    - target: URL của Exporter. 
    - scrape_interval: Tần suất lấy metric.

  * file "prometheus.yml":
```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'redis'
    static_configs:
      - targets: ['host:9121']
```

- Dựng file docker-compose.yml -> Tổ chức các container (Granafa + Redis + Exporter + Prometheus)
```yaml
version: '3'

services:
  redis:
    image: redis:7
    container_name: redis
    ports:
      - "6379:6379"

  redis_exporter:
    image: oliver006/redis_exporter
    container_name: redis_exporter
    ports:
      - "9121:9121"
    environment:
      - REDIS_ADDR=redis://171.244.139.224:6379

  prometheus:
    image: prom/prometheus
    container_name: prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"

  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
      - "3000:3000"
    depends_on:
      - prometheus
```

- Chạy hệ thống:
```
docker-compose up -d
```
### 2.5. Ý nghĩa các metrix từ Redis ###
Xem từ giao diện prometheus. (localhost:9090)
#### Chỉ số chống phân mảnh RAM ####
| Metric                    | Ý nghĩa                                                               |
|---------------------------|------------------------------------------------------------------------|
| redis_active_defrag_running | Redis có đang chạy chống phân mảnh RAM không (1 là có, 0 là không). |

#### Chỉ số về bộ nhớ (Memory Allocation) ####
| Metric | Ý nghĩa |
|--------|---------|
|redis_allocator_active_bytes | Bộ nhớ đang được allocator giữ lại (active).|
|redis_allocator_allocated_bytes | 	Bộ nhớ thực sự được Redis sử dụng. |
|redis_allocator_frag_bytes | Số byte bị phân mảnh giữa active và allocated. |
|redis_allocator_frag_ratio | Tỉ lệ giữa active và allocated: >1 nghĩa là có fragmentation. |
|redis_allocator_resident_bytes | redis_allocator_resident_bytes |
|redis_allocator_rss_bytes | Resident Set Size từ allocator (bộ nhớ chiếm dụng thật). |
|redis_allocator_rss_ratio | Tỉ lệ giữa RSS và Active: >1 → dư thừa do fragmentation. |

-> nếu frag_ratio hoặc rss_ratio cao → Redis đang lãng phí RAM.

#### AOF (Append Only File) ####
| Metric | Ý nghĩa |
|--------|---------|
| redis_aof_enabled | AOF mode có bật không (1 là bật, 0 là tắt). |
| redis_aof_current_rewrite_duration_sec | Thời gian tính bằng giây của lần rewrite AOF đang chạy (nếu có). -1 là không chạy.|
| redis_aof_last_bgrewrite_status | Kết quả lần AOF rewrite gần nhất: 1 thành công, 0 lỗi. |
| redis_aof_last_cow_size_bytes | Dữ liệu copy-on-write (COW) của lần rewrite gần nhất. |
| redis_aof_last_rewrite_duration_sec | Thời gian của lần rewrite AOF gần nhất. | 
| redis_aof_last_write_status | Kết quả lần ghi AOF gần nhất. |
| redis_aof_rewrite_in_progress | Có đang rewrite AOF không (1 là có). |
| redis_aof_rewrite_scheduled | Có đang lên lịch rewrite sau khi RDB xong không. |

-> Nếu không dùng AOF, gần như tất cả các giá trị này sẽ là 0 hoặc -1.

#### Clients & Connection ####
| Metric | Ý nghĩa |
|--------|---------|
|redis_blocked_clients | Số client bị block (ví dụ đang đợi BLPOP).|
|redis_client_recent_max_input_buffer_bytes | Input buffer lớn nhất trong các client gần đây. |
|redis_client_recent_max_output_buffer_bytes | Output buffer lớn nhất (giúp phát hiện client "treo"). |
|redis_clients_in_timeout_table	| Số client đang nằm trong bảng timeout. |
