<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Excepciones". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.

### Respuesta

En C no existen excepciones como mecanismo del lenguaje. Por tanto,
cuando ocurre un error dentro de una función, debe indicarse de forma
explícita mediante valores de retorno o parámetros adicionales. La
función no puede "interrumpir" automáticamente el flujo del programa
como ocurre en Java.

Una primera opción consiste en devolver un valor especial que indique
error. Por ejemplo, podría devolverse -1 cuando se recibe un número
negativo, suponiendo que ese valor no sea válido como resultado real.

``` c
#include <stdio.h>
#include <math.h>

float raiz(float x) {
    if (x < 0)
        return -1;   // valor especial de error
    return sqrt(x);
}

int main() {
    float r = raiz(-4);
    if (r == -1)
        printf("Error: número negativo\n");
    else
        printf("Resultado: %f\n", r);
}
```

Una segunda opción consiste en usar un parámetro adicional (por ejemplo,
un puntero a entero) para indicar si ha ocurrido error.

``` c
float raiz(float x, int *error) {
    if (x < 0) {
        *error = 1;
        return 0;
    }
    *error = 0;
    return sqrt(x);
}
```

## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

### Respuesta

Una excepción es un mecanismo del lenguaje que permite indicar que se ha
producido una situación anómala durante la ejecución de un programa. En
lugar de devolver simplemente un valor especial, se genera un objeto que
representa el error y que puede interrumpir el flujo normal del
programa.

El objetivo principal es separar el código que realiza el trabajo normal
del código que gestiona los errores. Esto permite escribir funciones más
limpias, ya que no es necesario comprobar manualmente cada posible error
tras cada llamada.

## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

### Respuesta

En Java, una forma habitual de modelar este caso consiste en lanzar una
excepción cuando el argumento es inválido. Así se evita devolver valores
especiales ambiguos y se obliga al código llamador a decidir cómo
gestionar el error. En este ejemplo se usa `IllegalArgumentException`
porque recibir un número negativo para calcular una raíz cuadrada se
considera un uso incorrecto del método.

El control del error se realiza desde fuera del método `raiz`, en el
`main`, mediante un bloque `try-catch`.

```java
public class Calculadora {
    public static double raiz(double x) {
        if (x < 0) {
            throw new IllegalArgumentException(
                "No se puede calcular la raíz de un número negativo: " + x
            );
        }
        return Math.sqrt(x);
    }

    public static void main(String[] args) {
        try {
            double resultado = Calculadora.raiz(-4);
            System.out.println("Resultado: " + resultado);
        } catch (IllegalArgumentException e) {
            System.out.println("Error controlado desde main: " + e.getMessage());
        }
    }
}
```


## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

### Respuesta

Lanzar una excepción significa crearla y transferir el control de
ejecución hacia el mecanismo de manejo de errores mediante `throw`.
Capturarla o controlarla significa interceptarla en un bloque `catch`
para tratarla (mostrar un mensaje, transformar el error, registrar
información, etc.). Si ningún `catch` adecuado aparece en el método
actual, la excepción se propaga al método llamador.

Durante la propagación, la pila de llamadas se va desenrollando: cada
método intermedio termina de forma abrupta y no continúa por su flujo
normal. Esos métodos no se reanudan después automáticamente; se abandona
su ejecución en el punto donde ocurrió el error. Solo se ejecutan los
bloques `finally` pendientes antes de seguir propagando.

```java
public class Calculadora {
    static double raiz(double x) {
        if (x < 0) throw new IllegalArgumentException("x negativo");
        return Math.sqrt(x);
    }

    static void procesar() {
        double r = raiz(-9);
        System.out.println(r); // no se ejecuta
    }

    public static void main(String[] args) {
        try {
            procesar();
        } catch (IllegalArgumentException e) {
            System.out.println("Capturada en main: " + e.getMessage());
        }
    }
}
```


## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

### Respuesta

La propagación natural evita tener que comprobar códigos de error en
cada llamada intermedia. En C, cuando no existen excepciones del
lenguaje, cada función suele tener que devolver un estado y el llamador
debe revisarlo de forma explícita. Eso introduce código repetitivo,
aumenta la posibilidad de olvidar comprobaciones y mezcla lógica normal
con lógica de error.

En Java, el error sube automáticamente hasta un manejador adecuado. Esto
permite que las funciones intermedias se centren en su responsabilidad
principal y que el tratamiento del fallo se concentre donde tenga más
sentido funcional. Como resultado, el código suele ser más legible,
modular y mantenible.


## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

### Respuesta

Sí, en Java las excepciones son objetos (instancias de clases que
heredan de `Throwable`). Al ser objetos, encapsulan datos y
comportamiento útiles del error: mensaje, causa, tipo concreto y traza
de pila. Esa encapsulación permite transportar información completa sin
depender de variables globales ni códigos numéricos opacos.

Además, se pueden crear excepciones personalizadas para expresar errores
del dominio de forma más clara. Por ejemplo, `SaldoInsuficienteException`
o `ConfiguracionInvalidaException` hacen que el contrato de una API sea
más explícito y facilitan un manejo selectivo con `catch` por tipo.


## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

### Respuesta

Un objeto excepción aporta, como mínimo, el tipo de excepción y un
mensaje descriptivo. El tipo permite distinguir categorías de error y
decidir un tratamiento diferente en cada `catch`. El mensaje aporta
contexto textual para diagnóstico o para informar al usuario.

También incluye la traza de pila (*stack trace*), que indica la secuencia
de métodos y líneas por donde pasó el error. En muchos casos se añade
además la causa (`cause`), lo que permite encadenar excepciones y
conservar el origen real del problema al transformar errores entre capas.


## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

### Respuesta

Sí, en Java se pueden declarar varios bloques `catch` después de un
`try`. Se evalúan en orden de arriba a abajo, y cada uno define el tipo
de excepción que puede manejar. Por ello, conviene colocar primero las
excepciones más específicas y dejar las más generales al final.

Sin embargo, para una excepción concreta solo se ejecuta un único
`catch`: el primero compatible con su tipo. Tras ejecutar ese `catch`,
el control continúa después del bloque completo `try-catch-finally`
(salvo que dentro del `catch` se lance otra excepción).


## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

### Respuesta

Para garantizar la liberación de recursos se usa `finally`. El código en
ese bloque se ejecuta tanto si hay excepción como si no la hay, y también
cuando se hace `return` desde `try` o `catch`. Por eso se utiliza para
cierre de ficheros, conexiones o liberación de bloqueos.

Ejemplo con `catch` y `finally`:

```java
try {
    System.out.println(Calculadora.raiz(-1));
} catch (IllegalArgumentException e) {
    System.out.println("Error: " + e.getMessage());
} finally {
    System.out.println("Liberando recursos (con catch)");
}
```

Ejemplo con `try-finally` sin `catch` (la excepción se sigue propagando):

```java
try {
    System.out.println(Calculadora.raiz(-1));
} finally {
    System.out.println("Liberando recursos (sin catch)");
}
```


## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

### Respuesta

Sí, `finally` puede aparecer sin `catch`, siempre que exista un `try`.
La combinación `try-finally` es válida y se usa cuando no se quiere
manejar localmente la excepción pero sí asegurar limpieza de recursos.

En condiciones normales, `finally` se ejecuta tanto si ocurre excepción
como si no ocurre. También se ejecuta si hay `return` dentro de `try`.
La excepción son terminaciones anómalas de la JVM (por ejemplo,
`System.exit` o fallo fatal), donde no se garantiza su ejecución.


## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

### Respuesta

Las excepciones controladas (*checked*) son las que el compilador obliga
a capturar o declarar con `throws` (por ejemplo, `IOException`). Las no
controladas (*unchecked*) son subclases de `RuntimeException` y no
requieren declaración obligatoria. `RuntimeException` representa errores
de programación o de uso indebido de una API, donde no siempre tiene
sentido forzar captura explícita.

Ejemplos típicos: controladas (`IOException`, `SQLException`);
no controladas (`NullPointerException`, `IllegalArgumentException`).
También pueden definirse propias: `DatosNoDisponiblesException extends Exception`
(controlada) o `ReglaNegocioInvalidaException extends RuntimeException`
(no controlada).

Situaciones donde suele preferirse excepción **controlada**:

- Apertura/lectura de ficheros donde el fallo es esperable.
- Acceso a red o servicios externos con cortes temporales.
- Operaciones de base de datos recuperables por el llamador.
- Carga de configuración que puede faltar en ciertos entornos.

Situaciones donde suele preferirse excepción **no controlada**:

- Parámetros inválidos pasados a un método público.
- Uso de un objeto en estado inconsistente.
- Errores de lógica interna detectados con validaciones.
- Supuestos de programación rotos (precondiciones incumplidas).


## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

### Respuesta

`throws` se coloca en la firma de un método para declarar que ese método
puede lanzar determinadas excepciones. Es parte del contrato de la API,
ya que informa al llamador de que debe gestionarlas o volver a
declararlas. Su uso es especialmente relevante con excepciones
controladas.

Se considera alternativa a capturar porque, en vez de manejar el error
localmente con `try-catch`, se delega la responsabilidad en capas
superiores. Esto es útil cuando el método actual no tiene contexto
suficiente para decidir una recuperación adecuada.


## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

### Respuesta

En este caso, el método declara `throws FileNotFoundException` y no
captura la excepción para que se propague. Aun así, se mantiene un
`finally` para garantizar el cierre del recurso si llegó a abrirse.

```java
import java.io.*;

public class Lector {
    public static String primeraLinea(String ruta) throws FileNotFoundException, IOException {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader(ruta));
            return br.readLine();
        } finally {
            if (br != null) {
                try {
                    br.close();
                } catch (IOException e) {
                    // Se ignora en este ejemplo didáctico
                }
            }
        }
    }
}
```


## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

### Respuesta

Sí, se puede declarar en `throws` una excepción no controlada, incluida
`RuntimeException` o sus subclases. No es obligatorio, pero a veces se
hace como documentación adicional del contrato para avisar de posibles
fallos por uso incorrecto.

