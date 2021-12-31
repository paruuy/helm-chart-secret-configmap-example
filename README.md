# Example of a Helm Chart to configure Secrets and ConfigMaps from a file

This helm-chart has a secret configuration and a configmap created from files found in the template itself.
Helm's official documentation was used: https://helm.sh/docs/chart_template_guide/accessing_files/

In the "certificates" folder are the files that will be used to create the secret and in the "resources" folder are the files that are used to create a configmap </br>
![helmchartstructure](https://user-images.githubusercontent.com/25550691/147822740-ed977553-6aa3-4882-aec4-6627f8c14224.png)

**Limitations**
- It is okay to add extra files to your Helm chart. These files will be bundled. Be careful, though. Charts must be smaller than **1M** because of the storage limitations of Kubernetes objects.
- Files in templates/ cannot be accessed.
- Files excluded using .helmignore cannot be accessed.

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
