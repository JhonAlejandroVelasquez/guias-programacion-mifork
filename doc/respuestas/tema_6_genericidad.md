<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Genericidad". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia y polimorfismo.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

### Respuesta

Una forma simple de emular una estructura genérica sin usar generics consiste en guardar los datos en un array de `Object`. De esta forma, se puede introducir cualquier tipo de objeto en la estructura, porque todos heredan de `Object`. En C se suele hacer algo similar con `void*`, aunque entonces es necesario llevar información adicional del tipo real para poder interpretar correctamente los datos.

El problema de este enfoque es que la estructura no sabe qué tipo contiene cada posición. Por tanto, el acceso correcto depende de que quien la use recuerde qué se ha guardado en cada sitio y haga el cast adecuado en el momento de extraerlo. La estructura funciona, pero la seguridad de tipos queda en manos del programador.

```java
public class PilaSinGenerics {
    private final Object[] datos;
    private int cima = 0;

    public PilaSinGenerics(int capacidad) {
        datos = new Object[capacidad];
    }

    public void apilar(Object valor) {
        datos[cima++] = valor;
    }

    public Object desapilar() {
        return datos[--cima];
    }
}
```

## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

### Respuesta

La programación genérica consiste en escribir clases y algoritmos que trabajan con tipos que no se fijan hasta el momento de usar la clase o el método. En lugar de repetir el mismo código para `int`, `String`, `double` y otros tipos, se escribe una sola versión parametrizada por tipos.

El ejemplo anterior se parece a una solución genérica, pero en realidad no lo es en sentido estricto. Al usar `Object` o `void*`, se está creando una estructura polimórfica muy básica, pero no se está obteniendo el chequeo de tipos en compilación que caracteriza a los generics modernos. Es, por tanto, una aproximación previa a la programación genérica, no su versión segura.

La diferencia clave es que una solución realmente genérica permite que el compilador compruebe que sólo se usan los tipos correctos. Con `Object`, esa comprobación no existe y los errores suelen aparecer más tarde, durante la ejecución.

## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

### Respuesta

El principal problema es que se pierde la comprobación de tipos en tiempo de compilación. Eso permite introducir valores de tipos distintos en la misma estructura sin que el compilador avise, aunque luego la lógica del programa espere un tipo concreto. El error no se detecta hasta que se intenta usar el dato y se realiza el cast incorrecto.

También aparece una fuerte dependencia de conversiones explícitas. Cada vez que se extrae un elemento, hay que recuperar su tipo real mediante un cast manual, lo que hace el código más frágil y más difícil de mantener. Si el cast es incorrecto, la excepción aparece en ejecución, normalmente lejos del lugar donde se introdujo el dato.

Además, se pierde claridad en la interfaz. Una colección de `Object` no comunica qué tipo de elementos debería contener, así que el uso correcto depende de convenciones externas o de documentación adicional. Esto reduce la legibilidad y aumenta la probabilidad de errores en programas grandes.


## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

### Respuesta

Los parámetros de tipo son nombres simbólicos que representan tipos todavía no concretados. Se escriben normalmente entre corchetes angulares, como `<T>` o `<T, U>`, y permiten que una clase o un método trabajen con distintos tipos sin perder el chequeo estático del compilador.

Su función es trasladar al sistema de tipos la información que antes se resolvía a mano con `Object` o `void*`. De esta forma, el compilador sabe qué operaciones están permitidas y puede verificar que los valores que se pasan y se reciben son coherentes con el tipo elegido al instanciar la clase o invocar el método.

Por ejemplo, una clase `Caja<T>` puede contener un valor de tipo `T`, y cuando se usa como `Caja<String>` el compilador trata ese `T` como `String`. Esto evita casts innecesarios y hace que el código sea más expresivo y seguro.


## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

### Respuesta

```java
import java.util.ArrayList;
import java.util.List;

public class EjemploGenericsJava {
    public static void main(String[] args) {
        List<String> nombres = new ArrayList<>();
        nombres.add("Ana");
        nombres.add("Luis");
        nombres.add("Marta");

        for (String nombre : nombres) {
            System.out.println(nombre.toUpperCase());
        }
    }
}
```

