# Estadística con R 

* R es un lenguaje especializado en implementar técnicas estadísticas. 

* Utilizaremos R para realizar un análisis estadístico básico.

## Resumen estadístico

El archivo "datos de empleados.sav" de SPSS es un conjunto de datos ampliamente utilizado en análisis estadísticos y gestión de recursos humanos. Este archivo contiene información detallada sobre los empleados de una organización, abarcando diversas variables como edad, género, departamento, puesto de trabajo, salario, años de servicio, nivel educativo y rendimiento laboral. Gracias a esta riqueza de datos, los analistas pueden explorar patrones y tendencias dentro de la fuerza laboral, lo que facilita la toma de decisiones informadas en áreas cruciales como la contratación, capacitación, evaluación del desempeño y retención de empleados.

Por ejemplo, los datos pueden utilizarse para identificar diferencias salariales entre distintos géneros o departamentos, analizar la relación entre la antigüedad y el rendimiento laboral, o evaluar el impacto de la formación en el desarrollo profesional. Además, la estructura del archivo "datos de empleados.sav" permite la aplicación de diversas técnicas estadísticas y métodos de análisis, como regresión, análisis de varianza y pruebas de hipótesis, proporcionando un enfoque robusto para abordar cuestiones complejas en la gestión de recursos humanos.


![Datos Empleados](Figures/Datos_empleados.png){width=100%}

**Las funciones más elementales tienen una sintaxis muy intuitiva**


```r
library(readxl)
bbdd <- read_xlsx("Datos/Datos_de_empleados.xlsx")
# bbdd$fechnac <- as.Date(as.numeric(bbdd$fechnac),format = "%Y%m%d")
knitr::kable(head(bbdd[,c(2,6:7)],6))
```



|sexo | salario| salini|
|:----|-------:|------:|
|h    |   57000|  27000|
|h    |   40200|  18750|
|m    |   21450|  12000|
|m    |   21900|  13200|
|h    |   45000|  21000|
|h    |   32100|  13500|

**Para calcular algunos indicadores básicos se utilizan comandos sencillos**


```r
> # Min, max,...
> min(bbdd$salario)
#> [1] 15750
> max(bbdd$salario)
#> [1] 135000
> which.max(bbdd$salario)
#> [1] 29
> bbdd$salario[which.max(bbdd$salario)]
#> [1] 135000
> sum(bbdd$salario)
#> [1] 16314875
```

### Medidas de posición central

Las medidas de tendencia central son estadísticas fundamentales que describen el punto medio o típico de un conjunto de datos. Entre las más utilizadas se encuentran la media aritmética, la mediana y la moda. La media aritmética, o promedio, se calcula sumando todos los valores y dividiéndolos por el número total de observaciones, proporcionando una medida del centro basada en todos los datos. La mediana, en cambio, es el valor que divide el conjunto de datos en dos mitades iguales, siendo especialmente útil cuando hay valores atípicos o distribuciones sesgadas, ya que no se ve afectada por extremos. La moda es el valor que aparece con mayor frecuencia en el conjunto de datos y puede ser útil para identificar el valor más común en una distribución. Cada una de estas medidas ofrece una perspectiva diferente sobre la centralidad de los datos y se elige según las características del conjunto de datos y el contexto del análisis


```r
> # Media y Mediana de Salario
> mean(bbdd$salario)
#> [1] 34419.57
> median(bbdd$salario)
#> [1] 28875
> 
> # Es fácil calcular la media geométrica
> x <- bbdd$salario
> n <- length(x)
> prod(x)^(1/n)
#> [1] Inf
> geom <- exp(mean(log(x)))
> print(geom)
#> [1] 31470.09
> 
> # También la media armónica
> armo <- 1/mean(1/x)
> print(armo)
#> [1] 29366.07
```

**Algunos detalles sobre valores perdidos**

En R, los valores faltantes están representados por el símbolo NA (no disponible). 
Los valores imposibles (por ejemplo, la división por cero) están representados por el símbolo NaN (no es un número)


```r
> x <- c(1,2,NA,3)
> mean(x) # devuelve NA
#> [1] NA
> sum(x, na.rm=TRUE) # devuelve 2
#> [1] 6
> mean(x, na.rm=TRUE) # devuelve 2
#> [1] 2
```

