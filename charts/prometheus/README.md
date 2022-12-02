# Prometheus + Isio + Grafana

###  설치 방법

```shell
waffle@opeartor:~$ k create ns prometheus 
waffle@opeartor:~$ helm install prometheus prometheus/kube-prometheus-stack --version 42.1.1 -f values.yaml -n prometheus
waffle@opeartor:~$ k -n prometheus port-forward deployment/prometheus-grafana 8888:3000
waffle@opeartor:~$ ./istio-grafana.sh # istio 대시보드 추가
waffle@operator:~$ k -n prometheus apply -f ../../misc/grafana/virtualservice.yaml # vs 적용
```