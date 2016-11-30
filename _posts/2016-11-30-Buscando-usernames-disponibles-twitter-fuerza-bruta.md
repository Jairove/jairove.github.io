---
layout: post
title: Buscando usernames disponibles en Twitter por fuerza bruta
---

Cuando me registré en ***Twitter***, allá por 2009, la disponibilidad de nombres de usuario no era un problema. Al principio utilicé mi nombre y apellido, pero en algún momento decidí que era demasiado largo teniendo en cuenta la limitación de 140 caracteres, quise reducirlo a mi username habitual, pero era demasiado tarde, ya estaba registrado, y a pesar de que la cuenta se encuentra inactiva desde 2011 la política de ***Twitter*** no contempla reclamar nombres de usuario, a no ser que se trate de marcas comerciales. 

Al final el destino quiso que me quedase con otro username que no llevaba mi nombre, el cual he mantenido durante los últimos 5 años, pero ya no me gustaba, quería volver a tener uno más personal. La idea era clara, **debía ser breve**, preferiblemente mi nombre seguido de pocos caracteres, pero todos los que iba probando no estaban disponibles, entonces se me ocurrió probar por **fuerza bruta** todas las combinaciones posibles de mi nombre seguido de hasta dos caracteres. 

Para ello **programé un script en Python** que prueba todas las cadenas del estilo jairoX, donde X es una cadena de hasta longitud dos formada por letras minúsculas ASCII o el guión bajo. En Python sería el siguiente conjunto
{% highlight python %}
CHARACTERS = string.ascii_lowercase+'_'
{% endhighlight %}

Decidí mantener una longitud máxima de dos porque el número de combinaciones tiene un crecimiento exponencial de orden n, una simple longitud de 3 supondría 19683 variaciones posibles (27^3).

{% highlight python %}
	def next(string):
	    if len(string) <= 0:
	        string.append(indexToCharacter(0))
	    else:
	        string[0] = indexToCharacter((characterToIndex(string[0]) + 1) % CHARACTERS_LEN)
	        if CHARACTERS.index(string[0]) is 0:
	            return list(string[0]) + next(string[1:])
	    return string
{% endhighlight %}

La función encargada de generar las cadenas es **recursiva**, y la he reutilizado de otro proyecto, es bastante simple, recibe como parámetro una lista y se va incrementando el valor del primer elemento, como se le aplica módulo CHARACTERS_LEN, cuando se llegue al valor cuyo módulo sea 0 es que ya se han probado todas las combinaciones, y lo siguiente es incrementar la longitud de la cadena, volviendo a invocar la función recursivamente, pasando como argumento la cadena sin contener el carácter ya probado (por lo que será de longitud cero). Hay que tener en cuenta que por la forma en que Python trata las cadenas, string es realmente una lista, que puede convertirse fácilmente en cadena empleando ‘’.join(string). Ya sólo quedaría invocar a esta función dentro de un bucle, que termine cuando se hayan generado todas las combinaciones posibles, y en el que también se envíe una **petición HTTP a Twitter** con el username generado. Si el resultado de la petición es ***200 (OK)*** significa que el username ya está asignado, y si se obtiene un ***404 (Not found)*** es que el nombre está disponible. 

{% highlight python %}
while i<CHARACTERS_LEN*CHARACTERS_LEN:
        generated_name = next(generated_name)
        username = ''.join(generated_name)
        url = 'http://twitter.com/jairo'+username
        r = requests.get(url)

        if r.status_code == 404:
            print('AVAILABLE USERNAME:\t jairo'+ username)
{% endhighlight %}

Ya sólo queda ejecutarlo volcando la salida a un fichero y esperar, como no me apetecía probar las posibles consecuencias de enviar miles de peticiones a Twitter con mi IP, empleé una VPN (aunque al final parece que las protecciones contra ataques DOS no saltaron si es que existen).

Esta fue la salida:
{% highlight python %}
AVAILABLE USERNAME:	 jairoua
AVAILABLE USERNAME:	 jairoxa
AVAILABLE USERNAME:	 jairoxb
AVAILABLE USERNAME:	 jairowc
AVAILABLE USERNAME:	 jairoue
AVAILABLE USERNAME:	 jairoxe
AVAILABLE USERNAME:	 jairokf
AVAILABLE USERNAME:	 jairowf
AVAILABLE USERNAME:	 jairoih
AVAILABLE USERNAME:	 jairokh
AVAILABLE USERNAME:	 jairowh
AVAILABLE USERNAME:	 jairoyh
AVAILABLE USERNAME:	 jairoei
AVAILABLE USERNAME:	 jairoyi
AVAILABLE USERNAME:	 jairoyj
AVAILABLE USERNAME:	 jairofk
AVAILABLE USERNAME:	 jairoxk
AVAILABLE USERNAME:	 jairokm
AVAILABLE USERNAME:	 jairoxm
AVAILABLE USERNAME:	 jairozm
AVAILABLE USERNAME:	 jairoxn
AVAILABLE USERNAME:	 jairoip
AVAILABLE USERNAME:	 jairowp
AVAILABLE USERNAME:	 jairodq
AVAILABLE USERNAME:	 jairokq
AVAILABLE USERNAME:	 jairouq
AVAILABLE USERNAME:	 jairowq
AVAILABLE USERNAME:	 jairoxq
AVAILABLE USERNAME:	 jairoyq
AVAILABLE USERNAME:	 jairohr
AVAILABLE USERNAME:	 jairoqr
AVAILABLE USERNAME:	 jairodt
AVAILABLE USERNAME:	 jairoiu
AVAILABLE USERNAME:	 jairoev
AVAILABLE USERNAME:	 jairokv
AVAILABLE USERNAME:	 jairofw
AVAILABLE USERNAME:	 jairoiw
AVAILABLE USERNAME:	 jairokw
AVAILABLE USERNAME:	 jairomw
AVAILABLE USERNAME:	 jairoqw
AVAILABLE USERNAME:	 jairoyw
AVAILABLE USERNAME:	 jairozw
AVAILABLE USERNAME:	 jairolx
AVAILABLE USERNAME:	 jairopx
AVAILABLE USERNAME:	 jairody
AVAILABLE USERNAME:	 jairoey
AVAILABLE USERNAME:	 jairohy
AVAILABLE USERNAME:	 jairoiy
AVAILABLE USERNAME:	 jairoqy
AVAILABLE USERNAME:	 jairouy
AVAILABLE USERNAME:	 jairogz
AVAILABLE USERNAME:	 jairoiz
AVAILABLE USERNAME:	 jairowz
{% endhighlight %}

Tras revisarlos finalmente me quedé con **@jairoev**, que de alguna manera es parecido a mi ansiado **@jairove**.

Podrían hacerse muchas mejoras, como pasar como parámetro el nombre al que anexionar las cadenas generadas, dar soporte para cadenas de longitud variable (creando una función que calcule la potencia), o escribir en un fichero directamente sin tener que redirigir la salida estándar al ejecutar desde la terminal. Pero para el poco tiempo que le dediqué yo ya le he sacado buen provecho.

Puedes consultar el **código completo** [aquí](https://gist.github.com/Jairove/ffd85b8295af719adb9e40a07602d9e1) 


