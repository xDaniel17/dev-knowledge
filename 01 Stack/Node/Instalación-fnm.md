---
title: Guías Node.js
date: 2026-03-29
lastUpdated: 2026-03-29
tags: [tecnologias, desarrollo]
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: []
migrated: true
migratedFrom: 01 Stack/Node-Backend/Instalación-fnm.md
---

Guía para instalación de node.js con el gestor de versiones `fnm`
**Documentación**: [**Sitio Oficial**](https://github.com/Schniz/fnm)
**Ejemplo**: [Youtube](https://www.youtube.com/watch?v=yB4n_K7dZV8&list=PLUofhDIg_38qm2oPOV-IRTTEKyrVBBaU7) 
### Requisitos
- Instalar: [Rust](https://www.rust-lang.org/learn/get-started)
### Pasos 
1. Instalar Rust en caso sea necesario desde la web oficial
2. Instalar fnm desde la [documentación oficial](https://github.com/Schniz/fnm)
3. Una vez instalado fnm ejecutar:
```PowerShell
	fnm install [versión que se desea instalar]//18.16.0
```
4. Listar las versión:
```PowerShell
	fnm ls
```
5. Establecer versión por defect:
```PowerShell
	fnm alias [versión] default
```
### Ejecución 
Para ejecutar un archivo de node se utiliza el comando `node`
```PowerShell
	node archivo.js
```

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
