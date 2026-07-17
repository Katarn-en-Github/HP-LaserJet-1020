# Guía completa: HP LaserJet 1020 + CUPS 2.4.10 en Raspberry Pi OS Trixie

## Introducción

Esta guía documenta el procedimiento completo para hacer funcionar una
**HP LaserJet 1020** en una Raspberry Pi Zero W con Raspberry Pi OS
Trixie y CUPS 2.4.10.

## Síntomas

-   La impresora aparece correctamente en CUPS.
-   Los trabajos terminan como "Completed".
-   La impresora no enciende el LED rojo, no hace ruido y no imprime.

## Causa

La HP LaserJet 1020 no almacena permanentemente su firmware. Cada vez
que se energiza necesita que el host le cargue `sihp1020.dl`.

## Diagnóstico

Comprobar:

``` bash
journalctl -b | grep -i hplj1020
```

Si aparece:

``` text
Missing HP LaserJet 1020 firmware file /lib/firmware/hp/sihp1020.dl
```

el problema está identificado.

## Instalación del controlador

``` bash
sudo apt update
sudo apt install printer-driver-foo2zjs
```

Verificar:

``` bash
which foo2zjs-wrapper
```

Debe devolver:

``` text
/usr/bin/foo2zjs-wrapper
```

## Habilitar paquetes fuente

Editar `/etc/apt/sources.list.d/raspbian.sources`:

Cambiar:

``` text
Types: deb
```

por:

``` text
Types: deb deb-src
```

Actualizar:

``` bash
sudo apt update
```

## Descargar el código fuente

``` bash
mkdir -p ~/temp
cd ~/temp
apt source foo2zjs
```

Dentro del árbol aparece `getweb.in`, que revela desde dónde descargar
el firmware.

## Obtener el firmware

``` bash
cd ~/temp/foo2zjs-20200505dfsg0
wget https://www.quirinux.org/printers/sihp1020.tar.gz
tar -xzf sihp1020.tar.gz
```

## Convertir la imagen

Verificar:

``` bash
which arm2hpdl
```

Convertir:

``` bash
arm2hpdl sihp1020.img > sihp1020.dl
```

## Instalar el firmware

``` bash
sudo mkdir -p /lib/firmware/hp
sudo cp sihp1020.dl /lib/firmware/hp/
```

## Carga automática

Desconectar y volver a conectar la impresora.

Verificar:

``` bash
journalctl -b | grep -i hplj1020
```

Debe verse:

``` text
loading HP LaserJet 1020 firmware ...
download successful.
```

## Pruebas

Estado:

``` bash
lpstat -t
```

Dispositivos:

``` bash
lpinfo -v
```

Impresión:

``` bash
lp -d HP_LaserJet_1020 /usr/share/cups/data/testprint
```

También imprimir desde la interfaz web de CUPS.

## Flujo de funcionamiento

1.  CUPS recibe el trabajo.
2.  `foomatic-rip` invoca `foo2zjs-wrapper`.
3.  `foo2zjs` genera el flujo ZjStream.
4.  Udev ejecuta `/usr/lib/udev/hplj1020`.
5.  El script detecta `/lib/firmware/hp/sihp1020.dl`.
6.  El firmware se envía por USB.
7.  La impresora queda operativa.
8.  El trabajo se imprime.

## Archivos importantes

-   `/lib/firmware/hp/sihp1020.dl` (imprescindible)
-   `/usr/lib/udev/hplj1020`
-   `/usr/bin/foo2zjs-wrapper`

## Respaldo recomendado

Conservar una copia de:

``` text
/lib/firmware/hp/sihp1020.dl
```

En una instalación nueva solo será necesario:

``` bash
sudo mkdir -p /lib/firmware/hp
sudo cp sihp1020.dl /lib/firmware/hp/
```

## Problemas frecuentes

-   **No existe `foo2zjs-wrapper`**: instalar `printer-driver-foo2zjs`.
-   **Missing HP LaserJet 1020 firmware**: falta `sihp1020.dl`.
-   **El trabajo termina pero no imprime**: verificar que el firmware se
    cargó con `journalctl`.
-   **La impresora vuelve a quedar muda tras apagarla**: normal; el
    firmware debe cargarse nuevamente al reconectarla.

## Conclusión

Una vez instalado el firmware y el controlador, la HP LaserJet 1020
imprime correctamente desde CUPS y desde la línea de comandos. El único
componente que Raspberry Pi OS Trixie no proporciona automáticamente es
`sihp1020.dl`, por lo que conviene conservar una copia de ese archivo.
