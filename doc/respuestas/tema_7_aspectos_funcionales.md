<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Aspectos funcionales". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia, polimorfismo y genericidad.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero.

### Respuesta

Un puntero a función en C es una variable que almacena la dirección de una función concreta. Esto permite pasar funciones como argumentos, guardarlas en estructuras de datos o decidir en tiempo de ejecución qué comportamiento ejecutar. La firma del puntero debe coincidir exactamente con la firma de la función objetivo (tipo de retorno y parámetros).

En este caso, se define una función que recibe una cadena modificable y la transforma a mayúsculas in situ. Después, en una variable local llamada `aMayusculas`, se guarda la referencia a esa función y se invoca usando el puntero. Se mantiene así la idea de "función como dato", aunque en C se trabaja con direcciones de memoria.

```c
#include <stdio.h>
#include <ctype.h>

char* convertirAMayusculas(char* texto) {
	for (int i = 0; texto[i] != '\0'; i++) {
		texto[i] = (char)toupper((unsigned char)texto[i]);
	}
	return texto;
}

int main(void) {
	char cadena[] = "Hola Mundo";

	char* (*aMayusculas)(char*) = convertirAMayusculas;

	printf("Resultado: %s\n", aMayusculas(cadena));
	return 0;
}
```


## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda.

### Respuesta

Una función lambda es una función anónima que puede declararse en el mismo punto donde se necesita, sin crear necesariamente una función con nombre separado. Su utilidad principal es reducir código ceremonial y facilitar que el comportamiento se pase como parámetro o se almacene en variables de forma más directa.

En JavaScript, las funciones son valores de primera clase desde el principio del lenguaje, por lo que una lambda se asigna de forma natural a una variable. En Java, una lambda necesita un tipo objetivo, normalmente una interfaz funcional; por eso se usa `Function<String, String>` para representar "una función que transforma un `String` en otro `String`".

```javascript
const aMayusculas = (texto) => texto.toUpperCase();

console.log(aMayusculas("Hola Mundo"));
```

```java
import java.util.function.Function;

public class EjemploLambda {
	public static void main(String[] args) {
		Function<String, String> aMayusculas = texto -> texto.toUpperCase();

		System.out.println(aMayusculas.apply("Hola Mundo"));
	}
}
```


## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?

### Respuesta

El paradigma funcional es un estilo de programación que centra el diseño en el uso de funciones puras, composición de transformaciones y minimización de estados mutables. En lugar de pensar primero en objetos que cambian internamente, se piensa en datos que se transforman mediante funciones que, idealmente, producen siempre la misma salida para la misma entrada.

Cuando se afirma que Java 8 es multiparadigma, se indica que no obliga a un único enfoque: se puede programar con clases y objetos, pero también con mecanismos funcionales como lambdas, referencias a método y operaciones sobre colecciones con `stream`. Decir que las funciones son ciudadanos de primera clase significa que se pueden asignar a variables, pasar como parámetros, devolver como resultado y almacenar en estructuras, del mismo modo que cualquier otro valor.


## 4. Explica la sintaxis básica de una función lambda en Java.

### Respuesta

La forma general de una lambda en Java es `(parámetros) -> expresión` o `(parámetros) -> { bloque }`. A la izquierda de `->` se colocan los parámetros de entrada y a la derecha el cuerpo que se ejecuta. Si el cuerpo es una sola expresión, su valor se devuelve implícitamente; si hay bloque con llaves, debe usarse `return` cuando corresponda.

Además, el tipo no suele escribirse explícitamente en la lambda porque Java lo infiere a partir del contexto (interfaz funcional esperada). Por ejemplo, si se asigna a `Function<String, Integer>`, Java deduce que el parámetro es `String` y que el resultado debe ser `Integer`. Esto permite código compacto sin perder comprobación estática de tipos.

```java
import java.util.function.Function;

Function<String, Integer> longitud1 = s -> s.length();
Function<String, Integer> longitud2 = (String s) -> {
	return s.length();
};
```


## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro.

### Respuesta

Pasar una función como parámetro permite separar el "qué" del "cómo". El método `transformar` puede encargarse del flujo principal y delegar el detalle de transformación en la función recibida. Así, el método queda reutilizable para muchas operaciones distintas (mayúsculas, recorte, reemplazo, etc.) sin duplicar estructura.

En JavaScript se hace de forma directa porque las funciones ya son valores nativos del lenguaje. En Java se expresa igual idea con `Function<String, String>`, invocando con `apply`. En ambos casos, `transformar` ejecuta la función transformadora internamente sobre la cadena recibida.

```javascript
function transformar(texto, transformadora) {
	return transformadora(texto);
}

const aMayusculas = (s) => s.toUpperCase();
console.log(transformar("hola", aMayusculas));
```

```java
import java.util.function.Function;

public class Transformaciones {
	public static String transformar(String texto, Function<String, String> transformadora) {
		return transformadora.apply(texto);
	}

	public static void main(String[] args) {
		Function<String, String> aMayusculas = s -> s.toUpperCase();
		System.out.println(transformar("hola", aMayusculas));
	}
}
```


## 6. Ahora, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro.

### Respuesta

Una ventaja importante de las lambdas es que pueden definirse en línea justo donde se usan. Esto evita declarar variables auxiliares cuando la transformación sólo se necesita una vez. El resultado suele ser más legible en operaciones locales y reduce ruido en el código.

En este caso, la función de inversión se pasa directamente a `transformar`. Se mantiene la misma firma del método y sólo cambia el comportamiento aportado por la lambda en la llamada concreta.

```javascript
function transformar(texto, transformadora) {
	return transformadora(texto);
}

console.log(transformar("abcdef", s => s.split("").reverse().join("")));
```

```java
import java.util.function.Function;

public class InversionDirecta {
	public static String transformar(String texto, Function<String, String> transformadora) {
		return transformadora.apply(texto);
	}

	public static void main(String[] args) {
		System.out.println(transformar("abcdef", s -> new StringBuilder(s).reverse().toString()));
	}
}
```


## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda.

### Respuesta

Un cierre (closure) es una función que conserva acceso al entorno léxico en el que fue creada. En términos prácticos, una lambda puede usar variables locales externas a su propio cuerpo, siempre que el lenguaje lo permita. En Java, esas variables deben ser finales o efectivamente finales (no modificadas después de inicializarse).

Esto permite definir funciones más expresivas y configurables sin crear clases adicionales. La lambda captura la variable externa y la utiliza cuando se ejecuta, incluso si se invoca en otro punto del flujo. Así se modela comportamiento dependiente de contexto de forma compacta.

```java
import java.util.function.Function;

public class EjemploClosure {
	public static String transformar(String texto, Function<String, String> transformadora) {
		return transformadora.apply(texto);
	}

	public static void main(String[] args) {
		String sufijo = " - procesado"; // variable capturada por la lambda

		Function<String, String> concatenarSufijo = s -> s + sufijo;

		System.out.println(transformar("entrada", concatenarSufijo));
	}
}
```


## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

### Respuesta

Un puntero a función en C apunta únicamente a código ejecutable; no incorpora de forma nativa el entorno donde se definió ese comportamiento. Por ello, si se necesita contexto adicional, suele pasarse explícitamente mediante parámetros extra o estructuras auxiliares. El mecanismo es potente, pero de bajo nivel.

Una lambda moderna, como en Java o JavaScript, puede actuar como cierre y capturar variables del contexto de definición. Además, en Java se integra con el sistema de tipos mediante interfaces funcionales, lo que añade seguridad estática. En resumen, ambos conceptos representan "comportamiento invocable", pero la lambda aporta mejor integración semántica y de tipado para programación de alto nivel.


## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

### Respuesta

