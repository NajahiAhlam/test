2025-05-29 08:54:54.256 ERROR [mycnp-bff,66fab4611a3bdd1b,66fab4611a3bdd1b] 14232 --- [io-8081-exec-10] o.a.c.c.C.[.[.[.[dispatcherServlet]      : Servlet.service() for servlet [dispatcherServlet] in context with path [/api] threw exception [Request processing failed; nested exception is org.springframework.dao.InvalidDataAccessResourceUsageException: Named parameter not bound : endDate; nested exception is org.hibernate.QueryException: Named parameter not bound : endDate] with root cause

org.hibernate.QueryException: Named parameter not bound : endDate
	at org.hibernate.query.internal.QueryParameterBindingsImpl.verifyParametersBound(QueryParameterBindingsImpl.java:211) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.query.internal.AbstractProducedQuery.beforeQuery(AbstractProducedQuery.java:1511) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.query.internal.NativeQueryImpl.beforeQuery(NativeQueryImpl.java:253) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.query.internal.AbstractProducedQuery.list(AbstractProducedQuery.java:1615) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.query.Query.getResultList(Query.java:165) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.springframework.data.jpa.repository.query.JpaQueryExecution$CollectionExecution.doExecute(JpaQueryExecution.java:128) ~[spring-data-jpa-2.7.11.jar:2.7.11]
	at org.springframework.data.jpa.repository.query.JpaQueryExecution.execute(JpaQueryExecution.java:90) ~[spring-data-jpa-2.7.11.jar:2.7.11]
	at org.springframework.data.jpa.repository.query.AbstractJpaQuery.doExecute(AbstractJpaQuery.java:156) ~[spring-data-jpa-2.7.11.jar:2.7.11]
	at org.springframework.data.jpa.repository.query.AbstractJpaQuery.execute(AbstractJpaQuery.java:144) ~[spring-data-jpa-2.7.11.jar:2.7.11]
	at org.springframework.data.repository.core.support.RepositoryMethodInvoker.doInvoke(RepositoryMethodInvoker.java:137) ~[spring-data-commons-2.7.11.jar:2.7.11]
	at org.springframework.data.repository.core.support.RepositoryMethodInvoker.invoke(RepositoryMethodInvoker.java:121) ~[spring-data-commons-2.7.11.jar:2.7.11]
 @Query(value = "SELECT c.id, c.assigne_user_id, u.email, COUNT(c.id), c.date_lancement, " +
            "c.number_semaine_apres_lancement, c.date_echeance, c.categorie, c.etat " +
            "FROM conditions c " +
            "JOIN users u ON c.assigne_user_id = u.user_id " +
            "WHERE (:startDate::DATE IS NULL OR " +
            "(c.categorie = 'poste condition' AND (c.date_lancement + INTERVAL '1 week' * c.number_semaine_apres_lancement) " +
            "BETWEEN :startDate::DATE AND :endDate::DATE)) " +
            "OR (:startDate::DATE IS NULL OR " +
            "(c.categorie = 'précondition' AND c.date_echeance BETWEEN :startDate::DATE AND :endDate::DATE)) " +
            "GROUP BY c.id, c.assigne_user_id, u.email",
            nativeQuery = true)
    List<Object[]> findFilteredConditions(@Param("start") LocalDate startDate, @Param("end") LocalDate endDate);
