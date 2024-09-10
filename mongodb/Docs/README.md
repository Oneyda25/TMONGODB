
# Tienda de Películas - Base de Datos MongoDB

## Descripción del Proyecto

Este proyecto consiste en una base de datos para gestionar una tienda de películas, utilizando MongoDB como sistema de almacenamiento NoSQL. La base de datos incluye colecciones para manejar clientes, películas, empleados y ventas. Además, se incorporan consultas avanzadas, comandos DDL y DML, y subconsultas para proporcionar un manejo completo de los datos de la tienda.

### Colecciones principales:
- **Clientes**: Detalles de los clientes.
- **Películas**: Información sobre las películas disponibles.
- **Empleados**: Información de los empleados de la tienda.
- **Ventas**: Transacciones de ventas registradas.

## Requisitos Previos

1. Tener instalado **MongoDB** en tu máquina.
2. Tener acceso a **MongoDB Compass** o **MongoDB Shell** para interactuar con la base de datos.
3. Conocimientos básicos de MongoDB y JSON.

## Restaurar la Base de Datos

### Paso 1: Crear la base de datos

Ejecuta el siguiente comando en MongoDB Shell o Compass para crear la base de datos:

```bash
use TiendaPeliculas
```

### Paso 2: Insertar las colecciones

#### 2.1. Insertar clientes:

```js
db.clientes.insertMany([
  { "_id": 1, "nombre": "Juan Pérez", "email": "juan@example.com", "telefono": "1234567890" },
  { "_id": 2, "nombre": "María Gómez", "email": "maria@example.com", "telefono": "0987654321" },
  
])
```

#### 2.2. Insertar películas:

```js
db.peliculas.insertMany([
  { "_id": 1, "titulo": "Matrix", "genero": "Ciencia Ficción", "precio": 10.00 },
  { "_id": 2, "titulo": "El Padrino", "genero": "Drama", "precio": 8.50 },
  
])
```

#### 2.3. Insertar empleados:

```js
db.empleados.insertMany([
  { "_id": 1, "nombre": "Carlos Ruiz", "puesto": "Cajero" },
  { "_id": 2, "nombre": "Sofía Ramírez", "puesto": "Gerente" },
  { "_id": 3, "nombre": "Luis Torres", "puesto": "Vendedor" }
])
```

#### 2.4. Insertar ventas:

```js
db.ventas.insertMany([
  { "_id": 1, "cliente_id": 1, "pelicula_id": 2, "empleado_id": 1, "fecha": new Date("2023-09-01"), "total": 8.50 },
  { "_id": 2, "cliente_id": 2, "pelicula_id": 1, "empleado_id": 2, "fecha": new Date("2023-09-02"), "total": 10.00 },
  { "_id": 3, "cliente_id": 3, "pelicula_id": 3, "empleado_id": 1, "fecha": new Date("2023-09-03"), "total": 7.00 },
  { "_id": 4, "cliente_id": 4, "pelicula_id": 4, "empleado_id": 2, "fecha": new Date("2023-09-04"), "total": 9.00 },
  
])
```

## Consultas y Comandos

### DDL (Data Definition Language)

1. Crear una nueva colección `promociones`:

```js
db.createCollection("promociones")
```

2. Crear un índice en la colección `peliculas`:

```js
db.peliculas.createIndex({ "titulo": 1 })
```

3. Eliminar la colección `empleados`:

```js
db.empleados.drop()
```

### DML (Data Manipulation Language)

1. Insertar un nuevo cliente:

```js
db.clientes.insertOne({ "_id": 16, "nombre": "Carlos Mendoza", "email": "carlosm@example.com", "telefono": "9876543210" })
```

2. Actualizar el precio de una película:

```js
db.peliculas.updateOne({ "_id": 1 }, { $set: { "precio": 12.00 } })
```

3. Eliminar una venta:

```js
db.ventas.deleteOne({ "_id": 20 })
```

### Consultas avanzadas

1. Consultar todas las películas cuyo género sea `Ciencia Ficción` y cuyo precio sea mayor a 9.00:

```js
db.peliculas.find({ "genero": "Ciencia Ficción", "precio": { $gt: 9.00 } })
```

2. Contar cuántas ventas fueron realizadas por el empleado con ID 1:

```js
db.ventas.countDocuments({ "empleado_id": 1 })
```

3. Agrupar las ventas por `cliente_id` y calcular el total gastado por cada cliente:

```js
db.ventas.aggregate([
  { $group: { _id: "$cliente_id", total_gastado: { $sum: "$total" } } }
])
```

### Subconsultas

1. Listar los clientes que han comprado la película "Matrix":

```js
db.ventas.aggregate([
  { $lookup: {
    from: "peliculas",
    localField: "pelicula_id",
    foreignField: "_id",
    as: "detalles_pelicula"
  }},
  { $match: { "detalles_pelicula.titulo": "Matrix" }},
  { $lookup: {
    from: "clientes",
    localField: "cliente_id",
    foreignField: "_id",
    as: "detalles_cliente"
  }},
  { $project: { "detalles_cliente.nombre": 1, _id: 0 } }
])
```

2. Obtener el nombre de los empleados que han realizado ventas superiores a $9.00:

```js
db.ventas.aggregate([
  { $match: { "total": { $gt: 9.00 } }},
  { $lookup: {
    from: "empleados",
    localField: "empleado_id",
    foreignField: "_id",
    as: "detalles_empleado"
  }},
  { $project: { "detalles_empleado.nombre": 1, _id: 0 } }
])
```

3. Obtener todas las películas que han sido vendidas y mostrar los nombres de los clientes que las compraron:

```js
db.ventas.aggregate([
  { $lookup: {
    from: "peliculas",
    localField: "pelicula_id",
    foreignField: "_id",
    as: "detalles_pelicula"
  }},
  { $lookup: {
    from: "clientes",
    localField: "cliente_id",
    foreignField: "_id",
    as: "detalles_cliente"
  }},
  { $project: { "detalles_pelicula.titulo": 1, "detalles_cliente.nombre": 1, _id: 0 } }
])
```

## Instrucciones Relevantes

1. Asegúrate de tener MongoDB en ejecución y bien configurado.
2. Utiliza MongoDB Compass o MongoDB Shell para ejecutar las consultas y verificar los datos.
3. Sigue los ejemplos y ajusta los ID o valores si es necesario para personalizar la base de datos.


