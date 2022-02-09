#1. способ 1
https://prometheus-operator.dev/docs/prologue/quick-start/

```sh
git clone https://github.com/prometheus-operator/kube-prometheus.git

kubectl create -f manifests/setup
until kubectl get servicemonitors --all-namespaces ; do date; sleep 1; echo ""; done
kubectl create -f manifests/

kubectl --namespace monitoring port-forward svc/grafana 3000
```
Плюсы: много готовых дашбоардов,  prometheus-operator  развивается быстро
Минус: нету  loki, нету  helm,  оператор сложен

#2.  способ  два 
https://grafana.com/docs/loki/latest/installation/helm/

```sh
helm upgrade --install loki grafana/loki-stack
helm upgrade --install loki grafana/loki-stack   --set fluent-bit.enabled=true,promtail.enabled=false,grafana.enabled=true,prometheus.enabled=true,prometheus.alertmanager.persistentVolume.enabled=false,prometheus.server.persistentVolume.enabled=false
```

Плюсы:  loki + fluent-bit, helm
Минусы: нет готовых дашбордов

#3. патчим deployment
```sh
sudo kubectl -n ingress-nginx patch deployment ingress-nginx-controller --patch "$(cat ./ingress_annotation.yaml)"
```
#4. настройка  loki + fluentbid
```sh
diff -ub fluentbit_my.yaml fluentbit_orig.yaml
```

#5.  проверка рег выражения
https://grokconstructor.appspot.com/do/match

4. test grok pattern
```sh
192.168.0.121 - - [09/Feb/2022:13:40:51 +0000] "GET / HTTP/2.0" 200 615 "-" "curl/7.74.0" 32 0.072 [default-nginx-80] [] 172.17.0.9:80 615 0.072 200 a53e962103492bdb2fc70519b366c488

^(?<remoteAddr>[^ ]*) (?<the_real_ip>[^ ]*) (?<remote_user>[^ ]*) \[(?<time_local>[^\]]*)\] "(?<method>\S+)(?: +(?<request>[^\"]*?)(?: +\S*)?)?" (?<status>[^ ]*) (?<body_bytes_sent>[^ ]*)(?: "(?<httpReferer>[^\"]*)" "(?<httpUserAgent>[^\"]*)")? (?<request_length>[^ ]*) (?<request_time>[^ ]*) \[(?<proxy_upstream_name>[^ ]*)\] (?<unknown1>[^ ]*) (?<upstream_addr>[^ ]*) (?<upstream_response_length>[^ ]*) (?<upstream_response_time>[^ ]*) (?<upstream_status>[^ ]*) (?<requestid>[^ ]*)$
```
