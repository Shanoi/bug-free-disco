# Persistence

## Annotations

### Entity
```Java
@Entity
```
Permet de définir une table.

### Embeddable
```Java
@Embeddable
```
Permet de définir un élément qui ne peut pas exister seul. Par exemple si un item fait partie d'un sac.

### Id et GeneratedValue
```Java
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
```
Permet de définir l'attribut ID dans la base de donnée. Il est obligatoire. Le `@GeneratedValue` permet de générer automatiquement la valeur.

### Temporal
```Java
@Temporal(TemporalType.DATE)
```
Permet de définir un attribut de type temps. Ici, il permet de définir une `Date`.

### ManyToOne
Depuis la classe Event.
```Java
@ManyToOne
private Organizer organizer;
```
Depuis la classe Organizer.
```Java
@OneToMany(mappedBy = "organizer")
private Set<Event> events = new HashSet<>();
```
Permet de définir la relation entre deux élément de la base de données.
Ne pas oublier de définir la stratégie lorsque l'on delete d'un côté ou de l'autre ([cascading](https://github.com/polytechnice-si/4A_ISA_TheCookieFactory/blob/develop/chapters/Persistence.md#cascading-operation-through-relations))

___

## Fichiers nécessaires et arborescence

<p align="center">
  <img src="https://raw.githubusercontent.com/Shanoi/bug-free-disco/master/XQC/Imgs/BD.png"/>
</p>

### Persistence

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

### Resources

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
-----

## Utilisation

### Constructor
Un constructeur vide est nécessaire.

#### Types

Dans la base de données, pour stocker des dates nous utilisons un java.sql.Timestamp. Ce type est compatible avec la base de données contrairement à LocalDateTime par exemple. En revanche, dès que l'on sort une date de la base de données, on la convertit en LocalDateTime qui est beaucoup plus maniable qu'un Timestamp.

#### Equal
La méthode `equal` doit reposer sur des business items. On doit pouvoir définir en quoi deux entités sont égales d'un point de vue business.
Lorsque l'on défini cette méthode, il faut faire attention à ne pas faire de références cycliques qui entraineraient une boucle infinie d'appels.

### HashCode
La fonction `hashcode` doit être correctement définie puisqu'elle sert à identifier les objets en mémoire. Mêmes remarques que pour `equal`.

### Testing
Comme on utilise Arquillian, on doit préciser la configuration dans le `arquilian.xml`.

```xml
<property name="properties">
  my-datasource = new://Resource?type=DataSource
  my-datasource.JdbcUrl = jdbc:hsqldb:mem:TCFDB;shutdown=true
  my-datasource.UserName = sa
  my-datasource.Password =
  my-datasource.JtaManaged = true
  my-datasource.LogSql = true
</property>
```
Le `mem` permet de définir la BD en mémoire (RAM).

Le `shutdown` éteint la BD après la dernière connexion.

Le `JtaManaged` supporte les transactions au niveau du container.

Le `LogSql` permet de logger les requêtes SQL générées par JPA.

Pour tester la persistence, un `EntityManager` est nécessaire.

**To be continued ...**
