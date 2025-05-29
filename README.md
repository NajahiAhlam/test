2025-05-29 08:23:38.849 ERROR [mycnp-bff,7d916c7d852e68af,7d916c7d852e68af] 12868 --- [nio-8081-exec-8] o.h.engine.jdbc.spi.SqlExceptionHelper   : ERREUR: n'a pas pu déterminer le type de données du paramètre $1
2025-05-29 08:23:38.850 ERROR [mycnp-bff,7d916c7d852e68af,7d916c7d852e68af] 12868 --- [nio-8081-exec-8] o.a.c.c.C.[.[.[.[dispatcherServlet]      : Servlet.service() for servlet [dispatcherServlet] in context with path [/api] threw exception [Request processing failed; nested exception is org.springframework.dao.InvalidDataAccessResourceUsageException: could not extract ResultSet; SQL [n/a]; nested exception is org.hibernate.exception.SQLGrammarException: could not extract ResultSet] with root cause

org.postgresql.util.PSQLException: ERREUR: n'a pas pu déterminer le type de données du paramètre $1
	at org.postgresql.core.v3.QueryExecutorImpl.receiveErrorResponse(QueryExecutorImpl.java:2675) ~[postgresql-42.3.8.jar:42.3.8]
	at org.postgresql.core.v3.QueryExecutorImpl.processResults(QueryExecutorImpl.java:2365) ~[postgresql-42.3.8.jar:42.3.8]
	at org.postgresql.core.v3.QueryExecutorImpl.execute(QueryExecutorImpl.java:355) ~[postgresql-42.3.8.jar:42.3.8]
	at org.postgresql.jdbc.PgStatement.executeInternal(PgStatement.java:490) ~[postgresql-42.3.8.jar:42.3.8]
	at org.postgresql.jdbc.PgStatement.execute(PgStatement.java:408) ~[postgresql-42.3.8.jar:42.3.8]
	at org.postgresql.jdbc.PgPreparedStatement.executeWithFlags(PgPreparedStatement.java:167) ~[postgresql-42.3.8.jar:42.3.8]
	at org.postgresql.jdbc.PgPreparedStatement.executeQuery(PgPreparedStatement.java:119) ~[postgresql-42.3.8.jar:42.3.8]
	at com.zaxxer.hikari.pool.ProxyPreparedStatement.executeQuery(ProxyPreparedStatement.java:52) ~[HikariCP-4.0.3.jar:na]
	at com.zaxxer.hikari.pool.HikariProxyPreparedStatement.executeQuery(HikariProxyPreparedStatement.java) ~[HikariCP-4.0.3.jar:na]
	at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.extract(ResultSetReturnImpl.java:57) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.loader.Loader.getResultSet(Loader.java:2322) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.loader.Loader.executeQueryStatement(Loader.java:2075) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.loader.Loader.executeQueryStatement(Loader.java:2037) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.loader.Loader.doQuery(Loader.java:956) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.loader.Loader.doQueryAndInitializeNonLazyCollections(Loader.java:357) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.loader.Loader.doList(Loader.java:2868) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.loader.Loader.doList(Loader.java:2850) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.loader.Loader.listIgnoreQueryCache(Loader.java:2682) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.loader.Loader.list(Loader.java:2677) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.loader.custom.CustomLoader.list(CustomLoader.java:338) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.internal.SessionImpl.listCustomQuery(SessionImpl.java:2186) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.internal.AbstractSharedSessionContract.list(AbstractSharedSessionContract.java:1204) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.query.internal.NativeQueryImpl.doList(NativeQueryImpl.java:177) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.query.internal.AbstractProducedQuery.list(AbstractProducedQuery.java:1617) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.query.Query.getResultList(Query.java:165) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.springframework.data.jpa.repository.query.JpaQueryExecution$CollectionExecution.doExecute(JpaQueryExecution.java:128) ~[spring-data-jpa-2.7.11.jar:2.7.11]
	at org.springframework.data.jpa.repository.query.JpaQueryExecution.execute(JpaQueryExecution.java:90) ~[spring-data-jpa-2.7.11.jar:2.7.11]
	at org.springframework.data.jpa.repository.query.AbstractJpaQuery.doExecute(AbstractJpaQuery.java:156) ~[spring-data-jpa-2.7.11.jar:2.7.11]
	at org.springframework.data.jpa.repository.query.AbstractJpaQuery.execute(AbstractJpaQuery.java:144) ~[spring-data-jpa-2.7.11.jar:2.7.11]
	at org.springframework.data.repository.core.support.RepositoryMethodInvoker.doInvoke(RepositoryMethodInvoker.java:137) ~[spring-data-commons-2.7.11.jar:2.7.11]
	at org.springframework.data.repository.core.support.RepositoryMethodInvoker.invoke(RepositoryMethodInvoker.java:121) ~[spring-data-commons-2.7.11.jar:2.7.11]
	at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.doInvoke(QueryExecutorMethodInterceptor.java:160) ~[spring-data-commons-2.7.11.jar:2.7.11]
	at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.invoke(QueryExecutorMethodInterceptor.java:139) ~[spring-data-commons-2.7.11.jar:2.7.11]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186) ~[spring-aop-5.3.27.jar:5.3.27]
	at org.springframework.data.projection.DefaultMethodInvokingMethodInterceptor.invoke(DefaultMethodInvokingMethodInterceptor.java:81) ~[spring-data-commons-2.7.11.jar:2.7.11]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186) ~[spring-aop-5.3.27.jar:5.3.27]
	at org.springframework.transaction.interceptor.TransactionInterceptor$1.proceedWithInvocation(TransactionInterceptor.java:123) ~[spring-tx-5.3.27.jar:5.3.27]
	at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:388) ~[spring-tx-5.3.27.jar:5.3.27]
	at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119) ~[spring-tx-5.3.27.jar:5.3.27]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186) ~[spring-aop-5.3.27.jar:5.3.27]
	at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:137) ~[spring-tx-5.3.27.jar:5.3.27]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186) ~[spring-aop-5.3.27.jar:5.3.27]
	at org.springframework.data.jpa.repository.support.CrudMethodMetadataPostProcessor$CrudMethodMetadataPopulatingMethodInterceptor.invoke(CrudMethodMetadataPostProcessor.java:145) ~[spring-data-jpa-2.7.11.jar:2.7.11]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186) ~[spring-aop-5.3.27.jar:5.3.27]
	at org.springframework.aop.interceptor.ExposeInvocationInterceptor.invoke(ExposeInvocationInterceptor.java:97) ~[spring-aop-5.3.27.jar:5.3.27]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186) ~[spring-aop-5.3.27.jar:5.3.27]
	at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:220) ~[spring-aop-5.3.27.jar:5.3.27]
	at com.sun.proxy.$Proxy236.findFilteredConditions(Unknown Source) ~[na:na]
	at com.example.mycnp.service.impl.ReportingServiceImpl.getGroupedConditionsByAssigne(ReportingServiceImpl.java:331) ~[classes/:na]
 @Query(value = "SELECT  c.id, c.assigne_user_id, u.email, COUNT(c.id) , c.date_lancement, c.number_semaine_apres_lancement, c.date_echeance, c.categorie, c.etat " +
            "FROM conditions c " +
            "JOIN users u ON c.assigne_user_id = u.user_id " +
            "WHERE (:startDate IS NULL OR " +
            "(c.categorie = 'poste condition' AND (c.date_lancement + INTERVAL '1 week' * c.number_semaine_apres_lancement) BETWEEN :startDate AND :endDate)) " +
            "OR (:startDate IS NULL OR " +
            "(c.categorie = 'précondition' AND c.date_echeance BETWEEN :startDate AND :endDate)) " +
            "GROUP BY c.id, c.assigne_user_id, u.email",
            nativeQuery = true)
    List<Object[]> findFilteredConditions(@Param("startDate") LocalDate startDate, @Param("endDate") LocalDate endDate);	
