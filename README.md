package com.example.mycnp.mapper;

import com.example.mycnp.domain.*;
import com.example.mycnp.model.dto.*;
import lombok.experimental.UtilityClass;

import java.util.List;
import java.util.stream.Collectors;

@UtilityClass
public class RisqueInstanceMapper {

    public static RisqueInstance toEntity(RisqueInstanceDTO dto) {
        RisqueInstance entity = new RisqueInstance();

        // Set base fields
        entity.setContexte(dto.getContexte());
        entity.setNiveauRisqueResiduel(dto.getNiveauRisqueResiduel());
        entity.setNiveauIntrinseque(dto.getNiveauIntrinseque());
        entity.setAutreRisque(dto.getAutreRisque());
        entity.setComment(dto.getComment());

        // Set Risque relation by ID
        if (dto.getRisqueId() != null) {
            Risque risque = new Risque();
            risque.setId(dto.getRisqueId());
            entity.setRisque(risque);
        }

        // Map Conditions
        if (dto.getConditions() != null) {
            List<Conditions> conditions = dto.getConditions().stream()
                    .map(c -> Conditions.builder()
                            .assigne(c.getAssigne())
                            .categorie(c.getCategorie())
                            .commentCond(c.getCommentCond())
                            .dateEcheance(c.getDateEcheance())
                            .dateLancement(c.getDateLancement())
                            .detail(c.getDetail())
                            .etat(c.getEtat())
                            .numberSemaineApresLancement(c.getNumberSemaineApresLancement())
                            .build())
                    .collect(Collectors.toList());
            entity.setConditions(conditions);
        }

        // Map ZoneRisqueInstances
        if (dto.getZoneRisqueInstance() != null) {
            List<ZoneRisqueInstance> zoneInstances = dto.getZoneRisqueInstance().stream()
                    .map(z -> {
                        ZoneRisqueInstance zone = new ZoneRisqueInstance();
                        zone.setName(z.getName());

                        if (z.getZoneRisqueId() != null) {
                            ZoneRisque zr = new ZoneRisque();
                            zr.setId(z.getZoneRisqueId());
                            zone.setZoneRisque(zr);
                        }

                        // Map AnalyseInstances
                        if (z.getAnalyseRisqueInstance() != null) {
                            List<AnalyseInstance> analyseInstances = z.getAnalyseRisqueInstance().stream()
                                    .map(a -> {
                                        AnalyseInstance ai = new AnalyseInstance();
                                        ai.setFacteur(a.getFacteur());
                                        ai.setReponse(a.getReponse());

                                        if (a.getAnalyseRisqueId() != null) {
                                            AnalyseRisque analyseRisque = new AnalyseRisque();
                                            analyseRisque.setId(a.getAnalyseRisqueId());
                                            ai.setAnalyseRisque(analyseRisque);
                                        }

                                        return ai;
                                    }).collect(Collectors.toList());
                            zone.setAnalyseInstances(analyseInstances);
                        }

                        return zone;
                    }).collect(Collectors.toList());

            entity.setZoneRisques(zoneInstances);
        }

        return entity;
    }
}
