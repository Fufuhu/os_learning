# serviceのyamlサンプル
```yaml
apiVersion: v1
kind: Service
metadata:
  name: docker-registry      
spec:
  selector:                  
    docker-registry: default
  portalIP: 172.30.136.123   
  ports:
  - nodePort: 0
    port: 5000               
    protocol: TCP
    targetPort: 5000          
```