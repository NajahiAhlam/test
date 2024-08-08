@Query("SELECT "
     + "COUNT(CASE WHEN p.typeInvestissement.nom = 'RTB' AND p.isInitiative = false THEN 1 ELSE NULL END) AS countProjetsTransverse, "
     + "SUM(CASE WHEN p.isInitiative = false THEN b.intern + b.extern ELSE 0 END) AS sumTotalETP, "
     + "COUNT(CASE WHEN p.isInitiative = false AND p.phase <> 'Opportunité' THEN 1 ELSE NULL END) "
     + "  + COUNT(CASE WHEN p.isInitiative = true "
     + "                 AND p.phase <> 'Opportunité' "
     + "                 AND f.dateOuverture >= CURRENT_DATE "
     + "                 AND f.dateCloture <= CURRENT_DATE THEN 1 ELSE NULL END) "
     + "AS numerator, "
     + "COUNT(CASE WHEN p.isInitiative = false "
     + "             OR (p.isInitiative = true "
     + "                 AND f.dateOuverture >= CURRENT_DATE "
     + "                 AND f.dateCloture <= CURRENT_DATE) THEN 1 ELSE NULL END) "
     + "AS denominator "
     + "FROM Projet p "
     + "JOIN p.fdr f "
     + "JOIN p.leadPrincipal l "
     + "JOIN l.budget b "
     + "WHERE (:programme IS NULL OR p.programme.nom = :programme) "
     + "AND (:lead IS NULL OR EXISTS (SELECT l FROM p.leadPrincipal l WHERE l.intitule = :lead)) "
     + "AND (:enjeux IS NULL OR EXISTS (SELECT e FROM p.enjeux e WHERE e.nom = :enjeux))")
Map<String, Object> getAggregatedMetrics(@Param("programme") String programme,
                                         @Param("lead") String lead,
                                         @Param("enjeux") String enjeux);
------------
@Service
public class ProjetService {

    @Autowired
    private ProjetRepository projetRepository;

    public Map<String, Object> getAggregatedMetrics(String programme, String lead, String enjeux) {
        Map<String, Object> result = projetRepository.getAggregatedMetrics(programme, lead, enjeux);

        Long countProjetsTransverse = ((Number) result.get("countProjetsTransverse")).longValue();
        Long sumTotalETP = ((Number) result.get("sumTotalETP")).longValue();
        Long numerator = ((Number) result.get("numerator")).longValue();
        Long denominator = ((Number) result.get("denominator")).longValue();

        Double tauxAnnuelLancement = (denominator == 0) ? null : numerator.doubleValue() / denominator.doubleValue();

        Map<String, Object> metrics = new HashMap<>();
        metrics.put("countProjetsTransverse", countProjetsTransverse);
        metrics.put("sumTotalETP", sumTotalETP);
        metrics.put("numerator", numerator);
        metrics.put("denominator", denominator);
        metrics.put("tauxAnnuelLancement", tauxAnnuelLancement);

        return metrics;
    }
}


