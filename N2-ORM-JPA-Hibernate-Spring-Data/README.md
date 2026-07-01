# TP2 — ORM JPA Hibernate Spring Data

## Concept

Avec **JPA + Hibernate**, plus besoin d'écrire du SQL à la main. On travaille avec des objets Java, et Hibernate traduit ça en SQL automatiquement.

C'est le principe de l'**ORM** (Object-Relational Mapping) : faire le lien entre objets Java et tables SQL.

---

## Partie 1 — Gestion des Produits

Entité `Product` (id, name, price, quantity), mappée avec `@Entity` :

```java
@Entity
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;
    private int quantity;
}
```

**Repository** avec Spring Data — juste une interface, pas de code :

```java
public interface ProductRepository extends JpaRepository<Product, Long> {
    List<Product> findByNameContains(String keyword);
    List<Product> findByPriceLessThan(double price);
}
```

Opérations testées : ajout, consultation (tous / par id), recherche par mot-clé, mise à jour, suppression.

---

## Partie 2 — Système Hospitalier

Modélisation d'une clinique avec plusieurs entités liées :

```text
Patient ──────────────────────────────────────────┐
  │ (un patient peut avoir plusieurs rendez-vous)  │
  │                                                │
  ▼                                                │
RendezVous ◄─── Medecin                           │
  │                                               │
  ▼                                               │
Consultation                                      │
                                                  │
AppUser ◄────────────────► AppRole               │
  (plusieurs users peuvent avoir plusieurs rôles) │
```

| Entité | Contenu | Relations |
|---|---|---|
| **Patient** | nom, dateNaissance, malade, score | `@OneToMany` → RendezVous |
| **Medecin** | nom, email, spécialité | `@OneToMany` → RendezVous |
| **RendezVous** | date, statut (PENDING/CANCELLED/DONE) | `@ManyToOne` Patient & Medecin, `@OneToOne` Consultation |
| **Consultation** | date, rapport médical | `@OneToOne` → RendezVous |
| **AppUser / AppRole** | utilisateurs et rôles | `@ManyToMany` |

---

## Migration H2 → MySQL

H2 = base en mémoire (pratique pour les tests). Pour passer à MySQL, modifier `application.properties` :

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/tp2db?createDatabaseIfNotExist=true
spring.datasource.username=root
spring.datasource.password=
```

---

## Structure du projet

```text
tp2-orm/
├── entities/
│   ├── Product.java        ← Entité produit
│   ├── Patient.java        ← Entité patient
│   ├── Medecin.java        ← Entité médecin
│   ├── RendezVous.java     ← Rendez-vous (relie patient et médecin)
│   ├── Consultation.java   ← Compte rendu médical
│   ├── AppUser.java        ← Utilisateur
│   ├── AppRole.java        ← Rôle
│   └── StatusRDV.java      ← Enum : PENDING, CANCELLED, DONE
├── repositories/
│   ├── ProductRepository.java
│   ├── PatientRepository.java
│   ├── MedecinRepository.java
│   ├── RendezVousRepository.java
│   ├── ConsultationRepository.java
│   ├── AppUserRepository.java
│   └── AppRoleRepository.java
└── Tp2OrmApplication.java  ← Point d'entrée + données de test
```

---

## Comment lancer

```bash
cd tp2-orm
mvn spring-boot:run
```

Console H2 disponible sur **http://localhost:8080/h2-console**
(JDBC URL : `jdbc:h2:mem:tp2db`, username : `sa`, password : vide)

---

## Ce qu'on a appris

- Transformer une classe Java en table SQL avec `@Entity`
- Les relations `@OneToMany`, `@ManyToOne`, `@OneToOne`, `@ManyToMany`
- Utiliser **Spring Data JPA** sans écrire de SQL
- Différence entre H2 (développement) et MySQL (production)
- Génération automatique du schéma par Hibernate (`ddl-auto=create-drop`)