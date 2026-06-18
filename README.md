# dashboard-dbt

## Playlist del Día (`index.html`)

Lista de canciones compartida en vivo: cualquiera con el link puede agregar o quitar
canciones y los cambios se sincronizan al instante para todos (Firebase Realtime Database).

Para activarla:

1. Crea un proyecto gratuito en https://console.firebase.google.com.
2. Habilita **Realtime Database** (modo de prueba o reglas abiertas, ver abajo).
3. En *Project settings > General > Your apps*, registra una app web y copia el
   objeto de configuración.
4. Pega esos valores en `firebaseConfig` dentro de `index.html` (reemplaza los `TODO_*`).
5. En las reglas de Realtime Database, usa algo como:

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

## Operations Dashboard (`dashboard.html`)

Dashboard de operaciones de pedidos para Del Bravo Trade & Specialties of Mexico,
alimentado en tiempo real desde Google Sheets (CSV publicado). Se actualiza
automáticamente cada 5 minutos.