-------------------------------------------------------------
2024-08-08 09:53:23.289 ERROR 5460 --- [  restartedMain] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'analyticsController' defined in file [C:\Users\NAJAHIA\Downloads\cockpit (1)\cockpit\target\classes\com\example\cockpit\controller\AnalyticsController.class]: Unsatisfied dependency expressed through constructor parameter 0; nested exception is org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'analyticsServiceImp' defined in file [C:\Users\NAJAHIA\Downloads\cockpit (1)\cockpit\target\classes\com\example\cockpit\service\impl\AnalyticsServiceImp.class]: Unsatisfied dependency expressed through constructor parameter 0; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'projetRepository' defined in com.example.cockpit.repositories.ProjetRepository defined in @EnableJpaRepositories declared on JpaRepositoriesRegistrar.EnableJpaRepositoriesConfiguration: Invocation of init method failed; nested exception is org.springframework.data.repository.query.QueryCreationException: Could not create query for public abstract java.util.Map com.example.cockpit.repositories.ProjetRepository.getAggregatedMetrics(java.lang.Long,java.lang.String,java.lang.String); Reason: Validation failed for query for method public abstract java.util.Map com.example.cockpit.repositories.ProjetRepository.getAggregatedMetrics(java.lang.Long,java.lang.String,java.lang.String)!; nested exception is java.lang.IllegalArgumentException: Validation failed for query for method public abstract java.util.Map com.example.cockpit.repositories.ProjetRepository.getAggregatedMetrics(java.lang.Long,java.lang.String,java.lang.String)!
	at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:800) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.ConstructorResolver.autowireConstructor(ConstructorResolver.java:229) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.autowireConstructor(AbstractAutowireCapableBeanFactory.java:1372) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1222) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:582) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:542) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:335) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:234) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:333) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:208) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:955) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:920) ~[spring-context-5.3.28.jar:5.3.28]
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:583) ~[spring-context-5.3.28.jar:5.3.28]
	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:147) ~[spring-boot-2.7.13.jar:2.7.13]
	at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:731) [spring-boot-2.7.13.jar:2.7.13]
	at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:408) [spring-boot-2.7.13.jar:2.7.13]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:307) [spring-boot-2.7.13.jar:2.7.13]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1303) [spring-boot-2.7.13.jar:2.7.13]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1292) [spring-boot-2.7.13.jar:2.7.13]
	at com.example.cockpit.CockpitApplication.main(CockpitApplication.java:12) [classes/:na]
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_352-352]
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_352-352]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_352-352]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_352-352]
	at org.springframework.boot.devtools.restart.RestartLauncher.run(RestartLauncher.java:50) [spring-boot-devtools-2.7.13.jar:2.7.13]
Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'analyticsServiceImp' defined in file [C:\Users\NAJAHIA\Downloads\cockpit (1)\cockpit\target\classes\com\example\cockpit\service\impl\AnalyticsServiceImp.class]: Unsatisfied dependency expressed through constructor parameter 0; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'projetRepository' defined in com.example.cockpit.repositories.ProjetRepository defined in @EnableJpaRepositories declared on JpaRepositoriesRegistrar.EnableJpaRepositoriesConfiguration: Invocation of init method failed; nested exception is org.springframework.data.repository.query.QueryCreationException: Could not create query for public abstract java.util.Map com.example.cockpit.repositories.ProjetRepository.getAggregatedMetrics(java.lang.Long,java.lang.String,java.lang.String); Reason: Validation failed for query for method public abstract java.util.Map com.example.cockpit.repositories.ProjetRepository.getAggregatedMetrics(java.lang.Long,java.lang.String,java.lang.String)!; nested exception is java.lang.IllegalArgumentException: Validation failed for query for method public abstract java.util.Map com.example.cockpit.repositories.ProjetRepository.getAggregatedMetrics(java.lang.Long,java.lang.String,java.lang.String)!
	at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:800) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.ConstructorResolver.autowireConstructor(ConstructorResolver.java:229) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.autowireConstructor(AbstractAutowireCapableBeanFactory.java:1372) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1222) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:582) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:542) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:335) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:234) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:333) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:208) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.config.DependencyDescriptor.resolveCandidate(DependencyDescriptor.java:276) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.doResolveDependency(DefaultListableBeanFactory.java:1391) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.resolveDependency(DefaultListableBeanFactory.java:1311) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.ConstructorResolver.resolveAutowiredArgument(ConstructorResolver.java:887) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:791) ~[spring-beans-5.3.28.jar:5.3.28]
	... 24 common frames omitted
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'projetRepository' defined in com.example.cockpit.repositories.ProjetRepository defined in @EnableJpaRepositories declared on JpaRepositoriesRegistrar.EnableJpaRepositoriesConfiguration: Invocation of init method failed; nested exception is org.springframework.data.repository.query.QueryCreationException: Could not create query for public abstract java.util.Map com.example.cockpit.repositories.ProjetRepository.getAggregatedMetrics(java.lang.Long,java.lang.String,java.lang.String); Reason: Validation failed for query for method public abstract java.util.Map com.example.cockpit.repositories.ProjetRepository.getAggregatedMetrics(java.lang.Long,java.lang.String,java.lang.String)!; nested exception is java.lang.IllegalArgumentException: Validation failed for query for method public abstract java.util.Map com.example.cockpit.repositories.ProjetRepository.getAggregatedMetrics(java.lang.Long,java.lang.String,java.lang.String)!
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1804) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:620) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:542) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:335) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:234) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:333) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:208) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.config.DependencyDescriptor.resolveCandidate(DependencyDescriptor.java:276) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.doResolveDependency(DefaultListableBeanFactory.java:1391) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.resolveDependency(DefaultListableBeanFactory.java:1311) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.ConstructorResolver.resolveAutowiredArgument(ConstructorResolver.java:887) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:791) ~[spring-beans-5.3.28.jar:5.3.28]
	... 38 common frames omitted
