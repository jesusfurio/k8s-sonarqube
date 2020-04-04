k8s-sonarqube
![alt text](https://miro.medium.com/fit/c/1838/551/1*zf72LjVfgiRJVmGyUY3Rjg.png) 
# Despliegue de servidor Jenkins y esclavos bajo demanda.
En este repositorio, podreis encontrar los ficheros y las instrucciones necesarias para desplegar un entorno de Jenkins en Kubernetes.
Tendremos un master desplegado y los esclavos se crearán bajo demanda al ejecutar una Pipeline. De esta manera optimizaremos recursos en nuestra infraestructura y nos aseguraremos de utilizar únicamente los necesarios.

### Pre-requisitos 📋
* Crear un namespace llamado sonarqube:
```
kubectl create namespace sonarqube
```

### Despliegue 📦
* Primero debemos crear los volúmenes para los datos y las extensiones de Sonarqube:
```
kubectl apply -f data.yaml
kubectl apply -f extensions.yaml
```

* Después crearemos un secret donde almacenaremos la contraseña de la base de datos de Postgre a la que se va a conectar Sonarqube:
```
kubectl apply -f secret.yaml
```
Recordad que las contraseñas en los secret deben ser declaradas en base64 por lo que previamente debemos sacarla con el comando:
```
echo <PASSWORD> | base64
```
Y la añadimos al campo "password:" del yaml.

* Desplegamos el service con el NodePort para exponer la aplicación:
```
kubectl apply -f services.yaml
```
Vamos a desplegar dos servicios. Un Nodeport para exponer nuestra aplicación al exterior y que nos expondrá un puerto por el que acceder. Kubernetes, cuando desplegamos un Nodeport, le puede asignar uno aleatorio en el rango 30000-32767. Si queremos usar un puerto específico, usaremos "nodePort: puerto" en el yaml.

* Por último, vamos a desplegar Sonarqube:
```
kubectl apply -f deployment.yaml
```
En el Pod expondremos el puerto 9000 para acceder a SonarQube.
Previamente, antes de que el pod de Sonarqube se despliegue, lanzaremos un contenedor con Alpine para que SonarQube tenga permisos de acceso al volumen creado.
Una vez desplegado podremos acceder a nuestro SonarQube con la URL de nuestro cluster o minikube y el puerto facilitado por nuestro NodePort.

## Construido con 🛠️
Imágenes Docker usadas:
* [sonarqube:latest]: https://hub.docker.com/_/sonarqube/ -