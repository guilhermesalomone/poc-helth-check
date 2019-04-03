#Helth Check

## Referencia

Implementac�o responsavel por disponibilizar status de 'saude' dos microservi�os utilizando actuator.

#### Actuator

Actuator traz recursos prontos para produ��o para o nosso aplicativo.

Monitorar nosso aplicativo, coletar m�tricas, entender o tr�fego ou o estado do nosso banco de dados torna-se trivial com essa depend�ncia.

O principal benef�cio desta biblioteca � que podemos obter ferramentas de n�vel de produ��o sem ter que implementar esses recursos por conta pr�pria.

O atuador � usado principalmente para expor informa��es operacionais sobre o aplicativo em execu��o - sa�de, m�tricas, informa��es, dump, env etc. Ele usa n�s de extremidade HTTP ou JMX para nos permitir interagir com ele.

Uma vez que esta depend�ncia esteja no caminho de classe, v�rios pontos de extremidade est�o dispon�veis para n�s fora da caixa. Como na maioria dos m�dulos Spring, podemos facilmente configur�-lo ou estend�-lo de v�rias maneiras.

##### Pontos de extremidade predefinidos

/auditevents - lista eventos relacionados � auditoria de seguran�a, como login/logout do usu�rio. Al�m disso, podemos filtrar por principal ou tipo entre outros campos

/beans - retorna todos os beans dispon�veis em nosso BeanFactory . Ao contr�rio de /auditevents , ele n�o suporta filtragem

/conditions - anteriormente conhecido como /autoconfig , cria um relat�rio das condi��es de configura��o autom�tica

/configprops - nos permite buscar todos os beans @ConfigurationProperties

/env - retorna as propriedades atuais do ambiente. Al�m disso, podemos recuperar propriedades �nicas

/flyway - fornece detalhes sobre nossas migra��es de banco de dados Flyway

/health - resume o status de integridade de nosso aplicativo

/heapdump - cria e retorna um dump de heap da JVM usada pelo nosso aplicativo

/info - retorna informa��es gerais. Pode ser dados personalizados, informa��es de compila��o ou detalhes sobre o �ltimo commit

/liquibase - b ehaves like / flyway mas para Liquibase

/logfile - retorna logs comuns de aplicativos

/loggers - nos permite consultar e modificar o n�vel de log do nosso aplicativo

/metrics - detalha as m�tricas do nosso aplicativo. Isso pode incluir m�tricas gen�ricas e personalizadas

/prometheus - retorna m�tricas como a anterior, mas formatada para funcionar com um servidor Prometheus

/scheduledtasks - fornece detalhes sobre cada tarefa agendada dentro do nosso aplicativo

/sessions - lista sess�es HTTP, uma vez que estamos usando o Spring Session

/shutdown - executa um desligamento normal do aplicativo

/threaddump - copia as informa��es do encadeamento da JVM subjacente

#### Micrometer

O micr�metro fornece uma fachada simples sobre os clientes de instrumenta��o para v�rios sistemas de monitoramento populares. Atualmente, suporta os seguintes sistemas de monitoramento: Atlas, Datadog, Graphite, Ganglia, Influx, JMX e Prometheus.

No Spring Boot 2.0, as m�tricas internas foram substitu�das pelo suporte do Micr�metro. Assim, podemos esperar mudan�as graduais. Se nosso aplicativo estava usando servi�os de m�trica, como GaugeService ou CounterService, eles n�o estar�o mais dispon�veis.

No Spring Boot 2.0, obteremos um bean do tipo MeterRegistry autoconfigurado para n�s.

O Micr�metro agora faz parte das depend�ncias do Atuador.


### Pre-requisitos

##### Adicionar a depend�ncia no pom do projeto.

``` sh	


<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-webflux</artifactId>
</dependency>

<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
> Verificar versionamento no artifactory. Que por default � nativo no Parent do Spring

#### Configuracao do projeto	
	
	
##### Adicionar no properties do projeto as vari�veis que ser�o utilizados pela actuator

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
              exposure:
                  include: [metrics, info, health, beans, loggers]
              base-path: /management
  
```
	
> Tipo de arquivo Properties

``` sh
	
 
###################################################### 
#
# Configuracao Health Check
#
######################################################

management.server.servlet.context-path: /management
management.health.mail.enabled: false # When using the MailService, configure an SMTP server and set this to true
management.endpoints.web.exposure.include: [metrics, info, health, beans, loggers]
management.endpoints.web.base-path: /management
	

```
	
> Os dados devem ser substitu�dos conforme os projetos em que ser�o aplicados.


##### Criar classe de configura��o para inicializar o Health.

``` sh


/**
 * 
 * @author Guilherme.Salomone
 *
 */
@Component
public class HealthCheck implements ReactiveHealthIndicator  {
  
    @Override
    public Mono<Health> health() {
        return checkDownstreamServiceHealth().onErrorResume(
          ex -> Mono.just(new Health.Builder().down(ex).build())
        );
    }
 
    private Mono<Health> checkDownstreamServiceHealth() {
        // we could use WebClient to check health reactively
        return Mono.just(new Health.Builder().up().build());
    }
}


```

## Refer�ncias  

### https://www.baeldung.com/spring-boot-actuators
### https://www.baeldung.com/micrometer
	

	
	
	
	