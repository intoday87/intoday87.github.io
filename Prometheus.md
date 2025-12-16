---
title: 모르고 살자니 너무 불편한 Prometheus
---
메트릭 수집시 prometheus + grafana 조합으로 많이 쓰이는데 매번 대충 연결만 해놓고 쓰고 있었는데 매번 다른 환경에서 주로 nodejs와 prometheus와의 연동을 다시 하게 되는데 이해 없이 쓰자니 매번 불편함으로 다가온다.

이해하는 내용을 정리를 해보자!

## [Prometheus](https://prometheus.io/) 는 뭐하는 애인가?

### Prometheus가 뭐하는 애인가?

**Prometheus는 메트릭 기반 모니터링 시스템**이에요.

- 서버, 애플리케이션, DB, 컨테이너 같은 대상에서  
    **CPU, 메모리, 요청 수, 에러 수, 지연 시간** 같은 _숫자 메트릭_을
    
- **주기적으로 수집(scrape)** 해서
    
- **시계열 데이터(time-series)** 로 저장하고
    
- **PromQL**이라는 쿼리 언어로 조회/집계하는 도구입니다.
    

로그(log)나 트레이싱(trace)이 아니라  
👉 **“지금 시스템 상태가 어떤지 숫자로 보는 용도”**라고 생각하면 됩니다.

---

### Prometheus가 많이 쓰이는 이유

#### 1. Pull 방식 구조 (운영에 유리)

- Prometheus가 대상 서비스의 `/metrics` 엔드포인트를 **가져오는 방식**
    
- 장점
    
    - 대상이 일시적으로 죽어도 Prometheus 쪽이 단순함
        
    - 방화벽 / 네트워크 구성에 유리
        
    - 서비스가 “메트릭을 노출만 하면 끝”
        

👉 Kubernetes, 마이크로서비스 환경에 잘 맞음

---

#### 2. 강력한 데이터 모델 + PromQL

- 모든 메트릭이:
    
    `metric_name{label1="value1", label2="value2"} = number`
    
- PromQL로:
    
    - rate, sum, avg, percentile
        
    - label 기준 집계
        
    - 시간 범위 계산  
        가능
        

👉 “지금 5분간 에러율이 1% 넘은 서비스” 같은 걸 바로 표현 가능

---

#### 3. CNCF 공식 프로젝트 (사실상 표준)

- Kubernetes, Envoy, etcd, Redis, MySQL, Node exporter 등
    
- **대부분의 오픈소스가 Prometheus 메트릭을 기본 제공**
    

👉 생태계가 너무 큼 (exporter 천국)

---

#### 4. Alerting까지 한 세트

- Alertmanager와 조합하면:
    
    - 특정 조건 발생 시
        
    - Slack, PagerDuty, Email 알림 가능
        

👉 “장애 감지 → 알림”까지 한 흐름 완성

---

## Grafana랑 왜 항상 같이 쓰이나?

### 역할 분담이 명확함

|역할|Prometheus|Grafana|
|---|---|---|
|데이터 수집|✅|❌|
|데이터 저장|✅|❌|
|쿼리 엔진|✅ (PromQL)|❌|
|시각화|❌|✅|
|대시보드|❌|✅|
|여러 데이터소스 통합|❌|✅|

👉 **Prometheus = 데이터 엔진**  
👉 **Grafana = 시각화 / UI**

---

### Grafana가 Prometheus에 특히 잘 맞는 이유

- PromQL을 네이티브로 지원
    
- label 기반 필터링 UI가 뛰어남
    
- Kubernetes / Node / JVM / Nginx 대시보드 템플릿이 이미 수백 개
    

👉 거의 “꽂으면 바로 그림 나오는” 수준

---

## 한 줄 요약

- **Prometheus**:
    
    > 시스템과 애플리케이션의 상태를 숫자 메트릭으로 수집·저장·쿼리하는 모니터링 시스템
    
- **Grafana와의 조합**:
    
    > Prometheus가 모은 메트릭을 가장 잘 시각화해 주는 표준 조합
    
- **왜 많이 쓰이냐면**:
    
    > 클라우드/쿠버네티스 환경에 딱 맞고, 생태계가 사실상 표준이라서
    

---
by chatgpt

## 그래서 nodejs랑은 어떻게 쓰여야 하나?

prometheus는 exporter가 매우 많은데 nodejs의 경우 별도의 exporter를 사용하지 않고 [prom-client](https://www.npmjs.com/package/prom-client) 를 사용해서 nodejs에 대한 정보를 제공한다.



