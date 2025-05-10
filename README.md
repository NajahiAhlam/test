package com.example.mycnp.mapper;

import com.example.mycnp.domain.*;
import com.example.mycnp.model.dto.*;
import lombok.experimental.UtilityClass;

import java.util.List;
import java.util.stream.Collectors;

@UtilityClass
public class RisqueInstanceMapper {

    // --- DTO to Entity ---
    public static RisqueInstance toEntity(RisqueInstanceDTO dto) {
        RisqueInstance entity = new RisqueInstance();

        // Full object reference instead of just ID
        Risque risque = new Risque();
        risque.setId(dto.getRisqueId());
        entity.setRisque(risque);

        entity.setContexte(dto.getContexte());
        entity.setNiveauIntrinseque(dto.getNiveauIntrinseque());
        entity.setAutreRisque(dto.getAutreRisque());
        entity.setNiveauRisqueResiduel(dto.getNiveauRisqueResiduel());
        entity.setComment(dto.getComment());

        // Conditions
        List<ConditionInstance> conditions = dto.getConditions().stream()
                .map(condDto -> {
                    ConditionInstance cond = new ConditionInstance();
                    cond.setCategorie(condDto.getCategorie());
                    cond.setAssigne(condDto.getAssigne());
                    cond.setCommentCond(condDto.getCommentCond());
                    cond.setDetail(condDto.getDetail());
                    cond.setEtat(condDto.getEtat());
                    cond.setDateEcheance(condDto.getDateEcheance());
                    cond.setDateLancement(condDto.getDateLancement());
                    cond.setNumberSemaineApresLancement(condDto.getNumberSemaineApresLancement());
                    cond.setRisqueInstance(entity); // link back
                    return cond;
                }).collect(Collectors.toList());

        entity.setConditions(conditions);

        // Zones
        List<ZoneRisqueInstance> zoneInstances = dto.getZoneRisqueInstance().stream()
                .map(zoneDto -> {
                    ZoneRisqueInstance zoneEntity = new ZoneRisqueInstance();
                    zoneEntity.setZoneRisqueId(zoneDto.getZoneRisqueId());
                    zoneEntity.setName(zoneDto.getName());
                    zoneEntity.setRisqueInstance(entity); // link back

                    // AnalyseRisque
                    List<AnalyseRisqueInstance> analyseInstances = zoneDto.getAnalyseRisqueInstance().stream()
                            .map(arDto -> {
                                AnalyseRisqueInstance analyse = new AnalyseRisqueInstance();
                                analyse.setAnalyseRisqueId(arDto.getAnalyseRisqueId());
                                analyse.setFacteur(arDto.getFacteur());
                                analyse.setReponse(arDto.getReponse());
                                analyse.setZoneRisqueInstance(zoneEntity); // link back
                                return analyse;
                            }).collect(Collectors.toList());

                    zoneEntity.setAnalyseRisqueInstance(analyseInstances);
                    return zoneEntity;
                }).collect(Collectors.toList());

        entity.setZoneRisqueInstance(zoneInstances);

        return entity;
    }

    // --- Entity to DTO ---
    public static RisqueInstanceDTO toDTO(RisqueInstance entity) {
        return RisqueInstanceDTO.builder()
                .risqueId(entity.getRisque() != null ? entity.getRisque().getId() : null)
                .contexte(entity.getContexte())
                .niveauIntrinseque(entity.getNiveauIntrinseque())
                .autreRisque(entity.getAutreRisque())
                .niveauRisqueResiduel(entity.getNiveauRisqueResiduel())
                .comment(entity.getComment())

                .conditions(entity.getConditions().stream().map(cond -> ConditionInstanceDTO.builder()
                        .categorie(cond.getCategorie())
                        .assigne(cond.getAssigne())
                        .commentCond(cond.getCommentCond())
                        .detail(cond.getDetail())
                        .etat(cond.getEtat())
                        .dateEcheance(cond.getDateEcheance())
                        .dateLancement(cond.getDateLancement())
                        .numberSemaineApresLancement(cond.getNumberSemaineApresLancement())
                        .build()).collect(Collectors.toList()))

                .zoneRisqueInstance(entity.getZoneRisqueInstance().stream().map(zone -> ZoneRisqueInstanceDTO.builder()
                        .zoneRisqueId(zone.getZoneRisqueId())
                        .name(zone.getName())
                        .analyseRisqueInstance(zone.getAnalyseRisqueInstance().stream().map(analyse -> AnalyseRisqueInstanceDTO.builder()
                                .analyseRisqueId(analyse.getAnalyseRisqueId())
                                .facteur(analyse.getFacteur())
                                .reponse(analyse.getReponse())
                                .build()).collect(Collectors.toList()))
                        .build()).collect(Collectors.toList()))
                .build();
    }
}
