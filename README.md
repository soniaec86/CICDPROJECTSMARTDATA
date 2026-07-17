# CICDPROJECTSMARTDATA

El objetivo del proyecto final es obtener las estadisticas del monto que se recupero por empresa, visualizando la cantidad de cuentas a las cuales se le retiro el saldo y clasificando por producto.


 # $\color{blue}{INSUMOS}$

* Archivo ppe_000_062026_1.csv .- archivo con el listado de cuentas a las cuales de les retiro el saldo
* Archivo cliente.json .- archivo catalogo de clientes registrados en la empresa y contiene la información si se desea que el monto se excente
* Archivo producto.json .- archivo catalogo de productos que ofrece la empresa
* Archivo cuentas.csv .- archivo catalogo de cuentas que se tienen registradas.

  <img width="1907" height="617" alt="image" src="https://github.com/user-attachments/assets/2bebc0c7-6010-4201-88c9-548d88056d98" />
  
# $\color{blue}{ARQUITECTURA}$

Se basa en un arquitectura madallion para estructurar datos de manera lógica dentro de un data lakehouse. Su objetivo principal es incrementar la calidad, confiabilidad y utilidad de los datos a medida que avanzan por distintas capas, desde datos en bruto hasta información lista para análisis y toma de decisiones estratégicas.

  <img width="1112" height="461" alt="image" src="https://github.com/user-attachments/assets/f0ee3e46-e3f7-47b7-bbab-4b9e5e014b22" />

La arquitectura Medallion es un patrón de diseño de datos que organiza la información en capas (bronze, silver, gold), para llegar a esto se creo un acuenta de azure, se creo una suscripción, grupo de recursos, cuenta de almacenamiento y se aprovisiono dos recursos de databricks una para desarrollo y la segunda para el ambiente productivo, es necesario crear los dos dabricks en la misma region para que compartan el mismo **metastore**, este permite trabajar con **Unity Catalog** para introducir un modelo de gobernanza basado en un metastore a nivel de cuenta, compartir metadatos y permisos entre múltiples espacios de trabajo. Esto facilita la administración centralizada sin comprometer la seguridad ni la flexibilidad. Además, incluye un modelo jerárquico de tres niveles: catálogos, esquemas y tablas, que organiza los datos de manera granular y escalable.

 # $\color{blue}{DESARROLLO}$

se aprovisionan todos los recursos necesarios para el desarrollo del proyecto.

<img width="1916" height="522" alt="image" src="https://github.com/user-attachments/assets/05cf278e-432c-403d-8200-b8dc27ff7828" />

<img width="1901" height="622" alt="image" src="https://github.com/user-attachments/assets/5bc20260-cfd0-4608-9bf4-a83f8b6a48e6" />

<img width="1906" height="412" alt="image" src="https://github.com/user-attachments/assets/376b31d7-7087-4abb-a956-0b8087b76c7c" />

El tipo de conexión con el storage account es **identity manager**, ligado al access conector de databricks


<img width="1915" height="872" alt="image" src="https://github.com/user-attachments/assets/8be82f2d-8ea4-41a1-a1dd-f0a390a2189d" />

<img width="1911" height="502" alt="image" src="https://github.com/user-attachments/assets/78937609-6728-40f8-a02a-9bdba3361c9a" />

En databrick debemos de crear el cluster con la siguientes caracteristicas que va a permitir la ejecución de los notebook, ejecución del WF, etc, se crea en el ambiente de desarrollo para realizar nuestras pruebas y garantizar que en producción va a ejecutar correctamente. 

<img width="1905" height="950" alt="image" src="https://github.com/user-attachments/assets/561c7f7b-420e-4b5e-bba4-d8b14a04bc1a" />

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

