## adding support for new language

It's simple for anyone to add support for their native language to the plugin. If your desired language isn’t available yet, you can easily contribute by following these steps: 


### Steps to Add a New Language:

- navigate to the resources/lang/ directory. This is where all language files are stored.
- Create a New JSON Language File for the lang like ``es.json``
In the newly created JSON file, provide translations for the following required strings:
```json
  {
  "Please enter a search term to get started.": "Por favor, ingrese un término de búsqueda para comenzar.",
  "to select": "para seleccionar",
  "to navigate": "para navegar",
  "to close": "para cerrar",
  "Search for anything ...": "Buscar cualquier cosa ...",
  "favorite this item": "Agregar este elemento a favoritos",
  "delete": "eliminar",
  "recent": "reciente",
  "favorites": "favoritos"
}
```