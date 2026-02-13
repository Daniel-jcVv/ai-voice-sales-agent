# Mi Tienda - API Falsa para Voice Agent

## Que es esto?

Una API falsa de una tienda de tecnologia usando **json-server**. Simula tener pedidos y productos para probar el voice agent de n8n sin necesitar una tienda real.

```
db.json  -->  json-server  -->  API en localhost:3000
(datos)       (la magia)        (endpoints listos)
```

## Archivos

| Archivo | Para que sirve |
|---------|---------------|
| `db.json` | La "base de datos" (orders + products) |
| `db-back.json` | Backup de db.json (json-server modifica db.json con POST/PUT/DELETE) |
| `package.json` | Dependencias y scripts del proyecto |
| `.gitignore` | Archivos que Git debe ignorar |

## Requisitos

- Node.js instalado (`node --version`)
- npm instalado (`npm --version`)
- ngrok instalado (`ngrok version`)

## Pasos para levantar el servidor

### 1. Instalar dependencias

```bash
cd voice_agent/mi-tienda
npm install
```

### 2. Arrancar json-server

```bash
npm start
```

Deberias ver:

```
JSON Server started on PORT :3000
Endpoints:
  http://localhost:3000/orders
  http://localhost:3000/products
```

### 3. Probar que funciona

En otra terminal (sin cerrar la anterior):

```bash
curl http://localhost:3000/orders
```

O en Postman: `GET http://localhost:3000/orders`

**Nota Postman:** NO escribas "GET" en la barra de URL, solo la URL. El metodo se selecciona en el dropdown azul.

## Exponer con ngrok (para que n8n acceda)

localhost solo funciona en tu PC. ngrok crea un tunel publico:

```
localhost:3000  -->  ngrok  -->  https://algo-random.ngrok-free.dev
(solo tu PC)        (tunel)     (accesible desde internet)
```

### 1. Configurar authtoken (solo la primera vez)

```bash
ngrok config add-authtoken TU_TOKEN_AQUI
```

El token se obtiene en tu cuenta de ngrok.io

### 2. Crear el tunel

```bash
ngrok http 3000
```

### 3. Obtener la URL publica

ngrok muestra la URL en "Forwarding", pero a veces se corta. Para verla completa:

- Abre http://127.0.0.1:4040 en tu navegador (panel de ngrok)
- O ejecuta: `curl http://127.0.0.1:4040/api/tunnels`

### 4. Usar la URL publica

Ejemplo: `https://tu-url-random.ngrok-free.dev/orders`

Esa URL es la que se usa en n8n para conectar el voice agent con esta API.

## Endpoints disponibles

| Metodo | URL | Que hace |
|--------|-----|----------|
| GET | /orders | Todos los pedidos (20) |
| GET | /orders/1001-2024 | Un pedido especifico |
| GET | /products | Todos los productos (30) |

## Nota sobre ngrok free

En plan gratuito, ngrok muestra una pagina de advertencia al abrir en navegador. Para saltarla desde codigo, agrega el header:

```
ngrok-skip-browser-warning: true
```
