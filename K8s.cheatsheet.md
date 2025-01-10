# Helm commands

- Crear configuración `helm create <nombre>`
- Aplicar configuración inicial: `helm install <nombre> .`
- Aplicar actualizaciones: `helm upgrade <nombre> .`

# K8s commands

- Obtener pods, deployments y services: `kubectl get <pods | deployments | services>`
- Revisar todos pods: `kubectl describe pods`
- Revisar un pod: `kubectl describe pod <nombre>`
- Eliminar pod: `kubectl delete pod <nombre>`
- Revisar logs: `kubectl logs <nombre>`

# Crear deployment:

```
kubectl create deployment <nombre> --image=<registro/url/imagen> --dry-run=client -o yaml > deployment.yml
```

Ejemplo para nats:

```
kubectl create deployment nats --image=nats --dry-run=client -o yaml > deployment.yml
```

# Hacer llamadas a servicios

- Ejecutar el siguiente comando en la carpeta k8s/tienda: `kubectl get nodes -o wide`. Tomar la IP de la columna INTERNAL-IP
- Listar los servicios: `kubectl get services`. Tomar el puerto de la columna PORT del servicio client-gateway o el que se quiera llamar (debe tener los puertos expuestos en la configuracion de kubernetes, recordar que el puerto se muestra como \*\*\*:{tomar_este}/TCP).
- En postman, cambiar localhost por la INTERNAL-IP anterior y cambiar el puerto .

# Crear service

```
kubectl create service clusterip <nombre> --tcp=<8888> --dry-run=client -o yaml > service.yml
**kubectl create service nodeport <nombre> --tcp=<3000> --dry-run=client -o yaml > service.yml**
```

ejemplo:

```
cd k8s/tienda/templates/client-gateway
kubectl create service nodeport client-gateway --tcp=3000 --dry-run=client -o yaml > service.yml
```

- **clusterip**: solo se puede acceder desde dentro del cluster
- **nodeport**: se puede acceder desde fuera del cluster

# Secrets

- Crear secretos, varios a la vez, o uno por uno.

```
kubectl create secret generic <nombre> --from-literal=key=value

kubectl create secret generic secret1 --from-literal=key1=value1 --from-literal=key2=value2
```

Ejemplo para orders:

```
kubectl create secret generic orders-ms-secrets --from-literal=database_url="postgresql://XXXXXXXX:XXXXXXXXX@ep-small-river-a5u9w2ak.us-east-2.aws.neon.tech/orders-db?sslmode=require"
secret/orders-ms-secrets created
```

- Obtener los secretos `kubectl get secrets`
- Ver el contenido de un secreto `kubectl get secrets <nombre> -o yaml`
  ejemplo para auth-ms-secrets: `kubectl get secrets auth-ms-secrets -o yaml`

## Editar un secret

La forma más fácil es borrarlo y volverlo a crear pero si es más de un secret, no vamos a querer perder los demás.
Recordar que los secrets están en `base64`, por lo que si queremos editar un secret, debemos hacerlo en `base64`.

1. Editar el secret con `kubectl edit secret <nombre>` esto invocará el editor
2. Cambiar el valor (se puede usar un editor en [línea para convertir a base64](https://www.rapidtables.com/web/tools/base64-decode.html))
3. Tocar **i** para insertar líneas y editar el archivo
4. Poner el valor a decodificar en una nueva línea
5. Presionar **esc** y luego `:. ! base64 -D` para decodificar el valor
6. Presionar **i** para insertar o editar el valor
7. Presionar **esc** y luego `:. ! base64` para codificar el valor
8. Editar nuevamente el archivo **i** y dejar la línea en su posición
9. Presionar **esc** y luego **:wq** para guardar y salir

## Configurar secretos de Google Cloud para obtener las imágenes

1. Crear secreto:

```
kubectl create secret docker-registry gcr-json-key --docker-server=SERVIDOR-DE-GOOGLE-docker.pkg.dev --docker-username=_json_key --docker-password="$(cat 'PATH/DE/Tienda Microservices IAM.json')" --docker-email=TU_CORREO@gmail.com
```

ejemplo:

```
kubectl create secret docker-registry gcr-json-key --docker-server=southamerica-east1-docker.pkg.dev --docker-username=_json_key --docker-password="$(cat 'c:/Users/aeveg/OneDrive/Documentos/SCHOOL/NestMicroserviceCourse/03-Products-Launcher/k8s/products-app-microservices-e77ca8a29b2d.json')" --docker-email=aevega1991@gmail.com
```

2. Path del secreto para que use la llave:

```
kubectl patch serviceaccounts default -p '{ "imagePullSecrets": [{ "name":"gcr-json-key" }] }'
```

## Exportar y aplicar configuraciones con archivos (secrets en este caso)

- Para exportar los archivos de configuración

```
kubectl get secret <nombre> -o yaml > <nombre>.yml
```

- Aplicar la configuración basado en el archivo

```
kubectl create -f <nombre>.yml
```
