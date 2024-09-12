# To add per domain logging, add the following to the domain's configuration in the `domains` section:

```yaml
accessLog:
  filePath: "/logs/access.log"
  format: json
  maxSize: 200  # megabytes
  maxBackups: 3
  # maxAge: 3     # days
  # compress: true
  # format: |
  #   {{.Client.IP}} - {{.Client.User}} [{{.Request.Time.Format "02/Jan/2006:15:04:05 -0700"}}] "{{.Request.Method}} {{.Request.URL.Path}} {{.Request.Proto}}" {{.Response.StatusCode}} {{.Response.ContentLength}} "{{.Request.Referer}}" "{{.Request.UserAgent}}" # Nginx  
  fields:
    defaultMode: keep
    names:
      ClientUsername: drop
```
and to add per service logging, add the following to the service's configuration in the `services` section:

```yaml
services:
  your-service:
    image: your-image
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.your-service.rule=Host(`yourdomain.com`)"
      - "traefik.http.routers.your-service.entrypoints=websecure"
      - "traefik.http.routers.your-service.tls=true"
      - "traefik.http.routers.your-service.tls.certresolver=letsencrypt"
      # Access Log Configuration 1      
      - "traefik.http.middlewares.myservice-log.plugin.accesslog.format={{ .Client.IP }} - {{ .Client.User }} [{{ .Request.Time.Format \"02/Jan/2006:15:04:05 -0700\" }}] \"{{ .Request.Method }} {{ .Request.URL.Path }} {{ .Request.Proto }}\" {{ .Response.StatusCode }} {{ .Response.ContentLength }} \"{{ .Request.Referer }}\" \"{{ .Request.UserAgent }}\""
      # Access Log Configuration 2
      - "traefik.http.routers.your-service.middlewares=your-service-accesslog"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.filepath=/path/to/yourdomain.com-access.log"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.format=json"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.headers.defaultmode=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.headers.names.Authorization=drop"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.headers.names.Content-Type=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.defaultmode=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.names.ClientUsername=drop"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.names.User-Agent=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.names.Referer=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.names.X-Forwarded-For=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.names.X-Forwarded-Proto=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.names.X-Forwarded-Host=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.names.X-Forwarded-Uri=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.names.X-Forwarded-Method=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.names.X-Forwarded-Server=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.names.X-Forwarded-Port=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.names.X-Forwarded-Prefix=keep"
      - "traefik.http.middlewares.your-service-accesslog.accesslog.fields.names.X-Forwarded-Prefix=keep"
```
