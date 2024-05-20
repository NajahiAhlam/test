import { Injectable } from '@angular/core';
// import {UserService} from "./user.service";
import {NbIcon, NbMenuItem} from "@nebular/theme";
import {Observable, of} from "rxjs";
import { UserService } from 'src/app/@core/services/user.service';
import {NbIconConfig} from "@nebular/theme/components/icon/icon.component";
import { KeycloakService } from 'keycloak-angular';

@Injectable({
  providedIn: 'root'
})
export class MenuGeneratorService {

  constructor(private keycloakService:KeycloakService) { }
  getMenuItems(): Observable<NbMenuItem[]> {
    let authorities = this.keycloakService.getUserRoles();
    let items: NbMenuItem[]=[];
    if(authorities){
      items= [
        ...this.createMenuItems(authorities),
        ...this.dashboardMenuItems(authorities),
        ...this.contratsMenuItems(authorities),
        ...this.usersMenuItems(authorities),
        ... this.GestionReferentiel(authorities),
        ...this.rapportFinancers(authorities),
        ...this.permissionsMenuItems(authorities)
         
      ]
    }

    return of(items);
  }

  dashboardMenuItems=(authorities: string | string[]): NbMenuItem[] =>{
    if (authorities.includes("ADMIN") || authorities.includes("VALIDATOR") || authorities.includes("INITIATOR")){
      let menuDashboard: NbMenuItem={
        title: 'Dashboard',
        link:'/pages/dashboard',
        icon: {
          icon: 'chart-line',
          pack: 'fas',
        },
        children: [],
      }
      menuDashboard.children?.push({
        title: 'Vue 360°',
        icon: 'eye',
        link: '/pages/dashboard/v360'
      },{
        title: 'Dashboard Programme',
        icon: 'eye',
        link: '/pages/dashboard/dashboard-programme'
      },{
        title:'ScoreCard360°',
        icon:'eye',
        link:'/pages/dashboard/score-card'
      },{
        title:'Data Quality Report - DQR',
        icon:'clipboard',
        link:'/pages/dashboard/DQR'
      },{
        title:'Data Harmonization Report - DHR',
        icon :'clipboard',
        link:'/pages/dashboard/DHR'
      },{
        title:'Budget Analysis',
        icon:'file',
        link:'/pages/dashboard/budget-analysis'
      },{
        title:'Trajectoire de Cloture',
        icon:'clipboard',
        link:'/pages/dashboard/trajectoire-cloture'
      })
      return [menuDashboard]
    }
    return [];
  }
  usersMenuItems = (authorities: string | string[]): NbMenuItem[] => {
    if (authorities.includes('ADMIN')) {
      return  [
            { title: "Gestion des Utilisateurs", icon: "user" , link: "/pages/utilisateur-list"
            }
          ]
    }
    return []
  }

  createMenuItems = (authorities: string | string[]): NbMenuItem[] => {
    if (authorities.includes('ADMIN')) {
      return  [
            { title: "Creer Record", icon: "edit-outline" , link: "/pages/utilisateur-list"
            }
          ]
    }
    return []
  }
  contratsMenuItems = (authorities: string | string[]): NbMenuItem[] => {
    if (authorities.includes('ADMIN')) {
      return  [
            { title: "Listes des Contrats", icon: "list" , link: "/pages/gestion-contrat"
            }
          ]
    }
    return []
  }
  permissionsMenuItems = (authorities: string | string[]): NbMenuItem[] => {
    if (authorities.includes('ADMIN')) {
      return  [
            { title: "gestion des permissions", icon: "list" , link: "/pages/permission-list"
            }
          ]
    }
    return []
  }
  GestionReferentiel(authorities:string|string[]){
    if (authorities.includes("ADMIN")|| authorities.includes("CELLULE_HABILITATION")){
      let gAdminMenu: NbMenuItem={
        title: "Gestion Référentiels",
        children: [],
        icon:{
          icon: "list",
          pack:'fas'
        },
        expanded: false,
      };
      gAdminMenu.children?.push(
        {
        title: "Programmes",
        link: "/pages/Gestion-Référentiels/programme-list",
        icon:'pen-to-square'

        },{
          title: "Leads",
          link: "/pages/Gestion-Référentiels/lead-list",
          icon:'pen-to-square'

        },{
          title: "Fournisseur",
          link: "/pages/Gestion-Référentiels/fournisseur-list",
          icon:'pen-to-square'

        },
        );
      return [gAdminMenu];
    }
    return [];
  }
  rapportFinancers(authorities:string|string[]){
    if (authorities.includes("ADMIN")|| authorities.includes("CELLULE_HABILITATION")){
      let gAdminMenu: NbMenuItem={
        title: "RAPPORTS FINANCIERS",
        children: [],
        icon:{
          icon: "file-lines",
          pack:'fas'
        },
        expanded: false,
      };
      gAdminMenu.children?.push(
        {
        title: "Biling Tracking",
        link: "/pages/Gestion-Référentiels/programmeList",
        icon:'chart-line'
        }
        );
      return [gAdminMenu];
    }
    return [];
  }
}
