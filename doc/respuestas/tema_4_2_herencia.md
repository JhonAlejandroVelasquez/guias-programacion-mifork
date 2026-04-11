<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.
</prompt>
----
-->

## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

### Respuesta

La herencia define una relación de especialización: si `Artillero` hereda de `Soldado`, entonces **Artillero es-un Soldado**. Esto permite modelar familias de objetos con una parte común y partes específicas. La superclase concentra lo compartido y las subclases añaden diferencias.

Las dos implicaciones principales son: (1) **compatibilidad de tipos**, porque un objeto de subtipo puede tratarse como su supertipo (`Soldado s = new Artillero(...)`); y (2) **herencia de estado y comportamiento**, porque la subclase recibe atributos y métodos de la superclase (por ejemplo, `nombre` y `saludar()`).

En el ejemplo, `Artillero` y `Zapador` heredan de `Soldado`. Ambos saludan igual porque comparten método y estado heredado, pero cada uno tiene su propio dato específico (`cohetes` o `minas`) accesible por getter. Al recorrer un `Soldado[]`, todos son válidos por compatibilidad de tipos.

```java
class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Hola, soy " + nombre);
    }
}

class Artillero extends Soldado {
    private int cohetes;

    public Artillero(String nombre, int cohetes) {
        super(nombre);
        this.cohetes = cohetes;
    }

    public int getCohetes() {
        return cohetes;
    }
}

class Zapador extends Soldado {
    private int minas;

    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public int getMinas() {
        return minas;
    }
}

public class Demo1 {
    public static void main(String[] args) {
        Soldado[] peloton = {
            new Artillero("Luis", 6),
            new Zapador("Marta", 4),
            new Artillero("Ana", 3)
        };

        for (Soldado s : peloton) {
            s.saludar();
        }
    }
}
```

## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre?

### Respuesta

Al crear una instancia de subclase se ejecuta una cadena de constructores, uno por cada nivel de la jerarquía. El orden siempre va desde la superclase hacia la subclase. Por ejemplo, en `new Artillero(...)`, primero se ejecuta `Soldado(...)` y después `Artillero(...)`.

`super(...)` sirve para invocar el constructor de la clase padre. Si no se escribe nada, Java intenta insertar `super()` automáticamente al inicio del constructor. Esa llamada implícita exige que exista un constructor sin parámetros accesible en la superclase.

Si la clase base no tiene constructor sin parámetros visible, es obligatorio escribir `super(conParametros)` de forma explícita. En ese caso, no hacerlo provoca error de compilación. Por tanto, se debe llamar a `super` siempre que el diseño del padre lo requiera.

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

### Respuesta

Sí, los atributos privados de la superclase forman parte de la instancia concreta de la subclase en memoria. Una instancia de `Artillero` contiene su propio estado (`cohetes`) y también el estado heredado de `Soldado` (`nombre`), aunque sea privado.

Sin embargo, esa presencia en memoria no da acceso directo desde el código de la subclase. `private` restringe el acceso a la propia clase que declara el atributo. Por eso `Artillero` no puede usar `nombre` directamente si está declarado como privado en `Soldado`.

La forma correcta es acceder mediante métodos de la superclase (por ejemplo, `saludar()` o un getter público/protegido). Así se respeta encapsulación, aunque el dato exista dentro del mismo objeto físico.

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

### Respuesta

La compatibilidad de tipos favorece la extensibilidad porque el código cliente depende del supertipo (`Soldado`) y no de cada subtipo concreto. Esto permite añadir nuevas clases sin tocar la lógica común ya escrita.

Si se añade `Francotirador`, el bucle que recorre `Soldado[]` y llama a `saludar()` no cambia. El nuevo subtipo entra de forma natural por cumplir el contrato del supertipo. Es una mejora clara de mantenibilidad.

Este enfoque reduce modificaciones en código estable y concentra cambios en puntos de extensión. Se evita propagar condicionales por tipo para operaciones que ya son comunes en la jerarquía.

```java
class Francotirador extends Soldado {
    private int balas;

    public Francotirador(String nombre, int balas) {
        super(nombre);
        this.balas = balas;
    }

    public int getBalas() {
        return balas;
    }
}

public class Demo4 {
    public static void main(String[] args) {
        Soldado[] peloton = {
            new Artillero("Luis", 6),
            new Zapador("Marta", 4),
            new Francotirador("Irene", 20)
        };

        // No se modifica al añadir subtipos
        for (Soldado s : peloton) {
            s.saludar();
        }
    }
}
```

## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

### Respuesta

Sí, una referencia del supertipo puede apuntar a objetos reales de subtipos. Esta conversión de subtipo a supertipo se denomina **upcasting** y suele ser implícita y segura. Por eso `Soldado s = new Artillero(...)` es válido.

Con una referencia `Soldado`, solo se puede invocar directamente lo declarado/visible en `Soldado`. Para acceder a métodos exclusivos de `Artillero`, se necesita **downcasting** (conversión explícita), que puede fallar si el objeto real no es de ese tipo.

`instanceof` permite comprobar el tipo real antes de convertir, evitando `ClassCastException`. En recorridos heterogéneos (array de `Soldado` con distintos subtipos) es la forma segura de aplicar lógica específica por subtipo.

```java
public class Demo5 {
    public static void main(String[] args) {
        Soldado[] peloton = {
            new Artillero("Luis", 6),
            new Zapador("Marta", 4),
            new Artillero("Ana", 3)
        };

        for (Soldado s : peloton) {
            s.saludar();

            if (s instanceof Artillero) {
                Artillero a = (Artillero) s; // downcasting seguro
                System.out.println("Cohetes: " + a.getCohetes());
            }
        }
    }
}
```

## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

### Respuesta

`protected` es un nivel de acceso intermedio en Java. Permite usar el miembro dentro de la misma clase, dentro del mismo paquete y desde subclases. Es más abierto que `private`, pero más restringido que `public`.

Se implementa anteponiendo la palabra clave `protected` al atributo o método. Es útil cuando se busca extender comportamiento en subclases sin exponerlo totalmente hacia cualquier consumidor externo.

En el ejemplo, `nombre` se declara protegido en `Soldado`. De ese modo, `Zapador` puede usarlo en su método específico para mostrar una acción propia, manteniendo una encapsulación parcial.

```java
class Soldado {
    protected String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Hola, soy " + nombre);
    }
}

class Zapador extends Soldado {
    private int minas;

    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }

    public void ponerMinas() {
        System.out.println(nombre + " está colocando " + minas + " minas.");
    }

    public int getMinas() {
        return minas;
    }
}
```

## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

### Respuesta

No en todos los lenguajes orientados a objetos existe una clase raíz universal única. El modelo depende del lenguaje y de su sistema de tipos; algunos separan jerarquías o tratan objetos y valores de forma distinta.

En Java sí existe una clase base común: `java.lang.Object`. Toda clase hereda directa o indirectamente de `Object`, lo que garantiza métodos comunes como `toString()`, `equals()` y `hashCode()`.

Como matiz, los tipos primitivos (`int`, `double`, etc.) no son objetos. Aun así, Java dispone de clases envoltorio (`Integer`, `Double`, ...) para tratarlos como objetos cuando es necesario.

## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

### Respuesta

La herencia múltiple permite que una clase herede de varias superclases simultáneamente. Su objetivo es combinar comportamientos de más de una base, pero puede introducir conflictos de resolución y ambigüedad.

En Java no existe herencia múltiple de clases: una clase solo puede tener un `extends`. Esto simplifica la jerarquía y evita problemas clásicos de diseño.

Java sí permite implementar múltiples interfaces (`implements A, B, C`). Por tanto, hay herencia múltiple de **tipo** (interfaces), pero no de implementación de estado de varias clases.

## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente.

### Respuesta

Una excepción no controlada se define heredando de `RuntimeException`. De ese modo no exige `try/catch` obligatorio por compilador, aunque puede capturarse si interesa. Para aportar contexto funcional, la excepción puede contener un objeto `Usuario`.

La composición con `Usuario` permite saber exactamente qué dato causó el fallo. Esto mejora trazabilidad y depuración sin depender solo del mensaje de texto.

Se recomienda sobrecargar constructores: uno básico y otro con causa (`Throwable cause`) para encadenar errores. Así se conserva la excepción original y se añade información de dominio en la misma estructura de error.

