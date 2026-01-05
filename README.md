# Analisis-compras-hospital-publico
# Introduccion
Este Informe se realiza con el proposito de resolver preguntas y esclarecer el contexto acerca de las ordenes de compra de la unidad de Farmacia ambulatoria del Hospital Dr.Lautaro Navarro de la region de Magallanes. Con la necesidad de identificar informacion y posibles factores que sean importantes respecto a las transacciones de tipo compra agil, donde se intentara investigar las razones acerca de variaciones entre los años 2024 y 2025 sobre el gasto en ordenes de compra y la identificacion de medicamentos de alto costo con los que la farmacia ha adquirido, con el proposito de dejar en contexto el estudio acerca de flujo de ordenes y causa de ellas. 
Las metricas importantes a utilizar son el valor total con impuestos, la fecha de envio de las ordenes de compra y la cantidad de ordenes totales del hospital.
Se utiliza el valor unitario de los medicamentos para realizar comparaciones en las graficas de medicamentos mas costosos.

## Analisis en POWER BI

0.5v 26/01/05 Analisis realizado en power bi donde se realizan dashboards explorativos y de presentacion
https://github.com/franco12321232-star/analisis-compras-hospital-publico/blob/Analisis-compra-agil-hospital-publico-powerbi/readmepowerbi.md
-------------------------------------------------------------------------------------------------------------------------------------------------
Las interrogantes son:

 Cerca de periodos suceden las faltas de stock en farmacia ambulatoria?
 
 Cuales son los medicamentos mas costosos de las ordenes de farmacia ambulatoria y falta CENABAST?
 
 Que periodos son donde ocurren la mayor cantidad de ordenes de compra en el año dentro del hospital?

 Cual es el gasto que incurre el hospital? y del area de farmacia y por falta de CENABAST?