**Igualmente se obtien en medidas de posición no central**

Las medidas de posición no central, como los cuartiles, deciles y percentiles, son herramientas estadísticas que dividen un conjunto de datos en partes iguales para analizar su distribución. Los cuartiles dividen los datos en cuatro partes: el primer cuartil (Q1) indica el 25% inferior, el segundo cuartil (Q2) es la mediana, y el tercer cuartil (Q3) representa el 75% inferior. Los deciles y percentiles proporcionan divisiones más finas, en diez y cien partes, respectivamente.


```r
quantile(bbdd$salario)
```

      0%      25%      50%      75%     100% 
 15750.0  24000.0  28875.0  36937.5 135000.0 

```r
quantile(bbdd$salario,c(0,.15,.85))
```

     0%     15%     85% 
15750.0 22050.0 50027.5 

### Medidas de dispersión absolutas

Las medidas de dispersión absolutas cuantifican la variabilidad o dispersión de un conjunto de datos respecto a su centro. Entre las más comunes se encuentran el rango, la desviación media y la desviación estándar. El rango es la diferencia entre el valor máximo y el mínimo, proporcionando una medida simple de la extensión de los datos. La desviación media calcula el promedio de las diferencias absolutas entre cada dato y la media, ofreciendo una visión general de la dispersión. La desviación estándar mide la dispersión de los datos respecto a la media.


```r
> # Medidas de dispersión absolutas
> range(bbdd$salario)
#> [1]  15750 135000
> IQR(bbdd$salario) # Rango intercuartílico
#> [1] 12937.5
> sd(bbdd$salario) # Desviación estándar
#> [1] 17075.66
> var(bbdd$salario) # Varianza
#> [1] 291578214
```

### Medidas de asimetría y curtosis

Las medidas de asimetría y curtosis evalúan la forma y la distribución de un conjunto de datos. La asimetría indica si los datos están sesgados hacia un lado: una asimetría positiva sugiere una cola larga a la derecha, mientras que una negativa indica una cola larga a la izquierda. La curtosis mide la "agudeza" de la distribución: una curtosis alta (leptocúrtica) indica colas más pesadas y un pico más pronunciado, mientras que una baja (platicúrtica) señala colas más ligeras y un pico más plano. Estas medidas son cruciales para comprender la distribución de los datos más allá de la media y la dispersión


```r
if(!require(moments)) {install.packages("moments")}
#> Loading required package: moments
library(moments)

skewness(bbdd$salario) #nos da el valor de la asimetria de los datos de la variable x
```

[1] 2.117877

```r
kurtosis(bbdd$salario) #nos da el achatamiento de la distribucion de los datos de la variable x.
```

[1] 8.30863

### La función 'summary()'

La función 'summary()' en R proporciona un resumen estadístico de un objeto, como un conjunto de datos o un modelo. Para data frames, incluye medidas como la media, mediana, mínimos, máximos y cuartiles, ofreciendo una visión rápida y completa de las características clave del conjunto de datos


```r
# Resumen de la variable
summary(bbdd$salario)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>   15750   24000   28875   34420   36938  135000
# También para una data frame
BBDD <- as.data.frame(bbdd[,c(3,6:7)])
summary(BBDD)
#>    fechnac             salario           salini     
#>  Length:474         Min.   : 15750   Min.   : 9000  
#>  Class :character   1st Qu.: 24000   1st Qu.:12488  
#>  Mode  :character   Median : 28875   Median :15000  
#>                     Mean   : 34420   Mean   :17016  
#>                     3rd Qu.: 36938   3rd Qu.:17490  
#>                     Max.   :135000   Max.   :79980
# Un summary por categorías
# Añadimos variable al data frame
bbdd$MiVariable <- floor(bbdd$tiempemp/30)
by(bbdd$salario, bbdd$MiVariable, summary)
#> bbdd$MiVariable: 2
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>   15750   23438   27900   33087   35250  103500 
#> --------------------------------------------- 
#> bbdd$MiVariable: 3
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>   16200   25200   30825   38021   40875  135000
```

### Actividad

1. Importar datos del archivo "datos de empleados":

