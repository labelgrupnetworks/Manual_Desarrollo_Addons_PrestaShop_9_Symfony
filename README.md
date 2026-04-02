# Desarrollo de Módulos para PrestaShop
## Manual Completo con Metodologías Symfony Modernas

**Versión:** 1.0 | **PrestaShop:** 8.x / 9.x | **Actualización:** Abril 2026  
**Repositorio de ejemplos:** https://github.com/PrestaShop/example-modules  
**Documentación oficial:** https://devdocs.prestashop-project.org/9/

---

# Prólogo

Este manual nace con un único objetivo: ser el recurso definitivo en español para el desarrollo profesional de módulos en PrestaShop usando las metodologías modernas que introduce el framework Symfony.

PrestaShop es, a día de hoy, una de las plataformas de comercio electrónico de código abierto más utilizadas en el mundo hispano. Sin embargo, la plataforma ha vivido en los últimos años una transformación profunda: la adopción progresiva de Symfony como columna vertebral de su arquitectura.

A lo largo de este manual recorreremos ese camino juntos. Empezaremos desde cero y llegaremos hasta los patrones arquitectónicos más avanzados como CQRS, la integración con API Platform o el uso de Doctrine ORM. Cada concepto irá acompañado de código real, referencias a los módulos de ejemplo oficiales y, donde sea relevante, una comparación explícita entre la forma antigua y la nueva.

> **Cómo usar este manual:** Los capítulos están ordenados de menor a mayor complejidad.
> Si ya tienes experiencia con PrestaShop 1.7, puedes saltar al Capítulo 3. Si vienes
> de PrestaShop 1.6, empieza desde el principio. El Apéndice B incluye una guía de
> migración rápida para cada tecnología.

---

# Índice General

**PARTE I — FUNDAMENTOS**
- Capítulo 1: PrestaShop y su arquitectura moderna
- Capítulo 2: Preparación del entorno de desarrollo
- Capítulo 3: La transición al framework Symfony

**PARTE II — TU PRIMER MÓDULO**
- Capítulo 4: Estructura y anatomía de un módulo
- Capítulo 5: Ciclo de vida e instalación
- Capítulo 6: El sistema de Hooks
- Capítulo 7: Página de configuración básica

**PARTE III — SYMFONY EN PRESTASHOP**
- Capítulo 8: El contenedor de servicios y la inyección de dependencias
- Capítulo 9: Controladores Symfony en módulos
- Capítulo 10: Plantillas Twig

**PARTE IV — GESTIÓN DE DATOS**
- Capítulo 11: ObjectModel — el enfoque clásico
- Capítulo 12: Doctrine ORM — el enfoque moderno
- Capítulo 13: Consultas avanzadas y repositorios

**PARTE V — FORMULARIOS**
- Capítulo 14: Formularios Symfony en módulos
- Capítulo 15: Extender formularios existentes
- Capítulo 16: Subida de ficheros y datos complejos

**PARTE VI — INTERFAZ DE ADMINISTRACIÓN**
- Capítulo 17: El componente Grid
- Capítulo 18: Controladores con pestañas (Tabs)
- Capítulo 19: Extender Grids existentes
- Capítulo 20: Extender plantillas del back office

**PARTE VII — PATRONES AVANZADOS**
- Capítulo 21: El patrón CQRS
- Capítulo 22: API REST con API Platform
- Capítulo 23: Comandos de consola Symfony
- Capítulo 24: WebService de PrestaShop

**PARTE VIII — FRONT OFFICE**
- Capítulo 25: Integración en el front office
- Capítulo 26: Rutas personalizadas de módulo
- Capítulo 27: JavaScript y el JS Router
- Capítulo 28: Plantillas de correo electrónico

**PARTE IX — CASOS ESPECIALES**
- Capítulo 29: Módulos de pago
- Capítulo 30: Compatibilidad multitienda

**PARTE X — CALIDAD Y PRODUCCIÓN**
- Capítulo 31: Testing de módulos
- Capítulo 32: Buenas prácticas y seguridad
- Capítulo 33: Distribución y publicación

**APÉNDICES**
- Apéndice A: Referencia rápida de hooks
- Apéndice B: Guía de migración PS 1.6/1.7 → PS 8/9
- Apéndice C: Herramientas y recursos

---

# PARTE I — FUNDAMENTOS

> Esta primera parte establece las bases conceptuales y técnicas necesarias
> antes de escribir la primera línea de código de un módulo. Aunque puedas
> tener ganas de ir directamente al código, no te saltes estos capítulos:
> entender la arquitectura de PrestaShop es lo que diferencia a un desarrollador
> de módulos de un copista de tutoriales.

---

# Capítulo 1: PrestaShop y su arquitectura moderna

> **En este capítulo aprenderás:**
> - Cómo ha evolucionado la arquitectura de PrestaShop desde la versión 1.6 hasta la 9
> - Qué componentes de Symfony están integrados y para qué sirven
> - Qué tabla de compatibilidad de características manejar según la versión objetivo
> - Por qué la coexistencia de arquitecturas legacy/Symfony es inevitable (y manejable)
>
> **Nivel:** Básico  
> **Prerrequisitos:** Conocimiento básico de PHP orientado a objetos

---

## 1.1 Qué es PrestaShop y por qué importa su arquitectura

PrestaShop es una plataforma de e-commerce de código abierto escrita en PHP. Nació en 2007 como un proyecto académico en Francia y desde entonces ha crecido hasta convertirse en una de las soluciones más populares del mercado, especialmente en Europa y Latinoamérica.

Lo que hace interesante a PrestaShop desde el punto de vista del desarrollador no es solo su popularidad, sino su modelo de extensibilidad. PrestaShop fue diseñado desde el principio para ser extendido mediante **módulos** y **temas**. Esta arquitectura de plugins hace que prácticamente cualquier funcionalidad pueda añadirse sin tocar el núcleo de la plataforma.

### El mercado de módulos

El Marketplace oficial de PrestaShop, conocido como **PrestaShop Addons**, alberga miles de módulos de terceros. Desarrollar módulos puede ser una actividad profesional muy lucrativa, pero también representa uno de los desafíos técnicos más complejos del ecosistema PHP, precisamente porque la plataforma está en plena transformación arquitectónica.

---

## 1.2 La arquitectura de PrestaShop: antes y ahora

### La arquitectura clásica (PrestaShop 1.5 / 1.6)

En sus inicios, PrestaShop usaba una arquitectura completamente propia:

| Componente | Tecnología clásica | Descripción |
|---|---|---|
| ORM | `ObjectModel` | Mapeador objeto-relacional casero |
| Plantillas | Smarty | Motor de plantillas PHP |
| Controladores | Propios (`AdminController`) | Sin dependencias externas |
| Extensibilidad | Hooks (`Hook::exec()`) | Sistema de eventos propio |

Esta arquitectura era funcional pero limitante. El código mezclaba presentación y lógica, y los patrones de diseño modernos eran difíciles de aplicar.

### La transición (PrestaShop 1.7)

Con PrestaShop 1.7 (2016) llegó la primera gran transformación: la integración **parcial** de Symfony. El back office comenzó a migrarse, pero de forma incremental. Esto creó una plataforma **dual**: algunas páginas funcionaban con la arquitectura clásica y otras con Symfony.

> **Nota:** Esta dualidad arquitectónica es la que explica por qué a veces encontramos
> código "antiguo" y código "nuevo" conviviendo en el mismo proyecto. No es un bug,
> es una característica de la transición progresiva que tomará varios años completarse.

### La arquitectura moderna (PrestaShop 8 / 9)

PrestaShop 8 y especialmente PrestaShop 9 consolidan la adopción de Symfony:

```
┌──────────────────────────────────────────────────────────────┐
│                      PRESTASHOP 9                            │
│                                                              │
│  ┌──────────────────────┐   ┌────────────────────────────┐  │
│  │    CAPA LEGACY        │   │     CAPA SYMFONY           │  │
│  │                      │   │                            │  │
│  │  • ObjectModel       │   │  • Doctrine ORM            │  │
│  │  • Smarty templates  │   │  • Twig templates          │  │
│  │  • Old controllers   │   │  • Symfony controllers     │  │
│  │  • Hook::exec()      │   │  • Service Container (DI)  │  │
│  │  • HelperForm/List   │   │  • Symfony Forms           │  │
│  │                      │   │  • API Platform            │  │
│  └──────────────────────┘   └────────────────────────────┘  │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │         SISTEMA DE HOOKS (compartido por ambas)      │   │
│  └──────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────┘
```

> **Importante:** Ambas capas coexistirán durante años. Un módulo puede —y a veces
> debe— interactuar con las dos. Sin embargo, **todo código nuevo debe escribirse
> en la capa Symfony**. La capa legacy está en modo mantenimiento.

---

## 1.3 Tabla de compatibilidad de características

| Característica | PS 1.6 | PS 1.7 | PS 8.x | PS 9.x |
|---|:---:|:---:|:---:|:---:|
| ObjectModel | ✓ | ✓ | ✓ | ✓ |
| Symfony DI Container | — | Parcial | ✓ | ✓ |
| Doctrine ORM | — | Desde 1.7.6 | ✓ | ✓ |
| Symfony Forms | — | Parcial | ✓ | ✓ |
| Grid component | — | Desde 1.7.5 | ✓ | ✓ |
| Console commands | — | Desde 1.7.5 | ✓ | ✓ |
| Twig en módulos | — | ✓ | ✓ | ✓ |
| API Platform | — | — | Desde 8.1 | ✓ |
| CQRS | — | Parcial | ✓ | ✓ |
| Autowiring de servicios | — | — | Desde 8.1 | ✓ |
| Atributos PHP 8 en Doctrine | — | — | ✓ | ✓ |

---

## 1.4 El sistema de módulos: visión general

Un módulo en PrestaShop es, en esencia, un directorio dentro de `/modules/` que contiene:

1. Un archivo PHP principal con el mismo nombre que el directorio
2. Una clase PHP que extiende `Module` (o una de sus subclases)
3. Opcionalmente: controladores, vistas, servicios, entidades, etc.

El módulo se comunica con PrestaShop principalmente a través de **hooks**: puntos específicos del ciclo de vida de la plataforma donde el módulo puede inyectar código o contenido.

### ¿Por qué Symfony cambia las reglas del juego?

Cuando PrestaShop adoptó Symfony, los módulos ganaron acceso a:

- **Inyección de dependencias**: objetos construidos y conectados automáticamente
- **Formularios tipados**: validación automática, tipos reutilizables y extensibles
- **ORM Doctrine**: mapeo objeto-relacional profesional con repositorios y relaciones
- **Consola**: comandos CLI para automatización y mantenimiento
- **Testing nativo**: integración con PHPUnit y compatibilidad con mocks

La curva de aprendizaje es mayor, pero el resultado son módulos más robustos, mantenibles y testeables.

---

## 1.5 Estructura de directorios de PrestaShop relevante para módulos

```
prestashop/
├── modules/                    ← Aquí viven nuestros módulos
│   └── mi_modulo/
├── override/                   ← Overrides del core (usar con precaución)
├── src/                        ← Código fuente del core (Symfony)
├── config/                     ← Configuración de Symfony
├── var/
│   ├── cache/                  ← Caché de Symfony (regenerada constantemente)
│   └── modules/                ← Archivos estáticos de módulos
├── bin/
│   └── console                 ← CLI de Symfony/PrestaShop
└── composer.json               ← Dependencias del proyecto
```

> **Advertencia:** Los archivos en `/var/cache/` se regeneran constantemente.
> Nunca pongas ahí archivos que quieras persistir. Para archivos estáticos
> de módulos usa `/var/modules/TU_MODULO/` o `_PS_MODULE_DIR_ . TU_MODULO . '/views/'`.

---

### Resumen del Capítulo 1

| Concepto | Clave a recordar |
|---|---|
| Arquitectura dual | PS 8/9 tiene capa legacy + capa Symfony coexistiendo |
| ObjectModel | Válido pero en mantenimiento; usar Doctrine para código nuevo |
| Hooks | El mecanismo central de extensibilidad de todos los módulos |
| Versión objetivo | Verificar la tabla de compatibilidad antes de usar una característica |

**Próximo capítulo:** Capítulo 2 — Preparación del entorno de desarrollo.

---

# Capítulo 2: Preparación del entorno de desarrollo

> **En este capítulo aprenderás:**
> - Los requisitos de sistema para PrestaShop 8/9
> - Cómo instalar PrestaShop en Laragon (Windows/WSL) y con Docker
> - Cómo activar el modo de desarrollo y limpiar la caché eficientemente
> - Qué herramientas de calidad de código instalar desde el principio
> - Cómo configurar Git para el desarrollo de módulos
>
> **Nivel:** Básico  
> **Prerrequisitos:** PHP instalado y un servidor web local funcionando

---

## 2.1 Requisitos del sistema

| Componente | PS 8.x | PS 9.x |
|---|---|---|
| PHP | 8.1+ | 8.2+ |
| MySQL / MariaDB | 5.7+ / 10.4+ | 8.0+ / 10.6+ |
| Composer | 2.x | 2.x |
| Node.js (assets) | 18+ | 20+ |
| Servidor web | Apache 2.4 / Nginx 1.18 | Apache 2.4 / Nginx 1.24 |

```bash
# Verificar versiones instaladas
php -v
mysql --version
composer --version
node --version
git --version
```

---

## 2.2 Configuración de PHP para desarrollo

Edita tu `php.ini` con estos valores antes de empezar:

```ini
; Ejemplo 2.1 — php.ini optimizado para desarrollo de módulos PrestaShop

; Mostrar todos los errores durante el desarrollo
display_errors = On
error_reporting = E_ALL

; Aumentar límites para instalación y subida de archivos
max_execution_time = 300
memory_limit = 512M
upload_max_filesize = 64M
post_max_size = 64M

; Extensiones requeridas por PrestaShop
extension=intl
extension=gd
extension=zip
extension=curl
extension=mbstring
extension=pdo_mysql
```

> **Consejo:** Usa un archivo `php.ini` separado para desarrollo (ej: `php-dev.ini`) y
> apunta a él con `php -c php-dev.ini` cuando ejecutes comandos de consola. Así no
> afectas la configuración de producción.

---

## 2.3 Instalación con Docker (recomendado para equipos)

La instalación con Docker garantiza un entorno idéntico para todo el equipo y facilita la integración con pipelines CI/CD.

```yaml
# Ejemplo 2.2 — docker-compose.yml para desarrollo de módulos PrestaShop
# Archivo: docker-compose.yml (en la raíz del proyecto)

version: '3.8'
services:
  prestashop:
    image: prestashop/prestashop:8.1
    environment:
      - DB_SERVER=mysql
      - DB_NAME=prestashop
      - DB_USER=prestashop
      - DB_PASSWD=prestashop
      - PS_DEV_MODE=1
      - PS_INSTALL_AUTO=1
    ports:
      - "8080:80"
    volumes:
      # Montamos solo la carpeta de módulos para desarrollo
      - ./modules:/var/www/html/modules
    depends_on:
      - mysql

  mysql:
    image: mysql:8.0
    environment:
      MYSQL_DATABASE: prestashop
      MYSQL_USER: prestashop
      MYSQL_PASSWORD: prestashop
      MYSQL_ROOT_PASSWORD: root
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```

```bash
# Levantar el entorno
docker-compose up -d

# Ver logs en tiempo real
docker-compose logs -f prestashop

# Ejecutar comandos de consola dentro del contenedor
docker-compose exec prestashop php bin/console cache:clear
```

---

## 2.4 Activar el modo de desarrollo

> **Importante:** El modo de desarrollo es **obligatorio** durante el desarrollo.
> Sin él, PrestaShop cachea plantillas y servicios, lo que hace imposible ver
> los cambios al instante.

**Opción 1 — Variable de entorno** (recomendada, no requiere editar código):

```bash
# Archivo: .env.local (añadir al .gitignore, nunca commitear)
APP_ENV=dev
```

**Opción 2 — Desde el back office:**

`Parámetros avanzados > Rendimiento > Modo de depuración > Activar`

**Opción 3 — Editando el archivo de configuración:**

```php
<?php
// Ejemplo 2.3 — Activar modo debug en config/defines.inc.php
// SOLO para desarrollo local. NUNCA en producción.
define('_PS_MODE_DEV_', true);
```

---

## 2.5 Comandos de consola esenciales

Durante el desarrollo usarás estos comandos constantemente:

```bash
# Ejemplo 2.4 — Comandos de consola más utilizados en desarrollo

# Limpiar la caché (imprescindible después de cambiar services.yml, rutas, etc.)
php bin/console cache:clear

# Instalar o desinstalar un módulo sin necesitar el back office
php bin/console prestashop:module install mi_modulo
php bin/console prestashop:module uninstall mi_modulo

# Ver todos los servicios registrados en el contenedor
php bin/console debug:container | grep mi_modulo

# Ver todas las rutas registradas (incluyendo las de tus módulos)
php bin/console debug:router | grep mi_modulo

# Ver el SQL que generaría Doctrine para tus entidades (solo para referencia)
php bin/console doctrine:schema:update --dump-sql
```

> **Consejo:** Crea un alias en tu shell para limpiar la caché rápidamente:
>
> ```bash
> # Añadir a ~/.bashrc o ~/.zshrc
> alias pscache='php bin/console cache:clear'
> alias psmod='php bin/console prestashop:module'
> ```

---

## 2.6 Control de versiones con Git

Todo desarrollo profesional de módulos debe estar bajo control de versiones. A lo largo de este manual usaremos Git para documentar cada paso del desarrollo, con commits que sirven como puntos de restauración y documentación del progreso.

### Inicializar el repositorio del módulo

```bash
# Ejemplo 2.5 — Inicialización del repositorio de un módulo

# Crear el directorio del módulo (dentro de la instalación de PrestaShop)
mkdir -p modules/mi_modulo
cd modules/mi_modulo

# Inicializar Git
git init
git branch -M main
```

### .gitignore recomendado para módulos

```gitignore
# Ejemplo 2.6 — .gitignore para un módulo PrestaShop

# Dependencias de Composer (se regeneran con `composer install`)
/vendor/

# Archivos de caché
/var/

# Configuración local (credenciales, etc.)
/.env.local
/config/parameters.php

# Archivos generados automáticamente por PrestaShop
/config.xml

# IDEs
/.idea/
/.vscode/
*.swp
*.swo

# Sistema operativo
.DS_Store
Thumbs.db
```

### Convención de commits (Conventional Commits)

