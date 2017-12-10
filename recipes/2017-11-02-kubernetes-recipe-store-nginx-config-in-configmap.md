# Kubernetes recipe: store nginx config with ConfigMap and reverse-proxy requests from your domain to your Github page

Tags: Kubernetes, Nginx, Ingress, ConfigMap

As we know, we can publish GitHub pages on a custom domain. However, in this case, we are unable to have https. Another approach is to get nginx reverse-proxy that will proxy a request from your domain to YOUR-GITHUB-USERNAME.github.io.

If we have kubernetes cluster with Ingress and kube-lego, we can deploy such nginx reverse-proxy and store nginx config with kubernetes ConfigMap. Pay attention to escaping dollar sign \$ in your nginx config.
The configuration below shows how to deploy such reverse proxy.
Do not forget to change YOUR-GITHUB-USERNAME.github.io to your github page and mysite.com to your domain name.

```yaml
cat <<EOF | kubectl create -f -
apiVersion: v1
kind: Namespace
metadata:
  name: mysite
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysite-config
  namespace: mysite
data:
  default.conf: |
    upstream node {
      # CHANGE ME
      server YOUR-GITHUB-USERNAME.github.io;
    }
server {
      listen                  80;
      server_name             _;
      root                    /usr/share/nginx/html;
      location / {
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        # CHANGE ME
        proxy_set_header Host YOUR-GITHUB-USERNAME.github.io;
        proxy_pass http://node;
        proxy_redirect off;
        port_in_redirect off;
      }
    }
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: mysite-deployment
  namespace: mysite
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: mysite
    spec:
      containers:
      - image: nginx:1.13.6-alpine
        imagePullPolicy: Always
        name: mysite
        ports:
        - containerPort: 80
        volumeMounts:
        - name: mysite-configs
          mountPath: /etc/nginx/conf.d
      # Load the configuration files for nginx
      volumes:
        - name: mysite-configs
          configMap:
            name: mysite-config
---
apiVersion: v1
kind: Service
metadata:
  name: mysite-service
  namespace: mysite
spec:
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
  selector:
    app: mysite
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: mysite-ingress
  namespace: mysite
  annotations:
    kubernetes.io/tls-acme: "true"
    kubernetes.io/ingress.class: "nginx"
spec:
  tls:
  - hosts:
    # CHANGE ME
    - myname.com
    secretName: mysite-tls
  rules:
  # CHANGE ME
  - host: myname.com
    http:
      paths:
      - path: /
        backend:
          serviceName: mysite-service
          servicePort: 80
EOF
```

