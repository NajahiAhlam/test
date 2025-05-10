package com.example.mycnp.mapper;

import com.example.mycnp.domain.*;
import com.example.mycnp.model.dto.*;

import java.util.List;
import java.util.stream.Collectors;

public class RisqueInstanceMapper {

    // Entity -> DTO
    public static RisqueInstanceDTO toDTO(RisqueInstance entity) {
        if (entity == null) return null;

        RisqueInstanceDTO dto = new RisqueInstanceDTO();
        dto.setRisqueId(entity.getRisqueId());
        dto.setContexte(entity.getContexte());
        dto.setNiveauIntrinseque(entity.getNiveauIntrinseque());
        dto.setNiveauRisqueResiduel(entity.getNiveauRisqueResiduel());
        dto.setAutreRisque(entity.getAutreRisque());
        dto.setComment(entity.getComment());

        // Zones
        List<ZoneRisqueInstanceDTO> zoneDtos = entity.getZoneRisqueInstances().stream()
                .map(zone -> {
                    ZoneRisqueInstanceDTO zoneDto = new ZoneRisqueInstanceDTO();
                    zoneDto.setZoneRisqueId(zone.getZoneRisqueId());
                    zoneDto.setName(zone.getName());

                    List<AnalyseRisqueInstanceDTO> analyseDtos = zone.getAnalyseRisqueInstances().stream()
                            .map(analyse -> {
                                AnalyseRisqueInstanceDTO analyseDto = new AnalyseRisqueInstanceDTO();
                                analyseDto.setAnalyseRisqueId(analyse.getAnalyseRisqueId());
                                analyseDto.setFacteur(analyse.getFacteur());
                                analyseDto.setReponse(analyse.getReponse());
                                return analyseDto;
                            }).collect(Collectors.toList());

                    zoneDto.setAnalyseRisqueInstance(analyseDtos);
                    return zoneDto;
                }).collect(Collectors.toList());

        dto.setZoneRisqueInstance(zoneDtos);

        // Conditions
        List<ConditionInstanceDTO> conditionDtos = entity.getConditionInstances().stream()
                .map(cond -> {
                    ConditionInstanceDTO condDto = new ConditionInstanceDTO();
                    condDto.setAssigne(cond.getAssigne());
                    condDto.setCategorie(cond.getCategorie());
                    condDto.setCommentCond(cond.getCommentCond());
                    condDto.setDetail(cond.getDetail());
                    condDto.setEtat(cond.getEtat());
                    condDto.setDateEcheance(cond.getDateEcheance());
                    condDto.setDateLancement(cond.getDateLancement());
                    condDto.setNumberSemaineApresLancement(cond.getNumberSemaineApresLancement());
                    return condDto;
                }).collect(Collectors.toList());

        dto.setConditions(conditionDtos);

        return dto;
    }

    // DTO -> Entity
    public static RisqueInstance toEntity(RisqueInstanceDTO dto) {
        if (dto == null) return null;

        RisqueInstance entity = new RisqueInstance();
        entity.setRisqueId(dto.getRisqueId());
        entity.setContexte(dto.getContexte());
        entity.setNiveauIntrinseque(dto.getNiveauIntrinseque());
        entity.setNiveauRisqueResiduel(dto.getNiveauRisqueResiduel());
        entity.setAutreRisque(dto.getAutreRisque());
        entity.setComment(dto.getComment());

        // Zones
        List<ZoneRisqueInstance> zoneEntities = dto.getZoneRisqueInstance().stream()
                .map(zoneDto -> {
                    ZoneRisqueInstance zone = new ZoneRisqueInstance();
                    zone.setZoneRisqueId(zoneDto.getZoneRisqueId());
                    zone.setName(zoneDto.getName());

                    List<AnalyseRisqueInstance> analyseEntities = zoneDto.getAnalyseRisqueInstance().stream()
                            .map(analyseDto -> {
                                AnalyseRisqueInstance analyse = new AnalyseRisqueInstance();
                                analyse.setAnalyseRisqueId(analyseDto.getAnalyseRisqueId());
                                analyse.setFacteur(analyseDto.getFacteur());
                                analyse.setReponse(analyseDto.getReponse());
                                return analyse;
                            }).collect(Collectors.toList());

                    zone.setAnalyseRisqueInstances(analyseEntities);
                    return zone;
                }).collect(Collectors.toList());

        entity.setZoneRisqueInstances(zoneEntities);

        // Conditions
        List<ConditionInstance> conditionEntities = dto.getConditions().stream()
                .map(condDto -> {
                    ConditionInstance cond = new ConditionInstance();
                    cond.setAssigne(condDto.getAssigne());
                    cond.setCategorie(condDto.getCategorie());
                    cond.setCommentCond(condDto.getCommentCond());
                    cond.setDetail(condDto.getDetail());
                    cond.setEtat(condDto.getEtat());
                    cond.setDateEcheance(condDto.getDateEcheance());
                    cond.setDateLancement(condDto.getDateLancement());
                    cond.setNumberSemaineApresLancement(condDto.getNumberSemaineApresLancement());
                    return cond;
                }).collect(Collectors.toList());

        entity.setConditionInstances(conditionEntities);

        return entity;
    }
}