```cpp
#include <iostream>
#include <string>
#include <vector>

int main() {
    std::vector<std::string> nombres;
    nombres.push_back("Ana");
    nombres.push_back("Luis");
    nombres.push_back("Marta");

    for (const std::string& nombre : nombres) {
        std::cout << nombre << std::endl;
    }

    return 0;
}
```

En Java, `List<String>` garantiza en compilación que sólo se añadan cadenas y que el recorrido entregue elementos de tipo `String` sin casts. Eso evita errores frecuentes en colecciones sin tipo, donde cualquier objeto podía entrar en la lista.

En C++, `std::vector<std::string>` cumple la misma idea usando templates. Cada elemento del vector es ya un `std::string`, así que el recorrido resulta seguro y directo. En ambos lenguajes el código queda más legible y el tipo concreto se conoce en el uso real de la estructura.


## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

### Respuesta

Cuando se instancia una clase genérica, el compilador utiliza el tipo concreto elegido para comprobar que el uso es correcto. A partir de ahí, el comportamiento interno depende del lenguaje. En C++, normalmente se genera código específico para cada combinación de tipos usada, mientras que en Java se conserva una sola versión de la clase con información de tipos borrada en gran parte.

En Java, este mecanismo se llama *type erasure*. Significa que los parámetros de tipo existen durante la compilación para comprobar el código, pero después se eliminan en gran medida del bytecode. Por eso, en tiempo de ejecución, una `Lista<String>` y una `Lista<Integer>` se comportan como la misma clase genérica ya borrada, con conversiones insertadas automáticamente por el compilador.

En C++ se habla de instanciación de plantillas. El compilador genera una versión concreta del código para cada tipo que se usa realmente, por ejemplo una `vector<string>` distinta de una `vector<int>`. Eso da mucho control al compilador y suele permitir optimizaciones, aunque también puede aumentar el tamaño del binario.


## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

### Respuesta

```java
public class Par<T, U> {
    private final T primero;
    private final U segundo;

    public Par(T primero, U segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public T getPrimero() {
        return primero;
    }

    public U getSegundo() {
        return segundo;
    }
}

class Estadisticas {
    public static Par<Double, Double> mediaYDesviacion(double[] valores) {
        double suma = 0.0;
        for (double valor : valores) {
            suma += valor;
        }

        double media = suma / valores.length;
        double sumaCuadrados = 0.0;
        for (double valor : valores) {
            double diferencia = valor - media;
            sumaCuadrados += diferencia * diferencia;
        }

        double desviacion = Math.sqrt(sumaCuadrados / valores.length);
        return new Par<>(media, desviacion);
    }
}
```

La clase `Par` permite agrupar dos valores relacionados sin crear una clase específica para cada caso. En este ejemplo, el método `mediaYDesviacion` devuelve dos resultados a la vez y el tipo del par deja claro que ambos son `Double`.

Esto mejora la legibilidad y evita ambigüedades en la interfaz del método. En lugar de usar un array o una estructura sin nombre, se devuelve un objeto con dos campos bien identificados, lo que facilita su uso y su mantenimiento.


## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

### Respuesta

```java
import java.util.Random;

public class Utilidades {
    private static final Random RAND = new Random();

    public static Object seleccionaUnoObject(Object a, Object b) {
        return RAND.nextBoolean() ? a : b;
    }

    public static <T> T seleccionaUno(T a, T b) {
        return RAND.nextBoolean() ? a : b;
    }
}
```

Con la versión basada en `Object`, quien llama al método recibe un `Object` y suele tener que hacer cast después para recuperar el tipo real. Eso obliga a conocer de antemano qué tipo se ha devuelto, y ese conocimiento queda fuera del sistema de tipos del compilador.

Con la versión genérica, el compilador deduce el tipo `T` a partir de los argumentos y devuelve directamente ese mismo tipo. Además, ambos parámetros deben ser compatibles con el mismo `T`, así que no se puede mezclar libremente un `String` y un `Integer` en la misma llamada. El chequeo se realiza antes de ejecutar el programa, que es justo la ventaja de los generics.


## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

### Respuesta

Sí, los parámetros de tipo pueden restringirse con límites superiores. En Java se escribe `T extends Number`, lo que significa que `T` puede ser cualquier subtipo de `Number`, como `Integer`, `Double` o `Long`. De esa forma, el código puede usar operaciones comunes a todos esos números, como `doubleValue()`.

Una solución simple consiste en guardar directamente coordenadas de tipo `Number`. Otra más segura consiste en parametrizar la clase con `T extends Number`, de manera que el compilador conozca con exactitud el tipo numérico que se está usando en cada instancia. La primera solución es más flexible; la segunda refuerza mejor el chequeo estático.

```java
public class PuntoNumero {
    private final Number x;
    private final Number y;

    public PuntoNumero(Number x, Number y) {
        this.x = x;
        this.y = y;
    }

    public Number getX() {
        return x;
    }

    public Number getY() {
        return y;
    }

    public double calcularDistanciaA(PuntoNumero otro) {
        double dx = x.doubleValue() - otro.x.doubleValue();
        double dy = y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}

public class Punto<T extends Number> {
    private final T x;
    private final T y;

    public Punto(T x, T y) {
        this.x = x;
        this.y = y;
    }

    public T getX() {
        return x;
    }

    public T getY() {
        return y;
    }

    public double calcularDistanciaA(Punto<T> otro) {
        double dx = x.doubleValue() - otro.x.doubleValue();
        double dy = y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

Tras la compilación, debido al *type erasure*, el tipo final de la versión genérica queda borrado y el parámetro `T` se reemplaza por su límite, que en este caso es `Number`. Por eso, en el bytecode la clase trabaja realmente con `Number`, aunque en el código fuente se siga viendo el tipo concreto usado en cada instancia.


## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

### Respuesta

La solución basada en `Number` sí permite mezclar tipos distintos en las dos coordenadas, por ejemplo un `Integer` y un `Double`. En cambio, la versión genérica con un único parámetro `T` obliga a que ambas coordenadas tengan exactamente el mismo tipo concreto. Si se quiere permitir mezclas distintas y seguir usando generics, habría que declarar dos parámetros independientes, por ejemplo `<X extends Number, Y extends Number>`.

En la versión sin generics, `getX()` devuelve `Number`, así que al recuperar el valor sólo se conoce su supertipo común. Eso obliga a convertir o inspeccionar el tipo real si se quiere usar una operación específica de `Integer`, `Double` o cualquier otra subclase.

En la versión genérica, `getX()` devuelve `T`, y por tanto el compilador sabe exactamente qué tipo concreto se ha elegido al instanciar la clase. Si se crea `Punto<Integer>`, entonces `getX()` devuelve `Integer`; si se crea `Punto<Double>`, devuelve `Double`. Esa es la mejora principal del refuerzo de tipos con generics.


## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.
```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

### Respuesta

Una forma habitual de resolver este problema es usar un parámetro de tipo que represente al propio tipo concreto de punto. Así, la interfaz queda parametrizada con el subtipo exacto que la implementa y el método de distancia sólo acepta objetos de ese mismo subtipo. De este modo, el compilador impide comparar un `Punto2D` con un `Punto3D` por accidente.

```java
public interface Punto<T extends Punto<T>> {
    double distanciaA(T p);
}

public class Punto2D implements Punto<Punto2D> {
    private final double x;
    private final double y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public double distanciaA(Punto2D p) {
        double dx = x - p.x;
        double dy = y - p.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

public class Punto3D implements Punto<Punto3D> {
    private final double x;
    private final double y;
    private final double z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public double distanciaA(Punto3D p) {
        double dx = x - p.x;
        double dy = y - p.y;
        double dz = z - p.z;
        return Math.sqrt(dx * dx + dy * dy + dz * dz);
    }
}
```

Con este diseño desaparecen el `instanceof` y el downcasting, porque el tipo correcto ya viene garantizado por la firma del método. Si se intenta llamar a `distanciaA` con un tipo distinto, el error se detecta en compilación, no en ejecución.

