# Guion oral para video de presentación

**Duración estimada:** 10 a 12 minutos.  
**Estilo recomendado:** hablar despacio, usar frases cortas y evitar leer nombres técnicos en inglés si no se van a explicar. Cada bloque indica qué mostrar y qué decir.

---

## 1. Saludo e introducción — 0:00 a 0:45

### Mostrar

- La pantalla principal del sistema o el archivo `GUIA_PRESENTACION_FUNCIONALIDADES.md`.

### Decir exactamente

> Buenos días. Mi nombre es [tu nombre] y en esta presentación voy a explicar el Sistema de Gestión de Pedidos y Ventas de Productos. Este proyecto fue desarrollado para organizar el proceso comercial de una tienda, desde el registro de los productos y clientes hasta la creación de pedidos, la emisión de ventas y la generación de comprobantes.
>
> El objetivo principal es reducir errores manuales, controlar el stock de los productos y mantener un historial claro de cada operación realizada.

---

## 2. Arquitectura general — 0:45 a 1:40

### Mostrar

- Explorador de archivos: carpeta `SistemaPedidos`.
- Las carpetas `vg-pedidos-ventas-fe` y `vg-pedidos-ventas-be`.
- Archivo `DOCUMENTACION_TECNICA.md` o la primera sección de `GUIA_PRESENTACION_FUNCIONALIDADES.md`.

### Decir exactamente

> El proyecto está dividido en dos partes principales. La primera es el frontend, ubicado en la carpeta `vg-pedidos-ventas-fe`. Esta parte representa la interfaz que utiliza el usuario en el navegador.
>
> La segunda es el backend, ubicado en la carpeta `vg-pedidos-ventas-be`. Esta parte contiene la lógica del negocio, la seguridad y la comunicación con la base de datos.
>
> El frontend fue desarrollado con Angular y TypeScript. El backend fue desarrollado con Java y Spring Boot. Ambos se comunican mediante una API REST, y la información se almacena en Microsoft SQL Server.
>
> En resumen, Angular muestra las pantallas, Spring Boot valida las reglas importantes y SQL Server guarda los datos de manera permanente.

---

## 3. Tecnologías y plataformas — 1:40 a 2:20

### Mostrar

- `vg-pedidos-ventas-fe/package.json`.
- `vg-pedidos-ventas-be/pom.xml`.
- Opcionalmente, SQL Server Management Studio con la base de datos abierta.

### Decir exactamente

> Para el frontend se utilizó Angular 19, TypeScript, Tailwind CSS y jsPDF. Angular permite construir una aplicación web por módulos; Tailwind ayuda con los estilos visuales; y jsPDF permite generar comprobantes descargables en formato PDF.
>
> Para el backend se utilizó Java 17, Spring Boot, Spring Data JPA y Spring Security. Spring Boot permite construir la API; JPA facilita la conexión con la base de datos; y Spring Security protege las rutas mediante autenticación con token JWT.
>
> Finalmente, usamos Microsoft SQL Server como motor de base de datos para registrar usuarios, productos, clientes, pedidos, ventas y comprobantes.

> Además de poder ejecutarse de manera local, el proyecto se encuentra desplegado en la nube dentro de un servidor virtual privado, también llamado VPS. La versión pública se puede acceder desde la dirección comercial-bg.ddns.net. Esto permite demostrar que el sistema no solo funciona en un equipo de desarrollo, sino también en un entorno accesible por internet.

---

## 4. Inicio de sesión y seguridad — 2:20 a 3:15

### Mostrar

- La pantalla de login en `http://localhost:4200`.
- Ruta de demostración: `/login`.
- En código: `vg-pedidos-ventas-fe/src/app/core/services/auth.service.ts`.
- En código: `vg-pedidos-ventas-be/.../rest/AuthRest.java`.
- Opcional: `auth.interceptor.ts` y `SecurityConfig.java`.

### Decir exactamente

> La primera funcionalidad es el inicio de sesión. El usuario ingresa sus credenciales y el frontend envía la solicitud al backend.
>
> El backend valida las credenciales y, si son correctas, genera un token JWT. Este token representa la sesión del usuario y se envía en las solicitudes posteriores para verificar que el usuario esté autorizado.
>
> En el frontend, el servicio de autenticación conserva la sesión y el interceptor agrega automáticamente el token a cada solicitud protegida. En el backend, Spring Security valida ese token antes de permitir el acceso a las funcionalidades.
>
> El sistema maneja dos roles: administrador y vendedor. El administrador puede administrar catálogos y usuarios. El vendedor se enfoca en clientes, pedidos y ventas.

---

## 5. Gestión de categorías y productos — 3:15 a 4:30

### Mostrar

