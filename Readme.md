Justificacion tecnica del pipeline CI/CD DevSecOps

Este documento describe las decisiones tecnicas tomadas para el pipeline CI/CD aplicado al repositorio practico2/practica2. Se explican las herramientas usadas en cada etapa, su Fase de DevSecOps, el tipo de riesgo que mitigan y la razon de su necesidad aun cuando el sistema ya funcione.

Resumen general
- El pipeline integra componentes de calidad de codigo, seguridad en el desarrollo y automatizacion de builds y despliegues.
- Se utiliza GitHub Actions para orquestar las etapas, con gates que detienen el proceso ante fallos de pruebas, lint, SAST y SCA.
- Se generan artefactos versionados por SHA (tag) para trazabilidad y despliegues reproducibles.

Alcance y arquitectura breve
- Frontend: SPA React gestionado con Vite.
- Backend: microservicios Node (users-service, academic-service, api-gateway).
- Contenedores: Docker Compose para orquestar servicios en pruebas locales y CI.
- Seguridad: Semgrep para SAST, Trivy para SCA y trazabilidad de imagenes versionadas.

Etapas y herramientas (con objetivos de DevSecOps)
- Preparacion y versionado (CI)
  * Herramienta: GitHub Actions (workflow devsecops.yml), caching (actions/cache).
  * Fase DevSecOps: Integracion Continua (CI).
  * Riesgo que mitiga: variaciones deze entornos, dependencias no reproducibles, drift entre ejecuciones.
  * Por que es necesaria: garantiza que cada ejecucion use dependencias reproducibles y que el entorno sea estable entre ejecuciones.

- Instalacion y pruebas Backend (CI)
  * Herramienta: npm ci, npm test (tres servicios: users-service, academic-service, api-gateway).
  * Fase: CI.
  * Riesgo: regresiones funcionales al introducir cambios.
  * Por que: valida que la logica de negocio y las integraciones se mantengan funcionando tras cambios.

- Calidad de codigo (CI)
  * Herramienta: ESLint (lint) para frontend; script existente en frontend/package.json.
  * Fase: CI.
  * Riesgo: codigo con anti-patrones, errores de estilo o vulnerabilidades de patrones de programacion.
  * Por que: mejora mantenibilidad y reduce riesgos de introduccion de fallos debiles.

- SAST (Static Application Security Testing)
  * Herramienta: Semgrep (config auto, severidad ERROR).
  * Fase: SAST.
  * Riesgo: vulnerabilidades y malas practicas de seguridad en el codigo fuente.
  * Por que: deteccion temprana evita fallos de seguridad antes del despliegue.

- Seguridad de dependencias (SCA)
  * Herramienta: Trivy (escaneo de dependencias en imagenes durante el pipeline).
  * Fase: SCA.
  * Riesgo: vulnerabilidades conocidas en librerias y componentes externos.
  * Por que: evita introducir vulnearibilidades heredadas al producto final.

- Build de contenedores y versionado
  * Herramienta: Docker Buildx, docker-compose build.
  * Fase: CI/CD.
  * Riesgo: desalineacion entre artefactos y entornos; falta de versionado claro.
  * Por que: artefactos versionados por SHA permiten despliegues reproducibles y trazables.

- Seguridad de contenedores (escaneo de imagenes)
  * Herramienta: Trivy (escaneo de imagenes) para users-service, academic-service y api-gateway.
  * Fase: SCA/Contenedores.
  * Riesgo: vulnerabilidades en sistema base y librerias del contenedor.
  * Por que: reduce vectores de ataque en el runtime y facilita cumplimiento de policies.

- Pruebas de humo (Smoke tests)
  * Herramienta: docker-compose up -d y consultas basicas (curl health).
  * Fase: Validacion post-despliegue (CD).
  * Riesgo: despliegue incompleto o servicios no disponibles.
  * Por que: verifica que los servicios mas importantes respondan a un endpoint basico.

Gates y criterios de exito
- Cada etapa publica un conjunto de checks. Si alguno falla (lint, pruebas, Semgrep, Trivy, pruebas de humo), el pipeline se detiene y no avanza.
- Esto garantiza que el codigo y las imagenes desplegadas cumplen con criterios de calidad y seguridad antes de poder avanzar a despliegues o commits finales.