## Alcance
Se realiza el estudio en 2 años, desde el 2024-2025, del hospital Dr.Lautaro Navarro de la region de magallanes, se considera solo los datos de compra agil, y exclusivamente los estados de recepcion conforme entran al estudio lo que es un indicador fuerte de compra concretada y recibida. El por que, tiene relacion con otros estados en el mismo atributo como "aceptado" y "enviado al proveedor" que son imprecisos respecto a la recepcion de medicamentos y productos en hospital, por lo que se asume que no fueron concretado ya que el estado nunca fue actualizado como recepcion conforme, asi que se termina omitiendo. Las ordenes de compra con la frase "(SUSPENDIDO)", por falta de informacion respecto a esta descripcion, se deduce que el pedido que se realizó anteriormente llego a hospital y la orden ya se encontraba subida al sistema, por lo que simplemente se agrega la descripcion, pero por alguna razon se mantuvo como una especie de historial de transacciones, para fines de analisis, se conservara este dato; se excluyen todos los insumos de oficina, mantenimiento, muebles y area oncologica que, para este estudio, se interpreta como no asociada a farmacia si no lo dice en el nombre de la orden explicitamente, traslados de emergencia de pacientes, protesis exceptuando las pilas de aparatos auditivos. Se considera todos los datos que mencionan Farmacia (comité farmacia, farmacia unitaria, etc), las iniciales FAR que sean pertinentes y cenabast, con especial atencion en Farmacia ambulatoria, la cual se considera que es suficientemente explicita y es siempre considerada.
Existen estimados de llegada y envio de productos por parte de proveedores, por lo que esos datos quedan exentos por ser simplemente estimados y no un registro o confirmacion de llegada en el mismo hospital, se entiende que existen ordenes de compra agil que se realiza a causa de tardanza de proveedores pero no se va a considerar esto en el estudio, simplemente se vera en el contexto de CENABAST.
##  Procesamiento de los datos 
Se realiza la extraccion de la informacion del sector salud por datos publicos (2024-2025), al ingresar a la pagina de mercado publico, se redirige al portal "https://datos-abiertos.chilecompra.cl/descargas/ordenes-y-licitaciones" donde se descargan las bases de datos comprendidas en periodos de 6 meses. 
<img width="1908" height="856" alt="image" src="https://github.com/user-attachments/assets/46caebc4-2383-4d1e-8644-9e316655776e" />
Se intenta utilizar "https://www.mercadopublico.cl/Portal/Modules/Site/Busquedas/BuscadorAvanzado.aspx?qs=2" y "https://buscador.mercadopublico.cl/ordenes-de-compra?date_from=2025-12-15&date_to=2025-12-16&order_by=recent&page_number=1" pero poseen limitancias respecto a la cantidad de observaciones a descargar, ademas de fuertes problemas en la UI de la pagina web.
## limpieza de datos y pre-analisis
Una vez obtenidas las bases de datos, se realiza el proceso de revision de la integridad de la base de datos por medio de RStudio. Al observarse que no hay errores que afecten al filtrado se procede a seleccionar los datos pertinentes al estudio, luego de extraer por medio de lenguaje en R *(Vease Proyecto hospital.html)* se procede a subir los datos a google sheets para con el objetivo de consolidar las tablas, limpiar datos, borrar espacios, normalizar datos y eliminar columnas extras. Luego se filtra segun palabras clave como Farmacia,(Far), FALTA CENABAST, stock critico. Donde se extraen varios datos utiles para el caso propuesto, se realiza una exploracion de medidas y creacion de graficos para visualizar patrones y formular preguntas, terminando luego con la exportacion a formato .csv con el proposito de crear visualizaciones por tableau para continuar con un analisis mas meticuloso.
Se observa que no existen datos referentes a fechas de pedidos recibidos, lo que hace que la exploracion con el proposito de entender los periodos de falta de medicamento y retraso de pedidos sea mas complejo.
Existe una designacion de forma farmaceutica llamada unidad, la cual puede significar desde cajas enteras de ampolla, medicamento inyectable unitario, tubos de crema, etc. Ademas de la posibilidad de distintas marcas con diferentes presentaciones farmaceuticas. Para este estudio se mantiene la designacion de unidad, sería un grave error asumir formas farmaceuticas, un ejemplo es: 
<img width="1314" height="79" alt="image" src="https://github.com/user-attachments/assets/0f498cc1-06e3-4242-8f2e-cd9f5b2c6f43" />
Se nota un claro ejemplo en donde se puede inferir que las unidades de ibuprofeno ampolla vienen en cajas de X unidades, se entiende asi por que el ValorNetoItem da el costo unitario, siendo este muy alto para una sola ampolla.
<img width="841" height="53" alt="image" src="https://github.com/user-attachments/assets/f9b64ddb-b3e6-4f33-bc57-2d1f9d0e754f" />

Existen fuertes discrepancias en los valores de los atributos:
<img width="946" height="555" alt="image" src="https://github.com/user-attachments/assets/47843fa7-2a96-4259-b22e-da58f3ed4e75" />

Por lo que se considera eliminar las columnas ONUproducto y NombreItem, la razon es que se utiliza como fundamento la DescripcionCotizacion del proveedor para confirmar que el medicamento sea el mismo de la columna DescripcionItem, se potencia mas esta deduccion al usar la DescripcionOC donde tambien contiene informacion respecto al medicamento o producto asociado. 
Se normalizan los datos de la columna descripcionitem utilizando la formula =IF(REGEXMATCH(A1;"^[0-9]");REGEXREPLACE(A1;"^[^ ]+";"");A1). El proposito es eliminar todos los codigos internos de farmacia hasta que llegue a algun espacio. La necesidad de hacerlo viene de que no existe material para poder agregar los codigos interns por propia cuenta, por lo que se procede a mantener los nombres de los medicamentos sin codigo asociado.

<img width="642" height="352" alt="image" src="https://github.com/user-attachments/assets/81397143-7145-469e-9795-3beb8a50ab13" />


Existen datos redundantes y no pertinentes al estudio, por ejemplo la region a estudiar y la institucion, se eliminan columnas como la RegionUnidadCompra, entCode, Institucion, Sector, rubros del proveedor, UnidadCompraRut, etc.

<img width="1699" height="690" alt="image" src="https://github.com/user-attachments/assets/d502f44d-94e4-4121-b295-3812b19cc035" />


Tambien se eliminan los datos de cotizacion como monto cotizado u otros, se considera que puede prestarse a confusion si se necesita manipular datos o realizar mediciones, ya que las variaciones son casi imperceptibles y pueden cruzarse en el analisis al momento de calcular totales.

