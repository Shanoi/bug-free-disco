# PolyEvent Workflow and dependencies

## Parties

- [Persistence](https://github.com/Shanoi/bug-free-disco/tree/master/XQC/Parties/persistence.md)
- [JSF](https://github.com/Shanoi/bug-free-disco/tree/master/XQC/Parties/jsf.md)

## Start

L'utilisateur saisi la commande ***register id password*** dans la cli (s'il n'est pas déjà enregistré) :
1. Appel à

# PolyEvent Framework Facilitation...


## J2EE & Tomee

Quand on souhaite lancer tomee, il faut le faire depuis un des modules du projet. Ici, c'est depuis le module **event** que devra être lancé la commande `mvn tomee:run`.
En effet, pour que un container (server ?) tomee puisse être déployé et que la commande s'effectue correctement, il faut qu'elle soit lancée depuis un module dont le **pom.xml** spécifie que le type de packaging de ce dernier est **war** et non pas jar.

### Problème de compilation

On peut très vite faire face à des problèmes de compilation, car si d'autres modules dépendent du module dont le packaging est spécifié en war, alors il y aura un problème car il leur faudra un **jar** pour pouvoir compiler... or nous n'avons qu'un **war**. Le trick est donc de spécifier dans le pom.xml du module de déploiement que l'on veut aussi compilé ce dernier en **jar**. C'est facilement faisable grâce à un plugin maven que l'on ajoute dans la balise pom.xml, en voici le code :

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-war-plugin</artifactId>
    <version>3.2.0</version>
    <executions>
        <execution>
            <id>make-a-war</id>
            <phase>compile</phase>
            <goals>
                <goal>war</goal>
            </goals>
        </execution>
    </executions>
</plugin>
<plugin>
    <artifactId>maven-jar-plugin</artifactId>
    <configuration>
        <classifier>classes</classifier>
    </configuration>
    <executions>
        <execution>
            <id>package-jar</id>
            <phase>package</phase>
            <goals>
                <goal>jar</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

Normalement, ceci devrait permettre de résoudre les problèmes de compilation.

La partie intéressante a relever est qu'apparemment, tous les modules dont dépend le module sous lequel le serveur TomEE va être déployé semblent être pris en compte dans le *"parsing"* qu'effectue le serveur à son lancement. Aisni dans notre cas, les services du module organizer ainsi que ceux du module responsible seront *parsés* et leur wsdl générés.

### WSDL

Lançons maintenant *mvn tomee:run* sur le module event. Cela va générer des wsdl des différents services implémentés dans ce module. Les adresses pour atteindre ces modules sont spécifiées et construite de la manière suivante:
1. Tout d'abord on accède à l'ip + port spécifiés, soit localhost:8080 (http://localhost:8080)
2. Ensuite, on met le nom du module depuis lequel la commande a été lancée, soit event. Ce qui donne : localhost:8080/event (http://localhost:8080/event)
3. TomEE expose les web services détectés grâce aux annotations **@WebService** sous... **webservices**, on a donc localhost:8080/event/webservices (http://localhost:8080/event/webservices)
4. Enfin il faut rajouter le nom du web service qui porte le nom de l'interface de ce dernier, ou sinon c'est le nom donné dans un annotation EJB du type **@Stateless(name = "...")**. Sans oublier le **?wsdl** suffixant le nom du web service, on a au final localhost:8080/event/webservices/EventWS?wsdl (http://localhost:8080/event/webservices/EventWS?wsdl).

Voici les différentes adresses pour les wsdl :
+ [Event](http://localhost:8080/event/webservices/EventWS?wsdl)
+ [Responsible](http://localhost:8080/event/webservices/OrganizerWS?wsdl)
+ [Organizer](http://localhost:8080/event/webservices/ResponsibleWS?wsdl)

### Utilisation des WSDL

Que faire de ces wsdl ? Pour faire propre, on peut les mettre dans le dossier resources du projet dans lequel ils doivent être utilisés. Rappelons qu'il y a précisément 1 wsdl par service WS exposés. Ces wsdl contiennent tout ce qu'il faut pour générer les stubs côté client (Ici notre CLI). Pour générer les stubs on pourra se servir de l'IDE. Il faut simplement faire un clique droit sur les wsdl > WebServices > Generate Java Code From Wsdl. Une fenêtre de dialogue s'ouvre, il faut sélectionner comme *Web Service Platform* la valeur **Glassfish / JAX-RS ...**.

Une fois les stubs générés, la cli va reconnaître tout ce dont elle a besoin pour continuer.
