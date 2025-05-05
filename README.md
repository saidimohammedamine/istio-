# istio-

Configure Access to Open5Gs dashboard
cat <<EOF | sudo tee open5gs-webui-gateway.yaml 
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: open5gs-webui-gateway
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: open5gs-webui
spec:
  hosts:
  - "*"
  gateways:
  - open5gs-webui-gateway
  http:
  - match:
    route:
    - destination:
        host: cp-webui.open5gs.svc.cluster.local  #name of your service 
        port:
          number: 80
EOF
kubectl apply -f open5gs-webui-gateway.yaml
NODE_PORT_OPEN5GS=$(kubectl -n istio-system get svc istio-ingressgateway \
  --output=jsonpath='{range .spec.ports[1]}{.nodePort}')
echo $NODE_PORT_OPEN5GS
32102
