#Helth Check

## Referencia

Implementacão responsavel por disponibilizar status de 'saude' dos microserviços utilizando actuator.

#### Actuator

Actuator traz recursos prontos para produção para o nosso aplicativo.

Monitorar nosso aplicativo, coletar métricas, entender o tráfego ou o estado do nosso banco de dados torna-se trivial com essa dependência.

O principal benefício desta biblioteca é que podemos obter ferramentas de nível de produção sem ter que implementar esses recursos por conta própria.

O atuador é usado principalmente para expor informações operacionais sobre o aplicativo em execução - saúde, métricas, informações, dump, env etc. Ele usa nós de extremidade HTTP ou JMX para nos permitir interagir com ele.

Uma vez que esta dependência esteja no caminho de classe, vários pontos de extremidade estão disponíveis para nós fora da caixa. Como na maioria dos módulos Spring, podemos facilmente configurá-lo ou estendê-lo de várias maneiras.

##### Pontos de extremidade predefinidos

/auditevents - lista eventos relacionados à auditoria de segurança, como login/logout do usuário. Além disso, podemos filtrar por principal ou tipo entre outros campos

/beans - retorna todos os beans disponíveis em nosso BeanFactory . Ao contrário de /auditevents , ele não suporta filtragem

/conditions - anteriormente conhecido como /autoconfig , cria um relatório das condições de configuração automática

/configprops - nos permite buscar todos os beans @ConfigurationProperties

/env - retorna as propriedades atuais do ambiente. Além disso, podemos recuperar propriedades únicas

/flyway - fornece detalhes sobre nossas migrações de banco de dados Flyway

/health - resume o status de integridade de nosso aplicativo

/heapdump - cria e retorna um dump de heap da JVM usada pelo nosso aplicativo

/info - retorna informações gerais. Pode ser dados personalizados, informações de compilação ou detalhes sobre o último commit

/liquibase - b ehaves like / flyway mas para Liquibase

/logfile - retorna logs comuns de aplicativos

/loggers - nos permite consultar e modificar o nível de log do nosso aplicativo

/metrics - detalha as métricas do nosso aplicativo. Isso pode incluir métricas genéricas e personalizadas

/prometheus - retorna métricas como a anterior, mas formatada para funcionar com um servidor Prometheus

/scheduledtasks - fornece detalhes sobre cada tarefa agendada dentro do nosso aplicativo

/sessions - lista sessões HTTP, uma vez que estamos usando o Spring Session

/shutdown - executa um desligamento normal do aplicativo

/threaddump - copia as informações do encadeamento da JVM subjacente

#### Micrometer

O micrômetro fornece uma fachada simples sobre os clientes de instrumentação para vários sistemas de monitoramento populares. Atualmente, suporta os seguintes sistemas de monitoramento: Atlas, Datadog, Graphite, Ganglia, Influx, JMX e Prometheus.

No Spring Boot 2.0, as métricas internas foram substituídas pelo suporte do Micrômetro. Assim, podemos esperar mudanças graduais. Se nosso aplicativo estava usando serviços de métrica, como GaugeService ou CounterService, eles não estarão mais disponíveis.

No Spring Boot 2.0, obteremos um bean do tipo MeterRegistry autoconfigurado para nós.

O Micrômetro agora faz parte das dependências do Atuador.


### Pre-requisitos

##### Adicionar a dependência no pom do projeto.

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
	
> Os dados devem ser substituídos conforme os projetos em que serão aplicados.


##### Criar classe de configuração para inicializar o Health.

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

## Referências  

### https://www.baeldung.com/spring-boot-actuators
### https://www.baeldung.com/micrometer
	

	
	
	
	