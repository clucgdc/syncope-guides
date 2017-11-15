# How to add new domains

If you followed the previous guide and deployed a brand new Syncope, it comes with only 1 domain Master.  If you used the stand-alone distribution, it comes with 2 domains Master and Two.

To add a new domain, start from the Maven project, copy and rename 3 files in domains/ folder. For example, domain Two needs 3 files:

* Two.properties
* TwoContent.xml
* TwoDomain.xml

Here is the steps to add a new domain:

1. Copy and rename 3 domain files
2. Update properties file and Domain.xml file
3. Rebuild Maven project and (re)deploy
4. Restart application server (e.g. Tomcat) to register the new domain
5. Execute Java code to update the domain credentials
6. Restart application server (again) to refresh the web views

As you can see, adding new domains is better done very early, from the solution design phase. It is harder to add a new domain when Syncope is already being used in production.

### Java code

Maven dependencies:

```xml
<dependency>
	<groupId>org.apache.syncope.client</groupId>
	<artifactId>syncope-client-lib</artifactId>
	<version>2.0.6</version>
</dependency>
<dependency>
	<groupId>org.apache.syncope.core</groupId>
	<artifactId>syncope-core-spring</artifactId>
	<version>2.0.6</version>
</dependency>
```

Code to create Syncope Client and update domain Two:

```java
import javax.ws.rs.core.Response;
import org.apache.syncope.client.lib.SyncopeClient;
import org.apache.syncope.client.lib.SyncopeClientFactoryBean;
import org.apache.syncope.common.lib.to.DomainTO;
import org.apache.syncope.common.lib.types.CipherAlgorithm;
import org.apache.syncope.common.rest.api.service.DomainService;


SyncopeClientFactoryBean clientFactory = new SyncopeClientFactoryBean().setAddress("http://localhost:9080/syncope/rest/");
SyncopeClient client = clientFactory.create("admin", "password");

DomainService ds = client.getService(DomainService.class);
DomainTO newDomain = new DomainTO();
newDomain.setKey("Two");
newDomain.setAdminCipherAlgorithm(CipherAlgorithm.SHA);
newDomain.setAdminPwd("somepassword");
Response res = ds.create(newDomain);
System.out.println(res.getStatusInfo());
```

