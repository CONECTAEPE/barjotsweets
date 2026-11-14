# Barjotsweet — publicación en GitHub Pages + persistencia Supabase

## Estado de la integración

- Proyecto Supabase: `BARJOTSWEET`
- Project ref: `soljzsxndbstdfyxmipn`
- Región: `us-east-2` (Ohio)
- URL: `https://soljzsxndbstdfyxmipn.supabase.co`
- Base de datos creada con tablas `profiles`, `user_roles`, `system_data` y `system_backups`.
- RLS habilitado en las cuatro tablas.
- Cliente JavaScript Supabase fijado a `@supabase/supabase-js@2.81.1` mediante CDN.
- La aplicación usa solo la clave pública `publishable`; no contiene `service_role` ni claves secretas.

## 1. Publicar en GitHub Pages

1. Descarga este paquete y coloca `index.html` en la raíz del repositorio GitHub que utilizarás.
2. Sube y confirma el archivo en la rama `main`.
3. En GitHub abre **Settings → Pages**.
4. En **Build and deployment**, selecciona **Deploy from a branch**.
5. Selecciona la rama `main` y la carpeta `/ (root)`, y guarda.
6. Espera a que GitHub Pages publique el sitio. La URL tendrá una forma similar a `https://TU-USUARIO.github.io/TU-REPOSITORIO/`.

No necesita Node, compilación ni servidor propio: es un sitio estático.

## 2. Autorizar el dominio en Supabase Auth

En el proyecto `BARJOTSWEET`, abre **Authentication → URL Configuration**:

- **Site URL:** coloca la URL real de GitHub Pages, por ejemplo `https://TU-USUARIO.github.io/TU-REPOSITORIO/`.
- **Redirect URLs:** agrega la URL exacta `https://TU-USUARIO.github.io/TU-REPOSITORIO/**`.
- Si usarás un dominio personalizado, agrega también ese dominio y su patrón de rutas.
- Para pruebas locales, puedes agregar `http://localhost:5500/**` si ese es el puerto que utilizas.

La dirección real debe reemplazar los valores de ejemplo. La aplicación devuelve a la URL desde la que se inició el registro.

## 3. Configurar el correo de autenticación

En **Authentication → Sign In / Providers → Email**:

- Mantén habilitado el proveedor Email.
- Se recomienda mantener activada la confirmación de correo.
- Configura las plantillas de confirmación si deseas personalizar los mensajes.

## 4. Crear la primera cuenta Administrador

1. Abre la aplicación publicada en GitHub Pages.
2. Escribe el correo y una contraseña de al menos 8 caracteres.
3. Pulsa **Crear una cuenta nueva** y escribe el nombre completo cuando se solicite.
4. Confirma el correo si Supabase lo solicita.
5. Inicia sesión una vez confirmada la cuenta.
6. En Supabase abre **SQL Editor → New query** y ejecuta `PROMOVER_ADMIN.sql`, sustituyendo `TU_CORREO@DOMINIO.COM` por el correo que registraste.
7. Cierra sesión y vuelve a entrar en Barjotsweet. La aplicación leerá el nuevo rol `admin`.

Todos los registros nuevos reciben automáticamente el rol `usuario`. No pueden asignarse permisos administrativos desde el navegador.

## 5. Roles

- `admin`: administra el directorio de usuarios, edita el contenido, exporta respaldos y restaura información.
- `editor`: edita y sincroniza el contenido.
- `usuario`: consulta el contenido sin modificarlo.

Para asignar el rol `editor`, usa el mismo SQL del archivo `PROMOVER_ADMIN.sql` cambiando `'admin'` por `'editor'`.

## 6. Persistencia y respaldos

- El contenido de las seis secciones se guarda en `public.system_data`.
- La aplicación recupera el contenido desde Supabase al iniciar sesión.
- Los cambios de Administradores y Editores se sincronizan automáticamente.
- Los respaldos manuales se descargan como JSON y también se registran en `public.system_backups`.
- La restauración de un JSON válido sincroniza el contenido restaurado con Supabase.
- Los perfiles y roles se almacenan en `profiles` y `user_roles`; las contraseñas son administradas por Supabase Auth y no se guardan en tablas de la aplicación.

## 7. Migrar datos de la versión local anterior

La versión local anterior y el sitio GitHub Pages tienen almacenamientos de navegador distintos. Si ya tienes una copia JSON exportada desde la versión anterior:

1. Publica esta versión.
2. Crea y confirma tu cuenta Administrador.
3. Promueve la cuenta a `admin` con el SQL anterior.
4. Inicia sesión, usa **Restaurar datos** y selecciona el JSON exportado previamente.

Las antiguas contraseñas locales no se migran: por seguridad, cada usuario debe crear una cuenta nueva con correo electrónico en Supabase Auth.

## Seguridad

La clave `sb_publishable_...` incluida en `index.html` es una clave pública para clientes web. La protección de datos depende de las políticas RLS ya habilitadas. **Nunca agregues una clave `service_role`, `sb_secret_...`, contraseña de base de datos ni token de administración al HTML o al repositorio GitHub.**
