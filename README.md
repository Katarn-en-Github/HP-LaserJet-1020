# HP LaserJet 1020 - Respaldo Completo para Raspberry Pi OS Trixie + CUPS 2.4.10
Versión del documento: 1.0
Última actualización: Julio 2026
## Descripción

Este repositorio contiene todos los archivos necesarios para restaurar el funcionamiento de una impresora **HP LaserJet 1020** en una instalación limpia de **Raspberry Pi OS Trixie** utilizando **CUPS 2.4.10**.

Este respaldo fue creado después de resolver el problema del firmware faltante:

```
/lib/firmware/hp/sihp1020.dl
```

que impide que la HP LaserJet 1020 imprima correctamente bajo Raspberry Pi OS Trixie.

El procedimiento fue probado exitosamente sobre una Raspberry Pi Zero W.

---

# Hardware utilizado

- Raspberry Pi Zero W
- Raspberry Pi OS Trixie (32 bits)
- CUPS 2.4.10
- HP LaserJet 1020 (USB)

---

# Estado

✔ Firmware cargado automáticamente por udev.

✔ CUPS detecta correctamente la impresora.

✔ Página de prueba impresa correctamente desde la interfaz web de CUPS.

✔ Impresión normal desde clientes Linux.

---

# Estructura del respaldo

```
HP-LaserJet-1020/

README.md

checksums/
    SHA256SUMS

docs/
    00. Proceso completo HP LaserJet 1020 en Raspberry Pi OS Trixie con CUPS 2.4.10.md
    01. Instalación de CUPS en Raspberry Pi OS Trixie.md
    02. HP LaserJet 1020 en Raspberry Pi OS Trixie con CUPS.md
    03. Configuración de clientes.md
    Fuentes.md
    HP1020 Guía del Usuario.pdf

firmware/
    sihp1020.tar.gz
    sihp1020.img
    sihp1020.dl

ppd/
    HP-LaserJet_1020.ppd

scripts/
    hplj1020

source/
    foo2zjs-20200505dfsg0/
    foo2zjs_20200505dfsg0.orig.tar.xz
    foo2zjs_20200505dfsg0-3.debian.tar.xz
    foo2zjs_20200505dfsg0-3.dsc

system-info/
    cups-packages.txt
    cups-policy.txt
    cups-version.txt
    kernel.txt
    os-release.txt
    paquetes.txt
    raspbian.sources
    raspi.sources
```

---

# Contenido de cada carpeta

## firmware/

Contiene el firmware original utilizado por la impresora.

Es el recurso más importante del respaldo.

```
sihp1020.tar.gz
```

Firmware descargado.

```
sihp1020.img
```

Imagen original del firmware.

```
sihp1020.dl
```

Firmware convertido mediante **arm2hpdl**.

Debe copiarse a:

```
/lib/firmware/hp/
```

---

## scripts/

Contiene el script instalado por el paquete foo2zjs.

```
/usr/lib/udev/hplj1020
```

Este script es el encargado de detectar la impresora y cargar automáticamente el firmware cada vez que ésta se conecta.

---

## ppd/

Archivo PPD utilizado por CUPS que contiene una copia independiente del PPD para facilitar reinstalaciones rápidas sin tener que navegar dentro del código fuente.

---

## source/

Código fuente completo del paquete Debian **foo2zjs**, incluyendo todos los parches aplicados por Debian.

Permite reconstruir completamente el procedimiento sin depender de Internet.

---

## docs/

Documentación completa del proceso.

Incluye:

- guía paso a paso
- explicación técnica
- resolución de problemas
- procedimiento de recuperación

---

## system-info/

Información de la Raspberry Pi donde se verificó el funcionamiento.

Incluye:

- versiones de CUPS
- paquetes instalados
- versión del kernel
- versión del sistema operativo
- repositorios APT utilizados

---

## checksums/

Contiene las firmas SHA256 de todos los archivos del respaldo.

Permite comprobar que ningún archivo ha sido alterado.

Para verificar:

```
cd HP-LaserJet-1020

sha256sum -c checksums/SHA256SUMS
```

---

# Restauración

La guía completa se encuentra en:

```
docs/
Guía_Completa_HP_LaserJet_1020_CUPS_2.4.10_Raspberry_Pi_OS_Trixie.md
```

---

# Resultado esperado

Después de seguir la guía:

- la impresora aparece en CUPS;
- el firmware se carga automáticamente;
- la página de prueba imprime correctamente;
- no aparecen errores de tipo:

```
Missing HP LaserJet 1020 firmware
```

ni

```
Filter failed
```

---

# Fecha del respaldo

Julio de 2026.

---

# Observaciones

Este respaldo fue generado a partir de una instalación funcional y completamente verificada.

La documentación incluida permite repetir todo el proceso incluso si los repositorios o enlaces originales dejan de estar disponibles en el futuro.