<img width="1565" height="720" alt="image" src="https://github.com/user-attachments/assets/62f7c703-a538-4677-9a03-35a91d6e8d63" />

### Se revisa por inconsistencias en las observaciones como se describe a continuacion:

Aparecen solo datos nulos de la columna DescuentosOC, ademas de que no se sabe cual es el origen de los descuentos o cual es el criterio y las actividades que se considera para descuento, se omite.
se nota que el porcentaje de impuesto no cambia en los 2 años de analisis, el valor obtenido es de 19% constante excepto por algunas excepeciones como:


<img width="344" height="805" alt="image" src="https://github.com/user-attachments/assets/1d8985d0-d2d8-470e-ae2d-cd3ae9999020" />


Y tambien:


<img width="292" height="719" alt="image" src="https://github.com/user-attachments/assets/4dff12b0-55af-4d7a-8a47-74cf349d70aa" />


Lo cual hace que el calculo con la columna ImpuestosOC salga erronea, por el hecho de que el valor de CargosOC es sumado con Impuestos OC, la variacion es menor pero notoria si se calcula el porcentaje con el valor base.


<img width="97" height="264" alt="image" src="https://github.com/user-attachments/assets/1facd4b6-29e6-4623-8c2d-ce5033cc895e" />
<img width="104" height="185" alt="image" src="https://github.com/user-attachments/assets/6d584992-be11-4e15-8236-bf75cfd99e5d" />


Se incluye la columna de CargosOC por impactar el valor total de las ordenes de compra.

Se descubre un dato referente a una orden de envio, presenta la descripcion (SUSPENDIDO). En este caso y por falta de informacion, se deduce que el pedido que se realizó anteriormente llego a hospital y la orden ya se encontraba subida al sistema, por lo que simplemente se agrega la descripcion, pero se mantiene como una especie de historial de transacciones, para fines de analisis, se conservara este dato.
<img width="1102" height="187" alt="image" src="https://github.com/user-attachments/assets/172ac37f-7d0c-4f19-861b-085a81aaca72" />


Finalizando, se obtiene un total de 193 ordenes de compra despues de todo el proceso de filtrado y limpieza.
# Analisis y visualizacion.
Se utiliza el programa Tableau junto con google sheets para crear las visualizaciones y tablas. se realizan mas correcciones de limpieza, formato de los datos y normalizacion de datos, producto de la exploracion y creacion de graficos, realizandose comparaciones entre años, se utiliza el formato de MES/AÑO para evidenciar mas facilmente la frecuencia de las ordenes de compra.

### Creacion de tabla de analisis de valor total
<img width="1429" height="177" alt="image" src="https://github.com/user-attachments/assets/b242be0c-3063-44f8-bcbc-7610acbd7842" />

Se realiza una tabla de metricas resumen, donde se clasifican las ordenes segun frases o palabras, las cuales se identifican como patrones y se filtran. Se utiliza el monto total de las ordenes de compra, las cuales tienen agregado el impuesto y el cargo del proveedor si es que existe.
Empezando por la columna de la izquierda, se encuentra la columna de total stock critico, el cual se consideran todas las ordenes de compra que llevan la palabra "stock critico", se menciona que dentro de esta clasificacion tambien hay ordenes de stock critico proveniente de farmacia ambulatoria y farmacia unitaria, por lo que se da jerarquia a stock critico mas que las otras clasificaciones. La columna CENABAST son todas las ordenes que son faltas de suministro por parte de esta institucion, farmacia ambulatoria, dosis unitaria y comite de farmacia son areas internas del hospital, controlados son los medicamentos que su condicion de venta es de controlados, por ejemplo el metilfenidato, lisdexanfetamina, etc. Otros sin clasificar concierne todo lo que no sea las clasificaciones dadas(CENABAST, FARMACIA AMBULATORIA, STOCK CRITICO, ETC.) entrando ciertos insumos como pilas de audifono, programas VIH entre otros.
El gasto total del hospital en ordenes es de $186.614.862, Cenabast con un $36.988.402 y farmacia ambulatoria con $46.137.003. En total, el gasto de ambos tipos de ordenes llegarian a $83.125.406, un 44,54% de todo el gasto en ordenes.

