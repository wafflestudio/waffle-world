# Prometheus + Isio + Grafana

###  설치 방법

```shell
waffle@opeartor:~$ helm dep update
waffle@opeartor:~$ helm install prometheus -n prometheus -f override-values.yaml .
waffle@opeartor:~$ k -n prometheus prot-forward deplyoment/prometheus-grafana 8888:3000
waffle@opeartor:~$ ./istio-grafana.sh # istio 대시보드 추가
```