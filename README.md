import { Component, OnInit } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';

@Component({
  selector: 'app-stepper',
  templateUrl: './stepper.component.html',
  styleUrls: ['./stepper.component.scss']
})
export class StepperComponent implements OnInit {

  projetId!: number;
  currentStep = 0;
  maxStep = 4; // total steps - 1

  constructor(private route: ActivatedRoute, private router: Router) {}

  ngOnInit(): void {
    this.projetId = Number(this.route.snapshot.paramMap.get('id'));
    this.currentStep = Number(this.route.snapshot.paramMap.get('step')) || 0;
  }

  nextStep() {
    if (this.currentStep < this.maxStep) {
      this.currentStep++;
      this.updateRoute();
    }
  }

  previousStep() {
    if (this.currentStep > 0) {
      this.currentStep--;
      this.updateRoute();
    }
  }

  private updateRoute() {
    console.log('Step changed to:', this.currentStep);
    this.router.navigate(['/stepper', this.projetId, 'step', this.currentStep], { replaceUrl: true });
  }
}
