# VOID FRAME

Utilidad para **Windows 11** que vive únicamente en la bandeja del sistema y pinta
de negro el marco de las ventanas de escritorio: barra de título negra, texto blanco
y borde negro.

Sin ventana principal, sin instalador y sin dependencias externas.

> **By: Jurdest (Void Lord)**

---

## Requisitos

- **Windows 11** build **22000** o superior.
- **.NET Framework 4.8** (viene incluido de serie en Windows 11).

## Instalación

1. Descarga **`VOID FRAME.exe`** de este repositorio.
2. Ejecútalo y acepta el aviso de UAC. La aplicación pide permisos de administrador
   para poder modificar ventanas de otros procesos.
3. El icono 元 aparece en la bandeja del sistema (en Windows 11 suele estar dentro
   de la flecha `^`; puedes arrastrarlo a la barra de tareas).

Es un ejecutable portable: no escribe nada fuera de tu perfil de usuario y no
necesita instalación. Si quieres tenerlo "instalado", crea una carpeta propia en
`C:\Program Files\VOID FRAME\` y ejecútalo desde ahí.

> **Aviso de SmartScreen:** el ejecutable no está firmado digitalmente, así que
> Windows puede mostrar un aviso de "aplicación desconocida". Es lo normal en
> programas sin certificado de firma de código. Puedes verificar que la descarga
> es íntegra comparando el hash con [`SHA256SUMS.txt`](SHA256SUMS.txt).

## Uso

Haz clic derecho sobre el icono de la bandeja:

| Opción | Qué hace |
|---|---|
| ✓ **Activar** | Activa o desactiva el pintado del marco |
| ✓ **Iniciar con Windows** | Crea o elimina el arranque automático (sin aviso de UAC) |
| **Salir** | Restaura el marco de las ventanas y cierra la aplicación |

- **Doble clic** en el icono: activa o desactiva.
- **Pasar el puntero** por encima: muestra *Activado* o *Desactivado*.

El estado se recuerda entre ejecuciones. Al cerrar la aplicación se restaura el
aspecto original de todas las ventanas.

### Arranque automático

Se implementa con una **tarea programada** de Windows llamada `VOID FRAME`,
configurada con "Ejecutar con los privilegios más altos". Así arranca al iniciar
sesión **sin mostrar el UAC** cada vez.

Por eso no aparece en *Administrador de tareas → Aplicaciones de arranque*: esa
lista solo muestra las entradas del registro `Run` y la carpeta Inicio, no las
tareas programadas. Puedes verla en **Programador de tareas** (`taskschd.msc`) o con:

```powershell
schtasks /Query /TN "VOID FRAME" /V /FO LIST
```

Si mueves el ejecutable de carpeta, desactiva y vuelve a activar
**Iniciar con Windows** para que la tarea apunte a la ruta nueva.

## Qué hace exactamente

Recorre las ventanas de nivel superior y aplica estos atributos documentados de
DWM (`DwmSetWindowAttribute`) a cada una:

| Atributo | Valor | Efecto |
|---|---|---|
| `DWMWA_USE_IMMERSIVE_DARK_MODE` (20) | `1` | Marco en modo oscuro |
| `DWMWA_CAPTION_COLOR` (35) | `0x00000000` | Barra de título negra |
| `DWMWA_TEXT_COLOR` (36) | `0x00FFFFFF` | Texto de la barra en blanco |
| `DWMWA_BORDER_COLOR` (34) | `0x00000000` | Borde de 1 px negro |

Los cambios se reaplican al crearse, mostrarse o activarse una ventana, y cada
2 segundos como red de seguridad. Al desactivar o salir se restaura el color
predeterminado del sistema y el modo oscuro original de cada ventana.

## Limitaciones

Vienen de Windows y del diseño de DWM; no son fallos del programa:

1. **Solo se repinta la barra de título que dibuja Windows.** Muchas aplicaciones
   modernas dibujan su propia barra dentro del área de cliente y no cambian:
   **Explorador de archivos**, **Bloc de notas**, **Terminal**, **Configuración**,
   Calculadora, **Chrome, Edge, Discord, VS Code** y todo lo basado en
   Chromium/Electron, además de **juegos** con marco propio.
2. **No cambia el contenido de las ventanas**, solo el marco.
3. **No crea un borde nuevo**: recolorea el borde de 1 px que ya dibuja DWM.
4. **Ventanas protegidas**: algunos procesos con anti-cheat o de nivel SYSTEM
   pueden ignorar el cambio.
5. **El icono de bandeja depende del Explorador**: si se reinicia y el icono no
   reaparece, vuelve a ejecutar la aplicación.

En un diálogo Win32 clásico (por ejemplo `charmap.exe` o `regedit`) el cambio es
inmediato y completo.

## Código fuente

Este repositorio distribuye **únicamente el ejecutable compilado**. El código
fuente no se publica aquí.

## Licencia

Todos los derechos reservados. Se permite el uso personal y gratuito del ejecutable
tal como se distribuye. No se permite su redistribución, modificación ni la
extracción de su contenido sin autorización del autor.
