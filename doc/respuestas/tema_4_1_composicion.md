<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Composición". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación y Excepciones.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.1. Composición


## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

### Respuesta

En C, la composición se expresa incluyendo una `struct` dentro de otra.
Así, una línea puede modelarse como una estructura que contiene dos
puntos, y cada punto contiene sus dos coordenadas. La idea sigue siendo
la misma que en lenguaje natural: una `Linea` tiene dos `Punto`.

Como C no incorpora métodos dentro de las estructuras, las operaciones se
programan como funciones externas. Una función puede calcular la
distancia entre dos puntos y otra reutilizar esa lógica para obtener la
longitud de una línea.

```c
#include <stdio.h>
#include <math.h>

struct Punto {
	double x;
	double y;
};

struct Linea {
	struct Punto inicio;
	struct Punto fin;
};

double distanciaPuntos(struct Punto a, struct Punto b) {
	double dx = b.x - a.x;
	double dy = b.y - a.y;
	return sqrt(dx * dx + dy * dy);
}

double longitudLinea(struct Linea l) {
	return distanciaPuntos(l.inicio, l.fin);
}

int main() {
	struct Punto p1 = {0.0, 0.0};
	struct Punto p2 = {3.0, 4.0};
	struct Linea linea = {p1, p2};

	printf("Longitud: %.2f\n", longitudLinea(linea));
	return 0;
}
```


## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.  

### Respuesta

En Java, la misma idea se expresa con clases. La clase `Linea` queda
compuesta por dos objetos `Punto`, pero además cada clase encapsula sus
datos y sus operaciones. Eso mejora el diseño respecto a C, porque se
puede impedir que el exterior modifique el estado interno de los
objetos.

Para garantizar la inmutabilidad, los atributos se declaran `private`
`final` y no se ofrecen métodos modificadores. Una vez creado un `Punto`
o una `Linea`, su estado ya no cambia. Así se evita que una línea pase a
unir otros puntos distintos por accidente.

```java
public final class Punto {
	private final double x;
	private final double y;

	public Punto(double x, double y) {
		this.x = x;
		this.y = y;
	}

	public double getX() {
		return x;
	}

	public double getY() {
		return y;
	}

	public double distanciaA(Punto otro) {
		double dx = otro.x - this.x;
		double dy = otro.y - this.y;
		return Math.sqrt(dx * dx + dy * dy);
	}
}

public final class Linea {
	private final Punto inicio;
	private final Punto fin;

	public Linea(Punto inicio, Punto fin) {
		if (inicio == null || fin == null) {
			throw new IllegalArgumentException("Los puntos no pueden ser nulos");
		}
		this.inicio = inicio;
		this.fin = fin;
	}

	public Punto getInicio() {
		return inicio;
	}

	public Punto getFin() {
		return fin;
	}

	public double longitud() {
		return inicio.distanciaA(fin);
	}
}
```


## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

### Respuesta

La multiplicidad indica cuántos objetos de una clase pueden estar
relacionados con un objeto de otra clase. Es una forma de expresar si la
relación es de uno a uno, de uno a muchos, opcional, obligatoria, etc.
No describe el algoritmo, sino la estructura permitida por el modelo.

En el ejemplo, de `Linea` a `Punto` la multiplicidad es exactamente `2`,
porque toda línea está formada por dos puntos: inicio y fin. En sentido
contrario, de `Punto` a `Linea`, la multiplicidad es `0..*`, porque un
punto puede no pertenecer a ninguna línea o puede participar en muchas
líneas distintas. Aunque el código no guarde esa relación inversa de
forma explícita, conceptualmente esa es la multiplicidad.


## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.

### Respuesta

En una composición fuerte, el objeto contenedor es el responsable más
claro de las partes y su ciclo de vida queda ligado al suyo. Si el
contenedor deja de existir, también deja de tener sentido que existan sus
componentes como objetos independientes dentro de ese modelo.

En una composición débil, en cambio, el contenedor solo mantiene una
referencia a objetos que pueden existir antes, durante y después de esa
relación. Por eso suele hablarse de **agregación** o **asociación** para
la relación débil, mientras que el término **composición** propiamente
dicho suele reservarse para la relación fuerte.


## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

### Respuesta

