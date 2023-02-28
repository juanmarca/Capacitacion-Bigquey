# Taller Bigquery

<img width="705" src="https://user-images.githubusercontent.com/2066453/218166406-b1ae0410-411b-4b93-8c25-320485813504.png">

Abrir el servicio de bigquey.

![Captura de Pantalla 2023-02-23 a la(s) 09 41 21](images/1.png)

Aparecerá la siguiente pantalla, Verificar que estemos en el proyecto prd-farma-peru-cost-control.

<img width="700" src="images/2.png">

Clonaremos el repositorio de Github en Cloud Shell.

![Captura de Pantalla 2023-02-23 a la(s) 09 43 27](https://user-images.githubusercontent.com/2066453/220939599-db910af2-e87e-4e21-bbbf-49f3e60fdb28.png)

Ejecutar el siguiente comando en el terminal de linux.

	git clone https://github.com/luisgradossalinas/taller-gcp-dataplatform-04
	
Se nos pedirá nuestro usuario y nuestro PAT (Personal Access Token).

![Captura de Pantalla 2023-02-23 a la(s) 09 51 50](https://user-images.githubusercontent.com/2066453/220941768-37ac13a9-f947-4950-94d6-e12bead5bab3.png)

Veremos que se ha creado una carpeta en Cloud Shell, creamos una variable para usarla en el terminal de Cloud Shell.

	export PROJECT_ID=TUPROJECTID
	
Ejecutar.

	gcloud config set project $PROJECT_ID

Ejecutar en Cloud Shell (habilitará  algunos servicios).

	gcloud services enable dataform.googleapis.com \
		secretmanager.googleapis.com \
		cloudfunctions.googleapis.com \
		run.googleapis.com \
		cloudscheduler.googleapis.com \
		workflows.googleapis.com \
		datastore.googleapis.com \
		cloudbuild.googleapis.com \
		firestore.googleapis.com \
		dataplex.googleapis.com \
		sourcerepo.googleapis.com \
		artifactregistry.googleapis.com

Nos vamos al servicio de Firestore, clic en NATIVE MODE.

![firestore01](https://user-images.githubusercontent.com/2066453/220948582-70e3255a-b4d1-487c-b092-b48511813303.png)

Seleccionamos la location : name5 (United States)) y clic en Create database.

![firestore02](https://user-images.githubusercontent.com/2066453/220948628-0c1836f8-acc7-4705-93f4-69bfb6da9270.png)

Regresamos a Cloud Shell y ejecutamos los siguientes comandos, para insertar datos en Firestore.

	cd taller-gcp-dataplatform-04
	pip3 install google-cloud-firestore pandas
	python3 datastore/load_rows_datastore.py

Se realiza la inserción de 7 registros en Firestore en una colección.

![Captura de Pantalla 2023-02-23 a la(s) 13 55 33](https://user-images.githubusercontent.com/2066453/221003547-6b574b99-a253-4d35-a30f-65027e15287d.png)

Validamos que los registros se hayan insertado, nos vamos al servicio de Firestore.

![Captura de Pantalla 2023-02-23 a la(s) 13 59 17](https://user-images.githubusercontent.com/2066453/221004321-b0af151c-7f1a-4765-ad44-73955099815b.png)

Fork al repo de Github : https://github.com/luisgradossalinas/dataform-elt-ecommerce-gcp

![Captura de Pantalla 2023-02-23 a la(s) 14 08 12](https://user-images.githubusercontent.com/2066453/221006165-e6221700-6f67-4681-8902-841b5356f2d2.png)

Clic en Create fork.

![Captura de Pantalla 2023-02-23 a la(s) 14 08 33](https://user-images.githubusercontent.com/2066453/221006242-44df3b5a-e64c-413c-9ba3-84cf077fb6b5.png)

Copiamos la url del repo (https://github.com/TU_USUARIO/dataform-elt-ecommerce-gcp).

Crear los recursos desde Terraform.

    cd iac
    terraform init
    
Pegamos el siguiente comando en un txt.

    terraform apply -var="project=$PROJECT_ID" -var="pat_github=REEMPLAZA_TU_PAT" -var="repo_url=TU_URL_REPO_GITHUB"

Una vez modificado, lo ejecutamos en Cloud Shell.

Copiamos el valores de las variables de salida en un txt, lo usaremos en pasos siguientes.

Subir los archivos csv a cloud storage desde un bash ejecutado desde Cloud Shell.

    cd ..
	wget https://datalake-fashionstore.s3.amazonaws.com/data_gcp/distribution_centers/distribution_centers.csv -P data
	wget https://datalake-fashionstore.s3.amazonaws.com/data_gcp/events/events.csv -P data
	wget https://datalake-fashionstore.s3.amazonaws.com/data_gcp/inventory_items/inventory_items.csv -P data
	wget https://datalake-fashionstore.s3.amazonaws.com/data_gcp/order_items/order_items.csv -P data
	wget https://datalake-fashionstore.s3.amazonaws.com/data_gcp/orders/orders.csv -P data
	wget https://datalake-fashionstore.s3.amazonaws.com/data_gcp/products/products.csv -P data
	wget https://datalake-fashionstore.s3.amazonaws.com/data_gcp/users/users.csv -P data

Copiamos el valor de la variable de salida : command_output y lo ejecutamos en Cloud Shell, este comando subir los archivos csv en Cloud Storage.

    sh loadgcs/SH_Load_GCS.sh TU_PROJECT_ID TU_BUCKET

Crear tablas en BigQuery, abrimos y copiamos el contenido del archivo sql/script.sql

Lo pegamos en el Editor de BigQuery y clic en RUN.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218595541-99bec3ed-f8dc-4087-bf47-dd63612feaf3.png">

## Cloud Functions

Nos vamos al servicio de Cloud Functions, y clic en **fnc-read-gcs-write-bigquery**.

Clic en TESTING.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218161259-d79b2dee-df81-4e42-a82f-0c62b8ddf22a.png">

Clic en TEST IN CLOUD SHELL.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218161328-792ea971-9eb3-4163-b0a6-59da8eb2ed54.png">

Le damos enter, para ejecutar la Functions (tomará menos de 2 minutos.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218161443-efa14244-98aa-4256-904c-929d4bb2e5e1.png">

Nos vamos a BigQuery, para validar que efectivamente las tablas estén pobladas.

Nos ubicamos en la tabla distribution_centers, y le damos PREVIEW.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218162196-16f14093-6bb8-487d-b1f0-182040a1b3d6.png">

Luego de validar los datos de las 7 tablas, ejecutamos el siguiente comando, porque la functions será ejecutada desde un workflow.

    TRUNCATE TABLE ecommerce_bronze.distribution_centers;
    TRUNCATE TABLE ecommerce_bronze.events;
    TRUNCATE TABLE ecommerce_bronze.inventory_items;
    TRUNCATE TABLE ecommerce_bronze.order_items;
    TRUNCATE TABLE ecommerce_bronze.orders;
    TRUNCATE TABLE ecommerce_bronze.products;
    TRUNCATE TABLE ecommerce_bronze.users;

<img width="805" src="https://user-images.githubusercontent.com/2066453/218162650-31798cca-4af9-435e-91b1-1ccfec22547d.png">

## Dataform

Nos vamos al servicio de Dataform, veremos un repo llamado **dataform-ecommerce**.

<img width="705" src="https://user-images.githubusercontent.com/2066453/219121091-8b8e3dda-1bb9-4ef0-bf8b-f4633aa8ca0c.png">

Nos vamos al servicio de IAM, para brindar un permiso para que Dataform pueda obtener el valor almacenado del Secret Manager.

Buscamos el service account usado en Dataform y le asignamos el rol Secret Manager Secret Accessor y BigQuery Admin.

![Captura de Pantalla 2023-02-23 a la(s) 16 12 05](https://user-images.githubusercontent.com/2066453/221031306-7ce0e1fc-6613-4e69-9369-8ca2f5acb4a8.png)

Clic en SAVE.

![Captura de Pantalla 2023-02-23 a la(s) 19 07 30](https://user-images.githubusercontent.com/2066453/221059581-0f56da41-532f-4af7-afeb-e53581e51eb1.png)

Volvemos a Dataform, clic el repo dataform-ecommerce y clic en CREATE DEVELOPMENT WORKSPACE, con el nombre de dev.

<img width="505" src="https://user-images.githubusercontent.com/2066453/219121262-5e818e93-c7f9-4d8c-b7fc-aab3d27e78c6.png">

Entrar al archivo dataform.json, reemplazamos el valor TUIDPROYECTO, por el nuestro.

<img width="505" src="https://user-images.githubusercontent.com/2066453/218574170-fd114da1-3999-44fe-bf31-dd171e41a4ec.png">

Clic en COMMIT 2 CHANGES y agregamos un comentario sobre el cambio, clic en COMMIT.

<img width="505" src="https://user-images.githubusercontent.com/2066453/218574285-653e170c-dcf1-4718-b365-90e04de49f13.png">

Clic en PUSH TO REMOTE BRANCH.

Ejecutamos el proyecto de Dataform, y se crearán tablas en los dataset de bigquery silver y gold.

## Workflows

Nos vamos al servicio de Workflows y le damos a workflow-ecommerce, clic en EXECUTE.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218579327-c4449437-1066-46df-9b16-8b53e5c94f02.png">

Clic en EXECUTE.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218579511-b9b184af-7981-488e-b241-3bd7269f2c21.png">

Veremos que se está ejecutando el Workflow (Primero se ejecuta el Cloud Functions y luego el proyecto en Dataform)

<img width="705" src="https://user-images.githubusercontent.com/2066453/218579631-ccecbae2-9bd3-40b0-ae37-40653f5722a8.png">

Ir de nuevo a Dataform y en la pestaña EXECUTIONS veremos que se ha ejecutado las transformaciones en SQL en BigQuery.

<img width="805" src="https://user-images.githubusercontent.com/2066453/219102174-074b06a6-f0ad-4438-818d-aa09a19dcec3.png">

Nos vamos a BigQuery y veremos que existen tablas en los dataset : ecommerce_silver y ecommerce_gold

<img width="205" src="https://user-images.githubusercontent.com/2066453/219102331-9662581d-6f7f-486d-9bd9-f927dd008977.png">

## Cloud Scheduler

Ir al servicio de Cloud Scheduler, veremos uno que dice **scheduler-workflow-ecommerce**.

<img width="805" src="https://user-images.githubusercontent.com/2066453/219107452-21850bc5-351f-4cd8-90ca-909975e62ce2.png">

## Dataplex

Ir al servicio de Dataplex y seleccionamos los Datasets :

	ecommerce_bronze
	ecommerce_silver
	ecommerce_gold
	
<img width="805" src="https://user-images.githubusercontent.com/2066453/218597773-7d91b83b-09ea-4f58-a70b-038cfd17f9a6.png">

Seleccionamos la tabla : sales_by_product

<img width="805" src="https://user-images.githubusercontent.com/2066453/218597979-23bff9e9-9c5d-45f1-9de4-2d52253d8b41.png">

Clic en LINAEGE.

<img width="805" src="https://user-images.githubusercontent.com/2066453/219109732-e2948b3c-4fe8-4277-8eb7-10219fb52d56.png">

## Looker Studio

Ir a la siguiente url : https://lookerstudio.google.com

Crear un gráfico en Looker, clic en Crear Fuente de datos.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218807099-aef6e454-cdba-4ead-9cb5-29ba622cbad2.png">

Seleccionamos BigQuery.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218807202-257662a8-c9a8-4062-8652-256c76e48474.png">

Seleccionamos la tabla sales_by_day y clic en CONECTAR.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218807295-a29b87ba-097e-4dad-bd7c-4ac50ca84d2d.png">

Clic en CREAR INFORME.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218807481-83b8e0af-1f5a-4f1a-af1d-197c2714716b.png">

Aparecerá la siguiente pantalla.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218807754-e55c9eec-18cb-4168-8df4-f7caf9806a75.png">

Clic en Añair un gráfico - Gráfico de columnas.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218807873-99281e0f-c970-45a5-9ce0-fba7f1e5c1a9.png">

Aparecerá el siguiente gráfico.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218807981-d976d0f9-4e94-477f-8df4-851053e84f8c.png">

Definimos como métrica el campo : revenue.

<img width="805" src="https://user-images.githubusercontent.com/2066453/218808113-a88c5c03-2569-4361-89aa-f06fa9ce8d07.png">
