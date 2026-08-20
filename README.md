# Gestión de Clientes — Denis Bardales (versión con Firebase + GitHub)

> **Si ya tenías la app funcionando y solo necesitas la actualización para que
> varios usuarios compartan la misma lista de clientes, ve directo a la sección
> [Actualización: acceso compartido entre usuarios](#actualización-acceso-compartido-entre-usuarios)
> al final de este documento.**

App para llevar el control de tus clientes (empresas, planes, tarifas, estado de pago
y visitas), con los datos guardados en la nube (Firebase) para que puedas entrar
desde tu computadora y tu teléfono con el mismo usuario y ver la misma información.

Este documento es la guía paso a paso para dejarla funcionando. Tómate tu tiempo,
son pasos de una sola vez.

## Resumen de lo que vas a hacer

1. Crear un proyecto de Firebase (gratis).
2. Activar el inicio de sesión con correo y contraseña, y crear tu usuario.
3. Activar la base de datos Firestore y aplicar las reglas de seguridad.
4. Copiar la configuración de tu proyecto dentro de `index.html`.
5. Subir el proyecto a GitHub y publicarlo con GitHub Pages para tener un link propio.

---

## Paso 1 — Crear el proyecto de Firebase

1. Entra a [https://console.firebase.google.com](https://console.firebase.google.com) con tu cuenta de Google.
2. Haz clic en **"Agregar proyecto"** (o "Crear proyecto").
3. Ponle un nombre, por ejemplo `gestion-clientes-denis`.
4. Puedes desactivar Google Analytics (no lo necesitas para esta app).
5. Espera a que se cree el proyecto.

## Paso 2 — Activar el inicio de sesión (Authentication)

1. En el menú izquierdo del proyecto, entra a **Authentication**.
2. Haz clic en **"Comenzar"** (Get started).
3. En la pestaña **Sign-in method**, elige **Correo electrónico/contraseña** (Email/Password) y actívalo. Guarda.
4. Ve a la pestaña **Users** (Usuarios) y haz clic en **"Agregar usuario"**.
5. Escribe el correo y la contraseña con la que vas a entrar a tu app (por ejemplo tu correo `denisjosue.dj@gmail.com` y una contraseña que tú elijas). Guarda.

Importante: la app **no tiene un formulario de registro público** — solo tú puedes
entrar, con el usuario que creaste aquí. Si más adelante quieres agregar a alguien
más, repites este mismo paso con su correo.

## Paso 3 — Activar Firestore (la base de datos)

1. En el menú izquierdo, entra a **Firestore Database**.
2. Haz clic en **"Crear base de datos"**.
3. Elige una ubicación (cualquiera cercana a Honduras, ej. `us-central` o `southamerica-east1`) y modo **producción**.
4. Una vez creada, ve a la pestaña **Reglas** (Rules) y reemplaza todo el contenido por lo que está en el archivo `firestore.rules` de esta carpeta:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/clientes/{clientId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

5. Haz clic en **"Publicar"**.

Esta regla asegura que solo tú (ya con tu sesión iniciada) puedas leer o escribir
tus propios clientes — nadie más puede ver tus datos, aunque conozca la dirección
de la app.

## Paso 4 — Copiar la configuración a `index.html`

1. En Firebase Console, ve a **Configuración del proyecto** (el ícono de engranaje, arriba a la izquierda).
2. Baja hasta **"Tus apps"** y haz clic en el ícono `</>` (Web) para registrar una app web.
3. Ponle un nombre (ej. `app-clientes`) y haz clic en **Registrar app**. No necesitas Firebase Hosting en este paso.
4. Firebase te va a mostrar un bloque de código con algo así:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "gestion-clientes-denis.firebaseapp.com",
  projectId: "gestion-clientes-denis",
  storageBucket: "gestion-clientes-denis.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

5. Abre el archivo `index.html` de esta carpeta y busca (cerca del inicio) el bloque:

```js
window.__FIREBASE_CONFIG__ = {
  apiKey: "TU_API_KEY",
  authDomain: "TU_PROYECTO.firebaseapp.com",
  projectId: "TU_PROYECTO",
  storageBucket: "TU_PROYECTO.appspot.com",
  messagingSenderId: "TU_SENDER_ID",
  appId: "TU_APP_ID"
};
```

6. Reemplaza cada valor `"TU_..."` por el valor real que te dio Firebase. Guarda el archivo.

**Nota sobre seguridad:** estos valores no son secretos — están diseñados para ir
dentro del código de cualquier app web de Firebase. Lo que realmente protege tus
datos son el inicio de sesión (Paso 2) y las reglas de Firestore (Paso 3).

## Paso 5 — Subir el proyecto a GitHub

1. Entra a [https://github.com](https://github.com) e inicia sesión.
2. Haz clic en el botón **"New"** (Nuevo repositorio).
3. Nómbralo, por ejemplo `gestion-clientes`. Puede ser público (no hay datos sensibles en el código, solo la configuración pública mencionada arriba). Crea el repositorio.
4. Dentro del repositorio recién creado, haz clic en **"uploading an existing file"** (subir un archivo existente).
5. Arrastra el archivo `index.html` (ya editado con tu configuración) a la página. Confirma con **"Commit changes"**.

## Paso 6 — Publicar con GitHub Pages (para tener un link)

1. En tu repositorio, ve a **Settings → Pages**.
2. En **"Branch"**, elige `main` y la carpeta `/ (root)`. Guarda.
3. Espera uno o dos minutos y recarga la página — GitHub te mostrará un link como:
   `https://tu-usuario.github.io/gestion-clientes/`
4. Abre ese link, inicia sesión con el correo y contraseña que creaste en el Paso 2, ¡y ya tienes tu app en la nube!

Puedes guardar ese link en tu teléfono (agregarlo a la pantalla de inicio) para
abrirlo como si fuera una app.

---

## ¿Ya tenías clientes registrados en la versión anterior?

Si ya habías empezado a usar la primera versión de la app (la que guardaba los
datos en el navegador), no los pierdes:

1. Abre esa versión anterior y haz clic en **"Exportar respaldo"** — se descarga un archivo `.json`.
2. Entra a esta nueva app (ya publicada y con tu sesión iniciada) y haz clic en **"Importar respaldo"**, selecciona ese mismo archivo.
3. Tus clientes aparecerán en la nueva versión, ahora sincronizados en la nube.

## Actualizaciones futuras

Cada vez que quieras hacerle un cambio a la app (por ejemplo, otro plan de precios
o un campo nuevo), puedo prepararte el archivo `index.html` actualizado — solo
tienes que volver a subirlo a GitHub (Paso 5) reemplazando el archivo existente;
GitHub Pages se actualiza solo en un par de minutos.

---

## Actualización: acceso compartido entre usuarios

En la primera versión, cada usuario que creabas en Firebase Authentication tenía
su propia lista de clientes separada — por diseño, para que nadie viera los datos
de nadie más. Si agregaste un segundo usuario esperando que viera los mismos
clientes que tú, por eso no los veía: estaban guardados en un espacio solo tuyo.

Esta versión lo cambia: **ahora todos los usuarios que crees en Firebase
Authentication comparten la misma lista de clientes.** Sigue sin existir un
formulario de registro público en la app, así que solo pueden entrar las
personas a las que tú les crees un usuario manualmente (Paso 2 de la guía).

Para actualizar tu app ya publicada a esta versión:

1. **Respalda lo que ya tienes.** Entra a tu app actual con la cuenta que tiene
   clientes registrados y haz clic en **"Exportar respaldo"** (se descarga un
   `.json`). Si las dos cuentas ya tienen clientes distintos, repite esto con
   cada cuenta por separado, para no perder ninguno.
2. **Actualiza las reglas de Firestore.** En Firebase Console → Firestore
   Database → pestaña **Reglas**, reemplaza todo el contenido por el nuevo
   `firestore.rules` que te compartí (permite que cualquier usuario que inició
   sesión lea y escriba la misma colección `clientes`). Haz clic en **Publicar**.
3. **Reemplaza el código en GitHub.** En tu repositorio, abre `index.html`,
   haz clic en el ícono de lápiz (editar), borra todo el contenido y pega el del
   nuevo `index.html` que te compartí — o simplemente vuelve a subir el archivo
   (Paso 5) reemplazando el existente. Confirma los cambios.
4. Espera uno o dos minutos a que GitHub Pages actualice y vuelve a abrir tu link.
5. Inicia sesión con cualquiera de las dos cuentas y usa **"Importar respaldo"**
   para volver a cargar el o los archivos `.json` del paso 1. Si importaste desde
   ambas cuentas y algún cliente quedó duplicado, ábrelo y elimínalo con el botón
   **"Eliminar cliente"**.

De ahí en adelante, cualquier cliente que agregues o edites con una cuenta será
visible de inmediato para la otra.