Además, la interfaz refleja mejor la intención del código: cada implementación sólo sabe calcular distancias dentro de su propia dimensión. Eso hace más claro el contrato y reduce de forma importante la posibilidad de errores lógicos.


## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

### Respuesta

No, `List<String>` no es subtipo de `List<Object>`. En Java, los tipos genéricos son invariantes por defecto, así que aunque `String` sí sea subtipo de `Object`, eso no se traslada automáticamente a `List<String>`. Si se permitiera, se podría añadir un `Integer` a una lista pensada para cadenas, rompiendo la seguridad de tipos.

Con los arrays la situación es distinta: `String[]` sí es subtipo de `Object[]`. Los arrays son covariantes en Java, lo que parece cómodo pero introduce un riesgo en tiempo de ejecución. Por ejemplo, se puede asignar un `String[]` a una variable `Object[]`, pero si luego se intenta guardar un `Object` cualquiera, el sistema lanzará una excepción `ArrayStoreException`.

La covarianza significa que si `A` es subtipo de `B`, entonces `Contenedor<A>` también se considera subtipo de `Contenedor<B>`. La contravarianza es lo contrario: `Contenedor<B>` puede usarse donde se espera `Contenedor<A>` en ciertos contextos, normalmente de escritura. La invariancia, que es el caso por defecto en Java para genéricos, significa que aunque `A` y `B` estén relacionados, `Contenedor<A>` y `Contenedor<B>` no lo están entre sí.


## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

### Respuesta

Un wildcard es un comodín que representa un tipo desconocido dentro de un genérico. En Java se escribe con `?` y permite expresar relaciones más flexibles entre tipos sin renunciar al control del compilador. Su utilidad principal está en permitir cierto grado de covarianza o contravarianza de forma segura.

`List<? extends T>` se usa cuando se quiere leer de una lista que produce elementos de tipo `T` o de subtipos de `T`. `List<? super T>` se usa cuando se quiere escribir en una lista que acepta elementos de tipo `T` o de sus supertipos. Como regla práctica, `extends` se asocia a lectura y `super` a escritura.

```java
import java.util.List;

public class Wildcards {
    public static double suma(List<? extends Number> numeros) {
        double total = 0.0;
        for (Number n : numeros) {
            total += n.doubleValue();
        }
        return total;
    }

    public static void anadeEnteros(List<? super Integer> destino, int... valores) {
        for (int valor : valores) {
            destino.add(valor);
        }
    }
}
```

En el método de suma, la lista puede ser de `Integer`, `Double` o cualquier otro subtipo de `Number`, porque sólo se necesita leer. En el método que añade enteros, la lista puede ser de `Integer`, `Number` u `Object`, porque lo que interesa es poder insertar valores enteros de forma segura.

Esta distinción evita muchos problemas de tipos y hace que los métodos genéricos sean más reutilizables. Los wildcards permiten describir con bastante precisión cómo se va a usar una colección, sin tener que fijar un único tipo exacto cuando no hace falta.


## Apuntes Clase Relevantes

12) ¿String [] es un Object []?

    String [] miarrayS = {"A", "B"};
    Object [] miarrayO = miarrayS;


    Stack           Heap
    miarrays
    miarrayO        String []

    miarrayO[O] = new Empleado();


    List<String>  NO ES TIPO COMPATIBLE con List<Object>

    COMPILADOR       => List<String> milistas = ...
    LOS GENÉRICOS    => LIST<Object> milistaO = miListaS;
    SON INVARIANTES  => milistaO.add(new empleado())

13) List<String>mi lista    <?>
    List <?> miLista        <?> super Number>
    List <? Super Number>   <?> extends Number>
    List<? extends Number>


                                        Object

                                        Number

                                Double              Integer

Punto <T extends Number> 

Number media (List <? extends Number > Number)

rellenarConElementos(List <? super.Number>Lista) {
    Number n = ...;
    list.add(n);
                    lista.get()? -> Object
    numeros.get(o) -> Numbers
    numeros.add (num number) // Da error de compilación
}

