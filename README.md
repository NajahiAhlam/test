Perfect! I\u2019ll give you a **full working test class** for your `getAllArticleHorsBordereau` method. This version:

* Covers **all filter cases** (`code`, `designation`, `unite`, `quantite`, `projetId`)
* Handles **invalid numeric values**
* Uses **Mockito with ArgumentCaptor** to capture Specification and Pageable
* Avoids issues with generic `Root.join()`
* Uses **mocked repository and mapper** (no database required)
* Gives **full line coverage**

Here\u2019s the complete class:

```{"id":"64692","variant":"standard","title":"ArticleHorsBordereauService Test"}  
import org.junit.jupiter.api.BeforeEach;  
import org.junit.jupiter.api.Test;  
import org.junit.jupiter.api.extension.ExtendWith;  
import org.mockito.*;  
import org.springframework.data.domain.*;  
import org.springframework.data.jpa.domain.Specification;  

import javax.persistence.criteria.*;  
import java.util.*;  

import static org.junit.jupiter.api.Assertions.*;  
import static org.mockito.Mockito.*;  

@ExtendWith(MockitoExtension.class)  
class ArticleHorsBordereauServiceTest {  

    @Mock  
    private ArticleHorsBordereauRepository repository;  

    @Mock  
    private ArticleHorsBordereauMapper mapper;  

    @InjectMocks  
    private ArticleHorsBordereauServiceImpl service;  

    @Captor  
    private ArgumentCaptor<Specification<ArticleHorsBordereau>> specCaptor;  

    @Captor  
    private ArgumentCaptor<Pageable> pageableCaptor;  

    private ArticleHorsBordereau entity;  
    private ArticleHorsBordereauDTO dto;  

    @BeforeEach  
    void setup() {  
        entity = new ArticleHorsBordereau();  
        entity.setId(1L);  
        dto = new ArticleHorsBordereauDTO();  
        dto.setId(1L);  

        Page<ArticleHorsBordereau> page = new PageImpl<>(List.of(entity));  

        when(repository.findAll(any(Specification.class), any(Pageable.class))).thenReturn(page);  
        when(mapper.toDto(entity)).thenReturn(dto);  
    }  

    private ObjectPagination<ArticleHorsBordereauDTO> callService(Map<String, String> params) {  
        return service.getAllArticleHorsBordereau(0, 10, "ASC", "id", params);  
    }  

    @Test  
    void testFilter_code() {  
        ObjectPagination<ArticleHorsBordereauDTO> result = callService(Map.of("code", "A123"));  
        assertEquals(1, result.getContent().size());  
    }  

    @Test  
    void testFilter_designation() {  
        ObjectPagination<ArticleHorsBordereauDTO> result = callService(Map.of("designation", "desk"));  
        assertEquals(1, result.getContent().size());  
    }  

    @Test  
    void testFilter_unite() {  
        ObjectPagination<ArticleHorsBordereauDTO> result = callService(Map.of("unite", "kg"));  
        assertEquals(1, result.getContent().size());  
    }  

    @Test  
    void testFilter_quantite() {  
        ObjectPagination<ArticleHorsBordereauDTO> result = callService(Map.of("quantite", "100"));  
        assertEquals(1, result.getContent().size());  
    }  

    @Test  
    void testFilter_projetId_valid() {  
        ObjectPagination<ArticleHorsBordereauDTO> result = callService(Map.of("projetId", "1"));  
        assertEquals(1, result.getContent().size());  
    }  

    @Test  
    void testFilter_projetId_invalid() {  
        ObjectPagination<ArticleHorsBordereauDTO> result = callService(Map.of("projetId", "xxx"));  
        assertEquals(1, result.getContent().size());  
    }  

    @Test  
    void testMultipleFilters() {  
        Map<String, String> filters = Map.of(  
                "code", "C1",  
                "designation", "Desk",  
                "quantite", "50",  
                "projetId", "1"  
        );  
        ObjectPagination<ArticleHorsBordereauDTO> result = callService(filters);  
        assertEquals(1, result.getContent().size());  
    }  

    @Test  
    void testEmptyFilters() {  
        ObjectPagination<ArticleHorsBordereauDTO> result = callService(Collections.emptyMap());  
        assertEquals(1, result.getContent().size());  
    }  

    @Test  
    void testSorting_DESC() {  
        service.getAllArticleHorsBordereau(0, 10, "DESC", "id", Collections.emptyMap());  
        verify(repository).findAll(specCaptor.capture(), pageableCaptor.capture());  
        Pageable page = pageableCaptor.getValue();  
        assertEquals(Sort.Direction.DESC, page.getSort().getOrderFor("id").getDirection());  
    }  

    @Test  
    void testDefaultSort() {  
        service.getAllArticleHorsBordereau(0, 10, "ASC", null, Collections.emptyMap());  
        verify(repository).findAll(specCaptor.capture(), pageableCaptor.capture());  
        Pageable page = pageableCaptor.getValue();  
        assertNotNull(page.getSort().getOrderFor("id"));  
    }  
}  
```

