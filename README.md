# **MongoDB Query Examples - Restaurants Dataset**

Este documento contiene ejemplos de consultas MongoDB para trabajar con la colección `restaurants`. Cada consulta aborda un caso de uso específico para filtrar, ordenar y formatear los datos.

---

## **Consultas Básicas**

1. **Listar todos los documentos de la colección:**
   ```javascript
   db.restaurants.find();
   ```

2. **Seleccionar campos específicos (`restaurant_id`, `name`, `borough`, `cuisine`):**
   ```javascript
   db.restaurants.find({}, {restaurant_id: 1, name: 1, borough: 1, cuisine: 1});
   ```

3. **Excluir el campo `_id` en los resultados:**
   ```javascript
   db.restaurants.find({}, {restaurant_id: 1, name: 1, borough: 1, cuisine: 1, _id: 0});
   ```

4. **Incluir el código postal (`address.zipcode`) en los resultados:**
   ```javascript
   db.restaurants.find({}, {restaurant_id: 1, name: 1, borough: 1, cuisine: 1, "address.zipcode": 1, _id: 0});
   ```

---

## **Consultas con Filtros**

5. **Restaurantes en el distrito "Bronx":**
   ```javascript
   db.restaurants.find({borough: "Bronx"});
   ```

6. **Limitar a 5 restaurantes en el Bronx:**
   ```javascript
   db.restaurants.find({borough: "Bronx"}).limit(5);
   ```

7. **Omitir los primeros 5 y devolver los siguientes 5 restaurantes en el Bronx:**
   ```javascript
   db.restaurants.find({borough: "Bronx"}).skip(5).limit(5);
   ```

8. **Restaurantes con alguna calificación mayor a 90:**
   ```javascript
   db.restaurants.find({"grades.score": {$gt: 90}});
   ```

9. **Restaurantes con calificaciones entre 80 y 100:**
   ```javascript
   db.restaurants.find({grades: {$elemMatch: {score: {$gt: 80, $lt: 100}}}});
   ```

10. **Restaurantes con coordenadas en longitud mayor a -95.754168:**
    ```javascript
    db.restaurants.find({"address.coord.0": {$gt: -95.754168}});
    ```

11. **Restaurantes no americanos con calificaciones mayores a 70 y longitud menor a -65.754168:**
    ```javascript
    db.restaurants.find({cuisine: {$ne: "American"}, "grades.score": {$gt: 70}, "address.coord.0": {$lt: -65.754168}});
    ```

12. **Restaurantes no americanos con calificación "A" fuera de Brooklyn:**
    ```javascript
    db.restaurants.find({cuisine: {$ne: "American"}, "grades.grade": "A", borough: {$ne: "Brooklyn"}});
    ```

---

## **Búsquedas por Nombre**

13. **Restaurantes cuyo nombre empieza con "Wil":**
    ```javascript
    db.restaurants.find({name: {$regex: /^Wil/}}, {restaurant_id: 1, name: 1, borough: 1, cuisine: 1});
    ```

14. **Restaurantes cuyo nombre termina con "ces":**
    ```javascript
    db.restaurants.find({name: {$regex: /ces$/}}, {restaurant_id: 1, name: 1, borough: 1, cuisine: 1});
    ```

15. **Restaurantes cuyo nombre contiene "Reg":**
    ```javascript
    db.restaurants.find({name: {$regex: /Reg/}}, {restaurant_id: 1, name: 1, borough: 1, cuisine: 1});
    ```

---

## **Consultas Combinadas**

16. **Restaurantes en el Bronx que sirven cocina americana o china:**
    ```javascript
    db.restaurants.find({borough: "Bronx", $or: [{cuisine: "American"}, {cuisine: "Chinese"}]});
    ```

17. **Restaurantes en Staten Island, Queens, Bronx o Brooklyn:**
    ```javascript
    db.restaurants.find({borough: {$in: ["Staten Island", "Queens", "Bronx", "Brooklyn"]}}, {restaurant_id: 1, name: 1, borough: 1, cuisine: 1});
    ```

18. **Restaurantes fuera de Staten Island, Queens, Bronx y Brooklyn:**
    ```javascript
    db.restaurants.find({borough: {$nin: ["Staten Island", "Queens", "Bronx", "Brooklyn"]}}, {restaurant_id: 1, name: 1, borough: 1, cuisine: 1});
    ```

19. **Restaurantes con calificaciones menores a 10:**
    ```javascript
    db.restaurants.find({"grades.score": {$lt: 10}}, {restaurant_id: 1, name: 1, borough: 1, cuisine: 1});
    ```

20. **Restaurantes que no son americanos ni chinos, o cuyo nombre empieza con "Wil":**
    ```javascript
    db.restaurants.find({$or: [{cuisine: {$nin: ["American ", "Chinese"]}}, {name: {$regex: /^Wil/}}]}, {_id: 0, restaurant_id: 1, name: 1, borough: 1, cuisine: 1});
    ```

---

## **Consultas Avanzadas**

21. **Restaurantes con calificación "A", puntuación 11 y fecha exacta:**
    ```javascript
    db.restaurants.find({"grades.grade": "A", "grades.score": 11, "grades.date": ISODate("2014-08-11T00:00:00Z")}, {_id: 0, restaurant_id: 1, name: 1, score: 1, cuisine: 1});
    ```

22. **Restaurantes donde la segunda calificación es "A" con puntuación 9 y fecha específica:**
    ```javascript
    db.restaurants.find({"grades.1.grade": "A", "grades.1.score": 9, "grades.1.date": ISODate("2014-08-11T00:00:00Z")}, {_id: 0, restaurant_id: 1, name: 1, score: 1, cuisine: 1});
    ```

23. **Restaurantes con coordenadas de latitud entre 42 y 52:**
    ```javascript
    db.restaurants.find({"address.coord.1": {$gt: 42, $lt: 52}}, {_id: 0, restaurant_id: 1, name: 1, address: 1});
    ```

---

## **Ordenamiento y Tipos**

24. **Ordenar restaurantes por nombre ascendente:**
    ```javascript
    db.restaurants.find({}).sort({name: 1});
    ```

25. **Ordenar restaurantes por nombre descendente:**
    ```javascript
    db.restaurants.find({}).sort({name: -1});
    ```

26. **Ordenar por distrito ascendente y tipo de cocina descendente:**
    ```javascript
    db.restaurants.find({}).sort({borough: 1, cuisine: -1});
    ```

27. **Restaurantes sin calle registrada:**
    ```javascript
    db.restaurants.find({"address.street": null}, {address: 1});
    ```

28. **Restaurantes con coordenadas de tipo `double`:**
    ```javascript
    db.restaurants.find({"address.coord": {$type: "double"}});
    ```

---

## **Filtros Especiales**

29. **Restaurantes con puntuaciones múltiplos de 7:**
    ```javascript
    db.restaurants.find({"grades.score": {$mod: [7, 0]}}, {restaurant_id: 1, name: 1, grades: 1, _id: 0});
    ```

30. **Restaurantes cuyo nombre contiene "mon":**
    ```javascript
    db.restaurants.find({name: {$regex: /mon/}}, {restaurant_id: 1, name: 1, borough: 1, "address.coord": 1, cuisine: 1, _id: 0});
    ```

31. **Restaurantes cuyo nombre empieza con "Mad":**
    ```javascript
    db.restaurants.find({name: {$regex: /^Mad/}}, {restaurant_id: 1, name: 1, borough: 1, "address.coord": 1, cuisine: 1, _id: 0});
    ```

