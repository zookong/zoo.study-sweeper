# Spring Security

### WebSecurityConfigurerAdapter를 통한 설정

```java
// TODO: 해당 어노테이션에 대해 깊게 살펴보자
@EnableWebSecurity
public class WebConfigurerAdapter extends WebSecurityConfigurerAdapter {

    @Bean
    public FormAuthenticationProvider formAuthenticationProvider() {
        return new FormAuthenticationProvider();
    }

    // 스프링 시큐리티 규칙
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // iframe 허용안함.
        http.headers().frameOptions().disable();

        // csrf 보안 설정 비활성화
        // TODO: 왜 비활성화를 해야할까?
        http.csrf().disable();

        http.authorizeRequests()
                // login page에 대해 전부 접근 허용
                .antMatchers("/login", "/static/**").permitAll()
                .antMatchers("/").authenticated()
                .antMatchers("/admin/restricted/**").hasRole("restricted")
                .antMatchers("/admin/general/**").hasAnyRole("restricted", "general");

        http.formLogin()
                .loginPage("/login")
                .usernameParameter("userId")
                .passwordParameter("password")
                .loginProcessingUrl("/login")
                // 이거 내부 post로 이동하는 것임, url 변경.. / RequestDispatcher를 통해 내부 컨트롤러를 하번 더 ..
                // 해당 컨트롤러에서 구체적인 로직을 넣을 수 있음..
                .successForwardUrl("/login/succeed")
                // 이거는 get..
                .defaultSuccessUrl("/login/succeed")
                // 로그인 성공시, 더욱 구체적인 로직을 넣을 수 .. 있음
                .successHandler((request, response, authentication) -> {
                    response.sendRedirect("/login/succeed");
                })
                .failureUrl("/login/fail");

        http.logout()
                .logoutUrl("/logout")
                .logoutSuccessUrl("/logout/succeed")
                // logout할 경우 HttpSession을 무효화할지 여부
                .invalidateHttpSession(true)
                .deleteCookies("JSESSIONID");

        http.exceptionHandling()
                // AuthenticationEntryPoint를 상속받은 내용을 적으면 가능함.
                .authenticationEntryPoint((request, response, authException) -> {
                    response.sendRedirect("/auth/fail");
                })
                .accessDeniedPage("/auth/denied");
    }

}
```

