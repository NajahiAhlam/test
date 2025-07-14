
      <div class="key-value-pair" *ngIf="reporterNames">
        <div class="key col-6">Initiateur:</div>
        <div class="value col-6">{{ (reporterNames)"> }}</div>
      </div>

      <div class="key-value-pair" *ngIf="cordinateurNames">
        <div class="key col-6">Coordinateur:</div>
        <div class="value col-6" >{{ (cordinateurNames)"> }}</div>
      </div>

      <div class="key-value-pair" *ngIf="sponsorNames">
        <div class="key col-6">Sponsor:</div>
        <div class="value col-6" >{{ sponsorNames }}</div>
      </div>
hey chat in chaque of vlaue of those i have it like thise format: 
Luc Lefevre, Sophie Roux
i want to color the user actif in chaque of them and to do that i want you to do a methode and separete user by , if there are ore than one and after separte them the chech
if reporterNames the separete list  ==  data.lead | emailToName to color it with green and same for cordinateurNames the separet list check one by one if = data.assigneeName
color it with green and same for sponsorNames separete list by , and the one = data.sponsor color it with green
i did the pipe only in data.lead because i have it as email and i have this pipe emailToName  that get me the full name from email 
