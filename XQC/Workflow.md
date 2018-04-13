# PolyEvent Workflow and dependencies

## Start

L'utilisateur saisi la commande ***register id password*** dans la cli (s'il n'est pas déjà enregistré) :
1. Appel à

# PolyEvent Framework Facilitation...

## JSF

### State machine

### Fichiers nécessaires et arborescence

## Persistence

### Annotations

### Fichiers nécessaires et arborescence

<p align="center">
  <img src="https://raw.githubusercontent.com/Shanoi/bug-free-disco/master/XQC/Imgs/BD.png"/>
</p>

#### Persistence

Le ficher **persistence.xml** contient les définitions des classes qui seront persistentes.

```xml
<persistence-unit name="tcf_persistence_unit" transaction-type="JTA">

        <jta-data-source>PolyEventDataSource</jta-data-source>

        <class>fr.unice.polytech.isa.teamk.entities.Event</class>
        <class>fr.unice.polytech.isa.teamk.entities.Organizer</class>
        <class>fr.unice.polytech.isa.teamk.entities.Material</class>
        <class>fr.unice.polytech.isa.teamk.entities.Room</class>
        <class>fr.unice.polytech.isa.teamk.entities.Provider</class>
        <class>fr.unice.polytech.isa.teamk.entities.Quote</class>

        <exclude-unlisted-classes>true</exclude-unlisted-classes>

        <properties>
            <property name="openjpa.jdbc.SynchronizeMappings" value="buildSchema(ForeignKeys=true)"/>
            <property name="openjpa.RuntimeUnenhancedClasses" value="unsupported" />
        </properties>

</persistence-unit>
```

#### Resources

Le fichier **resources.xml** contient la définition pour se connecter à la base de données.

```xml
<resources>
    <Resource id="production" type="DataSource">
        JdbcDriver  org.hsqldb.jdbcDriver
        JdbcUrl     jdbc:hsqldb:file:proddb
        UserName    sa
        Password
        LogSql      true
        JtaManaged  true
    </Resource>
</resources>
```

### Utilisation


## J2EE & Tomee

Quand on souhaite lancer tomee, il faut le faire depuis un des modules du projet. Ici, c'est depuis le module **event** que devra être lancé la commande `mvn tomee:run`.
En effet, pour que un container (server ?) tomee puisse être déployé et que la commande s'effectue correctement, il faut qu'elle soit lancée depuis un module dont le **pom.xml** spécifie que le type de packaging de ce dernier est **war** et non pas jar.

### Problème de compilation

On peut très vite faire face à des problèmes de compilation, car si d'autres modules dépendent du module dont le packaging est spécifié en war, alors il y aura un problème car il leur faudra un **jar** pour pouvoir compiler... or nous n'avons qu'un **war**. Le trick est donc de spécifier dans le pom.xml du module de déploiement que l'on veut aussi compilé ce dernier en **jar**. C'est facilement faisable grâce à un plugin maven que l'on ajoute dans la balise pom.xml, en voici le code :

```
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

## Utilisation des WSDL

Que faire de ces wsdl ? Pour faire propre, on peut les mettre dans le dossier resources du projet dans lequel ils doivent être utilisés. Rappelons qu'il y a précisément 1 wsdl par service WS exposés.
