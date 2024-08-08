deposer-fichier.component.ts:71 Error uploading file: 
HttpErrorResponse {headers: HttpHeaders, status: 200, statusText: 'OK', url: 'http://localhost:9090/api/cockpit/sciformaTimeSheets/upload', ok: false, …}
error
: 
error
: 
SyntaxError: Unexpected token 'F', "File uploa"... is not valid JSON at JSON.parse (<anonymous>) at XMLHttpRequest.onLoad (http://localhost:4200/vendor.js:68343:41) at _ZoneDelegate.invokeTask (http://localhost:4200/polyfills.js:8274:171) at http://localhost:4200/vendor.js:80081:49 at AsyncStackTaggingZoneSpec.onInvokeTask (http://localhost:4200/vendor.js:80081:30) at _ZoneDelegate.invokeTask (http://localhost:4200/polyfills.js:8274:54) at Object.onInvokeTask (http://localhost:4200/vendor.js:80395:25) at _ZoneDelegate.invokeTask (http://localhost:4200/polyfills.js:8274:54) at Zone.runTask (http://localhost:4200/polyfills.js:8078:37) at ZoneTask.invokeTask [as invoke] (http://localhost:4200/polyfills.js:8351:26)
text
: 
"File uploaded and parsed successfully"
[[Prototype]]
: 
Object
headers
: 
HttpHeaders
lazyInit
: 
() => {…}
lazyUpdate
: 
null
normalizedNames
: 
Map(0) {size: 0}
[[Prototype]]
: 
Object
message
: 
"Http failure during parsing for http://localhost:9090/api/cockpit/sciformaTimeSheets/upload"
name
: 
"HttpErrorResponse"
ok
: 
false
status
: 
200
statusText
: 
"OK"
url
: 
"http://localhost:9090/api/cockpit/sciformaTimeSheets/upload"
[[Prototype]]
: 
HttpResponseBase
deposer-fichier.component.ts:80 Upload error: 
HttpErrorResponse {headers: HttpHeaders, status: 200, statusText: 'OK', url: 'http://localhost:9090/api/cockpit/sciformaTimeSheets/upload', ok: false, …}
error
: 
error
: 
SyntaxError: Unexpected token 'F', "File uploa"... is not valid JSON at JSON.parse (<anonymous>) at XMLHttpRequest.onLoad (http://localhost:4200/vendor.js:68343:41) at _ZoneDelegate.invokeTask (http://localhost:4200/polyfills.js:8274:171) at http://localhost:4200/vendor.js:80081:49 at AsyncStackTaggingZoneSpec.onInvokeTask (http://localhost:4200/vendor.js:80081:30) at _ZoneDelegate.invokeTask (http://localhost:4200/polyfills.js:8274:54) at Object.onInvokeTask (http://localhost:4200/vendor.js:80395:25) at _ZoneDelegate.invokeTask (http://localhost:4200/polyfills.js:8274:54) at Zone.runTask (http://localhost:4200/polyfills.js:8078:37) at ZoneTask.invokeTask [as invoke] (http://localhost:4200/polyfills.js:8351:26)
text
: 
"File uploaded and parsed successfully"
[[Prototype]]
: 
Object
headers
: 
HttpHeaders {normalizedNames: Map(0), lazyUpdate: null, lazyInit: ƒ}
message
: 
"Http failure during parsing for http://localhost:9090/api/cockpit/sciformaTimeSheets/upload"
name
: 
"HttpErrorResponse"
ok
: 
false
status
: 
200
statusText
: 
"OK"
url
: 
"http://localhost:9090/api/cockpit/sciformaTimeSheets/upload"
[[Prototype]]
: 
HttpResponseBase
error	@	deposer-fichier.component.ts:80
Zone - Promise.then (asynchrone)		
getUserEmail	@	auth-interceptor.service.ts:41
handleRequest	@	auth-interceptor.service.ts:20
intercept	@	auth-interceptor.service.ts:13
intercept	@	http-error-handling-…eptor.service.ts:15
(anonyme)	@	request-auth-interceptor.service.ts:21
intercept	@	request-auth-interceptor.service.ts:14
Zone - XMLHttpRequest.addEventListener:readystatechange (asynchrone)		
uploadFile	@	deposer-fichier.component.ts:74
onFileSelected	@	deposer-fichier.component.ts:39
DeposerFichierComponent_Template_input_change_15_listener	@	deposer-fichier.component.html:22
Zone - HTMLInputElement.addEventListener:change (asynchrone)		
DeposerFichierComponent_Template	@	deposer-fichier.component.html:22
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
Afficher 1 605 autres frames