## Visualizaciones de la tabla
<img width="894" height="554" alt="image" src="https://github.com/user-attachments/assets/938fc6ab-b2a4-466e-83d5-588a68b1eca5" />

Lo que queda evidente es que gran parte del costo proviene de farmacia ambulatoria con un 24,7%, si se compara esta proporcion, la cual es de $46.137.003 en contraste a CENABAST y STOCK CRITICO, que llegan a $49.329.647 con un 26,43%. Parte del gasto esta concentrado en el abastecimiento de farmacia y se ve que el 26,43% se ocupa en cubrir faltas de CENABAST y reposicion de stock cuando llega a niveles criticos, lo cual supera el valor de las ordenes de farmacia ambulatoria en un 1.73%.

### Creacion de tabla de analisis de frecuencia de ordenes


Farmacia ambulatoria presenta un aumento en su cantidad de ordenes de 22 a 34, lo cual equivale al aumento notorio de un 7,6%, desde 20,25% el 2024 a un 27,85%
<img width="1410" height="608" alt="image" src="https://github.com/user-attachments/assets/7d682183-ba32-47cb-ad6f-fd0ea0806a2e" />


La elaboracion de la tabla se realiza basado en las tablas dinamicas y llamando directamente los datos, se ve que hay una gran cantidad de ordenes entre mayo y septiembre del 2024. no se evidencia un patron semejante en el 2025, exceptuando los principios y finales de año, que muestran una cantidad de pedidos iguales, con poca frecuencia. Se nota que de marzo en adelante los pedidos comienzan a aumentar aunque la frecuencia se vuelve mucho mas irregular, donde hay periodos de pocos pedidos y altos pedidos, siendo junio-julio un momento de pedidos relativamente altos. La cantidad de ordenes de FALTA CENABAST dentro de los 2 años suma en total 46, en contraste con farmacia ambulatoria que son 56 ordenes. Si se comparan las ordenes de FALTA CENABAST sumados con stock critico, llegan a 61 ordenes, lo cual es importante ya que estos 2 tipos de pedido son marcadores de falta o alerta de falta en el caso de las ordenes de stock critico. 
<img width="597" height="369" alt="image" src="https://github.com/user-attachments/assets/a7ee509d-a14a-4718-a9d4-e3ab2f39f4ac" />
<img width="601" height="377" alt="image" src="https://github.com/user-attachments/assets/cb49c288-d687-44df-a7bb-f4542e40a328" />


## Analisis de ordenes de "FALTA CENABAST"
Se crea un dashboard comparativo de ordenes de compra con la frase "FALTA CENABAST", donde se nota que la mayor cantidad de ordenes ocurre entre mitades del año 2024 y 2025, con una gran cantidad de ordenes a principios del año 2025, ademas la cantidad de ordenes anuales aumentan de 20 a 26, es posible que grandes cantidades de falta de de productos y medicamentos por cenabast sea un problema recurrente de años anteriores y seria interesante realizar un analisis de gran escala con comparativa de otros hospitales de esta region y otros fuera de la region para tener una comparativa de los quiebres y "faltas de cenabast", donde se podria saber si el proveedor, dificultad geografica, tiempo de recepcion de la orden de compra y poblacion son factores de prioridad o dificultad en el despacho.  

Se obtiene que las ordenes por falta de envios de parte de CENABAST equivale al 19,82% de las ordenes totales de compra rapida en 2 años.
<img width="780" height="478" alt="image" src="https://github.com/user-attachments/assets/7a8eb6d2-edbe-4638-be8c-267090923308" />

El cambio en la participacion en el valor total, o mejor llamado, gastos, por parte de CENABAST, es de un 10,63% a un 26,24% lo que significa que aumenta en un 15,61%.


<img width="560" height="429" alt="image" src="https://github.com/user-attachments/assets/abbf2902-4093-4de3-b398-d269b3a6f787" /> 


Se observa que las ordenes de compra son mas frecuentes entre los meses de junio-agosto del 2024 y junio-septiembre del 2025. A principios de año 2025, el mes de abril llega a 7 ordenes realizadas.

