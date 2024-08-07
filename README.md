auth-interceptor.service.ts:41 
 POST http://localhost:9090/api/cockpit/sciforma/upload 404 (Not Found)
excel.service.ts:40 Error uploading file: 
HttpErrorResponse {headers: HttpHeaders, status: 404, statusText: 'OK', url: 'http://localhost:9090/api/cockpit/sciforma/upload', ok: false, …}
error
: 
{timestamp: '2024-08-07T15:15:51.571+00:00', status: 404, error: 'Not Found', message: 'No message available', path: '/api/cockpit/sciforma/upload'}
headers
: 
HttpHeaders {normalizedNames: Map(0), lazyUpdate: null, lazyInit: ƒ}
message
: 
"Http failure response for http://localhost:9090/api/cockpit/sciforma/upload: 404 OK"
name
: 
"HttpErrorResponse"
ok
: 
false
status
: 
404
statusText
: 
"OK"
url
: 
"http://localhost:9090/api/cockpit/sciforma/upload"
[[Prototype]]
: 
HttpResponseBase
(anonyme)	@	excel.service.ts:40
Zone - Promise.then (asynchrone)		
getUserEmail	@	auth-interceptor.service.ts:41
handleRequest	@	auth-interceptor.service.ts:20
intercept	@	auth-interceptor.service.ts:13
intercept	@	http-error-handling-…eptor.service.ts:15
(anonyme)	@	request-auth-interceptor.service.ts:21
intercept	@	request-auth-interceptor.service.ts:14
Zone - Promise.then (asynchrone)		
uploadFile	@	dashboard-vue-projets.component.ts:81
DashboardVueProjetsComponent_Template_input_change_7_listener	@	dashboard-vue-projets.component.html:9
Zone - HTMLInputElement.addEventListener:change (asynchrone)		
DashboardVueProjetsComponent_Template	@	dashboard-vue-projets.component.html:9
load (asynchrone)		
p	@	node_modules\pace-js\pace.min.js:6
a	@	node_modules\pace-js\pace.min.js:6
nt.watch	@	node_modules\pace-js\pace.min.js:6
(anonyme)	@	node_modules\pace-js\pace.min.js:6
tt.trigger	@	node_modules\pace-js\pace.min.js:6
r.open	@	node_modules\pace-js\pace.min.js:6
Zone - Promise.then (asynchrone)		
getUserEmail	@	auth-interceptor.service.ts:41
handleRequest	@	auth-interceptor.service.ts:20
intercept	@	auth-interceptor.service.ts:13
intercept	@	http-error-handling-…eptor.service.ts:15
(anonyme)	@	request-auth-interceptor.service.ts:21
intercept	@	request-auth-interceptor.service.ts:14
Zone - Promise.then (asynchrone)		
loadChildren	@	app-routing.module.ts:9
Afficher 1 620 autres frames
app-init.ts:9 Token expired
﻿
  private uploadUrl = environment.baseUrl+ '/api/cockpit/sciforma/upload';

 uploadFile(file: File): Observable<any> {
    const formData: FormData = new FormData();
    formData.append('file', file, file.name);
  
    const headers = new HttpHeaders({
      'Content-Type': 'multipart/form-data; boundary=----WebKitFormBoundaryabcdefg'
    });
  
    const options = {
      headers,
      reportProgress: true,
      responseType: 'json'
    };
  
    return this._http.post(this.uploadUrl, formData, options as any).pipe(
      catchError(error => {
        console.error('Error uploading file:', error);
        return throwError(error);
      })
    );
  }
 package com.example.cockpit.controller;

import com.example.cockpit.entities.Canvas;
import com.example.cockpit.entities.Sciforma;
import com.example.cockpit.exception.SsoException;
import com.example.cockpit.repositories.CanvasRepository;
import com.example.cockpit.repositories.SciformaRepository;
import com.example.cockpit.service.impl.ExcelService;
import com.example.cockpit.service.impl.SciformaServiceImp;
import lombok.RequiredArgsConstructor;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.MultipartFile;

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.util.List;

@RequestMapping("api/cockpit/sciforma")
@RequiredArgsConstructor
public class SciformaController {

    private final SciformaServiceImp sciformaServiceImp;
    private final SciformaRepository sciformaRepository;

    @PostMapping("/upload")
    public ResponseEntity<String> uploadFile(@RequestParam("file") MultipartFile file) throws SsoException {
        if (!sciformaServiceImp.validateExcelFile(file)) {
            return ResponseEntity.badRequest().body("Invalid file format");
        }

        try (InputStream inputStream = file.getInputStream()) {
            List<Sciforma> sciformas = sciformaServiceImp.parseExcel(inputStream);
            sciformaRepository.saveAll(sciformas);
            return ResponseEntity.ok("File uploaded and parsed successfully");
        } catch (IOException e) {
            return ResponseEntity.status(500).body("Failed to parse the file");
        }
    }


    @GetMapping("/download")
    public ResponseEntity<byte[]> downloadFile() {
        List<Sciforma> sciformas = sciformaRepository.findAll();
        ByteArrayInputStream in = sciformaServiceImp.generateExcel(sciformas);
        HttpHeaders headers = new HttpHeaders();
        headers.add("Content-Disposition", "attachment; filename=factures.xlsx");
        try {
            byte[] bytes = toByteArray(in);
            return ResponseEntity
                    .ok()
                    .headers(headers)
                    .contentType(MediaType.APPLICATION_OCTET_STREAM)
                    .body(bytes);
        } catch (IOException e) {
            return ResponseEntity.status(500).body(null);
        }
    }

    private byte[] toByteArray(InputStream in) throws IOException {
        ByteArrayOutputStream buffer = new ByteArrayOutputStream();
        int nRead;
        byte[] data = new byte[1024];
        while ((nRead = in.read(data, 0, data.length)) != -1) {
            buffer.write(data, 0, nRead);
        }
        buffer.flush();
        return buffer.toByteArray();
    }

    @GetMapping("/getCountByPhase")
    public List<Object[]> getCountByPhase(
            @RequestParam Long year,
            @RequestParam(value = "programme", required = false) String programme,
            @RequestParam(value = "lead", required = false) String lead) {
        return sciformaServiceImp.getCountByPhase(year, programme, lead);
    }
}
 
