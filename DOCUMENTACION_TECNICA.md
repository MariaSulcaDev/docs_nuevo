# Documentación técnica — Sistema de Pedidos y Ventas

## Propósito

Sistema web para administrar categorías, productos, clientes y usuarios, y para gestionar el ciclo comercial **pedido → venta → comprobante**.

## Arquitectura

```text
Angular 19 (SPA, puerto 4200)
        │ HTTP + JWT
        ▼
Spring Boot 3 / Java 17 (API REST, puerto 8080)
        │ Spring Data JPA
        ▼
Microsoft SQL Server
```

El repositorio se organiza en dos aplicaciones independientes:

| Directorio | Tecnología | Responsabilidad |
| --- | --- | --- |
| `vg-pedidos-ventas-fe` | Angular 19, TypeScript, Tailwind CSS | Interfaz de usuario, formularios, guardas, interceptores y comprobantes PDF. |
| `vg-pedidos-ventas-be` | Java 17, Spring Boot 3, Maven | API REST, reglas de negocio, seguridad JWT y persistencia. |

## Backend

La API sigue el flujo `REST → Service → Repository → SQL Server`. Los controladores reciben solicitudes HTTP; los servicios concentran las validaciones y las transacciones; los repositorios usan Spring Data JPA.

- Base URL: `http://localhost:8080/api/v1`
- Autenticación: JWT Bearer.
- Documentación interactiva: `http://localhost:8080/swagger-ui.html`.
- Esquema: `vg-pedidos-ventas-be/database/schema.sql`.
- Configuración: variables `DB_URL`, `DB_USERNAME`, `DB_PASSWORD`, `JWT_SECRET` y `SERVER_PORT`.

Los recursos principales son autenticación, categorías, productos, clientes, usuarios, pedidos y ventas. Las operaciones de negocio verifican existencia y estado de los registros, stock disponible, reglas de DNI/RUC y permisos por rol.

## Flujo comercial

1. Un vendedor registra un pedido con cliente y productos.
2. El backend valida productos activos y stock suficiente.
3. Al emitir la venta, descuenta el stock, calcula IGV al 18 % y asigna una serie y correlativo.
4. El pedido pasa de `PENDING` a `INVOICED`.
5. Si la venta se anula, se repone el stock y el pedido vuelve a `PENDING`.

## Frontend

La SPA usa módulos por dominio con carga diferida. `core/` contiene autenticación, guardas e interceptores; `shared/` reúne componentes, pipes y modelos; `modules/` aloja las pantallas de cada dominio.

- Inicio local: `npm install` y `npm start`.
- URL local: `http://localhost:4200`.
- El interceptor adjunta el token JWT a las solicitudes.
- Las guardas controlan acceso por sesión y rol; el backend aplica la autorización definitiva.

## Puesta en marcha

1. Ejecute `database/schema.sql` en SQL Server.
2. En el backend, configure las variables de entorno según `.env.example` y ejecute `mvn spring-boot:run`.
3. En el frontend, ejecute `npm install` y `npm start`.
4. Abra `http://localhost:4200` e inicie sesión.

## Referencias

- La documentación detallada del backend está en `vg-pedidos-ventas-be/README.md`.
- La documentación detallada del frontend está en `vg-pedidos-ventas-fe/README.md`.
