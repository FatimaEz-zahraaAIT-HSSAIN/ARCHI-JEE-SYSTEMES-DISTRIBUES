# TP1 — Injection des Dépendances

## Concept

L'injection de dépendances = donner ses dépendances à un objet de l'extérieur (couplage faible), plutôt que de les créer lui-même (couplage fort). Ex : une voiture reçoit son moteur au lieu de le fabriquer elle-même.

Ici, `MetierImpl` dépend de l'interface `IDao`, pas de `DaoImpl`.

---

## Partie 1 — 4 façons d'injecter (Spring)

| Méthode | Description |
|---|---|
| **Pres1.java** | Instanciation statique — objets créés à la main dans le code |
| **Pres2.java** | Instanciation dynamique — nom de classe lu depuis `config.txt`, via réflexion |
| **PresSpringXML.java** | Spring lit `applicationContext.xml` et crée les beans |
| **PresSpringAnnotations.java** | `@Component` + `@Autowired`, Spring fait tout automatiquement |

```xml
<bean id="dao" class="ma.enset.dao.DaoImpl"/>
<bean id="metier" class="ma.enset.metier.MetierImpl">
    <property name="dao" ref="dao"/>
</bean>
```

---

## Partie 2 — Mini Framework IoC (fait maison)

Reproduction du fonctionnement interne de Spring :
- Lecture d'un XML pour créer les beans (JAXB)
- Scan de package pour détecter les classes `@Component`
- Injection via **constructeur**, **setter** ou **attribut** directement

```java
ApplicationContext context = new XMLApplicationContext("beans.xml");
// ou : new AnnotationApplicationContext("ma.enset.app")
IMetier metier = (IMetier) context.getBean("metier");
System.out.println(metier.calcul());
```

---

## Structure du projet

```text
tp1-injection/               ← Partie 1 (Spring)
├── dao/ (IDao, DaoImpl, DaoImplV2)
├── metier/ (IMetier, MetierImpl)
├── presentation/ (Pres1, Pres2, PresSpringXML, PresSpringAnnotations)
└── resources/applicationContext.xml
tp1-mini-framework/          ← Partie 2 (framework maison)
├── framework/annotations/ (@Component, @Autowired)
├── framework/context/ (XMLApplicationContext, AnnotationApplicationContext)
└── app/ (application de test)
```

---

## Lancer les exemples

Exécuter directement la classe correspondante, ex : `ma.enset.presentation.Pres1`, `ma.enset.app.presentation.PresXML`, etc.

---

## Ce qu'on a appris

- **Couplage faible** : dépendre des interfaces, pas des implémentations
- **Inversion de contrôle (IoC)** : le framework crée les objets, pas nous
- **Réflexion Java** : créer des objets depuis un nom de classe (`Class.forName()`)
- Le fonctionnement interne de **Spring**