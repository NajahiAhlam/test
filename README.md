You\u2019re seeing this error **because you\u2019re trying to mock a generic method with raw types**, and Mockito cannot infer the correct generic signature for `Join<X, Y>`.

This is **NOT caused by your Spring Boot specification code** \u2014 that part is fine.
The problem is **always in the TEST**, never in the service method.

---

# \u2705 **Why the Test Fails**

`root.join("projet")` returns:

```java
Join<ArticleHorsBordereau, Projet>
```

But your mock was declared like:

```java
when(root.join("projet")).thenReturn(projetJoin);
```

Mockito sees:

* Method: `join(String attributeName)`
* Return type: **Join<Object, Object>** (erased)
* Your mock: **Join<ArticleHorsBordereau, Projet>**

\u274c They don\u2019t match \u2192 Mockito says:

> Cannot resolve method 'thenReturn(Join<X, Y>)'

This is a common problem with **JPA Criteria mocking**.

---

# \u2705 **Solution: Cast the Return Type Manually**

You must cast the mocked return type, so Mockito \u201caccepts\u201d it.

### \u2714 FIX:

```java
when(root.join(eq("projet"), any(JoinType.class)))
        .thenReturn((Join) projetJoin);

when(root.join(eq("article"), any(JoinType.class)))
        .thenReturn((Join) articleJoin);
```

If your code uses `root.join("projet")` without joinType:

```java
when(root.join(eq("projet")))
        .thenReturn((Join) projetJoin);
```

This cast is REQUIRED because of Java type erasure.

---

# \U0001f50d **Full Example of How to Mock JPA Criteria API Correctly**

```java
CriteriaBuilder cb = mock(CriteriaBuilder.class);
CriteriaQuery cq = mock(CriteriaQuery.class);
Root<ArticleHorsBordereau> root = mock(Root.class);

Join<ArticleHorsBordereau, Projet> projetJoin = mock(Join.class);

// FIX: cast needed
when(root.join(eq("projet"))).thenReturn((Join) projetJoin);

when(projetJoin.get("id")).thenReturn(mock(Path.class));
when(root.get(anyString())).thenReturn(mock(Path.class));

Predicate predicate = mock(Predicate.class);
when(cb.like(any(Expression.class), anyString())).thenReturn(predicate);
when(cb.equal(any(Expression.class), any())).thenReturn(predicate);
when(cb.and(any())).thenReturn(predicate);
```

---

# \u26a0 IMPORTANT

For testing specifications **you should NEVER mock Criteria manually**.

\U0001f449 The correct way is to test using **DataJpaTest + H2** and verify results.

Mocking Criteria API is extremely fragile.

---

# \u2705 Recommended Testing Strategy

Use:

```java
@DataJpaTest
```

Insert test data in H2:

```java
ArticleHorsBordereau a1 = repo.save(...)
ArticleHorsBordereau a2 = repo.save(...)
```

Then call your method and assert the results \u2014 no mocks needed.

---

# If you want, I can rewrite the full test for:

\u2714 `getAllArticleHorsBordereau()`
\u2714 including H2 dataset
\u2714 full assertions
\u2714 no Criteria mocking

Just tell me **\u201cgenerate test for this method\u201d** and I\u2019ll generate a clean working one.
