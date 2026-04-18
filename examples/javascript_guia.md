# JavaScript del Portafolio — `app.js`
### Sesión 3 · JavaScript Esencial &nbsp;|&nbsp; Sesión 4 · JavaScript Asíncrono y APIs

---

## Índice

1. [Variables: `let` y `const`](#1-variables-let-y-const)
2. [Módulos ES6](#2-módulos-es6)
3. [Arrow Functions y Template Literals](#3-arrow-functions-y-template-literals)
4. [Destructuring, Spread y Rest](#4-destructuring-spread-y-rest)
5. [Clases ES6 y Objetos](#5-clases-es6-y-objetos)
6. [Métodos de Array](#6-métodos-de-array)
7. [Selección DOM](#7-selección-dom)
8. [Manipulación DOM y Renderizado](#8-manipulación-dom-y-renderizado)
9. [Eventos](#9-eventos)
10. [Local Storage](#10-local-storage)
11. [El Event Loop y Asincronía](#11-el-event-loop-y-asincronía)
12. [Promesas (Promise)](#12-promesas-promise)
13. [async / await](#13-async--await)
14. [try / catch / finally](#14-try--catch--finally)
15. [Fetch GET](#15-fetch-get)
16. [Fetch POST](#16-fetch-post)
17. [JSON](#17-json)
18. [Promise.all()](#18-promiseall)
19. [APIs Públicas usadas](#19-apis-públicas-usadas)

---

## 1. Variables: `let` y `const`

JavaScript moderno usa **`const`** y **`let`** en lugar del antiguo `var`.

| Palabra clave | ¿Se puede reasignar? | Cuándo usarla |
|---|---|---|
| `const` | ❌ No | Valores que no cambian: URLs, configuración, objetos fijos |
| `let` | ✅ Sí | Valores que cambian: contadores, estado de filtros |
| `var` | ✅ Sí | ⚠️ Evitar — tiene scope confuso y hoisting problemático |

```js
// ✅ const: valor fijo durante toda la ejecución
const SITE_AUTHOR = 'Hebert Cussi';
const API_BASE    = 'https://jsonplaceholder.typicode.com';
const POKE_API    = 'https://pokeapi.co/api/v2';

// ✅ let: cambia según la interacción del usuario
let currentFilter = 'all';   // cambia cuando el usuario pulsa un filtro
let pokemonPage   = 0;        // se incrementa al pedir más pokémon
let projectsData  = [];       // se llena desde la API
```

> **Regla práctica:** usar siempre `const` por defecto. Cambiar a `let` solo si necesitas reasignar. Nunca usar `var`.

---

## 2. Módulos ES6

Los módulos permiten **dividir el código en archivos** y compartir funciones entre ellos con `export` e `import`.

```js
// app.js — exportar funciones para que otros archivos las usen
export { renderProjects, initContactForm, fetchProjects };
```

```html
<!-- index.html — importar el módulo con type="module" -->
<script type="module" src="assets/js/app.js"></script>
```

```js
// otro-archivo.js — importar lo que se necesita
import { renderProjects } from './app.js';
```

> ⚠️ `type="module"` requiere un servidor HTTP (Live Server, `npx serve`).
> Abriendo `index.html` directo desde el explorador dará **CORS error**.
> Para desarrollo sin servidor: quitar `type="module"` y la línea `export`.

**Tipos de export:**

```js
// Export nombrado (varios por archivo)
export { renderProjects, fetchProjects };
export const PI = 3.14159;

// Export por defecto (uno solo por archivo)
export default class App { }

// Import nombrado
import { renderProjects } from './app.js';

// Import por defecto
import App from './app.js';

// Import todo
import * as utils from './utils.js';
```

---

## 3. Arrow Functions y Template Literals

### Arrow Functions

Sintaxis corta para escribir funciones. Heredan el contexto `this` del entorno donde se definen.

```js
// Función tradicional
function greet(name) {
  return '¡Hola desde el portafolio de ' + name + '!';
}

// ✅ Arrow function — equivalente
const greet = (name) => `¡Hola desde el portafolio de ${name}! 🚀`;

// Sin parámetros
const sayHi = () => 'Hola';

// Un parámetro — se puede omitir el paréntesis
const double = n => n * 2;

// Cuerpo con varias líneas — necesita llaves y return
const getFullName = (first, last) => {
  const full = `${first} ${last}`;
  return full.trim();
};

// Retornar objeto directamente — envolver en paréntesis
const makeUser = (name) => ({ name, active: true });
```

### Template Literals

Cadenas de texto delimitadas con **backticks** (`` ` ``). Permiten interpolación de expresiones con `${}` y texto multilínea.

```js
const name    = 'Hebert';
const amount  = 1500;

// ✅ Template literal
const greet      = `¡Hola desde el portafolio de ${name}! 🚀`;
const formatPrice = `$${Number(amount).toLocaleString('es-BO')}`;

console.log(greet);       // ¡Hola desde el portafolio de Hebert! 🚀
console.log(formatPrice); // $1.500

// Multilínea sin concatenar
const html = `
  <article class="project-card">
    <h3>${name}</h3>
    <p>Desarrollador Full Stack</p>
  </article>
`;

// Expresión dentro de ${}
const total = `Total: ${10 * 5} items`;  // Total: 50 items
```

---

## 4. Destructuring, Spread y Rest

### Destructuring de Objeto

Extrae propiedades de un objeto en variables con nombres propios.

```js
const devProfile = {
  name:     'Hebert Cussi',
  role:     'Full Stack Developer',
  location: 'La Paz, Bolivia',
  skills:   ['JavaScript', 'React', 'Node.js', 'Python'],
};

// ✅ Destructuring — extrae name, role, skills en una sola línea
const { name, role, skills } = devProfile;

console.log(name);  // 'Hebert Cussi'
console.log(role);  // 'Full Stack Developer'

// Con alias (renombrar la variable)
const { name: fullName, location: city } = devProfile;
console.log(fullName); // 'Hebert Cussi'
console.log(city);     // 'La Paz, Bolivia'

// Con valor por defecto
const { available = false } = devProfile;
console.log(available); // false (no existía en el objeto)

// Destructuring en parámetros de función
function showProfile({ name, role }) {
  console.log(`${name} trabaja como ${role}`);
}
showProfile(devProfile);
```

### Destructuring de Array

```js
const [mainSkill, ...otherSkills] = skills;

console.log(mainSkill);    // 'JavaScript'
console.log(otherSkills);  // ['React', 'Node.js', 'Python']

// Intercambiar valores sin variable temporal
let a = 1, b = 2;
[a, b] = [b, a];
console.log(a, b); // 2 1

// Saltar elementos con coma
const [first, , third] = ['a', 'b', 'c'];
console.log(first, third); // 'a' 'c'
```

### Spread Operator (`...`)

Expande un array u objeto en sus elementos individuales.

```js
const frontEnd = ['React', 'Angular', 'TypeScript'];
const backEnd  = ['Node.js', 'Express', 'Django'];

// ✅ Combinar arrays
const allTechs = [...frontEnd, ...backEnd];
// ['React', 'Angular', 'TypeScript', 'Node.js', 'Express', 'Django']

// ✅ Clonar y extender un objeto SIN mutar el original
const updatedProfile = { ...devProfile, available: true };
// devProfile no cambia — updatedProfile tiene la propiedad extra

// Spread en llamada a función
const nums = [3, 1, 4, 1, 5];
console.log(Math.max(...nums)); // 5
```

### Rest Parameter (`...`)

Recoge múltiples argumentos en un array. Se usa en la **definición** de la función.

```js
// ✅ El resto de elementos después del primero
const [mainSkill, ...otherSkills] = skills;

// ✅ Rest en parámetros de función
function logAll(first, ...rest) {
  console.log('Primero:', first);
  console.log('Resto:', rest);
}
logAll('a', 'b', 'c', 'd');
// Primero: a
// Resto: ['b', 'c', 'd']
```

> **Diferencia clave:** `spread` expande, `rest` recoge. El operador `...` hace ambas cosas según el contexto.

---

## 5. Clases ES6 y Objetos

Las clases son una sintaxis más clara para crear objetos con propiedades y métodos compartidos.

```js
class Project {
  #id;  // ✅ Campo privado (ES2022) — solo accesible dentro de la clase

  constructor({ id, title, description, techs, emoji, category }) {
    this.#id         = id;
    this.title       = title;
    this.description = description;
    this.techs       = techs;
    this.emoji       = emoji;
    this.category    = category;
  }

  // Getter: accede al campo privado desde fuera
  get id() { return this.#id; }

  // Método: genera HTML con template literal + .map()
  toHTML() {
    const badges = this.techs
      .map(t => `<span class="tech-badge">${t}</span>`)
      .join('');

    return `
      <article class="project-card" data-id="${this.#id}">
        <div class="project-img">${this.emoji}</div>
        <div class="project-info">
          <h3>${this.title}</h3>
          <p>${this.description}</p>
          <footer class="project-tags">${badges}</footer>
        </div>
      </article>`;
  }
}

// Crear instancias con objetos literales como argumento
const p1 = new Project({
  id: 1, category: 'frontend', emoji: '📱',
  title: 'App de Tareas',
  description: 'Aplicación con drag & drop y modo oscuro.',
  techs: ['React', 'CSS Modules'],
});

console.log(p1.title);   // 'App de Tareas'
console.log(p1.toHTML()); // string con HTML listo para insertar en el DOM
```

---

## 6. Métodos de Array

Los métodos de array modernos reciben una **función callback** y devuelven un resultado nuevo sin modificar el original.

### `.filter()` — Filtra elementos

```js
const filterProjects = (category) =>
  category === 'all'
    ? localProjects
    : localProjects.filter(p => p.category === category);

// Uso
const frontEndProjects = filterProjects('frontend');
// Solo los proyectos con category === 'frontend'
```

### `.map()` — Transforma cada elemento

```js
// Extrae solo los títulos
const getTitles = () => localProjects.map(p => p.title);
// ['App de Tareas', 'EcoShop', 'Dashboard Analytics', ...]

// Generar HTML de cada proyecto
projectsGrid.innerHTML = filtered
  .map(p => p.toHTML())
  .join('');
```

### `.reduce()` — Acumula en un valor

```js
// Contar proyectos por categoría
const countByCategory = localProjects.reduce((acc, p) => {
  acc[p.category] = (acc[p.category] || 0) + 1;
  return acc;
}, {});
// { frontend: 2, fullstack: 2, backend: 1 }
```

### `.find()` — Busca el primer elemento que cumple la condición

```js
const findProject = (id) => localProjects.find(p => p.id === id);

const project = findProject(3);
console.log(project.title); // 'Dashboard Analytics'
```

### `.forEach()` — Itera sin devolver nada

```js
filterButtons.forEach(btn => {
  btn.classList.toggle('active', btn.dataset.filter === category);
});
```

### `.join()` — Une elementos en un string

```js
const badges = this.techs
  .map(t => `<span class="tech-badge">${t}</span>`)
  .join('');  // une con separador vacío
```

### Encadenamiento

```js
// map + join en una sola expresión
statsEl.innerHTML = stats
  .map(({ label, value }) => `
    <div class="stat-item">
      <strong>${value}</strong>
      <span>${label}</span>
    </div>`)
  .join('');
```

---

## 7. Selección DOM

El DOM (Document Object Model) es la representación del HTML como árbol de objetos que JavaScript puede leer y modificar.

```js
// querySelector — selecciona el PRIMER elemento que coincide con el selector CSS
const projectsGrid = document.querySelector('.projects-grid');
const contactForm  = document.querySelector('#contacto form');

// querySelectorAll — selecciona TODOS los elementos (devuelve NodeList)
const filterButtons = document.querySelectorAll('.filter-btn');

// getElementById — selecciona por ID (más rápido que querySelector)
const themeToggle   = document.getElementById('theme-toggle');
const pokeGrid      = document.getElementById('poke-grid');
const countryInput  = document.getElementById('country-search');
const toastEl       = document.getElementById('toast');
```

| Método | Devuelve | Selector |
|---|---|---|
| `querySelector(css)` | Primer elemento o `null` | Cualquier selector CSS |
| `querySelectorAll(css)` | `NodeList` (todos) | Cualquier selector CSS |
| `getElementById(id)` | Elemento o `null` | Solo ID, sin `#` |
| `getElementsByClassName` | `HTMLCollection` (live) | Solo clase, sin `.` |

---

## 8. Manipulación DOM y Renderizado

```js
function renderProjects(category = 'all') {
  if (!projectsGrid) return;

  const filtered = filterProjects(category);

  // ✅ innerHTML: reemplaza TODO el contenido interno con HTML
  projectsGrid.innerHTML = filtered
    .map(p => p.toHTML())
    .join('');

  // ✅ classList.toggle(clase, condición): agrega o quita clase según booleano
  filterButtons.forEach(btn => {
    btn.classList.toggle('active', btn.dataset.filter === category);
  });

  // ✅ textContent: cambia el texto visible (más seguro que innerHTML para texto plano)
  const counter = document.getElementById('project-count');
  if (counter) counter.textContent = `${filtered.length} proyectos`;
}
```

### Métodos de manipulación más usados

```js
// Propiedades de contenido
element.innerHTML   = '<p>HTML</p>';   // inserta HTML (riesgo XSS con datos de usuario)
element.textContent = 'Texto plano';   // inserta solo texto, escapa HTML

// Clases CSS
element.classList.add('activo');
element.classList.remove('activo');
element.classList.toggle('dark-mode');              // alterna
element.classList.toggle('active', condicion);      // agrega si true, quita si false
element.classList.contains('activo');               // true / false

// Atributos
element.setAttribute('aria-label', 'Modo claro');
element.getAttribute('data-filter');

// Acceso a data attributes
btn.dataset.filter;   // lee data-filter="frontend"

// Deshabilitar / habilitar
btn.disabled = true;
btn.disabled = false;
```

---

## 9. Eventos

Los eventos permiten ejecutar código cuando el usuario **interactúa** con la página.

```js
// Sintaxis base
element.addEventListener('tipo-de-evento', funcionCallback);

// ── Filtros de proyectos (click) ──────────────────────────
filterButtons.forEach(btn => {
  btn.addEventListener('click', (e) => {
    currentFilter = e.currentTarget.dataset.filter;
    renderProjects(currentFilter);
  });
});

// ── Tema oscuro (click) ───────────────────────────────────
themeToggle.addEventListener('click', () => {
  document.body.classList.toggle('dark-mode');
  const isDark = document.body.classList.contains('dark-mode');

  themeToggle.setAttribute('aria-label', isDark ? 'Modo claro' : 'Modo oscuro');
  themeToggle.textContent = isDark ? '☀️' : '🌙';

  localStorage.setItem('theme', isDark ? 'dark' : 'light');
});

// ── Scroll suave (click en enlaces internos) ──────────────
document.querySelectorAll('a[href^="#"]').forEach(anchor => {
  anchor.addEventListener('click', (e) => {
    e.preventDefault();   // ✅ Cancela el comportamiento por defecto (salto brusco)
    const target = document.querySelector(anchor.getAttribute('href'));
    if (target) target.scrollIntoView({ behavior: 'smooth' });
  });
});

// ── Buscador de países (input — debounce) ─────────────────
let searchTimer;
countryInput.addEventListener('input', (e) => {
  clearTimeout(searchTimer);
  // Espera 600ms después de que el usuario deja de escribir
  searchTimer = setTimeout(() => fetchCountry(e.target.value), 600);
});

// ── DOMContentLoaded — punto de entrada seguro ───────────
document.addEventListener('DOMContentLoaded', () => {
  initContactForm();
  loadDashboardData();
  if (pokeSection) fetchPokemons(0);
});
```

### Eventos más comunes

| Evento | Se dispara cuando… |
|---|---|
| `click` | El usuario hace clic |
| `input` | El valor de un input cambia |
| `submit` | Se envía un formulario |
| `DOMContentLoaded` | El HTML termina de parsearse |
| `keydown` / `keyup` | Se presiona / suelta una tecla |
| `change` | Un select o checkbox cambia |
| `scroll` | El usuario hace scroll |
| `mouseover` / `mouseout` | El cursor entra / sale de un elemento |

---

## 10. Local Storage

`localStorage` guarda datos en el navegador que **persisten aunque se cierre la pestaña**.

```js
// ── Guardar tema ──────────────────────────────────────────
localStorage.setItem('theme', 'dark');

// ── Leer tema al cargar la página ─────────────────────────
function initTheme() {
  const saved = localStorage.getItem('theme');  // 'dark' | 'light' | null
  if (saved === 'dark') {
    document.body.classList.add('dark-mode');
    if (themeToggle) themeToggle.textContent = '☀️';
  }
}
initTheme();

// ── Guardar borrador del formulario ───────────────────────
function saveFormDraft(data) {
  // Los objetos se deben convertir a string con JSON.stringify
  localStorage.setItem('form-draft', JSON.stringify(data));
}

// ── Recuperar borrador ────────────────────────────────────
function loadFormDraft() {
  const raw = localStorage.getItem('form-draft');
  // JSON.parse convierte el string de vuelta a objeto
  return raw ? JSON.parse(raw) : null;
}

// ── Eliminar tras envío exitoso ───────────────────────────
localStorage.removeItem('form-draft');
```

### Métodos de localStorage

| Método | Descripción |
|---|---|
| `setItem(key, value)` | Guarda un valor (siempre como string) |
| `getItem(key)` | Lee un valor — devuelve `null` si no existe |
| `removeItem(key)` | Elimina una clave |
| `clear()` | Elimina todo el almacenamiento del sitio |
| `key(index)` | Obtiene la clave en esa posición |

> ⚠️ `localStorage` solo almacena **strings**. Para objetos y arrays usar `JSON.stringify` al guardar y `JSON.parse` al leer.

---

## 11. El Event Loop y Asincronía

JavaScript es **single-thread**: ejecuta una sola tarea a la vez. El Event Loop es el mecanismo que permite ejecutar código asíncrono sin bloquear la interfaz.

```
┌─────────────────────────────────────────┐
│            CALL STACK (síncrono)        │
│  → ejecuta funciones en orden           │
└────────────────┬────────────────────────┘
                 │ cuando está vacío
┌────────────────▼────────────────────────┐
│         MICROTASK QUEUE                 │
│  → Promesas resueltas (.then, await)    │
│  → Se vacía ANTES que el callback queue │
└────────────────┬────────────────────────┘
                 │
┌────────────────▼────────────────────────┐
│         CALLBACK QUEUE (macrotasks)     │
│  → setTimeout, setInterval, eventos     │
└─────────────────────────────────────────┘
```

```js
// Demostración del orden de ejecución
console.log('1 – Síncrono');

setTimeout(() => console.log('3 – Macrotarea (callback queue)'), 0);

Promise.resolve()
  .then(() => console.log('2 – Microtarea (promesa)'));

console.log('1b – Síncrono también');

// Salida en consola:
// 1 – Síncrono
// 1b – Síncrono también
// 2 – Microtarea (promesa)      ← microtask queue va antes
// 3 – Macrotarea (callback queue)
```

En el portafolio se usa esto en `showToast`:

```js
function showToast(msg, type = 'success') {
  toastEl.textContent = msg;
  toastEl.className = `toast toast--${type} toast--show`;

  // setTimeout: macrotarea — se ejecuta después de 3 segundos
  setTimeout(() => {
    toastEl.classList.remove('toast--show');
  }, 3000);
}
```

---

## 12. Promesas (Promise)

Una `Promise` representa una operación que **todavía no ha terminado** pero que terminará en el futuro. Tiene tres estados: `pending` → `fulfilled` o `rejected`.

```js
function validateEmail(email) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const valid = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
      if (valid) {
        resolve({ ok: true, email });      // ✅ fulfills con el valor
      } else {
        reject(new Error(`Email inválido: ${email}`));  // ❌ rechaza con error
      }
    }, 500);
  });
}
```

### `.then()` y `.catch()`

```js
// Encadenamiento de promesas
validateEmail('hebert@ejemplo.com')
  .then(({ email }) => {
    console.log(`✅ Email válido: ${email}`);
    return fetch('https://api.ejemplo.com/contacto');  // devuelve otra promesa
  })
  .then(res => res.json())                              // encadena el siguiente .then
  .then(data => console.log('Respuesta:', data))
  .catch(err => {
    console.error('❌ Error:', err.message);            // captura cualquier error
  });
```

---

## 13. async / await

`async/await` es **azúcar sintáctico** sobre las Promesas. Hace que el código asíncrono se lea como código síncrono.

```js
// Función marcada con async → siempre devuelve una Promesa
async function fetchJSON(url) {
  const res = await fetch(url);           // await: pausa aquí hasta que la promesa se resuelva
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return await res.json();                // res.json() también es una Promesa
}
```

### Comparación: `.then()` vs `async/await`

```js
// Con .then() — funciona pero se vuelve complejo
fetch(url)
  .then(res => res.json())
  .then(data => {
    return fetch(data.nextUrl);
  })
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));

// ✅ Con async/await — más legible
async function getData() {
  const res1  = await fetch(url);
  const data1 = await res1.json();

  const res2  = await fetch(data1.nextUrl);
  const data2 = await res2.json();

  console.log(data2);
}
```

---

## 14. try / catch / finally

Bloque para **manejar errores** en código asíncrono (y síncrono).

```js
async function initContactForm() {
  contactForm.addEventListener('submit', async (e) => {
    e.preventDefault();
    const btnSubmit = contactForm.querySelector('.btn-submit');
    btnSubmit.textContent = 'Enviando...';
    btnSubmit.disabled = true;

    const { nombre, email, mensaje } = Object.fromEntries(
      new FormData(contactForm)
    );

    try {
      // ① Valida el email con una Promesa
      await validateEmail(email);

      // ② Envía el formulario con Fetch POST
      const res = await fetch(`${API_BASE}/posts`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ nombre, email, mensaje }),
      });

      if (!res.ok) throw new Error('No se pudo enviar el mensaje');

      const data = await res.json();
      localStorage.removeItem('form-draft');
      contactForm.reset();
      showToast(`✅ Mensaje enviado, ${nombre}! (ID: ${data.id})`);

    } catch (err) {
      // Se ejecuta si cualquier await lanza un error
      showToast(`❌ ${err.message}`, 'error');

    } finally {
      // ✅ Se ejecuta SIEMPRE — haya error o no
      btnSubmit.textContent = 'Enviar mensaje →';
      btnSubmit.disabled = false;
    }
  });
}
```

| Bloque | Cuándo se ejecuta |
|---|---|
| `try` | Siempre — contiene el código que puede fallar |
| `catch(err)` | Solo si ocurre un error dentro del `try` |
| `finally` | **Siempre**, después de `try` o `catch` |

---

## 15. Fetch GET

`fetch()` es la API nativa del navegador para hacer peticiones HTTP.

```js
// Wrapper reutilizable para Fetch GET
async function fetchJSON(url) {
  const res = await fetch(url);                          // GET por defecto
  if (!res.ok) throw new Error(`HTTP ${res.status} – ${res.statusText}`);
  return await res.json();
}

// ── Uso con JSONPlaceholder ────────────────────────────
const posts = await fetchJSON(`${API_BASE}/posts?_limit=3`);

// ── Uso con PokeAPI ───────────────────────────────────
const data = await fetchJSON(`${POKE_API}/pokemon?limit=6&offset=0`);

// ── Uso con REST Countries ───────────────────────────
const [country] = await fetchJSON(
  `https://restcountries.com/v3.1/name/Bolivia?fields=name,capital,population,flags`
);
```

### Propiedades de la respuesta

```js
const res = await fetch(url);

res.ok          // true si status 200-299
res.status      // número: 200, 404, 500…
res.statusText  // 'OK', 'Not Found'…
res.json()      // Promesa → objeto JavaScript
res.text()      // Promesa → string
res.blob()      // Promesa → archivo binario
```

---

## 16. Fetch POST

Para enviar datos al servidor, se configura el segundo argumento de `fetch`.

```js
const res = await fetch(`${API_BASE}/posts`, {
  method: 'POST',                                  // método HTTP
  headers: {
    'Content-Type': 'application/json',            // indicamos que enviamos JSON
  },
  body: JSON.stringify({                           // objeto → string JSON
    nombre,
    email,
    mensaje,
    title:  nombre,
    body:   mensaje,
  }),
});

if (!res.ok) throw new Error('No se pudo enviar el mensaje');

const data = await res.json();    // respuesta del servidor
console.log('ID creado:', data.id);
```

---

## 17. JSON

JSON (JavaScript Object Notation) es el formato estándar para intercambiar datos con APIs.

```js
// JavaScript → JSON string (para enviar al servidor)
const objeto = { nombre: 'Hebert', edad: 30, activo: true };
const jsonString = JSON.stringify(objeto);
// '{"nombre":"Hebert","edad":30,"activo":true}'

// JSON string → JavaScript (al recibir del servidor)
const raw  = '{"nombre":"Hebert","edad":30}';
const data = JSON.parse(raw);
console.log(data.nombre); // 'Hebert'

// En Fetch: res.json() hace el JSON.parse automáticamente
const data = await res.json();

// En localStorage: los objetos se almacenan como string
localStorage.setItem('form-draft', JSON.stringify({ nombre, email }));
const draft = JSON.parse(localStorage.getItem('form-draft'));
```

### Tipos válidos en JSON

| Tipo JS | Ejemplo JSON |
|---|---|
| String | `"hola"` |
| Number | `42`, `3.14` |
| Boolean | `true`, `false` |
| Array | `[1, 2, 3]` |
| Object | `{"key": "value"}` |
| null | `null` |
| ❌ undefined | no existe en JSON |
| ❌ function | no existe en JSON |

---

## 18. Promise.all()

Ejecuta **múltiples promesas en paralelo** y espera a que todas terminen. Si una falla, todas fallan.

```js
// ── PokeAPI: 6 detalles en paralelo ──────────────────────
async function fetchPokemons(offset = 0) {
  const data = await fetchJSON(`${POKE_API}/pokemon?limit=6&offset=${offset}`);

  // Sin Promise.all: 6 requests secuenciales (lento)
  // ✅ Con Promise.all: 6 requests al mismo tiempo (rápido)
  const details = await Promise.all(
    data.results.map(p => fetchJSON(p.url))
  );

  pokeGrid.innerHTML = details.map(({ name, sprites, types }) => `
    <div class="poke-card">
      <img src="${sprites.front_default}" alt="${name}" />
      <p>${name}</p>
    </div>`).join('');
}

// ── Dashboard: 3 APIs al mismo tiempo ────────────────────
async function loadDashboardData() {
  const [posts, users, todos] = await Promise.all([
    fetchJSON(`${API_BASE}/posts?_limit=1`),   // promesa 1
    fetchJSON(`${API_BASE}/users?_limit=1`),   // promesa 2
    fetchJSON(`${API_BASE}/todos?_limit=1`),   // promesa 3
  ]);
  // Cuando las 3 terminan, desestructuramos los resultados
  console.log(posts, users, todos);
}
```

### Variantes de Promise

```js
// Promise.all — espera todas, falla si una falla
const results = await Promise.all([p1, p2, p3]);

// Promise.allSettled — espera todas, no falla aunque alguna falle
const results = await Promise.allSettled([p1, p2, p3]);
results.forEach(r => {
  if (r.status === 'fulfilled') console.log(r.value);
  if (r.status === 'rejected')  console.warn(r.reason);
});

// Promise.race — devuelve la primera que termine (ganadora)
const first = await Promise.race([p1, p2, p3]);

// Promise.any — devuelve la primera que se resuelva exitosamente
const first = await Promise.any([p1, p2, p3]);
```

---

## 19. APIs Públicas usadas

| API | URL | Gratuita | API Key |
|---|---|---|---|
| **JSONPlaceholder** | `jsonplaceholder.typicode.com` | ✅ | No |
| **PokeAPI** | `pokeapi.co/api/v2` | ✅ | No |
| **REST Countries** | `restcountries.com/v3.1` | ✅ | No |
| **OpenWeather** | `openweathermap.org/api` | ✅ (plan free) | ✅ Sí |
| **The Movie DB** | `themoviedb.org/documentation/api` | ✅ (plan free) | ✅ Sí |

### Ejemplo de uso con OpenWeather (requiere API key)

```js
const API_KEY = 'TU_API_KEY_AQUI';  // Obtener en openweathermap.org

async function getWeather(city) {
  const url = `https://api.openweathermap.org/data/2.5/weather`
            + `?q=${city}&appid=${API_KEY}&units=metric&lang=es`;

  const data = await fetchJSON(url);

  const { name, main: { temp }, weather: [{ description }] } = data;
  console.log(`${name}: ${temp}°C – ${description}`);
}

getWeather('La Paz');
```

### Ejemplo de uso con The Movie DB (requiere API key)

```js
const TMDB_KEY = 'TU_API_KEY_AQUI'; // Obtener en themoviedb.org

async function getMovies() {
  const url = `https://api.themoviedb.org/3/movie/popular`
            + `?api_key=${TMDB_KEY}&language=es-ES&page=1`;

  const { results } = await fetchJSON(url);

  results.slice(0, 5).forEach(({ title, vote_average }) => {
    console.log(`${title} — ⭐ ${vote_average}`);
  });
}
```

---

## Flujo completo del formulario de contacto

El formulario combina **todos los conceptos** de las sesiones 3 y 4:

```
Usuario escribe     →  evento 'input'  →  saveFormDraft() (localStorage)
Usuario envía       →  evento 'submit'  →  e.preventDefault()
                    →  new FormData()   →  Destructuring { nombre, email, mensaje }
                    →  await validateEmail(email)  →  Promise (resolve/reject)
                    →  await fetch(POST)  →  Event Loop (microtask)
                    →  await res.json()   →  JSON.parse automático
                    →  showToast()        →  Manipulación DOM + classList
                    →  finally            →  resetea botón siempre
```

---

*Documentación generada para el curso Desarrollo Web Full Stack — Hebert Cussi Cuentas · 2026*
