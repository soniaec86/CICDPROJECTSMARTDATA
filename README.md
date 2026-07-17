# CICDPROJECTSMARTDATA

El objeto del proyecto final es obtener las estadisticas del monto que se recupero por empresa, visualizando la cantidad de cuentas a las cuales se le retiro el saldo y clasificando por producto.


 # $\color{blue}{INSUMOS}$

* Archivo ppe_000_062026_1.csv .- archivo con el listado de cuentas a las cuales de les retiro el saldo
* Archivo cliente.json .- archivo catalogo de clientes registrados en la empresa y contiene la información si se desa que el monto se excente
* Archivo producto.json .- archivo catalogo de productos que ofrece la empresa
* Archivo cuentas.csv .- archivo catalogo de cuentas que se tienen registradas.

  <img width="1907" height="617" alt="image" src="https://github.com/user-attachments/assets/2bebc0c7-6010-4201-88c9-548d88056d98" />
  
# $\color{blue}{ARQUITECTURA}$

Se basa en un arquitectura madallion para estructurar datos de manera lógica dentro de un data lakehouse. Su objetivo principal es incrementar la calidad, confiabilidad y utilidad de los datos a medida que avanzan por distintas capas, desde datos en bruto hasta información lista para análisis y toma de decisiones estratégicas.

  <img width="1112" height="461" alt="image" src="https://github.com/user-attachments/assets/f0ee3e46-e3f7-47b7-bbab-4b9e5e014b22" />

La arquitectura Medallion es un patrón de diseño de datos que organiza la información en capas (bronze, silver, gold), para llegar a esto se creo un acuenta de azure y se creo una suscripción, grupo de recursos y cuenta de almacenamiento y se aprovisiono dos recursos de databricks una para desarrollo y la segunda para el ambiente productivo, es necesario crear los dos dabricks en la misma region para que compartan el mismo **metastore**, con este trabajaremos bajo **Unity Catalog** para introducir un modelo de gobernanza basado en un metastore a nivel de cuenta, lo que permite compartir metadatos y permisos entre múltiples espacios de trabajo. Esto facilita la administración centralizada sin comprometer la seguridad ni la flexibilidad. Además, incluye un modelo jerárquico de tres niveles: catálogos, esquemas y tablas, que organiza los datos de manera granular y escalable.

 # $\color{blue}{DESARROLLO}$

<img width="1916" height="522" alt="image" src="https://github.com/user-attachments/assets/05cf278e-432c-403d-8200-b8dc27ff7828" />

<img width="1901" height="622" alt="image" src="https://github.com/user-attachments/assets/5bc20260-cfd0-4608-9bf4-a83f8b6a48e6" />

<img width="1906" height="412" alt="image" src="https://github.com/user-attachments/assets/376b31d7-7087-4abb-a956-0b8087b76c7c" />

El tipo de conexión con el storage account es **identity manager**, ligado al access conector de databricks


<img width="1915" height="872" alt="image" src="https://github.com/user-attachments/assets/8be82f2d-8ea4-41a1-a1dd-f0a390a2189d" />

<img width="1911" height="502" alt="image" src="https://github.com/user-attachments/assets/78937609-6728-40f8-a02a-9bdba3361c9a" />

del lado de databricks es necesario la creacion del credencial y los external location uno por cada contenedor del data lake, estos ultimos se crearon en el notebook de prepracion de ambiente (localizado en la carpeta de proceso o bien en la carpeta de PreparacionAmbiente) donde tambien se crea el catalogo **catalog_project** y las tablas de las capas bronze, silver y golden 

<img width="1907" height="440" alt="image" src="https://github.com/user-attachments/assets/cfa84567-8fba-45c2-9108-1c9ebd4cc446" />

<img width="1912" height="597" alt="image" src="https://github.com/user-attachments/assets/6dd53ea3-cc03-496d-a61b-46ee676fc365" />

<img width="1917" height="867" alt="image" src="https://github.com/user-attachments/assets/864e0b28-b91d-4224-9f05-0f8bbb1ac944" />

En la carpeta proceso se listan los notebook creados para ingesta y transformacion de las datos almacenados en los archivos que tenemos en nuestro contendor origen raw, siguiendo con nuestra arquitectura se ingesta cada entidad en la capa **bronze** generando un **.parquet y delta_log** de la ejecución.

<img width="1902" height="472" alt="bronze-fileppe" src="https://github.com/user-attachments/assets/64044262-47a0-4003-8eff-11615abaa380" />

<img width="1907" height="486" alt="bronze-accounts" src="https://github.com/user-attachments/assets/a1e9d62a-05d1-4c74-9de8-cf1c9f44e5d6" />

<img width="1907" height="515" alt="bronze-client" src="https://github.com/user-attachments/assets/b8b9a989-53f2-440e-93c8-0751924003a8" />

<img width="1907" height="452" alt="image" src="https://github.com/user-attachments/assets/f78c5b70-1bf0-4b3b-bd1d-829afe52539a" />

En la capa **silver**, se realizan las transformaciones necesarias de acuerdo a nuestro modelo o finalidad del proyecto, inner join entre tablas, sumatorias, agregar campos, conteo, etc. al igual que en bronze, se obtienen los .parquet y delta_log.

<img width="1907" height="497" alt="silver-cuentasbyclient" src="https://github.com/user-attachments/assets/5945e634-fb3e-422a-a3e1-e681d3bf7f05" />

<img width="1907" height="495" alt="silver-totalbyproduct" src="https://github.com/user-attachments/assets/b00b4b79-7536-456d-a864-c329b6caebaf" />

En la capa **golden**, es la ultima capa en la cual los datos estan limpios y confiables, listos para el usuario final y la muestra de nuestras estadisticas representadas en los dashboard.

<img width="1912" height="492" alt="golden-accountsbyclient" src="https://github.com/user-attachments/assets/e33776b0-f7cc-4369-89f7-575f39bb8e87" />

<img width="1911" height="516" alt="golden-totalbyproduct" src="https://github.com/user-attachments/assets/042ae236-fa59-43fd-b90d-f49b3615a59f" />

<img width="1707" height="700" alt="Monto total recaudado por producto_1" src="https://github.com/user-attachments/assets/f2ba09e6-e81c-455a-99be-f638a0e20729" />

<img width="1697" height="875" alt="Total de cuentas por cliente" src="https://github.com/user-attachments/assets/45dc6b1c-8c4a-4fe4-ada6-8407ef5fe509" />

 # $\color{blue}{PRODUCCIÓN}$ 




 














  

  