Devolver funciones permite construir "fábricas de comportamiento". En este ejemplo, `crearDescuento` recibe un porcentaje y devuelve otra función que sabrá cómo aplicarlo a cualquier precio posterior. Se separa así la configuración inicial (porcentaje) de la ejecución repetida (aplicar descuento sobre importes).

La closure aparece porque la función devuelta captura el valor de `porcentaje` desde el contexto de `crearDescuento`. Aunque `crearDescuento` ya haya terminado, la lambda conserva ese valor para cálculos futuros. Por eso se pueden generar dos funciones distintas (por ejemplo 10% y 25%) y ambas recuerdan su propia configuración.

```java
import java.util.function.Function;

public class Descuentos {
	public static Function<Double, Double> crearDescuento(double porcentaje) {
		return precio -> precio * (1.0 - porcentaje / 100.0);
	}

	public static void main(String[] args) {
		Function<Double, Double> descuento10 = crearDescuento(10.0);
		Function<Double, Double> descuento25 = crearDescuento(25.0);

		double precio = 200.0;
		System.out.println("Precio original: " + precio);
		System.out.println("Con 10%: " + descuento10.apply(precio));
		System.out.println("Con 25%: " + descuento25.apply(precio));
	}
}
```


## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

### Respuesta

Una interfaz funcional en Java es una interfaz que representa una única operación abstracta y que, por tanto, puede implementarse mediante una expresión lambda o una referencia a método. Su objetivo es dar un tipo estático al comportamiento, para que el compilador pueda validar parámetros y retorno de forma segura.

El requisito principal es tener exactamente un método abstracto. Puede incluir métodos `default`, `static` e incluso métodos heredados de `Object` sin romper la condición funcional. Suele anotarse con `@FunctionalInterface` para que el compilador valide explícitamente ese contrato y avise si se añade accidentalmente un segundo método abstracto.


## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

### Respuesta

Definir una interfaz funcional propia permite expresar mejor el dominio del problema. Aunque `Function<String, String>` ya existe, un nombre como `Transformador` puede hacer el código más legible para quien mantiene la aplicación, porque comunica intención semántica en lugar de solo tipos genéricos.

La interfaz se declara con un único método abstracto que reciba un `String` y devuelva otro `String`. Después, cualquier lambda compatible podrá asignarse a ese tipo y usarse en métodos que reciban un `Transformador`.

```java
@FunctionalInterface
public interface Transformador {
	String transformar(String texto);
}

class UsoTransformador {
	public static String aplicar(String entrada, Transformador t) {
		return t.transformar(entrada);
	}

	public static void main(String[] args) {
		Transformador aMayusculas = s -> s.toUpperCase();
		System.out.println(aplicar("hola", aMayusculas));
	}
}
```


## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

### Respuesta

Generalizar la interfaz permite reutilizar la misma abstracción para múltiples pares de tipos de entrada y salida. Con generics, el contrato deja de estar limitado a `String -> String` y pasa a representar cualquier transformación `T -> R` con seguridad de tipos en compilación.

El ejemplo `Double -> Integer` encaja bien porque muestra una conversión real de dominio numérico. La lambda puede usar `Math.round` y devolver un entero de forma explícita, quedando claro tanto el tipo de entrada como el de salida en la propia firma del transformador.

```java
@FunctionalInterface
public interface Transformador<T, R> {
	R transformar(T valor);
}

class EjemploGenerico {
	public static void main(String[] args) {
		Transformador<Double, Integer> redondear = d -> (int) Math.round(d);

		Integer resultado = redondear.transformar(12.7);
		System.out.println(resultado); // 13
	}
}
```


## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

### Respuesta

Java incluye en `java.util.function` un conjunto amplio de interfaces funcionales reutilizables para los casos más frecuentes. La idea es evitar crear interfaces ad hoc cuando ya existe una estándar equivalente, facilitando interoperabilidad con APIs como `Stream`, `Optional` o colecciones.

