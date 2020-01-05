# 2020-01-06 Proxy your custom domain to github page with Ingress in Kubernetes

As we know, we can publish GitHub pages on a custom domain. However, in this case, we are [unable to have https](https://help.github.com/en/github/working-with-github-pages/securing-your-github-pages-site-with-https). Another approach is to get some reverse-proxy that will proxy a request from your domain to https://YOUR-GITHUB-USERNAME.github.io.

Kubernetes ConfigMap gives us abilities to define and use nginx configurations. This is an example how to publish YOUR-GITHUB-USERNAME.github.io page with Kubernetes.

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Namespace
metadata:
  name: mysite
EOF

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysite-config
  namespace: mysite
data:
  default.conf: |
    upstream node {
      server CHANGE_ME.github.io:443;
    }
    server {
      listen                  80;
      server_name             _;
      root                    /usr/share/nginx/html;
      location / {
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header Host CHANGE_ME.github.io;
        proxy_pass https://node;
        proxy_redirect off;
        port_in_redirect off;
      }
    }
EOF

cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysite-deployment
  namespace: mysite
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysite
  template:
    metadata:
      labels:
        app: mysite
    spec:
      containers:
      - image: nginx:stable-alpine
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
  name: mysite-svc
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
  labels:
    app: mysite
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
    kubernetes.io/ingress.class: nginx
spec:
  tls:
    - hosts:
        # CHANGE ME
        - your.domain.com
      secretName: mysite-cert
  rules:
  # CHANGE ME
    - host: your.domain.com
      http:
        paths:
          - path: /
            backend:
              serviceName: mysite-svc
              servicePort: 80
EOF

```
