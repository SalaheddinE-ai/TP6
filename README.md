# TP 6 — Spring Boot & Thymeleaf

##  Objectif

Ce TP a pour objectif de développer une application web complète en utilisant **Spring Boot** côté backend et **Thymeleaf** comme moteur de templates pour générer des صفحات HTML dynamiques.
L’application permet de gérer une liste d’étudiants avec des opérations CRUD (Create, Read, Update, Delete).

---

##  Étape 1 : Génération du projet

Créer un projet Spring Boot via Spring Initializr avec les dépendances suivantes :

* Spring Web
* Spring Data JPA
* Thymeleaf
* MySQL Driver (ou PostgreSQL)
* Spring Boot DevTools

Structure du projet :

```text
com.example.student_management
│
├── controller
├── service
├── repository
├── entity
└── templates
```

---

##  Étape 2 : Configuration de la base de données

Dans `application.properties` :

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/studentdb
spring.datasource.username=root
spring.datasource.password=

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

Créer la base :

```sql
CREATE DATABASE studentdb;
```

---

##  Étape 3 : Création de l’entité

```java
@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private String nom;
    private String prenom;

    private LocalDate dateNaissance;

    // Getters et Setters
}
```

---

##  Étape 4 : Repository

```java
@Repository
public interface StudentRepository extends JpaRepository<Student, Integer> {
}
```

---

##  Étape 5 : Service

```java
@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository) {
        this.repository = repository;
    }

    public List<Student> findAll() {
        return repository.findAll();
    }

    public Student save(Student student) {
        return repository.save(student);
    }

    public void delete(int id) {
        repository.deleteById(id);
    }

    public Student findById(int id) {
        return repository.findById(id).orElse(null);
    }
}
```

---

##  Étape 6 : Contrôleur MVC (Thymeleaf)

Contrairement au REST, ici on retourne des **pages HTML** :

```java
@Controller
@RequestMapping("/students")
public class StudentController {

    private final StudentService service;

    public StudentController(StudentService service) {
        this.service = service;
    }

    @GetMapping("/all")
    public String listStudents(Model model) {
        model.addAttribute("students", service.findAll());
        return "students";
    }

    @GetMapping("/new")
    public String showForm(Model model) {
        model.addAttribute("student", new Student());
        return "form";
    }

    @PostMapping("/save")
    public String save(Student student) {
        service.save(student);
        return "redirect:/students/all";
    }

    @GetMapping("/delete/{id}")
    public String delete(@PathVariable int id) {
        service.delete(id);
        return "redirect:/students/all";
    }
}
```

---

##  Étape 7 : Templates Thymeleaf

###  1. students.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Students</title>
</head>
<body>

<h2>Liste des étudiants</h2>

<a href="/students/new">Ajouter étudiant</a>

<table border="1">
    <tr>
        <th>ID</th>
        <th>Nom</th>
        <th>Prénom</th>
        <th>Date</th>
        <th>Actions</th>
    </tr>

    <tr th:each="s : ${students}">
        <td th:text="${s.id}"></td>
        <td th:text="${s.nom}"></td>
        <td th:text="${s.prenom}"></td>
        <td th:text="${s.dateNaissance}"></td>
        <td>
            <a th:href="@{/students/delete/{id}(id=${s.id})}">Supprimer</a>
        </td>
    </tr>
</table>

</body>
</html>
```

---

###  2. form.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Form</title>
</head>
<body>

<h2>Ajouter un étudiant</h2>

<form th:action="@{/students/save}" th:object="${student}" method="post">

    Nom: <input type="text" th:field="*{nom}" /><br>
    Prénom: <input type="text" th:field="*{prenom}" /><br>
    Date: <input type="date" th:field="*{dateNaissance}" /><br>

    <button type="submit">Save</button>

</form>

</body>
</html>
```

---

##  Étape 8 : Lancer l’application

```bash
mvn spring-boot:run
```

Accès :

```text
http://localhost:8080/students/all
```

---

##  Fonctionnement

* `/students/all` → affiche la liste
* `/students/new` → formulaire d’ajout
* `/students/save` → enregistre
* `/students/delete/{id}` → supprime

---

##  Différence REST vs Thymeleaf

| REST API        | Thymeleaf        |
| --------------- | ---------------- |
| JSON            | HTML             |
| Postman         | Navigateur       |
| @RestController | @Controller      |
| Frontend séparé | Frontend intégré |

---

##  Conclusion

Ce TP permet de comprendre :

* L’intégration de Thymeleaf avec Spring Boot
* Le pattern MVC (Model - View - Controller)
* La création d’interfaces web dynamiques
* Les opérations CRUD complètes

 Résultat : une application web fonctionnelle avec interface utilisateur complète.

---

##  Améliorations possibles

* Ajouter Bootstrap pour le design
* Ajouter validation des formulaires
* Ajouter pagination
* Ajouter édition des étudiants

---