```java
class Usuario {
    private final String id;
    private final String nombre;

    public Usuario(String id, String nombre) {
        this.id = id;
        this.nombre = nombre;
    }

    @Override
    public String toString() {
        return "Usuario{id='" + id + "', nombre='" + nombre + "'}";
    }
}

class UsuarioNoEncontradoException extends RuntimeException {
    private final Usuario usuario;

    public UsuarioNoEncontradoException(Usuario usuario) {
        super("Usuario no encontrado: " + usuario);
        this.usuario = usuario;
    }

    public UsuarioNoEncontradoException(Usuario usuario, Throwable cause) {
        super("Usuario no encontrado: " + usuario, cause);
        this.usuario = usuario;
    }

    public Usuario getUsuario() {
        return usuario;
    }
}
```

## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

### Respuesta

La herencia no solo reutiliza implementación, también establece una relación conceptual fuerte de tipo (**es-un**). Si esa relación no existe realmente en el dominio, el diseño queda forzado y difícil de mantener.

Además, heredar incrementa el acoplamiento con la clase base. Cambios en la superclase pueden afectar a subclases de forma indirecta y costosa. Esto reduce la flexibilidad de evolución del sistema.

Para reutilización pura, composición o delegación suele ser mejor opción. Se reaprovecha comportamiento sin imponer jerarquías rígidas ni compromisos semánticos incorrectos.

## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

### Respuesta

La composición permite construir objetos a partir de otros objetos colaborando entre sí. Esto facilita intercambiar piezas sin reestructurar una jerarquía completa, lo que mejora adaptabilidad.

También reduce acoplamiento porque cada clase conoce interfaces o colaboradores concretos en lugar de depender de una base heredada compleja. El impacto de cambios suele estar más localizado.

En pruebas unitarias, composición ofrece ventajas claras: se pueden inyectar dobles de prueba y aislar comportamiento con facilidad. Por eso se recomienda como estrategia por defecto, dejando herencia para casos donde la relación “es-un” sea sólida.

## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

### Respuesta

La idea indica que las subclases acaban dependiendo de detalles internos de la superclase (estado protegido, orden de llamadas, supuestos implícitos). Aunque no sean públicos para clientes externos, esos detalles se vuelven relevantes para quien hereda.

Cuando la base cambia internamente, las subclases pueden romperse aunque la interfaz pública parezca igual. Esto limita la libertad de evolución de la superclase y debilita encapsulación efectiva.

Con composición, esa dependencia interna suele ser menor y más explícita. El contrato entre objetos se expresa por interfaces/colaboraciones, no por extensión de implementación interna.

## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

### Respuesta

Con herencia, lo común se centraliza en `Persona`, y `Estudiante`/`Trabajador` amplían la base con datos propios. Es una solución directa cuando el dominio valida claramente que ambos son subtipos de la misma entidad base.

Con composición, ambos tipos contienen un objeto `DatosPersonales` recibido por constructor. Esto favorece reutilización sin acoplar a una jerarquía única y permite evolucionar cada clase con mayor independencia.

Ambas alternativas pueden ser válidas; la elección depende del modelo del dominio y del grado de flexibilidad que se necesite. En escenarios cambiantes, composición suele ofrecer mejor mantenibilidad.

```java
// Alternativa 1: Herencia
class Persona {
    private String dni;
    private String nombre;

    public Persona(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() { return dni; }
    public String getNombre() { return nombre; }
}

class EstudianteH extends Persona {
    private String carrera;

    public EstudianteH(String dni, String nombre, String carrera) {
        super(dni, nombre);
        this.carrera = carrera;
    }
}

class TrabajadorH extends Persona {
    private String empresa;

    public TrabajadorH(String dni, String nombre, String empresa) {
        super(dni, nombre);
        this.empresa = empresa;
    }
}
```

```java
// Alternativa 2: Composición
class DatosPersonales {
    private String dni;
    private String nombre;

    public DatosPersonales(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() { return dni; }
    public String getNombre() { return nombre; }
}

class Estudiante {
    private DatosPersonales datos;
    private String carrera;

    public Estudiante(DatosPersonales datos, String carrera) {
        this.datos = datos;
        this.carrera = carrera;
    }
}

class Trabajador {
    private DatosPersonales datos;
    private String empresa;

    public Trabajador(DatosPersonales datos, String empresa) {
        this.datos = datos;
        this.empresa = empresa;
    }
}
```