### XML 통한 설정

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:security="http://www.springframework.org/schema/security"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
                        http://www.springframework.org/schema/security
                        http://www.springframework.org/schema/security/spring-security-4.1.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <!--
        로그인 처리를 위한 Spring Security 설정
        2014-11-14 nohsoo
    -->
    <bean id="loginDao" class="core.common.auth.dao.LoginDao">
        <property name="sqlSessionTemplate" ref="sqlSessionTemplate"/>
    </bean>
    <bean id="loginLogDao" class="core.common.monitoring.dao.LoginLogDao">
        <property name="sqlSessionTemplate" ref="sqlSessionTemplate"/>
    </bean>
    <bean id="companyDao" class="core.common.company.dao.CompanyDao">
        <property name="sqlSessionTemplate" ref="sqlSessionTemplate"/>
    </bean>
    <bean id="serviceProductDao" class="core.common.product.dao.ServiceProductDao">
        <property name="sqlSessionTemplate" ref="sqlSessionTemplate"/>
    </bean>

    <import resource="classpath:spring/spring-security-handler.xml"/>

    <security:authentication-manager id="authenticationManager" alias="authenticationManager">
        <security:authentication-provider ref="userAuthenticationWithOAuthProvider">
        </security:authentication-provider>
    </security:authentication-manager>

    <security:authentication-manager id="mobileAuthenticationManager" alias="mobileAuthenticationManager">
        <security:authentication-provider user-service-ref="mobileLoginDetailService">
            <security:password-encoder base64="false" hash="sha-256"></security:password-encoder>
        </security:authentication-provider>
    </security:authentication-manager>

    <security:authentication-manager id="remoteAuthenticationManager" alias="remoteAuthenticationManager">
        <security:authentication-provider user-service-ref="remoteLoginDetailService">
        </security:authentication-provider>
    </security:authentication-manager>

    <security:global-method-security pre-post-annotations="enabled" secured-annotations="enabled"
                                     authentication-manager-ref="authenticationManager"/>


    <bean id="webSecurityExpressionHandler"
          class="org.springframework.security.web.access.expression.DefaultWebSecurityExpressionHandler">
        <property name="defaultRolePrefix" value=""/>
    </bean>

    <bean id="filterSecurityInterceptor"
          class="org.springframework.security.web.access.intercept.FilterSecurityInterceptor">
        <property name="authenticationManager" ref="authenticationManager"/>
        <property name="accessDecisionManager" ref="accessDecisionManager"/>
        <property name="securityMetadataSource" ref="securityMetadataSource"/>
    </bean>
    <bean id="securityMetadataSource" class="framework.MyFilterInvocationSecurityMetadataSource">
        <property name="menuDao" ref="menuDao"/>
        <property name="loginDao" ref="loginDao"/>
    </bean>
    <bean id="menuDao" class="core.common.role.dao.MenuDao">
        <property name="sqlSessionTemplate" ref="sqlSessionTemplate"/>
    </bean>
    <bean id="accessDecisionManager" class="org.springframework.security.access.vote.AffirmativeBased">
        <constructor-arg>
            <list>
                <bean class="framework.MyRoleVoter"/>
            </list>
        </constructor-arg>
    </bean>
    <bean id="roleVoter" class="framework.MyRoleVoter"/>

    <security:http auto-config="true" name="remoteSecurityHttp" pattern="/remote/admin/auth/**" use-expressions="true"
                   realm="WMS Login" authentication-manager-ref="remoteAuthenticationManager"
                   access-decision-manager-ref="accessDecisionManager">
        <security:headers disabled="true"/>
        <security:csrf disabled="true"/>
        <security:expression-handler ref="webSecurityExpressionHandler"/>

        <security:custom-filter ref="filterSecurityInterceptor" before="FILTER_SECURITY_INTERCEPTOR"/>
        <security:session-management session-fixation-protection="none"></security:session-management>

        <!-- 로그인 폼 -->
        <security:form-login login-page="/auth/login.do"
                             login-processing-url="/remote/admin/auth/login"
                             default-target-url="/dashboard/main.do"
                             authentication-failure-url="/auth/login.do?failure=1"
                             authentication-success-handler-ref="userAuthenticationSuccessHandler"
                             authentication-failure-handler-ref="userAuthenticationFailureHandler"
                             username-parameter="userid"
                             password-parameter="userpw"
                             always-use-default-target="false"/>

        <!-- 로그아웃 처리 -->
        <security:logout
                logout-url="/auth/logout"
                success-handler-ref="userLogOutSuccessHandler"
                delete-cookies="JSESSIONID" invalidate-session="true"/>
    </security:http>

    <security:http auto-config="true" name="mobileSecurityHttp" pattern="/m/auth/**" use-expressions="true"
                   realm="WMS Login" authentication-manager-ref="mobileAuthenticationManager"
                   access-decision-manager-ref="accessDecisionManager">
        <security:headers disabled="true"/>
        <security:csrf disabled="true"/>
        <security:expression-handler ref="webSecurityExpressionHandler"/>

        <security:custom-filter ref="filterSecurityInterceptor" before="FILTER_SECURITY_INTERCEPTOR"/>
        <security:session-management session-fixation-protection="none"></security:session-management>

        <!-- 로그인 폼 -->
        <security:form-login login-page="/m/auth/login.do"
                             login-processing-url="/m/auth/login"
                             default-target-url="/m/dashboard/main.do"
                             authentication-failure-url="/m/auth/login.do?failure=1"
                             authentication-success-handler-ref="userAuthenticationSuccessHandler"
                             authentication-failure-handler-ref="userAuthenticationFailureHandler"
                             username-parameter="userid"
                             password-parameter="userpw"
                             always-use-default-target="false"/>

        <!-- 로그아웃 처리 -->
        <security:logout
                logout-url="/m/auth/logout"
                success-handler-ref="userLogOutSuccessHandler"
                delete-cookies="JSESSIONID" invalidate-session="true"/>
    </security:http>


    <security:http auto-config="true" name="securityHttp" pattern="/**" use-expressions="true" realm="WMS Login"
                   authentication-manager-ref="authenticationManager"
                   access-decision-manager-ref="accessDecisionManager">
        <security:headers disabled="true"/>
        <security:csrf disabled="true"/>
        <security:expression-handler ref="webSecurityExpressionHandler"/>

        <security:custom-filter ref="filterSecurityInterceptor" before="FILTER_SECURITY_INTERCEPTOR"/>
        <security:session-management session-fixation-protection="none"></security:session-management>

        <!-- 로그인 폼 -->
        <security:form-login login-page="/auth/login.do"
                             login-processing-url="/auth/login"
                             default-target-url="/dashboard/main.do"
                             authentication-failure-url="/auth/login.do?failure=1"
                             authentication-success-handler-ref="userAuthenticationSuccessHandler"
                             authentication-failure-handler-ref="userAuthenticationFailureHandler"
                             username-parameter="userid"
                             password-parameter="userpw"
                             always-use-default-target="false"/>

        <!-- 로그아웃 처리 -->
        <security:logout
                logout-url="/auth/logout"
                success-handler-ref="userLogOutSuccessHandler"
                delete-cookies="JSESSIONID" invalidate-session="true"/>
    </security:http>

    <!-- 세션 레디스 클러스터링 -->
    <context:annotation-config/>
    <bean class="org.springframework.session.data.redis.config.annotation.web.http.RedisHttpSessionConfiguration"/>