<img width="554" height="425" alt="image" src="https://github.com/user-attachments/assets/81bd7800-3080-415d-bb44-5767ad7c1be5" />

La cantidad de productos que llegan a farmacia es mucho mas alta a mitades de año, siendo el carvedilol 6,25 con 45.000 unidades y la metformina 850mg con 31.000 unidades. 

reemplazar por graficas con notas

Coincidentemente, el 2025 tambien realiza ordenes de grandes cantidades a mitades de año, siendo el levetiracetam de 1000mg  con 11.340 unidades el mas solicitado, seguido de zopiclona con 7.200 unidades.

 reemplazar por graficas ocn notas


### Valor total de medicamentos cenabast (cambiar titulo graficas para ser mas claro)
Se crea una grafica comparativa entre medicamentos de los años 2024 y 2025, donde se ve cual es el medicamento dentro de las ordenes de "Falta cenabast" que representa la mayor fraccion del valor total de estas ordenes.
<img width="1251" height="483" alt="image" src="https://github.com/user-attachments/assets/3af3c440-2743-471d-947a-0e3044290cbe" />


La metformina de 850mg es el 15,83% del valor total de todos los medicamentos de cenabast del 2024 con $1.291.150 y la albumina humana 20% representa el 23,53% del valor total de todos los medicamentos de cenabast del 2025 $6.783.000 (vease anexo del analisis de *comparativa de medicamentos por año*). Hay un gran contraste de cantidad/precio con fluticasona 27,5 mcg suspension nasal y tambien timolol maleato 0,5% solucion oftalmica dentro del 2024, siendo 180 unidades de fluticasona con un valor total de $1.155.395, el valor unitario es de $5.394 y su valor neto es de $970.920 y timolol maleato al 0,5% , 160 unidades con un valor total de $1.144.305, su precio unitario es de $6.010 y el valor neto es de $961.600; ademas de cinacalcet 60mg cm y levetiracetam 100mg/ml solucion oral.
Por ahora el medicamento con el valor total mas alto es la metformina 850mg con $1.291.150, la cual se pidieron 31.000 unidades y el medicamento con la mayor cantidad solicitada es el carvedilol 6,25mg con 45.000 unidades. El valor neto unitario de ambos medicamentos es de $35 y $21 respectivamente.

<img width="1239" height="476" alt="image" src="https://github.com/user-attachments/assets/f8920188-7671-47d0-bdee-d95e279f216b" />

## Medicamentos de alto coste y medicamentos de gran cantidad
 La grafica concierne todos los medicamentos dentro del periodo de 2 años y de valor total con impuestos como aparece en la primera grafica, y los medicamentos con grandes cantidades solicitadas dentro de la farmacia ambulatoria, sin contar stcok critico y CENABAST.
 
<img width="1103" height="631" alt="image" src="https://github.com/user-attachments/assets/61e1e7b9-7c77-4dc9-a5e1-44de92bf91ac" />

La fluticasona 50 MCG INH, proveniente de orden de FARMACIA AMBULATORIA MARZO / FLUTICASONA 50 MCG INH presenta el mayor valor unitario con $70.001, seguido de sofosbuvir-velpatasvir de las ordenes FARMACIA AMBULATORIA URGENTE DICIEMBRE / SOFOSBUVIR-VELPATASVIR 400-100 MG CM y FARMACIA AMBULATORIA DICIEMBRE / SOFOSBUVIR-VELPATASVIR 400-100 MG CMcon $51.000 y tercero la pancreolipasa de 5000UI en granulos de la orden FARMACIA AMBULATORIA JULIO / PANCREOLIPASA 5.000 UI GRANULOS con $34.805

<img width="1341" height="618" alt="image" src="https://github.com/user-attachments/assets/26c7b874-f2ae-4d86-80a2-082ec344f2a0" />
La ISOSORBIDE DINITRATO 10 MG CM es el medicamento que lidera en cantidades ordenadas con 23115 unidades, luego lo seguiria la TIAMINA CLORHIDRATO 10 MG CM  y PROPRANOLOL 10 MG CM con 18000 y 12000 unidades respectivamente.

# Conclusion

