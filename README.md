# VXLAN Observability 一鍵部署說明

## 1. 安裝 Docker
```sh
curl -fsSL https://get.docker.com | sudo bash
```

## 2. 下載 Grafana Enterprise 安裝檔
```sh
wget https://dl.grafana.com/grafana-enterprise/release/12.2.0/grafana-enterprise_12.2.0_17949786146_linux_amd64.deb -P volumes/
```

## 3. 權限與資料夾設定
```sh
# 建議在專案根目錄下執行
sudo chmod -R 777 volumes/
sudo chown -R 65534:65534 volumes/prometheus-data

```

## 4. 啟動服務
```sh
sudo docker compose up -d
```

## 5. 設定 systemd 自動啟動
```sh
sudo nano /etc/systemd/system/vxserverprometheusgrafana.service
# 貼入以下內容
[Unit]
Description=Grafana + Prometheus + sFlow-RT (vxserverprometheusgrafana)
Requires=docker.service
After=docker.service
#請修改為你的實際路徑
RequiresMountsFor=/home/vxserver/vxserverprometheusgrafana

[Service]
Type=oneshot
#請修改為你的實際路徑
WorkingDirectory=/home/vxserver/vxserverprometheusgrafana 
ExecStart=/usr/bin/docker compose up -d
ExecStop=/usr/bin/docker compose down
RemainAfterExit=true

[Install]
WantedBy=multi-user.target
```

## 6. 啟用 systemd 服務並設為開機自動啟動
```sh
sudo systemctl daemon-reload
sudo systemctl enable vxserverprometheusgrafana.service
sudo systemctl start vxserverprometheusgrafana.service
```

---
如需更多 Grafana/Prometheus/sFlow-RT 設定，請參考各自官方文件。

---

## 專案目錄結構範例

```
docker_sflow-rt_grafana_pormetheus/
├─ docker-compose.yml
├─ readme.md
├─ volumes/
│  ├─ prometheus-data/           # Prometheus 資料目錄
│  ├─ prometheus-config/
│  │  └─ prometheus.yml          # Prometheus 設定檔
│  ├─ grafana-data/              # Grafana 資料目錄
│  └─ grafana.deb  # 下載的 Grafana deb(如下載下來執行異常，請替換名稱重新執行)
 ─ sflow-rt/
   └─ flows.js                   # sFlow-RT flows 設定

```

> 若有檔案遺漏或結構不同，請依此架構調整，確保服務能正確運作。
## 適用grfana ID:11201、11096