para la creación y ejecución automatica en producción del WF, se crea el YAML3, basado en la esctructura de la plantilla donde se definen los secretos del databricks origen y destino, la lista de notebook a ejecutar, directorio de ejecución, los parametros de entrada(storage_account,container, cataog, schema) segun lo requiera el task y con el nombre que se estable en los notebooks, nombre del cluster. 

<img width="1907" height="947" alt="image" src="https://github.com/user-attachments/assets/5f4860fd-579e-4791-83e1-6252e1efc2b2" />

**CREACIÓN DE SECRETOS EN GITHUB UTILIZADOS EN YML**

<img width="1881" height="877" alt="image" src="https://github.com/user-attachments/assets/4b545bab-ec18-43f5-ab3d-55ac93d438af" />


### Nota
Antes del despligue a producción es necesario considerar que el ClusterSD se encuentre creado y encendido en el databricks **producción** con el mismo nombre que configuramos en el YML3, asi una vez realizada la integración a producción se dispare la creacion y ejecución del job.

<img width="1907" height="353" alt="image" src="https://github.com/user-attachments/assets/61fa6049-4565-4461-8f50-4bf3ba0a524c" />


 # $\color{blue}{PRODUCCIÓN}$ 

 Para el despliegue al ambiente de producción vamos a utilizar la plataforma **github** para lo cual es necesario crear una cuenta y una rama alterna a main en este caso se llama construccion, es necesario vincular github con databricks, en databricks desarollo se crea Gi folder.

 <img width="873" height="340" alt="image" src="https://github.com/user-attachments/assets/5ee260c2-9d84-4e2e-b46f-51009d519f4a" />

Cuando se encuentran vinculados las dos aplicaciones en databrick en workspace se vera el directorio con el nombre que agregamos en github y el icono de la carpeta es asi <img width="26" height="17" alt="image" src="https://github.com/user-attachments/assets/abf4d411-3bcb-4406-b088-840bdd772783" />

en este directorio colocamos todas las carpetas y archivos que deseamos deplegar en el ambiente productivo, el paso siguiente es realizar el commit a la rama construcción <img width="118" height="25" alt="image" src="https://github.com/user-attachments/assets/07a469c8-e810-48de-8c1d-071e7b86a190" />

<img width="1912" height="568" alt="image" src="https://github.com/user-attachments/assets/80864e80-b9cc-4b47-81f6-6f3fe2a343fe" />

se abre cuan cuadro de dialogo con el listado de los objetos que detecto github se agregan o modifican, se agrega una descripción del commit a realizar y se presiona el boton **commit & push*, cuando termine el con el commit se informa que es correcto el commit

<img width="1907" height="957" alt="commitpush" src="https://github.com/user-attachments/assets/fdfa0f18-52ae-4a36-a13e-67976a198fff" />

el siguiente y ultimo es crear un pull request para realizar merge de la rama construcción a main, con esto todos los archivos se van a crear en nuestro ambiente productivo. y tambien el despligue de manera automatica del **YML** que se creo para la ejecución del WF en el ambiente de **PRODUCCIÓN**

<img width="1897" height="636" alt="Merge" src="https://github.com/user-attachments/assets/19f58ac9-3b95-438e-bbf7-2daf2e5ef32f" />

github notifica que el despligue fue correcto en producción y para validar, vamos al ambiente productivo y validamos la creación y ejecución correcta del workflows.

<img width="1912" height="817" alt="WF_PRODUCCION" src="https://github.com/user-attachments/assets/9e91fea0-e4dd-4d70-91bc-69e08852a287" />

<img width="1887" height="882" alt="Ejecucion_WF_Product" src="https://github.com/user-attachments/assets/bdb2d9a8-5817-47b3-b661-3461ffed46a4" />

## * creación del catalogo y tablas

<img width="1901" height="757" alt="catalog_product" src="https://github.com/user-attachments/assets/bcf639c8-ddca-4d2d-b661-7385a0e59747" />


### 👤 Autor
### Sonia Ezquibel Cervantes












 














  

  




