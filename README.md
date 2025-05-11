2025-05-11 01:20:25.181 ERROR [mycnp-bff,83963fea2ce7ed63,83963fea2ce7ed63] 11784 --- [nio-8081-exec-1] o.a.c.c.C.[.[.[.[dispatcherServlet]      : Servlet.service() for servlet [dispatcherServlet] in context with path [/api] threw exception [Request processing failed; nested exception is org.springframework.dao.InvalidDataAccessApiUsageException: org.hibernate.TransientObjectException: object references an unsaved transient instance - save the transient instance before flushing: com.example.mycnp.domain.Conditions; nested exception is java.lang.IllegalStateException: org.hibernate.TransientObjectException: object references an unsaved transient instance - save the transient instance before flushing: com.example.mycnp.domain.Conditions] with root cause

org.hibernate.TransientObjectException: object references an unsaved transient instance - save the transient instance before flushing: com.example.mycnp.domain.Conditions
	at org.hibernate.engine.internal.ForeignKeys.getEntityIdentifierIfNotUnsaved(ForeignKeys.java:347) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.type.EntityType.getIdentifier(EntityType.java:508) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.type.EntityType.nullSafeSet(EntityType.java:281) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.persister.collection.AbstractCollectionPersister.writeElement(AbstractCollectionPersister.java:925) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.persister.collection.AbstractCollectionPersister.recreate(AbstractCollectionPersister.java:1347) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.action.internal.CollectionRecreateAction.execute(CollectionRecreateAction.java:50) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.spi.ActionQueue.executeActions(ActionQueue.java:604) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.spi.ActionQueue.lambda$executeActions$1(ActionQueue.java:478) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at java.util.LinkedHashMap.forEach(LinkedHashMap.java:684) ~[na:1.8.0_352-352]
	at org.hibernate.engine.spi.ActionQueue.executeActions(ActionQueue.java:475) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.AbstractFlushingEventListener.performExecutions(AbstractFlushingEventListener.java:344) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.event.internal.DefaultFlushEventListener.onFlush(DefaultFlushEventListener.java:40) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
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
	at org.springframework.orm.jpa.JpaTransactionManager.doCommit(JpaTransactionManager.java:562) ~[spring-orm-5.3.27.jar:5.3.27]
	at org.springframework.transaction.support.AbstractPlatformTransactionManager.processCommit(AbstractPlatformTransactionManager.java:743) ~[spring-tx-5.3.27.jar:5.3.27]
	at org.springframework.transaction.support.AbstractPlatformTransactionManager.commit(AbstractPlatformTransactionManager.java:711) ~[spring-tx-5.3.27.jar:5.3.27]
	at org.springframework.transaction.interceptor.TransactionAspectSupport.commitTransactionAfterReturning(TransactionAspectSupport.java:654) ~[spring-tx-5.3.27.jar:5.3.27]
	at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:407) ~[spring-tx-5.3.27.jar:5.3.27]
	at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119) ~[spring-tx-5.3.27.jar:5.3.27]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186) ~[spring-aop-5.3.27.jar:5.3.27]
	at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:763) ~[spring-aop-5.3.27.jar:5.3.27]
	at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:708) ~[spring-aop-5.3.27.jar:5.3.27]
	at com.example.mycnp.service.impl.RequestServiceImpl$$EnhancerBySpringCGLIB$$1fe86cfb.createRisqueInstance(<generated>) ~[classes/:na]
	at com.example.mycnp.controller.WfController.updateRisque(WfController.java:151) ~[classes/:na]
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
	at org.springframework.web.servlet.FrameworkServlet.doPut(FrameworkServlet.java:920) ~[spring-webmvc-5.3.27.jar:5.3.27]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:558) ~[tomcat-embed-core-9.0.74.jar:4.0.FR]
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883) ~[spring-webmvc-5.3.27.jar:5.3.27]
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:623) ~[tomcat-embed-core-9.0.74.jar:4.0.FR]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:209) ~[tomcat-embed-core-9.0.74.jar:9.0.74]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:153) ~[tomcat-embed-core-9.0.74.jar:9.0.74]
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:51) ~[tomcat-embed-websocket-9.0.74.jar:9.0.74]
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:178) ~[tomcat-embed-core-9.0.74.jar:9.0.74]
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:153) ~[tomcat-embed-core-9.0.74.jar:9.0.74]
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:337) ~[spring-security-web-5.7.8.jar:5.7.8]
	at org.springframework.security.web.access.intercept.FilterSecurityInterceptor.invoke(FilterSecurityInterceptor.java:115) ~[spring-security-web-5.7.8.jar:5.7.8]
	at org.springframework.securit
  @Transactional
    public RisqueInstanceDTO createRisqueInstance(RisqueInstanceDTO dto, Long demandeId) {
        for (ConditionsDTO conditionDTO : dto.getConditions()) {
            // Fetch user by email (assigne)
            Optional<User> assigneeUser = userRepository.findByEmail(conditionDTO.getAssigne());
            if (!assigneeUser.isPresent()) {
                // Handle the case where the user is not found
                throw new RuntimeException("User with email " + conditionDTO.getAssigne() + " not found");
            }

            // Create and save the new Conditions object
            Conditions condition = new Conditions();
            condition.setCategorie(conditionDTO.getCategorie());
            condition.setDateEcheance(conditionDTO.getDateEcheance());
            condition.setDateLancement(conditionDTO.getDateLancement());
            condition.setDetail(conditionDTO.getDetail());
            condition.setEtat(conditionDTO.getEtat());
            condition.setNumberSemaineApresLancement(conditionDTO.getNumberSemaineApresLancement());

            // Set the User as the assignee
            condition.setAssigne(assigneeUser.get());

            // Save the condition entity first
            conditionsRepository.save(condition);
            conditionDTO.setId(condition.getId());
        }

        // Create and save the RisqueInstance object
        RisqueInstance risqueInstance = RisqueInstanceMapper.toEntity(dto, userRepository);

        // Save the RisqueInstance
        RisqueInstance savedRisqueInstance = risqueInstanceRepository.save(risqueInstance);

        // Find and update the DemandeQualification
        DemandeQualification demande = demandeQualificationRepository.findById(demandeId)
                .orElseThrow(() -> new IllegalArgumentException("Aucune demande avec id = " + demandeId));

        // Add the new RisqueInstance to the DemandeQualification
        List<RisqueInstance> risqueInstances = Optional.ofNullable(demande.getRisques()).orElse(new ArrayList<>());
        risqueInstances.add(savedRisqueInstance);
        demande.setRisques(risqueInstances);

        // Save the updated DemandeQualification
        demandeQualificationRepository.save(demande);

        // Return the saved RisqueInstance as a DTO
        return RisqueInstanceMapper.toDTO(savedRisqueInstance);
    }