Entre las más usadas están `Function<T,R>`, `Predicate<T>`, `Consumer<T>`, `Supplier<T>`, `UnaryOperator<T>` y `BinaryOperator<T>`. También existen variantes especializadas para primitivos, como `IntFunction<R>`, `ToIntFunction<T>`, `IntPredicate`, `DoubleConsumer`, `BiFunction<T,U,R>`, `BiPredicate<T,U>` y `BiConsumer<T,U>`, que reducen boxing y mejoran rendimiento en escenarios intensivos.

```java
import java.util.function.*;

Function<String, Integer> f = s -> s.length();
Predicate<Integer> p = n -> n > 0;
Consumer<String> c = s -> System.out.println(s);
Supplier<Double> s = () -> Math.random();
UnaryOperator<String> u = x -> x.trim();
BinaryOperator<Integer> b = (x, y) -> x + y;
```


## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

### Respuesta

`forEach` permite recorrer una colección delegando la acción sobre cada elemento en una función `Consumer`. Es una alternativa funcional al bucle clásico cuando se quiere destacar la operación a aplicar más que la mecánica de iteración. El código suele quedar más declarativo y compacto.

Para mostrar sólo enteros positivos, basta con incluir una condición dentro de la lambda. Así se mantiene un único recorrido y se expresa de forma local qué criterio se evalúa en cada elemento.

```java
import java.util.Arrays;
import java.util.List;

public class EjemploForEach {
	public static void main(String[] args) {
		List<Integer> numeros = Arrays.asList(-3, 0, 7, 15, -2, 4);

		numeros.forEach(n -> {
			if (n > 0) {
				System.out.println("Positivo: " + n);
			}
		});
	}
}
```

## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

### Respuesta

La firma usa `Consumer<? super T>` porque el consumidor "consume" elementos de tipo `T`, y en genericidad conviene aceptar también consumidores definidos para supertipos de `T`. Esto da más flexibilidad sin perder seguridad: si una lista contiene `String`, un `Consumer<Object>` puede consumir esos `String` sin problema.

PECS significa "Producer Extends, Consumer Super". Si un parámetro produce valores para leer, suele usarse `? extends`; si consume valores que se le escriben, suele usarse `? super`. Aplicado a `transformar`, una firma robusta sería `Function<? super T, ? extends R>`: la función puede aceptar `T` o un supertipo como entrada, y puede devolver `R` o un subtipo como salida.

```java
import java.util.function.Function;

public class Util {
	public static <T, R> R transformar(T valor, Function<? super T, ? extends R> f) {
		return f.apply(valor);
	}
}
```

## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

### Respuesta

Una referencia a método permite reutilizar un método existente como valor funcional, evitando escribir una lambda equivalente cuando no aporta información adicional. En esencia, se toma un método ya definido y se "apunta" a él para invocarlo más tarde desde una variable.

En JavaScript, al extraer un método de un objeto hay que conservar el contexto (`this`) con `bind`. En Java, la referencia a método de instancia concreta se expresa con `objeto::metodo` y puede asignarse a interfaces funcionales sin manejo manual de contexto.

```javascript
class Persona {
	constructor(nombre) {
		this.nombre = nombre;
	}

	saludar() {
		return `Hola, soy ${this.nombre}`;
	}
}

const p = new Persona("Ana");
const refSaludar = p.saludar.bind(p);
console.log(refSaludar());
```

```java
import java.util.function.Supplier;

class Persona {
	private final String nombre;

	public Persona(String nombre) {
		this.nombre = nombre;
	}

	public String saludar() {
		return "Hola, soy " + nombre;
	}
}

public class ReferenciaMetodo {
	public static void main(String[] args) {
		Persona p = new Persona("Ana");
		Supplier<String> refSaludar = p::saludar;

		System.out.println(refSaludar.get());
	}
}
```


## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

### Respuesta

