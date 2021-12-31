# Example of a Helm Chart to configure Secrets and ConfigMaps from a file

This helm-chart has a secret configuration and a configmap created from files found in the template itself.

In the "certificates" folder are the files that will be used to create the secret and in the "resources" folder are the files that are used to create a configmap
![image](https://user-images.githubusercontent.com/25550691/147822629-c1519d88-b318-48b2-95f9-36b11cae953f.png)

## Secret

```yaml
apiVersion: v1
kind: Secret
metadata: 
    name: da-core-server-certificates
type: Opaque
data: 
{{- $root := . -}}
{{- range $path, $bytes := .Files.Glob "certificates/*" }}  # Read all the files found in the "certificates" folder
    {{ base $path }}: {{ $root.Files.Get $path | b64enc }}  # For each file found, it creates a secret with the name and content of the file found.
{{- end }}
```


## ConfigMap
```yaml
apiVersion: v1
kind: ConfigMap
metadata: 
    name: configmap-config
data: 
    da-core-server.properties: |+ # The configmap name is created manually
        {{ range .Files.Lines "resources/da-core-server.properties" }} # Read line by line from the specified file (resources/da-core-server.properties) and create the content of the configmap. 
        {{ . }}{{ end }}
    da-ui-server.properties: |+ # The configmap name is created manually
        {{ range .Files.Lines "resources/da-ui-server.properties" }} # Read line by line from the specified file (resources/da-ui-server.properties) and create the content of the configmap. 
        {{ . }}{{ end }}

```