2. Calcular medidas de tendencia central (media y mediana) y de dispersión (rango, desviación estándar) para una variable numérica, por ejemplo, el salario.

3. Calcular medidas de asimetría y curtosis para la misma variable.

## Gráficos con R

* Las gráficas son la mejor forma de simplificar lo complejo. 
* Un buen gráfico suele ser más accesible que una tabla. Sin embargo es muy importante tener claro qué gráfico queremos hacer.
* Las facilidades gráficas de R constituyen una de las componentes más importantes de este lenguaje.
* R incluye muchas y muy variadas funciones para hacer gráficas estadísticas estándar: desde gráficos muy simples a figuras de gran calidad para incluir en artículos y libros.
* Permite además construir otras nuevas a la medida del usuario (aunque a veces hacer cosas simples no es fácil).
* Permite exportar gráficas en distintos formatos: PDF, JPEG, GIF, etc.
* Para ver una demo de gráficos con colores: demo(graphics).
* Aquí únicamente veremos algunas de todas las posibilidades.
* Otra alternativa son los paquetes: ggplot y ggplot2.

**La función plot()**

La función plot() en R se utiliza para crear gráficos básicos. Es fundamental para visualizar datos, permitiendo la generación de gráficos de dispersión, líneas, barras, y más. Con diversos argumentos y opciones de personalización, plot() facilita el análisis visual y la interpretación de conjuntos de datos complejos.


```r
> x <- (0:100)/10
> y <- sin(x)
> plot(x, y, main="Función Seno")
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-9-1.png" width="672" />

** Algunas opciones de la función plot()**

* **main:** Cambia el título del gráfico
* **sub:** Cambia el subtítulo del gráfico
* **type:** Tipo de gráfico (puntos, líneas, etc.)
* **xlab, ylab:** Cambia las etiquetas de los ejes
* **xlim, ylim:** Cambia el rango de valores de los ejes
* **lty:** Cambia el tipo de línea; lwd: Cambia el grosor de línea
* **col:** Color con el que dibuja


```r
plot(x, y, main="Seno", type="l")
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-10-1.png" width="672" />

```r
plot(x, y, main="Función Seno", lty=2, col="red", type="l")
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-10-2.png" width="672" />

```r
plot(x, cos(x), main="Función Coseno", lty=3, col="blue", type="l",xlim=c(0, 2), ylab="cos(x)")
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-10-3.png" width="672" />

**Gráfico de sectores**


```r
pie(c(3,5,8))
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-11-1.png" width="672" />

```r
pie(c(3,5,8), labels=c("Uno","Dos","Tres"),col=c("blue","red","green"),main="Mi gráfico")
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-11-2.png" width="672" />

**La función boxplot realiza este clásico gráfico**

La función boxplot() en R se usa para crear gráficos de caja, que visualizan la distribución de un conjunto de datos a través de sus cuartiles. Muestra la mediana, los cuartiles y los posibles valores atípicos, permitiendo identificar la dispersión, simetría y anomalías en los datos


```r
# Con una sola variable
boxplot(bbdd$salario,col=c('powderblue'))
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-12-1.png" width="672" />

```r
# Con las tres variables a la vez
boxplot(bbdd$salario,bbdd$salini,col=c('powderblue','#FF6D0099'))
# Los dos juntos
par(mfrow=c(1,2))
boxplot(bbdd$salario,bbdd$salini,col=c('powderblue','#FF6D0099'))
boxplot(log(bbdd$salario),log(bbdd$salini),col=c('powderblue','#FF6D0099'))
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-12-2.png" width="672" />

**Escribe `colors()` para una lista de colores**

### ggplot2

El paquete ggplot2 en R es una herramienta poderosa para la creación de gráficos y visualizaciones de datos. Basado en la gramática de gráficos de Hadley Wickham, ggplot2 permite construir visualizaciones complejas de manera flexible y coherente. Utiliza un sistema de capas, donde cada capa representa una parte del gráfico, como los datos, los estéticos, y los elementos geométricos. Los usuarios pueden personalizar gráficos con facilidad, añadiendo títulos, etiquetas, y temas. Además, ggplot2 facilita la visualización de relaciones entre variables, distribuciones, y patrones, convirtiéndose en una opción popular para el análisis exploratorio de datos y la comunicación de resultados