- En la aplicación: módulo Categorías y módulo Productos.
- Rutas de demostración: `/categories` y `/products`.
- Para mostrar formularios: `/categories/new`, `/products/new`, `/products/edit/:id` y `/products/:id`.
- Crear o editar un producto.
- En código: `modules/category/` y `modules/product/` del frontend.
- En código: `CategoryRest.java`, `ProductRest.java` y los servicios correspondientes del backend.

### Decir exactamente

> Ahora voy a mostrar la gestión de categorías y productos. Estas funcionalidades permiten mantener actualizado el catálogo que se utilizará durante los pedidos y las ventas.
>
> En categorías, el administrador puede registrar, editar, desactivar y restaurar categorías. En productos, puede registrar el código, nombre, categoría, precio y stock disponible.
>
> El sistema valida que el precio y el stock no sean negativos. Además, verifica que el producto esté relacionado con una categoría existente y activa.
>
> Es importante mencionar que la eliminación es lógica. Esto significa que, en lugar de borrar un producto o una categoría de forma definitiva, el sistema cambia su estado a inactivo. De esta forma, se conserva el historial de las operaciones anteriores.
>
> En el backend, los controladores reciben la solicitud y los servicios aplican las validaciones antes de guardar la información en SQL Server.

---

## 6. Gestión de clientes y usuarios — 4:30 a 5:35

### Mostrar

- En la aplicación: módulo Clientes.
- Ruta de demostración: `/clients`.
- Para registrar o editar: `/clients/new` y `/clients/edit/:id`.
- Registrar o buscar un cliente por documento.
- En la aplicación: módulo Usuarios, usando perfil administrador.
- Ruta de demostración: `/users`; para crear un usuario, `/users/new`.
- Archivos: `ClientRest.java`, `UserRest.java`, `DocumentValidator.java` y `CredentialGenerator.java`.

### Decir exactamente

> La siguiente funcionalidad es la gestión de clientes. El sistema permite registrar, editar, consultar y buscar clientes por su documento de identidad.
>
> Para mejorar la calidad de los datos, se valida que el DNI tenga ocho dígitos y que el RUC tenga once dígitos. También se valida el formato del correo electrónico.
>
> Esta búsqueda por documento es muy útil durante la creación de un pedido, porque permite encontrar rápidamente a un cliente o registrarlo sin salir del proceso comercial.
>
> También existe un módulo de usuarios, exclusivo para el administrador. Desde allí se crean usuarios con rol de administrador o vendedor. Las contraseñas se almacenan de forma segura mediante cifrado BCrypt, por lo que no se guardan como texto visible en la base de datos.

---

## 7. Creación y gestión de pedidos — 5:35 a 7:05

### Mostrar

- En la aplicación: módulo Pedidos y botón Nuevo pedido.
- Ruta de demostración: `/orders`; para el formulario, `/orders/new`.
- Para editar un pedido pendiente: `/orders/edit/:id`.
- Buscar cliente, agregar productos, cambiar cantidades y guardar.
- En código: `modules/order/components/order-form/` y `OrderRest.java`.
- Opcional: `OrderServiceImpl.java`.

### Decir exactamente

> A continuación, voy a demostrar el flujo principal del sistema: la creación de un pedido.
>
> Primero, el vendedor busca al cliente mediante su DNI o RUC. Si el cliente no existe, puede registrarlo desde el mismo formulario. Después, selecciona los productos disponibles y los agrega al carrito.
>
> El sistema muestra las cantidades, subtotales y el total del pedido. Cuando se guarda, el backend verifica nuevamente que el cliente y los productos estén activos, que las cantidades sean mayores que cero y que exista stock suficiente.
>
> El pedido se guarda inicialmente con el estado pendiente, identificado como PENDING. Mientras se encuentre pendiente, se puede editar, cancelar o convertir en una venta.
>
> Si un pedido se cancela, pasa al estado CANCELLED. Si luego se necesita recuperarlo, puede restaurarse y volver al estado pendiente. Cuando ya se emitió una venta, el pedido pasa al estado INVOICED y deja de ser editable.

---

## 8. Emisión de venta y comprobante — 7:05 a 8:40

### Mostrar

- Desde un pedido pendiente, usar Generar venta.
- Vista inicial: `/orders`; listado de ventas: `/sales`; comprobante: `/sales/:id`.
- Elegir boleta o factura.
- Abrir el comprobante resultante.
- Descargar PDF o usar imprimir.
- Archivos: `SaleRest.java`, `SaleServiceImpl.java`, `TaxCalculator.java` y `sale-receipt.component.ts`.

### Decir exactamente

