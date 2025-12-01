You're right \u2014 **your test does not actually cover or verify the logic inside the `Specification`**, because using:

```java
when(projetPriceArticleRepository.findAll((Specification<ProjetPriceArticle>) any(), any(Pageable.class)))
    .thenReturn(page);
```

means:

\u27a1\ufe0f You are **not testing the predicates**, nor whether `cb.equal`, `cb.like`, joins, etc. were generated properly.
\u27a1\ufe0f The `Specification` lambda *is never executed by your test*.
\u27a1\ufe0f So filters (`projetId`, `code`, `fournisseurId`, etc.) are **not being tested at all**.

---

# \u2705 Why your predicates aren't covered

### Because you are mocking `findAll(specification, pageable)` with `any(Specification.class)`

Mocking with `any()` makes Mockito **skip** executing the real JPA specification logic and simply return your mocked `Page`.

So none of these lines ever run:

```java
switch (key) {
  case "projetId": predicates.add( ... );
  case "code": predicates.add( ... );
  ...
}
```

---

# \u2705 How to properly test Specifications

There are **two** correct approaches:

---

# \u2714 **Option 1 (Recommended): Use ArgumentCaptor to verify predicates were built**

You don\u2019t let the JPA repo execute anything.
Instead, you inspect the **Specification** passed into your repository.

### Example Fix:

```java
@Captor
ArgumentCaptor<Specification<ProjetPriceArticle>> specCaptor;

@Test
void testProjetIdPredicateIsCreated() {

    Map<String, String> params = Map.of("projetId", "123");

    service.getAllProjetPriceArticle(0, 10, "ASC", "id", params);

    verify(projetPriceArticleRepository).findAll(specCaptor.capture(), any(Pageable.class));

    Specification<ProjetPriceArticle> specification = specCaptor.getValue();

    // Create mock Root, CriteriaBuilder, CriteriaQuery
    Root<ProjetPriceArticle> root = mock(Root.class);
    CriteriaBuilder cb = mock(CriteriaBuilder.class);
    CriteriaQuery<?> query = mock(CriteriaQuery.class);

    // Calling specification.toPredicate MUST execute your switch-case
    specification.toPredicate(root, query, cb);

    // Verify that joins and cb.equal were called
    verify(root).join("articleProjet");
    verify(cb).equal(any(), eq(123L));
}
```

### This **forces your predicates to run**, and you verify that:

* the join is performed
* the predicate `cb.equal(..., 123)` is created

\u2714 **NOW your code is covered.**

---

# \u2714 **Option 2: Use a real in-memory DB (H2) and do an integration test**

If you want to test the whole query end-to-end:

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
class ProjetPriceArticleRepositoryTest {

    @Autowired
    ProjetPriceArticleRepository repository;

    @Test
    void testFilterByProjetId() {
        // insert real Projet, Article, etc.
        Projet projet = new Projet();
        projet.setId(1L);
        em.persist(projet); 

        // ...

        Map<String, String> params = Map.of("projetId", "1");
        Specification<ProjetPriceArticle> spec = ... // call your method to build it

        Page result = repository.findAll(spec, PageRequest.of(0, 10));

        assertEquals(1, result.getTotalElements());
    }
}
```

\u2714 This verifies Spring Data JPA + real SQL + your joins + predicates.

---

# \u274c What **NOT** to do

Do NOT test Specifications like this:

```java
when(repo.findAll(any(), any())).thenReturn(page)
```

Because **none of your filtering logic will execute**.

---

# \U0001f4cc Summary of Recommended Fix

\u27a1\ufe0f **Use ArgumentCaptor to capture the Specification**
\u27a1\ufe0f **Invoke `toPredicate()` manually**
\u27a1\ufe0f **Verify joins + predicates via Mockito**

This method gives **full coverage** over:

* `cb.like`
* `cb.equal`
* joins (`root.join(...)`)
* switching between cases

---

If you want, I can generate a **complete corrected test class** with all predicate verifications.