Seguimos el estándar **Conventional Commits** (https://www.conventionalcommits.org) para que el historial del repositorio sea legible y sirva como documentación:

| Tipo | Cuándo usarlo | Ejemplo |
|---|---|---|
| `feat:` | Nueva funcionalidad | `feat: implementar hook displayProductAdditionalInfo` |
| `fix:` | Corrección de bug | `fix: corregir error al instalar en PS 8.2` |
| `docs:` | Solo documentación | `docs: actualizar README con requisitos` |
| `refactor:` | Refactorización sin cambio de comportamiento | `refactor: migrar ObjectModel a Doctrine` |
| `test:` | Añadir o modificar tests | `test: añadir test unitario para ComentarioService` |
| `chore:` | Tareas de mantenimiento | `chore: actualizar dependencias de Composer` |

---

## 2.7 Herramientas de calidad de código

Instala estas herramientas en todos tus módulos desde el primer día:

```bash
# Ejemplo 2.7 — Instalación de herramientas de calidad de código

# PHP CS Fixer — para cumplir los estándares de codificación de PrestaShop
composer require --dev friendsofphp/php-cs-fixer

# PHPStan — análisis estático para detectar errores antes de ejecutar
composer require --dev phpstan/phpstan

# PHPUnit — para los tests
composer require --dev phpunit/phpunit
```

```php
<?php
// Ejemplo 2.8 — Configuración de PHP CS Fixer
// Archivo: .php-cs-fixer.dist.php

$finder = PhpCsFixer\Finder::create()
    ->in(__DIR__ . '/src')
    ->in(__DIR__ . '/controllers')
    ->name('*.php')
    ->notName('index.php');

return (new PhpCsFixer\Config())
    ->setRules([
        '@Symfony'      => true,
        'yoda_style'    => false,
        'concat_space'  => ['spacing' => 'one'],
    ])
    ->setFinder($finder);
```

```bash
# Comprobar el código sin modificarlo
vendor/bin/php-cs-fixer fix --dry-run --diff

# Corregir automáticamente
vendor/bin/php-cs-fixer fix
```

---

### Resumen del Capítulo 2

| Concepto | Clave a recordar |
|---|---|
| Modo desarrollo | Activar siempre en local; `APP_ENV=dev` es la forma más limpia |
| `cache:clear` | Ejecutar después de cualquier cambio en `services.yml` o rutas |
| Git + Conventional Commits | El historial de commits documenta el progreso del módulo |
| PHP CS Fixer | Instalar desde el inicio; los estándares son obligatorios en Addons |
| Docker | Garantiza entorno idéntico entre desarrolladores y con CI/CD |

**Próximo capítulo:** Capítulo 3 — La transición al framework Symfony.

---

# Capítulo 3: La transición al framework Symfony

> **En este capítulo aprenderás:**
> - Por qué PrestaShop eligió Symfony y qué ventajas aporta
> - Los conceptos fundamentales de Symfony que usarás en todos los módulos
> - La diferencia entre el contenedor legacy y el contenedor Symfony
> - El flujo de trabajo moderno para crear un módulo completo
>
> **Nivel:** Básico-Intermedio  
> **Prerrequisitos:** PHP orientado a objetos, conceptos básicos de MVC

---

## 3.1 ¿Por qué Symfony?

La decisión de PrestaShop de adoptar Symfony no fue arbitraria. Symfony es uno de los frameworks PHP más maduros del mundo. Entre sus ventajas para PrestaShop:

| Ventaja | Impacto en el desarrollo de módulos |
|---|---|
| Componentes reutilizables | Formularios, validación, routing, consola... todo ya construido |
| Inyección de dependencias | Módulos más desacoplados, más fáciles de testear |
| Estándares PSR | Código interoperable con el ecosistema PHP |
| Comunidad masiva | Miles de bundles, documentación, soporte |
| Testabilidad nativa | Diseñado para tests desde el primer día |

> **Nota:** Aprender Symfony para PrestaShop no es un gasto, es una inversión.
> Los mismos conocimientos sirven para Laravel, Drupal, API Platform y cualquier
> proyecto PHP moderno.

---

## 3.2 Conceptos de Symfony que usarás en cada módulo

### El Service Container (Contenedor de Servicios)

El Service Container es el corazón de Symfony. Es una clase que sabe cómo construir objetos y resolver sus dependencias automáticamente:

```php
<?php
// Ejemplo 3.1 — Comparativa: creación manual vs. inyección de dependencias

// SIN Symfony (forma antigua — acoplamiento fuerte)
$conexion   = new Conexion('localhost', 'user', 'pass');
$repositorio = new ProductoRepositorio($conexion);
$servicio   = new ProductoServicio($repositorio);

// CON Symfony (forma moderna — el contenedor construye todo)
// Solo necesitas pedir el servicio; Symfony resuelve el árbol completo
$servicio = $container->get('mi_modulo.producto_servicio');
```

La configuración del contenedor se hace en YAML:

```yaml
# Ejemplo 3.2 — Declaración de un servicio en config/services.yml
# El contenedor inyecta automáticamente las dependencias indicadas

services:
  mi_empresa.mi_modulo.producto_servicio:
    class: MiEmpresa\MiModulo\Service\ProductoService
    arguments:
      - '@doctrine.orm.entity_manager'  # @ = referencia a otro servicio
      - '@translator'
      - '%database_prefix%'             # % = referencia a un parámetro
```

### Inyección de Dependencias (DI)

```php
<?php
// Ejemplo 3.3 — Sin DI vs. con DI

// SIN DI — la clase crea sus propias dependencias (difícil de testear)
class ProductoService
{
    public function __construct()
    {
        $this->repositorio = new ProductoRepositorio(); // ← acoplamiento duro
    }
}

// CON DI — las dependencias se inyectan desde fuera (fácil de testear)
class ProductoService
{
    public function __construct(
        private readonly ProductoRepositorioInterface $repositorio
        // ↑ Symfony inyecta la implementación concreta automáticamente
    ) {
    }
}
```

La ventaja de la DI se ve claramente en los tests:

```php
<?php
// Ejemplo 3.4 — Testear un servicio con DI es trivial

// Podemos inyectar un mock en lugar de la implementación real
$mockRepositorio = $this->createMock(ProductoRepositorioInterface::class);
$mockRepositorio->method('findById')->willReturn($productoFalso);

$servicio = new ProductoService($mockRepositorio);
// Probamos el servicio sin tocar la base de datos
```

### Symfony Routing

```yaml
# Ejemplo 3.5 — Definición de rutas en config/routes.yml
# Cada ruta mapea una URL a un método de un controlador

mi_modulo_configuracion:
  path: /mi-modulo/configuracion
  methods: [GET, POST]
  defaults:
    _controller: 'MiEmpresa\MiModulo\Controller\Admin\ConfiguracionController::indexAction'

mi_modulo_editar:
  path: /mi-modulo/editar/{id}
  methods: [GET, POST]
  requirements:
    id: \d+                             # Solo números
  defaults:
    _controller: 'MiEmpresa\MiModulo\Controller\Admin\EditarController::indexAction'
```

### Twig — El motor de plantillas moderno

```
Smarty (clásico)                       Twig (moderno)
─────────────────────────────────────────────────────
{$nombre|escape:'html':'UTF-8'}        {{ nombre }}   ← escapa automáticamente
{foreach $lista as $item}              {% for item in lista %}
    <div>{$item.nombre}</div>              <div>{{ item.nombre }}</div>
{/foreach}                             {% endfor %}
{if $condicion}                        {% if condicion %}
    {l s='Texto' mod='modulo'}             {{ 'Texto'|trans({}, 'Modules.Modulo.Admin') }}
{/if}                                  {% endif %}
{include file='template.tpl'}          {% include 'template.html.twig' %}
```

---

## 3.3 Los tres contextos de ejecución

PrestaShop tiene tres contextos donde puede ejecutar el código de un módulo. Los servicios que declares deben estar disponibles en el contexto correcto:

| Archivo de configuración | Contexto disponible |
|---|---|
| `config/services.yml` | Back office Symfony (páginas modernas) |
| `config/admin/services.yml` | Back office completo (incluyendo páginas legacy) |
| `config/front/services.yml` | Front office (páginas de la tienda) |

> **Nota:** Si tu módulo usa hooks tanto en el front office como en el back office,
> debes declarar esos servicios en **ambos** archivos de configuración.

---

## 3.4 El flujo de trabajo moderno

Con todo esto en mente, el flujo completo para crear un módulo moderno es:

```
1.  Crear la estructura de directorios y el composer.json
         ↓
2.  Crear la clase principal del módulo (extends Module)
         ↓
3.  Definir install() con registerHook() y la creación de tablas
         ↓
4.  Declarar servicios en config/services.yml
         ↓
5.  Implementar entidades Doctrine en src/Entity/
         ↓
6.  Implementar repositorios en src/Repository/
         ↓
7.  Implementar servicios de negocio en src/Service/
         ↓
8.  Implementar controladores Symfony en src/Controller/Admin/
         ↓
9.  Crear formularios Symfony en src/Form/Type/
         ↓
10. Crear plantillas Twig en views/templates/
         ↓
11. Limpiar caché y probar
```

---

### Resumen del Capítulo 3

| Concepto | Clave a recordar |
|---|---|
| Service Container | Construye y conecta objetos automáticamente; no uses `new` para servicios |
| DI | Las dependencias van en el constructor; Symfony las inyecta |
| `services.yml` | El archivo de configuración que declara qué clases son servicios |
| Twig | Motor de plantillas moderno; escapa HTML automáticamente |
| Contextos | `services.yml`, `admin/services.yml`, `front/services.yml` según dónde se use |

**Próximo capítulo:** Capítulo 4 — Estructura y anatomía de un módulo.

---

# PARTE II — TU PRIMER MÓDULO

> Con las bases teóricas claras, ahora construiremos un módulo real paso a paso.
> Esta parte es eminentemente práctica: cada capítulo añade una pieza nueva al
> módulo y termina con un commit de Git que puedes usar como punto de referencia.

---

# Capítulo 4: Estructura y anatomía de un módulo

> **En este capítulo aprenderás:**
> - La estructura mínima obligatoria que PrestaShop exige en un módulo
> - La diferencia entre la estructura clásica y la estructura moderna con Symfony
> - Cómo configurar el autoloading PSR-4 con `composer.json`
> - Las convenciones de nomenclatura que PrestaShop impone
>
> **Módulos de referencia:** `demosymfonyformsimple`, `demodoctrine`  
> **Nivel:** Básico  
> **Versión mínima PS:** 8.0.0

---

## 4.1 El módulo más simple posible

Vamos a crear el módulo más simple posible para entender la estructura mínima requerida. Lo llamaremos `helloworld`.

```bash
# Ejemplo 4.1 — Crear la estructura inicial del módulo

mkdir -p modules/helloworld
cd modules/helloworld
git init
git branch -M main
```

### El archivo principal

```php
<?php
// Ejemplo 4.2 — Clase principal mínima de un módulo PrestaShop
// Archivo: modules/helloworld/helloworld.php

// Seguridad obligatoria: impide el acceso directo al archivo PHP
if (!defined('_PS_VERSION_')) {
    exit;
}

/**
 * Clase Helloworld — Módulo de ejemplo "Hello World".
 *
 * REGLA: El nombre de la clase DEBE ser PascalCase del nombre técnico del módulo.
 * REGLA: DEBE extender Module (o PaymentModule, etc. según el tipo).
 */
class Helloworld extends Module
{
    public function __construct()
    {
        // REGLA: $this->name DEBE coincidir con el nombre del directorio y del archivo PHP
        // Solo letras minúsculas y números. SIN guiones, guiones bajos ni espacios.
        $this->name = 'helloworld';

        // Sección del catálogo de módulos donde aparecerá
        $this->tab = 'front_office_features';

        // Versión semántica (MAJOR.MINOR.PATCH)
        $this->version = '1.0.0';

        $this->author = 'Mi Empresa';

        // Rango de versiones de PS compatibles
        $this->ps_versions_compliancy = [
            'min' => '8.0.0',
            'max' => _PS_VERSION_,
        ];

        // ¿Crear instancia al cargar la lista de módulos aunque no esté instalado?
        // false = recomendado (mejor rendimiento)
        $this->need_instance = 0;

        // ¿La página de configuración usa Bootstrap?
        $this->bootstrap = true;

        // IMPORTANTE: llamar al padre DESPUÉS de definir name, tab y version
        parent::__construct();

        // Nombre y descripción para mostrar (DEBEN ir después de parent::__construct()
        // para que las traducciones estén disponibles)
        $this->displayName = $this->trans('Hello World', [], 'Modules.Helloworld.Admin');
        $this->description = $this->trans(
            'Módulo de ejemplo para aprender el desarrollo de módulos PrestaShop.',
            [],
            'Modules.Helloworld.Admin'
        );
    }

    public function install(): bool
    {
        // Siempre llamar al install del padre primero
        return parent::install()
            && $this->registerHook('displayHeader')
            && $this->registerHook('displayFooterBefore');
    }

    public function uninstall(): bool
    {
        // Limpiar configuración al desinstalar (buena práctica)
        Configuration::deleteByName('HELLOWORLD_MESSAGE');

        return parent::uninstall();
    }

    public function hookDisplayHeader(array $params): void
    {
        // Añadir assets solo en las páginas que los necesitan
        $this->context->controller->addCSS($this->getPathUri() . 'views/css/helloworld.css');
        $this->context->controller->addJS($this->getPathUri() . 'views/js/helloworld.js');
    }

    public function hookDisplayFooterBefore(array $params): string
    {
        $this->context->smarty->assign([
            'mensaje' => Configuration::get('HELLOWORLD_MESSAGE') ?: '¡Hola Mundo!',
        ]);

        return $this->display(__FILE__, 'views/templates/hook/footer_before.tpl');
    }
}
```

### El archivo de seguridad index.php

```php
<?php
// Ejemplo 4.3 — Archivo de seguridad para evitar listado de directorios
// Archivo: modules/helloworld/index.php
// (Debe existir en CADA subdirectorio del módulo)

header('Expires: Mon, 26 Jul 1997 05:00:00 GMT');
header('Last-Modified: ' . gmdate('D, d M Y H:i:s') . ' GMT');
header('Cache-Control: no-store, no-cache, must-revalidate');
header('Pragma: no-cache');
header('Location: ../');
exit;
```

> **Importante:** El archivo `index.php` de seguridad debe estar presente en
> **cada subdirectorio** del módulo, no solo en la raíz. PrestaShop Validator
> lo comprueba automáticamente y rechaza módulos que no lo tengan.

### Commit del primer hito

```bash
# Ejemplo 4.4 — Primer commit del módulo

git add .
git commit -m "feat: estructura mínima del módulo helloworld

- Clase principal que extiende Module
- Hooks displayHeader y displayFooterBefore registrados
- Archivos index.php de seguridad en todos los directorios"
```

---

## 4.2 Estructura de un módulo moderno completo

Un módulo que usa todas las capacidades de Symfony tiene esta estructura:

```
mi_modulo/
├── mi_modulo.php               ← Clase principal (OBLIGATORIO)
├── composer.json               ← Autoloading PSR-4 y dependencias
├── logo.png                    ← Logo 32×32 px (visible en el catálogo)
├── index.php                   ← Seguridad (OBLIGATORIO)
│
├── config/                     ← Configuración Symfony
│   ├── index.php
│   ├── routes.yml              ← Rutas del módulo
│   ├── services.yml            ← Servicios (contexto Symfony)
│   ├── admin/
│   │   └── services.yml        ← Servicios (back office legacy también)
│   └── front/
│       └── services.yml        ← Servicios (front office)
│
├── src/                        ← Código PHP moderno (PSR-4, autoloading Composer)
│   ├── Controller/
│   │   └── Admin/
│   │       └── ConfiguracionController.php
│   ├── Entity/
│   │   └── MiEntidad.php       ← Entidades Doctrine
│   ├── Form/
│   │   └── Type/
│   │       └── ConfiguracionFormType.php
│   ├── Grid/
│   │   ├── Definition/Factory/
│   │   └── Data/Factory/
│   ├── Repository/
│   │   └── MiEntidadRepository.php
│   └── Service/
│       └── MiServicio.php
│
├── controllers/                ← Controladores legacy (si se necesitan)
│   ├── admin/
│   └── front/
│
├── views/                      ← Plantillas y assets
│   ├── css/
│   ├── js/
│   └── templates/
│       ├── admin/              ← Plantillas Twig para el back office
│       ├── front/              ← Plantillas para el front office
│       └── hook/               ← Plantillas para los hooks
│
├── translations/               ← Archivos de traducción
├── upgrade/                    ← Scripts de actualización
│   └── upgrade-1.1.0.php
└── tests/                      ← Tests del módulo
    ├── Unit/
    └── Integration/
```

---

## 4.3 El archivo composer.json

El `composer.json` es obligatorio para que el autoloading de PHP funcione con las clases en `/src/`:

```json
{
  "name": "mi-empresa/mi-modulo",
  "description": "Mi módulo de PrestaShop",
  "type": "prestashop-module",
  "license": "OSL-3.0",
  "authors": [
    {
      "name": "Mi Empresa",
      "email": "dev@mi-empresa.com"
    }
  ],
  "require": {
    "php": ">=8.1"
  },
  "require-dev": {
    "friendsofphp/php-cs-fixer": "^3.0",
    "phpstan/phpstan": "^1.0",
    "phpunit/phpunit": "^10.0"
  },
  "autoload": {
    "psr-4": {
      "MiEmpresa\\MiModulo\\": "src/"
    }
  },
  "autoload-dev": {
    "psr-4": {
      "MiEmpresa\\MiModulo\\Tests\\": "tests/"
    }
  }
}
```

```bash
# Instalar dependencias y generar el autoloading optimizado
composer install
composer dump-autoload -o
```

> **Nota:** PrestaShop incluye automáticamente el `vendor/autoload.php` del módulo
> cuando detecta un `composer.json` en el directorio raíz del módulo.

---

## 4.4 Convenciones de nomenclatura

PrestaShop tiene convenciones estrictas que **debes** respetar para que el módulo funcione correctamente:

| Elemento | Regla | Ejemplo correcto | Ejemplo incorrecto |
|---|---|---|---|
| Nombre técnico | Solo minúsculas + números | `demosymfonyform` | `demo_symfony_form` |
| Directorio | Igual al nombre técnico | `modules/demosymfonyform/` | `modules/demo-symfony/` |
| Archivo PHP | `{nombre}.php` | `demosymfonyform.php` | `DemoSymfonyForm.php` |
| Clase PHP | PascalCase del nombre | `class Demosymfonyform` | `class DemoSymfonyForm` |
| Namespace | Tu empresa + módulo | `MiEmpresa\MiModulo\` | `MiModulo\` |
| Config. BD | MAYÚSCULAS con prefijo | `MI_MODULO_OPCION` | `opcion` |
| Tablas BD | Prefijo del módulo | `ps_mi_modulo_datos` | `ps_datos` |
| Clases CSS | Prefijo del módulo | `.mi-modulo-banner` | `.banner` |

---

### Resumen del Capítulo 4

| Concepto | Clave a recordar |
|---|---|
| Archivo principal | Mismo nombre que el directorio; clase en PascalCase |
| `index.php` | Obligatorio en cada subdirectorio |
| `composer.json` | Habilita autoloading PSR-4 para las clases en `src/` |
| `logo.png` | 32×32 px, aparece en el catálogo de módulos |
| Nomenclatura | Solo minúsculas y números en el nombre técnico |

**Próximo capítulo:** Capítulo 5 — Ciclo de vida e instalación del módulo.

---

# Capítulo 5: Ciclo de vida e instalación del módulo

> **En este capítulo aprenderás:**
> - El ciclo de vida completo de un módulo en PrestaShop
> - Cómo implementar `install()` y `uninstall()` de forma robusta
> - Cómo crear y eliminar tablas SQL correctamente
> - Cómo gestionar configuraciones con el objeto `Configuration`
> - Cómo crear scripts de actualización para nuevas versiones
>
> **Nivel:** Básico  
> **Versión mínima PS:** 8.0.0

---

## 5.1 El ciclo de vida completo de un módulo

```
[Descarga/Subida al servidor]
          ↓
[INSTALACIÓN]   →  install()     ← crea tablas, registra hooks, configura defaults
          ↓
[HABILITADO]    →  hooks activos, módulo visible en la tienda
          ↓
[CONFIGURACIÓN] →  getContent()  ← controlador de configuración
          ↓
[ACTUALIZACIÓN] →  upgrade scripts (upgrade/upgrade-X.Y.Z.php)
          ↓
[DESHABILITADO] →  hooks desactivados, módulo invisible
          ↓
[DESINSTALACIÓN] → uninstall()   ← elimina tablas, limpia configuración
          ↓
[ELIMINACIÓN del servidor]
```

---

## 5.2 El método install()

El método `install()` es crítico. Si devuelve `false`, **la instalación falla y se revierte automáticamente**. Organízalo en métodos privados para mantener la legibilidad:

```php
<?php
// Ejemplo 5.1 — install() estructurado con métodos privados
// Patrón recomendado para módulos de complejidad media-alta

public function install(): bool
{
    // 1. Registrar el módulo en la BD (siempre primero)
    if (!parent::install()) {
        return false;
    }

    // 2. Crear tablas en la base de datos
    if (!$this->installDatabase()) {
        return false;
    }

    // 3. Configurar valores por defecto
    if (!$this->installConfiguration()) {
        return false;
    }

    // 4. Registrar tabs de navegación en el back office
    if (!$this->installTabs()) {
        return false;
    }

    // 5. Registrar hooks
    return $this->registerHook('displayHeader')
        && $this->registerHook('displayFooterBefore')
        && $this->registerHook('actionProductAdd');
}

private function installDatabase(): bool
{
    // Usar IF NOT EXISTS para que no falle si ya existe la tabla
    // Usar _DB_PREFIX_ siempre, nunca hardcodear 'ps_'
    $sql = 'CREATE TABLE IF NOT EXISTS `' . _DB_PREFIX_ . 'mi_modulo_datos` (
        `id`             int(11) unsigned NOT NULL AUTO_INCREMENT,
        `nombre`         varchar(255)     NOT NULL,
        `fecha_creacion` datetime         NOT NULL,
        PRIMARY KEY (`id`)
    ) ENGINE=' . _MYSQL_ENGINE_ . ' DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;';

    return Db::getInstance()->execute($sql);
}

private function installConfiguration(): bool
{
    return Configuration::updateValue('MI_MODULO_ACTIVO', 1)
        && Configuration::updateValue('MI_MODULO_COLOR', '#0070c0')
        && Configuration::updateValue('MI_MODULO_MAX_ITEMS', 5);
}

private function installTabs(): bool
{
    // Ver detalle en Capítulo 18
    return true;
}
```

---

## 5.3 El método uninstall()

La desinstalación debe limpiar **todo** lo que la instalación creó:

```php
<?php
// Ejemplo 5.2 — uninstall() con limpieza completa

public function uninstall(): bool
{
    // 1. Eliminar configuraciones del módulo
    Configuration::deleteByName('MI_MODULO_ACTIVO');
    Configuration::deleteByName('MI_MODULO_COLOR');
    Configuration::deleteByName('MI_MODULO_MAX_ITEMS');

    // 2. Eliminar tablas de la base de datos
    //    DILEMA: ¿eliminar datos del usuario?
    //    Recomendación: solo eliminar datos de configuración del módulo,
    //    NO los datos de negocio que el usuario ha generado.
    Db::getInstance()->execute(
        'DROP TABLE IF EXISTS `' . _DB_PREFIX_ . 'mi_modulo_datos`'
    );

    // 3. Eliminar tabs de navegación
    $this->uninstallTabs();

    // 4. Llamar al uninstall del padre (SIEMPRE al final)
    return parent::uninstall();
}
```

---

## 5.4 Scripts de actualización de versión

Cuando publicas una nueva versión, los usuarios existentes necesitan migrar sus datos sin perderlos. PrestaShop ejecuta automáticamente los scripts que encuentre en `upgrade/`:

```php
<?php
// Ejemplo 5.3 — Script de actualización a la versión 1.1.0
// Archivo: upgrade/upgrade-1.1.0.php
// Convención de nombre: upgrade-{NUEVA_VERSION}.php

/**
 * Actualización a 1.1.0: añade columna 'email' y nuevo hook.
 *
 * @param Module $module Instancia del módulo
 * @return bool
 */
function upgrade_module_1_1_0(Module $module): bool
{
    // Añadir columna nueva a tabla existente
    $sql = 'ALTER TABLE `' . _DB_PREFIX_ . 'mi_modulo_datos`
            ADD COLUMN `email` varchar(255) NULL AFTER `nombre`';

    if (!Db::getInstance()->execute($sql)) {
        return false;
    }

    // Registrar un hook nuevo que añadimos en esta versión
    if (!$module->registerHook('actionNuevoHook')) {
        return false;
    }

    // Añadir nuevo valor de configuración
    return Configuration::updateValue('MI_MODULO_NUEVA_OPCION', 0);
}
```

> **Importante:** Nombra los scripts de actualización con la versión DESTINO
> (`upgrade-1.1.0.php` se ejecuta al actualizar A la versión 1.1.0).
> PrestaShop los ejecuta en orden ascendente de versión.

---

## 5.5 El objeto Configuration

`Configuration` es la API estándar para guardar y recuperar ajustes del módulo:

```php
<?php
// Ejemplo 5.4 — API completa del objeto Configuration

// ── ESCRIBIR ──────────────────────────────────────────────────────────────
// Guardar un valor (aplica a todas las tiendas en modo single-shop)
Configuration::updateValue('MI_MODULO_COLOR', '#0070c0');

// Guardar un valor solo para una tienda específica (multitienda)
Configuration::updateValue('MI_MODULO_COLOR', '#ff0000', false, null, (int) $idShop);

// ── LEER ──────────────────────────────────────────────────────────────────
// Obtener un valor
$color = Configuration::get('MI_MODULO_COLOR');

// Obtener con valor por defecto si no existe
$color = Configuration::get('MI_MODULO_COLOR') ?: '#0070c0';

// Obtener de una tienda específica
$color = Configuration::get('MI_MODULO_COLOR', null, null, (int) $idShop);

// ── ELIMINAR ──────────────────────────────────────────────────────────────
Configuration::deleteByName('MI_MODULO_COLOR');
```

> **Convención:** Usa siempre `NOMBRE_MODULO_NOMBRE_OPCION` en mayúsculas para las
> claves de configuración. Evita nombres genéricos que puedan colisionar con otros módulos.

---

## 5.6 Instalación desde la línea de comandos

```bash
# Ejemplo 5.5 — Gestión de módulos desde la CLI (útil para CI/CD)

# Instalar
php bin/console prestashop:module install mi_modulo

# Desinstalar
php bin/console prestashop:module uninstall mi_modulo

# Habilitar / deshabilitar sin desinstalar
php bin/console prestashop:module enable mi_modulo
php bin/console prestashop:module disable mi_modulo
```

---

### Resumen del Capítulo 5

| Concepto | Clave a recordar |
|---|---|
| `install()` | Devuelve `false` → instalación falla y se revierte; siempre llamar `parent::install()` primero |
| `uninstall()` | Limpiar todo: configuraciones, tablas, tabs; llamar `parent::uninstall()` al final |
| `upgrade/` | Scripts de migración automáticos; nombre = versión destino |
| `Configuration` | API para guardar ajustes; usar MAYÚSCULAS con prefijo del módulo |
| CLI | `php bin/console prestashop:module install/uninstall` para automatización |

**Próximo capítulo:** Capítulo 6 — El sistema de Hooks.

---

# Capítulo 6: El sistema de Hooks

> **En este capítulo aprenderás:**
> - Qué es un hook y cómo funciona internamente
> - La diferencia entre hooks de acción y hooks de visualización
> - Cómo registrar, implementar y crear hooks personalizados
> - Cómo disparar hooks desde distintos contextos (legacy, Symfony, Twig)
> - Los hooks más importantes del front y back office
>
> **Nivel:** Básico-Intermedio  
> **Versión mínima PS:** 8.0.0

---

## 6.1 ¿Qué es un hook?

Un hook es un **punto de anclaje** en el código de PrestaShop donde los módulos pueden inyectar código o contenido. Piensa en ellos como eventos: PrestaShop llega a un punto específico, "dispara" el hook, y todos los módulos suscritos ejecutan su código.

```
PrestaShop renderizando la página de un producto:

  1. Carga el controlador del producto
  2. Consulta la base de datos
  3. ── Hook: actionProductInit ──────── ← módulos ejecutan lógica
  4. Renderiza la cabecera HTML
  5. ── Hook: displayHeader ─────────── ← módulos inyectan CSS/JS
  6. Renderiza el contenido del producto
  7. ── Hook: displayProductAdditionalInformation ← módulos añaden contenido
  8. Renderiza el footer
  9. ── Hook: displayFooter ─────────── ← módulos añaden contenido
```

---

## 6.2 Tipos de hooks

### Hooks de acción (action hooks)

Prefijo `action`. Se ejecutan ante eventos del sistema. **No devuelven HTML**; sirven para ejecutar lógica de negocio.

```php
<?php
// Ejemplo 6.1 — Implementación de un hook de acción
// Método: hook + NombreHookEnPascalCase

/**
 * Se ejecuta cuando se añade un nuevo producto al catálogo.
 * $params['object'] contiene el objeto Product recién creado.
 */
public function hookActionProductAdd(array $params): void
{
    /** @var Product $producto */
    $producto = $params['object'];

    // Lógica de negocio: sincronizar con sistema externo
    $this->get('mi_empresa.mi_modulo.sync_service')
         ->sincronizarProducto($producto->id);
}
```

### Hooks de visualización (display hooks)

Prefijo `display`. Se llaman en puntos concretos de las plantillas. **Deben devolver HTML como string**.

```php
<?php
// Ejemplo 6.2 — Implementación de un hook de visualización

/**
 * Se llama en la ficha de producto del front office.
 * Debe devolver una cadena HTML que PrestaShop insertará en la página.
 */
public function hookDisplayProductAdditionalInformation(array $params): string
{
    $idProduct = (int) $params['product']['id_product'];

    // Obtener datos adicionales desde la BD o un servicio
    $datosExtra = $this->get('mi_empresa.mi_modulo.datos_service')
                       ->obtenerDatosExtra($idProduct);

    // Asignar variables a la plantilla
    $this->context->smarty->assign([
        'datos_extra' => $datosExtra,
        'id_product'  => $idProduct,
    ]);

    // Devolver el HTML renderizado desde la plantilla Smarty
    return $this->display(__FILE__, 'views/templates/hook/product_extra.tpl');
}
```

---

## 6.3 Registro de hooks

Los hooks deben registrarse en `install()`. Sin registro, el módulo no recibe las llamadas:

```php
<?php
// Ejemplo 6.3 — Registro de múltiples hooks en install()

public function install(): bool
{
    return parent::install()
        // Hooks de visualización
        && $this->registerHook('displayHeader')
        && $this->registerHook('displayFooter')
        && $this->registerHook('displayProductAdditionalInformation')
        // Hooks de acción
        && $this->registerHook('actionProductAdd')
        && $this->registerHook('actionOrderStatusUpdate');
}
```

> **Nota:** Puedes registrar hooks adicionales en cualquier momento desde el back office
> (`Módulos > Posiciones`) o programáticamente con `$this->registerHook('hookName')`.

---

## 6.4 Disparar hooks desde distintos contextos

| Contexto | Cómo disparar un hook |
|---|---|
| Controlador legacy | `Hook::exec('actionMiHook', ['dato' => $valor])` |
| Controlador Symfony | `$this->dispatchHook('actionMiHook', ['dato' => $valor])` |
| Plantilla Smarty | `{hook h='displayMiHook' parametro=$valor}` |
| Plantilla Twig | `{{ renderHook('displayMiHook', { parametro: valor }) }}` |

---

## 6.5 Crear hooks personalizados

```php
<?php
// Ejemplo 6.4 — Crear y disparar un hook personalizado

// En install(): PrestaShop crea automáticamente hooks que no existen
public function install(): bool
{
    return parent::install()
        && $this->registerHook('actionMiModuloOperacionCompletada')
        && $this->registerHook('displayMiModuloExtraContent');
}

// En el código del módulo: disparar el hook cuando ocurre el evento
private function procesarOperacion(array $datos): void
{
    // ... lógica de negocio ...

    // Notificar a otros módulos que pueden "engancharse"
    Hook::exec('actionMiModuloOperacionCompletada', [
        'datos'     => $datos,
        'timestamp' => time(),
        'modulo'    => $this->name,
    ]);
}
```

Otro módulo puede entonces suscribirse:

```php
<?php
// Ejemplo 6.5 — Suscribirse al hook personalizado de otro módulo

public function install(): bool
{
    return parent::install()
        && $this->registerHook('actionMiModuloOperacionCompletada');
}

public function hookActionMiModuloOperacionCompletada(array $params): void
{
    // Reaccionar al evento del otro módulo
    $this->logger->info(
        'Operación completada: ' . json_encode($params['datos'])
    );
}
```

---

## 6.6 Referencia rápida de hooks esenciales

### Front office — Visualización

| Hook | Dónde se inserta |
|---|---|
| `displayHeader` | `<head>` de todas las páginas (CSS/JS) |
| `displayFooter` | Antes del `</body>` |
| `displayHome` | Página de inicio |
| `displayProductAdditionalInformation` | Ficha de producto |
| `displayShoppingCartFooter` | Carrito de compra |
| `displayOrderConfirmation` | Confirmación de pedido |
| `displayLeftColumn` / `displayRightColumn` | Columnas laterales |

### Front office — Acción

| Hook | Cuándo se dispara |
|---|---|
| `actionCartSave` | Al guardar el carrito |
| `actionValidateOrder` | Al validar un pedido |
| `actionOrderStatusUpdate` | Al cambiar el estado de un pedido |
| `actionCustomerAccountAdd` | Al crear una cuenta de cliente |
| `actionAuthentication` | Al autenticar a un cliente |

### Back office

| Hook | Uso típico |
|---|---|
| `displayBackOfficeHeader` | Añadir CSS/JS al back office |
| `actionAdminControllerSetMedia` | Añadir assets a un controlador específico del BO |
| `displayAdminProductsExtra` | Añadir contenido al formulario de producto |
| `displayAdminOrderTabContent` | Añadir pestaña en la vista de pedido |

---

### Resumen del Capítulo 6

| Concepto | Clave a recordar |
|---|---|
| Hooks de acción | Prefijo `action`; ejecutan lógica, no devuelven HTML |
| Hooks de visualización | Prefijo `display`; devuelven HTML como string |
| Registro | Obligatorio en `install()`; sin registro = sin llamadas |
| Método PHP | `hook` + nombre del hook en PascalCase: `hookDisplayHeader()` |
| Hooks personalizados | `registerHook()` los crea si no existen; otros módulos pueden suscribirse |

**Próximo capítulo:** Capítulo 7 — Página de configuración básica.

---

# Capítulo 7: Página de configuración básica

> **En este capítulo aprenderás:**
> - La diferencia entre el enfoque clásico (HelperForm) y el moderno (Symfony Forms)
> - Cómo implementar una configuración básica con `HelperForm` para compatibilidad
> - Cómo redirigir a un controlador Symfony moderno desde `getContent()`
> - Cuándo usar cada enfoque según el contexto del proyecto
>
> **Módulos de referencia:** `demosymfonyformsimple`  
> **Nivel:** Básico  
> **Versión mínima PS:** 8.0.0

---

## 7.1 Los dos enfoques para la página de configuración

| Aspecto | HelperForm (clásico) | Symfony Forms (moderno) |
|---|---|---|
| Complejidad inicial | Baja | Media |
| Mantenibilidad | Media | Alta |
| Testabilidad | Baja | Alta |
| Validación | Manual | Automática (constraints) |
| Tipos de campo reutilizables | No | Sí |
| Compatibilidad | PS 1.5+ | PS 1.7.6+ (estable en 8+) |
| **Recomendado para** | Módulos simples / legacy | Módulos nuevos o complejos |

---

## 7.2 Configuración clásica con HelperForm

```php
<?php
// Ejemplo 7.1 — Página de configuración con HelperForm (enfoque clásico)
// Válido y funcional para módulos simples o que necesitan compatibilidad con PS 1.7

