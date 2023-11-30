---
layout: post
title: Deserialización insegura
tags: Deserialización insegura
math: true
date: 2023-10-28 15:32 +0800
---
Definición
La serialización es el proceso de transformación de un objeto o estructura de datos a un formato en el que pueda ser guardado y transportado para su posterior reconstrucción. Este proceso puede ser engañado por un atacante de dos formas. La primera consiste en modificar la lógica de la aplicación o lograr una ejecución remota de código. La segunda consiste en manipular los datos que se transportan en la serialización.

Ejemplo
- Un sitio que guarda en una cookie un objeto serializado con el id de usuario, el rol del usuario, hash de la contraseña, entre otros datos. un atacante modifica la cookie para darse permisos así mismo como administrador y obtener el control total.

Prevención
- Se debe implementar verificaciones de integridad tales como firmas digitales en cualquier objeto serializado con el fin de detectar modificaciones no autorizadas.
- Durante la deserialización es recomendable verificar el tipo de dato ya que por lo general se espera un tipo de dato o clase concreta después de la deserialización.
- El código que realiza la deserialización se debe ejecutar en un entorno conlos mínimos privilegios posibles.
- Se debe registrar cualquier falla en la deserialización y generar alertas de seguridad que indiquen un posible ataque.
- Se debe restringir y monitorear las conexiones (I/O) de red en los servidores o contenedores que utilizan funciones de deserialización.