```r
library(ggplot2)
Distr <- as.factor(bbdd$sexo)
qplot( x=Distr , y=salario , data=bbdd , geom=c("boxplot","jitter") , fill=Distr) + theme_bw()
#> Warning: `qplot()` was deprecated in ggplot2 3.4.0.
#> This warning is displayed once every 8 hours.
#> Call `lifecycle::last_lifecycle_warnings()` to see where
#> this warning was generated.
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-13-1.png" width="672" />

```r
Distr <- as.factor(bbdd$catlab)
qplot( x=Distr , y=salario , data=bbdd , geom=c("boxplot","jitter") , fill=Distr) +
theme_bw()
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-13-2.png" width="672" />

**Con ggplot se pueden hacer boxplots muy bonitos**

[https://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf]

También violin-plots

[https://www.data-to-viz.com/caveat/boxplot.html]

**Histograma**


```r
hist(log(bbdd$salario), breaks=20, col = "red")
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-14-1.png" width="672" />

**Se puede incluir la curva normal sobre el histograma**


```r
g <- log(bbdd$salario)
m <- mean(g)
std<-sqrt(var(g))
hist(g, density=10, breaks=20, prob=TRUE,col="blue", 
     xlab="log(Renta)", 
     main="Curva normal sobre histograma")
curve(dnorm(x, mean=m, sd=std), 
      col="red", lwd=4, add=TRUE)
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-15-1.png" width="672" />

### Actividad

1. Crear un histograma


```r
ggplot(empleados, aes(x = salario)) +
  geom_histogram(binwidth = 1000, fill = "blue", color = "black") +
  labs(title = "Distribución del Salario de los Empleados", x = "Salario", y = "Frecuencia") +  theme_minimal()
```

2. Crear un gráfico de caja (boxplot) para comparar los salarios por departamento


```r
ggplot(empleados, aes(x = departamento, y = salario, fill = departamento)) +
  geom_boxplot() +
  labs(title = "Distribución del Salario por Departamento", x = "Departamento", y = "Salario") +
  theme_minimal()
```

## Test Hipótesis

### Un simple t-test de igualdad de medias

El contraste de medias basado en la t de Student es una técnica estadística para comparar las medias de dos grupos y determinar si hay una diferencia significativa entre ellas. Se utiliza cuando los datos siguen una distribución normal y el tamaño de la muestra es pequeño. La prueba t calcula el valor t, que se compara con un valor crítico de la distribución t para decidir si se rechaza la hipótesis nula de igualdad de medias. Existen dos tipos principales: la prueba t para muestras independientes, que compara dos grupos distintos, y la prueba t para muestras dependientes, que compara dos mediciones en el mismo grupo


```r
# Contrastar si la Renta media de 2015 es 40000 euros
t.test(bbdd$salario,mu=40000)
```


	One Sample t-test

data:  bbdd$salario
t = -7.1151, df = 473, p-value = 4.168e-12
alternative hypothesis: true mean is not equal to 40000
95 percent confidence interval:
 32878.40 35960.73
sample estimates:
mean of x 
 34419.57 

```r
t.test(bbdd$salario,mu=35000,alternative="greater")
```


	One Sample t-test

data:  bbdd$salario
t = -0.74005, df = 473, p-value = 0.7702
alternative hypothesis: true mean is greater than 35000
95 percent confidence interval:
 33126.96      Inf
sample estimates:
mean of x 
 34419.57 

Puede usar la opción var.equal = TRUE para especificar varianzas iguales y una estimación de varianza agrupada. Puede usar la opción alternative = "less" o alternative = "greater" para especificar una prueba de una cola

### Un simple t-test de igualdad de medias pareadas


```r
t.test(bbdd$salario,bbdd$salini)
#> 
#> 	Welch Two Sample t-test
#> 
#> data:  bbdd$salario and bbdd$salini
#> t = 20.152, df = 665.3, p-value < 2.2e-16
#> alternative hypothesis: true difference in means is not equal to 0
#> 95 percent confidence interval:
#>  15707.74 19099.22
#> sample estimates:
#> mean of x mean of y 
#>  34419.57  17016.09
t.test(log(bbdd$salario),log(bbdd$salini))
#> 
#> 	Welch Two Sample t-test
#> 
#> data:  log(bbdd$salario) and log(bbdd$salini)
#> t = 28.163, df = 932.96, p-value < 2.2e-16
#> alternative hypothesis: true difference in means is not equal to 0
#> 95 percent confidence interval:
#>  0.6394884 0.7352878
#> sample estimates:
#> mean of x mean of y 
#> 10.356793  9.669405
```

**Un poco mas complicado: ANOVA de un factor**

El ANOVA de un factor (Análisis de Varianza) es una técnica estadística utilizada para comparar las medias de tres o más grupos independientes y determinar si existen diferencias significativas entre ellas. Este análisis evalúa si la variabilidad entre las medias de los grupos es mayor que la variabilidad dentro de los grupos. El ANOVA de un factor calcula un estadístico F, que compara la variabilidad entre los grupos con la variabilidad dentro de los grupos. Si el valor F es suficientemente alto, se rechaza la hipótesis nula de igualdad de medias. Es útil para experimentos con un único factor categórico y múltiples niveles.


```r
group <- as.factor(bbdd$catlab)
levels(group) <- c("asa","aaas","asddd")
anova <- aov(bbdd$salario ~ group, data = bbdd)
summary(anova)
#>              Df    Sum Sq   Mean Sq F value Pr(>F)    
#> group         2 8.944e+10 4.472e+10   434.5 <2e-16 ***
#> Residuals   471 4.848e+10 1.029e+08                   
#> ---
#> Signif. codes:  
#> 0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
TukeyHSD(anova)
#>   Tukey multiple comparisons of means
#>     95% family-wise confidence level
#> 
#> Fit: aov(formula = bbdd$salario ~ group, data = bbdd)
#> 
#> $group
#>                 diff       lwr       upr     p adj
#> aaas-asa    3100.349 -1657.805  7858.503 0.2768689
#> asddd-asa  36139.258 33251.225 39027.291 0.0000000
#> asddd-aaas 33038.909 27761.979 38315.839 0.0000000
```

### Test de normalidad paramétricos

**Test Paramétricos: K-S y Shapiro**

Los test de normalidad Kolmogorov-Smirnov (KS) y Shapiro-Wilk son utilizados para evaluar si un conjunto de datos sigue una distribución normal. El test KS compara la distribución empírica de los datos con una distribución normal teórica, evaluando las diferencias entre las funciones de distribución acumulada observada y esperada. El test Shapiro-Wilk, en cambio, evalúa la normalidad ajustando una estadística basada en los valores ordenados de la muestra. Mientras que el test KS es más general y se aplica a cualquier distribución, el Shapiro-Wilk es más específico para la normalidad y suele ser más potente para muestras pequeñas. Ambos test ayudan a validar supuestos en análisis estadísticos.


```r
g <- log(bbdd$salario)
ks.test(g, pnorm, mean(g), sd(g))
#> Warning in ks.test.default(g, pnorm, mean(g), sd(g)): ties
#> should not be present for the Kolmogorov-Smirnov test
```


	Asymptotic one-sample Kolmogorov-Smirnov test

data:  g
D = 0.13563, p-value = 5.343e-08
alternative hypothesis: two-sided

```r
shapiro.test(g)
```


	Shapiro-Wilk normality test

data:  g
W = 0.92568, p-value = 1.438e-14

Tenga en cuenta que, la prueba de normalidad es sensible al tamaño de la muestra. Las muestras pequeñas con mayor frecuencia pasan las pruebas de normalidad. Por lo tanto, es importante combinar la inspección visual y la prueba de significación para tomar la decisión correcta.

## Distribuciones bivariadas

### Tablas de contingencia 

Una tabla de correlación muestra las relaciones entre variables cuantitativas, indicando la fuerza y dirección de la asociación mediante coeficientes de correlación, como Pearson o Spearman. Permite identificar patrones de dependencia lineal o no lineal entre pares de variables. Por otro lado, una tabla de contingencia, también conocida como tabla de frecuencia cruzada, se utiliza para analizar la relación entre dos variables categóricas. Muestra la frecuencia de ocurrencia conjunta de las categorías de las variables, facilitando el análisis de asociaciones y la prueba de independencia mediante chi-cuadrado. Ambas tablas son herramientas fundamentales para la exploración y análisis de datos en estadística.

1. La función **table()** calcula tablas de frecuencias a partir de factores.


```r
> msa <- mean(bbdd$salario) # Salario
> msi <- mean(bbdd$salini) # SAl ini
> Ricos <- bbdd$salario > msa # ricos/pobres
> Ricosi <- bbdd$salini > msi # densos/no dendos
> # Distribuciones de frecuencia unidimensional
> table(Ricos)
#> Ricos
#> FALSE  TRUE 
#>   329   145
> table(Ricosi)
#> Ricosi
#> FALSE  TRUE 
#>   345   129
> # Distribuciones de frecuencia bidimensionales
> TaRiRi <- table(Ricos,Ricosi)
> print(TaRiRi)
#>        Ricosi
#> Ricos   FALSE TRUE
#>   FALSE   310   19
#>   TRUE     35  110
> TaRiSx <- table(Ricos,bbdd$sexo)
> print(TaRiSx)
#>        
#> Ricos     h   m
#>   FALSE 138 191
#>   TRUE  120  25
> 
> # Como siempre, hay gráficos informativos
> library(graphics)
> mosaicplot(TaRiRi,main = "Ricos vs Ricosi")
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-22-1.png" width="672" />

```r
> mosaicplot(TaRiSx,main = "Ricos vs Sexo",color = TRUE)
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-22-2.png" width="672" />

### Algo de test de independencia

El test de independencia chi-cuadrado ($\chi^2$) se utiliza para evaluar si dos variables categóricas están asociadas o son independientes entre sí. Se basa en la comparación entre las frecuencias observadas en una tabla de contingencia y las frecuencias esperadas si las variables fueran independientes. Calcula un estadístico $\chi^2$ que se compara con un valor crítico de la distribución chi-cuadrado, considerando el número de grados de libertad. Si el estadístico $\chi^2$ es mayor que el valor crítico, se rechaza la hipótesis nula de independencia, indicando una asociación significativa entre las variables. Este test es útil para analizar relaciones en datos categóricos.


```r
> # Contrastes Chi2 de independencia
> chisq.test(TaRiRi)
#> 
#> 	Pearson's Chi-squared test with Yates' continuity
#> 	correction
#> 
#> data:  TaRiRi
#> X-squared = 246.05, df = 1, p-value < 2.2e-16
> chisq.test(TaRiSx)
#> 
#> 	Pearson's Chi-squared test with Yates' continuity
#> 	correction
#> 
#> data:  TaRiSx
#> X-squared = 65.953, df = 1, p-value = 4.618e-16
```

### Correlaciones

Una matriz de correlaciones es una tabla que muestra las correlaciones entre múltiples variables cuantitativas. Cada celda indica el coeficiente de correlación entre un par de variables, facilitando la identificación de relaciones lineales y patrones de asociación en grandes conjuntos de datos.


```r
> cor(bbdd$salario,bbdd$salini)
#> [1] 0.8801175
> cor(bbdd$salario,bbdd$expprev)
#> [1] -0.09746693
> # Un plot con correlaciones
> library(ggcorrplot)
> ggcorrplot(cor(bbdd[,6:9]),lab_size = 3,hc.order = TRUE,method = "circle",lab = TRUE)
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-24-1.png" width="672" />


```r
> # para saber si es significativamente distinta de cero
> cor.test(bbdd$salario,bbdd$salini)
#> 
#> 	Pearson's product-moment correlation
#> 
#> data:  bbdd$salario and bbdd$salini
#> t = 40.276, df = 472, p-value < 2.2e-16
#> alternative hypothesis: true correlation is not equal to 0
#> 95 percent confidence interval:
#>  0.8580696 0.8989267
#> sample estimates:
#>       cor 
#> 0.8801175
> plot(bbdd$salario,bbdd$salini, main="Salario vs Salario Inicial", col='red')
```

<img src="02-Sesion2-Estadistica-Basica-con-R_files/figure-html/unnamed-chunk-25-1.png" width="672" />
