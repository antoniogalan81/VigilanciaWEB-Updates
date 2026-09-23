# VigilanciaWEB — canal de actualizaciones

Aquí solo hay artefactos de distribución: manifiestos, firmas y paquetes de un
sistema de videovigilancia local para comercios pequeños.

```
<canal>/trust.json                   qué claves son de fiar, y su versión
<canal>/trust.json.sig               firma de la lista, por una clave ya confiable
<canal>/<modulo>/manifest.json       qué versión hay de ese módulo
<canal>/<modulo>/manifest.json.sig   firma ECDSA P-256 de los bytes de arriba
<canal>/<modulo>/<modulo>-<v>.zip    el paquete
```

Canales: `dev`, `store-beta`, `stable`.
Módulos: `recorder` (crítico), `ai`, `diagnostics`, `launcher`.

## Por qué esto es público

Para que una instalación pueda leerlo **sin ninguna credencial**. Un token de
lectura caduca, y renovarlo obligaría a desplazarse a cada instalación: justo lo
que este diseño existe para evitar.

Ser público no debilita nada, porque la seguridad nunca estuvo en el
transporte:

* El transporte entrega bytes.
* **La firma decide si esos bytes son nuestros.**

Quien controle este repositorio puede dejar a una instalación sin
actualizaciones —cosa que también consigue cortándole Internet— pero **no puede
instalarle nada**: sin la clave privada, que no sale del PC de desarrollo,
cualquier manifiesto que publicara se rechaza.

El manifiesto tampoco lleva URLs ni comandos: solo un nombre de fichero y un
hash. Un manifiesto manipulado no puede desviar una descarga a otro servidor.

## Qué NO hay aquí

Ninguna credencial, ninguna clave privada, ninguna grabación, ninguna
configuración de instalación, ninguna dirección de red real y ningún dato de
ninguna persona. Se comprueba automáticamente —dentro de cada ZIP— antes de
cada publicación.

## Rotación de claves

`trust.json` lleva un número de versión y solo se sustituye por otra **firmada
por una clave que ya era de fiar** y con versión estrictamente mayor. Así se
puede cambiar la clave de firma a distancia sin tocar ninguna instalación, y no
se puede reponer una lista antigua con una clave ya revocada.

No subir nada a mano. Se publica con:

    python herramientas/publicar_github.py <version>
