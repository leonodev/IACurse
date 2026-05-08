# Spec

## Role
  Actua como un arquitecto de IOS Experto

## Task
  Genera una especificacion de como seria una arquitectura modular(ONION ARQUITECTURE), no generes codigo solo dame las especificaciones a nivel de arquitectura que deberiamos cumplir para ser una app escalable y mantenible y que cumpla con las buenas practicas de Clean Arquitecture y Solid. todo esto para Swift 6 con Concurrencia Estricta

## Context

Sera para una app de Delivery, es decir tendra modulos como Login, Registro, Home, Tarjetas de Regalo, Pagos con Bizum, Solicitud de tarjetas de credito, Pedidos, Entregas, y cualquier otro modulo que se te ocurra y que creas que deba contener una app de este tipo.
La app tedra que implementar Crashlytics, RemoteConfig de Firebase, y todo el Backen estar a cargo de Supabase.


## Specification template

quiero que esta especificacion me la crees en un fichero delivery.spec.md
- Donde menciones que arquitectura debemos usar, y el porque.
- Porque esta es la mejor opcion de cara a que sea una app super mantenible y escalable
- cuales serian los criterios de aceptacion para poder aplicar esta arquitectura al interior del equipo de desarrollo.


## Step to follow

- deberias tener en cuenta que la app es una app que sera desarrollado por modulos, y cada modulo sera configurado con Swift Package Manager.
- deberias analizar si para cada modulo sera mejor o no tener su propio repositorio, y que a lo mejor teniendo tag o versionados de cada modulo sea mas escalable
- deberas tener en cuenta que es posible que al crear un feature tengamos que hacer 2 o mas PR`s porque hemos tenido que tocar modulos distintos
- deberias listar tambien si deberiamos tener modulos como el DesignSystem, Todo lo que es Core, y que funcionalidad deberia entrar en este modulo de Core, tambien la capa de Red que sera la encargada de gestionar peticiones hacia Supabase, podria existir otro funcionalidad de Utils para itilerias como Helpers, Extension etc.
- deberia analizar el poder tener una funcionalidad de almacenamiento tanto interno con Realm, como en KeyChain de la App para datos sencibles.
- Deberias tener en cuenta que queremos usar un mecanismo de Inyeccion de dependencias Nativo, basado en properties Wrapper, y este debe ser muy facil de usar desde cualquier punto y debe ser capaz de poder usar en test unitarios para mockear una instancia.
- IMPORTANTE que analices todos estos matices y que los apliques a una arquitectura por capas como puede ser la ONION ARQUITECTURE
- Deberia tener al menos 10 criterios de aceptacion en formato EARS.

## Output Checklist

 - el fichero delivery.spec.md
 - las especificaciones con:
    - porque usar esta arquitectuta, que ganamos?
    - que soluciona esta arquitectura.
    - como quedarian los modulos y que deberia ir dentro de cada modulo.
    - Los criterios de aceptacion para cumplir con esta arquitectura.