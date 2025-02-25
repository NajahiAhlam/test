2025-02-25 15:06:56.310 ERROR 23424 --- [nio-9090-exec-4] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is org.springframework.dao.InvalidDataAccessApiUsageException: org.hibernate.TransientPropertyValueException: object references an unsaved transient instance - save the transient instance before flushing : com.sgma.cockpit.entities.ChargeCout.fournisseur -> com.sgma.cockpit.entities.referentiels.Fournisseur; nested exception is java.lang.IllegalStateException: org.hibernate.TransientPropertyValueException: object references an unsaved transient instance - save the transient instance before flushing : com.sgma.cockpit.entities.ChargeCout.fournisseur -> com.sgma.cockpit.entities.referentiels.Fournisseur] with root cause

org.hibernate.TransientPropertyValueException: object references an unsaved transient instance - save the transient instance before flushing : com.sgma.cockpit.entities.ChargeCout.fournisseur -> com.sgma.cockpit.entities.referentiels.Fournisseur
	at org.hibernate.engine.spi.CascadingActions$8.noCascade(CascadingActions.java:379) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.internal.Cascade.cascade(Cascade.java:169) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.AbstractSaveEventListener.cascadeBeforeSave(AbstractSaveEventListener.java:426) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.DefaultPersistEventListener.justCascade(DefaultPersistEventListener.java:167) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.DefaultPersistEventListener.entityIsPersistent(DefaultPersistEventListener.java:160) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.DefaultPersistEventListener.onPersist(DefaultPersistEventListener.java:124) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.service.internal.EventListenerGroupImpl.fireEventOnEachListener(EventListenerGroupImpl.java:118) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.internal.SessionImpl.persistOnFlush(SessionImpl.java:801) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.spi.CascadingActions$8.cascade(CascadingActions.java:341) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.internal.Cascade.cascadeToOne(Cascade.java:510) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.internal.Cascade.cascadeAssociation(Cascade.java:434) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.internal.Cascade.cascadeProperty(Cascade.java:220) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.internal.Cascade.cascadeCollectionElements(Cascade.java:543) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.internal.Cascade.cascadeCollection(Cascade.java:474) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.internal.Cascade.cascadeAssociation(Cascade.java:437) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.internal.Cascade.cascadeProperty(Cascade.java:220) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.internal.Cascade.cascade(Cascade.java:153) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.AbstractFlushingEventListener.cascadeOnFlush(AbstractFlushingEventListener.java:159) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.AbstractFlushingEventListener.prepareEntityFlushes(AbstractFlushingEventListener.java:149) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.AbstractFlushingEventListener.flushEverythingToExecutions(AbstractFlushingEventListener.java:82) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.DefaultFlushEventListener.onFlush(DefaultFlushEventListener.java:39) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.service.internal.EventListenerGroupImpl.fireEventOnEachListener(EventListenerGroupImpl.java:107) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.internal.SessionImpl.doFlush(SessionImpl.java:1407) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.internal.SessionImpl.managedFlush(SessionImpl.java:489) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.internal.SessionImpl.flushBeforeTransactionCompletion(SessionImpl.java:3303) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.internal.SessionImpl.beforeTransactionCompletion(SessionImpl.java:2438) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.jdbc.internal.JdbcCoordinatorImpl.beforeTransactionCompletion(JdbcCoordinatorImpl.java:449) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.resource.transaction.backend.jdbc.internal.JdbcResourceLocalTransactionCoordinatorImpl.beforeCompletionCallback(JdbcResourceLocalTransactionCoordinatorImpl.java:183) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.resource.transaction.backend.jdbc.internal.JdbcResourceLocalTransactionCoordinatorImpl.access$300(JdbcResourceLocalTransactionCoordinatorImpl.java:40) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.resource.transaction.backend.jdbc.internal.JdbcResourceLocalTransactionCoordinatorImpl$TransactionDriverControlImpl.commit(JdbcResourceLocalTransactionCoordinatorImpl.java:281) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.transaction.internal.TransactionImpl.commit(TransactionImpl.java:101) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.springframework.orm.jpa.JpaTransactionManager.doCommit(JpaTransactionManager.java:562) ~[spring-orm-5.3.28.jar:5.3.28]
	at org.springframework.transaction.support.AbstractPlatformTransactionManager.processCommit(AbstractPlatformTransactionManager.java:743) ~[spring-tx-5.3.28.jar:5.3.28]
	at org.springframework.transaction.support.AbstractPlatformTransactionManager.commit(AbstractPlatformTransactionManager.java:711) ~[spring-tx-5.3.28.jar:5.3.28]
	at org.springframework.transaction.interceptor.TransactionAspectSupport.commitTransactionAfterReturning(TransactionAspectSupport.java:654) ~[spring-tx-5.3.28.jar:5.3.28]
	at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:407) ~[spring-tx-5.3.28.jar:5.3.28]
	at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119) ~[spring-tx-5.3.28.jar:5.3.28]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186) ~[spring-aop-5.3.28.jar:5.3.28]
	at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:763) ~[spring-aop-5.3.28.jar:5.3.28]
	at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:708) ~[spring-aop-5.3.28.jar:5.3.28]
	at com.sgma.cockpit.service.impl.DemandeServiceImp$$EnhancerBySpringCGLIB$$f83f65a4.updateDemande(<generated>) ~[classes/:na]
	at com.sgma.cockpit.controller.DemandeController.updateDemande(DemandeController.java:42) ~[classes/:na]
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_352-352]
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_352-352]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_352-352]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_352-352]
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:205) ~[spring-web-5.3.28.jar:5.3.28]
	at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:150) ~[spring-web-5.3.28.jar:5.3.28]
	at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:117) ~[spring-webmvc-5.3.28.jar:5.3.28]
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:895) ~[spring-webmvc-5.3.28.jar:5.3.28]
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:808) ~[spring-webmvc-5.3.28.jar:5.3.28]
	at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87) ~[spring-webmvc-5.3.28.jar:5.3.28]
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1072) ~[spring-webmvc-5.3.28.jar:5.3.28]
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:965) ~[spring-webmvc-5.3.28.jar:5.3.28]
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006) ~[spring-webmvc-5.3.28.jar:5.3.28]
	at org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:909) ~[spring-webmvc-5.3.28.jar:5.3.28]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:555) ~[tomcat-embed-core-9.0.76.jar:4.0.FR]
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883) ~[spring-webmvc-5.3.28.jar:5.3.28]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:623) ~[tomcat-embed-core-9.0.76.jar:4.0.FR]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:209) ~[tomcat-embed-core-9.0.76.jar:9.0.76]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:153) ~[tomcat-embed-core-9.0.76.jar:9.0.76]
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:51) ~[tomcat-embed-websocket-9.0.76.jar:9.0.76]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:178) ~[tomcat-embed-core-9.0.76.jar:9.0.76]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:153) ~[tomcat-embed-core-9.0.76.jar:9.0.76]
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:337) ~[spring-security-web-5.7.9.jar:5.7.9]
	at org.springframework.security.web.access.intercept.FilterSecurityInterceptor.invoke(FilterSecurityInterceptor.java:115) ~[spring-security-web-5.7.9.jar:5.7.9]
	at org.springframework.security.web.access.intercept.FilterSecurityInterceptor.doFilter(FilterSecurityInterceptor.java:81) ~[spring-security-web-5.7.9.jar:5.7.9]
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:346) ~[spring-security-web-5.7.9.jar:5.7.9]
  @Override
    @Transactional
    public void updateDemande(@RequestBody DemandeDTO demandeDTO) throws PermissionException, ActionNONAuthoriseException {


        if (!userService.canDo("Modifier Demande COPRO")) {
            throw new PermissionException("ressource introuvable");
        }

        System.out.println(demandeDTO);
        Optional<Demande> optionalDemande = demandeRepository.findById(demandeDTO.getId());

        if (!optionalDemande.isPresent()) {
            throw new RuntimeException("Demande a modifié est introuvable");
        }

        if (demandeDTO.getSession() == null || demandeDTO.getSession().getId() == 0) {
            demandeDTO.setSession(null); // Set session to null if ID is 0 or session is null
        }
        Demande demande = modelMapper.map(demandeDTO, Demande.class);
        List<String> statusList = Arrays.asList("COPRO", "rejetée", "validé");

        if (statusList.contains(demande.getStatusFicheCopro())) {
            throw new ActionNONAuthoriseException("Action non authoriser");
        }
        demande.getDispositifRh().forEach(dispositifRh -> {
            dispositifRh.setDemande(demande);

            dispositifRhRepository.save(dispositifRh);

        });
      /* demande.getChargeCout().forEach(chargeCout -> {
           chargeCout.setDemande(demande);

            chargeCoutRepository.save(chargeCout);

        });*/
        if (demande.getChargeCout() != null) {
            demande.getChargeCout().removeIf(Objects::isNull);
            for (ChargeCout charge : demande.getChargeCout()) {
                if (charge.getFournisseur() == null) {
                    charge.setFournisseur(null);
                }

                charge.setDemande(demande);
                chargeCoutRepository.save(charge);
            }
        }

        Demande saveDemande = demandeRepository.save(demande);
        DemandeDTO map = modelMapper.map(saveDemande, DemandeDTO.class);
       // return map;
    }

