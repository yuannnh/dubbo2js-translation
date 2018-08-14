
# Implementation of cross-language calls by dubbo2.js
> [dubbo2.js](https://github.com/dubbo/dubbo2.js) is a Dubbo client for node.js developped by [Qianmiwang](https://www.qianmi.com/). It supports Dubbo's native protocol, which makes the RPC calls between javascript and java efficient and agile. This tool has been contributed to Dubbo's community.

## Cross-language calls for micro service
Nowadays, Internet architecture tends to be micro-service way. The discussions about micro-service architecture becomes the most mentioned topic in different technical conferences. In China, most of the companies, such as Qianmiwang, choose Dubbo as their micro-servie architecture solution. As most of the internet companies, Qianmiwang uses various of programming languages. Java is for most of the backend services. Each business based on these backend can choose its own programming language such as go, python and javascript. Therefore, here comes a challenge, cross language calls. Some well mentioned solutions are as follows:

  - Spring cloud. Spring cloud provides a set of components for micro-service development. It is based on HTTP protocol and is designed in the restful way, which makes it support cross-language calls. Other languages can call the services simply by implementing an HTTP interface.
  - Service mesh. People call service mesh the next generation of micro-service framework. The core of this solution is SideCar. Even though the concept of SideCar changes a lot during the revolution of Service mesh, its main job never changed: providing reliable communication between different services.
  - Motan. [Motan](https://github.com/weibocom/motan) is an open source cross-language service framework developped by Sina Weibo. Its early version only supports motan-java. However, as the new versions come out, more languages are supported in order to handle the cross-language problem. Its newest version(1.1.0) provides motan-go, motan-php, motan-openresty, etc. Similar to SideCar in Service mesh, Motan forwards messages by mortan-go, which can be considered as an agent. Meanwhile, motan2, its own protocol, is built for cross-language calls.

  According to the solutions below, there are two ways to solve the cross-language calls problem:

  - communicating by a common protocol.
  - implementing an agent as a protocol adapter.

  When a new team is choosing technical solutions, what I mentioned below could be our candidates. Meanwhile, the old system's compatibility and migration costs should also be considered. The first trial we did is to work on RPC protocol.

## Cross-language calls by a common protocol 

  ### springmvc

  ![springmvc](../../img/blog/springmvc.png)

  Before achieving the real cross-language calls, the most common solution is to use the http protocol. We can call Dubbo provider indirectly by controller/restController provided by springmvc. This is easy to carry out, but there are lots of inconveniences. firstly, a call will go through too many nodes. Secondly, an extra communication layer (for http protocol) will be involved, but it could have been handled simply by the TCP protocol. Thirdly, we need to implement the RPC interface in the controller part. This will be extra work for developers.

  ### We support some common protocols

  Most of the service management frameworks support multiple protocols, dubbo as well. Besides its own protocol, the common protocols such as Dangdangwang's [Rest](https://dangdangdotcom.github.io/dubbox/rest.html) protocol and Qianmiwang's [json-rpc](https://github.com/apache/incubator-dubbo-rpc-jsonrpc) protocol are also supported.       

  The developers getting used to traditional RPC interfaces might feel uncomfortable while working on restful RPC interfaces because of the annotations such as @Path, @Post and @Get. On the one hand, this bad development experience is not good for rebuilding new interfaces. On the other hand, its unique interface style might make it incompatible with the other protocols used for old interfaces. Of course, if there is no old system's problem, Rest protocol is easiest implementation of cross-languages calls, since most of the languages support Rest protocols.

  Even if Dubbo tried on restful interface, the difference between rest architecture and RPC architecture should not be ignored. Rest architecture defines each resources, and it needs basic operations of http protocols such as GET, POST, DELETE, PUT. In my opinion, a rest protocol is more s calls between different systems on the internet, while RPC is suitable for inner system calls. Similar to Rest protocol, json-rpc is also implemented by text sequence and http protocol. Using json-rpc solve the cross-language problems, meanwhile, it makes our solution compatible with old interfaces and development habits get kept.

  Json-rpc is Qianmiwang's early solution for their implementation of cross-languages protocol. They open-sourced their [dubbo-client-py](https://github.com/dubbo/dubbo-client-py) and [dubbo-node-client](https://github.com/QianmiOpen/dubbo-node-client), two clients based on json-rpc protocol. With these tools, we can easily call the rpc services provided by dubbo-provider-java with while using python or node.js. The inner system calls for java services are mainly implemented by Dubbo protocol. In order to adapt the old system, two protocols could be configured.  

  ``` xml
  <dubbo:protocol name="dubbo" port="20880" />
  <dubbo:protocol name="jsonrpc" port="8080" />
  ```




