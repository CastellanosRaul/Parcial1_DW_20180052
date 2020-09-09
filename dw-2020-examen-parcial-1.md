dw-2020-parcial-1-RaulCastellanos-20180052
================
Tepi
9/3/2020

# Examen parcial

Indicaciones generales:

  - Usted tiene el período de la clase para resolver el examen parcial.

  - La entrega del parcial, al igual que las tareas, es por medio de su
    cuenta de github, pegando el link en el portal de MiU.

  - Pueden hacer uso del material del curso e internet (stackoverflow,
    etc.). Sin embargo, si encontramos algún indicio de copia, se
    anulará el exámen para los estudiantes involucrados. Por lo tanto,
    aconsejamos no compartir las agregaciones que generen.

## Sección I: Preguntas teóricas.

  - Existen 10 preguntas directas en este Rmarkdown, de las cuales usted
    deberá responder 5. Las 5 a responder estarán determinadas por un
    muestreo aleatorio basado en su número de carné.

  - Ingrese su número de carné en `set.seed()` y corra el chunk de R
    para determinar cuáles preguntas debe responder.

<!-- end list -->

``` r
set.seed(20180052) 
v<- 1:10
preguntas <-sort(sample(v, size = 5, replace = FALSE ))

paste0("Mis preguntas a resolver son: ",paste0(preguntas,collapse = ", "))
```

    ## [1] "Mis preguntas a resolver son: 1, 2, 4, 5, 9"

### Listado de preguntas teóricas

1.  Para las siguientes sentencias de `base R`, liste su contraparte de
    `dplyr`:
      - `str()`
      - `df[,c("a","b")]`
      - `names(df)[4] <- "new_name"` donde la posición 4 corresponde a
        la variable `old_name`
      - `df[df$variable == "valor",]`

#### Respuesta 1 alternativas de las funciones con dplyr

  - Estructura del dataset `str(df)`, Alternativa con dplyr:
    `glimpse(df)`
  - Seleccion de columnas `df[,c("a","b")]`, Alternativa con dplyr:
    `select(df, "a", "b")`
  - Renombrar columnas `names(df)[4] <- "new_name"`, Alternativa con
    dplyr: `rename(df, Nuevo_Nombre = Nombre_a_reemplazar)`
  - Filtro de datos `df[df$variable == "valor",]`, Alternativa con
    dplyr: `filter(df, Dato_par_filtrar)`

<!-- end list -->

2.  Al momento de filtrar en SQL, ¿cuál keyword cumple las mismas
    funciones que el keyword `OR` para filtrar uno o más elementos una
    misma columna?

#### Respuesta 2

Una alternativa de `OR` podría ser `WHERE` con `AND`, a que con `WHERE`
podremos especificar la columna con una condicion y el `AND` nos ayudará
a agregar más condiciones.

3.  ¿Por qué en R utilizamos funciones de la familia apply
    (lapply,vapply) en lugar de utilizar ciclos?

4.  ¿Cuál es la diferencia entre utilizar `==` y `=` en R?

#### Respuesta 4

El `==` se utiliza para realizar una comparación lógica, por ejemplo
`1==2 -> FALSE`, al utilizar `=` es asignarle un valor a una variable,
por ejemplo `A = 5, A tomará el valor 5`.

5.  ¿Cuál es la forma correcta de cargar un archivo de texto donde el
    delimitador es `:`?

#### Respuesta 5

Utilizamos la función `read_delim()` por medio de la libraria `readr`,
en esta funcion podemos especificar la data y su delimitador, por
ejemplo `read_delim(file = "data", delim = :")`

6.  ¿Qué es un vector y en qué se diferencia en una lista en R?
7.  ¿Qué pasa si quiero agregar una nueva categoría a un factor que no
    se encuentra en los niveles existentes?
8.  Si en un dataframe, a una variable de tipo `factor` le agrego un
    nuevo elemento que *no se encuentra en los niveles existentes*,
    ¿cuál sería el resultado esperado y por qué?
      - El nuevo elemento
      - `NA`
9.  En SQL, ¿para qué utilizamos el keyword `HAVING`?

#### Respuesta 9

Se utiliza para agregar un filtro adicional, por ejemplo: `SELECT * FROM
datos GROUP BY (Edades) HAVING Edades > 18` esto nos permite agrupar las
edades por año agregando el filtro por \> 17 por medio de `HAVING` el
cual le indica al query que agrupe a las edades tomando en cuenta que
sean de 18 o más años.

10. Si quiero obtener como resultado las filas de la tabla A que no se
    encuentran en la tabla B, ¿cómo debería de completar la siguiente
    sentencia de SQL?
    
      - SELECT \* FROM A \_\_\_\_\_\_\_ B ON A.KEY = B.KEY WHERE
        \_\_\_\_\_\_\_\_\_\_ = \_\_\_\_\_\_\_\_\_\_

Extra: ¿Cuántos posibles exámenes de 5 preguntas se pueden realizar
utilizando como banco las diez acá presentadas? (responder con código de
R.)

