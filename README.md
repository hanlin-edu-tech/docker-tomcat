## 使用情況調查（2026-11-09）

> 本節為 hanlin-edu-tech repo 清理作業的調查紀錄，待負責人確認後決定封存或刪除。

- 調查結論：仍在使用
- 建議處置：保留，理由：本 repo `Dockerfile.basetomcat` 產出的 basetomcat image 是 org 內 private repo `tutor-ms-currency` 的 base image，該服務在正式環境執行中。
- 最後更新：pushedAt 2018-10-26；最後 commit `82a36dd` 2018-10-26 Rodick.Huang 新增 basetomcat
- 用途：建置公司通用的 Tomcat 8.5.31 docker image（`Dockerfile`、`basejre.dockerfile` 產出 tomcat-msm 系列，`Dockerfile.basetomcat` 產出 basetomcat），內含 Cloud Profiler agent 與 MongoDB session manager，供各 Java web 服務作為 base image。

### 檢查結果

| 檢查項目 | 結果 | 依據 |
|---|---|---|
| GitHub 活動 | 2018-10-26 後無 commit；無 PR、issue、release、tag、Actions | 已查核，細節記錄於內部調查報告 |
| org 內程式碼引用 | private repo `tutor-ms-currency` 以 basetomcat 為 base image；tomcat-msm 系列查無引用 | 已查核，細節記錄於內部調查報告 |
| GCP 部署 | 以 basetomcat 為基底的服務在正式環境執行中（image layer 比對確認）；本 repo 產出的 image 無直接部署 | 已查核，細節記錄於內部調查報告 |
| AWS 部署 | 查無；部分帳號的部分服務無權限查詢 | 已查核，細節記錄於內部調查報告 |

### 待確認事項
- registry 上的 image 於 2022 年重新推送，本 repo 無對應 commit，需由 `tutor-ms-currency` 維護者確認重建來源是否與本 repo 一致。
- 舊版 image 存放在個人 Docker Hub 帳號且仍被拉取，需由帳號擁有者確認是否刪除或轉移。
- tomcat-msm 系列 image 查無使用者，需由 GCP 管理者決定是否清除。


## ROOT 版
build and deploy
```
docker build -t="gcr.io/tutor-204108/tomcat-msm:8.5.31" .
gcloud docker -- push "gcr.io/tutor-204108/tomcat-msm:8.5.31"
```

base jre build and deploy 
```
docker build -f basejre.dockerfile --build-arg "START_SCRIPT_ARG=run-app-war.sh" -t="gcr.io/tutor-204108/tomcat-msm:8.5.31-base" .
gcloud docker -- push "gcr.io/tutor-204108/tomcat-msm:8.5.31-base"
```

---

## PATH 版
build and deploy
```
docker build --build-arg "START_SCRIPT_ARG=run-app-war.sh" -t="gcr.io/tutor-204108/tomcat-msm:8.5.31-app" .
gcloud docker -- push "gcr.io/tutor-204108/tomcat-msm:8.5.31-app"
```

base jre build and deploy 
```
docker build -f basejre.dockerfile --build-arg "START_SCRIPT_ARG=run-app-war.sh" -t="gcr.io/tutor-204108/tomcat-msm:8.5.31-app-base" .
gcloud docker -- push "gcr.io/tutor-204108/tomcat-msm:8.5.31-app-base"
```

---

## Multiple path 版
build and deploy
```
docker build --build-arg "START_SCRIPT_ARG=run-multiple-app-war.sh" -t="gcr.io/tutor-204108/tomcat-msm:8.5.31-multiple-app" .
gcloud docker -- push "gcr.io/tutor-204108/tomcat-msm:8.5.31-multiple-app"
```

base jre build and deploy 
```
docker build -f basejre.dockerfile --build-arg "START_SCRIPT_ARG=run-app-war.sh" -t="gcr.io/tutor-204108/tomcat-msm:8.5.31-multiple-app-base" .
gcloud docker -- push "gcr.io/tutor-204108/tomcat-msm:8.5.31-multiple-app-base"
```
