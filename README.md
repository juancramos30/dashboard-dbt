# dashboard-dbt

Este repo contiene dos apps independientes, sin relación entre sí, cada una en su
propia carpeta:

```
playlist/index.html  # Playlist del Día (en vivo, Firebase)
dashboard/index.html # Operations Dashboard (pedidos DBT/SoM)
```

## Playlist del Día (`playlist/`)

Lista de canciones compartida en vivo: cualquiera con el link puede agregar o quitar
canciones y los cambios se sincronizan al instante para todos (Firebase Realtime Database).

Conectada al proyecto Firebase **coro-app-c-claretianos**
(`https://coro-app-c-claretianos-default-rtdb.firebaseio.com`).

**Importante:** la base se creó en "modo de prueba", cuyas reglas por defecto
**expiran a los 30 días** (deja de funcionar la app si no se cambian antes). Para
dejarlo permanente, ve a Firebase Console > Realtime Database > pestaña **Reglas**
y reemplaza el contenido por:

```json
{
  "rules": {
    "playlist": {
      ".read": true,
      ".write": true
    }
  }
}
```

Esto permite que cualquiera lea y escriba el playlist (sin login), tal como está
pensada la app. Si más adelante quieres restringir quién edita, cambia estas reglas.

## Operations Dashboard (`dashboard/`)

Dashboard de operaciones de pedidos para Del Bravo Trade & Specialties of Mexico,
alimentado en tiempo real desde Google Sheets (CSV publicado). Se actualiza
automáticamente cada 5 minutos.
