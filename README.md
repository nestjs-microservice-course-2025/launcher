# Products App

### Un proyecto que implementa un cliente API Gateway y dos microservicios de productos y de ordenes enlazados por un NATS Server y desplegado en Docker Compose.

## Importante

Si se trabaja en el repositorio que tiene los sub-módulos, **primero actualizar y hacer push** en el sub-módulo y **después** en el repositorio principal.

Si se hace al revés, se perderán las referencias de los sub-módulos en el repositorio principal y tendremos que resolver conflictos.

## Pasos para crear los Git Submodules

1. Crear un nuevo repositorio en GitHub
2. Clonar el repositorio en la máquina local
3. Añadir el submodule, donde `repository_url` es la url del repositorio y `directory_name` es el nombre de la carpeta donde quieres que se guarde el sub-módulo (no debe de existir en el proyecto)

```
git submodule add <repository_url> <directory_name>
```

4. Añadir los cambios al repositorio (git add, git commit, git push)
   Ej:

```
git add .
git commit -m "Add submodule"
git push
```

5. Inicializar y actualizar Sub-módulos, cuando alguien clona el repositorio por primera vez, debe de ejecutar el siguiente comando para inicializar y actualizar los sub-módulos

```
git submodule update --init --recursive
```

6. Para actualizar las referencias de los sub-módulos

```
git submodule update --remote
```

## Levantar el proyecto en desarrollo

1. Clonar el repositorio
2. Revisar los pasos para crear los Git Submodules en el apartado anterior, aplicar lo que sea necesario.
3. Copiar las variables de entorno que se tengan creadas o crear un archivo `.env` en el root del proyecto basado en el archivo `.env.template`
4. Ejecutar `docker compose up --build`

## Para resolver los errores de importacion de dependencias en los proyectos

Instalar las dependencias de cada submódulo:

```
cd client-gateway
npm install
cd ../products-ms
npm install
cd ../orders-ms
npm install
cd ../payments-ms
npm install
cd ..
```

Volver a levantar el proyecto `docker compose up --build`

# Prod

1. Clonar el repositorio
2. Crear un `.env` basado en el `.env.template`
3. Ejecutar el comando

```
docker compose -f docker-compose.prod.yml build
```

4. Levantar produccion

```
docker compose -f docker-compose.prod.yml up
```

5. Bajar produccion

```
docker compose -f docker-compose.prod.yml down
```
