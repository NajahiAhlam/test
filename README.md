form value:  
{risqueId: 1, contexte: 'zez', niveauIntrinseque: 'Faible  ', autreRisque: 'zez', niveauRisqueResiduel: 'Faible  ', …}
autreRisque
: 
"zez"
comment
: 
""
conditions
: 
Array(1)
0
: 
assigne
: 
"julien.blanc@example.com"
categorie
: 
"précondition"
commentCond
: 
"zeze"
dateEcheance
: 
Tue May 20 2025 00:00:00 GMT+0100 (UTC+01:00) {}
dateLancement
: 
""
detail
: 
"zdez"
etat
: 
""
id
: 
null
numberSemaineApresLancement
: 
""
[[Prototype]]
: 
Object
length
: 
1
[[Prototype]]
: 
Array(0)
contexte
: 
"zez"
niveauIntrinseque
: 
"Faible  "
niveauRisqueResiduel
: 
"Faible  "
risqueId
: 
1
zoneRisqueInstance
: 
Array(2)
0
: 
AnalyseRisqueInstance
: 
Array(1)
0
: 
analyseRisqueId
: 
1
facteur
: 
""
reponse
: 
""
[[Prototype]]
: 
Object
length
: 
1
[[Prototype]]
: 
Array(0)
name
: 
"test"
zoneRisqueId
: 
1
[[Prototype]]
: 
Object
1
: 
AnalyseRisqueInstance
: 
[{…}]
name
: 
"test 2"
zoneRisqueId
: 
2
[[Prototype]]
: 
Object
length
: 
2
[[Prototype]]
: 
Array(0)
[[Prototype]]
: 
Object
give me the backend how to create this objet give me create and the mapper i map like this
package com.example.mycnp.mapper;

import com.example.mycnp.domain.DemandeQualification;
import com.example.mycnp.model.Enum.Criticite;
import com.example.mycnp.model.Enum.Decision;
import com.example.mycnp.model.Enum.Local;
import com.example.mycnp.model.StartWfRequestDto;
import com.example.mycnp.model.dto.DemandeQuesResDTO;
import com.example.mycnp.model.dto.DemandeRequestDTO;
import lombok.experimental.UtilityClass;

import java.util.List;
import java.util.stream.Collectors;

@UtilityClass
public class DemandeRequestMapper {
    public static DemandeRequestDTO mapToDTO(DemandeQualification demande) {
        String porteurProjet = demande.getPorteurProjet() != null ? demande.getPorteurProjet().getPrenom() + " " + demande.getPorteurProjet().getNom() : "--";
        String porteurMetier = demande.getPorteurMetier() != null ? demande.getPorteurMetier().getPrenom() + " " + demande.getPorteurMetier().getNom() : "--";
        String coordinateurCNP = demande.getCoordinateurCNP() != null ? demande.getCoordinateurCNP().getPrenom() + " " + demande.getCoordinateurCNP().getNom() : "--";
        String sponsorM = demande.getSponsorM() != null ? demande.getSponsorM().getPrenom() + " " + demande.getSponsorM().getNom() : "--";
        List<DemandeQuesResDTO> questionResponses = demande.getDemandeQualificationQuestionReponses().stream()
                .map(q -> new DemandeQuesResDTO(
                        q.getId(),
                        q.getEstTitre(),
                        q.getAnswered(),
                        q.getParentQuestion() != null ? q.getParentQuestion().getId() : null,
                        q.getSubQuestion() != null && q.getSubQuestion().getId() != null
                                ? q.getSubQuestion().getId() : null
                ))
                .collect(Collectors.toList());


        return DemandeRequestDTO.builder()
                .id(demande.getId())
                .filiale(demande.getNomFiliale())
                .objet(demande.getObjetDemande())
                .type(demande.getType())
                .description(demande.getDescription())
                .date(demande.getDateLancement())
                .dateDemande(demande.getDateDemande())
                .attachBase64(demande.getAttachBase64())
                .porteurProjet(porteurProjet)
                .porteurMetier(porteurMetier)
                .statut(demande.getStatus())
                .decision(demande.getDecision() != null ? demande.getDecision().name() : "--")
                .criticite(demande.getCriticite() != null ? demande.getCriticite().name() : "--")
                .local(demande.getLocal() != null ? demande.getLocal().name() : "--")
                .transmet(demande.isTransmet())
                .transmetSponsor(demande.isTransmetSponsor())
                .dateKickOff(demande.getDateKickOff())
                .commentaire(demande.getCommentaire())
                .coordinateurCNP(coordinateurCNP)
                .sponsor(sponsorM)
                .numeroDemande(demande.getNumeroDemande())
                .dateQualification(demande.getDateQualification())
                .etape(demande.getEtape())
                .status(demande.getStatus())
                .dateModification(demande.getDateModification())
                .dateCreation(demande.getDateCreation())
                .kickOffDecision(demande.getKickOffDecision())
                .demandeQualificationQuestionReponses(questionResponses)
                .build();
    }
    public static DemandeQualification mapToEntity(StartWfRequestDto dto) {
        DemandeQualification demande = new DemandeQualification();
        demande.setId(dto.getId());
        demande.setNomFiliale(dto.getFiliale());
        demande.setObjetDemande(dto.getObjet());
        demande.setType(dto.getType());
        demande.setDescription(dto.getDescription());
        demande.setDateLancement(dto.getDate());
        demande.setDateDemande(dto.getDateDemande());
        demande.setAttachBase64(dto.getAttachBase64());
        demande.setStatut(dto.getStatut());
        demande.setDecision(dto.getDecision() != null ? Decision.valueOf(dto.getDecision()) : null);
        demande.setCriticite(dto.getCriticite() != null ? Criticite.valueOf(dto.getCriticite()) : null);
        demande.setLocal(dto.getLocal() != null ? Local.valueOf(dto.getLocal()) : null);
        demande.setTransmet(dto.isTransmet());
        demande.setTransmetSponsor(dto.isTransmetSponsor());
        demande.setDateKickOff(dto.getDateKickOff());
        demande.setCommentaire(dto.getCommentaire());
        demande.setNumeroDemande(dto.getNumeroDemande());
        demande.setDateQualification(dto.getDateQualification());
        demande.setEtape(dto.getEtape());

        return demande;
    }
}
