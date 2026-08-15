# Guía completa de funcionalidades — Sistema de Pedidos y Ventas

> Documento de apoyo para una exposición o video demostrativo. Describe qué hace el sistema, cómo se ve en la interfaz y qué proceso ejecuta el backend.

## 1. Presentación del proyecto

El Sistema de Pedidos y Ventas digitaliza el proceso comercial de una tienda: administra datos maestros, registra pedidos, emite ventas y genera comprobantes. Está compuesto por una aplicación web y una API REST.

| Capa | Tecnologías | Función |
| --- | --- | --- |
| Frontend | Angular 19, TypeScript, Tailwind CSS, RxJS, Signals y jsPDF | Interfaz, validaciones visuales y generación de comprobantes. |
| Backend | Java 17, Spring Boot 3, Spring Data JPA, Spring Security y JWT | Reglas de negocio, seguridad, API y persistencia. |
| Base de datos | Microsoft SQL Server | Almacenamiento de usuarios, catálogos, pedidos, ventas y comprobantes. |

**Idea para explicar en el video:** “El frontend guía al usuario y consume la API; el backend valida las reglas críticas y SQL Server conserva la información de forma estructurada.”

## 2. Perfiles y seguridad

Existen dos perfiles principales:

| Perfil | Funcionalidades |
| --- | --- |
| Administrador | Gestiona categorías, productos y usuarios; consulta los demás módulos. |
| Vendedor | Registra clientes, crea pedidos, edita pedidos pendientes, emite y anula ventas. |

### Cómo funciona el acceso

1. El usuario inicia sesión en la pantalla de acceso.
2. El backend verifica las credenciales y devuelve un token JWT.
3. Angular conserva la sesión y el interceptor agrega `Authorization: Bearer <token>` a las solicitudes protegidas.
4. Las guardas de Angular ocultan rutas no permitidas; el backend vuelve a comprobar el rol antes de ejecutar una operación.
5. Si el token expira o la API devuelve 401, la sesión se cierra y el usuario regresa al login.

**Punto importante:** las guardas del frontend mejoran la experiencia, pero la autorización real se impone en Spring Security mediante roles.

## 3. Funcionalidades del frontend

### 3.1 Panel principal

El dashboard presenta indicadores de negocio: ingresos, pedidos pendientes, productos activos, alertas de stock, clientes y transacciones recientes. Permite iniciar rápidamente acciones frecuentes.

**Qué mostrar:** entrar al panel después del login y explicar que centraliza el estado operativo del negocio.

### 3.2 Categorías

- Lista, búsqueda y detalle de categorías.
- El administrador puede crear, editar, desactivar y restaurar categorías.
- La desactivación no elimina la información: cambia su estado a inactivo.
- El vendedor puede consultar las categorías, sin administrar el catálogo.

### 3.3 Productos

- Catálogo con código, categoría, precio y stock.
- Formularios para crear y editar productos.
- Validación de precio y stock no negativos.
- Semáforo visual para identificar niveles de inventario.
- Desactivación y restauración sin borrar el historial.
- Vista de detalle del producto.

**Qué enfatizar:** el sistema no permite usar productos inactivos ni vender cantidades mayores al stock disponible.

### 3.4 Clientes

- Lista, búsqueda, alta, edición y detalle de clientes.
- Validación de documento: DNI de 8 dígitos o RUC de 11 dígitos, según el tipo seleccionado.
- Validación de correo electrónico.
- Búsqueda directa por documento durante el registro de un pedido.
- Un vendedor puede crear o actualizar clientes para no interrumpir una venta; las acciones administrativas de baja y restauración son restringidas.

### 3.5 Usuarios

- Módulo exclusivo para administradores.
- Registro, edición, desactivación y restauración de usuarios.
- Roles ADMIN y SELLER.
- El nombre de usuario puede generarse automáticamente.
- Si no se indica una contraseña, el sistema puede generarla y mostrarla una sola vez para su entrega segura.

### 3.6 Pedidos: flujo de carrito

El formulario de pedido reproduce un escenario de venta real:

1. El vendedor busca al cliente por DNI o RUC.
2. Si no existe, puede registrarlo desde el mismo proceso.
3. Selecciona productos activos con stock disponible.
4. Ajusta cantidades y ve subtotales y total.
5. Guarda el pedido; el vendedor se toma del usuario autenticado, no de un campo editable.

Además, el módulo permite listar, ver detalle, editar pedidos pendientes, cancelar pedidos y restaurar los cancelados.

| Estado | Acciones permitidas |
| --- | --- |
| `PENDING` | Editar, cancelar o generar venta. |
| `CANCELLED` | Restaurar. |
| `INVOICED` | Consultar el comprobante; no permite edición. |

### 3.7 Ventas y comprobantes

- Listado de ventas emitidas y anuladas.
- Desde un pedido pendiente se puede generar una BOLETA o FACTURA.
- El sistema bloquea FACTURA si el cliente no tiene RUC.
- El detalle del comprobante muestra cliente, vendedor, productos, serie, correlativo, subtotal, IGV y total.
- Puede imprimirse desde el navegador.
- Puede descargarse como PDF vectorial mediante jsPDF: texto seleccionable, menor peso y sin capturar una imagen de pantalla.
- Una venta puede anularse indicando el motivo; el comprobante queda anulado y no se reactiva.