En esos casos se habla normalmente de **dependencia**, no de composición.
La idea es que una clase necesita a otra para realizar una operación,
pero no la conserva como parte estable de su estado interno. Es una
relación de uso, no de pertenencia estructural.

Por ejemplo, si un método recibe un `Punto` como parámetro para hacer un
cálculo puntual, existe dependencia de `Punto`. En cambio, si una clase
guarda un `Punto` en un atributo porque forma parte permanente de su
estructura, entonces sí se está ante una composición o agregación.


## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

### Respuesta

Una forma sencilla de distinguir ambos casos es observar quién crea los
puntos. Si `Linea` crea internamente sus propios puntos a partir de
coordenadas, se está modelando una composición fuerte: esos puntos nacen
con la línea y solo tienen sentido dentro de ella. Si, por el contrario,
la línea recibe puntos ya existentes, se trata de una composición débil
o agregación.

En ambos ejemplos se reutiliza una clase `Punto` inmutable. La diferencia
no está en `Punto`, sino en cómo `Linea` adquiere las referencias a esos
objetos.

```java
public final class Punto {
	private final double x;
	private final double y;

	public Punto(double x, double y) {
		this.x = x;
		this.y = y;
	}

	public double distanciaA(Punto otro) {
		double dx = otro.x - this.x;
		double dy = otro.y - this.y;
		return Math.sqrt(dx * dx + dy * dy);
	}
}

// Composición fuerte: la línea crea sus propios puntos.
public final class LineaFuerte {
	private final Punto inicio;
	private final Punto fin;

	public LineaFuerte(double x1, double y1, double x2, double y2) {
		this.inicio = new Punto(x1, y1);
		this.fin = new Punto(x2, y2);
	}

	public double longitud() {
		return inicio.distanciaA(fin);
	}
}

// Composición débil: la línea reutiliza puntos ya existentes.
public final class LineaDebil {
	private final Punto inicio;
	private final Punto fin;

	public LineaDebil(Punto inicio, Punto fin) {
		if (inicio == null || fin == null) {
			throw new IllegalArgumentException("Los puntos no pueden ser nulos");
		}
		this.inicio = inicio;
		this.fin = fin;
	}

	public double longitud() {
		return inicio.distanciaA(fin);
	}
}
```


## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

### Respuesta

En Java no suele hablarse de destrucción explícita como en C++ con
`delete`. Los objetos dejan de poder usarse cuando ya no existe ninguna
referencia accesible hacia ellos. A partir de ese momento, el recolector
de basura (*garbage collector*) puede liberar su memoria más adelante.

Por eso `Linea` no destruye directamente sus `Punto`. Simplemente, si la
`Linea` deja de ser alcanzable y esos puntos solo eran accesibles a
través de ella, entonces también pasan a ser inalcanzables y el sistema
podrá recuperarlos automáticamente. Además, ese momento no es exacto ni
determinista: Java decide cuándo ejecutar la recolección.


## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

### Respuesta

Aquí la relación es de composición débil porque los objetos `Profesor`
pueden existir independientemente del `Departamento`. El departamento los
agrupa y mantiene referencias a ellos, pero no es necesario que los cree
ni que su existencia dependa de él. La parte delicada del diseño está en
preservar la invariante: siempre debe existir director y ese director
debe pertenecer a la lista de profesores.

Para no romper la encapsulación, no se devuelve el array interno ni se
permite modificarlo desde fuera. En su lugar, se ofrecen operaciones de
alto nivel: añadir profesor, eliminar por posición, consultar cuántos
hay, obtener uno por posición y cambiar el director. Cuando una
operación rompe la invariante, se lanza una excepción.

