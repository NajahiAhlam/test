Très bien \U0001f44d
Dans **`create`**, tu **ne dois PAS retourner l\u2019entité ni le DTO d\u2019entrée**, mais **un DTO de réponse conforme exactement aux paramètres de sortie NARSA**.

\U0001f449 **La réponse doit toujours contenir :**

* `idRequete` (reçu dans la requête)
* `codeRetour` (code métier + HTTP)
* `messageRetour` (message explicatif)

---

## \u2705 1. DTO de réponse (OBLIGATOIRE)

### `NantissementResponseDTO`

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class NantissementResponseDTO {

    private String idRequete;      // même valeur que la requête
    private String codeRetour;     // 201, 400, 406, 500...
    private String messageRetour;  // message métier
}
```

---

## \u2705 2. Enum pour les codes métier (RECOMMANDÉ)

\U0001f449 Pour éviter le **hardcoding** partout.

```java
public enum NantissementCodeRetour {

    NANTI01("201", "NANTI01: Demande de nantissement créée"),
    NANTI02("400", "NANTI02: Erreur par défaut pour manque d'information ou incohérence"),
    NANTI03("406", "NANTI03: Véhicule déjà soumis par un autre organisme"),
    NANTI04("406", "NANTI04: Demande de nantissement déjà soumise"),
    NANTI05("406", "NANTI05: Incohérence châssis et matricule"),
    NANTI06("406", "NANTI06: Nantissement déjà déclaré"),
    NANTI07("500", "NANTI07: Erreur interne");

    private final String codeHttp;
    private final String message;

    NantissementCodeRetour(String codeHttp, String message) {
        this.codeHttp = codeHttp;
        this.message = message;
    }

    public String getCodeHttp() {
        return codeHttp;
    }

    public String getMessage() {
        return message;
    }
}
```

---

## \u2705 3. Service : méthode `create`

\U0001f449 **Le service retourne `NantissementResponseDTO`**, pas l\u2019entité.

```java
@Service
@RequiredArgsConstructor
public class DeclarationNantissementService {

    private final DeclarationNantissementRepository repository;

    @Transactional
    public NantissementResponseDTO create(DeclarationNantissementDTO dto) {

        try {
            // \U0001f534 Exemple de validations métier
            if (dto.getIdRequete() == null || dto.getNumChassis() == null) {
                return buildResponse(dto.getIdRequete(), NantissementCodeRetour.NANTI02);
            }

            // \U0001f534 Exemple : nantissement déjà existant
            if (repository.existsByNumChassis(dto.getNumChassis())) {
                return buildResponse(dto.getIdRequete(), NantissementCodeRetour.NANTI06);
            }

            // \u2705 Mapping DTO \u2192 Entity
            DeclarationNantissement entity = mapToEntity(dto);
            repository.save(entity);

            // \u2705 Succès
            return buildResponse(dto.getIdRequete(), NantissementCodeRetour.NANTI01);

        } catch (Exception e) {
            return buildResponse(dto.getIdRequete(), NantissementCodeRetour.NANTI07);
        }
    }

    private NantissementResponseDTO buildResponse(String idRequete, NantissementCodeRetour code) {
        return new NantissementResponseDTO(
                idRequete,
                code.getCodeHttp(),
                code.getMessage()
        );
    }
}
```

---

## \u2705 4. Controller : respecter HTTP + payload métier

\U0001f449 **HTTP status = codeRetour**

```java
@RestController
@RequestMapping("/sfi/nantissements")
@RequiredArgsConstructor
public class DeclarationNantissementController {

    private final DeclarationNantissementService service;

    @PostMapping("/creer")
    public ResponseEntity<NantissementResponseDTO> create(
            @RequestBody DeclarationNantissementDTO dto) {

        NantissementResponseDTO response = service.create(dto);

        return ResponseEntity
                .status(Integer.parseInt(response.getCodeRetour()))
                .body(response);
    }
}
```

---

## \u2705 5. Exemple de réponse réelle

### \u2714 Succès

```json
{
  "idRequete": "bc1c35aa-8c39-400f-85b3-75e392fa3f9b",
  "codeRetour": "201",
  "messageRetour": "NANTI01: Demande de nantissement créée"
}
```

### \u274c Nantissement déjà déclaré

```json
{
  "idRequete": "bc1c35aa-8c39-400f-85b3-75e392fa3f9b",
  "codeRetour": "406",
  "messageRetour": "NANTI06: Nantissement déjà déclaré"
}
```

---

## \u2705 Conclusion (important)

\u2714 **Tu retournes TOUJOURS :**

* `idRequete`
* `codeRetour`
* `messageRetour`

\u274c **Tu ne retournes jamais :**

* l\u2019entité
* le DTO d\u2019entrée
* des erreurs Spring par défaut

Si tu veux, je peux ensuite te fournir :

* \u2714 les **règles de validation exactes**
* \u2714 les **queries repository**
* \u2714 un **diagramme du flow métier**
* \u2714 une **gestion d\u2019erreurs centralisée (@ControllerAdvice)**
