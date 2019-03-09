## pageHelper 5.1.6 新版本出现的问题 
## PageHelper的原理是基于拦截器实现的。拦截器的配置有两种方法，一种是在mybatis的配置文件中配置，一种是直接
## 在spring的配置文件中进行。
## 由于没有Mybatis的配置文件 所以在spring的配置文件中配置

## 过去的配置：
 	<property name="plugins">
  		<array>
  			<bean class="com.github.pagehelper.PageHelper">
  				<property name="properties">
  					<props>
  					<props>
  						<prop key="dialect">mysql</prop>
  					</props>
  				</property>
  			</bean>
  		</array>
 	</property>
## 这样做会出现的问题 ：java.lang.ClassCastException: com.github.pagehelper.PageHelper cannot be 
## cast to org.apache.ibatis.plugin.Interceptor
## 这是因为pageHelper是通过mybatis的pulgin实现了Interceptor接口，从而获得要执行的sql语句实现分页技术，而
## 我们的PageHelper5.1.6版本中的这个类，并没有出现implements Interceptor，再来看下pagehelper这个包下的
## 其他类有没有实现Interceptor的，然后我们找到了下面这个:PageInterceptor
## 这样我们把spring的配置文件修改如下：
<property name="plugins">
	<array>
	  <bean class="com.github.pagehelper.PageInterceptor">
 			<property name="properties">
				<props>
 					<prop key="dialect">mysql</prop>
				</props>
 			</property>
		</bean>
	</array>
</property>
## 这样改过这后依然报错：Cause: com.github.pagehelper.PageException: 
## java.lang.ClassNotFoundException: mysql

## 这是因为在pagehelper插件4.0.0以后的版本支持自动识别使用的数据库，可以不用配置 <property 
## name="dialect" value="mysql"/>  最终结果如下：

 		<property name="plugins">
         <array>
            <bean class="com.github.pagehelper.PageInterceptor"/>
         </array>
    </property>

## 阿里巴巴的fastjson：com.alibaba.fastjson版本1.2.55版本在通过xml方式配置spring的时候会出现这个个奇怪的
## 问题：Class 'com.alibaba.fastjson.support.spring.FastJsonpResponseBodyAdvice' is marked 
## deprecated [config set: DataMangement/web-context] 
## 这是因为包扫描路径的问题，千万千万不能设置成com下面我出现问题的时候就是因为这里设置成com了,导致fastjson
## 的fastJsonpResponseBodyAdvice这个方法也被开启了

## 改成这样就可以了： <context:component-scan base-package="com.etoak.cxf"/>