```java
public final class Profesor {
	private final String nombre;

	public Profesor(String nombre) {
		if (nombre == null || nombre.isBlank()) {
			throw new IllegalArgumentException("El nombre no puede estar vacío");
		}
		this.nombre = nombre;
	}

	public String getNombre() {
		return nombre;
	}
}

public class Departamento {
	private static final int MAX_PROFESORES = 50;

	private final String nombre;
	private final Profesor[] profesores;
	private int numProfesores;
	private Profesor director;

	public Departamento(String nombre, Profesor directorInicial) {
		if (nombre == null || nombre.isBlank()) {
			throw new IllegalArgumentException("El nombre del departamento es obligatorio");
		}
		if (directorInicial == null) {
			throw new IllegalArgumentException("Debe existir un director inicial");
		}

		this.nombre = nombre;
		this.profesores = new Profesor[MAX_PROFESORES];
		this.profesores[0] = directorInicial;
		this.numProfesores = 1;
		this.director = directorInicial;
	}

	public String getNombre() {
		return nombre;
	}

	public int getNumeroProfesores() {
		return numProfesores;
	}

	public Profesor getProfesor(int pos) {
		comprobarPosicion(pos);
		return profesores[pos];
	}

	public Profesor getDirector() {
		return director;
	}

	public void addProfesor(Profesor profesor) {
		if (profesor == null) {
			throw new IllegalArgumentException("El profesor no puede ser nulo");
		}
		if (numProfesores == MAX_PROFESORES) {
			throw new IllegalStateException("No caben más profesores");
		}
		if (indiceDe(profesor) != -1) {
			throw new IllegalArgumentException("Ese profesor ya pertenece al departamento");
		}

		profesores[numProfesores] = profesor;
		numProfesores++;
	}

	public void setDirector(Profesor nuevoDirector) {
		if (nuevoDirector == null) {
			throw new IllegalArgumentException("El director no puede ser nulo");
		}
		if (indiceDe(nuevoDirector) == -1) {
			throw new IllegalArgumentException("El director debe pertenecer al departamento");
		}

		this.director = nuevoDirector;
	}

	public void removeProfesor(int pos) {
		comprobarPosicion(pos);

		if (profesores[pos] == director) {
			throw new IllegalStateException("No se puede eliminar al director actual");
		}

		for (int i = pos; i < numProfesores - 1; i++) {
			profesores[i] = profesores[i + 1];
		}

		profesores[numProfesores - 1] = null;
		numProfesores--;
	}

	private void comprobarPosicion(int pos) {
		if (pos < 0 || pos >= numProfesores) {
			throw new IndexOutOfBoundsException("Posición inválida: " + pos);
		}
	}

	private int indiceDe(Profesor profesor) {
		for (int i = 0; i < numProfesores; i++) {
			if (profesores[i] == profesor) {
				return i;
			}
		}
		return -1;
	}
}
```


## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

### Respuesta

Con `List`, el código queda más simple porque Java ya ofrece operaciones
de almacenamiento dinámico, inserción al final, borrado por posición y
consulta por índice. Así se evita buena parte del trabajo manual del
array: controlar el desplazamiento de elementos, manejar huecos y llevar
tantos detalles de implementación a mano.

Si existiera un método que devolviera todos los profesores, no convendría
devolver directamente la lista interna, porque desde fuera podría
modificarse y romper la invariante del objeto. Por ejemplo, alguien
podría eliminar al director desde esa lista sin pasar por las
comprobaciones del `Departamento`. La solución consiste en devolver una
vista no modificable o una copia defensiva.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Departamento {
	private final String nombre;
	private final List<Profesor> profesores;
	private Profesor director;

	public Departamento(String nombre, Profesor directorInicial) {
		if (nombre == null || nombre.isBlank()) {
			throw new IllegalArgumentException("El nombre del departamento es obligatorio");
		}
		if (directorInicial == null) {
			throw new IllegalArgumentException("Debe existir un director inicial");
		}

		this.nombre = nombre;
		this.profesores = new ArrayList<>();
		this.profesores.add(directorInicial);
		this.director = directorInicial;
	}

	public int getNumeroProfesores() {
		return profesores.size();
	}

	public Profesor getProfesor(int pos) {
		return profesores.get(pos);
	}

	public Profesor getDirector() {
		return director;
	}

	public void addProfesor(Profesor profesor) {
		if (profesor == null) {
			throw new IllegalArgumentException("El profesor no puede ser nulo");
		}
		if (profesores.contains(profesor)) {
			throw new IllegalArgumentException("Ese profesor ya pertenece al departamento");
		}
		profesores.add(profesor);
	}

	public void setDirector(Profesor nuevoDirector) {
		if (!profesores.contains(nuevoDirector)) {
			throw new IllegalArgumentException("El director debe pertenecer al departamento");
		}
		this.director = nuevoDirector;
	}

	public void removeProfesor(int pos) {
		Profesor profesor = profesores.get(pos);
		if (profesor == director) {
			throw new IllegalStateException("No se puede eliminar al director actual");
		}
		profesores.remove(pos);
	}

	public List<Profesor> getProfesores() {
		return Collections.unmodifiableList(profesores);
	}
}
```


## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

### Respuesta

En una composición recursiva, una clase contiene una referencia a otro
objeto de su misma clase. En este caso, una `Persona` puede tener una
madre, que a su vez también es otra `Persona`. El patrón es recursivo
porque la definición vuelve a usar la propia clase como componente.

Para mantener la inmutabilidad, los atributos se dejan como `private`
`final` y no se proporcionan setters. El caso base de la recursión puede
representarse con una madre desconocida mediante `null`. Otros ejemplos
clásicos son los nodos de un árbol, los directorios con subdirectorios,
los comentarios con respuestas y las excepciones con causa.

```java
public final class Persona {
	private final String nombre;
	private final Persona madre;