public function getContent(): string
{
    $output = '';

    if (Tools::isSubmit('submitMiModulo')) {
        $output .= $this->procesarFormulario();
    }

    return $output . $this->renderFormulario();
}

private function procesarFormulario(): string
{
    $nombre = Tools::getValue('MI_MODULO_NOMBRE');

    if (empty($nombre)) {
        return $this->displayError(
            $this->trans('El nombre no puede estar vacío.', [], 'Modules.Mimodulo.Admin')
        );
    }

    Configuration::updateValue('MI_MODULO_NOMBRE', $nombre);
    Configuration::updateValue('MI_MODULO_ACTIVO', (int) Tools::getValue('MI_MODULO_ACTIVO'));

    return $this->displayConfirmation(
        $this->trans('Configuración guardada correctamente.', [], 'Modules.Mimodulo.Admin')
    );
}

private function renderFormulario(): string
{
    $fields_form = [
        'form' => [
            'legend' => [
                'title' => $this->trans('Configuración', [], 'Modules.Mimodulo.Admin'),
                'icon'  => 'icon-cogs',
            ],
            'input' => [
                [
                    'type'     => 'text',
                    'label'    => $this->trans('Nombre a mostrar', [], 'Modules.Mimodulo.Admin'),
                    'name'     => 'MI_MODULO_NOMBRE',
                    'required' => true,
                    'hint'     => $this->trans('Texto visible en la tienda.', [], 'Modules.Mimodulo.Admin'),
                ],
                [
                    'type'    => 'switch',
                    'label'   => $this->trans('Activo', [], 'Modules.Mimodulo.Admin'),
                    'name'    => 'MI_MODULO_ACTIVO',
                    'is_bool' => true,
                    'values'  => [
                        ['id' => 'on',  'value' => 1, 'label' => $this->trans('Sí', [], 'Admin.Global')],
                        ['id' => 'off', 'value' => 0, 'label' => $this->trans('No', [], 'Admin.Global')],
                    ],
                ],
            ],
            'submit' => [
                'title' => $this->trans('Guardar', [], 'Admin.Actions'),
                'name'  => 'submitMiModulo',
            ],
        ],
    ];

    $helper                     = new HelperForm();
    $helper->module             = $this;
    $helper->show_toolbar       = false;
    $helper->submit_action      = 'submitMiModulo';
    $helper->token              = Tools::getAdminTokenLite('AdminModules');
    $helper->currentIndex       = AdminController::$currentIndex . '&configure=' . $this->name;
    $helper->tpl_vars           = [
        'fields_value' => [
            'MI_MODULO_NOMBRE'  => Configuration::get('MI_MODULO_NOMBRE'),
            'MI_MODULO_ACTIVO'  => Configuration::get('MI_MODULO_ACTIVO'),
        ],
    ];

    return $helper->generateForm([$fields_form]);
}
```

---

## 7.3 Configuración moderna: redirigir a un controlador Symfony

El patrón moderno (y el que usa `demosymfonyformsimple`) es que `getContent()` no renderice nada, sino que **redirija** al controlador Symfony que gestiona la configuración:

```php
<?php
// Ejemplo 7.2 — getContent() moderno que redirige al controlador Symfony
// Este es el patrón del módulo demosymfonyformsimple

public function getContent(): void
{
    // Obtener el router de Symfony desde el contenedor de servicios del módulo
    $router = $this->get('router');

    // Generar la URL de la ruta Symfony y redirigir
    $route = $router->generate('mi_modulo_configuracion');
    Tools::redirectAdmin($route);
}
```

> **Consejo:** Este es el patrón que debes seguir en todos los módulos nuevos.
> La lógica del formulario vive en el controlador Symfony (Capítulo 9),
> y los tipos de campo en una clase `FormType` (Capítulo 14).
> Consulta el módulo `demosymfonyformsimple` para ver la implementación completa.

---

### Resumen del Capítulo 7

| Concepto | Clave a recordar |
|---|---|
| `getContent()` | Habilita el botón "Configurar" en el catálogo de módulos |
| `HelperForm` | Enfoque clásico; rápido para formularios simples, difícil de testear |
| Redirección a Symfony | Patrón moderno; `getContent()` solo llama a `Tools::redirectAdmin()` |
| FormType | La lógica del formulario va en `src/Form/Type/` (ver Capítulo 14) |

**Próximo capítulo:** Capítulo 8 — El contenedor de servicios y la inyección de dependencias.

---

*[Continúa en parte2_nuevo.md]*
# PARTE III — SYMFONY EN PRESTASHOP

> Esta parte introduce el ecosistema Symfony dentro de PrestaShop y explica cómo
> los módulos se integran con el contenedor de servicios, los controladores y
> las plantillas Twig. Es el núcleo técnico del desarrollo moderno.

---

# Capítulo 8: El contenedor de servicios y la inyección de dependencias

> **En este capítulo aprenderás:**
> - Cómo declarar y usar servicios en `config/services.yml`
> - La diferencia entre servicios públicos y privados
> - Cómo usar el autowiring para reducir configuración manual
> - Cómo decorar servicios existentes del core de PrestaShop
> - Los tres archivos de configuración según el contexto de ejecución
>
> **Módulos de referencia:** `demosymfonyformsimple`, `demodoctrine`  
> **Nivel:** Intermedio  
> **Versión mínima PS:** 8.0.0

---

## 8.1 Declarar servicios en services.yml

El archivo `config/services.yml` es donde declaras qué clases PHP actúan como servicios del contenedor Symfony de tu módulo:

```yaml
# Ejemplo 8.1 — Declaración básica de servicios
# Archivo: modules/mi_modulo/config/services.yml
# Convención de nombre: empresa.modulo.nombre_descriptivo

services:
  # Servicio con dependencias declaradas explícitamente
  mi_empresa.mi_modulo.comentario_service:
    class: MiEmpresa\MiModulo\Service\ComentarioService
    arguments:
      - '@doctrine.orm.entity_manager'   # @ = otro servicio del contenedor
      - '@translator'                    # @ = servicio del core de Symfony
      - '@logger'
    public: true                         # Accesible desde $this->get() en el módulo

  # Servicio que depende de otro servicio del mismo módulo
  mi_empresa.mi_modulo.notificacion_service:
    class: MiEmpresa\MiModulo\Service\NotificacionService
    arguments:
      - '@mi_empresa.mi_modulo.comentario_service'
      - '%shop_default_currency%'        # % = parámetro del contenedor
    public: false                        # Solo accesible por inyección (recomendado)
```

---

## 8.2 Autowiring: la forma moderna de configurar servicios

Desde PrestaShop 8.1, los módulos soportan **autowiring**: Symfony resuelve automáticamente las dependencias por el tipo de los parámetros del constructor:

```yaml
# Ejemplo 8.2 — Configuración con autowiring (PS 8.1+)
# Archivo: modules/mi_modulo/config/services.yml
# Con esto, Symfony registra TODAS las clases en src/ como servicios
# y resuelve sus dependencias automáticamente.

services:
  _defaults:
    autowire: true       # Inyecta dependencias por tipo automáticamente
    autoconfigure: true  # Aplica tags automáticamente (Console Commands, etc.)
    public: false        # Por defecto, todos los servicios son privados

  # Registrar todas las clases de src/ como servicios
  MiEmpresa\MiModulo\:
    resource: '../src/'
    exclude:
      - '../src/Entity/'      # Las entidades Doctrine no son servicios
      - '../src/Tests/'
      - '../index.php'
```

> **Importante:** Con autowiring, si un servicio necesita acceso desde el módulo
> principal (mediante `$this->get()`), debes marcarlo explícitamente como público:
>
> ```yaml
> services:
>   MiEmpresa\MiModulo\Service\ComentarioService:
>     public: true   # Sobrescribe el default: false para este servicio
> ```

---

## 8.3 Crear un servicio de negocio completo

```php
<?php
// Ejemplo 8.3 — Servicio de negocio con inyección de dependencias
// Archivo: src/Service/ComentarioService.php

declare(strict_types=1);

namespace MiEmpresa\MiModulo\Service;

use Doctrine\ORM\EntityManagerInterface;
use MiEmpresa\MiModulo\Entity\Comentario;
use MiEmpresa\MiModulo\Repository\ComentarioRepository;
use Psr\Log\LoggerInterface;
use Symfony\Contracts\Translation\TranslatorInterface;

class ComentarioService
{
    // Constructor promotion (PHP 8+): declara y asigna en una línea
    public function __construct(
        private readonly EntityManagerInterface $entityManager,
        private readonly ComentarioRepository   $comentarioRepository,
        private readonly TranslatorInterface    $translator,
        private readonly LoggerInterface        $logger
    ) {
    }

    /**
     * Crea un nuevo comentario para un producto.
     *
     * @throws \InvalidArgumentException Si los datos no pasan la validación
     */
    public function crearComentario(
        int    $idProducto,
        int    $idCliente,
        string $titulo,
        string $contenido,
        int    $valoracion
    ): Comentario {
        // Validación de invariantes de negocio
        if ($valoracion < 1 || $valoracion > 5) {
            throw new \InvalidArgumentException(
                $this->translator->trans(
                    'La valoración debe estar entre 1 y 5.',
                    [],
                    'Modules.Mimodulo.Shop'
                )
            );
        }

        if (empty(trim($contenido))) {
            throw new \InvalidArgumentException(
                $this->translator->trans(
                    'El contenido no puede estar vacío.',
                    [],
                    'Modules.Mimodulo.Shop'
                )
            );
        }

        $comentario = new Comentario();
        $comentario
            ->setIdProducto($idProducto)
            ->setIdCliente($idCliente)
            ->setTitulo($titulo)
            ->setContenido($contenido)
            ->setValoracion($valoracion)
            ->setFechaCreacion(new \DateTimeImmutable())
            ->setAprobado(false); // Requiere aprobación manual

        $this->entityManager->persist($comentario);
        $this->entityManager->flush();

        $this->logger->info(sprintf(
            '[ComentarioService] Nuevo comentario #%d para producto %d',
            $comentario->getId(),
            $idProducto
        ));

        return $comentario;
    }

    /** @return Comentario[] */
    public function obtenerAprobadosPorProducto(int $idProducto): array
    {
        return $this->comentarioRepository->findAprobadosByProducto($idProducto);
    }
}
```

---

## 8.4 Servicios públicos vs. privados

```yaml
# Ejemplo 8.4 — Cuándo usar public: true

services:
  # PRIVADO (por defecto en Symfony moderno)
  # Solo inyectable; NO accesible con $container->get() ni $this->get()
  mi_empresa.mi_modulo.servicio_interno:
    class: MiEmpresa\MiModulo\Service\ServicioInterno
    # public: false es el valor por defecto

  # PÚBLICO
  # Necesario cuando accedes al servicio desde el módulo principal:
  #   $this->get('mi_empresa.mi_modulo.servicio_publico')
  mi_empresa.mi_modulo.servicio_publico:
    class: MiEmpresa\MiModulo\Service\ServicioPublico
    public: true
```

```php
<?php
// Ejemplo 8.5 — Acceder a un servicio desde el módulo principal
// (El módulo principal NO es un servicio Symfony, por eso necesita public: true)

class MiModulo extends Module
{
    public function hookActionProductAdd(array $params): void
    {
        // Solo funciona si el servicio tiene public: true en services.yml
        $servicio = $this->get('mi_empresa.mi_modulo.comentario_service');
        $servicio->sincronizarProducto($params['object']->id);
    }
}
```

---

## 8.5 Decorar servicios del core de PrestaShop

La **decoración** permite extender el comportamiento de un servicio existente sin modificar el núcleo:

```yaml
# Ejemplo 8.6 — Decorar el servicio de emails de PrestaShop
# El servicio original sigue existiendo y es accesible como '.inner'

services:
  mi_empresa.mi_modulo.email_decorator:
    class: MiEmpresa\MiModulo\Service\EmailDecorator
    decorates: prestashop.core.mail.service
    arguments:
      - '@mi_empresa.mi_modulo.email_decorator.inner'  # Servicio original
      - '@logger'
```

```php
<?php
// Ejemplo 8.7 — Implementación del decorador
// Archivo: src/Service/EmailDecorator.php

namespace MiEmpresa\MiModulo\Service;

use PrestaShop\PrestaShop\Core\Mail\MailServiceInterface;
use Psr\Log\LoggerInterface;

class EmailDecorator implements MailServiceInterface
{
    public function __construct(
        private readonly MailServiceInterface $decorated, // Servicio original
        private readonly LoggerInterface      $logger
    ) {
    }

    public function sendMail(/* mismos parámetros que el original */): bool
    {
        $this->logger->info('Iniciando envío de email...');

        $resultado = $this->decorated->sendMail(/* ... */);

        $this->logger->info($resultado ? 'Email enviado.' : 'Error al enviar email.');

        return $resultado;
    }
}
```

---

## 8.6 Los tres archivos de configuración de servicios

| Archivo | Contexto disponible | Cuándo usarlo |
|---|---|---|
| `config/services.yml` | Back office Symfony (páginas modernas) | Controladores Symfony, formularios |
| `config/admin/services.yml` | Back office completo (legacy + Symfony) | Hooks que se usan en el BO |
| `config/front/services.yml` | Front office (tienda) | Hooks del front office, AJAX |

```yaml
# Ejemplo 8.8 — Servicio disponible en el front office
# Archivo: modules/mi_modulo/config/front/services.yml

services:
  mi_empresa.mi_modulo.comentario_service:
    class: MiEmpresa\MiModulo\Service\ComentarioService
    arguments:
      - '@doctrine.orm.entity_manager'
    public: true
```

---

### Resumen del Capítulo 8

| Concepto | Clave a recordar |
|---|---|
| `services.yml` | Declara servicios y sus dependencias; `@servicio` y `%parametro%` |
| Autowiring | Resuelve dependencias por tipo automáticamente (PS 8.1+) |
| `public: true` | Necesario para acceder con `$this->get()` desde el módulo principal |
| Decoración | Extiende un servicio existente sin modificar el core |
| Contextos | Tres archivos de configuración según el contexto de ejecución |

**Próximo capítulo:** Capítulo 9 — Controladores Symfony en módulos.

---

# Capítulo 9: Controladores Symfony en módulos

> **En este capítulo aprenderás:**
> - Cómo crear un controlador Symfony que extiende `FrameworkBundleAdminController`
> - Cómo definir rutas en `config/routes.yml` para los controladores del módulo
> - Cómo registrar Tabs de navegación en el back office
> - Cómo manejar permisos y tokens CSRF
> - Cómo crear controladores para el front office con `ModuleFrontController`
>
> **Módulos de referencia:** `democontrollertabs`, `demosymfonyformsimple`  
> **Nivel:** Intermedio  
> **Versión mínima PS:** 8.0.0

---

## 9.1 Controlador de back office con FrameworkBundleAdminController

```php
<?php
// Ejemplo 9.1 — Controlador Symfony completo para el back office
// Archivo: src/Controller/Admin/ConfiguracionController.php

declare(strict_types=1);

namespace MiEmpresa\MiModulo\Controller\Admin;

use MiEmpresa\MiModulo\Form\Type\ConfiguracionFormType;
use PrestaShopBundle\Controller\Admin\FrameworkBundleAdminController;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

/**
 * Controlador para la página de configuración del módulo.
 *
 * FrameworkBundleAdminController proporciona:
 *   - $this->get('servicio')     → acceso al contenedor Symfony
 *   - $this->render(...)         → renderizar plantillas Twig
 *   - $this->addFlash(...)       → mensajes de éxito/error
 *   - $this->generateUrl(...)    → generar URLs a partir de rutas
 *   - $this->redirectToRoute()   → redireccionar a una ruta
 *   - $this->trans(...)          → traducir cadenas
 */
class ConfiguracionController extends FrameworkBundleAdminController
{
    /**
     * Muestra y procesa el formulario de configuración del módulo.
     * Patrón PRG (Post-Redirect-Get) para evitar reenvíos del formulario.
     */
    public function indexAction(Request $request): Response
    {
        // Obtener el servicio de configuración de PrestaShop
        $configuration = $this->get('prestashop.adapter.legacy.configuration');

        // Preparar datos actuales para el formulario
        $formData = [
            'nombre'    => $configuration->get('MI_MODULO_NOMBRE', ''),
            'activo'    => (bool) $configuration->get('MI_MODULO_ACTIVO', false),
            'color'     => $configuration->get('MI_MODULO_COLOR', '#0070c0'),
            'max_items' => (int) $configuration->get('MI_MODULO_MAX_ITEMS', 5),
        ];

        $form = $this->createForm(ConfiguracionFormType::class, $formData);
        $form->handleRequest($request);

        if ($form->isSubmitted() && $form->isValid()) {
            $data = $form->getData();

            $configuration->set('MI_MODULO_NOMBRE',    $data['nombre']);
            $configuration->set('MI_MODULO_ACTIVO',    (int) $data['activo']);
            $configuration->set('MI_MODULO_COLOR',     $data['color']);
            $configuration->set('MI_MODULO_MAX_ITEMS', $data['max_items']);

            $this->addFlash('success', $this->trans(
                'Configuración guardada correctamente.',
                'Modules.Mimodulo.Admin'
            ));

            // PRG: redirigir tras el POST para evitar reenvíos al recargar
            return $this->redirectToRoute('mi_modulo_configuracion');
        }

        return $this->render(
            '@Modules/mi_modulo/views/templates/admin/configuracion.html.twig',
            [
                'form'        => $form->createView(),
                'layoutTitle' => $this->trans('Configuración', 'Modules.Mimodulo.Admin'),
                'help_link'   => false,
            ]
        );
    }
}
```

---

## 9.2 Definir rutas en routes.yml

```yaml
# Ejemplo 9.2 — Definición de rutas para los controladores del módulo
# Archivo: modules/mi_modulo/config/routes.yml

# Ruta de configuración (GET para mostrar, POST para guardar)
mi_modulo_configuracion:
  path: /mi-modulo/configuracion
  methods: [GET, POST]
  defaults:
    _controller: 'MiEmpresa\MiModulo\Controller\Admin\ConfiguracionController::indexAction'

# Ruta con parámetro numérico obligatorio
mi_modulo_editar:
  path: /mi-modulo/editar/{id}
  methods: [GET, POST]
  requirements:
    id: \d+
  defaults:
    _controller: 'MiEmpresa\MiModulo\Controller\Admin\EditarController::indexAction'

# Ruta AJAX para el toggle de estado
mi_modulo_toggle_estado:
  path: /mi-modulo/toggle/{id}
  methods: [POST]
  requirements:
    id: \d+
  defaults:
    _controller: 'MiEmpresa\MiModulo\Controller\Admin\ToggleController::indexAction'

# Ruta de eliminación
mi_modulo_eliminar:
  path: /mi-modulo/eliminar/{id}
  methods: [POST, DELETE]
  requirements:
    id: \d+
  defaults:
    _controller: 'MiEmpresa\MiModulo\Controller\Admin\EliminarController::indexAction'
```

---

## 9.3 Registrar Tabs de navegación

Los Tabs hacen que el módulo aparezca en el menú lateral del back office. El método más limpio desde PS 1.7.7+ es declarar la propiedad `$tabs` en la clase principal:

```php
<?php
// Ejemplo 9.3 — Declaración de Tabs mediante la propiedad $tabs
// Archivo: mi_modulo.php
// Disponible desde PrestaShop 1.7.7. Más limpio que instanciar Tab manualmente.

class MiModulo extends Module
{
    /**
     * Lista de tabs a registrar automáticamente en install().
     * PrestaShop los crea/elimina automáticamente con el módulo.
     */
    public $tabs = [
        // Tab raíz (visible en el menú lateral bajo "Mejorar")
        [
            'name'              => ['en' => 'My Module', 'es' => 'Mi Módulo'],
            'class_name'        => 'AdminMiModulo',
            'route_name'        => 'mi_modulo_configuracion',
            'parent_class_name' => 'IMPROVE',
            'module_tab'        => true,
            'icon'              => 'store',
            'visible'           => true,
        ],
        // Sub-tab (aparece anidado bajo el tab raíz)
        [
            'name'              => ['en' => 'Configuration', 'es' => 'Configuración'],
            'class_name'        => 'AdminMiModuloConfig',
            'route_name'        => 'mi_modulo_configuracion',
            'parent_class_name' => 'AdminMiModulo',
            'module_tab'        => true,
            'visible'           => true,
        ],
    ];
}
```

### Secciones del menú disponibles

| Valor `parent_class_name` | Sección en el menú |
|---|---|
| `IMPROVE` | "Mejorar" |
| `SELL` | "Vender" |
| `CONFIGURE` | "Configurar" |
| `AdminCatalog` | Dentro de "Catálogo" |
| `AdminOrders` | Dentro de "Pedidos" |
| `AdminCustomers` | Dentro de "Clientes" |
| `AdminShipping` | Dentro de "Transporte" |
| `AdminAdvParameters` | Dentro de "Parámetros avanzados" |

---

## 9.4 Controlador de front office con ModuleFrontController

```php
<?php
// Ejemplo 9.4 — Controlador del front office
// Archivo: controllers/front/comentarios.php
// Nota: Los controladores front NO son Symfony; extienden ModuleFrontController

class MiModuloComentariosModuleFrontController extends ModuleFrontController
{
    /** @var bool Si la página requiere autenticación del cliente */
    public $auth = false;
    /** @var bool Si se muestra la columna izquierda */
    public $display_column_left = false;

    public function initContent(): void
    {
        parent::initContent();

        $idProducto = (int) Tools::getValue('id_product');

        if ($idProducto <= 0) {
            $this->redirect_after = '404';
            $this->redirect();
            return;
        }

        // Los servicios declarados en config/front/services.yml están disponibles
        $service    = $this->module->get('mi_empresa.mi_modulo.comentario_service');
        $comentarios = $service->obtenerAprobadosPorProducto($idProducto);

        $this->context->smarty->assign([
            'comentarios'  => $comentarios,
            'id_producto'  => $idProducto,
        ]);

        $this->setTemplate('module:mi_modulo/views/templates/front/comentarios.tpl');
    }

    /**
     * Método para peticiones AJAX.
     * Se accede con: ?fc=module&module=mi_modulo&controller=comentarios&action=crearComentario
     */
    public function displayAjaxCrearComentario(): void
    {
        if (!Tools::isSubmit('token') || Tools::getToken(false) !== Tools::getValue('token')) {
            $this->ajaxResponse(['success' => false, 'message' => 'Token inválido']);
        }

        try {
            $service = $this->module->get('mi_empresa.mi_modulo.comentario_service');
            $comentario = $service->crearComentario(
                (int) Tools::getValue('id_product'),
                (int) $this->context->customer->id,
                Tools::getValue('titulo'),
                Tools::getValue('contenido'),
                (int) Tools::getValue('valoracion')
            );

            $this->ajaxResponse([
                'success' => true,
                'id'      => $comentario->getId(),
                'message' => $this->module->trans('Comentario enviado.', [], 'Modules.Mimodulo.Shop'),
            ]);
        } catch (\InvalidArgumentException $e) {
            $this->ajaxResponse(['success' => false, 'message' => $e->getMessage()]);
        }
    }

