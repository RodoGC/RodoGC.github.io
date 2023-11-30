---
layout: post
title: Deserialización de datos que no son de confianza

tags: Deserialización de datos que no son de confianza

math: true
date: 2023-10-28 15:32 +0800
---
# Definición
A menudo resulta conveniente serializar objetos para la comunicación o guardarlos para su uso posterior. Sin embargo, los datos o códigos deserializados a menudo se pueden modificar sin utilizar las funciones de acceso proporcionadas si no utilizan criptografía para protegerse. Además, cualquier criptografía seguiría siendo seguridad del lado del cliente, lo cual es una suposición de seguridad peligrosa.

No se puede confiar en que los datos que no son de confianza estén bien formados.

Cuando los desarrolladores no imponen restricciones a las "cadenas de dispositivos", o series de instancias e invocaciones de métodos que pueden autoejecutarse durante el proceso de deserialización (es decir, antes de que el objeto se devuelva a la persona que llama), a veces es posible que los atacantes las aprovechen para realizar acciones no autorizadas, como generar un shell.

# Ejemplo 1

Este fragmento de código deserializa un objeto de un archivo y lo utiliza como botón de la interfaz de usuario:

Lenguaje de ejemplo: Java 

intentar {
Archivo archivo = nuevo Archivo ("objeto.obj");
ObjectInputStream en = nuevo ObjectInputStream(nuevo FileInputStream(archivo));
botón javax.swing.JButton = (javax.swing.JButton) in.readObject();
cercar();
}


Este código no intenta verificar el origen o el contenido del archivo antes de deserializarlo. Un atacante puede reemplazar el archivo deseado con un archivo que contenga código malicioso arbitrario que se ejecutará cuando se presione el botón.

Para mitigar esto, defina explícitamente readObject() final para evitar la deserialización. Un ejemplo de esto es:

(buen código)
Lenguaje de ejemplo: Java 

readObject final vacío privado (ObjectInputStream in) lanza java.io.IOException {
throw new java.io.IOException ("No se puede deserializar"); }

# Ejemplo 2

En Python, la biblioteca Pickle maneja los procesos de serialización y deserialización. En este ejemplo derivado de [ REF-467 ], el código recibe y analiza datos y luego intenta autenticar a un usuario basándose en la validación de un token.

Lenguaje de ejemplo: Python 
intentar {
clase EjemploProtocol(protocol.Protocol):
def dataReceived(self, data):

El código que estaría aquí analizaría los datos entrantes
Después de recibir los encabezados, llame a confirmAuth() para autenticarse

def confirmAuth(self, headers):
try:
token = cPickle.loads(base64.b64decode(headers['AuthToken']))
si no check_hmac(token['signature'], token['data'], getSecretKey()):
elevar AuthFail
self.secure_data = token['data' ]
excepto:
aumentar AuthFail
}

Desafortunadamente, el código no verifica que los datos entrantes sean legítimos. Un atacante puede construir un objeto serializado ilegítimo "AuthToken" que crea una instancia de uno de los subprocesos de Python para ejecutar comandos arbitrarios. Por ejemplo, el atacante podría construir un pickle que aproveche el módulo de subproceso de Python, que genera nuevos procesos e incluye una serie de argumentos para diversos usos. Dado que Pickle permite que los objetos definan el proceso sobre cómo deben ser desenganchados, el atacante puede ordenar al proceso de desenganche que llame a Popen en el módulo de subproceso y ejecute /bin/sh.


# Prevención
- Se debe implementar verificaciones de integridad tales como firmas digitales en cualquier objeto serializado con el fin de detectar modificaciones no autorizadas.
- Durante la deserialización es recomendable verificar el tipo de dato ya que por lo general se espera un tipo de dato o clase concreta después de la deserialización.
- El código que realiza la deserialización se debe ejecutar en un entorno conlos mínimos privilegios posibles.
- Se debe registrar cualquier falla en la deserialización y generar alertas de seguridad que indiquen un posible ataque.
- Se debe restringir y monitorear las conexiones (I/O) de red en los servidores o contenedores que utilizan funciones de deserialización.