## Sección II Preguntas prácticas.

  - Conteste las siguientes preguntas utilizando sus conocimientos de R.
    Adjunte el código que utilizó para llegar a sus conclusiones en un
    chunk del markdown.

A. De los clientes que están en más de un país,¿cuál cree que es el más
rentable y por qué?

B. Estrategia de negocio ha decidido que ya no operará en aquellos
territorios cuyas pérdidas sean “considerables”. Bajo su criterio,
¿cuáles son estos territorios y por qué ya no debemos operar ahí?

### II. Preguntas prácticas.

``` r
library(dplyr)
library(huxtable)
DataParcial <- readRDS("parcial_anonimo.rds")
```

## A

``` r
Paises <- DataParcial %>% group_by(Pais) %>% summarise(Cant_Por_Paises = n())
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

``` r
Clientes <- DataParcial %>% group_by(Cliente, Pais) %>% summarise(Unidades = sum(`Unidades plaza`),
                                                           Venta = sum(Venta)) 
```

    ## `summarise()` regrouping output by 'Cliente' (override with `.groups` argument)

``` r
Duplicados <- Clientes[duplicated(Clientes$Cliente),]
Duplicados <- Duplicados$Cliente #Nombres de los clientes que estan duplicados (dos paises)

Clientes <- Clientes %>% filter(Cliente %in% Duplicados)
Clientes <- Clientes %>% group_by(Cliente) %>% summarise(VentasTotales = sum(Venta), 
                                                         Unidades = sum(Unidades))
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

``` r
ClientesRentables <- Clientes %>% arrange(desc(VentasTotales))
ClientesRentables$VentasTotales <- format(ClientesRentables$VentasTotales, big.mark = ",")

#El cliente más rentable es 
ClientesRentables[1,]
```

``` 
                ┌─────────────────────────────────────┐
                │ Cliente    VentasTotales   Unidades │
                ├─────────────────────────────────────┤
                │ a17a7558   19,817.70       2.27e+03 │
                └─────────────────────────────────────┘
```

Column names: Cliente, VentasTotales,
Unidades

``` r
#ya que es el cliente que está en los dos países y tiene más unidades ordenadas registradas causando también un número alto en el monto total de ventas.
```

## B

``` r
Territorio <- DataParcial %>% select(Territorio, Venta, `Unidades plaza`)
Territorio <- Territorio %>% filter(Venta <0)
Territorio <- Territorio %>% group_by(Territorio) %>% 
  summarise(Ventas = sum(Venta),Unidades = sum(`Unidades plaza`)) %>% 
  arrange(Ventas)
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

``` r
Territorio <- Territorio %>% filter(Ventas < -200)

#Una pérdida considerable en ventas, se definirá a las ventas menores de 200, los que tengan pérdidas mayores a este número serán descartados. Los códigos y ventas de estos territorios se mostrarán en la siguiente tabla

Territorio[,1:2]
```

``` 
                      ┌──────────────────────────┐
                      │ Territorio        Ventas │
                      ├──────────────────────────┤
                      │ f7dfc635       -1.5e+04  │
                      │ 77192d63       -5.64e+03 │
                      │ 72520ba2       -3.76e+03 │
                      │ 69c1b705       -3.37e+03 │
                      │ 1d407777       -3.3e+03  │
                      │ bc8e06ed       -3.27e+03 │
                      │ 2e812869       -3.06e+03 │
                      │ 67e9cc18       -2.72e+03 │
                      │ 8f79b7f8       -1.86e+03 │
                      │ a0d39798       -1.78e+03 │
                      │ 75298f79       -1.57e+03 │
                      │ b50e91fb       -1.52e+03 │
                      │ fed6647d       -1.43e+03 │
                      │ a9e783db       -1.19e+03 │
                      │ c57e6d42       -1.18e+03 │
                      │ 0c169a3b       -1.17e+03 │
                      │ b97335a1       -1.14e+03 │
                      │ 7a861731     -792        │
                      │ 23e9d55d     -765        │
                      │ 4ca9988b     -758        │
                      │ abfa1d4e     -740        │
                      │ e49916a2     -674        │
                      │ 4814799f     -664        │
                      │ 9fdcc550     -663        │
                      │ 4856cd94     -638        │
                      │ 3cae948b     -626        │
                      │ 1c81fb6c     -481        │
                      │ 0dd30fcd     -479        │
                      │ 45c0376d     -475        │
                      │ 680cec1c     -389        │
                      │ bcdf2ef9     -369        │
                      │ 0ef0ce97     -348        │
                      │ a7ee3287     -295        │
                      │ a7291d87     -286        │
                      │ 7b674f31     -283        │
                      │ 67696f68     -254        │
                      │ dfd48934     -251        │
                      │ 91e7e31b     -249        │
                      │ 8682908b     -245        │
                      │ 80d1e625     -232        │
                      │ b77669c5     -231        │
                      │ c31adb2f     -230        │
                      │ 68de9759     -226        │
                      │ 3153c73e     -221        │
                      │ 0bbe6418     -218        │
                      └──────────────────────────┘
```

Column names: Territorio, Ventas
