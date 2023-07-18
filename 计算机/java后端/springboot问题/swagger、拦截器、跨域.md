如果我们的项目中有关于跨域的处理，同时还有拦截器，然后还要使用swagger，这种情况大家要注意了，有可能我们的拦截器会将swagger中的页面路径拦截掉导致swagger页面出不来，当我们在拦截器中把swagger的页面排除掉的时候，也有可能会导致跨域配置的失效。



swagger文档链接：

跨域文档链接：



按照上方的两个文档进行分别配置后，会发现跨域并不生效

因为swagger和跨域这样配置是冲突的



在swagger存在的情况下，配置跨域需要用下边的代码

```Java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;
/**
 * @className: CorsConfig
 * @description:
 * @author: sh.Liu
 * @date: 2020-12-02 10:16
 */
@Configuration
public class CorsConfig {
    @Bean
    public CorsFilter corsFilter() {
        CorsConfiguration config = new CorsConfiguration();
        config.addAllowedOrigin("*");
        config.setAllowCredentials(true);
        config.addAllowedMethod("*");
        config.addAllowedHeader("*");
        UrlBasedCorsConfigurationSource configSource = new UrlBasedCorsConfigurationSource();
        configSource.registerCorsConfiguration("/**", config);
        return new CorsFilter(configSource);
    }
}
```

