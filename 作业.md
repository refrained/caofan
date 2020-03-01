1. MyBatis的动态sql是可以让我们在XML文件中，使用标签的形式编写动态sql语句，来实现数据的CRUD操作。

   动态sql标签有where,if,choose,trim,foreach. 

   原理：使用XMLMapperBuilder获取xml文件流，解析mapper节点，分别处理缓存、参数、返回值、sql、增删改查节点；对每个结点，使用XMLStatementBuilder解析sql,并得到sqlSource,使用XMLScriptBuilder处理节点的sql部分，得到了sqlSource,就能得到BoundSql对象，而BoundSql中的sql字段表示了最终的sql语句。

2. 支持延迟加载。在配置文件中，可以使用延迟加载属性lazyLoadingEnabled值等于true或false。

   原理：使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。

3. SimpleExecutor、ReuseExecutor、BatchExecutor。

   SimpleExecutor：每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。

   ReuseExecutor：执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map<String, Statement>内，供下一次使用。简言之，就是重复使用Statement对象。

   BatchExecutor：执行update（没有select，JDBC批处理不支持select），将所有sql都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。与JDBC批处理相同。（分别从存储结构、范围、失效场景）

4. 一级缓存：MyBatis的一级缓存是SqlSession级别的，作用域是SqlSession，MyBatis默认开启一级缓存。在同一个SqlSession中，相同的sql，第一次查询的时候，就会从缓存中取，如果没有数据，就从数据库中查询出来，缓存到HashMap结构中。如果下次查询，缓存中有相同的key,value数据，就直接从缓存中取出数据，而不去查询数据库。当查询到的数据进行增删改的时候，缓存将会失效。

   二级缓存：二级缓存是mapper级别的缓存，多个SqlSession去操作同一个mapper的sql语句，多个SqlSession可以共用二级缓存，二级缓存是跨SqlSession。第一次调用mapper下的sql 的时候去查询信息，查询到的信息会存放到该mapper对应的二级缓存区域，第二次调用namespace下的mapper映射文件中相同的sql去查询，去对应的二级缓存内取结果。如果在相同的namespace下的mapper映射文件中增删改操作，并且提交了事务，缓存就会失效。

5. Mybatis仅可以编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件，Mybatis使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。

   
