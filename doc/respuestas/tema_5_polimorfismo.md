<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

### Respuesta

El polimorfismo es la capacidad de los objetos de diferentes clases a responder al mismo mensaje (llamada de método) de maneras distintas. Esta característica permite escribir código genérico que funciona con múltiples tipos de objetos sin necesidad de conocer específicamente cuál es el tipo exacto en tiempo de compilación. En la programación orientada a objetos, el polimorfismo es fundamental para implementar diseños flexibles y extensibles, permitiendo agregar nuevas clases sin modificar el código que las utiliza.

La sobreescritura de métodos es el mecanismo concreto mediante el cual se implementa el polimorfismo. Consiste en redefinir un método heredado en una clase derivada, proporcionando una implementación diferente para ese método. Cuando se sobreescribe un método, la subclase proporciona su propia versión del método heredado de su clase base, manteniendo la misma firma (nombre, parámetros y tipo de retorno, dentro de ciertas restricciones). De esta forma, cuando se invoca el método sobre un objeto, la versión ejecutada será la de la clase concreta del objeto, no la de la clase base.


## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

### Respuesta

La ligadura dinámica o enlace tardío es el mecanismo mediante el cual se determina qué versión de un método se ejecutará en tiempo de ejecución, en lugar de en tiempo de compilación. La relación con el polimorfismo es directa: la ligadura dinámica es el mecanismo que permite que el polimorfismo funcione. Cuando se invoca un método sobre una referencia de tipo base, el sistema determina en tiempo de ejecución cuál es el tipo real del objeto y ejecuta la versión del método correspondiente a esa clase concreta.

En Java, la ligadura dinámica es el comportamiento por defecto para todos los métodos no-estáticos, sin necesidad de indicarlo explícitamente. El compilador permite asignar objetos de clases derivadas a referencias de tipo base, y en tiempo de ejecución se resuelve correctamente qué método invocar. En cambio, en C++ es necesario declara explícitamente los métodos como `virtual` para que se utilice ligadura dinámica; si no se declara `virtual`, se utiliza ligadura estática y se ejecuta el método de la clase de la referencia, no la del objeto.

En Python, la ligadura dinámica es el comportamiento estándar y no requiere ninguna palabra clave especial. De hecho, Python utiliza un sistema aún más flexible que Java, basado en la búsqueda dinámica de atributos en tiempo de ejecución, lo que permite un polimorfismo aún más general conocido como "duck typing": si un objeto tiene el método que se llama, se ejecuta, independientemente de su clase.


## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

### Respuesta

A continuación se muestra el ejemplo solicitado:

```java
public class Soldado {
    private String nombre;
    
    public Soldado(String nombre) {
        this.nombre = nombre;
    }
    
    public void saluda() {
        System.out.println(nombre + ": ¡Presente!");
    }
}

public class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }
    
    @Override
    public void saluda() {
        System.out.println("ZAPADOR: ¡Listo para excavar!");
    }
}

public class Artillero extends Soldado {
    public Artillero(String nombre) {
        super(nombre);
    }
}

public class Main {
    public static void main(String[] args) {
        Soldado[] soldados = new Soldado[3];
        soldados[0] = new Zapador("Juan");
        soldados[1] = new Artillero("Carlos");
        soldados[2] = new Soldado("Pedro");
        
        for (Soldado s : soldados) {
            s.saluda();
        }
    }
}
```

Este código muestra cómo aunque el array se declara de tipo `Soldado`, cada elemento ejecuta la versión del método `saluda()` correspondiente a su clase concreta. El `Zapador` ejecuta su propia versión, mientras que el `Artillero` y el `Soldado` ejecutan la versión de la clase base.


## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

### Respuesta

Sí, es posible invocar el método base desde una versión sobreescrita utilizando la palabra clave `super`. Esta palabra clave hace referencia específicamente a la clase base, permitiendo acceder a sus métodos incluso después de haberlos sobreescrito en la clase derivada.

A continuación se modifica la clase `Zapador` para ilustrar esto:

```java
public class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }
    
    @Override
    public void saluda() {
        super.saluda();  // Invoca el método de Soldado
        System.out.println("ZAPADOR A SUS ORDENES");
    }
}
```

Ahora, cuando se invoca `saluda()` sobre un objeto `Zapador`, primero se ejecuta el comportamiento del `Soldado` (que imprime el nombre y "¡Presente!"), y luego se añade el mensaje adicional del zapador. La palabra clave utilizada es `super`, que permite al programador reutilizar y extender el comportamiento de la clase base en lugar de reemplazarlo completamente.


## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

### Respuesta

