# FullMenu

Aplicacion base construida sobre Next.js y el kit de componentes de Untitled UI. Este repositorio deja lista la estructura para un panel o dashboard con autenticacion, theming y acceso a base de datos via Prisma. El objetivo del README es que cualquier agente (humano o automatizado) pueda orientarse rapido sobre las piezas principales del sistema.

## Panorama rapido
- **Framework**: Next.js 15 (app router, TypeScript estricto y soporte para React 19).
- **UI**: Untitled UI React + Tailwind CSS 4; estilos globales en `src/styles`.
- **Estado global**: React Server Components + providers ligeros para router (`src/providers/router-provider.tsx`) y tema (`src/providers/theme.tsx`).
- **Autenticacion**: [`better-auth`](https://better-auth.dev/) con adaptador Prisma (`src/lib/auth.ts`) y cliente React (`src/lib/auth-client.ts`).
- **Base de datos**: Prisma Accelerate apuntando a PostgreSQL (`prisma/schema.prisma`, cliente en `src/lib/prisma.ts`).

## Estructura esencial
```
src/
+- app/             # Entrypoints del app router, layout global y endpoints API.
+- components/      # Componentes reutilizables provistos por Untitled UI.
+- generated/       # Prisma Client generado; no editar a mano.
+- hooks/, utils/   # Hooks y utilidades compartidas por la UI.
+- lib/             # Integraciones (auth, prisma) reutilizables en server actions.
+- providers/       # Providers cliente (router de React Aria, theming con next-themes).
```
- **`src/app/layout.tsx`** fija la fuente, estilos globales y envuelve con los providers mencionados.
- **`src/app/page.tsx`** es un placeholder; reemplazar por la pantalla principal que corresponda.
- **`src/app/api`** esta reservado para handlers route-based (`better-auth` suele generar endpoints aqui).

## Autenticacion y sesion
1. `src/lib/auth.ts` inicializa Better Auth con el adaptador Prisma y el proveedor `postgresql`.
2. `src/lib/prisma.ts` crea una unica instancia de Prisma Client extendida con `withAccelerate` para entornos serverless.
3. `src/lib/auth-client.ts` expone helpers `signIn`, `signUp`, `signOut` y `useSession` listos para usar en componentes client.
4. Las tablas de usuario/sesion/cuentas se describen en `prisma/schema.prisma` y se generan con `npx prisma generate`.

## Variables de entorno minimas
Configura un archivo `.env` (ya incluido en `.gitignore`). Revisar y establecer, como minimo:
- `DATABASE_URL` -> cadena de conexion a PostgreSQL.
- Variables de `better-auth` (por ejemplo `AUTH_SECRET`, `AUTH_URL` u otras segun el flujo que habilites).
- Cualquier clave adicional que necesiten integraciones externas futuras.

Tras definir los valores, ejecuta:
```
# Genera el cliente Prisma segun el schema
bunx prisma generate

# Opcional: sincroniza el esquema si todavia no hay migraciones
bunx prisma db push
```

## Scripts de uso cotidiano
- `bun install` (o `npm install`): instala dependencias.
- `bunx prisma generate`: regenera el cliente Prisma al modificar `schema.prisma`.
- `bunx prisma db push`: aplica el esquema actual a la base configurada.
- `bun run dev` (`npm run dev` / `pnpm dev` / `yarn dev`): levanta el servidor de desarrollo en `http://localhost:3000`.
- `bun run build` / `bun run start`: build y arranque en modo produccion.

## Flujo de desarrollo sugerido
1. Clonar el repo y copiar `.env.example` -> `.env` (si no existe, crear uno siguiendo la seccion anterior).
2. Instalar dependencias con el gestor preferido (el lockfile principal es `bun.lockb`).
3. Ejecutar `bunx prisma generate` para producir el cliente en `src/generated/prisma`.
4. Levantar `bun run dev` y construir la UI dentro de `src/app` y `src/components`.
5. Cuando se agreguen recursos protegidos, usar los helpers de `auth-client` para controlar sesiones en componentes cliente.

## Referencias utiles
- Documentacion Untitled UI React: <https://www.untitledui.com/react>
- Better Auth (adaptador Prisma + Next.js): <https://better-auth.dev/docs>
- Prisma Accelerate: <https://www.prisma.io/accelerate>

> Nota: este proyecto parte de un starter kit. Antes de desplegar a produccion, revisa permisos, claves y politicas de sesion segun tus necesidades reales.