</beans>
```


```java
/**
 *
 */
// TODO: ProviderManager와 어떤 관계인지 확인하고 정리하자
public class FormAuthenticationProvider implements AuthenticationProvider {

    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        UsernamePasswordAuthenticationToken usernamePassword = (UsernamePasswordAuthenticationToken) authentication;

        // 유저가 인증을 위해 넘긴 값
        String userId = usernamePassword.getName();
        String userPw = (String) usernamePassword.getCredentials();

        // issue: 단방향 암호화를 통해 password를 암호화하여 DB에 있는 값과 비교할 수 있다.
        // UserDto userDto = UserDto.byIdAndPassword(userId, userPw);

        /**
         * 해당 부분에서 Exception을 발생시킬 때, ProviderManager를 참고하는 것이 좋음
         * 만약, Authentication와 관련된 Fail 처리를 하고 싶으면 아래의 Exception을 참고.
         * AccountStatusException, InternalAuthenticationServiceException, AuthenticationException
         * 위의 3가지 Exception을 catch 하고 있기 때문에 이외의 Exception은 서버의 500에러로 핸들링 함.
         */


        //  이것을 반환하는 리드스
        // username-parameter="userid"
        // password-parameter="userpw"
        // Object principal, Object credentials,
        //			Collection<? extends GrantedAuthority> authorities
        /**
         * principal
         * credentials
         * authorities, SimpleGrantedAuthority를 통해 반환.. GrantedAuthority
         */
        // return null;
        return new UsernamePasswordAuthenticationToken("asd", "asdasd");
    }

    @Override
    public boolean supports(Class<?> authentication) {
        // UsernamePasswordAuthenticationToken : 간단한 username 과 password를 통한 인증 토큰 클래스
        return authentication.equals(UsernamePasswordAuthenticationToken.class);
    }


}
```
UsernamePasswordAuthenticationFilter 에 대해 조사하자