Los periodos de mayor pedido de ordenes por "FALTA CENABAST" son entre junio-agosto, lo que coinciden con periodos de mayor emision de pedidos y  lo que se puede suponer es que el hospital tiende a pedir stock por falta de medicamentos los mismos periodos en los años 2024-2025 segun lo presentado en el analisis, ademas cabe agregar que el aumento del coste de las ordenes, ademas del aumento en la participacion de las ordenes conciernes a cenabast en un 15,61% da a entender que es uno de los factores de inflacion del gasto total en hospital.
Es de importancia dar enfasis en estos periodos de mayor frecuencia de ordenes de pedido, principalmente por que es muy posible que las areas de bodega puede que no esten preparadas para flujos mayores en estos meses, pero para dar tal seguridad hace falta datos de ordenes de compra de tipo licitaciones para saber cual es el flujo constante de compras para entender todo el contexto.
Respecto a "FALTA CENABAST" y "STOCK CRITICO" se debe utilizar estas metricas para entender los posibles problemas de abastecimiento que puede enfrentar la farmacia a lo largo del año y seguir de cerca las incidencias de faltas de envio por parte del proveedor CENABAST, se puede realizar una conclusion mucho mas completa si se tienen datos de años anteriores respecto a la incidencia de problemas con el proveedor.
Segun se analizó, los medicamentos mas costosos de la farmacia son la fluticasona 50MCG, corticoide inhalador y sofosbuvir-velpatasvir 400-100mg en comprimidos, medicamento antiviral, las grandes cantidades de medicamento pedido la lidera la isosorbida de 10mg, vasodilatador, seguida de la tiamina de 10mg, vitamina B1.
Como se concluyó los periodos con mayor cantidad de ordenes son a mitades de año, donde se concentra gran cantidad de pedidos como junio-septiembre en el 2024 y en el 2025 seria abril y periodo junio-julio.

Los pedidos de alta prioridad como "FALTA CENABAST" y Stock critico, junto con farmacia ambulatoria son el 51,13% del gasto en estos 2 años, con una predominancia de 117 ordenes totales de 193 existentes en el estudio, que equivalen al 60.6% de las ordenes de compra, se considera que por la naturaleza de este tipo de compra, son adqusiciones urgentes que justifican el presupuesto de farmacia para mantener el correcto funcionamiento, pero el problema esta en la capacidad de preveer los quiebres de stock por CENABAST, lo cual es un dilema que impulsa a utilizar los procesos de compra agil con mas frecuencia al evidenciarse el crecimiento de las ordenes a lo largo de estos 2 años. Se necesita un mecanismo de prevision frente a tardanzas por parte del proveedor que este estudio no va a cubrir.
Se determina que parte del aumento en gastos de compra agil son principalmente de ordenes cenabast y farmacia, con cebabast llega a valores de $8.156.588 el 2024 a $28.831.814 en el 2025, farmacia ambulatoria llega de $15.538.045 del año 2024 a $30.598.958 en 2025. Esto apunta la necesidad de correr un analisis mas exaustivo referente a los problemas de stock por parte de proveedores, se recomienda fijarse en tendencias de crecimiento de estas 2 areas en los años anteriores

## Anexo limpieza

## ejemplo de DescripcionOC y DescripcionCotizacion utilizadas para apoyar criterio
Se utilizan las descripciones como apoyo, se buscan palabras claves y se comparan con la observacion en DescripcionItem si hay alguna duda respecto a ello.
<img width="1882" height="495" alt="image" src="https://github.com/user-attachments/assets/ebb0ce69-0e1a-442f-8e3f-f83fcbf339c1" />
<img width="1830" height="718" alt="image" src="https://github.com/user-attachments/assets/4be4eedb-4a52-46cd-9937-ff069c1ac870" />

Otra discrepancia en columna tamaño proveedor, se usa como ejemplo de normalizacion de los datos, no es pertiente al estudio y se puede eliminar sin problemas.

<img width="500" height="291" alt="image" src="https://github.com/user-attachments/assets/5b90de73-87ef-4a6e-992e-5e6c14dd402c" />
<img width="371" height="492" alt="image" src="https://github.com/user-attachments/assets/6191799c-2a6c-4f5e-826b-7e24e6e9d4e9" />

