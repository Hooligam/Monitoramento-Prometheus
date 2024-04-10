Requisitos:

Uma IDE, nesse caso utilizamos eclipe por ja carregar dependencias do maven, facilitando nosso trabalho.
Docker e docker-compose
Java jdk "1.8.0_312"
maven



-//----

dependecia no codigo


O Actuator atua como uma dependência. No nosso caso, estamos utilizando o Maven. Então, vamos copiar essa dependência.

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
COPIAR CÓDIGO
Se você estiver usando o Gradle, a implantação será outra. Mas, no nosso caso, é o Maven.



adicionanod actuator 

Nele, encontraremos diversas configurações: a porta onde a aplicação sobe; a configuração do Redis, MySQL e JPA para conexão; e o token JWT, porque essa aplicação usa um token. Para algumas ações, como excluir ou criar um tópico, é necessário ter um token gerado através de uma autenticação. Estamos falando de uma API REST.

Vamos inserir algumas linhas neste arquivo.

---
#actuator
management.endpoint.health.show-details=always management.endpoints.web.exposure.include=*

---
COPIAR CÓDIGO
A primeira linha que inserimos está como always, logo, mostrará todos os detalhes. Na segunda, temos o web.exposure. Mantendo o asterisco, receberemos todas as informações relacionadas à JVM e tudo que estiver relacionado ao gerenciamento da aplicação pela JVM. Para o nosso caso, isso não é muito interessante.

Sendo assim, apagaremos o asterísco e acessaremos alguns endpoints: health; info; e metrics. Esses três já são suficientes. Nosso foco será o metrics.

---
#actuator
management.endpoint.health.show-details=always management.endpoints.web.exposure.include=health,info,metrics
COPIAR CÓDIGO
---


O health é útil por poder ser usado para health check, dependendo de onde a aplicação estiver rodando. E o info é útil porque nos permite acessar as informações da aplicação importantes para nós e não para o público externo.

Os endpoints Health, info e metrics são internos, eles não devem ser expostos publicamente. Quando a aplicação estiver rodando na stack do Docker Compose, estes endpoints estarão fechados. Para acessá-la, teremos que passar por um proxy que também será implantado mais adiante.



apos subimos a aplicação com o actuator.


imagem 04




----------------------------------------------------------------------------------------------------------------


Buildamos nossa imagem com a aplicação (não vou entrar em datlhes pois não e o foco do projeto) e onde também temos agora o Prometheus rodando no localhost:9090


imagem 05








_______________________________________________________________________________________________


Para ter um consumo de api, temos um vamos subir um container client, que vai rodar um script simulando requisições
corretas e forçando erros, para termos metricas no nosso prometheus.


image 06




_______________________________________________________________________________________________

Anatomia da metrica

toda metrica tem 3 componetes basicos:|

metricname: Nome da metricas
labels: sses rótulos vão identificar qual é a série temporal que você quer consultar.
sample: Além disso, temos o sample, que é o resultado dessa consulta e fica no canto direito da linha retornada.

_______________________________________________________________________________________________


Tipos de dados de uma métrica
No Prometheus, temos quatro tipos de dados:

Instant vector: vetor instantâneo;
Range vector: vetor de uma série temporal;
Float: vetor do tipo escalar, ou seja, um ponto flutuante;
String: vetor não-utilizado, como podemos encontrar na documentação do Prometheus.