    private function ajaxResponse(array $data): never
    {
        header('Content-Type: application/json');
        die(json_encode($data));
    }
}
```

---

### Resumen del Capítulo 9

| Concepto | Clave a recordar |
|---|---|
| `FrameworkBundleAdminController` | Base para todos los controladores de BO en módulos modernos |
| `routes.yml` | Define la URL, métodos HTTP y el controlador para cada ruta |
| PRG pattern | Siempre redirigir con `redirectToRoute()` después de un POST exitoso |
| `$tabs` | La forma moderna de registrar tabs; PS los crea/elimina automáticamente |
| `ModuleFrontController` | Para el front office; no es Symfony, extiende la clase legacy |

**Próximo capítulo:** Capítulo 10 — Plantillas Twig en módulos.

---

# Capítulo 10: Plantillas Twig en módulos

> **En este capítulo aprenderás:**
> - La sintaxis de Twig comparada con Smarty
> - Cómo estructurar plantillas Twig en el back office de tu módulo
> - El namespace `@Modules/` para referenciar plantillas de módulos
> - Cómo renderizar hooks en plantillas Twig
> - Cómo crear extensiones Twig con filtros y funciones propias
>
> **Nivel:** Intermedio  
> **Versión mínima PS:** 8.0.0

---

## 10.1 Twig vs Smarty: guía de conversión rápida

| Operación | Smarty (front/BO legacy) | Twig (BO moderno) |
|---|---|---|
| Variable | `{$nombre}` | `{{ nombre }}` |
| Escape HTML | `{$var\|escape:'html'}` | `{{ var }}` ← automático |
| Escape manual | — | `{{ var\|e }}` |
| Condición | `{if $cond}...{/if}` | `{% if cond %}...{% endif %}` |
| Bucle | `{foreach $lista as $item}...{/foreach}` | `{% for item in lista %}...{% endfor %}` |
| Traducción | `{l s='Texto' mod='modulo'}` | `{{ 'Texto'\|trans({}, 'Modules.Modulo.Admin') }}` |
| Include | `{include file='template.tpl'}` | `{% include 'template.html.twig' %}` |
| Herencia | No disponible | `{% extends 'layout.html.twig' %}` |
| Bloque | — | `{% block content %}...{% endblock %}` |
| Comentario | `{* comentario *}` | `{# comentario #}` |
| HTML raw | `{$var nofilter}` | `{{ var\|raw }}` ← ¡solo con datos de confianza! |

> **Importante:** Twig escapa el HTML automáticamente. Esto es una ventaja de
> seguridad enorme. Solo usa `|raw` con contenido que hayas sanitizado tú mismo
> o que venga de fuentes de confianza del sistema (nunca de inputs del usuario).

---

## 10.2 Plantilla Twig para la página de configuración

```twig
{# Ejemplo 10.1 — Plantilla de configuración del back office #}
{# Archivo: views/templates/admin/configuracion.html.twig #}

{# Extender el layout base del back office de PrestaShop #}
{% extends '@PrestaShop/Admin/layout.html.twig' %}

{% block content %}
    {# Mostrar mensajes flash (se generan en el controlador con addFlash()) #}
    {% for message in app.flashes('success') %}
        <div class="alert alert-success alert-dismissible fade show">
            {{ message }}
        </div>
    {% endfor %}
    {% for message in app.flashes('error') %}
        <div class="alert alert-danger alert-dismissible fade show">
            {{ message }}
        </div>
    {% endfor %}

    {{ form_start(form) }}

    <div class="row">
        {# Columna principal con el formulario #}
        <div class="col-xl-8">
            <div class="card">
                <div class="card-header">
                    <h3 class="card-header-title">
                        {{ 'Configuración general'|trans({}, 'Modules.Mimodulo.Admin') }}
                    </h3>
                </div>

                <div class="card-body">
                    {# Renderizar campo a campo para control total del layout #}
                    <div class="form-group row">
                        <label class="form-control-label col-sm-3 required">
                            {{ form_label(form.nombre) }}
                        </label>
                        <div class="col-sm-9">
                            {{ form_widget(form.nombre) }}
                            <small class="form-text text-muted">
                                {{ form_help(form.nombre) }}
                            </small>
                            {{ form_errors(form.nombre) }}
                        </div>
                    </div>

                    <div class="form-group row">
                        <label class="form-control-label col-sm-3">
                            {{ form_label(form.activo) }}
                        </label>
                        <div class="col-sm-9">
                            {{ form_widget(form.activo) }}
                        </div>
                    </div>
                </div>

                <div class="card-footer">
                    <div class="d-flex justify-content-end">
                        <button type="submit" class="btn btn-primary">
                            <i class="material-icons">save</i>
                            {{ 'Guardar'|trans({}, 'Admin.Actions') }}
                        </button>
                    </div>
                </div>
            </div>
        </div>

        {# Columna lateral con información de ayuda #}
        <div class="col-xl-4">
            <div class="card">
                <div class="card-header">
                    <h3 class="card-header-title">
                        {{ 'Ayuda'|trans({}, 'Admin.Global') }}
                    </h3>
                </div>
                <div class="card-body">
                    <p>{{ 'Configura el comportamiento del módulo.'|trans({}, 'Modules.Mimodulo.Admin') }}</p>
                </div>
            </div>
        </div>
    </div>

    {{ form_end(form) }}
{% endblock %}
```

---

## 10.3 Namespace de plantillas de módulos

PrestaShop registra automáticamente un namespace Twig para cada módulo instalado:

```twig
{# Ejemplo 10.2 — Referenciar plantillas del módulo con el namespace @Modules/ #}

{# Desde un controlador PHP: #}
{#   return $this->render('@Modules/mi_modulo/views/templates/admin/index.html.twig'); #}

{# Desde otra plantilla Twig: #}
{% extends '@Modules/mi_modulo/views/templates/admin/layout.html.twig' %}
{% include '@Modules/mi_modulo/views/templates/admin/partials/tabla.html.twig' %}
```

---

## 10.4 Renderizar hooks en plantillas Twig

```twig
{# Ejemplo 10.3 — Disparar un hook desde una plantilla Twig del back office #}

<div class="card">
    <div class="card-body">
        {# Renderiza todos los módulos suscritos al hook, pasando parámetros #}
        {{ renderHook('displayAdminMiModuloExtra', {
            'id_producto': producto.id,
            'context': 'edicion'
        }) }}
    </div>
</div>
```

---

## 10.5 Usar Twig para responder a hooks del back office

```php
<?php
// Ejemplo 10.4 — Responder a un hook del BO devolviendo HTML de Twig
// Archivo: mi_modulo.php

public function hookDisplayAdminProductsExtra(array $params): string
{
    $idProduct  = (int) $params['id_product'];
    $datosExtra = $this->get('mi_empresa.mi_modulo.datos_service')
                       ->obtenerDatosExtra($idProduct);

    // Usar el motor Twig del módulo (disponible en el contexto Symfony)
    $twig = $this->get('twig');

    return $twig->render(
        '@Modules/mi_modulo/views/templates/hook/admin_product_extra.html.twig',
        [
            'datos_extra' => $datosExtra,
            'id_product'  => $idProduct,
        ]
    );
}
```

```twig
{# Ejemplo 10.5 — Plantilla Twig para el hook de producto #}
{# Archivo: views/templates/hook/admin_product_extra.html.twig #}

<div class="mi-modulo-product-extra">
    <h4>{{ 'Datos adicionales del módulo'|trans({}, 'Modules.Mimodulo.Admin') }}</h4>

    {% if datos_extra is not empty %}
        <table class="table table-striped">
            <thead>
                <tr>
                    <th>{{ 'Clave'|trans({}, 'Admin.Global') }}</th>
                    <th>{{ 'Valor'|trans({}, 'Admin.Global') }}</th>
                </tr>
            </thead>
            <tbody>
                {% for clave, valor in datos_extra %}
                    <tr>
                        <td><code>{{ clave }}</code></td>
                        <td>{{ valor }}</td>
                    </tr>
                {% endfor %}
            </tbody>
        </table>
    {% else %}
        <p class="text-muted">
            {{ 'Sin datos adicionales.'|trans({}, 'Modules.Mimodulo.Admin') }}
        </p>
    {% endif %}
</div>
```

---

## 10.6 Crear extensiones Twig personalizadas

```php
<?php
// Ejemplo 10.6 — Extensión Twig con filtros y funciones propias
// Archivo: src/Twig/MiModuloExtension.php

declare(strict_types=1);

namespace MiEmpresa\MiModulo\Twig;

use Twig\Extension\AbstractExtension;
use Twig\TwigFilter;
use Twig\TwigFunction;

class MiModuloExtension extends AbstractExtension
{
    public function getFilters(): array
    {
        return [
            new TwigFilter('precio', [$this, 'formatearPrecio']),
            new TwigFilter('truncar', [$this, 'truncarTexto']),
        ];
    }

    public function getFunctions(): array
    {
        return [
            // is_safe: html → Twig no escapará el resultado (lo generamos nosotros)
            new TwigFunction('estrellas', [$this, 'renderizarEstrellas'], ['is_safe' => ['html']]),
        ];
    }

    public function formatearPrecio(float $precio, string $moneda = 'EUR'): string
    {
        return number_format($precio, 2, ',', '.') . ' ' . $moneda;
    }

    public function truncarTexto(string $texto, int $longitud = 100): string
    {
        return mb_strlen($texto) > $longitud
            ? mb_substr($texto, 0, $longitud) . '…'
            : $texto;
    }

    public function renderizarEstrellas(int $valoracion, int $max = 5): string
    {
        $html = '<span class="mi-modulo-estrellas">';
        for ($i = 1; $i <= $max; $i++) {
            $filled = $i <= $valoracion ? 'filled' : 'empty';
            $html .= sprintf('<i class="material-icons star-%s">star</i>', $filled);
        }
        return $html . '</span>';
    }
}
```

```yaml
# Ejemplo 10.7 — Registrar la extensión Twig como servicio
# Archivo: config/services.yml

services:
  mi_empresa.mi_modulo.twig_extension:
    class: MiEmpresa\MiModulo\Twig\MiModuloExtension
    tags:
      - { name: twig.extension }
```

```twig
{# Uso en plantillas después de registrar la extensión #}

{{ producto.precio|precio('EUR') }}
{{ comentario.contenido|truncar(150) }}
{{ estrellas(valoracion.puntuacion, 5) }}
```

---

### Resumen del Capítulo 10

| Concepto | Clave a recordar |
|---|---|
| Escape automático | Twig escapa HTML por defecto; usar `|raw` solo con datos de confianza |
| `@Modules/` | Namespace para referenciar plantillas de tu módulo desde cualquier lugar |
| `renderHook()` | Función Twig para disparar hooks desde plantillas del BO |
| Extensiones Twig | Añaden filtros y funciones; declarar con el tag `twig.extension` |
| `form_start/end` | Helpers Twig para formularios Symfony; incluyen el token CSRF |

**Próximo capítulo:** Capítulo 11 — ObjectModel, el enfoque clásico.

---

# PARTE IV — GESTIÓN DE DATOS

> PrestaShop ofrece dos enfoques para gestionar datos: `ObjectModel` (clásico,
> disponible desde la versión 1.4) y Doctrine ORM (moderno, disponible desde 1.7.6).
> Esta parte explica ambos, cuándo usar cada uno y cómo migrar de uno al otro.

---

# Capítulo 11: ObjectModel — el enfoque clásico

> **En este capítulo aprenderás:**
> - La estructura de una entidad `ObjectModel` con soporte multilingüe
> - Cómo crear las tablas SQL correspondientes
> - Las operaciones CRUD con ObjectModel
> - Las limitaciones de ObjectModel que justifican la migración a Doctrine
>
> **Nivel:** Básico-Intermedio  
> **Versión mínima PS:** 1.5 (disponible en todas las versiones)

---

## 11.1 ¿Por qué conocer ObjectModel en 2024?

Aunque Doctrine ORM es el enfoque recomendado para módulos nuevos, ObjectModel sigue siendo relevante por estas razones:

| Situación | Usar ObjectModel |
|---|---|
| Módulo que necesita PS 1.6 / 1.7 compatible | Sí |
| Datos con soporte multilingüe nativo | Sí (tiene soporte built-in) |
| Datos con soporte multitienda nativo | Sí (tiene soporte built-in) |
| Interactuar con entidades del core (Product, Order...) | Necesario entenderlo |
| Nuevo módulo solo para PS 8+ | No, usar Doctrine |

---

## 11.2 Crear una entidad con ObjectModel

```php
<?php
// Ejemplo 11.1 — Entidad ObjectModel con soporte multilingüe
// Archivo: src/Entity/MiEntidadLegacy.php (o classes/MiEntidad.php)

if (!defined('_PS_VERSION_')) {
    exit;
}

class MiEntidad extends ObjectModel
{
    /** @var int ID del producto asociado */
    public $id_product;

    /** @var bool Si está activo */
    public $activo;

    /** @var string Fecha de creación (gestionada por PS automáticamente) */
    public $date_add;

    /** @var string Fecha de última modificación */
    public $date_upd;

    // Campos multilingüe (uno por idioma)
    /** @var string Nombre en el idioma actual */
    public $nombre;

    /** @var string Descripción en el idioma actual */
    public $descripcion;

    /**
     * @see ObjectModel::$definition
     * Este array es el "schema" de la entidad: define tabla, campos y validaciones.
     */
    public static $definition = [
        'table'    => 'mi_entidad',        // Nombre SIN prefijo ps_
        'primary'  => 'id_mi_entidad',      // Clave primaria
        'multilang' => true,                // Habilita soporte multilingüe
        'fields'   => [
            // ── Campos de la tabla principal (ps_mi_entidad) ──────────────────
            'id_product' => [
                'type'     => self::TYPE_INT,
                'validate' => 'isUnsignedId',
                'required' => true,
            ],
            'activo' => [
                'type'     => self::TYPE_BOOL,
                'validate' => 'isBool',
            ],
            'date_add' => [
                'type'     => self::TYPE_DATE,
                'validate' => 'isDate',
            ],
            'date_upd' => [
                'type'     => self::TYPE_DATE,
                'validate' => 'isDate',
            ],

            // ── Campos multilingüe (ps_mi_entidad_lang) ──────────────────────
            'nombre' => [
                'type'     => self::TYPE_STRING,
                'lang'     => true,            // ← indica campo multilingüe
                'validate' => 'isCleanHtml',
                'required' => true,
                'size'     => 255,
            ],
            'descripcion' => [
                'type'     => self::TYPE_HTML,
                'lang'     => true,
                'validate' => 'isCleanHtml',
            ],
        ],
    ];

    /**
     * Consulta estática para obtener entidades por producto.
     */
    public static function getByProduct(int $idProduct, int $idLang): array
    {
        return Db::getInstance()->executeS('
            SELECT e.*, el.nombre, el.descripcion
            FROM `' . _DB_PREFIX_ . 'mi_entidad` e
            LEFT JOIN `' . _DB_PREFIX_ . 'mi_entidad_lang` el
                   ON e.`id_mi_entidad` = el.`id_mi_entidad`
                  AND el.`id_lang` = ' . (int) $idLang . '
            WHERE e.`id_product` = ' . (int) $idProduct . '
              AND e.`activo` = 1
            ORDER BY e.`id_mi_entidad` ASC
        ');
    }
}
```

---

## 11.3 Crear las tablas SQL en install()

```php
<?php
// Ejemplo 11.2 — SQL para las tablas de un ObjectModel con multilingüe
// Los CREATE deben ir en el método installDatabase() del módulo

private function installDatabase(): bool
{
    // Tabla principal
    $sqlMain = 'CREATE TABLE IF NOT EXISTS `' . _DB_PREFIX_ . 'mi_entidad` (
        `id_mi_entidad` int(11) unsigned NOT NULL AUTO_INCREMENT,
        `id_product`    int(11) unsigned NOT NULL,
        `activo`        tinyint(1)       NOT NULL DEFAULT 1,
        `date_add`      datetime         NOT NULL,
        `date_upd`      datetime         NOT NULL,
        PRIMARY KEY (`id_mi_entidad`),
        KEY `id_product` (`id_product`)
    ) ENGINE=' . _MYSQL_ENGINE_ . ' DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;';

    if (!Db::getInstance()->execute($sqlMain)) {
        return false;
    }

    // Tabla multilingüe
    $sqlLang = 'CREATE TABLE IF NOT EXISTS `' . _DB_PREFIX_ . 'mi_entidad_lang` (
        `id_mi_entidad` int(11) unsigned NOT NULL,
        `id_lang`       int(11) unsigned NOT NULL,
        `id_shop`       int(11) unsigned NOT NULL DEFAULT 1,
        `nombre`        varchar(255)     NOT NULL,
        `descripcion`   text             NULL,
        PRIMARY KEY (`id_mi_entidad`, `id_lang`, `id_shop`)
    ) ENGINE=' . _MYSQL_ENGINE_ . ' DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;';

    return Db::getInstance()->execute($sqlLang);
}
```

---

## 11.4 Operaciones CRUD con ObjectModel

```php
<?php
// Ejemplo 11.3 — CRUD completo con ObjectModel

// ── CREAR ──────────────────────────────────────────────────────────────────
$entidad             = new MiEntidad();
$entidad->id_product = 5;
$entidad->activo     = true;
$entidad->date_add   = date('Y-m-d H:i:s');
$entidad->date_upd   = date('Y-m-d H:i:s');

// Valores multilingüe: un valor por ID de idioma
foreach (Language::getLanguages() as $lang) {
    $entidad->nombre[$lang['id_lang']]      = 'Nombre en ' . $lang['iso_code'];
    $entidad->descripcion[$lang['id_lang']] = 'Descripción en ' . $lang['iso_code'];
}

$entidad->add(); // → INSERT en ps_mi_entidad y ps_mi_entidad_lang

// ── LEER ───────────────────────────────────────────────────────────────────
// Por ID (carga en el idioma por defecto)
$entidad = new MiEntidad(5);

// Por ID con idioma específico
$entidad = new MiEntidad(5, Context::getContext()->language->id);

// Consulta personalizada (estática)
$lista = MiEntidad::getByProduct($idProduct, $idLang);

// ── ACTUALIZAR ─────────────────────────────────────────────────────────────
$entidad         = new MiEntidad(5);
$entidad->activo = false;
$entidad->update(); // → UPDATE en ps_mi_entidad

// ── ELIMINAR ───────────────────────────────────────────────────────────────
$entidad = new MiEntidad(5);
$entidad->delete(); // → DELETE en ps_mi_entidad y ps_mi_entidad_lang
```

---

### Resumen del Capítulo 11

| Concepto | Clave a recordar |
|---|---|
| `$definition` | Define tabla, clave primaria, campos y sus tipos/validaciones |
| `multilang => true` | Crea automáticamente la tabla `{tabla}_lang` |
| `add() / update() / delete()` | Las tres operaciones de escritura de ObjectModel |
| SQL manual | Las tablas se crean con SQL en `install()`; ObjectModel no las genera |
| Cuándo usarlo | Solo para compatibilidad PS 1.6/1.7 o para datos multilingüe/multitienda nativos |

**Próximo capítulo:** Capítulo 12 — Doctrine ORM, el enfoque moderno.

---

# Capítulo 12: Doctrine ORM — el enfoque moderno

> **En este capítulo aprenderás:**
> - Cómo definir entidades Doctrine con atributos PHP 8
> - Cómo crear repositorios con consultas personalizadas
> - La regla de oro: nunca usar `doctrine:schema:update` directamente
> - Cómo usar `EntityManager` para persistir y consultar datos
> - Las relaciones entre entidades (OneToMany, ManyToOne)
>
> **Módulos de referencia:** `demodoctrine`, `demoextendsymfonyform2`  
> **Nivel:** Intermedio  
> **Versión mínima PS:** 8.0.0 (disponible desde 1.7.6)

---

## 12.1 ObjectModel vs. Doctrine: cuándo usar cada uno

| Característica | ObjectModel | Doctrine ORM |
|---|---|---|
| Multilingüe nativo | ✓ | ✗ (implementación manual) |
| Multitienda nativo | ✓ | ✗ (implementación manual) |
| Testing fácil | ✗ | ✓ |
| Relaciones automáticas | ✗ | ✓ |
| QueryBuilder tipado | ✗ | ✓ |
| Caché de segundo nivel | ✗ | ✓ |
| Estándar de la industria | ✗ | ✓ |
| **Recomendado para PS 8+** | No | **Sí** |

---

## 12.2 Definir una entidad Doctrine

```php
<?php
// Ejemplo 12.1 — Entidad Doctrine con atributos PHP 8
// Archivo: src/Entity/Comentario.php
// Requiere: doctrine/orm (incluido en PrestaShop 8+)

declare(strict_types=1);

namespace MiEmpresa\MiModulo\Entity;

use Doctrine\ORM\Mapping as ORM;
use MiEmpresa\MiModulo\Repository\ComentarioRepository;

// Atributos PHP 8 (estilo moderno; Doctrine también soporta anotaciones @ORM\ en docblocks)
#[ORM\Table(name: 'ps_mi_modulo_comentario')]
#[ORM\Entity(repositoryClass: ComentarioRepository::class)]
class Comentario
{
    #[ORM\Id]
    #[ORM\Column(name: 'id_comentario', type: 'integer')]
    #[ORM\GeneratedValue(strategy: 'AUTO')]
    private ?int $id = null;

    #[ORM\Column(name: 'id_producto', type: 'integer')]
    private int $idProducto;

    #[ORM\Column(name: 'id_cliente', type: 'integer', nullable: true)]
    private ?int $idCliente = null;

    #[ORM\Column(type: 'string', length: 255)]
    private string $titulo;

    #[ORM\Column(type: 'text')]
    private string $contenido;

    #[ORM\Column(type: 'integer')]
    private int $valoracion;

    #[ORM\Column(type: 'boolean')]
    private bool $aprobado = false;

    #[ORM\Column(name: 'fecha_creacion', type: 'datetime_immutable')]
    private \DateTimeImmutable $fechaCreacion;

    // ── Getters y Setters ──────────────────────────────────────────────────

    public function getId(): ?int { return $this->id; }

    public function getIdProducto(): int { return $this->idProducto; }
    public function setIdProducto(int $v): static { $this->idProducto = $v; return $this; }

    public function getIdCliente(): ?int { return $this->idCliente; }
    public function setIdCliente(?int $v): static { $this->idCliente = $v; return $this; }

    public function getTitulo(): string { return $this->titulo; }
    public function setTitulo(string $v): static { $this->titulo = $v; return $this; }

    public function getContenido(): string { return $this->contenido; }
    public function setContenido(string $v): static { $this->contenido = $v; return $this; }

    public function getValoracion(): int { return $this->valoracion; }
    public function setValoracion(int $v): static { $this->valoracion = $v; return $this; }

    public function isAprobado(): bool { return $this->aprobado; }
    public function setAprobado(bool $v): static { $this->aprobado = $v; return $this; }

    public function getFechaCreacion(): \DateTimeImmutable { return $this->fechaCreacion; }
    public function setFechaCreacion(\DateTimeImmutable $v): static { $this->fechaCreacion = $v; return $this; }
}
```

---

## 12.3 Crear el repositorio

```php
<?php
// Ejemplo 12.2 — Repositorio Doctrine con consultas de negocio
// Archivo: src/Repository/ComentarioRepository.php

declare(strict_types=1);

namespace MiEmpresa\MiModulo\Repository;

use Doctrine\ORM\EntityRepository;
use MiEmpresa\MiModulo\Entity\Comentario;

class ComentarioRepository extends EntityRepository
{
    /**
     * Obtiene los comentarios aprobados de un producto, ordenados por fecha.
     *
     * @return Comentario[]
     */
    public function findAprobadosByProducto(int $idProducto): array
    {
        return $this->createQueryBuilder('c')
            ->where('c.idProducto = :idProducto')
            ->andWhere('c.aprobado = :aprobado')
            ->setParameter('idProducto', $idProducto)
            ->setParameter('aprobado', true)
            ->orderBy('c.fechaCreacion', 'DESC')
            ->getQuery()
            ->getResult();
    }

    /**
     * Obtiene los comentarios pendientes de aprobación.
     *
     * @return Comentario[]
     */
    public function findPendientes(): array
    {
        return $this->createQueryBuilder('c')
            ->where('c.aprobado = false')
            ->orderBy('c.fechaCreacion', 'ASC')
            ->getQuery()
            ->getResult();
    }

    /**
     * Calcula la valoración media de un producto (solo comentarios aprobados).
     */
    public function getValoracionMedia(int $idProducto): ?float
    {
        $result = $this->createQueryBuilder('c')
            ->select('AVG(c.valoracion) as media')
            ->where('c.idProducto = :id')
            ->andWhere('c.aprobado = true')
            ->setParameter('id', $idProducto)
            ->getQuery()
            ->getSingleScalarResult();

        return $result !== null ? round((float) $result, 1) : null;
    }
}
```

---

## 12.4 La regla de oro: crear las tablas manualmente

> **Advertencia:** **Nunca** ejecutes `php bin/console doctrine:schema:update --force`
> en un entorno PrestaShop. Este comando genera `FOREIGN KEY` hacia tablas del core
> que no son compatibles con la estructura de PrestaShop y pueden corromper la BD.
>
> El comando `--dump-sql` es útil solo como referencia para copiar el SQL generado
> y **eliminando manualmente** todas las líneas `FOREIGN KEY` antes de usarlo.

```php
<?php
// Ejemplo 12.3 — SQL manual para la entidad Comentario
// Basado en lo que generaría Doctrine, pero sin FOREIGN KEYs al core de PS

private function installDatabase(): bool
{
    $sql = 'CREATE TABLE IF NOT EXISTS `ps_mi_modulo_comentario` (
        `id_comentario`  int(11)         NOT NULL AUTO_INCREMENT,
        `id_producto`    int(11)         NOT NULL,
        `id_cliente`     int(11)         DEFAULT NULL,
        `titulo`         varchar(255)    NOT NULL,
        `contenido`      longtext        NOT NULL,
        `valoracion`     int(11)         NOT NULL,
        `aprobado`       tinyint(1)      NOT NULL DEFAULT 0,
        `fecha_creacion` datetime        NOT NULL COMMENT \'(DC2Type:datetime_immutable)\',
        PRIMARY KEY (`id_comentario`),
        KEY `IDX_id_producto` (`id_producto`),
        KEY `IDX_id_cliente`  (`id_cliente`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci';
    // NOTA: Sin FOREIGN KEY a ps_product ni ps_customer

    return Db::getInstance()->execute($sql);
}
```

---

## 12.5 Operaciones CRUD con EntityManager

```php
<?php
// Ejemplo 12.4 — CRUD completo con Doctrine EntityManager

// Obtener el EntityManager (en un servicio, se inyecta en el constructor)
$em = $this->get('doctrine.orm.entity_manager'); // En el módulo principal
// En un servicio: se inyecta como EntityManagerInterface

// ── CREAR ──────────────────────────────────────────────────────────────────
$comentario = new Comentario();
$comentario
    ->setIdProducto(5)
    ->setIdCliente(10)
    ->setTitulo('Excelente producto')
    ->setContenido('Lo recomiendo al 100%')
    ->setValoracion(5)
    ->setFechaCreacion(new \DateTimeImmutable())
    ->setAprobado(false);

$em->persist($comentario); // Registra el objeto para ser guardado
$em->flush();              // Ejecuta el INSERT en la BD

// ── LEER ───────────────────────────────────────────────────────────────────
$repo = $em->getRepository(Comentario::class);

$comentario = $repo->find(5);                              // Por ID
$comentarios = $repo->findAll();                           // Todos
$comentarios = $repo->findBy(['aprobado' => true]);        // Por criterio
$comentario = $repo->findOneBy(['titulo' => 'Hola']);      // Uno por criterio
$aprobados  = $repo->findAprobadosByProducto(5);           // Consulta personalizada

// ── ACTUALIZAR ─────────────────────────────────────────────────────────────
$comentario = $repo->find(5);
$comentario->setAprobado(true);
$em->flush(); // Ejecuta el UPDATE automáticamente (Doctrine detecta el cambio)

// ── ELIMINAR ───────────────────────────────────────────────────────────────
$comentario = $repo->find(5);
$em->remove($comentario);
$em->flush(); // Ejecuta el DELETE
```

---

### Resumen del Capítulo 12

| Concepto | Clave a recordar |
|---|---|
| Atributos PHP 8 | `#[ORM\Column(...)]` es el estilo moderno para mapear campos |
| `persist()` + `flush()` | `persist` registra, `flush` ejecuta contra la BD |
| `EntityRepository` | Extiende con métodos de consulta específicos del negocio |
| SQL manual | Crear tablas en `install()`; nunca usar `doctrine:schema:update --force` |
| Sin FOREIGN KEY al core | Las claves foráneas a tablas de PS causan incompatibilidades |

**Próximo capítulo:** Capítulo 13 — Consultas avanzadas y repositorios.

---

# Capítulo 13: Consultas avanzadas y repositorios

> **En este capítulo aprenderás:**
> - El QueryBuilder de Doctrine para consultas complejas
> - Cuándo combinar Doctrine con SQL nativo de PrestaShop
> - Paginación, ordenación y filtrado en repositorios
>
> **Módulos de referencia:** `demodoctrine`, `demo_grid`  
> **Nivel:** Intermedio-Avanzado  
> **Versión mínima PS:** 8.0.0

---

## 13.1 QueryBuilder avanzado

```php
<?php
// Ejemplo 13.1 — Consultas complejas con el QueryBuilder de Doctrine

public function buscarConFiltros(array $filtros, int $offset = 0, int $limit = 20): array
{
    $qb = $this->createQueryBuilder('c');

    // Filtro por texto (LIKE)
    if (!empty($filtros['titulo'])) {
        $qb->andWhere('c.titulo LIKE :titulo')
           ->setParameter('titulo', '%' . $filtros['titulo'] . '%');
    }

    // Filtro por estado booleano
    if (isset($filtros['aprobado'])) {
        $qb->andWhere('c.aprobado = :aprobado')
           ->setParameter('aprobado', (bool) $filtros['aprobado']);
    }

    // Filtro por rango de fechas
    if (!empty($filtros['fecha_desde'])) {
        $qb->andWhere('c.fechaCreacion >= :desde')
           ->setParameter('desde', new \DateTimeImmutable($filtros['fecha_desde']));
    }

    if (!empty($filtros['fecha_hasta'])) {
        $qb->andWhere('c.fechaCreacion <= :hasta')
           ->setParameter('hasta', new \DateTimeImmutable($filtros['fecha_hasta']));
    }

    // Ordenación dinámica (validar el campo para evitar inyección)
    $allowedOrderBy = ['fechaCreacion', 'valoracion', 'titulo', 'idProducto'];
    $orderBy  = in_array($filtros['order_by'] ?? '', $allowedOrderBy, true)
                ? $filtros['order_by']
                : 'fechaCreacion';
    $orderDir = strtoupper($filtros['order_dir'] ?? 'DESC') === 'ASC' ? 'ASC' : 'DESC';

    $qb->orderBy("c.{$orderBy}", $orderDir)
       ->setFirstResult($offset)
       ->setMaxResults($limit);

    return $qb->getQuery()->getResult();
}

/**
 * Contar resultados sin paginación (para calcular el total de páginas).
 */
public function countConFiltros(array $filtros): int
{
    $qb = $this->createQueryBuilder('c')->select('COUNT(c.id)');

    // Aplicar los mismos filtros (sin offset/limit)
    if (!empty($filtros['titulo'])) {
        $qb->andWhere('c.titulo LIKE :titulo')
           ->setParameter('titulo', '%' . $filtros['titulo'] . '%');
    }

    return (int) $qb->getQuery()->getSingleScalarResult();
}
```

---

## 13.2 Cuándo usar SQL nativo con Db::getInstance()

Doctrine es potente, pero a veces las consultas son tan complejas (múltiples JOINs con tablas del core de PS, funciones de agregación, subconsultas) que es más claro y eficiente usar SQL nativo:

```php
<?php
// Ejemplo 13.2 — SQL nativo para estadísticas complejas
// Combinar Doctrine y SQL nativo es perfectamente aceptable en módulos PS

public function getEstadisticasProducto(int $idProducto): ?array
{
    return Db::getInstance()->getRow('
        SELECT
            COUNT(*)                                                        AS total,
            ROUND(AVG(valoracion), 1)                                       AS media,
            SUM(CASE WHEN aprobado = 1 THEN 1 ELSE 0 END)                  AS aprobados,
            SUM(CASE WHEN aprobado = 0 THEN 1 ELSE 0 END)                  AS pendientes,
            SUM(CASE WHEN valoracion = 5 THEN 1 ELSE 0 END)                AS cinco_estrellas
        FROM `ps_mi_modulo_comentario`
        WHERE `id_producto` = ' . (int) $idProducto . '
    ');
}
```

> **Regla:** Usa Doctrine cuando la consulta se puede expresar limpiamente con
> QueryBuilder o DQL. Usa `Db::getInstance()` cuando necesites SQL específico de
> MySQL/MariaDB, funciones de agregación complejas o JOINs con muchas tablas del core.

---

### Resumen del Capítulo 13

| Concepto | Clave a recordar |
|---|---|
| QueryBuilder | Preferido para consultas con filtros dinámicos; encadena métodos `where`, `orderBy`, etc. |
| Paginación | `setFirstResult($offset)` + `setMaxResults($limit)` |
| SQL nativo | Usar `Db::getInstance()` para consultas complejas o de rendimiento crítico |
| Validar `orderBy` | Nunca pasar directamente un input del usuario como campo de ordenación |
| `getSingleScalarResult()` | Para consultas que devuelven un único valor (COUNT, AVG, etc.) |

**Próximo capítulo:** Capítulo 14 — Formularios Symfony en módulos.

---

*[Continúa en parte3_nuevo.md]*
# PARTE V — FORMULARIOS

> Los formularios son el principal punto de interacción del administrador con los datos
> del módulo. Esta parte cubre la creación de formularios propios y la extensión de
> los formularios del core de PrestaShop, desde lo más simple hasta el patrón CQRS.

---

# Capítulo 14: Formularios Symfony en módulos

> **En este capítulo aprenderás:**
> - Cómo crear un `FormType` de Symfony para la configuración del módulo
> - Los tipos de campo estándar de Symfony y los específicos de PrestaShop
> - Cómo añadir validaciones automáticas con `constraints`
> - El tipo `TranslatableType` para campos multilingüe
> - Cómo renderizar el formulario en una plantilla Twig
>
> **Módulos de referencia:** `demosymfonyformsimple`, `demosymfonyform`  
> **Nivel:** Intermedio  
> **Versión mínima PS:** 8.0.0

---

## 14.1 Crear un FormType de Symfony

```php
<?php
// Ejemplo 14.1 — FormType completo para la configuración de un módulo
// Archivo: src/Form/Type/ConfiguracionFormType.php

declare(strict_types=1);

namespace MiEmpresa\MiModulo\Form\Type;

use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\Extension\Core\Type\CheckboxType;
use Symfony\Component\Form\Extension\Core\Type\ChoiceType;
use Symfony\Component\Form\Extension\Core\Type\ColorType;
use Symfony\Component\Form\Extension\Core\Type\IntegerType;
use Symfony\Component\Form\Extension\Core\Type\TextareaType;
use Symfony\Component\Form\Extension\Core\Type\TextType;
use Symfony\Component\Form\FormBuilderInterface;
use Symfony\Component\OptionsResolver\OptionsResolver;
use Symfony\Component\Validator\Constraints\Length;
use Symfony\Component\Validator\Constraints\NotBlank;
use Symfony\Component\Validator\Constraints\Range;
use Symfony\Contracts\Translation\TranslatorInterface;

class ConfiguracionFormType extends AbstractType
{
    public function __construct(
        private readonly TranslatorInterface $translator
    ) {
    }

    public function buildForm(FormBuilderInterface $builder, array $options): void
    {
        $builder
            // ── Campo de texto con validación ─────────────────────────────────
            ->add('nombre', TextType::class, [
                'label'       => $this->translator->trans('Nombre a mostrar', [], 'Modules.Mimodulo.Admin'),
                'help'        => $this->translator->trans('Este texto se mostrará en la tienda.', [], 'Modules.Mimodulo.Admin'),
                'required'    => true,
                'constraints' => [
                    new NotBlank(['message' => 'El nombre es obligatorio.']),
                    new Length(['max' => 255, 'maxMessage' => 'Máximo 255 caracteres.']),
                ],
                'attr' => ['placeholder' => 'Ej: Bienvenidos a nuestra tienda'],
            ])

            // ── Área de texto ─────────────────────────────────────────────────
            ->add('descripcion', TextareaType::class, [
                'label'    => $this->translator->trans('Descripción', [], 'Modules.Mimodulo.Admin'),
                'required' => false,
                'attr'     => ['rows' => 4],
            ])

            // ── Checkbox booleano ─────────────────────────────────────────────
            ->add('activo', CheckboxType::class, [
                'label'    => $this->translator->trans('Módulo activo', [], 'Modules.Mimodulo.Admin'),
                'required' => false, // Los checkboxes siempre son required: false
            ])

            // ── Selector de opciones (select / radio) ─────────────────────────
            ->add('posicion', ChoiceType::class, [
                'label'    => $this->translator->trans('Posición', [], 'Modules.Mimodulo.Admin'),
                'choices'  => [
                    'Izquierda' => 'left',
                    'Centro'    => 'center',
                    'Derecha'   => 'right',
                ],
                'expanded' => false, // true = radio buttons | false = <select>
                'multiple' => false,
            ])

            // ── Entero con rango ──────────────────────────────────────────────
            ->add('max_items', IntegerType::class, [
                'label'       => $this->translator->trans('Máximo de elementos', [], 'Modules.Mimodulo.Admin'),
                'required'    => false,
                'constraints' => [new Range(['min' => 1, 'max' => 100])],
                'attr'        => ['min' => 1, 'max' => 100],
            ])

            // ── Selector de color ─────────────────────────────────────────────
            ->add('color', ColorType::class, [
                'label'    => $this->translator->trans('Color principal', [], 'Modules.Mimodulo.Admin'),
                'required' => false,
            ]);
    }

    public function configureOptions(OptionsResolver $resolver): void
    {
        $resolver->setDefaults([
            'csrf_protection' => true, // Token CSRF automático
        ]);
    }
}
```

---

## 14.2 Tipos de campo específicos de PrestaShop

Además de los tipos estándar de Symfony, PrestaShop proporciona tipos propios:

```php
<?php
// Ejemplo 14.2 — Tipos de campo específicos de PrestaShop
// Se importan desde el namespace PrestaShopBundle\Form\Admin\Type\

use PrestaShopBundle\Form\Admin\Type\SwitchType;         // Toggle on/off Bootstrap
use PrestaShopBundle\Form\Admin\Type\TranslatableType;   // Campo por cada idioma
use PrestaShopBundle\Form\Admin\Type\CategoryChoiceTreeType; // Árbol de categorías
use PrestaShopBundle\Form\Admin\Type\CountryChoiceType;  // Selector de país
use PrestaShopBundle\Form\Admin\Type\CurrencyChoiceType; // Selector de moneda
use PrestaShopBundle\Form\Admin\Type\LanguageChoiceType; // Selector de idioma
use PrestaShopBundle\Form\Admin\Type\ShopChoiceTreeType; // Árbol de tiendas (multitienda)

$builder
    // Toggle on/off con estilo PrestaShop (más visual que CheckboxType)
    ->add('activo', SwitchType::class, [
        'label' => 'Módulo activo',
    ])

    // Un campo de texto por cada idioma instalado en la tienda
    ->add('titulo', TranslatableType::class, [
        'label'   => 'Título',
        'type'    => TextType::class,
        'options' => [
            'required'    => true,
            'constraints' => [new NotBlank()],
        ],
    ])

    // Selector de categoría en árbol desplegable
    ->add('categoria', CategoryChoiceTreeType::class, [
        'label'    => 'Categoría',
        'required' => false,
    ])

    // Selector de país
    ->add('pais', CountryChoiceType::class, [
        'label' => 'País',
    ]);
```

---

## 14.3 Renderizar el formulario en Twig

```twig
{# Ejemplo 14.3 — Renderizado de formulario Symfony en el back office #}
{# Archivo: views/templates/admin/configuracion.html.twig #}

{% extends '@PrestaShop/Admin/layout.html.twig' %}

{% block content %}
{{ form_start(form) }}

<div class="card">
    <div class="card-header">
        <h3 class="card-header-title">
            {{ 'Configuración'|trans({}, 'Modules.Mimodulo.Admin') }}
        </h3>
    </div>

    <div class="card-body">
        {# Opción 1: Renderizar todos los campos de una vez (más rápido) #}
        {{ form_rest(form) }}

        {# Opción 2: Renderizar campo a campo (más control del layout) #}
        {#
        <div class="form-group row">
            <label class="form-control-label col-sm-3 required">
                {{ form_label(form.nombre) }}
            </label>
            <div class="col-sm-9">
                {{ form_widget(form.nombre) }}
                <small class="form-text text-muted">{{ form_help(form.nombre) }}</small>
                {{ form_errors(form.nombre) }}
            </div>
        </div>
        #}
    </div>

    <div class="card-footer">
        <button type="submit" class="btn btn-primary float-right">
            <i class="material-icons">save</i>
            {{ 'Guardar'|trans({}, 'Admin.Actions') }}
        </button>
    </div>
</div>

{{ form_end(form) }}
{% endblock %}
```

---

### Resumen del Capítulo 14

| Concepto | Clave a recordar |
|---|---|
| `AbstractType` | Clase base para todos los FormTypes de Symfony |
| `buildForm()` | Define los campos, tipos, opciones y constraints |
| `TranslatableType` | Genera un campo por cada idioma instalado |
| `SwitchType` | Toggle visual específico de PrestaShop |
| `form_rest(form)` | Renderiza todos los campos pendientes de una vez en Twig |
| `csrf_protection: true` | Token CSRF incluido automáticamente; no gestionarlo manualmente |

**Próximo capítulo:** Capítulo 15 — Extender formularios existentes del core.

---

# Capítulo 15: Extender formularios existentes del core

> **En este capítulo aprenderás:**
> - Cómo añadir campos personalizados a formularios del core (cliente, producto, etc.)
> - Los tres hooks necesarios: constructor, post-create y post-update
> - Cómo extender también el grid del listado para mostrar los datos nuevos
> - La progresión de complejidad de la serie `demoextendsymfonyform`
>
> **Módulos de referencia:** `demoextendsymfonyform1`, `demoextendsymfonyform2`, `demoextendsymfonyform3`  
> **Nivel:** Intermedio  
> **Versión mínima PS:** 8.0.0

---

## 15.1 La serie demoextendsymfonyform — progresión

| Módulo | Lo que añade | Patrón usado |
|---|---|---|
| `demoextendsymfonyform1` | Campo de texto al formulario de clientes | Db nativo |
| `demoextendsymfonyform2` | Campo de imagen + persistencia | Doctrine ORM |
| `demoextendsymfonyform3` | Campo de texto con persistencia CQRS | CQRS completo |

---

## 15.2 Los hooks de extensión de formularios

Para extender el formulario de clientes se necesitan exactamente tres hooks:

```php
<?php
// Ejemplo 15.1 — Registrar los tres hooks de extensión de formulario
// Patrón idéntico para cualquier entidad (cliente, producto, categoría, etc.)

public function install(): bool
{
    return parent::install()
        // 1. Se llama cuando Symfony construye el formulario
        && $this->registerHook('actionCustomerFormBuilderModifier')
        // 2. Se llama DESPUÉS de crear el cliente con éxito
        && $this->registerHook('actionAfterCreateCustomerFormHandler')
        // 3. Se llama DESPUÉS de actualizar el cliente con éxito
        && $this->registerHook('actionAfterUpdateCustomerFormHandler');
}
```

---

## 15.3 Implementar el hook del formulario

```php
<?php
// Ejemplo 15.2 — Hook que añade un campo al formulario de clientes del BO
// Este hook permite modificar el FormBuilder de Symfony del formulario de cliente

use Symfony\Component\Form\Extension\Core\Type\TextType;
use Symfony\Component\Validator\Constraints\Length;

/**
 * @param array $params
 *   - 'form_builder' FormBuilderInterface  El constructor del formulario
 *   - 'id'           int|null              ID del cliente (null = cliente nuevo)
 *   - 'data'         array                 Datos actuales del formulario
 */
public function hookActionCustomerFormBuilderModifier(array $params): void
{
    /** @var \Symfony\Component\Form\FormBuilderInterface $formBuilder */
    $formBuilder = $params['form_builder'];
    $idCustomer  = $params['id'] ?? null;

    // Obtener el valor actual si estamos editando un cliente existente
    $codigoExterno = '';
    if ($idCustomer !== null) {
        $codigoExterno = (string) Db::getInstance()->getValue(
            'SELECT `codigo_externo`
             FROM `' . _DB_PREFIX_ . 'mi_modulo_cliente_extra`
             WHERE `id_customer` = ' . (int) $idCustomer
        );
    }

    // Añadir el nuevo campo al FormBuilder de Symfony
    $formBuilder->add('mi_modulo_codigo_externo', TextType::class, [
        'label'       => $this->trans('Código externo', [], 'Modules.Mimodulo.Admin'),
        'help'        => $this->trans('ID del cliente en el sistema externo.', [], 'Modules.Mimodulo.Admin'),
        'required'    => false,
        'constraints' => [new Length(['max' => 64])],
    ]);

    // IMPORTANTE: actualizar los datos del formulario con el valor actual
    // para que el campo aparezca pre-relleno al editar un cliente existente
    $params['data']['mi_modulo_codigo_externo'] = $codigoExterno;
    $formBuilder->setData($params['data']);
}
```

---

## 15.4 Guardar los datos del campo personalizado

```php
<?php
// Ejemplo 15.3 — Guardar el campo personalizado tras crear/actualizar el cliente

/**
 * @param array $params
 *   - 'id'        int   ID del cliente creado/actualizado
 *   - 'form_data' array Datos del formulario enviado
 */
public function hookActionAfterCreateCustomerFormHandler(array $params): void
{
    $this->guardarCodigoExterno($params);
}

public function hookActionAfterUpdateCustomerFormHandler(array $params): void
{
    $this->guardarCodigoExterno($params);
}

private function guardarCodigoExterno(array $params): void
{
    $idCustomer = (int) $params['id'];
    $formData   = $params['form_data'];

    // Verificar que nuestro campo está presente en los datos del formulario
    if (!array_key_exists('mi_modulo_codigo_externo', $formData)) {
        return;
    }

    $codigoExterno = pSQL($formData['mi_modulo_codigo_externo'] ?? '');

    // INSERT ... ON DUPLICATE KEY UPDATE: crea o actualiza en una sola consulta
    Db::getInstance()->execute('
        INSERT INTO `' . _DB_PREFIX_ . 'mi_modulo_cliente_extra`
            (`id_customer`, `codigo_externo`)
        VALUES
            (' . $idCustomer . ', "' . $codigoExterno . '")
        ON DUPLICATE KEY UPDATE
            `codigo_externo` = "' . $codigoExterno . '"
    ');
}
```

---

## 15.5 Extender el grid de clientes para mostrar el campo nuevo

```php
<?php
// Ejemplo 15.4 — Añadir una columna al grid de clientes del back office

use PrestaShop\PrestaShop\Core\Grid\Column\Type\DataColumn;
use PrestaShop\PrestaShop\Core\Search\Filters\CustomerFilters;
use Symfony\Component\Form\Extension\Core\Type\TextType;

public function install(): bool
{
    return parent::install()
        && $this->registerHook('actionCustomerFormBuilderModifier')
        && $this->registerHook('actionAfterCreateCustomerFormHandler')
        && $this->registerHook('actionAfterUpdateCustomerFormHandler')
        // Hooks para modificar el grid del listado de clientes
        && $this->registerHook('actionCustomerGridDefinitionModifier')
        && $this->registerHook('actionCustomerGridQueryBuilderModifier');
}

/**
 * Añade la columna 'codigo_externo' al grid de clientes.
 */
public function hookActionCustomerGridDefinitionModifier(array $params): void
{
    /** @var \PrestaShop\PrestaShop\Core\Grid\Definition\GridDefinitionInterface $definition */
    $definition = $params['definition'];

    // Añadir columna después de la columna 'email'
    $definition->getColumns()->addAfter(
        'email',
        (new DataColumn('codigo_externo'))
            ->setName($this->trans('Cód. Externo', [], 'Modules.Mimodulo.Admin'))
            ->setOptions(['field' => 'codigo_externo', 'sortable' => true])
    );

    // Añadir filtro de búsqueda para la nueva columna
    $definition->getFilters()->add(
        (new \PrestaShop\PrestaShop\Core\Grid\Filter\Filter('codigo_externo', TextType::class))
            ->setTypeOptions([
                'required' => false,
                'attr'     => ['placeholder' => $this->trans('Buscar código...', [], 'Modules.Mimodulo.Admin')],
            ])
            ->setAssociatedColumn('codigo_externo')
    );
}

/**
 * Modifica la consulta del grid para incluir la columna de nuestra tabla.
 */
public function hookActionCustomerGridQueryBuilderModifier(array $params): void
{
    /** @var \Doctrine\DBAL\Query\QueryBuilder $qb */
    $qb             = $params['search_query_builder'];
    $searchCriteria = $params['search_criteria'];

    // JOIN con nuestra tabla extra de clientes
    $qb->addSelect('cex.codigo_externo')
       ->leftJoin(
           'c',                                          // Alias de ps_customer
           _DB_PREFIX_ . 'mi_modulo_cliente_extra',
           'cex',
           'c.id_customer = cex.id_customer'
       );

    // Aplicar el filtro si se especificó desde el formulario del grid
    $filtroValor = $searchCriteria->getFilters()['codigo_externo'] ?? null;
    if (!empty($filtroValor)) {
        $qb->andWhere('cex.codigo_externo LIKE :codigoExterno')
           ->setParameter('codigoExterno', '%' . $filtroValor . '%');
    }
}
```

---

## 15.6 Tabla de hooks de extensión disponibles

| Entidad del core | Hook de formulario | Post-crear | Post-actualizar |
|---|---|---|---|
| Cliente | `actionCustomerFormBuilderModifier` | `actionAfterCreateCustomerFormHandler` | `actionAfterUpdateCustomerFormHandler` |
| Producto | `actionProductFormBuilderModifier` | `actionAfterCreateProductFormHandler` | `actionAfterUpdateProductFormHandler` |
| Categoría | `actionCategoryFormBuilderModifier` | `actionAfterCreateCategoryFormHandler` | `actionAfterUpdateCategoryFormHandler` |
| Empleado | `actionEmployeeFormBuilderModifier` | `actionAfterCreateEmployeeFormHandler` | `actionAfterUpdateEmployeeFormHandler` |
| Fabricante | `actionManufacturerFormBuilderModifier` | `actionAfterCreateManufacturerFormHandler` | `actionAfterUpdateManufacturerFormHandler` |
| Proveedor | `actionSupplierFormBuilderModifier` | `actionAfterCreateSupplierFormHandler` | `actionAfterUpdateSupplierFormHandler` |
| Dirección | `actionAddressFormBuilderModifier` | `actionAfterCreateAddressFormHandler` | `actionAfterUpdateAddressFormHandler` |

---

### Resumen del Capítulo 15

| Concepto | Clave a recordar |
|---|---|
| Tres hooks | Constructor del form + post-crear + post-actualizar (siempre los tres) |
| `$params['data']` | Actualizar siempre con `$formBuilder->setData()` para pre-rellenar al editar |
| `pSQL()` | Sanitizar siempre los valores antes de guardarlos con SQL nativo |
| `addAfter()` | Insertar la columna del grid en la posición deseada |
| Grid query modifier | Hacer el JOIN con la tabla del módulo para que el dato esté disponible |

**Próximo capítulo:** Capítulo 16 — Subida de ficheros y datos complejos.

---

# PARTE VI — INTERFAZ DE ADMINISTRACIÓN

> El back office de PrestaShop moderno usa el Grid component para listados y
> controladores Symfony para páginas de gestión. Esta parte explica cómo
> construir interfaces de administración completas y profesionales.

---

# Capítulo 17: El componente Grid

> **En este capítulo aprenderás:**
> - La arquitectura del Grid component: definición, datos y criterios de búsqueda
> - Cómo implementar columnas, filtros, acciones de fila y acciones masivas
> - Cómo crear la fábrica de datos con Doctrine DBAL
> - Cómo conectar todo en un controlador y una plantilla Twig
>
> **Módulos de referencia:** `demo_grid`, `demoextendgrid`  
> **Nivel:** Intermedio  
> **Versión mínima PS:** 8.0.0

---

## 17.1 Arquitectura del Grid component

El Grid component tiene tres partes bien diferenciadas:

```
┌─────────────────────────────────────────────────────────────┐
│                     GRID COMPONENT                          │
│                                                             │
│  ┌──────────────────────┐                                   │
│  │  GridDefinition       │ ← Estructura: columnas, filtros, │
│  │  Factory              │   acciones de fila y masivas     │
│  └──────────────────────┘                                   │
│                                                             │
│  ┌──────────────────────┐                                   │
│  │  GridData             │ ← Datos: la consulta SQL que     │
│  │  Factory              │   alimenta el grid               │
│  └──────────────────────┘                                   │
│                                                             │
│  ┌──────────────────────┐                                   │
│  │  SearchCriteria       │ ← Estado: filtros activos,       │
│  │  (Filters)            │   ordenación, página actual      │
│  └──────────────────────┘                                   │
└─────────────────────────────────────────────────────────────┘
```

---

## 17.2 La fábrica de definición del Grid

```php
<?php
// Ejemplo 17.1 — Definición completa del Grid de comentarios
// Archivo: src/Grid/Definition/Factory/ComentariosGridDefinitionFactory.php

declare(strict_types=1);

namespace MiEmpresa\MiModulo\Grid\Definition\Factory;

use PrestaShop\PrestaShop\Core\Grid\Action\Bulk\BulkActionCollection;
use PrestaShop\PrestaShop\Core\Grid\Action\Bulk\Type\SubmitBulkAction;
use PrestaShop\PrestaShop\Core\Grid\Action\GridActionCollection;
use PrestaShop\PrestaShop\Core\Grid\Action\Row\RowActionCollection;
use PrestaShop\PrestaShop\Core\Grid\Action\Row\Type\LinkRowAction;
use PrestaShop\PrestaShop\Core\Grid\Action\Row\Type\SubmitRowAction;
use PrestaShop\PrestaShop\Core\Grid\Action\Type\LinkGridAction;
use PrestaShop\PrestaShop\Core\Grid\Column\ColumnCollection;
use PrestaShop\PrestaShop\Core\Grid\Column\Type\Common\ActionColumn;
use PrestaShop\PrestaShop\Core\Grid\Column\Type\Common\BulkActionColumn;
use PrestaShop\PrestaShop\Core\Grid\Column\Type\Common\DateTimeColumn;
use PrestaShop\PrestaShop\Core\Grid\Column\Type\Common\ToggleColumn;
use PrestaShop\PrestaShop\Core\Grid\Column\Type\DataColumn;
use PrestaShop\PrestaShop\Core\Grid\Definition\Factory\AbstractGridDefinitionFactory;
use PrestaShop\PrestaShop\Core\Grid\Filter\Filter;
use PrestaShop\PrestaShop\Core\Grid\Filter\FilterCollection;
use Symfony\Component\Form\Extension\Core\Type\ChoiceType;
use Symfony\Component\Form\Extension\Core\Type\NumberType;
use Symfony\Component\Form\Extension\Core\Type\TextType;

final class ComentariosGridDefinitionFactory extends AbstractGridDefinitionFactory
{
    /** Identificador único del grid; se usa en URLs y en los hooks de extensión */
    public const GRID_ID = 'comentarios';

    protected function getId(): string { return self::GRID_ID; }

    protected function getName(): string
    {
        return $this->trans('Comentarios', [], 'Modules.Mimodulo.Admin');
    }

    protected function getColumns(): ColumnCollection
    {
        return (new ColumnCollection())
            // Columna de checkboxes para acciones masivas
            ->add((new BulkActionColumn('bulk'))
                ->setOptions(['bulk_field' => 'id_comentario']))

            // Columna ID (ordenable)
            ->add((new DataColumn('id_comentario'))
                ->setName($this->trans('ID', [], 'Admin.Global'))
                ->setOptions(['field' => 'id_comentario', 'sortable' => true]))

            // Columna de texto
            ->add((new DataColumn('titulo'))
                ->setName($this->trans('Título', [], 'Modules.Mimodulo.Admin'))
                ->setOptions(['field' => 'titulo', 'sortable' => true]))

            // Columna con dato de tabla relacionada (via JOIN en la data factory)
            ->add((new DataColumn('nombre_producto'))
                ->setName($this->trans('Producto', [], 'Admin.Catalog.Feature'))
                ->setOptions(['field' => 'nombre_producto', 'sortable' => false]))

            // Columna toggle (activa/desactiva con un clic)
            ->add((new ToggleColumn('aprobado'))
                ->setName($this->trans('Aprobado', [], 'Modules.Mimodulo.Admin'))
                ->setOptions([
                    'field'            => 'aprobado',
                    'primary_field'    => 'id_comentario',
                    'route'            => 'mi_modulo_comentarios_toggle',
                    'route_param_name' => 'id',
                    'route_param_field' => 'id_comentario',
                    'sortable'         => true,
                ]))

            // Columna de fecha con formato automático
            ->add((new DateTimeColumn('fecha_creacion'))
                ->setName($this->trans('Fecha', [], 'Admin.Global'))
                ->setOptions(['field' => 'fecha_creacion', 'sortable' => true]))

            // Columna de acciones de fila
            ->add((new ActionColumn('actions'))
                ->setName($this->trans('Acciones', [], 'Admin.Global'))
                ->setOptions([
                    'actions' => (new RowActionCollection())
                        ->add((new LinkRowAction('edit'))
                            ->setIcon('edit')
                            ->setOptions([
                                'route'            => 'mi_modulo_comentarios_editar',
                                'route_param_name' => 'id',
                                'route_param_field' => 'id_comentario',
                            ]))
                        ->add((new SubmitRowAction('delete'))
                            ->setIcon('delete')
                            ->setOptions([
                                'method'           => 'POST',
                                'route'            => 'mi_modulo_comentarios_eliminar',
                                'route_param_name' => 'id',
                                'route_param_field' => 'id_comentario',
                                'confirm_message'  => $this->trans(
                                    '¿Eliminar este comentario?',
                                    [],
                                    'Modules.Mimodulo.Admin'
                                ),
                            ])),
                ]));
    }

    protected function getFilters(): FilterCollection
    {
        return (new FilterCollection())
            ->add((new Filter('id_comentario', NumberType::class))
                ->setTypeOptions(['required' => false, 'attr' => ['placeholder' => 'ID']])
                ->setAssociatedColumn('id_comentario'))

            ->add((new Filter('titulo', TextType::class))
                ->setTypeOptions(['required' => false, 'attr' => ['placeholder' => 'Título...']])
                ->setAssociatedColumn('titulo'))

            ->add((new Filter('aprobado', ChoiceType::class))
                ->setTypeOptions([
                    'required' => false,
                    'choices'  => ['Todos' => '', 'Aprobados' => '1', 'Pendientes' => '0'],
                    'choice_translation_domain' => false,
                ])
                ->setAssociatedColumn('aprobado'));
    }

    protected function getBulkActions(): BulkActionCollection
    {
        return (new BulkActionCollection())
            ->add((new SubmitBulkAction('aprobar'))
                ->setName($this->trans('Aprobar seleccionados', [], 'Modules.Mimodulo.Admin'))
                ->setOptions([
                    'submit_route_name' => 'mi_modulo_comentarios_aprobar_masivo',
                    'confirm_message'   => $this->trans('¿Aprobar los seleccionados?', [], 'Modules.Mimodulo.Admin'),
                ]))
            ->add((new SubmitBulkAction('eliminar'))
                ->setName($this->trans('Eliminar seleccionados', [], 'Modules.Mimodulo.Admin'))
                ->setOptions([
                    'submit_route_name' => 'mi_modulo_comentarios_eliminar_masivo',
                    'confirm_message'   => $this->trans('¿Eliminar los seleccionados?', [], 'Modules.Mimodulo.Admin'),
                ]));
    }

    protected function getGridActions(): GridActionCollection
    {
        return (new GridActionCollection())
            ->add((new LinkGridAction('add'))
                ->setName($this->trans('Añadir comentario', [], 'Modules.Mimodulo.Admin'))
                ->setIcon('add_circle_outline')
                ->setOptions(['route' => 'mi_modulo_comentarios_crear']));
    }
}
```

---

## 17.3 La fábrica de datos del Grid

```php
<?php
// Ejemplo 17.2 — Fábrica de datos del Grid con Doctrine DBAL
// Archivo: src/Grid/Data/Factory/ComentariosGridDataFactory.php

declare(strict_types=1);

namespace MiEmpresa\MiModulo\Grid\Data\Factory;

use Doctrine\DBAL\Connection;
use PrestaShop\PrestaShop\Core\Grid\Data\Factory\GridDataFactoryInterface;
use PrestaShop\PrestaShop\Core\Grid\Data\GridData;
use PrestaShop\PrestaShop\Core\Grid\Record\RecordCollection;
use PrestaShop\PrestaShop\Core\Grid\Search\SearchCriteriaInterface;

class ComentariosGridDataFactory implements GridDataFactoryInterface
{
    public function __construct(
        private readonly Connection $connection,
        private readonly string     $dbPrefix
    ) {
    }

    public function getData(SearchCriteriaInterface $searchCriteria): GridData
    {
        $qb = $this->connection->createQueryBuilder();

        $qb->select(
                'c.id_comentario',
                'c.titulo',
                'c.valoracion',
                'c.aprobado',
                'c.fecha_creacion',
                'pl.name AS nombre_producto'
            )
            ->from($this->dbPrefix . 'mi_modulo_comentario', 'c')
            ->leftJoin(
                'c',
                $this->dbPrefix . 'product_lang', 'pl',
                'c.id_producto = pl.id_product AND pl.id_lang = :idLang AND pl.id_shop = :idShop'
            )
            ->setParameter('idLang',  (int) \Context::getContext()->language->id)
            ->setParameter('idShop',  (int) \Context::getContext()->shop->id);

        // Aplicar filtros activos
        foreach ($searchCriteria->getFilters() as $field => $value) {
            if ($value === null || $value === '') {
                continue;
            }
            match ($field) {
                'id_comentario' => $qb->andWhere('c.id_comentario = :id')->setParameter('id', $value),
                'titulo'        => $qb->andWhere('c.titulo LIKE :titulo')->setParameter('titulo', "%$value%"),
                'aprobado'      => $qb->andWhere('c.aprobado = :aprobado')->setParameter('aprobado', $value),
                default         => null,
            };
        }

        // Contar registros totales para la paginación
        $countQb = clone $qb;
        $total   = (int) $countQb->select('COUNT(c.id_comentario)')->executeQuery()->fetchOne();

        // Aplicar ordenación y paginación
        if ($searchCriteria->getOrderBy()) {
            $qb->orderBy('c.' . $searchCriteria->getOrderBy(), $searchCriteria->getOrderWay());
        }
        $qb->setFirstResult($searchCriteria->getOffset())
           ->setMaxResults($searchCriteria->getLimit());

        $rows = $qb->executeQuery()->fetchAllAssociative();

        return new GridData(new RecordCollection($rows), $total);
    }
}
```

---

## 17.4 El controlador y la plantilla del Grid

```php
<?php
// Ejemplo 17.3 — Controlador que renderiza el Grid
// Archivo: src/Controller/Admin/ComentariosController.php

public function indexAction(Request $request): Response
{
    $gridFactory = $this->get('prestashop.core.grid.factory');
    $filters     = new \PrestaShop\PrestaShop\Core\Search\Filters(
        $request->query->all(),
        ComentariosGridDefinitionFactory::GRID_ID
    );

    $grid = $gridFactory->getGrid(
        $this->get('mi_empresa.mi_modulo.grid.definition.factory.comentarios'),
        $this->get('mi_empresa.mi_modulo.grid.data.factory.comentarios'),
        $filters
    );

    return $this->render(
        '@Modules/mi_modulo/views/templates/admin/comentarios/index.html.twig',
        ['grid' => $this->presentGrid($grid)]
    );
}
```

```twig
{# Ejemplo 17.4 — Plantilla Twig del Grid #}
{# Archivo: views/templates/admin/comentarios/index.html.twig #}

{% extends '@PrestaShop/Admin/layout.html.twig' %}

{% block content %}
    {# Una sola línea renderiza el grid completo con filtros, paginación y acciones #}
    {% include '@PrestaShop/Admin/Component/Grid/grid_panel.html.twig' with {
        'grid': grid
    } %}
{% endblock %}
```

---

## 17.5 Configurar los servicios del Grid en services.yml

```yaml
# Ejemplo 17.5 — Servicios del Grid en config/services.yml

services:
  # La factory de definición debe extender AbstractGridDefinitionFactory
  mi_empresa.mi_modulo.grid.definition.factory.comentarios:
    class: MiEmpresa\MiModulo\Grid\Definition\Factory\ComentariosGridDefinitionFactory
    parent: prestashop.core.grid.definition.factory.abstract_grid_definition
    public: true

  mi_empresa.mi_modulo.grid.data.factory.comentarios:
    class: MiEmpresa\MiModulo\Grid\Data\Factory\ComentariosGridDataFactory
    arguments:
      - '@doctrine.dbal.default_connection'
      - '%database_prefix%'
    public: true
```

---

### Resumen del Capítulo 17

| Concepto | Clave a recordar |
|---|---|
| `AbstractGridDefinitionFactory` | Clase base para la definición; implementar `getId()`, `getName()`, `getColumns()` |
| `ToggleColumn` | Cambia un estado booleano con un clic sin recargar la página |
| `GridDataFactoryInterface` | Implementar `getData()` que devuelve `GridData` con los registros y el total |
| `$this->presentGrid($grid)` | Convierte el objeto Grid en una estructura lista para Twig |
| `grid_panel.html.twig` | Template de PrestaShop que renderiza el grid completo |

**Próximo capítulo:** Capítulo 18 — Controladores con pestañas (Tabs).

---

# PARTE VII — PATRONES AVANZADOS

> Esta parte cubre los patrones arquitectónicos más sofisticados que usa PrestaShop
> internamente y que los módulos pueden adoptar para mayor robustez y testabilidad.

---

# Capítulo 21: El patrón CQRS

> **En este capítulo aprenderás:**
> - Qué es CQRS y por qué PrestaShop lo usa en su core
> - Cómo separar Commands (escritura) de Queries (lectura)
> - Cómo implementar CommandHandlers y QueryHandlers
> - Cómo usar CQRS en un controlador Symfony
>
> **Módulos de referencia:** `demoextendsymfonyform3`  
> **Nivel:** Avanzado  
> **Versión mínima PS:** 8.0.0

---

## 21.1 ¿Qué es CQRS y por qué usarlo?

**CQRS** (Command Query Responsibility Segregation) separa las operaciones que **modifican estado** (Commands) de las que **leen estado** (Queries).

```
SIN CQRS:
  Controlador → Servicio.guardarComentario(datos) → BD
  Controlador → Servicio.obtenerComentario(id)    → BD

CON CQRS:
  Controlador → AddComentarioCommand → AddComentarioCommandHandler → BD
  Controlador → GetComentarioQuery   → GetComentarioQueryHandler   → BD
```

| Ventaja | Descripción |
|---|---|
| Claridad semántica | Un `CreateProductCommand` describe inequívocamente su propósito |
| Hooks precisos | `actionAfterCreate*FormHandler` sabe exactamente si es una creación o actualización |
| Testabilidad | Cada handler es una clase pequeña y enfocada, fácil de probar |
| Separación de optimizaciones | Las queries pueden optimizarse sin afectar a los commands |

---

## 21.2 Commands — objetos de intención

```php
<?php
// Ejemplo 21.1 — Command para añadir un comentario
// Archivo: src/Command/AddComentarioCommand.php
// Un Command es un Value Object inmutable que encapsula la intención

declare(strict_types=1);

namespace MiEmpresa\MiModulo\Command;

/**
 * Command: añadir un comentario a un producto.
 * Es inmutable; su constructor valida los invariantes de negocio.
 */
final class AddComentarioCommand
{
    public function __construct(
        private readonly int    $idProducto,
        private readonly int    $idCliente,
        private readonly string $titulo,
        private readonly string $contenido,
        private readonly int    $valoracion
    ) {
        if ($valoracion < 1 || $valoracion > 5) {
            throw new \InvalidArgumentException('La valoración debe estar entre 1 y 5.');
        }
        if (empty(trim($titulo))) {
            throw new \InvalidArgumentException('El título es obligatorio.');
        }
    }

    public function getIdProducto(): int    { return $this->idProducto; }
    public function getIdCliente(): int     { return $this->idCliente; }
    public function getTitulo(): string     { return $this->titulo; }
    public function getContenido(): string  { return $this->contenido; }
    public function getValoracion(): int    { return $this->valoracion; }
}
```

---

## 21.3 Command Handlers — la lógica de escritura

```php
<?php
// Ejemplo 21.2 — CommandHandler para AddComentarioCommand
// Archivo: src/CommandHandler/AddComentarioCommandHandler.php

declare(strict_types=1);

namespace MiEmpresa\MiModulo\CommandHandler;

use Doctrine\ORM\EntityManagerInterface;
use MiEmpresa\MiModulo\Command\AddComentarioCommand;
use MiEmpresa\MiModulo\Entity\Comentario;

final class AddComentarioCommandHandler
{
    public function __construct(
        private readonly EntityManagerInterface $entityManager
    ) {
    }

    /**
     * @return int ID del comentario creado
     */
    public function handle(AddComentarioCommand $command): int
    {
        $comentario = new Comentario();
        $comentario
            ->setIdProducto($command->getIdProducto())
            ->setIdCliente($command->getIdCliente())
            ->setTitulo($command->getTitulo())
            ->setContenido($command->getContenido())
            ->setValoracion($command->getValoracion())
            ->setFechaCreacion(new \DateTimeImmutable())
            ->setAprobado(false);

        $this->entityManager->persist($comentario);
        $this->entityManager->flush();

        return (int) $comentario->getId();
    }
}
```

---

## 21.4 Queries y Query Result Objects

```php
<?php
// Ejemplo 21.3 — Query y su objeto de resultado
// Archivos: src/Query/ y src/QueryResult/

// ── La Query (intención de lectura) ───────────────────────────────────────
final class GetComentarioForEditingQuery
{
    public function __construct(private readonly int $id) {}
    public function getId(): int { return $this->id; }
}

// ── El resultado (Value Object inmutable) ─────────────────────────────────
final class EditableComentario
{
    public function __construct(
        private readonly int                $id,
        private readonly int                $idProducto,
        private readonly string             $titulo,
        private readonly string             $contenido,
        private readonly int                $valoracion,
        private readonly bool               $aprobado,
        private readonly \DateTimeImmutable $fechaCreacion
    ) {
    }

    // Solo getters — es inmutable, no tiene setters
    public function getId(): int               { return $this->id; }
    public function getIdProducto(): int       { return $this->idProducto; }
    public function getTitulo(): string        { return $this->titulo; }
    public function getContenido(): string     { return $this->contenido; }
    public function getValoracion(): int       { return $this->valoracion; }
    public function isAprobado(): bool         { return $this->aprobado; }
    public function getFechaCreacion(): \DateTimeImmutable { return $this->fechaCreacion; }
}
```

---

## 21.5 Usar CQRS en el controlador

```php
<?php
// Ejemplo 21.4 — Controlador con CQRS: Query para leer, Command para escribir

public function editarAction(int $id, Request $request): Response
{
    // ── QUERY: obtener datos para el formulario ────────────────────────────
    $queryHandler     = $this->get('mi_empresa.mi_modulo.query_handler.get_comentario');
    $comentarioEditable = $queryHandler->handle(new GetComentarioForEditingQuery($id));

    $form = $this->createForm(ComentarioFormType::class, [
        'titulo'    => $comentarioEditable->getTitulo(),
        'contenido' => $comentarioEditable->getContenido(),
        'valoracion' => $comentarioEditable->getValoracion(),
        'aprobado'  => $comentarioEditable->isAprobado(),
    ]);

    $form->handleRequest($request);

    if ($form->isSubmitted() && $form->isValid()) {
        $data = $form->getData();

        // ── COMMAND: persistir los cambios ─────────────────────────────────
        $commandHandler = $this->get('mi_empresa.mi_modulo.command_handler.update_comentario');
        $commandHandler->handle(new UpdateComentarioCommand(
            $id,
            $data['titulo'],
            $data['contenido'],
            $data['valoracion'],
            $data['aprobado']
        ));

        $this->addFlash('success', 'Comentario actualizado.');
        return $this->redirectToRoute('mi_modulo_comentarios_lista');
    }

    return $this->render(
        '@Modules/mi_modulo/views/templates/admin/comentarios/editar.html.twig',
        ['form' => $form->createView()]
    );
}
```

---

### Resumen del Capítulo 21

| Concepto | Clave a recordar |
|---|---|
| Command | Value Object inmutable que expresa una intención de cambio |
| CommandHandler | Clase con un método `handle()` que ejecuta la operación de escritura |
| Query | Value Object que expresa una petición de datos |
| QueryResult | Value Object inmutable que encapsula los datos leídos |
| Por qué CQRS | Más claro, más testeable, más mantenible que servicios con múltiples responsabilidades |

**Próximo capítulo:** Capítulo 22 — API REST con API Platform.

---

# Capítulo 23: Comandos de consola Symfony

> **En este capítulo aprenderás:**
> - Cuándo usar comandos de consola en lugar de controladores web
> - Cómo crear un comando con argumentos, opciones y salida formateada
> - Cómo registrar el comando como servicio con el tag correcto
> - Cómo configurar cron jobs para ejecutarlos periódicamente
>
> **Módulos de referencia:** `democonsolecommand`  
> **Nivel:** Intermedio  
> **Versión mínima PS:** 8.0.0 (disponible desde 1.7.5)

---

## 23.1 Cuándo usar comandos de consola

Los comandos de consola son ideales para tareas que:

| Tipo de tarea | Ejemplo |
|---|---|
| Batch / masivas | Sincronizar 10.000 productos con un ERP |
| Programadas (cron) | Generar reportes diarios automáticamente |
| Mantenimiento | Limpiar imágenes huérfanas, reindexar |
| Migraciones de datos | Transformar datos al actualizar el módulo |
| Diagnóstico | Verificar el estado de la integración externa |

> **Advertencia:** Los comandos de consola se ejecutan fuera del contexto de PrestaShop.
> Si necesitas clases del core como `ObjectModel`, pueden surgir problemas. Usa
> siempre servicios Symfony (Doctrine, repositorios) en lugar de clases legacy del core.

---

## 23.2 Crear un comando de consola

```php
<?php
// Ejemplo 23.1 — Comando de consola completo con argumentos y opciones
// Archivo: src/Command/SincronizarProductosCommand.php
// Uso: php bin/console mi-modulo:sincronizar-productos [id] [--limite=50] [--solo-nuevos]

declare(strict_types=1);

namespace MiEmpresa\MiModulo\Command;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputArgument;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Input\InputOption;
use Symfony\Component\Console\Output\OutputInterface;
use Symfony\Component\Console\Style\SymfonyStyle;

class SincronizarProductosCommand extends Command
{
    protected static $defaultName        = 'mi-modulo:sincronizar-productos';
    protected static $defaultDescription = 'Sincroniza productos con el sistema externo';

    public function __construct(
        private readonly \MiEmpresa\MiModulo\Service\SincronizadorService $sincronizador
    ) {
        parent::__construct();
    }

    protected function configure(): void
    {
        $this
            ->addArgument(
                'id_producto',
                InputArgument::OPTIONAL,
                'ID del producto a sincronizar (opcional; si se omite, sincroniza todos)'
            )
            ->addOption(
                'limite',
                'l',
                InputOption::VALUE_OPTIONAL,
                'Número máximo de productos a procesar',
                50
            )
            ->addOption(
                'solo-nuevos',
                null,
                InputOption::VALUE_NONE,
                'Procesar solo productos nuevos (no actualizados previamente)'
            );
    }

    protected function execute(InputInterface $input, OutputInterface $output): int
    {
        $io          = new SymfonyStyle($input, $output);
        $limite      = (int) $input->getOption('limite');
        $soloNuevos  = (bool) $input->getOption('solo-nuevos');
        $idProducto  = $input->getArgument('id_producto');

        $io->title('Sincronización con sistema externo');

        // Modo: un solo producto
        if ($idProducto !== null) {
            try {
                $this->sincronizador->sincronizarProducto((int) $idProducto);
                $io->success(sprintf('Producto #%d sincronizado correctamente.', $idProducto));
            } catch (\Exception $e) {
                $io->error('Error: ' . $e->getMessage());
                return Command::FAILURE;
            }
            return Command::SUCCESS;
        }

        // Modo: sincronización masiva
        $io->note(sprintf(
            'Procesando hasta %d producto(s)%s',
            $limite,
            $soloNuevos ? ' (solo nuevos)' : ''
        ));

        $pendientes    = $this->sincronizador->obtenerPendientes($limite, $soloNuevos);
        $sincronizados = 0;
        $errores       = 0;

        $io->progressStart(count($pendientes));

        foreach ($pendientes as $producto) {
            try {
                $this->sincronizador->sincronizarProducto($producto->id);
                $sincronizados++;
            } catch (\Exception $e) {
                $errores++;
                $io->newLine();
                $io->warning(sprintf('Error en producto %d: %s', $producto->id, $e->getMessage()));
            }
            $io->progressAdvance();
        }

        $io->progressFinish();
        $io->table(
            ['Resultado', 'Cantidad'],
            [
                ['Sincronizados con éxito', $sincronizados],
                ['Errores',                 $errores],
                ['Total procesados',        $sincronizados + $errores],
            ]
        );

        return $errores === 0 ? Command::SUCCESS : Command::FAILURE;
    }
}
```

---

## 23.3 Registrar el comando como servicio

```yaml
# Ejemplo 23.2 — Registro del comando en config/services.yml
# El tag 'console.command' lo hace visible para php bin/console

services:
  mi_empresa.mi_modulo.command.sincronizar_productos:
    class: MiEmpresa\MiModulo\Command\SincronizarProductosCommand
    arguments:
      - '@mi_empresa.mi_modulo.sincronizador_service'
    tags:
      - { name: 'console.command' }
```

---

## 23.4 Ejecutar y automatizar

```bash
# Ejemplo 23.3 — Ejemplos de uso del comando

# Sincronizar todos los productos (límite por defecto: 50)
php bin/console mi-modulo:sincronizar-productos

# Sincronizar un único producto
php bin/console mi-modulo:sincronizar-productos 42

# Sincronizar con límite mayor, solo nuevos
php bin/console mi-modulo:sincronizar-productos --limite=500 --solo-nuevos

# Ver la ayuda completa del comando
php bin/console mi-modulo:sincronizar-productos --help

# Modo verboso (muestra más información de depuración)
php bin/console mi-modulo:sincronizar-productos -v
```

```bash
# Ejemplo 23.4 — Cron job para ejecutar el comando cada hora

# Editar el crontab del servidor
crontab -e

# Añadir la línea (ajustar la ruta a la instalación de PrestaShop)
0 * * * * /usr/bin/php /var/www/html/bin/console mi-modulo:sincronizar-productos \
    >> /var/log/mi-modulo-sync.log 2>&1
```

---

### Resumen del Capítulo 23

| Concepto | Clave a recordar |
|---|---|
| `Command::SUCCESS` / `FAILURE` | Devolver el código correcto para que el cron o CI/CD detecte errores |
| `SymfonyStyle` | Proporciona métodos para salida formateada (`table`, `progressBar`, `title`) |
| Tag `console.command` | Obligatorio para que Symfony registre el comando en `php bin/console` |
| Evitar clases legacy | Usar servicios Symfony en los comandos; `ObjectModel` puede no funcionar |
| Cron jobs | Usar la ruta absoluta de PHP y redirigir stdout/stderr a un fichero de log |

---

*[Continúa en parte4_nuevo.md]*
# PARTE VIII — FRONT OFFICE

> El front office es la interfaz visible para los clientes de la tienda.
> Esta parte cubre la integración de módulos en el front: hooks, rutas,
> JavaScript y plantillas de correo electrónico.

---

# Capítulo 25: Integración en el front office

> **En este capítulo aprenderás:**
> - Los principales hooks del front office y cómo usarlos eficientemente
> - Cómo añadir CSS y JavaScript solo en las páginas que los necesitan
> - Cómo pasar variables de PHP a JavaScript de forma segura
> - Cómo gestionar llamadas AJAX desde el front office
>
> **Módulos de referencia:** `demoproductextracontent`  
> **Nivel:** Básico-Intermedio  
> **Versión mínima PS:** 8.0.0

---

## 25.1 Añadir contenido en la ficha de producto

```php
<?php
// Ejemplo 25.1 — Hook displayProductAdditionalInformation
// Módulo de referencia: demoproductextracontent

public function install(): bool
{
    return parent::install()
        && $this->registerHook('displayProductAdditionalInformation');
}

/**
 * Añade contenido extra en la ficha de producto.
 * $params['product'] contiene los datos del producto como array.
 */
public function hookDisplayProductAdditionalInformation(array $params): string
{
    $product = $params['product'];

    // Si necesitas más datos del producto que no vienen en el array:
    // $productObj = new Product($product['id_product'], false, $this->context->language->id);

    $datosExtra = $this->getDatosExtra((int) $product['id_product']);

    $this->context->smarty->assign([
        'product_name' => $product['name'],
        'datos_extra'  => $datosExtra,
    ]);

    return $this->display(__FILE__, 'views/templates/hook/product_extra.tpl');
}

private function getDatosExtra(int $idProduct): array
{
    return Db::getInstance()->executeS('
        SELECT clave, valor
        FROM `' . _DB_PREFIX_ . 'mi_modulo_producto_data`
        WHERE `id_product` = ' . $idProduct . '
        ORDER BY `orden` ASC
    ') ?: [];
}
```

```smarty
{* Ejemplo 25.2 — Plantilla Smarty para el hook de producto *}
{* Archivo: views/templates/hook/product_extra.tpl *}

<section class="mi-modulo-extra product-additional-info">
    <h3 class="h3 product-information-title">
        {l s='Información adicional' mod='mi_modulo'}
    </h3>

    {if $datos_extra}
        <table class="table">
            <tbody>
                {foreach $datos_extra as $dato}
                    <tr>
                        <th scope="row">{$dato.clave|escape:'html':'UTF-8'}</th>
                        <td>{$dato.valor|escape:'html':'UTF-8'}</td>
                    </tr>
                {/foreach}
            </tbody>
        </table>
    {else}
        <p class="text-muted">{l s='Sin información adicional disponible.' mod='mi_modulo'}</p>
    {/if}
</section>
```

---

## 25.2 Añadir CSS y JavaScript de forma óptima

> **Rendimiento:** Carga siempre los assets CSS/JS de forma condicional.
> Un módulo que carga archivos en todas las páginas ralentiza toda la tienda.

```php
<?php
// Ejemplo 25.3 — Carga condicional de assets en displayHeader

public function hookDisplayHeader(array $params): void
{
    $controller = $this->context->controller;

    // Lista de páginas donde debe cargarse el módulo
    $paginasPermitidas = ['product', 'cart', 'order'];

    if (!in_array($controller->php_self, $paginasPermitidas, true)) {
        return; // Salir sin cargar nada en otras páginas
    }

    // Añadir CSS (el segundo parámetro es el media type)
    $controller->addCSS($this->getPathUri() . 'views/css/front/mi_modulo.css', 'all');

    // Añadir JavaScript
    $controller->addJS($this->getPathUri() . 'views/js/front/mi_modulo.js');

    // Pasar variables de PHP a JavaScript de forma segura
    // Media::addJsDef() las incluye en un objeto JS antes de los demás scripts
    Media::addJsDef([
        'miModulo' => [
            'ajaxUrl'   => $this->context->link->getModuleLink($this->name, 'ajax', [], true),
            'token'     => Tools::getToken(false),
            'idCliente' => (int) $this->context->customer->id,
            'mensajes'  => [
                'exito' => $this->trans('Enviado correctamente.', [], 'Modules.Mimodulo.Shop'),
                'error' => $this->trans('Ha ocurrido un error.', [],  'Modules.Mimodulo.Shop'),
            ],
        ],
    ]);
}
```

---

## 25.3 JavaScript del front office con Fetch API

```javascript
// Ejemplo 25.4 — Petición AJAX al controlador front del módulo
// Archivo: views/js/front/mi_modulo.js
// Las variables de miModulo.* se definen en PHP con Media::addJsDef()

document.addEventListener('DOMContentLoaded', () => {
    const form = document.getElementById('mi-modulo-form');
    if (!form) return;

    form.addEventListener('submit', async (e) => {
        e.preventDefault();

        const btn = form.querySelector('[type="submit"]');
        btn.disabled = true;

        try {
            const response = await fetch(miModulo.ajaxUrl, {
                method: 'POST',
                headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
                body: new URLSearchParams({
                    action : 'crearComentario',
                    token  : miModulo.token,
                    titulo : form.querySelector('[name="titulo"]').value,
                    contenido: form.querySelector('[name="contenido"]').value,
                    valoracion: form.querySelector('[name="valoracion"]:checked')?.value ?? 0,
                }),
            });

            const data = await response.json();

            showAlert(data.success ? 'success' : 'danger', data.message);

            if (data.success) form.reset();
        } catch (_) {
            showAlert('danger', miModulo.mensajes.error);
        } finally {
            btn.disabled = false;
        }
    });
});

function showAlert(type, message) {
    const el = document.createElement('div');
    el.className = `alert alert-${type}`;
    el.textContent = message;
    document.querySelector('.mi-modulo-messages')?.append(el);
    setTimeout(() => el.remove(), 5000);
}
```

---

### Resumen del Capítulo 25

| Concepto | Clave a recordar |
|---|---|
| Carga condicional | Verificar `$controller->php_self` antes de añadir CSS/JS |
| `Media::addJsDef()` | La forma oficial de pasar variables PHP al JavaScript del front |
| `getModuleLink()` | Genera la URL de un controlador front del módulo (con SSL opcional) |
| Hooks de front | Devolver HTML (string) en display hooks; void en action hooks |
| `Tools::getToken()` | Token para verificar peticiones AJAX del front office |

**Próximo capítulo:** Capítulo 26 — Rutas personalizadas de módulo.

---

# Capítulo 26: Rutas personalizadas de módulo

> **En este capítulo aprenderás:**
> - Cómo registrar URLs limpias para las páginas del front office de tu módulo
> - Cómo definir rutas con parámetros dinámicos
> - Cómo generar esas URLs desde PHP, Smarty y Twig
>
> **Módulos de referencia:** `demomoduleroutes`  
> **Nivel:** Intermedio  
> **Versión mínima PS:** 8.0.0

---

## 26.1 Registrar rutas con el hook moduleRoutes

```php
<?php
// Ejemplo 26.1 — Hook moduleRoutes para rutas limpias en el front office
// Sin este hook, la URL sería: /index.php?fc=module&module=mi_modulo&controller=comentarios
// Con este hook, la URL es: /mi-modulo/comentarios

public function install(): bool
{
    return parent::install()
        && $this->registerHook('moduleRoutes');
}

/**
 * Define las rutas limpias del módulo.
 * La clave del array es el nombre interno de la ruta.
 */
public function hookModuleRoutes(): array
{
    return [
        // Ruta simple sin parámetros
        'module-mi_modulo-comentarios' => [
            'controller' => 'comentarios',   // Clase: MiModuloComentariosModuleFrontController
            'rule'       => 'mi-modulo/comentarios',
            'keywords'   => [],
            'params'     => ['fc' => 'module', 'module' => $this->name],
        ],

        // Ruta con parámetro obligatorio (ID del producto)
        'module-mi_modulo-producto' => [
            'controller' => 'comentarios',
            'rule'       => 'mi-modulo/producto/{id_product}/comentarios',
            'keywords'   => [
                'id_product' => ['regexp' => '[0-9]+', 'param' => 'id_product'],
            ],
            'params'     => ['fc' => 'module', 'module' => $this->name],
        ],

        // Ruta para feed RSS del módulo
        'module-mi_modulo-feed' => [
            'controller' => 'feed',
            'rule'       => 'mi-modulo/novedades.xml',
            'keywords'   => [],
            'params'     => ['fc' => 'module', 'module' => $this->name],
        ],
    ];
}
```

---

## 26.2 Generar URLs desde el código

```php
<?php
// Ejemplo 26.2 — Generar URLs de módulo desde PHP

// URL simple
$url = $this->context->link->getModuleLink(
    'mi_modulo',       // nombre técnico del módulo
    'comentarios',     // nombre del controlador (sin prefijo ModuleFront)
    [],                // parámetros adicionales
    true               // forzar SSL (https)
);
// Resultado: https://tutienda.com/mi-modulo/comentarios

// URL con parámetro
$url = $this->context->link->getModuleLink(
    'mi_modulo',
    'comentarios',
    ['id_product' => 42]
);
// Resultado: https://tutienda.com/mi-modulo/producto/42/comentarios
```

```smarty
{* Ejemplo 26.3 — Generar URLs en plantillas Smarty del front office *}

{assign var='url_comentarios' value=$link->getModuleLink('mi_modulo', 'comentarios')}
<a href="{$url_comentarios}">Ver todos los comentarios</a>

{assign var='url_producto' value=$link->getModuleLink('mi_modulo', 'comentarios', ['id_product' => $product.id_product])}
<a href="{$url_producto}">Comentarios de este producto</a>
```

```twig
{# Ejemplo 26.4 — Generar URLs en plantillas Twig del back office (si aplica) #}

<a href="{{ path('module-mi_modulo-comentarios') }}">Comentarios</a>
<a href="{{ path('module-mi_modulo-producto', {'id_product': producto.id}) }}">
    Comentarios del producto
</a>
```

---

### Resumen del Capítulo 26

| Concepto | Clave a recordar |
|---|---|
| `hookModuleRoutes` | Registra las URLs limpias del módulo; sin él se usan URLs con `?fc=module` |
| `rule` | La URL limpia; usa `{param}` para parámetros dinámicos |
| `keywords` | Define los parámetros dinámicos con su regexp de validación |
| `getModuleLink()` | Genera la URL del controlador front, respetando las rutas registradas |
| Cache importante | Limpiar la caché tras modificar `hookModuleRoutes` para que los cambios tengan efecto |

---

# Capítulo 27: JavaScript y el JS Router

> **En este capítulo aprenderás:**
> - Qué es el JS Router de PrestaShop y para qué sirve
> - Cómo exponer rutas Symfony al JavaScript del back office
> - Cómo usar `prestashop.instance.router.generate()` en JavaScript
>
> **Módulos de referencia:** `demojsrouting`  
> **Nivel:** Intermedio  
> **Versión mínima PS:** 8.0.0

---

## 27.1 El problema sin JS Router: URLs hardcodeadas

```javascript
// Ejemplo 27.1 — El problema: URLs hardcodeadas en JavaScript

// SIN JS Router (frágil, se rompe si cambia la URL)
const url = '/es/mi-modulo/toggle/' + id;

// CON JS Router (siempre correcto, usa las rutas definidas en routes.yml)
const url = prestashop.instance.router.generate('mi_modulo_toggle_estado', { id: id });
```

---

## 27.2 Exponer rutas al JavaScript desde el controlador

```php
<?php
// Ejemplo 27.2 — Exponer rutas Symfony al JS Router desde el controlador

public function indexAction(): Response
{
    // Hacer disponibles estas rutas para el JS Router de PrestaShop
    $this->get('prestashop.core.js_routing_feature')->expose([
        'mi_modulo_toggle_estado',
        'mi_modulo_comentarios_eliminar',
        'mi_modulo_ajax_search',
    ]);

    return $this->render(/* ... */);
}
```

---

## 27.3 Usar el router en JavaScript

```javascript
// Ejemplo 27.3 — Uso del JS Router de PrestaShop en el back office
// Archivo: views/js/admin/comentarios.js

// prestashop.instance.router está disponible en el BO moderno
// después de que el controlador exponga las rutas

document.querySelectorAll('.mi-modulo-toggle').forEach((btn) => {
    btn.addEventListener('click', async () => {
        const id     = btn.dataset.id;
        const activo = btn.dataset.activo === '1';

        // Generar la URL usando la ruta con nombre
        const url = prestashop.instance.router.generate(
            'mi_modulo_toggle_estado',
            { id: id }
        );

        const response = await fetch(url, {
            method : 'POST',
            headers: { 'X-Requested-With': 'XMLHttpRequest' },
        });

        const data = await response.json();

        if (data.status === 'ok') {
            btn.dataset.activo = data.activo ? '1' : '0';
            btn.classList.toggle('active', data.activo);
        }
    });
});
```

---

### Resumen del Capítulo 27

| Concepto | Clave a recordar |
|---|---|
| JS Router | Evita URLs hardcodeadas en JavaScript usando rutas con nombre |
| `expose()` | Hace las rutas disponibles en `prestashop.instance.router` del JS |
| `router.generate()` | Genera la URL correcta con parámetros, respetando el idioma y la configuración |
| Cuándo usarlo | Siempre en el back office moderno con JavaScript que llama a rutas Symfony |

---

# Capítulo 28: Plantillas de correo electrónico

> **En este capítulo aprenderás:**
> - Cómo crear plantillas de email para el módulo
> - Cómo enviar emails con las plantillas usando `Mail::Send()`
> - La estructura de los archivos de email (HTML y texto plano)
>
> **Módulos de referencia:** `example_module_mailtheme`  
> **Nivel:** Básico-Intermedio  
> **Versión mínima PS:** 8.0.0

---

## 28.1 Estructura de los emails de un módulo

```
mi_modulo/
└── mails/
    ├── en/
    │   ├── comentario_aprobado.html   ← Versión HTML del email
    │   └── comentario_aprobado.txt    ← Versión texto plano (fallback)
    └── es/
        ├── comentario_aprobado.html
        └── comentario_aprobado.txt
```

---

## 28.2 Enviar un email desde el módulo

```php
<?php
// Ejemplo 28.1 — Enviar email usando una plantilla del módulo
// Archivo: src/Service/NotificacionService.php

public function notificarComentarioAprobado(int $idCliente, string $nombreProducto): bool
{
    $customer = new \Customer($idCliente);
    if (!\Validate::isLoadedObject($customer)) {
        return false;
    }

    return \Mail::Send(
        (int) $this->context->language->id,        // ID del idioma
        'comentario_aprobado',                     // Nombre de la plantilla (sin extensión)
        \Mail::l('Tu comentario ha sido aprobado'), // Asunto del email
        [
            // Variables disponibles en la plantilla HTML/TXT
            '{firstname}'    => $customer->firstname,
            '{lastname}'     => $customer->lastname,
            '{shop_name}'    => \Configuration::get('PS_SHOP_NAME'),
            '{product_name}' => $nombreProducto,
            '{shop_url}'     => $this->context->link->getBaseLink(),
        ],
        $customer->email,                           // Email del destinatario
        $customer->firstname . ' ' . $customer->lastname,
        null,                                       // From (null = configuración de PS)
        null,                                       // From name
        null,                                       // Adjunto
        null,                                       // Modo
        _PS_MODULE_DIR_ . $this->name . '/mails/', // ← Ruta a las plantillas DEL MÓDULO
        false,
        (int) $this->context->shop->id
    );
}
```

---

## 28.3 Plantilla HTML del email

```html
<!-- Ejemplo 28.2 — Plantilla HTML del email -->
<!-- Archivo: mails/es/comentario_aprobado.html -->

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>{l s='Tu comentario ha sido aprobado' mod='mi_modulo'}</title>
</head>
<body style="font-family: Arial, sans-serif; color: #333333; background-color: #f4f4f4; margin: 0; padding: 20px;">

    <div style="max-width: 600px; margin: 0 auto; background-color: #ffffff;
                border-radius: 8px; overflow: hidden; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">

        <!-- Cabecera -->
        <div style="background-color: #1a1a2e; padding: 30px; text-align: center;">
            <h1 style="color: #ffffff; margin: 0; font-size: 24px;">{shop_name}</h1>
        </div>

        <!-- Cuerpo -->
        <div style="padding: 30px;">
            <p>Hola, <strong>{firstname} {lastname}</strong>,</p>

            <p>Nos complace informarte de que tu comentario sobre
               <strong>{product_name}</strong> ha sido aprobado y ya es visible
               para otros clientes.</p>

            <p style="text-align: center; margin: 30px 0;">
                <a href="{shop_url}"
                   style="background-color: #0070c0; color: #ffffff;
                          padding: 12px 24px; border-radius: 4px;
                          text-decoration: none; font-weight: bold;">
                    Visita la tienda
                </a>
            </p>

            <p>Gracias por tu contribución,<br>
               <strong>El equipo de {shop_name}</strong></p>
        </div>

        <!-- Pie -->
        <div style="background-color: #f8f8f8; padding: 15px; text-align: center;
                    font-size: 12px; color: #999999; border-top: 1px solid #eeeeee;">
            Este email fue enviado por <a href="{shop_url}">{shop_name}</a>.
        </div>
    </div>

</body>
</html>
```

```
Ejemplo 28.3 — Versión texto plano del email
Archivo: mails/es/comentario_aprobado.txt

Hola, {firstname} {lastname},

Tu comentario sobre {product_name} ha sido aprobado y ya es visible en la tienda.

Visita la tienda: {shop_url}

Gracias,
El equipo de {shop_name}
```

---

### Resumen del Capítulo 28

| Concepto | Clave a recordar |
|---|---|
| Estructura de carpetas | `mails/{idioma}/{plantilla}.html` y `.txt` |
| `Mail::Send()` | Último parámetro clave: la ruta a la carpeta `mails/` del módulo |
| Variables `{placeholder}` | Usar llaves `{}` en la plantilla; pasar el array de sustituciones |
| Versión TXT | Siempre crear también la versión texto plano para clientes que no soportan HTML |

---

# PARTE IX — CASOS ESPECIALES

---

# Capítulo 29: Módulos de pago

> **En este capítulo aprenderás:**
> - La diferencia entre `Module` y `PaymentModule`
> - El flujo completo de un pago con módulo PrestaShop
> - Cómo implementar `hookPaymentOptions()` y los controladores de pago
> - Las consideraciones de seguridad específicas de los módulos de pago
>
> **Nivel:** Intermedio-Avanzado  
> **Versión mínima PS:** 8.0.0

---

## 29.1 El flujo de un pago con módulo PrestaShop

```
Cliente en el checkout
         ↓
hookPaymentOptions()    → Muestra "Pagar con Mi Módulo" en el checkout
         ↓
[Cliente selecciona y confirma]
         ↓
Controller: payment.php → Procesa/redirige a la pasarela externa
         ↓
[Pasarela procesa el pago — puede ser asíncrono]
         ↓
Controller: validation.php → validateOrder() → Crea el pedido en PrestaShop
         ↓
hookPaymentReturn()     → Muestra la página de confirmación
```

---

## 29.2 Clase principal de un módulo de pago

```php
<?php
// Ejemplo 29.1 — Clase principal de un módulo de pago
// Archivo: mi_modulo_pago.php

class MiModuloPago extends PaymentModule
{
    public function __construct()
    {
        $this->name        = 'mi_modulo_pago';
        $this->tab         = 'payments_gateways';
        $this->version     = '1.0.0';
        $this->author      = 'Mi Empresa';

        $this->ps_versions_compliancy = ['min' => '8.0.0', 'max' => _PS_VERSION_];
        $this->controllers            = ['payment', 'validation']; // Controladores front
        $this->is_eu_compatible       = 1;
        $this->currencies             = true;
        $this->currencies_mode        = 'checkbox';

        parent::__construct();

        $this->displayName = $this->trans('Mi Módulo de Pago', [], 'Modules.Mipago.Admin');
        $this->description = $this->trans('Pasarela de pago de ejemplo.', [], 'Modules.Mipago.Admin');
    }

    public function install(): bool
    {
        return parent::install()
            && $this->registerHook('paymentOptions')  // Mostrar opción en el checkout
            && $this->registerHook('paymentReturn');   // Mostrar confirmación
    }

    /**
     * Define las opciones de pago que aparecen en el checkout.
     *
     * @param array $params ['cart' => Cart]
     * @return \PrestaShop\PrestaShop\Core\Payment\PaymentOption[]
     */
    public function hookPaymentOptions(array $params): array
    {
        if (!$this->active || !$this->checkCurrency($params['cart'])) {
            return [];
        }

        $opcion = new \PrestaShop\PrestaShop\Core\Payment\PaymentOption();
        $opcion->setCallToActionText(
            $this->trans('Pagar con Mi Pasarela', [], 'Modules.Mipago.Shop')
        )
        ->setAction(
            $this->context->link->getModuleLink($this->name, 'payment', [], true)
        )
        ->setAdditionalInformation(
            $this->context->smarty->fetch(
                'module:mi_modulo_pago/views/templates/hook/payment_infos.tpl'
            )
        )
        ->setLogo(
            Media::getMediaPath(_PS_MODULE_DIR_ . $this->name . '/views/img/logo_pago.png')
        );

        return [$opcion];
    }

    /** Se muestra en la página de confirmación tras el pago. */
    public function hookPaymentReturn(array $params): string
    {
        $this->context->smarty->assign([
            'shop_name'   => $this->context->shop->name,
            'reference'   => $params['order']->reference,
            'contact_url' => $this->context->link->getPageLink('contact', true),
        ]);

        return $this->display(__FILE__, 'views/templates/hook/payment_return.tpl');
    }

    private function checkCurrency(\Cart $cart): bool
    {
        $currency        = new \Currency($cart->id_currency);
        $moduleCurrencies = $this->getCurrency($cart->id_currency);

        if (is_array($moduleCurrencies)) {
            foreach ($moduleCurrencies as $mc) {
                if ($currency->id == $mc['id_currency']) {
                    return true;
                }
            }
        }

        return false;
    }
}
```

---

## 29.3 Controlador de pago (front)

```php
<?php
// Ejemplo 29.2 — Controlador que inicia el proceso de pago
// Archivo: controllers/front/payment.php

class MiModuloPagoPaymentModuleFrontController extends ModuleFrontController
{
    public function postProcess(): void
    {
        $cart = $this->context->cart;

        // Verificaciones de seguridad obligatorias
        if (!$this->module->active
            || $cart->id_customer === 0
            || $cart->id_address_delivery === 0
        ) {
            Tools::redirect('index.php?controller=order&step=1');
        }

        // Verificar que el módulo está autorizado para este carrito
        $autorizado = false;
        foreach (Module::getPaymentModules() as $module) {
            if ($module['name'] === $this->module->name) {
                $autorizado = true;
                break;
            }
        }

        if (!$autorizado) {
            $this->errors[] = $this->module->trans(
                'Este módulo de pago no está disponible.',
                [],
                'Modules.Mipago.Shop'
            );
            $this->redirectWithNotifications('index.php?controller=order');
        }

        // Aquí iría la lógica real de integración con la pasarela:
        // 1. Crear una sesión/intención de pago en la pasarela
        // 2. Redirigir al usuario a la URL de pago de la pasarela

        // Para este ejemplo simplificado, redirigimos directamente a validación:
        Tools::redirect(
            $this->context->link->getModuleLink(
                $this->module->name,
                'validation',
                ['status' => 'ok', 'ref' => uniqid()],
                true
            )
        );
    }
}
```

---

## 29.4 Controlador de validación

```php
<?php
// Ejemplo 29.3 — Controlador que valida y crea el pedido
// Archivo: controllers/front/validation.php

class MiModuloPagoValidationModuleFrontController extends ModuleFrontController
{
    public function postProcess(): void
    {
        $cart   = $this->context->cart;
        $status = Tools::getValue('status');

        if ($status !== 'ok') {
            // Pago rechazado o cancelado
            $this->errors[] = $this->module->trans(
                'El pago no pudo completarse. Por favor, inténtalo de nuevo.',
                [],
                'Modules.Mipago.Shop'
            );
            $this->redirectWithNotifications('index.php?controller=order&step=1');
        }

        // ── Crear el pedido en PrestaShop ──────────────────────────────────
        $this->module->validateOrder(
            (int) $cart->id,
            (int) Configuration::get('PS_OS_PAYMENT'), // Estado: Pago aceptado
            (float) $cart->getOrderTotal(true, Cart::BOTH),
            $this->module->displayName,   // Nombre del método de pago
            null,                         // Mensaje interno
            [],                           // Variables extra
            (int) $cart->id_currency,
            false,                        // No convertir divisa
            $this->context->customer->secure_key
        );

        // Redirigir a la confirmación del pedido
        Tools::redirect(
            $this->context->link->getPageLink('order-confirmation', true, null, [
                'id_cart'   => $cart->id,
                'id_module' => $this->module->id,
                'id_order'  => $this->module->currentOrder,
                'key'       => $this->context->customer->secure_key,
            ])
        );
    }
}
```

---

### Resumen del Capítulo 29

| Concepto | Clave a recordar |
|---|---|
| `PaymentModule` | Clase base para módulos de pago; proporciona `validateOrder()` |
| `hookPaymentOptions()` | Devuelve un array de `PaymentOption`; define cómo aparece en el checkout |
| `checkCurrency()` | Verificar siempre que la divisa del carrito está soportada |
| `validateOrder()` | Crea el pedido en PrestaShop; llamar SOLO cuando el pago está confirmado |
| Seguridad | Verificar `$cart->id_customer`, que el módulo esté autorizado y el `secure_key` |

---

# PARTE X — CALIDAD Y PRODUCCIÓN

---

# Capítulo 31: Testing de módulos

> **En este capítulo aprenderás:**
> - Cómo configurar PHPUnit para un módulo PrestaShop
> - Cómo escribir tests unitarios para servicios con inyección de dependencias
> - Cómo testear CommandHandlers y QueryHandlers
> - Las estrategias para mantener los tests independientes de PrestaShop
>
> **Nivel:** Intermedio  
> **Versión mínima PS:** 8.0.0

---

## 31.1 Por qué testear módulos PrestaShop

| Sin tests | Con tests |
|---|---|
| Cada cambio puede romper funcionalidad existente sin saberlo | Las regresiones se detectan inmediatamente |
| Difícil de refactorizar con confianza | Se puede refactorizar sabiendo qué funciona |
| Bugs en producción cuestan tiempo y reputación | Los bugs se encuentran en desarrollo |
| Obligatorio para pasar la validación de Addons | ✓ |

---

## 31.2 Configurar PHPUnit

```xml
<!-- Ejemplo 31.1 — Configuración de PHPUnit -->
<!-- Archivo: phpunit.xml.dist -->

<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/10.0/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true">

    <testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
        <testsuite name="Integration">
            <directory suffix="Test.php">./tests/Integration</directory>
        </testsuite>
    </testsuites>

    <coverage>
        <include>
            <directory suffix=".php">./src</directory>
        </include>
    </coverage>
</phpunit>
```

---

## 31.3 Test unitario de un servicio

```php
<?php
// Ejemplo 31.2 — Test unitario del ComentarioService
// Archivo: tests/Unit/Service/ComentarioServiceTest.php

declare(strict_types=1);

namespace MiEmpresa\MiModulo\Tests\Unit\Service;

use Doctrine\ORM\EntityManagerInterface;
use MiEmpresa\MiModulo\Entity\Comentario;
use MiEmpresa\MiModulo\Repository\ComentarioRepository;
use MiEmpresa\MiModulo\Service\ComentarioService;
use PHPUnit\Framework\MockObject\MockObject;
use PHPUnit\Framework\TestCase;
use Psr\Log\NullLogger;
use Symfony\Contracts\Translation\TranslatorInterface;

class ComentarioServiceTest extends TestCase
{
    private ComentarioService $service;
    private EntityManagerInterface|MockObject $em;
    private ComentarioRepository|MockObject   $repo;

    protected function setUp(): void
    {
        $this->em         = $this->createMock(EntityManagerInterface::class);
        $this->repo       = $this->createMock(ComentarioRepository::class);
        $translator       = $this->createMock(TranslatorInterface::class);

        // El translator devuelve la clave directamente para simplificar los tests
        $translator->method('trans')->willReturnArgument(0);

        $this->service = new ComentarioService(
            $this->em,
            $this->repo,
            $translator,
            new NullLogger()
        );
    }

    /** @test */
    public function crearComentario_conDatosValidos_persisteYDevuelveEntidad(): void
    {
        // Arrange: capturar la entidad que se pasa a persist()
        $entidadGuardada = null;
        $this->em->expects($this->once())
             ->method('persist')
             ->with($this->callback(function (Comentario $c) use (&$entidadGuardada) {
                 $entidadGuardada = $c;
                 return true;
             }));
        $this->em->expects($this->once())->method('flush');

        // Act
        $resultado = $this->service->crearComentario(5, 10, 'Título', 'Contenido', 4);

        // Assert
        $this->assertInstanceOf(Comentario::class, $resultado);
        $this->assertSame(5, $resultado->getIdProducto());
        $this->assertSame(4, $resultado->getValoracion());
        $this->assertFalse($resultado->isAprobado()); // Siempre empieza sin aprobar
        $this->assertInstanceOf(\DateTimeImmutable::class, $resultado->getFechaCreacion());
    }

    /** @test */
    public function crearComentario_conValoracionFueraDeRango_lanzaExcepcion(): void
    {
        $this->expectException(\InvalidArgumentException::class);
        $this->service->crearComentario(5, 10, 'Título', 'Contenido', 6); // 6 es inválido
    }

    /** @test */
    public function crearComentario_conContenidoVacio_lanzaExcepcion(): void
    {
        $this->expectException(\InvalidArgumentException::class);
        $this->service->crearComentario(5, 10, 'Título', '   ', 3); // Solo espacios
    }

    /** @test */
    public function aprobarComentario_conIdExistente_cambiaBoolAprobado(): void
    {
        $comentario = new Comentario();
        $comentario->setAprobado(false);

        $this->repo->method('find')->with(1)->willReturn($comentario);
        $this->em->expects($this->once())->method('flush');

        $this->service->aprobarComentario(1);

        $this->assertTrue($comentario->isAprobado());
    }

    /** @test */
    public function aprobarComentario_conIdInexistente_lanzaExcepcion(): void
    {
        $this->repo->method('find')->willReturn(null);
        $this->expectException(\RuntimeException::class);
        $this->service->aprobarComentario(999);
    }
}
```

---

## 31.4 Test unitario de un CommandHandler

```php
<?php
// Ejemplo 31.3 — Test de un CommandHandler CQRS
// Archivo: tests/Unit/CommandHandler/AddComentarioCommandHandlerTest.php

class AddComentarioCommandHandlerTest extends TestCase
{
    /** @test */
    public function handle_conCommandValido_persisteComentario(): void
    {
        $em = $this->createMock(EntityManagerInterface::class);

        $persistedEntity = null;
        $em->expects($this->once())
           ->method('persist')
           ->with($this->callback(function (Comentario $c) use (&$persistedEntity) {
               $persistedEntity = $c;
               return true;
           }));
        $em->expects($this->once())->method('flush');

        $handler = new AddComentarioCommandHandler($em);
        $command = new AddComentarioCommand(5, 10, 'Título', 'Contenido', 4);

        $handler->handle($command);

        $this->assertNotNull($persistedEntity);
        $this->assertSame(5, $persistedEntity->getIdProducto());
        $this->assertFalse($persistedEntity->isAprobado());
    }
}
```

---

```bash
# Ejemplo 31.4 — Ejecutar los tests

# Todos los tests
vendor/bin/phpunit

# Solo tests unitarios
vendor/bin/phpunit --testsuite Unit

# Un archivo específico
vendor/bin/phpunit tests/Unit/Service/ComentarioServiceTest.php

# Con cobertura de código (requiere Xdebug o PCOV)
vendor/bin/phpunit --coverage-html coverage/
```

---

### Resumen del Capítulo 31

| Concepto | Clave a recordar |
|---|---|
| Mocks | Usar `createMock()` para reemplazar dependencias en tests unitarios |
| `setUp()` | Inicializar el servicio bajo prueba antes de cada test |
| Naming de tests | `método_condición_resultadoEsperado()` es la convención más clara |
| `expects($this->once())` | Verificar que un método se llama exactamente una vez |
| DI facilita tests | Si el servicio usa DI, es trivial inyectar mocks; sin DI es imposible |

---

# Capítulo 32: Buenas prácticas y seguridad

> **En este capítulo aprenderás:**
> - La checklist de seguridad obligatoria para cualquier módulo
> - Las optimizaciones de rendimiento más impactantes
> - Los estándares de codificación de PrestaShop
> - Los errores más comunes (y cómo evitarlos)
>
> **Nivel:** Todos los niveles  
> **Versión mínima PS:** 8.0.0

---

## 32.1 Checklist de seguridad

### SQL Injection

```php
<?php
// Ejemplo 32.1 — Prevenir SQL Injection

// ❌ NUNCA concatenar inputs del usuario directamente en SQL
$nombre = $_GET['nombre'];
Db::getInstance()->execute("SELECT * FROM ps_product WHERE name = '$nombre'");

// ✅ Usar pSQL() para sanitizar inputs en SQL nativo
$nombre = pSQL(Tools::getValue('nombre'));
Db::getInstance()->execute("SELECT * FROM ps_product WHERE name = '$nombre'");

// ✅✅ Aún mejor: usar el QueryBuilder de Doctrine (parametrizado automáticamente)
$qb->where('p.name = :nombre')->setParameter('nombre', $nombre);
```

### XSS (Cross-Site Scripting)

```php
<?php
// Ejemplo 32.2 — Prevenir XSS en PHP y en plantillas

// En PHP al generar HTML
echo htmlspecialchars($userInput, ENT_QUOTES, 'UTF-8');

// En Smarty (escape explícito)
// {$variable|escape:'html':'UTF-8'}

// En Twig (escape automático — el más seguro)
// {{ variable }}   ← escapado automáticamente
// {{ variable|raw }}  ← SIN escape; SOLO para HTML de confianza
```

### CSRF (Cross-Site Request Forgery)

```php
<?php
// Ejemplo 32.3 — Verificar token CSRF en formularios POST

if (Tools::isSubmit('submitForm')) {
    // Verificación manual del token (en contexto legacy)
    if (!Tools::getToken(false) === Tools::getValue('token')) {
        die('Token de seguridad inválido');
    }
    // Procesar formulario...
}

// En Symfony Forms: la verificación CSRF es automática cuando
// csrf_protection: true (que es el valor por defecto)
```

### Subida de archivos

```php
<?php
// Ejemplo 32.4 — Subida de archivos segura

// ❌ NUNCA confiar en el nombre original del archivo
$filename = $_FILES['archivo']['name']; // ← puede ser "../../config.php"

// ✅ Validar tipo MIME real (no la extensión del nombre)
$finfo    = new \finfo(FILEINFO_MIME_TYPE);
$mimeType = $finfo->file($_FILES['archivo']['tmp_name']);

$allowedMimes = ['image/jpeg', 'image/png', 'image/gif', 'image/webp'];
if (!in_array($mimeType, $allowedMimes, true)) {
    throw new \RuntimeException('Tipo de archivo no permitido.');
}

// ✅ Generar nombre aleatorio seguro
$extension   = pathinfo($_FILES['archivo']['name'], PATHINFO_EXTENSION);
$safeNameFile = bin2hex(random_bytes(16)) . '.' . strtolower($extension);
$destination = _PS_MODULE_DIR_ . $this->name . '/views/img/uploads/' . $safeNameFile;

if (!move_uploaded_file($_FILES['archivo']['tmp_name'], $destination)) {
    throw new \RuntimeException('Error al mover el archivo.');
}
```

---

## 32.2 Optimizaciones de rendimiento

```php
<?php
// Ejemplo 32.5 — Caché de consultas frecuentes con el sistema de caché de PS

private function getProductosDestacados(): array
{
    // Clave única por idioma y tienda para evitar mezclar datos
    $cacheKey = 'mi_modulo_destacados_'
        . $this->context->language->id . '_'
        . $this->context->shop->id;

    if (Cache::isStored($cacheKey)) {
        return Cache::retrieve($cacheKey);
    }

    $productos = Db::getInstance()->executeS('SELECT ...');
    Cache::store($cacheKey, $productos);

    return $productos;
}
```

> **Rendimiento — Regla de oro:** Mide antes de optimizar. Usa el **Symfony Profiler**
> (activado en modo `APP_ENV=dev`) para identificar las consultas lentas y los
> servicios que tardan más.

---

## 32.3 Estándares de codificación de PrestaShop

PrestaShop sigue PSR-12 con estas reglas adicionales:

| Regla | Correcto | Incorrecto |
|---|---|---|
| Yoda conditions | `$valor == 0` | `0 == $valor` |
| Concatenaciones | `'texto ' . $variable` | `'texto'.$variable` |
| Directorio actual | `__DIR__` | `dirname(__FILE__)` |
| declare strict | `declare(strict_types=1);` en src/ | Omitirlo |
| Acceso a BD | `_DB_PREFIX_` | Hardcodear `ps_` |
| Global variables | Nunca | `global $link;` |

---

## 32.4 Lista de verificación previa a la publicación

```
SEGURIDAD
- [ ] Todos los inputs del usuario pasan por pSQL() o QueryBuilder
- [ ] Todos los outputs están escapados (Twig auto, Smarty manual)
- [ ] Los formularios POST verifican token CSRF
- [ ] La subida de archivos valida tipo MIME y usa nombre aleatorio
- [ ] Los controladores verifican permisos del empleado

ESTRUCTURA
- [ ] index.php en CADA subdirectorio del módulo
- [ ] logo.png de 32×32 px en la raíz
- [ ] composer.json con autoloading PSR-4 correcto
- [ ] install() y uninstall() correctamente implementados
- [ ] Scripts de upgrade/ para cada nueva versión

CÓDIGO
- [ ] PHP CS Fixer no reporta errores
- [ ] PHPStan nivel 5+ sin errores críticos
- [ ] Tests unitarios pasan correctamente
- [ ] Sin var_dump(), die(), echo de depuración
- [ ] Sin credenciales hardcodeadas
- [ ] Sin rutas absolutas del servidor

INTERNACIONALIZACIÓN
- [ ] Todos los textos visibles envueltos en $this->trans()
- [ ] Archivos de traducción en translations/
```

---

### Resumen del Capítulo 32

| Área | Regla crítica |
|---|---|
| SQL | `pSQL()` siempre o usar el QueryBuilder de Doctrine |
| XSS | En Twig es automático; en Smarty siempre `\|escape:'html'` |
| CSRF | Verificar token en POST; Symfony Forms lo hace automáticamente |
| Archivos | Validar MIME real, no extensión; nombre aleatorio |
| Rendimiento | Carga condicional de CSS/JS; caché para consultas frecuentes |
| Estándares | PHP CS Fixer + PHPStan desde el primer día |

---

# Capítulo 33: Distribución y publicación

> **En este capítulo aprenderás:**
> - Cómo preparar el ZIP del módulo para distribución
> - El proceso de validación de PrestaShop Addons
> - El versionado semántico y los scripts de upgrade
> - Cómo automatizar la generación del release con Git
>
> **Nivel:** Básico  
> **Versión mínima PS:** 8.0.0

---

## 33.1 Generar el ZIP de distribución

```bash
# Ejemplo 33.1 — Script para generar el ZIP de release
# Archivo: scripts/build.sh

#!/bin/bash
VERSION=$(grep "version" composer.json | head -1 | awk -F'"' '{print $4}')
MODULE_NAME="mi_modulo"
OUTPUT="${MODULE_NAME}-${VERSION}.zip"

echo "Generando release v${VERSION}..."

# Limpiar artefactos de desarrollo
composer install --no-dev --optimize-autoloader

# Crear ZIP excluyendo archivos que no deben distribuirse
zip -r "${OUTPUT}" "${MODULE_NAME}/" \
    --exclude "${MODULE_NAME}/.git/*" \
    --exclude "${MODULE_NAME}/tests/*" \
    --exclude "${MODULE_NAME}/.php-cs-fixer*" \
    --exclude "${MODULE_NAME}/phpunit.xml*" \
    --exclude "${MODULE_NAME}/.env*" \
    --exclude "${MODULE_NAME}/scripts/*" \
    --exclude "${MODULE_NAME}/node_modules/*"

echo "✓ Archivo generado: ${OUTPUT}"
```

---

## 33.2 Versionado semántico

```
MAJOR.MINOR.PATCH

MAJOR → Cambio incompatible con versiones anteriores (migración necesaria)
MINOR → Nueva funcionalidad compatible hacia atrás (upgrade automático)
PATCH → Corrección de bugs compatible hacia atrás (upgrade recomendado)
```

| Versión | Cuándo incrementar | Script upgrade necesario |
|---|---|---|
| 1.0.0 → 1.0.1 | Bug fix sin cambios de BD | No |
| 1.0.0 → 1.1.0 | Nueva funcionalidad, cambios en BD | Sí: `upgrade-1.1.0.php` |
| 1.0.0 → 2.0.0 | Cambio incompatible (nueva estructura) | Sí: `upgrade-2.0.0.php` |

---

## 33.3 Tagging de versiones en Git

```bash
# Ejemplo 33.2 — Flujo de release con Git tags

# Preparar el release
git add .
git commit -m "chore: preparar release v1.2.0"

# Crear el tag
git tag -a v1.2.0 -m "Release 1.2.0: añadir sistema de comentarios y grid"

# Listar tags
git tag -l

# El historial de tags documenta el progreso del módulo:
# v1.0.0 — Versión inicial
# v1.0.1 — Fix: error al instalar en PS 8.2
# v1.1.0 — Feature: panel de configuración Symfony
# v1.2.0 — Feature: sistema de comentarios con grid
```

---

### Resumen del Capítulo 33

| Concepto | Clave a recordar |
|---|---|
| ZIP de distribución | Excluir `.git/`, `tests/`, `node_modules/` y archivos de dev |
| `--no-dev` | `composer install --no-dev` reduce el tamaño del vendor/ de forma significativa |
| Semver | MAJOR.MINOR.PATCH; cada MINOR o MAJOR que cambia la BD necesita script upgrade |
| Git tags | Documenta el historial de releases; facilita el rollback |
| Validador de Addons | Verificar en https://validator.prestashop.com antes de enviar |

---

# APÉNDICES

---

# Apéndice A: Referencia rápida de hooks más utilizados

## Hooks de visualización — Front office

| Hook | Posición en la página | Uso típico |
|---|---|---|
| `displayHeader` | `<head>` de todas las páginas | CSS/JS globales |
| `displayFooter` | Antes del `</body>` | Scripts de analytics, widgets |
| `displayHome` | Página de inicio | Banners, sliders, productos destacados |
| `displayBanner` | Bajo el banner principal | Promociones |
| `displayNavFullWidth` | Navegación principal | Menús personalizados |
| `displayLeftColumn` | Columna izquierda | Filtros, menús laterales |
| `displayRightColumn` | Columna derecha | Widgets, promociones |
| `displayProductAdditionalInformation` | Ficha de producto | Contenido extra del producto |
| `displayProductButtons` | Botones del producto | Botones personalizados |
| `displayShoppingCartFooter` | Carrito | Notas, condiciones |
| `displayOrderConfirmation` | Página de confirmación | Píxeles de conversión, agradecimientos |

## Hooks de acción — Front office

| Hook | Cuándo se dispara | Parámetros clave |
|---|---|---|
| `actionCartSave` | Al guardar el carrito | `cart` |
| `actionValidateOrder` | Al validar un pedido | `cart`, `order`, `customer` |
| `actionOrderStatusUpdate` | Al cambiar estado del pedido | `newOrderStatus`, `id_order` |
| `actionCustomerAccountAdd` | Al crear cuenta de cliente | `newCustomer` |
| `actionAuthentication` | Al autenticar un cliente | `customer` |
| `actionProductAdd` | Al añadir producto al catálogo | `object` (Product) |
| `actionObjectProductInStockAfter` | Al reabastecer un producto | `object` |

## Hooks del back office

| Hook | Uso |
|---|---|
| `displayBackOfficeHeader` | CSS/JS globales en el BO |
| `actionAdminControllerSetMedia` | CSS/JS en un controlador específico |
| `displayAdminProductsExtra` | Contenido extra en el formulario de producto |
| `displayAdminOrderTabContent` | Contenido en pestaña de pedido |
| `displayAdminOrderTabLink` | Pestaña nueva en la vista de pedido |
| `displayAdminCustomers` | Contenido en la ficha del cliente |
| `displayDashboardTop` | Bloque en el dashboard del BO |

## Hooks de formularios Symfony del core

| Entidad | Constructor | Post-crear | Post-actualizar |
|---|---|---|---|
| Cliente | `actionCustomerFormBuilderModifier` | `actionAfterCreateCustomerFormHandler` | `actionAfterUpdateCustomerFormHandler` |
| Producto | `actionProductFormBuilderModifier` | `actionAfterCreateProductFormHandler` | `actionAfterUpdateProductFormHandler` |
| Categoría | `actionCategoryFormBuilderModifier` | `actionAfterCreateCategoryFormHandler` | `actionAfterUpdateCategoryFormHandler` |
| Empleado | `actionEmployeeFormBuilderModifier` | `actionAfterCreateEmployeeFormHandler` | `actionAfterUpdateEmployeeFormHandler` |

## Hooks de Grid del core

| Grid | Definición | Query Builder |
|---|---|---|
| Clientes | `actionCustomerGridDefinitionModifier` | `actionCustomerGridQueryBuilderModifier` |
| Productos | `actionProductGridDefinitionModifier` | `actionProductGridQueryBuilderModifier` |
| Pedidos | `actionOrderGridDefinitionModifier` | `actionOrderGridQueryBuilderModifier` |
| Empleados | `actionEmployeeGridDefinitionModifier` | `actionEmployeeGridQueryBuilderModifier` |

---

# Apéndice B: Guía de migración PS 1.6/1.7 → PS 8/9

## B.1 De HelperForm a Symfony Forms

**Antes (PS 1.6 / PS 1.7):**

```php
<?php
// getContent() genera el formulario directamente
public function getContent(): string
{
    if (Tools::isSubmit('submit')) {
        Configuration::updateValue('MI_OPCION', Tools::getValue('MI_OPCION'));
    }
    $helper = new HelperForm();
    // ... larga configuración ...
    return $helper->generateForm([$fields]);
}
```

**Después (PS 8 / PS 9):**

```php
<?php
// getContent() solo redirige; el formulario está en un controlador Symfony
public function getContent(): void
{
    Tools::redirectAdmin(
        $this->get('router')->generate('mi_modulo_configuracion')
    );
}
// La lógica vive en src/Controller/Admin/ConfiguracionController.php
// El formulario vive en src/Form/Type/ConfiguracionFormType.php
```

**Pasos de migración:**
1. Crear `src/Form/Type/ConfiguracionFormType.php` con los campos equivalentes
2. Crear `src/Controller/Admin/ConfiguracionController.php`
3. Declarar la ruta en `config/routes.yml`
4. Crear la plantilla Twig en `views/templates/admin/configuracion.html.twig`
5. Cambiar `getContent()` para que solo redirija
6. Declarar el FormType como servicio en `config/services.yml`

---

## B.2 De HelperList a Grid component

**Antes:**

```php
<?php
$helper = new HelperList();
$helper->identifier = 'id_mi_entidad';
$helper->title      = 'Mis Entidades';
$helper->fields_list = [
    'id_mi_entidad' => ['title' => 'ID'],
    'nombre'         => ['title' => 'Nombre'],
];
return $helper->generateList($lista, $helper->fields_list);
```

**Después:** Ver Capítulo 17 completo (Grid component).

**Pasos de migración:**
1. Crear `src/Grid/Definition/Factory/MiGridDefinitionFactory.php`
2. Crear `src/Grid/Data/Factory/MiGridDataFactory.php`
3. Crear el controlador con `$this->presentGrid()`
4. Declarar los servicios en `config/services.yml`
5. Crear la plantilla con `{% include 'grid_panel.html.twig' %}`

---

## B.3 De ObjectModel a Doctrine ORM

**Antes:**

```php
<?php
class MiEntidad extends ObjectModel
{
    public $nombre;
    public static $definition = [
        'table'   => 'mi_entidad',
        'primary' => 'id_mi_entidad',
        'fields'  => ['nombre' => ['type' => self::TYPE_STRING]],
    ];
}

$e = new MiEntidad();
$e->nombre = 'Test';
$e->add();
```

**Después:**

```php
<?php
#[ORM\Entity]
#[ORM\Table(name: 'ps_mi_entidad')]
class MiEntidad
{
    #[ORM\Id, ORM\GeneratedValue, ORM\Column(type: 'integer')]
    private ?int $id = null;

    #[ORM\Column(type: 'string', length: 255)]
    private string $nombre;

    public function getNombre(): string { return $this->nombre; }
    public function setNombre(string $v): static { $this->nombre = $v; return $this; }
}

$e = new MiEntidad();
$e->setNombre('Test');
$em->persist($e);
$em->flush();
```

**Notas importantes al migrar:**
- Crear la tabla SQL manualmente en `upgrade/upgrade-X.Y.Z.php`
- No usar `doctrine:schema:update --force` (genera foreign keys incompatibles)
- Migrar los datos existentes en el script de upgrade
- Si la entidad tiene campos multilingüe, implementarlos manualmente con Doctrine

---

## B.4 De AdminController a FrameworkBundleAdminController

**Antes:**

```php
<?php
class AdminMiModuloController extends ModuleAdminController
{
    public function __construct()
    {
        $this->bootstrap   = true;
        $this->table       = 'mi_entidad';
        $this->className   = 'MiEntidad';
        parent::__construct();
        $this->meta_title  = 'Mi Módulo';
    }
}
```

**Después:**

```php
<?php
class MiController extends FrameworkBundleAdminController
{
    public function indexAction(): Response
    {
        return $this->render(
            '@Modules/mi_modulo/views/templates/admin/index.html.twig'
        );
    }
}
```

---

# Apéndice C: Herramientas y recursos del desarrollador

## Herramientas esenciales

| Herramienta | Propósito | Instalación |
|---|---|---|
| PHP CS Fixer | Estándares de código | `composer require --dev friendsofphp/php-cs-fixer` |
| PHPStan | Análisis estático | `composer require --dev phpstan/phpstan` |
| PHPUnit | Tests unitarios | `composer require --dev phpunit/phpunit` |
| Composer | Gestión de dependencias | getcomposer.org |
| Git | Control de versiones | git-scm.com |
| PS Validator | Validar módulo para Addons | validator.prestashop.com |

## Módulos de referencia por tema

| Módulo | Tema principal | Nivel |
|---|---|---|
| `demosymfonyformsimple` | Formulario Symfony básico | Básico |
| `demosymfonyform` | Tipos de campo de PrestaShop | Básico/Medio |
| `demo_grid` | Grid component completo | Medio |
| `democontrollertabs` | Tabs de navegación | Medio |
| `demoextendsymfonyform1` | Extender formularios (texto) | Medio |
| `demoextendsymfonyform2` | Extender formularios (archivos) | Medio |
| `demoextendsymfonyform3` | Extender formularios (CQRS) | Avanzado |
| `demodoctrine` | Doctrine ORM | Medio |
| `democonsolecommand` | Comandos de consola | Medio |
| `demomoduleroutes` | Rutas del front office | Medio |
| `demoproductextracontent` | Contenido extra en producto | Básico |
| `demoproductform` | Formulario de producto | Medio |
| `demoextendgrid` | Extender grid existente | Medio |
| `demojsrouting` | JS Router del BO | Medio |
| `api_module` | API Platform REST | Avanzado |
| `demovieworderhooks` | Hooks de pedidos | Medio |
| `demomultistoreform` | Formularios multitienda | Avanzado |
| `demoextendtemplates` | Extender plantillas Twig | Medio |
| `demofiltermodules` | Filtrar hooks por contexto | Medio |
| `example_module_mailtheme` | Temas de email | Básico |

## Comandos de consola más usados

```bash
# Ejemplo C.1 — Referencia de comandos de consola

# Caché
php bin/console cache:clear                          # Limpiar caché completa
php bin/console cache:warmup                         # Precalentar la caché

# Módulos
php bin/console prestashop:module install mi_modulo
php bin/console prestashop:module uninstall mi_modulo
php bin/console prestashop:module enable mi_modulo
php bin/console prestashop:module disable mi_modulo

# Diagnóstico
php bin/console debug:container | grep mi_modulo    # Ver servicios del módulo
php bin/console debug:router | grep mi_modulo       # Ver rutas del módulo

# Doctrine
php bin/console doctrine:schema:update --dump-sql   # Ver el SQL generado (solo referencia)

# Calidad de código
vendor/bin/php-cs-fixer fix --dry-run               # Verificar sin cambiar
vendor/bin/php-cs-fixer fix                         # Corregir
vendor/bin/phpunit                                   # Ejecutar tests
```

## Recursos de documentación

| Recurso | URL |
|---|---|
| Documentación oficial PS 9 | https://devdocs.prestashop-project.org/9/ |
| Repositorio de ejemplos | https://github.com/PrestaShop/example-modules |
| Blog de desarrolladores | https://build.prestashop-project.org/ |
| Validador de módulos | https://validator.prestashop.com/ |
| GitHub de PrestaShop | https://github.com/PrestaShop/PrestaShop |
| Documentación de Symfony | https://symfony.com/doc/6.4/ |
| Documentación de Doctrine | https://www.doctrine-project.org/projects/doctrine-orm/ |
| Foro de desarrolladores | https://www.prestashop.com/forums/ |
| Tutorial oficial de módulos | https://devdocs.prestashop-project.org/9/modules/creation/tutorial/ |

---

# Conclusión

A lo largo de este manual hemos recorrido el camino completo del desarrollo de módulos para PrestaShop, desde la estructura más básica hasta los patrones arquitectónicos más avanzados.

**Los puntos clave que debes llevarte:**

1. **La dualidad es temporal.** PrestaShop está migrando a Symfony. Escribe código nuevo en la capa moderna; la legacy está en mantenimiento.

2. **Los hooks son el corazón.** Todo módulo vive y muere por sus hooks. Conoce los que necesitas antes de escribir una línea de código.

3. **Doctrine sobre ObjectModel.** Para módulos nuevos o actualizados, Doctrine ORM es más mantenible, testeable y estándar.

4. **Inyección de dependencias siempre.** Un servicio que crea sus propias dependencias es difícil de testear. Inyecta siempre.

5. **Testea desde el primer día.** Los tests no son una carga, son una inversión. Un módulo sin tests no es un módulo profesional.

6. **Seguridad no es opcional.** `pSQL()`, escape de salidas, validación de archivos y tokens CSRF son obligatorios, no opcionales.

7. **Los módulos de ejemplo son tu mejor referencia.** Antes de implementar algo, comprueba si hay un módulo de ejemplo en https://github.com/PrestaShop/example-modules.

---

**Versión 1.0 — PrestaShop 8.x / 9.x — Abril 2026**

*Documentación oficial: https://devdocs.prestashop-project.org/*  
*Módulos de ejemplo: https://github.com/PrestaShop/example-modules*
