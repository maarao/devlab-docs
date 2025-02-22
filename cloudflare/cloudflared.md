# Setup Cloudflared

We can use cloudflared and make cloudflared act as a VPS for us. Here's how to do it.

1. Go to <https://one.dash.cloudflare.com/>, click on Networks -> Tunnels, and click create a tunnel to create a cloudflared tunnel. I recommend creating 2 tunnels, one minimum for development and one for production. Create the second one by going through all these steps again. (No quick way of doing it)
2. Name your tunnel. I would follow this naming convention: Project_Name_WhatItsUsedFor_Tunnel Number  Ex: Dining_App_dev_001
3. Get your token. It will give you different ways of using cloudflared. Ignore all of that and copy one of the command. Go intoa text editor (or just paste it into your web browser) and copy the token at the end of the command. Whenever you spin up a cloudflared host, you must use this token in the startup
4. Create and apply your cloudflared service config. Make sure to specify the token you copied in the command. **Please put the token in your secrets so that you dno't accidentally publish your token and let anyone onto our network**. Here is an example of a yaml that uses secrets you can use to deploy to this:

  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: cloudflared
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: cloudflared
    template:
      metadata:
        labels:
          app: cloudflared
      spec:
        containers:
          - name: cloudflared
            image: cloudflare/cloudflared:latest
            args:
              - tunnel
              - --no-autoupdate
              - run
              - --token
              - $(CLOUDFLARE_TUNNEL_TOKEN)
            env:
              - name: CLOUDFLARE_TUNNEL_TOKEN
                valueFrom:
                  secretKeyRef:
                    name: cloudflare-tunnel-secret
                    key: tunnel-token
              - name: TUNNEL_CREDENTIALS_FILE
                value: /etc/cloudflared/tunnel_credentials.json
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 200m
                memory: 256Mi
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: cloudflared
  spec:
    type: ClusterIP
    selector:
      app: cloudflared
    ports:
      - port: 8080
        targetPort: 8080
  ```

5. Specify your public hostname. every project should create its own subdomains. The subdomains should be related to the project name and must be only nested one level within the domain (don't include any periods/dots. Dashes should be fine). leave path blank unless you know what you're doing. This will essentially be your base url, so I wouldn't complicate it too much.
6. Specify you service name. If you aren't using ingress (Which you 100% should be), it would point towards whatever service/pod you want cloudflared to route traffic to in the form of `http://<service-name>.<project-namespace>.svc.cluster.local:PORT` This should point towards the service thats handling ingress For example:
    - Development using nginx for ingress (using Kind): `http://ingress-nginx-controller.ingress-nginx.svc.cluster.local:80`
    - Prod in our k3s cluster: `http://<traefik-service-name>.<traefik-namespace>.svc.cluster.local:80`
7. Save and Apply the route and you should be able to access it with the public hostname you specified.
