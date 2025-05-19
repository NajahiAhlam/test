import { HttpClient, HttpHeaders } from '@angular/common/http';

// ...

validerRisque(id: number, payload: RisqueValidationRequest): Observable<RisqueInstance> {
  const headers = new HttpHeaders({ 'Content-Type': 'application/json' });

  return this.http.post<RisqueInstance>(
    `${this.apiUrl}/${id}/valider`,
    payload,
    { headers }
  );
}
