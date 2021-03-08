---
title: 'Service first using JAX-WS'
date: '2014-03-02T15:28:00+10:00'
permalink: /service-first-using-jax-ws
author: 'James Elsey'
category:
    - Java
tag:
    - java
    - jax-ws
    - 'web services'

---
There are two ways for developing services using JAX-WS, service first, and contract first. Service first means you would typically write the implementation first and generate the WSDL afterwards, whereas contract first you would define the WSDL first, then write the implementation afterwards. There are pros and cons for each approach, but I won’t dwell on those now.

There are 2 parts to a JAX-WS service, the Service Endpoint Interface (SEI) and the Service Implementation Bean (SIB). The SEI is an interface where you abstractly declare the methods (or operations) that your service will provide, along with the inputs and outputs. The SIB is a concrete implementation of the SEI, where you actually implement the code for the SEI. Let me show you a basic example

**SEI**

```java
import javax.jws.WebMethod;
import javax.jws.WebService;
import javax.jws.soap.SOAPBinding;

@WebService
@SOAPBinding(style = SOAPBinding.Style.RPC)
public interface TeamService {

    @WebMethod
    public String getTeam();
}
```

**SIB**

```java
import javax.jws.WebService;

@WebService(endpointInterface = "com.jameselsey.webservices.basicimplementationfirst.service.TeamService")
public class TeamServiceImpl implements TeamService{

    @Override
    public String getTeam(){
        return "Geelong Cats";
    }
}
```

That is pretty much it, now we just need to deploy the service. Lets avoid using a servlet container for now, as it complicates this example somewhat, we can run the service by implementing the following and taking advantage of the publish method on Endpoint.

```java
import com.jameselsey.webservices.service.sib.TeamServiceImpl;
import javax.xml.ws.Endpoint;

public class Runner {

    public static void main(String[] args) {
        String address = "http://localhost:9876/footy";
        Endpoint.publish(address, new TeamServiceImpl());

        System.out.println("Server up and running on " + address);
    }
}
```

If you then open http://localhost:9876/footy on your browser you’ll see a page listing the services and their WSDLs. The generated WSDL should look something like this.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<definitions xmlns="http://schemas.xmlsoap.org/wsdl/" xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/" xmlns:tns="http://service.basicimplementationfirst.webservices.jameselsey.com/" xmlns:wsam="http://www.w3.org/2007/05/addressing/metadata" xmlns:wsp="http://www.w3.org/ns/ws-policy" xmlns:wsp1_2="http://schemas.xmlsoap.org/ws/2004/09/policy" xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd" xmlns:xsd="http://www.w3.org/2001/XMLSchema" targetNamespace="http://service.basicimplementationfirst.webservices.jameselsey.com/" name="TeamServiceImplService">
   <types />
   <message name="getTeam" />
   <message name="getTeamResponse">
      <part name="return" type="xsd:string" />
   </message>
   <portType name="TeamService">
      <operation name="getTeam">
         <input wsam:Action="http://service.basicimplementationfirst.webservices.jameselsey.com/TeamService/getTeamRequest" message="tns:getTeam" />
         <output wsam:Action="http://service.basicimplementationfirst.webservices.jameselsey.com/TeamService/getTeamResponse" message="tns:getTeamResponse" />
      </operation>
   </portType>
   <binding name="TeamServiceImplPortBinding" type="tns:TeamService">
      <soap:binding transport="http://schemas.xmlsoap.org/soap/http" style="rpc" />
      <operation name="getTeam">
         <soap:operation soapAction="" />
         <input>
            <soap:body use="literal" namespace="http://service.basicimplementationfirst.webservices.jameselsey.com/" />
         </input>
         <output>
            <soap:body use="literal" namespace="http://service.basicimplementationfirst.webservices.jameselsey.com/" />
         </output>
      </operation>
   </binding>
   <service name="TeamServiceImplService">
      <port name="TeamServiceImplPort" binding="tns:TeamServiceImplPortBinding">
         <soap:address location="http://localhost:9876/footy" />
      </port>
   </service>
</definitions>
```

The important parts in the WSDL that I should mention are:

**Port**  
The port section describes the operations that the service exposes, you can think of this a little bit like a Java interface, where it declares the inputs and outputs abstractly, but mentions nothing about the actual implementation. You will notice that the operations match onto the methods marked as @WebMethod. Notice that the name on the port matches the interface and not the implementation.  
**Binding**  
The binding section is where you mention implementation details for an implementation of the interface (port), such as transport, style, and usages. Transport is typically http (although smtp can be used), style matches onto the SOAPBinding annotation that we set. The default style is document however I’ve overridden this to be rpc as it makes the examples easier to follow as its less complex.  
**Service**  
The service section maps interfaces onto implementations, or in WSDL terms, it lists the ports and their bindings. In the above example it mentions that there is an implementation of TeamServiceImplPort as described in TeamServiceImplPortBinding, available on http://localhost:9876/footy.

You can actually see this working, by creating a project from the WSDL using [SOAPUI](http://soapui.org), it will generate the following request for you

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:sei="http://sei.basicimplementationfirst.webservices.jameselsey.com/" xmlns:sib="http://sib.service.webservices.jameselsey.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <sei:getTeam/>
   </soapenv:Body>
</soapenv:Envelope>
```

As you can see the body contains a tag which is requesting the getTeam port, ultimately bound onto the getTeam Java interface method, implemented to return a pre defined string. If you execute the request, you’ll get the following response

```xml
<S:Envelope xmlns:S="http://schemas.xmlsoap.org/soap/envelope/">
   <S:Body>
      <ns2:getTeamResponse xmlns:ns2="http://sei.basicimplementationfirst.webservices.jameselsey.com/">
         <return>Geelong Cats</return>
      </ns2:getTeamResponse>
   </S:Body>
</S:Envelope>
```

**Summary**  
This was a quick brain dump of a simple JAX-WS service (I’m currently studying for the Oracle web services certification, trying to write my notes up). I’ve covered how to implement a SEI/SIB, how to publish a service, covered in brief the elements of the WSDL we’re interested in, and showed how the service can be invoked. Please check back soon as I’ll continue this example and post more of my study notes.