Caused by: org.springframework.data.repository.query.QueryCreationException: Could not create query for public abstract java.util.Map com.example.cockpit.repositories.ProjetRepository.getAggregatedMetrics(java.lang.Long,java.lang.String,java.lang.String); Reason: Validation failed for query for method public abstract java.util.Map com.example.cockpit.repositories.ProjetRepository.getAggregatedMetrics(java.lang.Long,java.lang.String,java.lang.String)!; nested exception is java.lang.IllegalArgumentException: Validation failed for query for method public abstract java.util.Map com.example.cockpit.repositories.ProjetRepository.getAggregatedMetrics(java.lang.Long,java.lang.String,java.lang.String)!
	at org.springframework.data.repository.query.QueryCreationException.create(QueryCreationException.java:101) ~[spring-data-commons-2.7.13.jar:2.7.13]
	at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.lookupQuery(QueryExecutorMethodInterceptor.java:107) ~[spring-data-commons-2.7.13.jar:2.7.13]
	at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.lambda$mapMethodsToQuery$1(QueryExecutorMethodInterceptor.java:95) ~[spring-data-commons-2.7.13.jar:2.7.13]
	at java.util.stream.ReferencePipeline$3$1.accept(ReferencePipeline.java:193) ~[na:1.8.0_352-352]
	at java.util.Iterator.forEachRemaining(Iterator.java:116) ~[na:1.8.0_352-352]
	at java.util.Collections$UnmodifiableCollection$1.forEachRemaining(Collections.java:1051) ~[na:1.8.0_352-352]
	at java.util.Spliterators$IteratorSpliterator.forEachRemaining(Spliterators.java:1801) ~[na:1.8.0_352-352]
	at java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:482) ~[na:1.8.0_352-352]
	at java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:472) ~[na:1.8.0_352-352]
	at java.util.stream.ReduceOps$ReduceOp.evaluateSequential(ReduceOps.java:708) ~[na:1.8.0_352-352]
	at java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:234) ~[na:1.8.0_352-352]
	at java.util.stream.ReferencePipeline.collect(ReferencePipeline.java:566) ~[na:1.8.0_352-352]
	at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.mapMethodsToQuery(QueryExecutorMethodInterceptor.java:97) ~[spring-data-commons-2.7.13.jar:2.7.13]
	at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.lambda$new$0(QueryExecutorMethodInterceptor.java:87) ~[spring-data-commons-2.7.13.jar:2.7.13]
	at java.util.Optional.map(Optional.java:215) ~[na:1.8.0_352-352]
	at org.springframework.data.repository.core.support.QueryExecutorMethodInterceptor.<init>(QueryExecutorMethodInterceptor.java:87) ~[spring-data-commons-2.7.13.jar:2.7.13]
	at org.springframework.data.repository.core.support.RepositoryFactorySupport.getRepository(RepositoryFactorySupport.java:365) ~[spring-data-commons-2.7.13.jar:2.7.13]
	at org.springframework.data.repository.core.support.RepositoryFactoryBeanSupport.lambda$afterPropertiesSet$5(RepositoryFactoryBeanSupport.java:323) ~[spring-data-commons-2.7.13.jar:2.7.13]
	at org.springframework.data.util.Lazy.getNullable(Lazy.java:231) ~[spring-data-commons-2.7.13.jar:2.7.13]
	at org.springframework.data.util.Lazy.get(Lazy.java:115) ~[spring-data-commons-2.7.13.jar:2.7.13]
	at org.springframework.data.repository.core.support.RepositoryFactoryBeanSupport.afterPropertiesSet(RepositoryFactoryBeanSupport.java:329) ~[spring-data-commons-2.7.13.jar:2.7.13]
	at org.springframework.data.jpa.repository.support.JpaRepositoryFactoryBean.afterPropertiesSet(JpaRepositoryFactoryBean.java:144) ~[spring-data-jpa-2.7.13.jar:2.7.13]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1863) ~[spring-beans-5.3.28.jar:5.3.28]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1800) ~[spring-beans-5.3.28.jar:5.3.28]
	... 49 common frames omitted
Cau