	public Persona(String nombre, Persona madre) {
		if (nombre == null || nombre.isBlank()) {
			throw new IllegalArgumentException("El nombre es obligatorio");
		}
		this.nombre = nombre;
		this.madre = madre;
	}

	public String getNombre() {
		return nombre;
	}

	public Persona getMadre() {
		return madre;
	}

	public static void main(String[] args) {
		Persona abuela = new Persona("Carmen", null);
		Persona madre = new Persona("Laura", abuela);
		Persona nieto = new Persona("Diego", madre);

		System.out.println("Nieto: " + nieto.getNombre());
		System.out.println("Madre: " + nieto.getMadre().getNombre());
		System.out.println("Abuela: " + nieto.getMadre().getMadre().getNombre());
	}
}
```

## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

### Respuesta

Una relación bidireccional es aquella en la que ambos extremos conocen
la relación. No solo `Departamento` sabría qué profesores contiene, sino
que además cada `Profesor` sabría a qué `Departamento` pertenece. Eso
permite navegar en ambos sentidos, pero también obliga a mantener la
coherencia entre las dos partes.

Para implementarlo, `Profesor` tendría que incorporar un atributo
`departamento`, y las operaciones de alta, baja o cambio deberían
actualizar ambos lados a la vez. Por ejemplo, al añadir un profesor al
departamento, también habría que fijar su referencia de vuelta; y al
eliminarlo, habría que dejarla a `null` o reasignarla correctamente.
Esa sincronización debe centralizarse bien para evitar estados
inconsistentes, como un profesor que apunte a un departamento que no lo
contiene en su colección.


**Notas de clase**

3)	**Multiplicidad** de A y B (p.ej: entre Línea y Punto)


Linea					Punto
P1:Punto	(00..*)		x: double
P2:Punto	(2..2)		y: double

 - 1 línea se relaciona con 2 puntos y como máximo con 2 puntos
 - 1 punto se relaciona como mínimo con 0 Líneas y como máximo con muchas líneas

 4)	**Composición fuerte VS Composición débil**
	
	**Fuerte** ➜ El contenedor (p.ej: línea) es el que crea los objetos que contiene (p.ej: punto) y estos no viven más allá del contenedor

		➜ El ciclo de vida del contenido está vinculado al contenedor

	**Débil** ➜ El contenedor y contenido tienen ciclos de vida independientes (p.ej: Los objetos Punto pueden vivir sin estar en objetos Linea)


	UML:
	 - Composición Propiamente (fuerte)
	 - Asociación o agregación (débil)
	 - Usamos rombos para expresar que el contenedor es basicamente un contenedor y poco más


7) En Java, la vida de Punto termina cuando es inaccesible, y en el ejemplo ocurre cuando Línea deja de serlo a su vez. Por tanto, cuando Línea es "basura", también lo serán sus puntos, y serán eliminados de memoria por el **recolector de basura**

8)	- Hay 2 composiciones débiles
	- No se expone el array al exterior (imposible garantizar invariante de clase)
	- En los méetodos que gestionan el departamento se controla que no se viole la invariante de clase

9) Con List<Profesor>

	- No cambia la interfaz gráfica
	- Es más fácil implementar algunos métodos, delegando en métodos de List
	- Si se devuelve, hay que devolver una copia, para proteger la invariante de clase.