Se crea una tabla con el proposito de observar por medio de mediciones, los datos observados:

<img width="362" height="62" alt="image" src="https://github.com/user-attachments/assets/903f118b-d8b5-40bd-87cc-57be9e92731f" />

Errores de digitacion del nombre CENABAST y correcion por medio de la herramienta "Find and Replace"


<img width="1542" height="737" alt="image" src="https://github.com/user-attachments/assets/f3fed651-df59-4f18-af45-dcf1da4d7e7d" />


Error en digitacion CRÍTICO por CRITICO y correcion por medio de la herramienta "Find and Replace"


<img width="1905" height="911" alt="image" src="https://github.com/user-attachments/assets/0e20c538-4ba8-4fd9-94f2-3304906dcd6f" />


Se observa que los caracteres especiales *()* pueden hacer que el contenido sea omitido por el filtrado, ocultando datos pertinentes para el estudio


<img width="1153" height="222" alt="image" src="https://github.com/user-attachments/assets/0f675da7-ec1c-4718-8fae-c036adf496c4" />

Hay 3 instancias de error en el nombre del medicamento AMBRISENTÁN, por lo que se corrige:
<img width="1252" height="143" alt="image" src="https://github.com/user-attachments/assets/e6dd47d4-02f7-4458-af7f-5cd82515b7c8" />

RILPIRIVINA - CABOTEGRAVIR 900-600 MG FA y RILPIRIVINA - CABOTEGRAVIR 900-600 MG FA son el mismo producto ya que el valor neto del item es el mismo, el formato y dosis son las mismas, ademas de que hubo errores en digitar este medicamento, se corregira como RILPIRIVINA - CABOTEGRAVIR 900-600 MG
<img width="1573" height="79" alt="image" src="https://github.com/user-attachments/assets/7234a6a4-01f3-4847-8396-46e61a6eb7bf" />
<img width="685" height="89" alt="image" src="https://github.com/user-attachments/assets/7c19a6ea-ca5f-43c6-a2ee-5cf66f8bd864" />


Existe repeticion de observaciones respecto a las pilas de audifono. Se deduce que como la descripcion de la orden pide 3 modelos de pila, N°675, N°13 y N°312. cada pedido de modelo de pila repite la orden asociada, por lo que habria 9 datos existentes, se procedera a eliminar esos datos extras para ser mas preciso en el analisis.

<img width="1320" height="362" alt="image" src="https://github.com/user-attachments/assets/eed7bddc-a0fb-478a-9f29-38a4d9376e1e" />
<img width="1833" height="192" alt="image" src="https://github.com/user-attachments/assets/f7b9d7c6-899d-475a-9a1b-e4392cb8d381" />

## anexo visualizacion 

### Frecuencia de ordenes
<img width="1622" height="444" alt="image" src="https://github.com/user-attachments/assets/72f7bedd-6fdb-4ea1-ad44-9ca17217a140" />
<img width="1616" height="430" alt="image" src="https://github.com/user-attachments/assets/40bcc296-af9e-4479-a5fe-f518c43ae3a7" />

Los periodos de julio-septiembre puede que sea importante observar simplemente por la cantidad de ordenes urgentes de stock critico y falta cenabast.

## Comparativa valor de medicamentos por año
En el año 2025 hay medicamentos que lideran en valor total con valores unitarios de $30000 y $13.800, la ALBUMINA HUMANA 20% SOL. PERF.IV FAM 50ML con 190 unidades y lactulosa 65-66 100ml con 210 unidades. 
Tambien existe una variacion del valor unitario de la CICLOFOSFAMIDA 1G FA POLVO LIOFILIZADO, de $14.144 a $21.238, segun la formula ((14.144-21238)/14144)*100, tiene un aumento de un 50,1% en su precio unitario.
Levetiracetam 1000mg cm es el medicamento de mayor cantidad ordenada, con 159 unidades
<img width="924" height="492" alt="image" src="https://github.com/user-attachments/assets/8a7921a5-3a2c-4824-8d2d-4daaf8afe901" />
<img width="1010" height="528" alt="image" src="https://github.com/user-attachments/assets/a77c06e1-69cc-4db3-94b6-ae8c4316d68c" />