El llamador no está obligado a poner `try-catch` en ese caso. Solo tiene
sentido capturarla cuando puede tomar una acción útil: traducir el error
a otro nivel, registrar contexto de negocio o devolver una respuesta
controlada (por ejemplo, en una API HTTP). Si no puede recuperarse, suele
ser mejor dejarla propagar.


## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

### Respuesta

Se recomienda usar excepciones controladas cuando el fallo es esperable y
el llamador puede recuperarse razonablemente (reintentar, pedir otra
ruta, usar otro recurso, etc.). `IOException` encaja bien en operaciones
de entrada/salida porque el entorno puede fallar por causas externas.

Las no controladas se recomiendan para errores de programación,
precondiciones incumplidas o estados inválidos que no deberían ocurrir en
un uso correcto de la API. En muchos lenguajes modernos no existe la
distinción obligatoria de Java; la opción más habitual es un modelo
equivalente a excepciones no controladas.


## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

### Respuesta

Sí, tiene sentido lanzar excepciones dentro de `catch`. Un patrón común
es capturar una excepción técnica de bajo nivel y lanzar otra más
expresiva para la capa superior. Esto permite desacoplar capas y mejorar
la semántica del error.

También se puede relanzar la misma excepción capturada con `throw e;`.
Esto se usa cuando se quiere hacer una acción previa (por ejemplo,
registrar contexto) pero sin ocultar el error original.

```java
try {
    // operación de bajo nivel
} catch (java.io.IOException e) {
    throw new RuntimeException("Error al procesar el documento", e);
}
```

```java
try {
    // operación
} catch (IllegalArgumentException e) {
    System.err.println("Parámetro inválido recibido");
    throw e; // relanzar la misma excepción
}
```


## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

### Respuesta

Que una excepción sea la causa de otra significa que un error original se
conserva como origen (`cause`) al lanzar una excepción nueva de nivel más
alto. Esta técnica, conocida como *exception chaining*, evita perder
diagnóstico y permite exponer un mensaje más cercano al dominio sin
ocultar el problema técnico subyacente.

En Java se hace pasando la excepción original al constructor de la nueva
excepción (`new MiExcepcion("...", causa)`). Cuando se imprime la traza,
normalmente se ve la cadena con textos como `Caused by`, por lo que el
origen queda visible.

```java
class ServicioPedidosException extends Exception {
    public ServicioPedidosException(String mensaje, Throwable causa) {
        super(mensaje, causa);
    }
}

try {
    // Bajo nivel: acceso a datos
    throw new java.sql.SQLException("Timeout en BD");
} catch (java.sql.SQLException e) {
    throw new ServicioPedidosException("No se pudo crear el pedido", e);
}
```

*Notas de clase*

2) **Excepción** ➜ Surge en situaciones  atípicas.

    - Cuando implementamos ➜ Nos permite indicar más claramente el error.

    - Cuando llamamos ➜ Me facilita reparar la lógica normal de la reacción o manejo de la situación

6) Sí, suelen ser objetos en Java.

    **Ventajas** ➜ Tienen métodos y estados. Puedo crear mis propias clases y excepciones

7) (a) Un mensaje (getMessage())

    (b) La traza de la pila "getStackTrace" "PrintStackTrace"

    (c) Opcionalmente, la "causa" es otra excepción que es la verdadera causa


8) Sí, se pueden tener más de uno 

```java
    try {
        ...
    } catch (AccesDeniedException) {
        ...
    } catch (IOException) {

    }
```

    - Sólo se ejecuta uno

    - Se va comprobando por oden hasta el primero que encaje
    
    - Se deben poner del más específico al más general, porque o sino, los catch, para excepciones más          específicas, no se ejecutarán

(9)

**finally** ➜ Se ejecuta siempre y cuando entre al try

(10)

Sí, pueden ir sin catch (el finally)

    - Se ejecuta, puesto que es finally
    - Si hubo excepción, como no tenemos catch, se propaga.

(11)

**Excepciones Controlodas** ➜ Obligan a usar try - catch / throws

    - IOException (Ejemplo: AccessDeniedException)

    - Otras

**No Controladas**
 - RunTimeException

 Ejemplos:

    - IlegalArgumentException
    - NullPointerException
    - ArrayIndexOutOfBoundsException

(12) y (13)

public String leerFichero(Path p) throws IOException
    try {
        ... = Files.readAllBytes
    } finally {
        ...
    }

(14)

    - Por poder, podemos pero el compilador no va a obligar al bloque try - catch
    - No es habitual
    - A veces se ponen para documentación

(15)

Se usan las controladas cuando el error es esperable y el llamador puede recuparse

IOException ➜ Operaciones de E/S

- Las no controladas se usan para errores de programación
- No en todos los lenguajes existen ambas opciones
- Más habituales ➜ No controladas

(16)

Sí, tienen sentido en algunos casos

    - Relanzar la misma excepción 
    - Envolver en otra excepción nueva (causa)


