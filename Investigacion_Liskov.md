# Investigación: principio de sustitución de Liskov

**Formato:** guion de diapositivas · **Área:** diseño de software orientado a objetos  
**Nota sobre el título:** “Liskop” parece ser una errata de **Liskov**. La literatura lo denomina principio de sustitución de Liskov (LSP) o subtipado conductual; no es un método paso a paso.

---

## Diapositiva 1 — ¿Qué es el principio de Liskov?

**Idea central:** cualquier instancia de un subtipo debe poder usarse donde se espera su tipo base sin cambiar las propiedades correctas del programa.

En otras palabras: heredar una interfaz implica respetar también el comportamiento que esa interfaz promete.

**Pregunta de investigación:** ¿cómo ayuda el LSP a diseñar jerarquías de tipos confiables?

*Visual sugerido:* una variable `TipoBase` que recibe indistintamente objetos `Subtipo A` y `Subtipo B`, y produce resultados consistentes.

---

## Diapositiva 2 — Objetivo y método de esta investigación

- **Objetivo:** explicar el origen, las reglas y el uso práctico del LSP.
- **Método:** revisión documental de los trabajos de Liskov (1987) y Liskov y Wing (1994).
- **Alcance:** subtipado y herencia en diseño de software; no se evalúa un lenguaje o proyecto particular.
- **Hallazgo principal:** la relación entre tipos debe conservar el comportamiento observable, no solo los nombres de métodos.

---

## Diapositiva 3 — Origen y evolución

- En **1987**, Barbara Liskov presentó la propiedad de sustitución en su trabajo sobre abstracción de datos y jerarquías.
- En **1994**, Barbara Liskov y Jeannette Wing desarrollaron una definición formal de subtipado conductual.
- Su aporte amplía la idea de “misma interfaz”: también considera especificaciones, invariantes y cambios de estado a través del tiempo.

**Lectura histórica:** la herencia es útil cuando expresa una relación de comportamiento válida; por sí sola no demuestra que un subtipo sea correcto.

Fuentes: [Liskov, 1987](https://doi.org/10.1145/62139.62141) · [Liskov y Wing, 1994](https://doi.org/10.1145/197320.197383).

---

## Diapositiva 4 — La regla en términos sencillos

Si un programa funciona con objetos del tipo `T`, debe seguir funcionando al recibir objetos de un subtipo `S` de `T`.

```text
Programa espera T → recibe S → conserva el comportamiento esperado
```

La compatibilidad se juzga desde lo que el cliente puede observar: resultados, errores permitidos y estado del objeto.

**No basta con:** que `S` herede de `T`, tenga los mismos métodos o compile.

Fuente conceptual: Liskov y Wing describen el subtipado como preservación de propiedades del supertipo en sus subtipos.

---

## Diapositiva 5 — ¿Qué debe conservar un subtipo?

1. **Precondiciones:** no exigir más condiciones para ejecutar una operación.
2. **Postcondiciones:** cumplir al menos lo que el tipo base prometía.
3. **Invariantes:** mantener las condiciones que siempre deben ser ciertas.
4. **Historia del estado:** permitir secuencias de operaciones coherentes con el tipo base.
5. **Contrato visible:** no introducir sorpresas incompatibles en resultados o excepciones.

Regla mnemotécnica habitual: **no fortalecer precondiciones ni debilitar postcondiciones**. El artículo formal también analiza invariantes y propiedades históricas.

Fuente: [Liskov y Wing, 1994](https://doi.org/10.1145/197320.197383).

---

## Diapositiva 6 — Ejemplo: rectángulo y cuadrado

Supongamos una clase `Rectángulo` con ancho y alto independientes. Un cliente hace:

```python
def calcular_area_esperada(figura):
    figura.set_ancho(5)
    figura.set_alto(4)
    assert figura.area() == 20
```

Si `Cuadrado` hereda de `Rectángulo` y cada setter iguala ambas dimensiones, tras esas llamadas el área será **16**, no **20**.

**Diagnóstico:** el cuadrado es un rectángulo en geometría, pero esa relación no funciona como subtipado para esta interfaz mutable. El subtipo rompe la expectativa del cliente.

---

## Diapositiva 7 — Una solución de diseño

Modelar ambos como figuras con un contrato común, sin obligar a que uno herede del otro:

```python
class Figura:
    def area(self): ...

class Rectangulo(Figura):
    def __init__(self, ancho, alto):
        self.ancho, self.alto = ancho, alto
    def area(self):
        return self.ancho * self.alto

class Cuadrado(Figura):
    def __init__(self, lado): self.lado = lado
    def area(self): return self.lado * self.lado
```

Ambas implementan la promesa de `Figura.area()`. Cada tipo conserva sus propias reglas y el cliente puede usar cualquier figura para calcular el área.

---

## Diapositiva 8 — Cómo aplicar el LSP en un proyecto

- Documentar el contrato de cada método: entradas válidas, resultado y efectos sobre el estado.
- Revisar si cada subtipo acepta las entradas que acepta el tipo base y respeta sus resultados.
- Probar el mismo conjunto de comportamientos contra todos los subtipos.
- Revisar también secuencias de llamadas, no solo una llamada aislada.
- Si un subtipo necesita excepciones, restricciones o cambios de significado, reconsiderar la jerarquía; usar composición o una interfaz más precisa.

**Pregunta de revisión:** ¿un cliente que conoce solo el tipo base tendría que preguntar qué subtipo recibió para poder funcionar correctamente?

---

## Diapositiva 9 — Aportes y límites

**Aportes**

- Reduce errores que aparecen al sustituir implementaciones.
- Hace más confiables el polimorfismo, las pruebas y la extensión del sistema.
- Centra el diseño en contratos y comportamiento observable.

**Límites**

- No determina automáticamente qué jerarquía conviene: requiere entender las expectativas del cliente.
- Las pruebas ayudan a detectar violaciones, pero no prueban por sí solas todo comportamiento posible.
- Una analogía del mundo real (por ejemplo, “cuadrado es un rectángulo”) no garantiza una relación de subtipado adecuada para una API concreta.

---

## Diapositiva 10 — Conclusiones y referencias

**Conclusiones**

1. El LSP es un criterio de corrección para relaciones de subtipado.
2. Un subtipo debe conservar las promesas conductuales del tipo base.
3. Cuando la herencia obliga a cambiar el contrato o sorprender al cliente, conviene rediseñar la abstracción.

**Referencias primarias**

- Liskov, B. (1987). *Keynote Address: Data Abstraction and Hierarchy*. OOPSLA ’87 Addendum, pp. 17–34. [ACM Digital Library](https://doi.org/10.1145/62139.62141).
- Liskov, B. H., & Wing, J. M. (1994). “A Behavioral Notion of Subtyping”. *ACM Transactions on Programming Languages and Systems*, 16(6), 1811–1841. [DOI: 10.1145/197320.197383](https://doi.org/10.1145/197320.197383) · [PDF de libre acceso](https://www.cs.columbia.edu/~wing/publications/LiskovWing94.pdf).

---

**Fin de la presentación**
