# Annotations

https://www.jmdoudoux.fr/java/dej/chap-ejb3.htm

## EJB

### Bean Types
+ *T* **Stateless***(name=className)* : pool d'instances qui sont utilisées au besoin (Un client pourra donc utiliser plusieurs instances différentes)
+ *T* **Stateful***(name=className)* : conserve l'état durant l'utilisation par le client (Un client utilisera toujours la même instance)
+ *T* **Singleton***(name=className)* :
+ *T* **Local** : permet un accès à l'EJB depuis un client dans la même JVM que celle de l'EBJ
+ *T* **Startup** :

### Resources
+ **EJB***(name="", beanInterface=Object.class, mappedName="", lookup=""| beanName="", description="")* :

### Embedded values
+ *T* **Embeddable** :

### EJB Callbacks
+ *M* **PostConstruct** : la méthode est invoquée après que l'instance soit créée et que les dépendances soient injectées

### Interceptors, Exceptions
+ *M* **AroundInvoke** : L'annotation @AroundInvoke permet de marquer une méthode qui sera exécutée lors de l'invocation des méthodes métiers d'un EJB. Cette annotation ne peut être utilisée qu'une seule fois dans une même classe d'un intercepteur ou d'un EJB. Il n'est pas possible d'annoter une méthode métier avec l'annotation @AroundInvoke.

## OTHERS

### javax.interceptors
+ *TM* **Interceptors** : L'annotation @javax.interceptor.Interceptors permet de définir les classes d'intercepteurs qui seront invoquées par le conteneur. Si plusieurs classes d'intercepteurs sont définies alors elles seront invoquées dans l'ordre de leur définition dans l'annotation.
Si l'annotation est utilisée sur la classe du bean alors les intercepteurs seront invoqués pour chaque méthode du bean. Si l'annotation est utilisée sur une méthode alors les intercepteurs seront invoqués uniquement pour la méthode.

### javax.jws
+ **WebService** : Service web développé dans sa classe dédié
+ **WebMethod** :
+ **WebParam** :

### javax.xml.ws
+ **WebFault** :

### javax.validation.constraints
+ **NotNull** : Obvious...

# LEGEND
+ **C** : *Constructor*
+ **F** : *Field*
+ **M** : *Method*
+ **T** : *Type*