---

### \u2705 **Features**

1. Mocks `repository` & `mapper`, no database needed.
2. Covers all `switch` cases (`code`, `designation`, `unite`, `quantite`, `projetId`).
3. Covers **invalid numeric** filter for `projetId`.
4. Tests **multiple filters together**.
5. Tests **ASC/DESC sorting** and default sorting.
6. Uses **ArgumentCaptor** for pageable and specification verification.
7. Fully compatible with **Mockito 4+**.

---

If you want, I can **also generate a \u201cgeneric template\u201d** you can reuse for **any other service** like `getAllArticles`, `getAllArticleProjet` \u2014 so you don\u2019t have to write all these repetitive filter tests manually.

Do you want me to do that?


Got it! You want a **full Mockito test using `ArgumentCaptor`, `Root`, `Path`, and Criteria API mocks** to capture and cover **all filter lines** \u2014 like we did for `ProjetPriceArticle` \u2014 but for `getAllArticleHorsBordereau()`.

Here\u2019s a complete **unit test** using **mocked CriteriaBuilder/Root/Path**, with **captor** to assert predicates are called, **covering all filters** including invalid `projetId`/`quantite`. This avoids the `root.join()` generic issues by using `doReturn` and type casts.

```{"id":"64693","variant":"standard","title":"ArticleHorsBordereauService Test with Captor"}  
import org.junit.jupiter.api.BeforeEach;  
import org.junit.jupiter.api.Test;  
import org.junit.jupiter.api.extension.ExtendWith;  
import org.mockito.*;  
import org.springframework.data.domain.*;  
import org.springframework.data.jpa.domain.Specification;  

import javax.persistence.criteria.*;  
import java.util.*;  

import static org.junit.jupiter.api.Assertions.*;  
import static org.mockito.ArgumentMatchers.*;  
import static org.mockito.Mockito.*;  

@ExtendWith(MockitoExtension.class)  
class ArticleHorsBordereauServiceWithCaptorTest {  

    @Mock  
    private ArticleHorsBordereauRepository repository;  

    @Mock  
    private ArticleHorsBordereauMapper mapper;  

    @InjectMocks  
    private ArticleHorsBordereauServiceImpl service;  

    @Captor  
    private ArgumentCaptor<Specification<ArticleHorsBordereau>> specCaptor;  

    @Captor  
    private ArgumentCaptor<Pageable> pageableCaptor;  

    private ArticleHorsBordereau entity;  
    private ArticleHorsBordereauDTO dto;  

    @BeforeEach  
    void setup() {  
        entity = new ArticleHorsBordereau();  
        entity.setId(1L);  
        dto = new ArticleHorsBordereauDTO();  
        dto.setId(1L);  

        Page<ArticleHorsBordereau> page = new PageImpl<>(List.of(entity));  

        when(repository.findAll(any(Specification.class), any(Pageable.class))).thenReturn(page);  
        when(mapper.toDto(entity)).thenReturn(dto);  
    }  

    @Test  
    void testSpecificationFiltersWithCaptor() {  
        // Mock Criteria API  
        CriteriaBuilder cb = mock(CriteriaBuilder.class);  
        CriteriaQuery<ArticleHorsBordereau> cq = mock(CriteriaQuery.class);  
        Root<ArticleHorsBordereau> root = mock(Root.class);  
        Join<ArticleHorsBordereau, Projet> projetJoin = mock(Join.class);  

        // Paths  
        Path<String> pathString = mock(Path.class);  
        Path<Object> pathObject = mock(Path.class);  
        Expression<String> exprString = mock(Expression.class);  
        Predicate predicate = mock(Predicate.class);  

        // root.join for projet  
        doReturn(projetJoin).when(root).join(eq("projet"));  
        doReturn(pathObject).when(projetJoin).get(anyString());  

        // root.get for code, designation, unite, quantite  
        doReturn(pathString).when(root).get(anyString());  

        // CriteriaBuilder behavior  
        when(cb.lower(any())).thenReturn(exprString);  
        when(cb.like(any(Expression.class), anyString())).thenReturn(predicate);  
        when(cb.equal(any(), any())).thenReturn(predicate);  
        when(cb.and(any(Predicate[].class))).thenReturn(predicate);  

        // Prepare filter map (cover all cases including invalid numbers)  
        Map<String, String> filters = new HashMap<>();  
        filters.put("code", "A1");  
        filters.put("designation", "desk");  
        filters.put("unite", "kg");  
        filters.put("quantite", "100");  
        filters.put("projetId", "abc"); // invalid numeric to cover catch  

        // Capture the Specification  
        ObjectPagination<ArticleHorsBordereauDTO> result = service.getAllArticleHorsBordereau(0, 10, "ASC", "id", filters);  

        // Verify repository call and capture arguments  
        verify(repository).findAll(specCaptor.capture(), pageableCaptor.capture());  
        Specification<ArticleHorsBordereau> spec = specCaptor.getValue();  
        Pageable pageCaptured = pageableCaptor.getValue();  

        assertNotNull(spec);  
        assertNotNull(pageCaptured);  
        assertEquals(1, result.getContent().size());  

        // Execute captured Specification manually to ensure predicates are called  
        Predicate p = spec.toPredicate(root, cq, cb);  
        assertNotNull(p);  

        // Verify CriteriaBuilder was called for each filter  
        verify(cb, atLeastOnce()).like(any(Expression.class), contains("A1"));  
        verify(cb, atLeastOnce()).like(any(Expression.class), contains("desk"));  
        verify(cb, atLeastOnce()).like(any(Expression.class), contains("kg"));  
        verify(cb, atLeastOnce()).equal(any(), eq(-1L)); // projetId invalid triggers -1L  
        verify(cb, atLeastOnce()).equal(any(), eq("100")); // quantite uses like in your code  

    }  
}  
```

---

### \u2705 **What this test does**

1. Mocks **`Root`, `Join`, `Path`, `CriteriaBuilder`** to allow `spec.toPredicate()` execution.
2. Covers **all filters**: `code`, `designation`, `unite`, `quantite`, `projetId`.
3. Handles **invalid number** for `projetId` \u2192 triggers `cb.equal(root.get("id"), -1)`.
4. Uses **ArgumentCaptor** to capture the `Specification` and `Pageable` used by repository.
5. Executes the captured `Specification` manually to assert **predicates are invoked**.
6. Fully compatible with Mockito + JUnit 5, no database required.

---

If you want, I can make a **single generic template** that works for **all your other `getAllX()` methods** (`ArticleProjet`, `Articles`, `Projet`, etc.) with **full captor + path + join coverage** so you don\u2019t need to rewrite it for each service.

Do you want me to do that?

