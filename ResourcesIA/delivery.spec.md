# Especificación de Arquitectura para App de Delivery

## Arquitectura Recomendada: Onion Architecture (Arquitectura Cebolla)

### Por qué usar esta arquitectura
La Onion Architecture es ideal para aplicaciones iOS desarrolladas con Swift 6 y concurrencia estricta porque promueve la separación de responsabilidades, facilita el testing y mejora la mantenibilidad. Esta arquitectura se basa en capas concéntricas donde las dependencias apuntan hacia adentro, lo que significa que las capas externas (como la UI o infraestructura) dependen de las capas internas (como el dominio), pero no al revés. Esto permite cambios en la UI o en el backend sin afectar la lógica de negocio.

### Beneficios para mantenibilidad y escalabilidad
- **Mantenibilidad**: Al separar claramente las responsabilidades, es más fácil modificar una capa sin afectar a las demás. Por ejemplo, cambiar la implementación de persistencia (de Realm a Core Data) no requiere cambios en la lógica de negocio.
- **Escalabilidad**: Facilita la adición de nuevos módulos y features. Cada módulo puede ser desarrollado independientemente usando Swift Package Manager (SPM), permitiendo equipos paralelos y despliegues incrementales.
- **Testing**: La inyección de dependencias nativa basada en property wrappers permite mockear fácilmente las dependencias en tests unitarios, mejorando la cobertura y la fiabilidad.
- **Concurrencia Estricta**: Swift 6 enfatiza la concurrencia, y esta arquitectura permite manejar operaciones asíncronas de manera estructurada, evitando race conditions y mejorando el rendimiento.
- **Buenas Prácticas**: Cumple con Clean Architecture y SOLID, asegurando que el código sea modular, testable y extensible.

### Qué soluciona esta arquitectura
- **Acoplamiento Bajo**: Reduce dependencias entre módulos, facilitando refactorizaciones.
- **Inyección de Dependencias**: Un mecanismo nativo basado en property wrappers hace que sea fácil inyectar dependencias en cualquier punto de la app, incluyendo tests.
- **Gestión de Estado**: Separa la lógica de negocio del estado de la UI, mejorando la predictibilidad.
- **Integración con Servicios Externos**: Facilita la integración con Firebase (Crashlytics, RemoteConfig) y Supabase, encapsulando estas dependencias en capas externas.
- **Módulos Independientes**: Permite versionar y etiquetar módulos, soportando múltiples PRs para features complejas.

## Estructura de Módulos

La app se dividirá en módulos usando Swift Package Manager (SPM). Cada módulo será un paquete independiente, lo que permite desarrollo paralelo y reutilización.

### Módulos Principales
1. **Core**: Contiene funcionalidades compartidas como helpers, extensions, utilities y la capa de red para Supabase. También incluye el sistema de inyección de dependencias.
   - **Funcionalidades**: 
     - Helpers para formateo, validación.
     - Extensions para tipos estándar (String, Date, etc.).
     - Capa de Red: Gestión de peticiones HTTP a Supabase, incluyendo autenticación y manejo de errores.
     - Inyección de Dependencias: Property wrappers para inyectar servicios.

2. **DesignSystem**: Biblioteca de componentes UI reutilizables, como botones, tarjetas, colores y tipografías. Asegura consistencia visual en toda la app.

3. **Domain**: Lógica de negocio pura, sin dependencias externas. Incluye entidades, casos de uso y reglas de negocio.
   - **Funcionalidades**: Modelos de datos (User, Order, Payment), casos de uso (Login, PlaceOrder), validaciones.

4. **Data**: Capa de persistencia y acceso a datos.
   - **Funcionalidades**: 
     - Almacenamiento local con Realm para datos no sensibles.
     - Keychain para datos sensibles (tokens, credenciales).
     - Integración con Supabase para datos remotos.

5. **Presentation**: Capa de UI, dependiente de Domain y Data.
   - **Funcionalidades**: ViewModels, Views (SwiftUI), navegación.

6. **Features**: Módulos específicos por feature, como Login, Registro, Home, Tarjetas de Regalo, Pagos con Bizum, Solicitud de Tarjetas de Crédito, Pedidos, Entregas.
   - Cada feature puede depender de Core, Domain, Data y Presentation.

### Análisis de Repositorios
- **Módulos Compartidos (Core, DesignSystem, Domain)**: Deben tener su propio repositorio para versionado independiente. Esto permite releases separados y facilita la reutilización en otras apps.
- **Módulos de Features**: Pueden estar en el mismo repositorio principal si son específicos de la app, pero para escalabilidad, considerar repositorios separados con tags/versiones. Esto soporta múltiples PRs: una para el módulo Core si se modifica, otra para el feature específico.
- **Ventajas**: Versionado permite rollback fácil y despliegues graduales. Múltiples PRs evitan conflictos masivos en merges.

### Inyección de Dependencias
- Usar property wrappers nativos de Swift para inyección. Ejemplo: `@Injected var service: ServiceProtocol`.
- Debe ser fácil de usar en cualquier punto y permitir mocking en tests unitarios.
- Implementación en el módulo Core, accesible desde todos los módulos.

### Integraciones Externas
- **Firebase**: Crashlytics para reportes de crashes, RemoteConfig para configuraciones remotas. Encapsulado en la capa de Data o un módulo separado.
- **Supabase**: Toda la comunicación backend a través de la capa de Red en Core.

## Criterios de Aceptación (Formato EARS)

1. **Escenario**: Dado que un desarrollador modifica la implementación de persistencia de Realm a Core Data, Cuando ejecuta los tests, Entonces todos los tests pasan sin cambios en la lógica de negocio.
2. **Escenario**: Dado que se añade un nuevo módulo de feature, Cuando se integra con SPM, Entonces no requiere cambios en módulos existentes.
3. **Escenario**: Dado que se usa inyección de dependencias con property wrappers, Cuando se ejecutan tests unitarios, Entonces se pueden mockear dependencias fácilmente.
4. **Escenario**: Dado que la app usa concurrencia estricta de Swift 6, Cuando se ejecutan operaciones asíncronas, Entonces no hay race conditions en la capa de dominio.
5. **Escenario**: Dado que se versionan módulos con SPM, Cuando se hace un release, Entonces se puede rollback a versiones anteriores sin afectar otros módulos.
6. **Escenario**: Dado que se separa UI de lógica de negocio, Cuando se cambia el diseño, Entonces no se modifica la capa de dominio.
7. **Escenario**: Dado que se integra Firebase Crashlytics, Cuando ocurre un crash, Entonces se reporta automáticamente sin afectar la lógica de negocio.
8. **Escenario**: Dado que se usa RemoteConfig, Cuando se cambia una configuración remota, Entonces se aplica sin redeploy de la app.
9. **Escenario**: Dado que se almacenan datos sensibles en Keychain, Cuando se accede a ellos, Entonces están seguros y no se exponen en logs.
10. **Escenario**: Dado que se estructura por capas, Cuando se añade una nueva entidad de dominio, Entonces se puede integrar sin cambios en capas externas.