## 4. Funcionalidades del backend

### 4.1 API REST

La API está bajo `/api/v1` y dispone de Swagger en `/swagger-ui.html`. Los controladores reciben las peticiones y delegan la lógica a servicios transaccionales.

| Recurso | Operaciones principales | Control de acceso |
| --- | --- | --- |
| Autenticación | `POST /auth/login` | Público. |
| Categorías | Listar, detalle, crear, editar, desactivar, restaurar | Lectura autenticada; cambios por ADMIN. |
| Productos | Listar, detalle, crear, editar, desactivar, restaurar | Lectura autenticada; cambios por ADMIN. |
| Clientes | Listar, buscar por documento, detalle, crear, editar, desactivar, restaurar | Lectura autenticada; alta y edición para operación comercial; baja/restauración por ADMIN. |
| Usuarios | Listar, detalle, crear, editar, desactivar, restaurar | ADMIN. |
| Pedidos | Listar, detalle, crear, editar, cancelar, restaurar | Autenticado para consulta; SELLER para operación. |
| Ventas | Listar, detalle, emitir y anular | Autenticado para consulta; SELLER para operación. |

### 4.2 Reglas de negocio

| Dominio | Reglas aplicadas por el backend |
| --- | --- |
| Categorías | Nombre obligatorio y único, sin importar mayúsculas/minúsculas. |
| Productos | Código único, categoría existente y activa, precio y stock no negativos. |
| Clientes | Documento único; DNI/RUC con longitud correcta; correo válido. |
| Usuarios | Usuario único; contraseña protegida con BCrypt; rol válido. |
| Pedidos | Cliente y vendedor activos; al menos un detalle; cantidades positivas; producto activo y con stock suficiente. |
| Ventas | Solo desde pedido pendiente; una venta vigente por pedido; FACTURA requiere RUC; revalida y descuenta stock; calcula IGV 18 %. |

### 4.3 Persistencia e integridad

- Spring Data JPA mapea las entidades a SQL Server.
- `schema.sql` es la fuente de verdad de la base de datos y Hibernate la valida al iniciar.
- Los catálogos usan baja lógica: el registro conserva su historial con estado activo/inactivo.
- Pedidos y ventas usan cabecera y detalle, por lo que cada producto queda registrado en una línea independiente.
- El precio unitario se guarda en el detalle; así no cambia el valor histórico de un pedido o venta si luego se modifica el catálogo.
- Las series de comprobantes se actualizan con bloqueo para evitar que dos ventas obtengan el mismo correlativo.

### 4.4 Manejo de errores

Un manejador global convierte errores de negocio en respuestas claras para la interfaz. Ejemplos: recurso inexistente, credenciales inválidas, falta de stock, estado de pedido no válido, documento inválido o registro inactivo. El interceptor del frontend transforma estas respuestas en notificaciones visibles.

## 5. Escenario recomendado para el video

1. **Introducción:** explicar el problema de controlar ventas, inventario y comprobantes en un solo sistema.
2. **Arquitectura:** mostrar las carpetas de frontend y backend, y resumir la comunicación Angular → API → SQL Server.
3. **Login y roles:** ingresar y explicar JWT, guardas y diferencias entre administrador y vendedor.
4. **Administración:** crear una categoría y un producto; resaltar validaciones y control de stock.
5. **Cliente:** buscar un documento y, si no existe, registrar el cliente.
6. **Pedido:** añadir productos, modificar cantidades y guardar un pedido pendiente.
7. **Venta:** convertir el pedido en boleta o factura; señalar IGV, descuento de stock y correlativo.
8. **Comprobante:** mostrar detalle, impresión o descarga PDF.
9. **Anulación:** explicar que repone stock, conserva trazabilidad y deja el comprobante anulado.
10. **Cierre:** abrir Swagger para evidenciar la API y destacar que las reglas críticas están protegidas en el backend.

## 6. Mensajes clave para la exposición

- “El sistema separa claramente presentación, reglas de negocio y datos.”
- “Las validaciones importantes no dependen solo de la pantalla: también se ejecutan en la API.”
- “La baja lógica conserva el historial de los catálogos.”
- “Cada venta mantiene el precio histórico, calcula el IGV y protege la numeración del comprobante.”
- “El flujo de anulación devuelve el stock y mantiene la trazabilidad comercial.”

## 7. Rutas para demostración local

| Servicio | Dirección |
| --- | --- |
| Frontend | `http://localhost:4200` |
| API | `http://localhost:8080` |
| Swagger | `http://localhost:8080/swagger-ui.html` |

Para ejecutar: primero prepare SQL Server con `vg-pedidos-ventas-be/database/schema.sql`; luego inicie el backend con `mvn spring-boot:run`; finalmente, en el frontend ejecute `npm install` y `npm start`.