> Desde un pedido pendiente, el vendedor puede generar una venta. En este paso, el sistema permite elegir el tipo de comprobante, que puede ser boleta o factura.
>
> Si se selecciona factura, el sistema exige que el cliente tenga un RUC válido. Esta regla se valida en el backend para proteger la integridad de la operación.
>
> Al emitir la venta, el backend vuelve a comprobar el stock disponible. Luego descuenta las unidades vendidas, calcula el impuesto general a las ventas, que corresponde al dieciocho por ciento, y genera una serie con su correlativo.
>
> El comprobante muestra el cliente, el vendedor, los productos, los precios, el subtotal, el IGV y el total. Desde la interfaz, el usuario puede imprimirlo o descargarlo como PDF.
>
> El PDF se genera de forma vectorial con jsPDF. Esto permite que el texto sea nítido, seleccionable y más ligero que una imagen capturada de la pantalla.

---

## 9. Anulación y trazabilidad — 8:40 a 9:30

### Mostrar

- Anular una venta de prueba.
- Ruta de demostración: `/sales` y luego `/sales/:id` para abrir el comprobante.
- Ver que cambia el estado de la venta y del pedido.
- Archivo `SaleServiceImpl.java` y, si es posible, la tabla de ventas en SQL Server.

### Decir exactamente

> El sistema también contempla la anulación de una venta. Cuando una venta se anula, el stock de los productos se repone y el pedido vuelve al estado pendiente, para que se pueda emitir nuevamente una venta si corresponde.
>
> Sin embargo, el comprobante anulado no se elimina ni se reactiva. Se conserva con su estado de anulación para mantener la trazabilidad y respetar el historial comercial.
>
> Además, el sistema guarda el precio de cada producto dentro del detalle del pedido y de la venta. Esto significa que, aunque el precio del catálogo cambie después, las operaciones anteriores mantienen su valor histórico correcto.

---

## 10. API, base de datos y cierre — 9:30 a 10:45

### Mostrar

- `http://localhost:8080/swagger-ui.html`.
- Archivo `vg-pedidos-ventas-be/database/schema.sql`.
- Archivo `application.yaml` sin mostrar valores sensibles.
- Opcional: SQL Server Management Studio con tablas como `products`, `orders`, `sales` y sus detalles.

### Decir exactamente

> Para finalizar, voy a mostrar Swagger, que es la documentación interactiva de la API. Aquí se pueden visualizar y probar los endpoints de autenticación, categorías, productos, clientes, usuarios, pedidos y ventas.
>
> La base de datos está definida en el archivo `schema.sql`. Allí se encuentran las tablas, relaciones, restricciones y datos iniciales necesarios para el sistema.
>
> El proyecto también está desplegado en la nube dentro de un VPS. La dirección pública es comercial-bg.ddns.net. Este despliegue permite acceder al sistema desde internet y demuestra que la aplicación fue preparada para funcionar fuera del entorno local.
>
> Para ejecutar el backend de manera local, es necesario configurar las variables de entorno que utiliza el archivo `application.yaml`. Estas variables incluyen la conexión a la base de datos, el puerto del servidor y la clave de seguridad del token. Podemos usar el archivo `.env.example` como referencia y crear un archivo `.env` local, sin incluirlo en el repositorio. De esta manera, los datos sensibles no quedan escritos directamente en el código.
>
> Un aspecto importante es que la lógica crítica se encuentra en el backend. Por ejemplo, la validación de stock, el cálculo de impuestos, el control de roles y la generación de correlativos no dependen únicamente de la pantalla. Esto mejora la seguridad y evita que una persona pueda saltarse las reglas modificando el navegador.
>
> En conclusión, este proyecto integra una interfaz moderna, una API segura y una base de datos estructurada para controlar el proceso completo de pedidos y ventas. Muchas gracias.

---

## Recomendaciones de exposición

- Habla en primera persona plural: “el sistema valida”, “hemos implementado”, “podemos observar”.
- No leas el código línea por línea. Abre el archivo, señala su propósito y explica el flujo.
- Antes de grabar, prepara datos de prueba: al menos una categoría, dos productos con stock y un cliente.
- No muestres contraseñas, tokens JWT, valores de `JWT_SECRET` ni datos reales de personas.
- Si muestras `application.yaml`, explica que las variables como `DB_URL`, `DB_USERNAME`, `DB_PASSWORD` y `JWT_SECRET` se obtienen desde el entorno; no muestres sus valores reales.
- Al mencionar el despliegue, di la dirección lentamente: “comercial guion be ge punto de de ene ese punto net”.
- Si una función tarda en cargar, explica qué está ocurriendo: “en este momento el frontend está solicitando los datos a la API”.
- Para una presentación más corta, conserva las secciones 1, 2, 4, 7, 8 y 10.
