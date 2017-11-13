# How to add new domains

If you followed the previous guide and deployed a brand new Syncope, it comes with only 1 domain Master.  If you used the stand-alone distribution, it comes with 2 domains Master and Two.

To add a new domain, copy and rename 3 files in domains/ folder. For example, domain Two needs 3 files:

* Two.properties
* TwoContent.xml
* TwoDomain.xml

Here is the steps to add a new domain:

1. Copy and rename 3 domain files
2. Update properties file
3. Restart application server (e.g. Tomcat)
4. Execute Java code to update the domain credentials

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
ds.delete("Two");
DomainTO newDomain = new DomainTO();
newDomain.setKey("Two");
newDomain.setAdminCipherAlgorithm(CipherAlgorithm.SHA);
newDomain.setAdminPwd("somepassword");
Response res = ds.create(newDomain);
System.out.println(res.getStatusInfo());
```

