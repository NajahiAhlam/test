2025-05-29 10:40:20.515 ERROR [mycnp-bff,398bfede2f1a7614,398bfede2f1a7614] 9016 --- [nio-8081-exec-5] o.a.c.c.C.[.[.[.[dispatcherServlet]      : Servlet.service() for servlet [dispatcherServlet] in context with path [/api] threw exception [Request processing failed; nested exception is java.time.format.DateTimeParseException: Text 'Tue Apr 01 2025 00:00:00 GMT+0000 (UTC)' could not be parsed, unparsed text found at index 28] with root cause

java.time.format.DateTimeParseException: Text 'Tue Apr 01 2025 00:00:00 GMT+0000 (UTC)' could not be parsed, unparsed text found at index 28
	at java.time.format.DateTimeFormatter.parseResolved0(DateTimeFormatter.java:1952) ~[na:1.8.0_352-352]
	at java.time.format.DateTimeFormatter.parse(DateTimeFormatter.java:1851) ~[na:1.8.0_352-352]
	at java.time.ZonedDateTime.parse(ZonedDateTime.java:597) ~[na:1.8.0_352-352]
	at com.example.mycnp.controller.ReportingController.getGroupedConditionsByAssigne(ReportingController.java:62) ~[classes/:na]
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_352-352]
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_352-352]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_352-352]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_352-352]
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:205) ~[spring-web-5.3.27.jar:5.3.27]
	at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:150) ~[spring-web-5.3.27.jar:5.3.27]
	at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:117) ~[spring-webmvc-5.3.27.jar:5.3.27]
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:895) ~[spring-webmvc-5.3.27.jar:5.3.27]
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:808) ~[spring-webmvc-5.3.27.jar:5.3.27]
	at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87) ~[spring-webmvc-5.3.27.jar:5.3.27]
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1072) ~[spring-webmvc-5.3.27.jar:5.3.27]
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:965) ~[spring-webmvc-5.3.27.jar:5.3.27]
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006) ~[spring-webmvc-5.3.27.jar:5.3.27]
	at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:898) ~[spring-webmvc-5.3.27.jar:5.3.27]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:529) ~[tomcat-embed-core-9.0.74.jar:4.0.FR]
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883) ~[spring-webmvc-5.3.27.jar:5.3.27]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:623) ~[tomcat-embed-core-9.0.74.jar:4.0.FR]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:209) ~[tomcat-embed-core-9.0.74.jar:9.0.74]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:153) ~[tomcat-embed-core-9.0.74.jar:9.0.74]
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:51) ~[tomcat-embed-websocket-9.0.74.jar:9.0.74]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:178) ~[tomcat-embed-core-9.0.74.jar:9.0.74]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:153) ~[tomcat-embed-core-9.0.74.jar:9.0.74]
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:337) ~[spring-security-web-5.7.8.jar:5.7.8]
	at org.springframework.security.web.access.intercept.FilterSecurityInterceptor.invoke(FilterSecurityInterceptor.java:115) ~[spring-security-web-5.7.8.jar:5.7.8]
	at org.springframework.security.web.access.intercept.FilterSecurityInterceptor.doFilter(FilterSecurityInterceptor.java:81) ~[spring-security-web-5.7.8.jar:5.7.8]
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:346) ~[spring-security-web-5.7.8.jar:5.7.8]
	at org.springframework.security.web.access.ExceptionTranslationFilter.doFilter(ExceptionTranslationFilter.java:122) ~[spring-security-web-5.7.8.jar:5.7.8]
 @GetMapping("/conditions/by-assigne")
    public ResponseEntity<Map<String, List<Map<String, Object>>>> getGroupedConditionsByAssigne(
            @RequestParam(required = false) String startDate,
            @RequestParam(required = false) String endDate) {

        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("EEE MMM dd yyyy HH:mm:ss zzz", Locale.ENGLISH);

        ZonedDateTime startZoned = ZonedDateTime.parse(startDate, formatter);
        LocalDate start = startZoned.toLocalDate();

        ZonedDateTime endZoned = ZonedDateTime.parse(endDate, formatter);
        LocalDate end = endZoned.toLocalDate();



        System.out.println("********************************************************start*****: " + start + " end  **** :  " + end);

        Map<String, List<Map<String, Object>>> groupedConditions = reportingService.getGroupedConditionsByAssigne(start, end);
        return ResponseEntity.ok(groupedConditions);
    }
