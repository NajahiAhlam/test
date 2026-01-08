Got it \U0001f44d
You want a **clean Spring Boot backend logic** to:

* Store a `Dossier`
* Call an **external API via Feign** for events (nantissement, mainlevée, etc.)
* **Persist the last event** returned by the external system
* Be able to **display the \u201cDernier Événement\u201d** in the frontend (Nebular alert)

I\u2019ll give you a **simple, robust, and scalable design**.

---

## 1\ufe0f\u20e3 What you should store in DB (important)

You already have almost everything \U0001f44c
These fields are perfect for the \u201clast event\u201d:

```java
private String idRequete;
private String dernierCodeRetour;
private String dernierMessageRetour;

@Enumerated(EnumType.STRING)
private DossierStatus status;
```

\U0001f449 **Rule**:
Each time an external event is called, you **overwrite** these 3 fields with the latest response.

---

## 2\ufe0f\u20e3 Define your business statuses clearly

Create a clear enum for dossier lifecycle:

```java
public enum DossierStatus {
    BROUILLON,                  // Saved but not sent
    NANTISSEMENT_ENCOURS,
    NANTISSEMENT_ACCEPTE,
    NANTISSEMENT_REFUSE,
    MAINLEVEE_ENCOURS,
    MAINLEVEE_ACCEPTEE,
    ERREUR
}
```

This lets you:

* Control UI buttons
* Avoid invalid transitions
* Be future-proof

---

## 3\ufe0f\u20e3 DTO for external API response

```java
@Data
public class NarsaResponseDto {
    private String idRequete;
    private String codeRetour;
    private String messageRetour;
}
```

---

## 4\ufe0f\u20e3 Feign Client (external API)

```java
@FeignClient(name = "narsaClient", url = "${narsa.api.url}")
public interface NarsaFeignClient {

    @PostMapping("/nantissement")
    ResponseEntity<NarsaResponseDto> declarationNantissement(
            @RequestBody DossierDto dossierDto
    );

    // same idea for mainlevée, modification, etc.
}
```

---

## 5\ufe0f\u20e3 Centralize the response handling (VERY IMPORTANT)

Create **one method** that maps external response \u2192 Dossier state.

```java
@Service
public class DossierEventHandler {

    public void applyExternalResponse(Dossier dossier,
                                      NarsaResponseDto response,
                                      HttpStatus httpStatus) {

        dossier.setIdRequete(response.getIdRequete());
        dossier.setDernierCodeRetour(response.getCodeRetour());
        dossier.setDernierMessageRetour(response.getMessageRetour());

        if (httpStatus.is2xxSuccessful()) {
            switch (response.getCodeRetour()) {
                case "NANTI01":
                    dossier.setStatus(DossierStatus.NANTISSEMENT_ACCEPTE);
                    dossier.setDateNantissement(LocalDateTime.now());
                    break;
                default:
                    dossier.setStatus(DossierStatus.ERREUR);
            }
        } else {
            dossier.setStatus(DossierStatus.NANTISSEMENT_REFUSE);
        }
    }
}
```

\u2705 This avoids **if/else chaos** in controllers
\u2705 Reusable for other events

---

## 6\ufe0f\u20e3 Service layer (main logic)

```java
@Service
@RequiredArgsConstructor
@Transactional
public class NantissementService {

    private final DossierRepository dossierRepository;
    private final NarsaFeignClient narsaFeignClient;
    private final DossierEventHandler eventHandler;

    public NarsaResponseDto declarationNantissement(Long dossierId) {

        Dossier dossier = dossierRepository.findById(dossierId)
                .orElseThrow(() -> new EntityNotFoundException("Dossier not found"));

        dossier.setStatus(DossierStatus.NANTISSEMENT_ENCOURS);

        ResponseEntity<NarsaResponseDto> response =
                narsaFeignClient.declarationNantissement(mapToDto(dossier));

        eventHandler.applyExternalResponse(
                dossier,
                response.getBody(),
                response.getStatusCode()
        );

        dossierRepository.save(dossier);

        return response.getBody();
    }
}
```

---

## 7\ufe0f\u20e3 Controller (clean & simple)

```java
@PostMapping("/dossiers/{id}/nantissement")
public ResponseEntity<NarsaResponseDto> declarationNantissement(
        @PathVariable Long id) {

    NarsaResponseDto response = nantissementService.declarationNantissement(id);

    if ("NANTI01".equals(response.getCodeRetour())) {
        return ResponseEntity.status(HttpStatus.CREATED).body(response);
    }

    return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(response);
}
```

---

## 8\ufe0f\u20e3 What the frontend consumes (perfect for your nb-alert)

Your entity already returns exactly what you need \U0001f447

```json
{
  "idRequete": "bc1c35aa-8c39-400f-85b3-75e392fa3f9b",
  "dernierCodeRetour": "NANTI01",
  "dernierMessageRetour": "Demande de nantissement créée",
  "status": "NANTISSEMENT_ACCEPTE"
}
```

Angular HTML stays **pure display logic**:

```html
<nb-alert status="success" *ngIf="dossier">
  <div class="alert-header">
    <h4><strong>Dernier Événement :</strong> Demande de nantissement</h4>
  </div>

  <div class="alert-body">
    <p>
      <strong>{{ dossier.dernierCodeRetour }} :</strong>
      {{ dossier.dernierMessageRetour }}
    </p>
  </div>

  <div class="alert-footer">
    <p>
      <strong>idRequete :</strong> {{ dossier.idRequete }}
    </p>
  </div>
</nb-alert>
```

---

## 9\ufe0f\u20e3 Bonus (best practice)

If later you want **history**, not just the last event:

* Create `DossierEvent` entity
* Store each response
* Keep `dernier*` as a **cache**

---

### \u2705 Summary

\u2714 One Dossier
\u2714 Multiple external events
\u2714 Always store **last event**
\u2714 Clean Spring Boot architecture
\u2714 Frontend-ready response

If you want, next I can:

* Design **event history table**
* Secure transitions (state machine)
* Improve error mapping per NANTI code