Java contempla cuatro formas principales: referencia a método estático (`Clase::metodoEstatico`), referencia a constructor (`Clase::new`), referencia a método de instancia de objeto concreto (`objeto::metodo`) y referencia a método de instancia sobre cualquier objeto de un tipo (`Clase::metodoInstancia`). Todas se usan cuando la firma encaja con una interfaz funcional esperada.

Estas variantes permiten expresar intenciones muy comunes sin crear lambdas repetitivas. El resultado suele ser código más limpio y alineado con APIs funcionales estándar como `map`, `forEach`, `Comparator` y constructores inyectados.

```java
import java.util.Arrays;
import java.util.List;
import java.util.function.BiFunction;
import java.util.function.Function;
import java.util.function.Supplier;

class Utiles {
	public static int parsearEntero(String s) {
		return Integer.parseInt(s);
	}
}

class Persona {
	private final String nombre;

	public Persona() {
		this("Sin nombre");
	}

	public Persona(String nombre) {
		this.nombre = nombre;
	}

	public String saludar() {
		return "Hola, soy " + nombre;
	}

	public String unir(String otro) {
		return nombre + " y " + otro;
	}
}

public class TiposReferencia {
	public static void main(String[] args) {
		Function<String, Integer> refEstatico = Utiles::parsearEntero;
		Supplier<Persona> refConstructor = Persona::new;

		Persona ana = new Persona("Ana");
		Supplier<String> refInstanciaConcreta = ana::saludar;

		BiFunction<Persona, String, String> refInstanciaCualquier = Persona::unir;

		List<String> nombres = Arrays.asList("luis", "ana", "marta");
		nombres.stream().map(String::toUpperCase).forEach(System.out::println);

		System.out.println(refEstatico.apply("123"));
		System.out.println(refConstructor.get().saludar());
		System.out.println(refInstanciaConcreta.get());
		System.out.println(refInstanciaCualquier.apply(ana, "Carlos"));
	}
}
```


## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

### Respuesta

Ordenar con lambda permite expresar criterios compuestos de forma cercana al lenguaje natural: primero por edad y, en caso de empate, por nombre. Con `Collections.sort`, el comparador recibe dos elementos y debe devolver negativo, cero o positivo según el orden relativo. Es un caso clásico donde el enfoque funcional mejora legibilidad frente a condicionales dispersas.

Se pueden escribir dos estilos correctos. En la versión manual se controla explícitamente la lógica de comparación. En la versión con utilidades de `Comparator`, se reutilizan constructores de comparadores encadenables (`comparingInt` y `thenComparing`), lo que reduce errores y hace el criterio más declarativo.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

class Persona {
	private final String nombre;
	private final int edad;

	public Persona(String nombre, int edad) {
		this.nombre = nombre;
		this.edad = edad;
	}

	public String getNombre() {
		return nombre;
	}

	public int getEdad() {
		return edad;
	}

	@Override
	public String toString() {
		return nombre + " (" + edad + ")";
	}
}

public class OrdenacionPersonas {
	public static void main(String[] args) {
		List<Persona> personas1 = new ArrayList<>();
		personas1.add(new Persona("Luis", 20));
		personas1.add(new Persona("Ana", 18));
		personas1.add(new Persona("Marta", 20));
		personas1.add(new Persona("Carlos", 18));

		// Version 1: comparacion manual
		Collections.sort(personas1, (p1, p2) -> {
			if (p1.getEdad() != p2.getEdad()) {
				return Integer.compare(p1.getEdad(), p2.getEdad());
			}
			return p1.getNombre().compareTo(p2.getNombre());
		});

		System.out.println("Manual: " + personas1);

		List<Persona> personas2 = new ArrayList<>(personas1);

		// Version 2: utilidades de Comparator
		Collections.sort(
			personas2,
			Comparator.comparingInt(Persona::getEdad)
					  .thenComparing(Persona::getNombre)
		);

		System.out.println("Con Comparator: " + personas2);
	}
}
```
