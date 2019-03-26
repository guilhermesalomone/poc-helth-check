#Helth Check

## Referencia

Implementacão responsavel por disponibilizar status de 'saude' dos microserviços utilizando actuator.

### Pre-requisitos

##### Adicionar a dependência no pom do projeto.

``` sh	
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
> Verificar versionamento no artifactory. Que por default é nativo no Parent do Spring

#### Configuracao do projeto	
	
	
##### Adicionar no properties do projeto as variáveis que serão utilizados pela actuator

> Tipo de arquivo YAML

``` sh
 

###################################################### 
#
# Configuracao Health Check
#
######################################################
management:
    server:
        servlet:
            context-path: /management
    health:
        mail:
            enabled: false # When using the MailService, configure an SMTP server and set this to true
    endpoints:
          web:
              base-path: /
              path-mapping:
                    health: /management/health 
  
```
	
> Tipo de arquivo Properties

``` sh
	
 
###################################################### 
#
# Configuracao Health Check
#
######################################################
management.server.servlet.context-path= /management
management.health.mail.enabled: false # When using the MailService, configure an SMTP server and set this to true
management.endpoints.web.base-path: /
management.endpoints.web.path-mapping.health: /management/health 
	

```
	
> Os dados devem ser substituídos conforme os projetos em que serão aplicados.


##### Criar classe de configuração para inicializar o Health.

``` sh


/**
 * 
 * @author Guilherme.Salomone
 *
 */
@Component
public class HealthCheck implements HealthIndicator {
  
    @Override
    public Health health() {
        int errorCode = check(); // perform some specific health check
        if (errorCode != 0) {
            return Health.down()
              .withDetail("Error Code", errorCode).build();
        }
        return Health.up().build();
    }
     
    public int check() {
        // Our logic to check health
        return 0;
    }
}

```

 
	
	

	
	
	
	