Al sobreescribir un método en Java, los tipos de los parámetros deben ser exactamente iguales a los del método base; si se cambian, se produce una sobrecarga accidental, no una sobreescritura. Respecto al tipo de retorno, desde Java 5 es posible utilizar un tipo más específico (subtipo) del retorno original, lo que se conoce como "covarianza de tipos de retorno". Los modificadores de acceso no pueden ser más restrictivos; por ejemplo, si el método base es `public`, el sobreescrito también debe serlo. La sobreescritura (*overriding*) se refiere a redefinir un método heredado de una clase base en una subclase, mientras que la sobrecarga (*overloading*) consiste en definir múltiples métodos con el mismo nombre pero diferentes parámetros en la misma clase o jerarquía, sin que uno sea versión del otro.

La anotación `@Override` es una anotación especial que informa al compilador que se pretende sobreescribir un método de la clase base. Su principal utilidad es que el compilador verificará que efectivamente existe un método con esa firma en la clase base; si no existe, generará un error de compilación. Es recomendable usarla siempre porque previene errores silenciosos, como escribir incorrectamente el nombre del método o cambiar accidentalmente los parámetros, que resultarían en una sobrecarga accidental en lugar de una sobreescritura. Además, mejora la legibilidad del código, haciendo explícita la intención del programador de sobreescribir un método heredado.


## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

### Respuesta

Sí, cuando se sobreescriben métodos como `toString()` o `equals()`, se está utilizando polimorfismo, aunque muchas veces los estudiantes no lo noten explícitamente. Estos métodos son heredados de la clase `Object`, que es la clase base de todas las clases en Java. Cuando se sobreescriben en una clase propia, se está aprovechando exactamente el mismo mecanismo de polimorfismo: proporcionar una implementación específica para esa clase que será utilizada en lugar de la versión genérica de `Object`.

En la práctica, el polimorfismo está presente desde el primer momento del aprendizaje de Java. Por ejemplo, cuando el sistema imprime un objeto usando `System.out.println()`, internamente invoca el método `toString()` del objeto, y gracias a la ligadura dinámica, se ejecuta la versión específica de la clase, no la genérica de `Object`. De forma similar, cuando se comparan objetos con `equals()`, se utiliza la versión sobreescrita. Por lo tanto, aunque no se hable explícitamente de polimorfismo en los temas iniciales, se está empleando constantemente desde el principio.


## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

### Respuesta

Una clase abstracta es una clase que no puede instanciarse directamente y está diseñada para ser extendida por otras clases. Sirve como plantilla o contrato que define qué métodos deben implementar sus subclases. Un método abstracto es un método que se declara en una clase abstracta pero no tiene implementación; solo especifica la firma del método. Las subclases que hereden de una clase abstracta están obligadas a proporcionar una implementación para todos los métodos abstractos, o de lo contrario, también deben ser declaradas como abstractas. Por lo tanto, no es posible crear instancias de una clase abstracta; el intento de hacerlo resultará en un error de compilación.

A continuación se muestra el ejemplo con `Soldado` como clase abstracta:

```java
public abstract class Soldado {
    private String nombre;
    
    public Soldado(String nombre) {
        this.nombre = nombre;
    }
    
    public void saluda() {
        System.out.println(nombre + ": ¡Presente!");
    }
    
    public abstract void atacar();
}

public class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }
    
    @Override
    public void atacar() {
        System.out.println("Zapador: ¡Colocando explosivos!");
    }
}

public class Artillero extends Soldado {
    public Artillero(String nombre) {
        super(nombre);
    }
    
    @Override
    public void atacar() {
        System.out.println("Artillero: ¡Disparando cañón!");
    }
}
```

La palabra clave `abstract` debe colocarse antes de la declaración de la clase (para hacerla abstracta) y también antes de la firma de los métodos abstractos. Cada subclase concreta debe proporcionar su propia implementación del método `atacar()`.


## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

### Respuesta

La palabra clave `final` se utiliza para impedir cambios. Cuando se aplica a una clase, evita que se cree ninguna subclase de esa clase; cuando se aplica a un método, impide que sea sobreescrito en las subclases. Esto desactiva el polimorfismo para esa clase o método específico, obligando a que el compilador utilice ligadura estática en lugar de dinámica para las llamadas a métodos `final`. La relación con el polimorfismo es inversa: `final` limita el polimorfismo, asegurando que el comportamiento de ese método o clase no pueda ser modificado por subclases.

En la API estándar de Java hay varios ejemplos de clases `final`. La clase `String` es probablemente el más conocido; es `final` porque la cadena de caracteres es un tipo fundamental que no debe ser extendido. Otros ejemplos incluyen `Integer`, `Double`, `Boolean` y otras clases wrapper; también `Math` es una clase `final`. El propósito principal de hacer estas clases `final` es asegurar que su comportamiento es predecible y no pueda ser alterado por subclases mal diseñadas, lo que es especialmente importante para clases que tienen implicaciones de seguridad o comportamiento matemático bien definido.


## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

### Respuesta

Una interfaz es un tipo especial de contrato que especifica qué métodos públicos debe implementar cualquier clase que la implemente. A diferencia de las clases abstractas, las interfaces no pueden contener estado (variables de instancia) ni constructores; solo declaran constantes y métodos abstract. Las interfaces están diseñadas para definir un conjunto de comportamientos que clases no relacionadas entre sí pueden implementar, permitiendo una forma de polimorfismo aún más flexible que la herencia.

Aunque las interfaces y las clases abstractas comparten similitudes, hay diferencias clave: una clase abstracta puede contener métodos concretos con implementación, constructores y variables de instancia, mientras que una interfaz históricamente solo contenía declaraciones de métodos abstract (aunque en versiones recientes de Java pueden tener métodos con implementación predeterminada usando `default`). La herencia entre clases es simple (una clase hereda de una sola clase base), pero una clase puede implementar múltiples interfaces simultáneamente. Esto permite que una clase adquiera comportamientos de varias fuentes, proporcionando una forma de polimorfismo múltiple que Java no permite mediante herencia de clases.


## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

### Respuesta

A continuación se muestra el ejemplo solicitado:

```java
public abstract class Punto {
    public abstract double calcularDistanciaA(Punto otro);
}

public class Punto2D extends Punto {
    private double x, y;
    
    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }
    
    @Override
    public double calcularDistanciaA(Punto otro) {
        if (!(otro instanceof Punto2D)) {
            throw new IllegalArgumentException("Se esperaba un Punto2D");
        }
        Punto2D p = (Punto2D) otro;  // Downcasting
        double dx = this.x - p.x;
        double dy = this.y - p.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

public class Punto3D extends Punto {
    private double x, y, z;
    
    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }
    
    @Override
    public double calcularDistanciaA(Punto otro) {
        if (!(otro instanceof Punto3D)) {
            throw new IllegalArgumentException("Se esperaba un Punto3D");
        }
        Punto3D p = (Punto3D) otro;  // Downcasting
        double dx = this.x - p.x;
        double dy = this.y - p.y;
        double dz = this.z - p.z;
        return Math.sqrt(dx * dx + dy * dy + dz * dz);
    }
}

public class Linea {
    private Punto inicio;
    private Punto fin;
    
    public Linea(Punto inicio, Punto fin) {
        this.inicio = inicio;
        this.fin = fin;
    }
    
    public double getLongitud() {
        return inicio.calcularDistanciaA(fin);
    }
}
```

Este diseño aprovecha el polimorfismo de forma elegante: la clase `Linea` no necesita conocer si trabaja con puntos 2D o 3D, ni tiene que distinguir entre ellos explícitamente. El método `calcularDistanciaA` se invoca polimórficamente sobre la referencia de tipo `Punto`, y gracias a la ligadura dinámica, se ejecuta la implementación correcta. El uso de `instanceof` dentro de cada implementación verifica que el otro punto es del tipo correcto, y el downcasting permite acceder a los atributos específicos de cada tipo de punto.


## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

### Respuesta

La herencia de interfaces en Java es un mecanismo que permite que una interfaz extienda a otra interfaz, heredando todos sus métodos. Una interfaz que extiende a otra debe proporcionar (directamente o a través de sus clases implementadoras) implementaciones para todos los métodos tanto de la interfaz base como de la interfaz derivada. Sí existe herencia múltiple de interfaces: una interfaz puede extender a múltiples interfaces simultáneamente, utilizando la palabra clave `extends` seguida de una lista de interfaceseparadas por comas.

A continuación se muestra el ejemplo solicitado:

```java
public interface Fichero {
    String leerContenido();
}

public interface FicheroEscribible extends Fichero {
    void enviarContenido(String contenido);
    void eliminar();
}

public class FicheroTexto implements FicheroEscribible {
    private String contenido;
    private String nombre;
    
    public FicheroTexto(String nombre, String contenido) {
        this.nombre = nombre;
        this.contenido = contenido;
    }
    
    @Override
    public String leerContenido() {
        return contenido;
    }
    
    @Override
    public void enviarContenido(String nuevo_contenido) {
        this.contenido = nuevo_contenido;
        System.out.println("Contenido del fichero actualizado");
    }
    
    @Override
    public void eliminar() {
        contenido = null;
        System.out.println("Fichero " + nombre + " eliminado");
    }
}
```

En este ejemplo, `FicheroEscribible` extiende a `Fichero`, heredando el método `leerContenido()` y agregando dos métodos adicionales. Cualquier clase que implemente `FicheroEscribible` debe proporcionar implementación para los tres métodos. Esto permite crear jerarquías de interfaces que van siendo más específicas conforme se desciende en la jerarquía, similar a como funcionan las clases abstractas pero sin las limitaciones de la herencia simple.
