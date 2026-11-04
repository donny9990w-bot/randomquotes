# randomquotes

App Express que muestra una frase aleatoria y el hostname del contenedor. Se despliega en AWS: GitHub Actions → ECR → ECS Fargate (4 tareas) detrás de un Application Load Balancer.

## Correr local

    docker build -t randomquotes .
    docker run --rm -p 8080:80 randomquotes

Abrir http://localhost:8080

## Despliegue

- Clúster: `randomquotes-cluster`
- Servicio: `randomquotes-service`, 4 tareas
- Cada push a `main` construye la imagen, la sube a ECR y fuerza un despliegue (`.github/workflows/deploy.yml`).
- Cuenta AWS Academy: no permite crear el proveedor OIDC ni el rol de la sección I.1 del tutorial, así que el workflow usa las credenciales temporales del laboratorio.

## Ajustes por AWS Academy

- En la Task Definition, usar `LabRole` como Task execution role.
- En GitHub → Settings → Secrets and variables → Actions, crear `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` y `AWS_SESSION_TOKEN` con los valores de AWS Details → AWS CLI.
- Esas credenciales vencen con la sesión del laboratorio: al abrir una sesión nueva hay que actualizar los tres secrets antes de hacer push.

URL del ALB: `PEGAR_DNS_DEL_ALB`

## Evidencias

| Prueba | Captura |
|---|---|
| Imagen en ECR | PEGAR |
| 4 tareas RUNNING | PEGAR |
| 4 destinos healthy | PEGAR |
| Respuestas con distintos Container Id | PEGAR |
| Tarea detenida y reemplazada | PEGAR |
| Workflow exitoso en GitHub Actions | PEGAR |
| Frase actualizada tras el push | PEGAR |

Identificadores de contenedor observados: `PEGAR_ID_1`, `PEGAR_ID_2`, `PEGAR_ID_3`

## Preguntas del tutorial

**¿Qué papel cumple el Application Load Balancer?** Es el único punto de entrada público. Recibe las solicitudes y las reparte entre las tareas saludables del Target Group, por eso cambia el Container Id entre respuestas.

**¿Por qué se crea una tarea nueva al detener una?** Porque el servicio declara un estado deseado de 4 tareas. Al quedar 3 en ejecución, se lanza una de reemplazo.

**¿Qué componente mantiene la cantidad deseada?** El programador del servicio de ECS (ECS Service).

**¿Qué efecto tiene el health check?** El ALB solo envía tráfico a destinos que responden bien en `/`. Si una tarea falla el chequeo, deja de recibir solicitudes y ECS la reemplaza.

## Conclusiones

ESCRIBIR 2 O 3 LÍNEAS PROPIAS.
